---
title: "핵심 동작 루프: RPA(Reasoning-Planning-Acting) & Reflection"
date: "2025-10-29"
tags: ["Agentic AI", "RPA", "Reasoning", "Reflection"]
difficulty: "medium"
---

# 핵심 동작 루프 (RPA & Reflection)

## 1. 핵심 개념 (Core Concept)

에이전트는 추론→계획→행동(RPA)을 반복하고, 결과를 반성(Reflection/Reflexion)하여 다음 스텝을 개선합니다. 루프에 “평가·가드레일”을 삽입하면 안정성이 크게 높아집니다.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 RPA 루프

```mermaid
flowchart TD
  R[Reasoning] --> P[Planning]
  P --> A[Acting\n(Tool Calls)]
  A --> O[Observation]
  O --> R
```

### 2.2 Reflection / Self-critique / Re-asking

- 실패·불확실 시 스스로 비판하고 질문을 재정의하여 품질을 높입니다.
- 전략: 자기 설명(Self-Explanation), 근거 점검(Grounding), 대안 경로 탐색.

### 2.3 Pseudocode(안정형 루프)
```python
def agent_loop(task, budget):
    memory = []
    plan = llm.plan(task, memory)
    cost = 0
    while not done(plan) and cost < budget:
        tool = route(plan)
        result = call_tool(tool)
        observation = summarize(result)
        memory = update_memory(memory, observation)
        quality = evaluate(plan, observation)  # 규칙 + LLM Judge
        if low(quality):
            critique = llm.reflect(plan, observation)
            plan = revise(plan, critique)
        else:
            plan = next_step(plan, observation)
        cost += estimate_cost(tool, result)
    return finalize(plan, memory)
```

### 2.4 중단(Stop)·안전(Guardrail)·비용(Spend) 정책
- Stop: 성공 기준 달성, 반복 한도 도달, 근거 부족 시 중단
- Guardrail: 스키마 검증, 금지 도메인 차단, 레이트 리밋, 안전 필터
- Spend: 최대 토큰/시간/도구 호출 한도, 고비용 도구 회피·대안 경로

---

## 3. 예시 (Example)

- 웹 리서치 후 답변 초안 작성 → 품질 기준과 비교 → 보강 검색 및 수정.
- 회귀 테스트: 동일 질문 세트에 대해 변경 전/후 스코어를 비교.

---

## 4. 예상 면접 질문 (Potential Interview Questions)

- RPA와 ReAct의 차이를 설명하라.
- Reflection을 언제/어떻게 끼워 넣는가? 과한 루프는 어떻게 억제하는가?

---

## 5. 더 읽어보기 (Further Reading)

- docs/references/anthropic/building-effective-agents.md
- docs/references/google/Agents_Companion_v2.pdf

---

## 6. See also

- 프롬프트 평가/벤치마크: 5-5 → [prompt-evaluation-and-benchmarks](../5-5-프롬프트-엔지니어링-and-평가/prompt-evaluation-and-benchmarks.md)
- 운영 평가/모니터링: 5-6 → [evaluation-monitoring-ops](../5-6-agentops-운영-and-자동화/evaluation-monitoring-ops.md)
