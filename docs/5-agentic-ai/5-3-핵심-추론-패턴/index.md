---
title: 핵심 추론 패턴
date: '2025-10-29'
tags: [Agentic-AI, Reasoning, Prompting, CoT, ReAct, ToT, GoT]
difficulty: easy
---

# 핵심 추론 패턴 (Core Reasoning Patterns)

에이전트의 문제 해결 능력은 어떤 추론 패턴을 사용하느냐에 따라 크게 달라집니다. 이 패턴들은 개발자가 에이전트에게 장착해줄 수 있는 **'사고의 도구함'** 과 같습니다. 단순한 지시 수행을 넘어, 복잡한 과업을 자율적으로 해결하기 위해서는 다양한 추론 패턴을 이해하고 문제의 성격에 맞게 적재적소에 활용해야 합니다.

이 섹션에서는 가장 기본적인 프롬프팅 기법부터, 외부 도구를 사용하고(ReAct), 여러 가능성을 탐색하며(ToT), 심지어 스스로의 실수를 통해 학습하는(Reflexion) 등 점진적으로 발전하는 에이전트의 핵심 추론 패턴들을 다룹니다.

*Note: 아래 다이어그램을 위한 이미지를 `docs/images/reasoning-patterns-evolution.png` 에 추가해주세요.*
![Reasoning Patterns Evolution](../../images/reasoning-patterns-evolution.png)

______________________________________________________________________

## 문서 목록

- **[생각의 사슬 (Chain-of-Thought, CoT)](./chain-of-thought-cot.md)**

  - 복잡한 문제를 단계별로 분해하여 LLM의 추론 과정을 유도하고 정확성을 높이는 가장 기본적인 추론 패턴입니다.

- **[ReAct (Reason + Act)](./react.md)**

  - 추론(Reason)과 행동(Act)을 결합하여, 외부 도구와 상호작용하며 정보를 얻고 과업을 수행하는 동적인 패턴을 학습합니다.

- **[Reflexion (자기 반성·자기 수정)](./reflexion.md)**

  - 에이전트가 스스로의 실패를 평가하고, 그로부터 배워 다음 행동을 개선해나가는 자기 수정 루프를 알아봅니다.

- **[생각의 트리 (Tree-of-Thoughts, ToT)](./tree-of-thoughts-tot.md)**

  - 단일 경로가 아닌, 여러 가능한 해결 경로를 트리 형태로 탐색하여 최적의 답을 찾는 고급 탐색 기법을 다룹니다.

- **[생각의 그래프 (Graph-of-Thoughts, GoT)](./graph-of-thoughts-got.md)**

  - 생각을 그래프 구조로 연결하여, 여러 아이디어를 병합하거나 순환적으로 개선하는 가장 유연하고 강력한 추론 패턴을 설명합니다.
