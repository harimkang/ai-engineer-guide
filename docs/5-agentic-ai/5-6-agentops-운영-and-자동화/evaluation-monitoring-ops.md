---
title: "평가 & 모니터링: 궤적 기반 평가, 텔레메트리, 대시보드"
date: "2025-10-29"
tags: ["Agentic AI", "AgentOps", "Monitoring", "Evaluation"]
difficulty: "medium"
---

# 평가 & 모니터링 (운영 관점)

## 1. 핵심 개념 (Core Concept)

실행 궤적을 단위로 평가(T-Eval)하고, 툴 호출 성공률·추론 정확도·지연/비용을 관측하여 운영 품질을 유지합니다. 오프라인(회귀)과 온라인(실서비스) 평가를 연계하고, 게이팅·알림·자동 폴백으로 안정성을 높입니다.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 Trajectory-based Evaluation(T-Eval)
- 단위: Thought/Action/Observation/Result, 스텝별 성공/실패/지연/비용 기록
- 목적: 어느 단계에서 실패/병목이 발생하는지 정확히 식별

### 2.2 LLM-as-a-Judge 연계(샘플링·중재·다중 심사)
- 오프라인: 벤치셋/홀드아웃 평가, 중재 다수결로 편향 완화
- 온라인: 실사용 일부 샘플 심사(Privacy 보호), 비용 버짓 관리

### 2.3 Telemetry/Tracing
- 도구: OpenTelemetry, LangSmith, PromptLayer 등
- 스팬: 프롬프트 구성, 리트리버/리랭커, 툴 호출, LLM 생성 단계로 분할
- 실패 재현: `trace_id`로 궤적 검색 → 리플레이 러너로 재현(5-6 라이프사이클 참조)

### 2.4 핵심 운영 지표(KPIs)
- 품질: EM/F1/루브릭/Judge, 회귀 실패율
- 신뢰성: 툴 호출 성공률, 스키마 위반율, 오류율(5xx/4xx)
- 효율: p50/p95 지연, 요청당 비용, 캐시 히트율

### 2.5 대시보드·알림·자동화
- SLO: 예) p95≤5s, 비용/요청≤$0.02, 스키마 위반≤1%, Judge≥3.5
- 알림: SLO 위반/버짓 소진/스파이크 감지 → 온콜
- 자동화: 재시도/격리 실행/폴백/트래픽 축소/롤백 트리거

---

## 3. 예시 (Example)

### 3.1 모니터링 파이프라인
```mermaid
flowchart LR
  E[Events\n(Traces/Logs)] --> A[Aggregator]
  A --> M[Metrics/KPIs]
  M --> D[Dashboard]
  M --> G[Gating]
  G -->|fail| FB[Fallback/Rollback]
  G -->|pass| DEP[Deploy]
```

### 3.2 T-Eval 집계 의사코드
```python
def aggregate(traces):
    kpis = {}
    for t in traces:
        for step in t.steps:
            kpis = update(kpis, step)
    return summarize(kpis)
```

---

## 4. 예상 면접 질문 (Potential Interview Questions)

- 운영 지표 중 우선순위를 어떻게 정하는가?
- 온라인/오프라인 평가의 역할 분담은?
- 비용 급증/지연 악화 시 어떤 자동화로 방어하는가?

---

## 5. 더 읽어보기 (Further Reading)

- docs/references/anthropic/building-effective-agents.md

---

## 6. See also

- 프롬프트 평가/벤치마크: 5-5 → [prompt-evaluation-and-benchmarks](../5-5-프롬프트-엔지니어링-and-평가/prompt-evaluation-and-benchmarks.md)
- 지연/비용 최적화·자동화: 5-8 → [infra-and-tooling](../5-8-데이터-and-인프라/infra-and-tooling.md), [ci-cd-and-automation](../5-8-데이터-and-인프라/ci-cd-and-automation.md)
