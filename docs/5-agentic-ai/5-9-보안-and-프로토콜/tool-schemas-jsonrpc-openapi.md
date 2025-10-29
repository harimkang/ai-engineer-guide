---
title: "툴 스키마: JSON-RPC & OpenAPI"
date: "2025-10-29"
tags: ["Agentic AI", "Tool Schema", "JSON-RPC", "OpenAPI"]
difficulty: "medium"
---

# 툴 스키마: JSON-RPC & OpenAPI

## 1. 핵심 개념 (Core Concept)

함수/툴 호출을 표준 스키마로 정의해 안전성과 상호운용성을 확보하고, 프롬프트 스키마와 연결하여 실패를 줄임.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 파라미터·응답 스키마와 검증
### 2.2 오류/예외 모델링과 재시도 정책
### 2.3 프롬프트 설계와의 연계(→ 5-5)

---

## 3. 예시 (Example)

- JSON-RPC 기반 날씨 API 툴 스키마 설계 개요.

---

## 4. 예상 면접 질문 (Potential Interview Questions)

- 툴 스키마 설계 시 안전성 보장 포인트는?

---

## 5. 더 읽어보기 (Further Reading)

- docs/references/openai/a-practical-guide-to-building-agents-3.pdf

---

## 6. See also

- 프롬프트 설계/스키마 연계: 5-5 → [prompt-design-optimization](../5-5-프롬프트-엔지니어링-and-평가/prompt-design-optimization.md), [prompt-evaluation-and-benchmarks](../5-5-프롬프트-엔지니어링-and-평가/prompt-evaluation-and-benchmarks.md)
- 에이전트 상호작용/프로토콜: 5-1 → [agent-interaction-and-protocols](../5-1-시스템-설계/agent-interaction-and-protocols.md)
