---
title: "추론 최적화 & 서빙: 양자화, vLLM/Ollama/TGI"
date: "2025-10-29"
tags: ["Agentic AI", "LLM", "Serving"]
difficulty: "medium"
---

# 추론 최적화 & 서빙

## 1. 핵심 개념 (Core Concept)

INT8/INT4 양자화, KV 캐시, 연속 배칭 등으로 지연·비용을 줄이고, vLLM/Ollama/TGI/TensorRT-LLM 등 프레임워크로 운영성을 확보합니다. 멀티 테넌트·레이트 리밋·관찰 가능성까지 포함해야 실서비스가 안정화됩니다.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 양자화(Quantization)와 정확도 관리
- PTQ/QAT: 사후 양자화(PTQ)는 빠르고 쉬움, 학습 기반(QAT)은 정확도 보존↑
- 포맷: INT8(일반) / INT4(메모리 절감 극대화) / FP8(Ampere/Hopper 최적화)
- 기법: GPTQ/AWQ(가중치), KV 캐시 8b/4b, QLoRA(NF4)로 학습 메모리 절감
- 주의: 캘리브레이션 데이터 필요, 정밀도 하락 시 민감 레이어 제외(예: Embedding/LM Head)

### 2.2 배칭/스케줄링/캐시
- 연속 배칭(Continuous Batching): 서로 다른 요청을 디코딩 단계에서 합쳐 처리 → GPU 유휴시간↓
- PagedAttention(vLLM): KV 캐시를 페이지로 관리해 단편화/스왑 오버헤드↓
- 프리픽스/컨텍스트 캐시: 공통 지시문/시스템 프롬프트를 캐시 공유
- Speculative/Lookahead Decoding: Draft 모델/토큰으로 검증 가속

### 2.3 병렬화·스케일링
- 텐서 병렬(TP)·파이프라인 병렬(PP)·데이터 병렬(DP) 조합
- 멀티 GPU/노드: Infiniband/PCIe, NCCL 토폴로지 고려, 핫샤딩(모델 파편 상주)
- 레이트 리밋/큐: 테넌트별 토큰/초 제한, 우선순위 큐, 거부/폴백 정책

### 2.4 서빙 프레임워크 비교(요점)
- vLLM: PagedAttention/연속 배칭, OpenAI 호환 API, 고스루풋. 다중 테넌트/메모리 관리 강점.
- TGI: HF Transformers 기반, 텐서 병렬 지원, 안정적 운영/모니터링 생태계.
- Ollama: 로컬·데스크톱/엣지 용, GGUF 양자 모델 패키징·간편 배포.
- TensorRT-LLM: NVIDIA 최적 커널·엔진으로 초고속 서빙, 구축 복잡도↑.

### 2.5 관찰 가능성·운영
- 메트릭: qps, p50/p95 지연, 토큰/초, 토큰당 비용, 캐시 히트율, OOM/에러율
- 로깅/트레이싱: 요청/응답, 배치/스케줄 결정, GPU 사용량, 단계별 시간
- 게이팅/자동화: SLO 위반 시 배치 크기 조정/모델 폴백/롤백

---

## 3. 예시 (Example)

### 3.1 vLLM 예시(개념)
```bash
vllm serve meta-llama/Meta-Llama-3-8B-Instruct \
  --gpu-memory-utilization 0.9 \
  --max-num-batched-tokens 8192 \
  --enable-prefix-caching
```

### 3.2 TGI Docker 예시(개념)
```bash
docker run -p 8080:80 ghcr.io/huggingface/text-generation-inference:latest \
  --model-id meta-llama/Meta-Llama-3-8B-Instruct \
  --num-shard 2 --max-input-tokens 8192
```

### 3.3 Ollama Modelfile 예시(개념)
```
FROM llama3:8b
SYSTEM "너는 신중한 조수다."
PARAMETER temperature 0.6
```

---

## 4. 예상 면접 질문 (Potential Interview Questions)

- vLLM과 TGI 선택 기준은?
- 연속 배칭/Speculative의 이득과 한계는?
- 양자화로 정확도가 하락할 때 어떤 보정 전략을 쓰는가?

---

## 5. 더 읽어보기 (Further Reading)

- 4장 LLM 서빙·배포 관련 문서

---

## 6. See also

- 아키텍처/어텐션 최적화: 5-7 → [llm-architecture](./llm-architecture.md)
- 인프라/자동화: 5-8 → [infra-and-tooling](../5-8-데이터-and-인프라/infra-and-tooling.md), [ci-cd-and-automation](../5-8-데이터-and-인프라/ci-cd-and-automation.md)
- 운영/평가 게이팅: 5-6/5-5 → [evaluation-monitoring-ops](../5-6-agentops-운영-and-자동화/evaluation-monitoring-ops.md), [prompt-evaluation-and-benchmarks](../5-5-프롬프트-엔지니어링-and-평가/prompt-evaluation-and-benchmarks.md)
