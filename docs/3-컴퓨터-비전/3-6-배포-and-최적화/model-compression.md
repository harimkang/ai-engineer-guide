---
title: 모델 압축 (프루닝, 양자화, 지식 증류)
date: 2025-07-04
tags:
  - 컴퓨터
  - 비전
  - 배포
  - 최적화
  - Model-Compression
  - Pruning
  - Quantization
  - Knowledge-Distillation
difficulty: medium
---

# 모델 압축 (프루닝, 양자화, 지식 증류)

## 1. 핵심 개념 (Core Concept)

모델 압축은 **매개변수 수·메모리 사용량·연산량을 줄여** 동일한 예측 정확도를 유지하면서 추론 지연(latency)과 전력 소비를 절감하는 기술군임. ([arxiv.org](https://arxiv.org/pdf/2101.09671?utm_source=chatgpt.com)) 일반적으로 **프루닝(pruning)**은 불필요한 가중치를 제거해 희소(sparse) 구조를 만들며, **양자화(quantization)**는 부동소수점 가중치·활성값을 저비트 정수(예: INT8)나 저정밀 부동소수점(FP8)으로 표현한다. ([proceedings.mlsys.org](https://proceedings.mlsys.org/paper_files/paper/2024/file/dea9b4b6f55ae611c54065d6fc750755-Paper-Conference.pdf?utm_source=chatgpt.com)) **지식 증류(knowledge distillation)**는 크고 성능 좋은 ‘교사(teacher)’ 모델이 ‘학생(student)’ 모델에 확률 분포·특징 표현을 전이해 파라미터를 대폭 줄이면서 성능을 유지·향상시키는 방법이다. ([arxiv.org](https://arxiv.org/abs/2408.10210?utm_source=chatgpt.com))

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 프루닝 (Pruning)

- **희소성 수준과 방법**: 프루닝은 제거 단위에 따라 _비구조(unstructured)_, _채널(channel)_, _필터(filter)_, _레이어(layer)_ 수준으로 나뉘며, 구조적 프루닝일수록 하드웨어 가속이 쉽다. ([dl.acm.org](https://dl.acm.org/doi/abs/10.1109/TPAMI.2024.3447085?utm_source=chatgpt.com))
    
- **정적 vs 동적**: 학습 후 한 번에 제거하는 _정적(static)_ 방식과 학습 중 반복적으로 제거·재학습하는 _동적(dynamic)_ 방식이 있다. ([arxiv.org](https://arxiv.org/pdf/2101.09671?utm_source=chatgpt.com))
    
- **‘Lottery Ticket Hypothesis’**: 잘 설계된 초기 가중치 서브네트워크(위닝 티켓)가 전체 모델 성능을 거의 유지한다는 가설이 제시되어, 초기화 기반 프루닝 연구를 촉발함. ([arxiv.org](https://arxiv.org/abs/1803.03635?utm_source=chatgpt.com))
    
- **PyTorch 구현**: `torch.nn.utils.prune` 모듈은 임계값 프루닝(`prune.l1_unstructured`)과 채널 프루닝(`prune.ln_structured`)을 지원하며, 마스크(mask)를 저장해 가중치 복원을 가능하게 한다. ([docs.pytorch.org](https://docs.pytorch.org/tutorials/intermediate/pruning_tutorial.html?utm_source=chatgpt.com))
    

### 2.2 양자화 (Quantization)

- **사후 양자화(PTQ) vs 양자화 인식 학습(QAT)**: PTQ는 학습된 모델을 통계 기반으로 즉시 INT8/FP8로 변환하며, QAT는 학습 단계에서 양자화 오류를 보정하기 위해 가짜양자화 연산(`FakeQuant`)을 삽입해 재학습한다. ([tensorflow.org](https://www.tensorflow.org/model_optimization/guide/quantization/training?utm_source=chatgpt.com))
    
- **OpenVINO™ POT(Post‑Training Optimization Tool)**: KL‑divergence, MSE 등 6가지 캘리브레이션 방법으로 정밀도 손실을 최소화하며, CPU·NPU용 INT8 경로에 최적화된 커널을 자동 적용한다. ([community.intel.com](https://community.intel.com/t5/Blogs/Tech-Innovation/Artificial-Intelligence-AI/Model-Optimization-Pipeline-for-Inference-Speedup-with-OpenVINO/post/1335755?utm_source=chatgpt.com))
    
- **신규 FP8 포맷**: ML‑Sys 2024 논문은 표준화된 E5M2 FP8이 INT8 대비 메모리 절감률은 낮지만, 변환 오버헤드 없이 GPU Tensor Core를 활용해 최대 1.7× 속도 이득을 보고했다고 보고함. ([proceedings.mlsys.org](https://proceedings.mlsys.org/paper_files/paper/2024/file/dea9b4b6f55ae611c54065d6fc750755-Paper-Conference.pdf?utm_source=chatgpt.com))
    

### 2.3 지식 증류 (Knowledge Distillation)

- **Soft Targets & Feature Imitation**: Softmax 온도 조절(temperature scaling)로 교사 확률분포를 부드럽게 만들어 학생이 미세 신호를 학습하도록 한다. 또한 숨겨진 계층 특징을 L2 loss로 모방하는 _feature distillation_이 있다. ([arxiv.org](https://arxiv.org/pdf/2206.14366?utm_source=chatgpt.com))
    
- **Transformer 축소 사례**: _DistilBERT_는 66 M→44 M (‑40 %) 파라미터로 GLUE 평균 점수 97 %를 유지했고, _TinyBERT_는 7.5× 빠른 추론과 96.8 % 성능을 기록했다. ([arxiv.org](https://arxiv.org/abs/1909.10351?utm_source=chatgpt.com), [arxiv.org](https://arxiv.org/abs/2408.10210?utm_source=chatgpt.com))
    
- **LLM 비용 절감**: 2025년 DeepSeek 등 기업은 distillation으로 5 M USD 이하 비용으로 GPT‑3.5급 모델을 훈련해 상용화했으며, 이는 고가 GPU 수요를 줄일 ‘게임 체인저’로 평가된다. ([businessinsider.com](https://www.businessinsider.com/deepseek-openai-distillation-big-tech-trouble-cheap-commodity-ai-2025-3?utm_source=chatgpt.com), [reuters.com](https://www.reuters.com/technology/artificial-intelligence/why-blocking-chinas-deepseek-using-us-ai-may-be-difficult-2025-01-29/?utm_source=chatgpt.com))
    

### 2.4 기법 비교 및 조합

|기준|프루닝|양자화|지식 증류|
|---|---|---|---|
|주효과|파라미터·연산 제거|표현 비트폭 축소|모델 구조 축소·성능 보존|
|정확도 영향|구조 유지 시 △ 5 %↓ 이하|INT8 <1 %, FP8 무시 가능|학생 크기에 따라 다양|
|하드웨어 요구|희소성 지원 가속기|INT8/FP8 ISA|별도 없음|
|상호 보완|프루닝→양자화 순서가 일반적|QAT + KD, INT4Distil 등||

```mermaid
graph LR
    A["Baseline Model"] -->|"Prune"| B["희소 모델"]
    B -->|"Quantize"| C["INT8/FP8 모델"]
    C -->|"Distill"| D["경량 학생 모델"]
```

---

## 3. 예시 (Example)

### 코드 예시 (Python)

```python
# 1️⃣ PyTorch 비구조 프루닝 예시
import torch.nn.utils.prune as prune
for name, module in model.named_modules():
    if isinstance(module, torch.nn.Conv2d):
        prune.l1_unstructured(module, name='weight', amount=0.4)  # 40% sparsity

# 2️⃣ TensorFlow 양자화 인식 학습(QAT) 예시
import tensorflow_model_optimization as tfmot
quantize_model = tfmot.quantization.keras.quantize_model
qat_model = quantize_model(model)
qat_model.compile(optimizer='adam', loss='categorical_crossentropy')
qat_model.fit(train_ds, epochs=3)

# 3️⃣ Hugging Face Knowledge Distillation (DistilBERT 스타일)
from transformers import Trainer, TrainingArguments, DistillationConfig
config = DistillationConfig(temperature=2.0, alpha_hard=0.1)
trainer = Trainer(teacher_model=teacher, model=student, args=TrainingArguments(...), distillation_config=config)
trainer.train()
```

### 사용 사례 (Use Case)

- **스마트폰 온‑디바이스 음성 인식**: INT8 양자화된 MobileNetV3‑Small는 Snapdragon NPU에서 FP32 대비 지연이 2.3× 감소. ([tensorflow.org](https://www.tensorflow.org/model_optimization/guide/quantization/training?utm_source=chatgpt.com))
    
- **경량 LLM API**: DeepSeek‑LLM 7B 학생 모델은 OpenAI GPT‑3.5 수준 성능을 1/10 비용으로 제공, 한국 스타트업 SaaS 백엔드에 채택. ([businessinsider.com](https://www.businessinsider.com/deepseek-openai-distillation-big-tech-trouble-cheap-commodity-ai-2025-3?utm_source=chatgpt.com))
    

---

## 4. 예상 면접 질문 (Potential Interview Questions)

- **Q. 프루닝이 모델 추론 속도를 항상 높이지 못하는 이유는?**
    
    - **A.** 비구조 희소성은 메모리 불연속성을 초래해 일반 GPU에서 sparsity >90 %가 아니면 연산 병목을 해소하지 못한다. 구조적 프루닝·전용 가속기 사용 시에야 실속도를 높일 수 있음. ([dl.acm.org](https://dl.acm.org/doi/abs/10.1109/TPAMI.2024.3447085?utm_source=chatgpt.com))
        
- **Q. PTQ와 QAT의 장단점은?**
    
    - **A.** PTQ는 데이터 200‑1000 샘플만으로 수 분 내 변환 가능하지만, 활성분포가 넓은 레이어에서 정확도 저하가 클 수 있다. QAT는 학습 비용이 추가되지만 최적화된 스케일 인자를 학습해 정확도 손실이 거의 없다. ([tensorflow.org](https://www.tensorflow.org/model_optimization/guide/quantization/training?utm_source=chatgpt.com), [proceedings.mlsys.org](https://proceedings.mlsys.org/paper_files/paper/2024/file/dea9b4b6f55ae611c54065d6fc750755-Paper-Conference.pdf?utm_source=chatgpt.com))
        
- **Q. 지식 증류 시 ‘온도(temperature)’ 하이퍼파라미터가 중요한 이유는?**
    
    - **A.** 온도를 높이면 Softmax 분포가 평탄해져 교사‑학생 KLD loss에 더 많은 ‘암시적 관계’ 정보가 포함되어 학생이 일반화 능력을 얻는다. 너무 높으면 정보량이 희석되고, 너무 낮으면 하드 라벨과 유사해진다. ([arxiv.org](https://arxiv.org/pdf/2206.14366?utm_source=chatgpt.com))
        

---

## 5. 더 읽어보기 (Further Reading)

- Han et al., _Deep Compression: Pruning, Quantization and Huffman Coding_ (2016) ([arxiv.org](https://arxiv.org/pdf/2101.09671?utm_source=chatgpt.com))
    
- Frankle & Carbin, _The Lottery Ticket Hypothesis_ (2019) ([arxiv.org](https://arxiv.org/abs/1803.03635?utm_source=chatgpt.com))
    
- TensorFlow Model Optimization Guide – Quantization ([tensorflow.org](https://www.tensorflow.org/model_optimization/guide/quantization/training?utm_source=chatgpt.com))
    
- PyTorch Tutorial – Pruning �� torch.nn.utils.prune ([docs.pytorch.org](https://docs.pytorch.org/tutorials/intermediate/pruning_tutorial.html?utm_source=chatgpt.com))
    
- Intel OpenVINO POT User Guide ([community.intel.com](https://community.intel.com/t5/Blogs/Tech-Innovation/Artificial-Intelligence-AI/Model-Optimization-Pipeline-for-Inference-Speedup-with-OpenVINO/post/1335755?utm_source=chatgpt.com))
    
- Jiao et al., _TinyBERT_ (2020) ([arxiv.org](https://arxiv.org/abs/1909.10351?utm_source=chatgpt.com))
    
- Symbolic KD Survey (2024) ([arxiv.org](https://arxiv.org/abs/2408.10210?utm_source=chatgpt.com))