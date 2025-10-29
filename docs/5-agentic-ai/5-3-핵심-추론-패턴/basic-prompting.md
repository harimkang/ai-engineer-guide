---
title: "기본 프롬프팅 기법: Zero-shot / Few-shot"
date: "2025-10-29"
tags: ["Agentic AI", "Prompting", "Basics"]
difficulty: "easy"
---

# 기본 프롬프팅 기법

## 1. 핵심 개념 (Core Concept)

Zero-shot/Few-shot 프롬프팅은 LLM의 기본 추론 품질을 좌우하는 핵심 기법입니다. 좋은 프롬프트는 “역할·목표·제약·출력 형식·평가 기준”을 명시하고, 필요한 경우 예시(Few-shot)로 분포를 교정합니다. 에이전트 상위 전략(CoT/ReAct/Reflexion 등)과 결합되면 견고성이 크게 향상됩니다.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 Zero-shot
- 핵심: 역할(Role), 목표(Task), 제약(Constraints), 출력 포맷(Output Schema), 평가 기준(Criteria)을 명확히.
- 장점: 비용·지연 최소화, 유지보수 용이. 단, 모호한 작업에는 성능 한계.

### 2.2 Few-shot(데모 선택, 순서·포맷)
- 유사 사례 k개를 선택하여 의도한 스타일/추론 경로/출력 형식을 학습 없이 유도.
- 선택 전략: 임베딩 유사도 기반 최근접 샘플, 다양성 확보(MMR)로 커버리지 향상.
- 순서·포맷: 쉬운→어려운 커리큘럼, 입력/출력의 키 필드 일관성 유지.

### 2.3 출력 스키마 고정(JSON Schema 권장)
- 파싱 안정성↑, 에이전트→툴/검증기로의 연결 용이.
- 스키마 불일치 대비: 엄격 검증 + 자동 수정 프롬프트(“형식을 교정하라”).

---

## 3. 예시 (Example)

### 3.1 Zero-shot 템플릿(요약)
시스템: 너는 엄격한 편집자다. 사용자가 제공한 글을 3문장으로 요약하라.
제약: 핵심 주장, 증거 1개, 한계 1개를 포함하라. 최대 80단어.
출력: Markdown 불릿 3개.

### 3.2 Few-shot 템플릿(분류)
시스템: 제품 리뷰를 긍정/중립/부정으로 분류하라. JSON으로만 답하라.
예시1 입력: "배송이 빨랐고 포장도 좋아요"
예시1 출력: {"label":"긍정","evidence":["배송이 빨랐고","포장도 좋다"]}
예시2 입력: "별로네… 다시는 안 살 듯"
예시2 출력: {"label":"부정","evidence":["다시는 안 산다"]}
입력: "가격은 괜찮지만 성능은 평범"
출력 스키마: {"label":"긍정|중립|부정","evidence":string[]}

### 3.3 스키마 검증 실패 시 교정 프롬프트
다음 JSON이 스키마와 불일치한다. 누락 필드를 합리적으로 채워 형식을 교정하라. 스키마: ... JSON: ...

---

## 4. 예상 면접 질문 (Potential Interview Questions)

- Few-shot 선택 전략이 결과에 미치는 영향은?
- 출력 스키마 강제의 장단점은?
- Zero-shot만으로 충분한 과제/그렇지 않은 과제는?

---

## 5. 더 읽어보기 (Further Reading)

- docs/references/google/Prompt Engineering_v7.pdf
- docs/references/openai/a-practical-guide-to-building-agents-3.pdf
- docs/references/anthropic/effective-context-engineering-for-ai-agents.md

---

## 6. See also

- 평가/벤치마크: 5-5 → [prompt-evaluation-and-benchmarks](../5-5-프롬프트-엔지니어링-and-평가/prompt-evaluation-and-benchmarks.md)
- 추론 패턴: 5-3 → [chain-of-thought-cot](./chain-of-thought-cot.md), [react](./react.md)
