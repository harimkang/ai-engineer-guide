---
title: "임베딩 & 벡터 데이터베이스 심화"
date: "2025-10-29"
tags: ["Agentic AI", "Embeddings", "Vector DB"]
difficulty: "medium"
---

# 임베딩 & 벡터 데이터베이스 심화

## 1. 핵심 개념 (Core Concept)

텍스트 임베딩으로 의미 공간을 구성하고, 벡터 DB로 유사도 검색을 가속합니다. 올바른 임베딩·인덱스·검색 설정이 RAG 성능의 8할을 좌우합니다.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 임베딩 선택과 정규화
- 선택 기준: 언어(한국어/다국어), 도메인(법/의료/코드), 비용, 차원 수, 라이선스
- 일관성: 동일 모델/차원을 지키고 교체 시 일괄 재임베딩 계획
- 정규화: 코사인 거리 사용 시 L2-normalize 권장, 평균 풀링·문장 단위 토크나이즈 점검

### 2.2 인덱싱(IVF/HNSW)과 파라미터
- HNSW: 파라미터 M(간선 수), efConstruction/efSearch(정밀-속도 트레이드오프)
- IVF(FAISS): nlist(클러스터 수), nprobe(탐색 클러스터 수)
- PQ/OPQ: 메모리 절감(정밀도 손실), 중요 영역은 원본 보존 또는 OPQ 고려

### 2.3 하이브리드 검색(BM25 + Vector)
- 초기 후보: BM25(키워드)와 Vector 후보를 결합합니다. 이때 RRF(Reciprocal Rank Fusion: 각기 다른 검색 결과의 순위를 역수로 변환하여 합산, 상위에 랭크된 결과에 더 높은 가중치를 부여하는 스코어 결합 방식) 등을 사용합니다.
- 최종: 교차 인코더/LLM 리랭커로 상위 R 재정렬
- 메타 필터: 문서 타입/버전/언어/권한으로 노이즈 억제

---

## 3. 예시 (Example)

### 3.1 pgvector 스키마·인덱스(개념)
```sql
CREATE TABLE docs (
  id TEXT PRIMARY KEY,
  title TEXT,
  text TEXT,
  lang TEXT,
  tags TEXT[],
  embedding VECTOR(1536)
);
CREATE INDEX ON docs USING ivfflat (embedding vector_cosine_ops) WITH (lists = 500);
```

### 3.2 FAISS 검색 의사코드
```python
index = faiss.read_index("docs.faiss")
q = embed(query)
D, I = index.search(q, k=50)
cands = [load_doc(i) for i in I]
results = rerank_cross_encoder(query, cands, top_n=12)
```

---

## 4. 예상 면접 질문 (Potential Interview Questions)

- 코사인/내적/유클리드 거리 선택 기준은?
- 하이브리드 검색에서 가중치/스코어 결합은 어떻게 하나?
- PQ/OPQ를 언제 적용하고, 어떤 손실을 감수하는가?
- 임베딩 모델 교체·드리프트 대응 전략은?

---

## 5. 더 읽어보기 (Further Reading)

- docs/references/anthropic/effective-context-engineering-for-ai-agents.md
- docs/references/openai/a-practical-guide-to-building-agents-3.pdf
- docs/references/google/Agents_Companion_v2.pdf

---

## 6. 보안·운영 팁 & See also

- PII/비밀정보 마스킹 후 인덱싱, 접근제어(프로젝트/역할 기반)
- 중복 제거(해시/MinHash), 버전/권한 태깅으로 필터 정확도↑
- 지표·회귀 테스트는 5-5 참조 → [prompt-evaluation-and-benchmarks](../5-5-프롬프트-엔지니어링-and-평가/prompt-evaluation-and-benchmarks.md)
- 메모리/압축: 5-2 → [memory-architecture](../5-2-메모리-and-컨텍스트-관리/memory-architecture.md), [context-compression-management](../5-2-메모리-and-컨텍스트-관리/context-compression-management.md)
