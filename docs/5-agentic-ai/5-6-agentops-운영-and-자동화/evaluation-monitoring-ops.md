---
title: "평가 & 모니터링: 궤적 기반 평가, 텔레메트리, 대시보드"
date: "2025-10-29"
tags: ["Agentic AI", "AgentOps", "Monitoring", "Evaluation"]
difficulty: "medium"
---

# 평가 & 모니터링 (운영 관점)

## 1. 핵심 개념 (Core Concept)

실행 궤적을 단위로 평가(T-Eval)하고, 툴 호출 성공률·추론 정확도·지연/비용을 관측하여 운영 품질을 유지함.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 Trajectory-based Evaluation(T-Eval): 단계별 툴 호출·상태 변화를 단위로 평가
### 2.2 LLM-as-a-Judge 연계(샘플링·중재·다중 심사) 및 바이어스 완화
### 2.3 Telemetry/Tracing(LangSmith/PromptLayer 등)과 실패 재현 로그
### 2.4 핵심 운영 지표: 툴 호출 성공률, 추론 정확도, 지연/비용, 회귀 실패율
### 2.5 대시보드와 알림 운영(SLO·에러 버짓·자동 재시도 정책)

---

## 3. 예시 (Example)

- 실패 패턴 알림 → 자동 재시도/격리 실행 → 리포트 생성.

---

## 4. 예상 면접 질문 (Potential Interview Questions)

- 운영 지표 중 우선순위를 어떻게 정하는가?

---

## 5. 더 읽어보기 (Further Reading)

- docs/references/anthropic/building-effective-agents.md

---

## 6. See also

- 프롬프트 평가/벤치마크: 5-5 → [prompt-evaluation-and-benchmarks](../5-5-프롬프트-엔지니어링-and-평가/prompt-evaluation-and-benchmarks.md)
- 지연/비용 최적화·자동화: 5-8 → [infra-and-tooling](../5-8-데이터-and-인프라/infra-and-tooling.md), [ci-cd-and-automation](../5-8-데이터-and-인프라/ci-cd-and-automation.md)
