---
title: "ReAct, Chain-of-Thought, Plan-and-Execute"
date: "2025-07-06"
tags: ["Agentic AI", "Architecture", "Reasoning", "ReAct", "CoT"]
difficulty: "medium"
---

# ReAct, Chain-of-Thought, Plan-and-Execute

## 1. 핵심 개념 (Core Concept)

**Chain-of-Thought (CoT)**, **ReAct**, 그리고 **Plan-and-Execute**는 AI 에이전트가 목표를 달성하기 위해 사용하는 핵심적인 추론 및 실행 아키텍처 패턴임. **CoT**는 LLM이 단계별로 생각하여 복잡한 문제의 정답률을 높이는 가장 기본적인 추론 방식임. **ReAct**는 추론(Reasoning)과 행동(Acting)을 결합하여 외부 환경과 상호작용하며 계획을 수정해나가는 동적인 패턴임. **Plan-and-Execute**는 먼저 전체 계획을 수립한 뒤, 계획에 따라 순차적으로 행동을 실행하는 더 구조화된 패턴임.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 Chain-of-Thought (CoT): 생각의 사슬

CoT는 LLM에게 최종 답변만 내놓으라고 하는 대신, 문제 해결 과정을 단계별로 생각하고 설명하도록 유도하는 프롬프팅 기법임. 이 과정을 통해 LLM은 복잡한 문제를 작은 단위로 나누어 해결하게 되므로, 최종 결과의 정확성과 논리적 일관성이 크게 향상됨.

*   **작동 방식**: 프롬프트에 "Step-by-step으로 생각해봐(Think step-by-step)"와 같은 문구를 추가하거나, 몇 가지 예시(Few-shot)를 통해 단계별 추론 과정을 보여줌.
*   **한계**: CoT는 LLM 내부의 추론 과정일 뿐, 외부 환경과 상호작용할 수 없어 잘못된 사실이나 오래된 정보에 기반한 추론을 바로잡을 수 없음.

### 2.2 ReAct: 추론과 행동의 시너지

ReAct (Reason + Act)는 CoT의 한계를 극복하기 위해, 추론과 행동을 하나의 루프 안에서 반복적으로 수행하는 패턴임. 이는 에이전트 라이프사이클(인지-계획-실행-반성)을 가장 직접적으로 구현한 아키텍처임.

*   **작동 방식**:
    1.  **Thought (생각)**: 현재 상황을 분석하고 다음 행동 계획을 세움.
    2.  **Action (행동)**: 계획에 따라 외부 툴(예: 위키피디아 검색 API)을 실행함.
    3.  **Observation (관찰)**: 툴 실행 결과를 관찰함.
    4.  **Repeat**: 관찰 결과를 바탕으로 다시 생각(Thought)하고 다음 행동을 결정함. 목표를 달성할 때까지 이 과정을 반복.

*   **장점**: 외부 세계의 최신 정보(Ground Truth)를 얻어 자신의 추론을 검증하고 수정할 수 있어, 환각(Hallucination)을 줄이고 사실 기반의 정확한 문제 해결이 가능함.

### 2.3 Plan-and-Execute: 계획가와 실행가의 분리

이 패턴은 복잡한 작업을 '계획가(Planner)'와 '실행가(Executor)'라는 두 개의 역할로 명확히 분리함.

*   **작동 방식**:
    1.  **Planner**: 사용자의 높은 수준의 목표를 입력받아, 이를 달성하기 위한 전체 단계별 계획을 한 번에 수립함.
    2.  **Executor**: 계획가가 수립한 계획을 하나씩 순서대로 실행함. 각 단계의 실행은 독립적이며, 일반적으로 실행 중에 계획을 수정하지 않음.

*   **ReAct와의 차이점**: ReAct는 매 단계마다 생각하고 행동하며 계획을 동적으로 수정하는 반면, Plan-and-Execute는 초기에 수립된 계획을 끝까지 따르는 경향이 강함. 따라서 예측 가능하고 안정적인 작업 흐름에 더 적합함.

---

## 3. 예시 (Example)

### 사용 사례: "현재 미국 대통령의 나이는?"

*   **Chain-of-Thought 방식**:
    *   (LLM 내부 생각) "1. 현재 미국 대통령이 누구인지 알아내야 한다. 2. 그 사람의 생년월일을 찾아야 한다. 3. 현재 날짜를 기준으로 나이를 계산해야 한다."
    *   (잘못된 답변 가능성) LLM의 학습 데이터가 오래되었다면, 이전 대통령의 정보를 기반으로 답변할 수 있음.

*   **ReAct 방식**:
    1.  **Thought**: 현재 미국 대통령이 누구인지 알아야겠다.
    2.  **Action**: `search("current US president")`
    3.  **Observation**: "조 바이든"
    4.  **Thought**: 조 바이든의 생년월일을 알아야겠다.
    5.  **Action**: `search("Joe Biden birth date")`
    6.  **Observation**: "1942년 11월 20일"
    7.  **Thought**: 이제 현재 날짜와 생년월일을 가지고 나이를 계산할 수 있다.
    8.  **Action**: `calculate_age("1942-11-20")` -> 최종 답변 생성.

*   **Plan-and-Execute 방식**:
    1.  **Plan**: `[
    {"tool": "search", "query": "current US president"},
    {"tool": "search", "query": "{step1_output} birth date"},
    {"tool": "calculate_age", "birth_date": "{step2_output}"}
]`
    2.  **Execute**: 계획에 따라 각 단계를 순차적으로 실행하여 최종 답변을 얻음.

---

## 4. 예상 면접 질문 (Potential Interview Questions)

*   **Q. ReAct 패턴이 Chain-of-Thought에 비해 갖는 가장 큰 장점은 무엇인가요?**
    *   **A.** 외부 세계와의 상호작용이 가능하다는 점입니다. CoT는 LLM 내부의 추론에만 의존하지만, ReAct는 툴 사용을 통해 외부의 최신 정보를 얻고, 이를 바탕으로 자신의 추론을 검증하고 수정할 수 있습니다. 이로 인해 사실적 정확성이 훨씬 높아지고 환각을 줄일 수 있습니다.

*   **Q. ReAct와 Plan-and-Execute 중 어떤 상황에서 각각 더 적합할까요?**
    *   **A.** ReAct는 환경의 피드백에 따라 계획을 계속 수정해야 하는 동적이고 예측 불가능한 작업에 더 적합합니다. 반면, Plan-and-Execute는 작업의 절차가 명확하고 예측 가능하여 초기에 전체 계획을 수립할 수 있는 안정적인 작업에 더 유리합니다. 예를 들어, 웹 리서치는 ReAct가, 정해진 순서의 데이터 처리 파이프라인은 Plan-and-Execute가 더 적합할 수 있습니다.

*   **Q. 이러한 추론 패턴들이 에이전트의 '환각(Hallucination)' 문제를 어떻게 완화할 수 있나요?**
    *   **A.** 특히 ReAct와 같이 툴 사용을 포함하는 패턴은 에이전트가 자신의 지식에만 의존하는 대신, 외부 API나 데이터베이스 같은 신뢰할 수 있는 정보 소스(Ground Truth)를 참조하도록 강제합니다. 툴을 통해 얻은 실제 데이터를 바탕으로 추론을 진행하므로, 사실에 기반하지 않은 내용을 지어내는 환각 현상을 크게 줄일 수 있습니다.

---

## 5. 더 읽어보기 (Further Reading)

*   [ReAct: Synergizing Reasoning and Acting in Language Models (Paper)](https://arxiv.org/abs/2210.03629)
*   [Chain-of-Thought Prompting Elicits Reasoning in Large Language Models (Paper)](https://arxiv.org/abs/2201.11903)
*   [Google Agent Document](https://www.kaggle.com/whitepaper-agent-companion)
*   [Anthropic Agent Document](https://www.anthropic.com/engineering/building-effective-agents)