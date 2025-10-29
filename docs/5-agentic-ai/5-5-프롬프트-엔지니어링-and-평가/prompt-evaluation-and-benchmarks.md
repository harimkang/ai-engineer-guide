---
title: "프롬프트 및 에이전트 평가: 자동화, 지표, 벤치마크"
date: "2025-10-29"
tags: ["Agentic AI", "Evaluation", "Benchmarks"]
difficulty: "medium"
---

# 프롬프트 및 에이전트 평가

## 1. 핵심 개념 (Core Concept)

LLM-as-a-Judge와 정량 지표를 결합한 자동 평가 파이프라인을 구축하고, 회귀 테스트로 안정적인 개선을 보장함.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 자동 평가 파이프라인(LLM 판정·규칙 기반 혼합)
### 2.2 지표(BLEU/ROUGE/정확도/추론 깊이)
### 2.3 LLM-as-a-Judge(중재·다수결·메타평가)와 한계
### 2.4 환각(Hallucination) 탐지·출력 일관성 검사(Self-Consistency)
### 2.5 지연(Latency)·비용(Cost) 평가 및 트레이드오프(→ 5-6/5-8)
### 2.6 프롬프트 회귀 테스트(고정 셋·사전/사후 비교)

---

## 3. 예시 (Example)

- 평가 데이터셋 + 자동 스코어링 + 대시보드 연계.

---

## 4. 예상 면접 질문 (Potential Interview Questions)

- LLM-as-a-Judge의 한계와 보완책은?

---

## 5. 더 읽어보기 (Further Reading)

- docs/references/google/Prompt Engineering_v7.pdf
- docs/references/anthropic/building-effective-agents.md

---

## 6. See also

- 운영 관점 평가/모니터링: 5-6 → [evaluation-monitoring-ops](../5-6-agentops-운영-and-자동화/evaluation-monitoring-ops.md)
- 지연/비용 최적화·텔레메트리: 5-8 → [infra-and-tooling](../5-8-데이터-and-인프라/infra-and-tooling.md), [ci-cd-and-automation](../5-8-데이터-and-인프라/ci-cd-and-automation.md)
- Function/Tool Calling 프롬프트 스키마: 5-9 → [tool-schemas-jsonrpc-openapi](../5-9-보안-and-프로토콜/tool-schemas-jsonrpc-openapi.md)
