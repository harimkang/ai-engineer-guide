---
title: "에이전트를 위한 데이터 엔지니어링: 합성·증강·필터링"
date: "2025-10-29"
tags: ["Agentic AI", "Data", "Engineering"]
difficulty: "medium"
---

# 에이전트를 위한 데이터 엔지니어링

## 1. 핵심 개념 (Core Concept)

에이전트 데이터는 “수집 → 정제/표준화 → 합성/증강 → 필터링/품질관리 → 버전/추적 → 학습/평가/회귀”의 수명주기를 가집니다. 합성(Self-play/Self-ask)과 품질 관리(룰 + LLM Judge)를 결합해 유효한 데이터를 꾸준히 확보하는 것이 핵심입니다.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 스키마 정의(Contract-first)
- 공통 스키마: {id, input, context, tools[], expected_output, rubric, meta(tags, source, pii, ts)}
- 태스크별 확장: 분류(label/evidence), 요약(length_limit/citations), 툴콜(params/result)
- 장점: 파이프라인 단계 간 결합도↓, 검증·로깅·게이팅 일관성↑

### 2.2 수집/정제/표준화
- 소스: 위키/문서/이슈/티켓/로그/데이터베이스/크롤링
- 정제: 포맷 정규화(HTML→MD, PDF→텍스트), 섹션/표/코드 분리, 언어 태깅
- 표준화: 문서/섹션 ID, 출처 URL/Commit, 버전(semver/date)

### 2.3 합성 데이터(Self-play/Self-ask)
- Self-ask: 복합 질문을 하위질문 분해 → 검색/툴 호출 → 최종 답 생성
- Self-play: 역할 쌍(질문자/답변자/리뷰어)로 데이터 생성, Reviewer가 루브릭 채점
- HyDE: 가설 문서 생성 후 검색 후보 확장(RAG 데이터 강화)

### 2.4 증강/필터링/중복 제거
- 증강: 동의어 치환, 형식 변환(JSON↔표), 요약/확장, 잡음 주입으로 견고성↑
- 필터링: 룰(금지어/스키마/길이/언어) + LLM Judge(근거/정확/간결)
- 중복 제거: 해시/MinHash/문장 임베딩 근접, 군집 후 대표 추출(MMR)

### 2.5 Instruction vs Dialogue 데이터셋
- Instruction: 입력→출력(단일 턴), 평가·튜닝 안정적, 스키마 강제 용이
- Dialogue/Agent: 다중 턴 + 툴 I/O + 궤적(Thought/Action/Obs). 추적·리플레이 필수

### 2.6 분할·버전·추적
- 분할: 학습/검증/홀드아웃/회귀(골든셋) 분리, 시간/도메인 분할로 누설 방지
- 버전: dataset_v, schema_v, prompt_v와 연계, 데이터 카드(Data Card) 작성
- 추적: MLflow/W&B/데이터 카탈로그에 메타데이터·지표·샘플 로그 기록

---

## 3. 예시 (Example)

### 3.1 파이프라인 다이어그램
```mermaid
flowchart LR
  S[Sources] --> C[정제/표준화]
  C --> SY[합성(Self-play/ask/HyDE)]
  SY --> A[증강]
  A --> F[필터링\n(룰+Judge)]
  F --> D[분할/버전/카달로그]
  D --> T[학습/평가/회귀]
```

### 3.2 합성 루프(의사코드)
```python
def synthesize(seed, k=3):
    items = []
    for _ in range(k):
        plan = llm.decompose(seed)
        ctx = retrieve(plan)
        out = llm.answer(seed, ctx)
        score = judge(out, rubric)
        if score >= 3.5:
            items.append({"input": seed, "context": ctx, "expected_output": out})
    return dedup(items)
```

---

## 4. 예상 면접 질문 (Potential Interview Questions)

- 합성 데이터의 편향을 낮추는 방법은?
- 데이터 누설을 방지하기 위한 분할 전략은?
- 다중 턴/툴 I/O 궤적 데이터의 스키마는 어떻게 정의하는가?

---

## 5. 더 읽어보기 (Further Reading)

- docs/references/anthropic/building-effective-agents.md
- docs/references/google/Agents_Companion_v2.pdf
- docs/references/openai/a-practical-guide-to-building-agents-3.pdf

---

## 6. 운영 팁 & See also

- 평가/게이팅: 5-5 → [prompt-evaluation-and-benchmarks](../5-5-프롬프트-엔지니어링-and-평가/prompt-evaluation-and-benchmarks.md)
- RAG 인덱싱: 5-4 → [embeddings-and-vector-dbs](../5-4-retrieval-augmented-generation-rag/embeddings-and-vector-dbs.md)
- 메모리/압축: 5-2 → [memory-architecture](../5-2-메모리-and-컨텍스트-관리/memory-architecture.md)
