---
title: "파인튜닝 & 적응: PEFT(LoRA/QLoRA), SFT/DPO"
date: "2025-10-29"
tags: ["Agentic AI", "LLM", "Fine-tuning"]
difficulty: "medium"
---

# 파인튜닝 & 적응

## 1. 핵심 개념 (Core Concept)

PEFT(LoRA/QLoRA 등)로 효율을 확보하고, SFT/DPO 등 정렬 기법을 과업·도메인에 맞춰 선택합니다. 데이터 큐레이션·평가·게이팅까지 포함한 “작게-빨리-안전하게”의 원칙이 중요합니다.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 LoRA/QLoRA·Adapter 개요
- LoRA: 선형층 W를 저랭크 A,B(랭크 r, 스케일 α)로 근사해 학습 파라미터를 크게 줄임. 대상: Wq, Wk, Wv, Wo, FFN(W1/W2) 등.
- QLoRA: 4bit(NF4)로 가중치 메모리 압축 + LoRA 학습으로 단일 GPU에서 미세적응 가능.
- 하이퍼파라미터 힌트: r∈{8,16,32}, α≈8r, dropout 0.05–0.1, 학습률 5e-5~2e-4(태스크 의존).

### 2.2 SFT vs DPO/ORPO/KTO 선택 기준
- SFT(Supervised Fine-Tuning): 정답/모범 응답이 있는 데이터로 지도학습. 장점: 안정적, 구현 간단. 한계: 선호 반영 제한.
- DPO(Direct Preference Optimization): 선호쌍(선호/비선호)으로 직접 최적화. 장점: RL 없이 선호 반영, 안정성↑. 주의: 데이터 품질/편향.
- ORPO/KTO: DPO 변형으로 안정성/효율 개선 시도. 선택은 구현 난이도·데이터에 따라.

### 2.3 데이터 전략과 오버피팅 방지
- 큐레이션: 중복 제거(해시/MinHash), PII 마스킹, 라이선스 확인, 균형 샘플링
- 합성 데이터: Self-Ask/자체 생성, Judge로 품질 필터링, 다양성 확보(MMR)
- 분할: 학습/검증/홀드아웃/회귀 셋 분리, 누설 방지
- 정규화: 라벨 노이즈 완화, 조기 종료, 샘플 가중치, Mix(일반+도메인) 비율 조절

### 2.4 학습·배포 파이프라인
- 체크포인트: LoRA 어댑터만 저장/배포, 필요 시 베이스와 병합(merge)
- 모니터링: 학습 손실/정확도 + 평가 지표/비용/지연, 스케줄 알림
- 게이팅: 5-5 기준 충족 시에만 Canary 배포(5-6)

### 2.5 안전·가드레일
- 안전 데이터 포함(유해/편향/개인정보 회피), 휴리스틱/LLM Judge로 필터
- 배포 전 레드팀 템플릿(프롬프트 공격/탈옥)으로 스모크 테스트

---

## 3. 예시 (Example)

### 3.1 PEFT 코드(개념, 의사코드)
```python
from peft import LoraConfig, get_peft_model
from transformers import AutoModelForCausalLM

model = AutoModelForCausalLM.from_pretrained(BASE, load_in_4bit=True)
cfg = LoraConfig(r=16, lora_alpha=128, lora_dropout=0.05, target_modules=["q_proj","v_proj"]) 
model = get_peft_model(model, cfg)
# dataloader: SFT or DPO pair data
train(model, dataloader, lr=1e-4, epochs=2)
model.save_pretrained("adapter")
```

### 3.2 하이퍼파라미터 레시피(예시)
- 배치: 64–256(Gradient Accumulation 병용), 시퀀스 길이=512–4096(태스크 의존)
- 옵티마이저: AdamW(β1=0.9, β2=0.95), weight decay 0.1, cosine 스케줄
- 혼합정밀: bf16/fp16, Gradient Checkpointing로 메모리 절감

---

## 4. 예상 면접 질문 (Potential Interview Questions)

- DPO의 장점과 주의점은?
- QLoRA가 유리한 조건과 한계는?
- 도메인 혼합비(일반:도메인)를 어떻게 정하는가?
- 어댑터 병합/스택의 장단점은?

---

## 5. 더 읽어보기 (Further Reading)

- 4장 LLM 파인튜닝 관련 문서

---

## 6. See also

- 평가/게이팅: 5-5 → [prompt-evaluation-and-benchmarks](../5-5-프롬프트-엔지니어링-and-평가/prompt-evaluation-and-benchmarks.md)
- 라이프사이클/배포: 5-6 → [agent-lifecycle-ops](../5-6-agentops-운영-and-자동화/agent-lifecycle-ops.md)
- 인프라/자동화: 5-8 → [ci-cd-and-automation](../5-8-데이터-and-인프라/ci-cd-and-automation.md)
