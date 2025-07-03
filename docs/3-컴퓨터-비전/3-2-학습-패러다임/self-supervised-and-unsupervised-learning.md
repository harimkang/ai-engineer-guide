---
title: "자기지도·비지도 학습 (SimCLR, MoCo, BYOL, MAE, DINOv2)"
date: "2025-07-03"
tags: ["컴퓨터 비전", "학습 패러다임", "자기지도 학습", "비지도 학습"]
difficulty: "hard"
---

# 자기지도·비지도 학습

## 1. 핵심 개념 (Core Concept)

**자기지도 학습(Self-Supervised Learning, SSL)**은 레이블이 없는 대규모 데이터셋을 활용하여, 데이터 자체에서 감독 신호(supervisory signal)를 만들어 모델을 학습시키는 패러다임입니다. 이는 레이블링 비용 없이도 데이터의 풍부한 시각적 표현(visual representation)을 학습하는 것을 목표로 합니다. 이렇게 사전 학습(pre-training)된 모델은 이후 소량의 레이블 데이터만으로 특정 과제(downstream task)에 미세 조정(fine-tuning)되어 높은 성능을 달성할 수 있습니다.

---

## 2. 상세 설명 (Detailed Explanation)

전통적인 **비지도 학습(Unsupervised Learning)**이 데이터의 숨겨진 구조(군집화, 차원 축소 등)를 찾는 데 중점을 두었다면, 자기지도 학습은 지도 학습의 프레임워크를 차용하여 레이블 없는 데이터로부터 스스로 문제를 만들고 푸는 방식으로 발전했습니다. 컴퓨터 비전 분야의 자기지도 학습은 크게 **대조 학습(Contrastive Learning)**과 **마스킹 기반 학습(Masking-based Learning)**으로 나눌 수 있습니다.

### 2.1 대조 학습 (Contrastive Learning)

"비슷한 것은 가깝게, 다른 것은 멀게"라는 아이디어를 기반으로 합니다. 동일한 이미지로부터 생성된 다른 버전(augmentation)의 샘플들은 '긍정 쌍(positive pair)'으로, 서로 다른 이미지의 샘플들은 '부정 쌍(negative pair)'으로 간주하여, 임베딩 공간에서 긍정 쌍은 가깝게, 부정 쌍은 멀어지도록 학습합니다.

*   **SimCLR**: 매우 큰 배치(batch)를 사용하여 배치 내의 다른 모든 이미지를 부정 쌍으로 활용하는 간단하고 강력한 프레임워크입니다. 성능을 위해 큰 배치 크기가 필수적입니다.
*   **MoCo (Momentum Contrast)**: 부정 쌍을 저장하는 동적인 큐(queue) 또는 딕셔너리를 도입하여 큰 배치 크기 문제를 해결했습니다. 모멘텀으로 업데이트되는 교사(teacher) 모델을 사용하여 일관된 표현을 학습합니다.
*   **BYOL (Bootstrap Your Own Latent)**: 부정 쌍을 아예 사용하지 않는 혁신적인 방식을 제안했습니다. 온라인(online) 네트워크가 교사 역할을 하는 타겟(target) 네트워크의 예측을 따라 하도록 학습합니다. 타겟 네트워크는 온라인 네트워크의 가중치를 지수 이동 평균(EMA)하여 천천히 업데이트함으로써 모델 붕괴(collapse)를 막습니다.

```mermaid
graph TD
    subgraph 대조 학습 (SimCLR, MoCo)
        A[이미지] --> B(Augmentation 1) --> C[인코더];
        A --> D(Augmentation 2) --> E[인코더];
        C -- 긍정 쌍 --> F{대조 손실<br>Contrastive Loss};
        E -- 긍정 쌍 --> F;
        G[다른 이미지들<br>(부정 쌍)] --> F;
    end
    subgraph 비-대조 학습 (BYOL)
        H[이미지] --> I(Augmentation 1) --> J[온라인 인코더];
        H --> K(Augmentation 2) --> L[타겟 인코더];
        J -- 예측 --> M{유사도 손실};
        L -- 타겟 --> M;
        J -- EMA 업데이트 --> L;
    end
```

### 2.2 마스킹 기반 학습 (Masking-based Learning)

자연어 처리의 BERT에서 영감을 받아, 이미지의 일부를 의도적으로 가리고 모델이 가려진 부분을 예측하도록 학습하는 방식입니다.

*   **MAE (Masked Autoencoders)**: 이미지 패치의 대부분(예: 75%)을 마스킹하고, 보이는 패치들만 인코더에 입력하여 잠재 표현을 학습합니다. 이후 가벼운 디코더가 마스킹된 부분을 포함하여 원본 이미지를 복원하도록 학습합니다. 비대칭적인 인코더-디코더 구조 덕분에 사전 학습이 매우 효율적입니다.
*   **DINOv2**: 학생-교사 구조를 사용하는 자기 증류(self-distillation) 방식으로, 학생 모델은 이미지의 일부를 가린 채 교사 모델의 출력을 예측하도록 학습합니다. 대규모로 선별된 데이터를 사용하여 특정 과제에 대한 미세 조정 없이도 뛰어난 성능을 보이는 범용 시각 특징(all-purpose visual features)을 학습하는 것을 목표로 합니다.

---

## 3. 주요 모델 비교

| 모델 | 핵심 아이디어 | 부정 쌍 사용 | 주요 특징 |
| :--- | :--- | :--- | :--- |
| **SimCLR** | 배치 내 대조 학습 | O | 큰 배치 크기 필요, 간단한 구조 |
| **MoCo** | 동적 딕셔너리 (큐) | O | 작은 배치로도 학습 가능 |
| **BYOL** | 온라인-타겟 네트워크 | X | 부정 쌍 없이 안정적인 학습 가능 |
| **MAE** | 마스크된 이미지 복원 | X | 비대칭 인코더-디코더, 매우 빠른 사전 학습 |
| **DINOv2** | 자기 증류, 마스킹 | X | 범용 시각 특징 학습, 미세 조정 없이도 강력한 성능 |

---

## 4. 예상 면접 질문 (Potential Interview Questions)

*   **Q. 자기지도 학습이 왜 중요한가요? 지도 학습과 비교하여 설명해주세요.**
    *   **A.** 지도 학습은 높은 성능을 위해 막대한 양의 레이블링된 데이터가 필요하지만, 이는 비용과 시간이 많이 듭니다. 자기지도 학습은 레이블이 없는 방대한 데이터를 활용하여 데이터의 본질적인 표현을 학습할 수 있게 해줍니다. 이를 통해 레이블링 비용을 획기적으로 줄이면서도, 사전 학습된 모델을 특정 과제에 미세 조정하여 지도 학습과 대등하거나 더 뛰어난 성능을 얻을 수 있기 때문에 매우 중요합니다.

*   **Q. 대조 학습에서 부정 쌍(negative pair)의 역할은 무엇이며, BYOL은 어떻게 부정 쌍 없이 학습이 가능한가요?**
    *   **A.** 대조 학습에서 부정 쌍은 모델이 모든 입력을 하나의 점으로 매핑하는 '모델 붕괴(model collapse)' 현상을 방지하는 중요한 역할을 합니다. 즉, 다른 이미지들은 서로 다른 표현을 갖도록 강제합니다. BYOL은 부정 쌍 대신, 서로 다른 가중치를 가진 두 개의 네트워크(온라인, 타겟)를 사용합니다. 타겟 네트워크는 온라인 네트워크의 과거 가중치를 평균낸 것이므로 항상 약간 다른 예측을 제공합니다. 온라인 네트워크가 이 약간 다른 타겟을 예측하도록 학습되면서, 모델은 붕괴를 피하고 유의미한 표현을 학습하게 됩니다.

*   **Q. MAE(Masked Autoencoders)가 BERT의 아이디어를 컴퓨터 비전에 어떻게 성공적으로 적용했나요?**
    *   **A.** MAE는 BERT의 핵심 아이디어인 '마스킹된 부분 예측'을 비전에 맞게 변형하여 적용했습니다. 텍스트와 달리 이미지는 공간적 중복성이 매우 높다는 점에 착안하여, 이미지 패치의 75%라는 매우 높은 비율을 마스킹합니다. 또한, 인코더는 보이는 패치만 처리하고 작은 디코더가 전체를 복원하는 비대칭 구조를 설계하여, 사전 학습의 계산 효율성을 극대화하면서도 모델이 이미지의 전체적인 맥락을 이해하도록 유도하는 데 성공했습니다.

---

## 5. 더 읽어보기 (Further Reading)

*   [SimCLR 논문: A Simple Framework for Contrastive Learning of Visual Representations](https://arxiv.org/abs/2002.05709)
*   [MoCo 논문: Momentum Contrast for Unsupervised Visual Representation Learning](https://arxiv.org/abs/1911.05722)
*   [BYOL 논문: Bootstrap Your Own Latent: A New Approach to Self-Supervised Learning](https://arxiv.org/abs/2006.07733)
*   [MAE 논문: Masked Autoencoders Are Scalable Vision Learners](https://arxiv.org/abs/2111.06377)
*   [DINOv2 논문: DINOv2: Learning Robust Visual Features without Supervision](https://arxiv.org/abs/2304.07193)