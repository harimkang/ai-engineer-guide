---
title: "프롬프트 설계 및 최적화: 템플릿, 탐색, 튜닝, 피드백"
date: "2025-10-29"
tags: ["Agentic AI", "Prompt Engineering", "Optimization"]
difficulty: "medium"
---

# 프롬프트 설계 및 최적화

## 1. 핵심 개념 (Core Concept)

명확한 목표·출력 포맷·가드레일을 정의하고, A/B·그리드 탐색과 휴먼/합성 피드백으로 성능을 체계적으로 개선합니다. Zero/Few-shot 기법과 패턴(CoT/ReAct/Reflexion)을 상황에 맞게 결합하세요.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 템플릿 구조(지시/컨텍스트/제약/출력 스키마)
- 구성: 역할(System) · 목표(User) · 제약(Do/Dont) · 출력 스키마(JSON Schema/Markdown) · 예시(Few-shot)
- 가드레일: "모르면 모른다고 답하라", 금지 도메인, PII/보안 정책, 길이/형식 제한
- 컨텍스트: 메모리·검색 결과는 출처/ID 포함, 한도 초과 시 요약 삽입(5-2/5-4)

### 2.2 기본 프롬프팅(Zero-shot / Few-shot)
- Zero-shot: 목표·제약·스키마만으로 응답 유도(간단/실시간 과제)
- Few-shot: 유사 예시 k개로 스타일/논증·스키마 정착(복잡/도메인 과제)
- 예시 선택: 임베딩 근접 + 다양성(MMR), 순서: 쉬운→어려운, 포맷: 입력/출력 키 일치

### 2.3 패턴 결합(CoT/ReAct/Reflexion)
- CoT: 단계적 추론 유도(정답 별도 명시), Self-Consistency는 5-3 참조
- ReAct: 툴 호출·관찰 로그 구조화, 실패 시 재질의/대체툴
- Reflexion: 실패 카탈로그·교정 루프로 재시도 품질 개선

### 2.4 파라미터 탐색(A/B, Grid)
- 후보: 시스템 프레이밍, 제약 문구, Few-shot 개수/순서, 스키마 엄격도, 생성 파라미터(temperature/top_p)
- 전략: A/B(소수 후보 빠른 비교) → Grid/랜덤 탐색(조합 탐색) → 수렴 후 회귀 테스트 고정

### 2.5 Prompt Tuning vs Instruction Tuning
- Prompt Tuning: 소량 벡터/소프트 프롬프트로 빠른 적응(모델 접근 필요)
- Instruction Tuning/DPO: 데이터·비용 크지만 일반화·안정성↑(모델·인프라 필요)

### 2.6 Function/Tool calling 프롬프트 스키마(→ 5-9 참조)
- JSON Schema 기반 매개변수·응답 정의, 엄격 검증 + 자동 교정 프롬프트
- 실패 정책: 재시도(백오프), 폴백, 서킷 브레이커, 비용 한도

### 2.7 피드백 메커니즘 (RLHF vs. Synthetic Feedback)
- **RLHF (Reinforcement Learning from Human Feedback)**: 사람이 직접 생성한 선호도 데이터(예: 답변 A가 B보다 낫다)를 바탕으로 보상 모델을 학습시키고, 이 보상 모델을 강화학습의 목표로 삼아 LLM을 파인튜닝하는 방식입니다. 모델의 응답을 인간의 가치에 더 잘 부합하도록 정렬(Align)하는 데 효과적입니다.
- **Synthetic Feedback (RLAIF)**: RLHF의 확장판으로, 사람 대신 더 강력한 AI 모델(LLM-as-a-Judge)이 피드백을 생성(Synthetic Feedback)하여 보상 모델을 학습시킵니다. 이를 RLAIF(Reinforcement Learning from AI Feedback)라고도 부릅니다. 사람의 개입을 최소화하여 피드백 루프를 자동화하고 확장성을 높일 수 있는 장점이 있습니다.

---

## 3. 예시 (Example)

### 3.1 템플릿 예시(요약 + 인용)
시스템: 너는 신중한 기술 편집자다. 주어진 컨텍스트로만 답하라.
제약: 근거가 불충분하면 "모른다"고 하라. 길이 120단어 이내.
출력: Markdown. 마지막에 [문서ID §섹션] 형태로 인용을 남겨라.

### 3.2 JSON 스키마 강제(분류)
시스템: 오직 JSON으로만 답하라. 스키마를 준수하라.
스키마:
```json
{"type":"object","required":["label","evidence"],
 "properties":{
   "label":{"enum":["긍정","중립","부정"]},
   "evidence":{"type":"array","items":{"type":"string"}}
 }}
```
입력: "가격은 괜찮지만 성능은 평범"

### 3.3 교정 프롬프트(스키마 불일치)
다음 JSON이 스키마와 불일치한다. 누락 필드를 추론해 보완하고 정확히 스키마로 출력하라. 스키마: … JSON: …

---

## 4. 예상 면접 질문 (Potential Interview Questions)

- Instruction Tuning과 Prompt Tuning의 장단점 비교.
- Few-shot 예시 선정/순서가 성능에 미치는 영향은?
- 스키마 강제/검증·교정의 설계 포인트는?
- ReAct/Reflexion을 프롬프트에 어떻게 구성하는가?

---

## 5. 더 읽어보기 (Further Reading)

- docs/references/google/Prompt Engineering_v7.pdf
- docs/references/openai/a-practical-guide-to-building-agents-3.pdf

---

## 6. 운영 팁 & See also

- 회귀 테스트/지표는 5-5 평가 문서 참조 → [prompt-evaluation-and-benchmarks](./prompt-evaluation-and-benchmarks.md)
- 추론 패턴: 5-3 → [chain-of-thought-cot](../5-3-핵심-추론-패턴/chain-of-thought-cot.md), [react](../5-3-핵심-추론-패턴/react.md), [reflexion](../5-3-핵심-추론-패턴/reflexion.md)
- 메모리·RAG 컨텍스트 설계: 5-2/5-4 → [memory-architecture](../5-2-메모리-and-컨텍스트-관리/memory-architecture.md), [basic-rag-pipeline](../5-4-retrieval-augmented-generation-rag/basic-rag-pipeline.md)
