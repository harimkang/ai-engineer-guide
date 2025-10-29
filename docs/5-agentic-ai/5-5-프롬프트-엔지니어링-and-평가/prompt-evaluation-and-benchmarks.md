---
title: "프롬프트 및 에이전트 평가: 자동화, 지표, 벤치마크"
date: "2025-10-29"
tags: ["Agentic AI", "Evaluation", "Benchmarks"]
difficulty: "medium"
---

# 프롬프트 및 에이전트 평가

## 1. 핵심 개념 (Core Concept)

정량 지표(EM/F1/BLEU/ROUGE 등)와 LLM-as-a-Judge를 결합한 자동 평가 파이프라인을 구축하고, 회귀 테스트로 안정적인 개선을 보장합니다. 평가는 일관된 데이터·스키마·로깅·승인 기준(SLO)과 함께 운영되어야 합니다.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 자동 평가 파이프라인(LLM 판정·규칙 기반 혼합)
- 단계: 데이터로드 → 전처리 → 실행(모델/프롬프트) → 스코어링 → 리포트 → 대시보드/게이팅
- 규칙 기반: 스키마 검증, 금지어/포맷, 인용 유무, 수치 정합성(간단 계산)
- LLM Judge: 포인트/페어와이즈/랭킹 평가, 심사 프롬프트와 루브릭 일관화

### 2.2 지표(태스크별)
- QA/추출: Exact Match, F1
- 요약/생성: ROUGE-L, BLEU, BERTScore, G-Eval(LLM Judge)
- 추론 깊이/품질: 루브릭 점수(근거성·정확성·간결성 등), Self-Consistency 비율

### 2.3 LLM-as-a-Judge(중재·다수결·메타평가)와 한계
- 중재 다수결: 다중 심사 후 중위수/다수결 집계, 샘플 중재 프롬프트로 편향 완화
- 한계: 자기 평정 바이어스, 프롬프트 민감, 비용. 보완: 샘플 블라인드, 다모델 심사

### 2.4 환각(Hallucination)·일관성(Self-Consistency)·근거성(Groundedness)
- 환각: 출처 유무, 사실 검증 실패 비율, 지식 컷오프 경고
- 일관성: 동일 입력의 다중 시도 결과 수렴도
- 근거성: 인용 문서 구간과 답의 정합성(문장 레벨 정합성 검사)

### 2.5 지연(Latency)·비용(Cost)·안정성(→ 5-6/5-8)
- 추적: 입력/출력 토큰, 요청 수, 툴 호출, 응답 시간 p50/p95
- 게이팅: “성능↑, 비용/지연 ≤ 한도”를 만족해야 배포

### 2.6 프롬프트 회귀 테스트(고정 셋·사전/사후 비교)
- 세트: 템플릿 테스트셋(스몰), 벤치 셋(중간), 홀드아웃(대형) 분리
- 비교: 버전 전/후 차이, 통계적 유의성(부트스트랩/랜덤화 테스트)

### 2.7 대표 벤치마크 (Representative Benchmarks)
- **AgentBench**: 운영체제, 데이터베이스, 웹 브라우징 등 8가지의 다양한 대화형 환경에서 LLM 에이전트의 추론 및 의사결정 능력을 종합적으로 평가하는 벤치마크입니다.
- **Chatbot Arena**: 두 개의 익명 모델이 동일한 프롬프트에 대해 생성한 답변을 사용자가 보고 더 나은 쪽을 투표하는 크라우드소싱 기반의 벤치마크입니다. Elo 점수 시스템을 사용하여 모델들의 순위를 매깁니다.
- **AlpacaEval**: 강력한 LLM(예: GPT-4)을 자동화된 평가자(Judge)로 사용하여, 특정 지시에 대한 모델의 응답과 표준 응답(예: text-davinci-003)을 비교하고 승률을 계산하는 방식으로 성능을 측정합니다. 빠르고 저렴하게 모델의 지시 수행 능력을 평가할 수 있습니다.

---

## 3. 예시 (Example)

### 3.1 파이프라인 다이어그램
```mermaid
flowchart LR
  D[Dataset] --> R[Runner]
  P[Prompt/Model Ver.] --> R
  R --> S[Scorer\n(규칙/LLM Judge)]
  S --> G[Gating\n(통과/보류)]
  S --> B[Dashboard/Logs]
```

### 3.2 의사코드
```python
def eval_run(dataset, prompt_v):
    rows = load(dataset)
    outputs = [run_model(prompt_v, r) for r in rows]
    scores = rule_scores(outputs) + judge_scores(rows, outputs)
    report = summarize(scores)
    gate = pass_if(report, thresholds)
    log_to_dashboard(report, prompt_v)
    return gate, report
```

---

## 4. 예상 면접 질문 (Potential Interview Questions)

- LLM-as-a-Judge의 한계와 보완책은?
- 어떤 태스크에 어떤 지표를 쓰는가? 혼합 지표는 어떻게 가중하는가?
- 회귀 테스트를 CI에 붙일 때 실패 기준·알림·롤백은?
- 비용/지연 한도에서 성능을 극대화하는 방법은?

---

## 5. 더 읽어보기 (Further Reading)

- docs/references/google/Prompt Engineering_v7.pdf
- docs/references/anthropic/building-effective-agents.md

---

## 6. 데이터·운영 팁

- 데이터: i.i.d/도메인/시간 분할, OOD/적대 샘플 포함, 누설 방지(중복 제거)
- 로깅: 입력/출력·판정·모델/프롬프트 버전·토큰/지연 메트릭 추적
- 추적/대시보드: MLflow/W&B/LangSmith, 알림(Slack/PagerDuty)
- 샌드박스: 위험 프롬프트/툴 호출 격리, 레이트 리밋, 비용 한도

---

## 7. See also

- 운영 관점 평가/모니터링: 5-6 → [evaluation-monitoring-ops](../5-6-agentops-운영-and-자동화/evaluation-monitoring-ops.md)
- 지연/비용 최적화·텔레메트리: 5-8 → [infra-and-tooling](../5-8-데이터-and-인프라/infra-and-tooling.md), [ci-cd-and-automation](../5-8-데이터-and-인프라/ci-cd-and-automation.md)
- Function/Tool Calling 프롬프트 스키마: 5-9 → [tool-schemas-jsonrpc-openapi](../5-9-보안-and-프로토콜/tool-schemas-jsonrpc-openapi.md)
