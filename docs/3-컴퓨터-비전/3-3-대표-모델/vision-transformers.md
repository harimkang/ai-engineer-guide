---
title: '비전 트랜스포머: ViT, DeiT, Swin, ConvNeXt'
date: '2025-07-04'
tags: [컴퓨터 비전, 대표 모델, 트랜스포머]
difficulty: hard
---

# 비전 트랜스포머: ViT, DeiT, Swin, ConvNeXt

## 1. 핵심 개념 (Core Concept)

비전 트랜스포머(Vision Transformer)는 자연어 처리(NLP) 분야에서 성공을 거둔 트랜스포머 아키텍처를 컴퓨터 비전 태스크에 적용한 모델임. **ViT**는 이미지를 패치(patch) 시퀀스로 취급하여 트랜스포머를 직접 적용하는 가능성을 열었고, **DeiT**는 지식 증류(knowledge distillation)를 통해 적은 데이터로도 ViT를 효율적으로 학습시켰음. **Swin Transformer**는 계층적 구조와 이동 윈도우(shifted window) 기반 어텐션을 도입하여 계산 효율과 성능을 모두 잡았으며, **ConvNeXt**는 반대로 CNN을 현대화하여 트랜스포머의 성능에 필적할 수 있음을 보여준 모델임.

______________________________________________________________________

## 2. 상세 설명 (Detailed Explanation)

### 2.1 ViT (Vision Transformer)

ViT는 CNN의 컨볼루션 연산 없이 순수 트랜스포머 구조만으로 이미지 분류에서 뛰어난 성능을 달성한 최초의 모델임.

- **이미지의 시퀀스화**: 이미지를 일정한 크기(예: 16x16)의 여러 패치로 나누고, 각 패치를 flatten하여 시퀀스 데이터로 만듦.
- **Patch & Positional Embedding**: Flatten된 패치들을 선형 투사(Linear Projection)하여 벡터로 만들고(Patch Embedding), 위치 정보를 주기 위해 Positional Embedding을 더해 트랜스포머 인코더의 입력으로 사용함.
- **데이터 요구량**: CNN이 가진 지역성(locality), 병진 등변성(translation equivariance)과 같은 귀납적 편향(inductive bias)이 없기 때문에, 작은 데이터셋에서는 CNN보다 성능이 낮음. 하지만 JFT-300M과 같은 매우 큰 데이터셋에서 사전 학습할 경우 CNN을 능가하는 성능을 보임.

### 2.2 DeiT (Data-efficient Image Transformer)

DeiT는 ViT가 대규모 데이터셋에 의존해야 하는 문제를 해결하기 위해 \*\*지식 증류(Knowledge Distillation)\*\*를 도입한 모델임.

- **지식 증류**: 잘 학습된 강력한 성능의 교사 모델(Teacher Model, 보통 CNN)의 '지식'을 학생 모델(Student Model, DeiT)에게 전달하는 방식.
- **Distillation Token**: 기존의 클래스 토큰(`[CLS]`)과 별개로, 교사 모델의 예측 분포를 학습하기 위한 '증류 토큰(Distillation Token)'을 추가함. 학습 시에는 실제 레이블과 교사 모델의 예측 레이블을 모두 사용하여 손실(loss)을 계산함.
- **효율적인 학습**: 이 방식을 통해 ImageNet-1K와 같은 상대적으로 작은 데이터셋만으로도 ViT를 효과적으로 학습시켜 높은 성능을 달성함.

### 2.3 Swin Transformer

Swin Transformer는 ViT의 높은 계산 복잡도(이미지 크기에 제곱 비례)와 계층적 특징 표현의 부재라는 한계를 해결하기 위해 제안됨.

- **Windowed Attention**: 전체 이미지에 대해 어텐션을 계산하는 대신, 겹치지 않는 작은 윈도우(window) 내에서만 어텐션을 계산하여 계산량을 크게 줄임.
- **Shifted Window Attention**: 이전 레이어의 윈도우를 일정량 이동(shift)시켜 다음 레이어에서 어텐션을 계산함. 이를 통해 윈도우 간의 정보 교환이 가능해져, 지역적 어텐션만으로도 전역적인 정보를 포착하는 효과를 냄.
- **계층적 구조**: 패치를 합쳐가며(Patch Merging) 점차 깊고 의미적인 특징 맵을 만들어내는 계층적 구조를 가짐. 이는 CNN과 유사하며, 객체 탐지나 세그멘테이션 같은 다양한 비전 태스크에 쉽게 적용할 수 있게 함.

### 2.4 ConvNeXt

ConvNeXt는 "ResNet을 현대화하면 Swin Transformer만큼의 성능을 낼 수 있을까?"라는 질문에서 시작된 연구임. Swin Transformer의 설계 요소를 차용하여 기존의 ConvNet(ResNet)을 점진적으로 개선함.

- **Modernizing a ConvNet**: 학습 전략(AdamW 옵티마이저 등), 매크로 디자인(Stage별 블록 수 변경), ResNeXt-ify(Grouped Conv), Inverted Bottleneck 구조, 큰 커널 사이즈(7x7), 다양한 마이크로 디자인(GELU 활성화 함수, Layer Normalization 등)을 순차적으로 적용함.
- **결과**: 순수 CNN 아키텍처인 ConvNeXt가 동일한 복잡도에서 Swin Transformer를 능가하는 성능을 보임. 이는 트랜스포머의 성공이 아키텍처 자체의 우수성보다는 특정 설계 디테일에서 비롯되었을 수 있음을 시사함.

______________________________________________________________________

## 3. 비교 (Comparison)

| 모델         | 핵심 아이디어                | 어텐션 방식               | 주요 특징                                   |
| :----------- | :--------------------------- | :------------------------ | :------------------------------------------ |
| **ViT**      | 이미지를 패치 시퀀스로 처리  | Global Attention          | 순수 트랜스포머, 대규모 데이터 필요         |
| **DeiT**     | 지식 증류를 통한 효율적 학습 | Global Attention          | Distillation Token, 적은 데이터로 학습 가능 |
| **Swin**     | 윈도우 기반 계층적 어텐션    | Windowed / Shifted Window | 계산 효율성, 계층적 표현, 범용성            |
| **ConvNeXt** | CNN의 현대화                 | (컨볼루션 사용)           | Swin의 설계 원칙을 CNN에 적용, 순수 CNN     |

______________________________________________________________________

## 4. 예상 면접 질문 (Potential Interview Questions)

- **Q. Vision Transformer(ViT)가 CNN에 비해 갖는 장점과 단점은 무엇인가요?**
  - **A.** 장점은 어텐션 메커니즘을 통해 이미지 전반의 장거리 의존성(long-range dependency)을 잘 포착할 수 있다는 점입니다. 단점은 CNN이 가진 귀납적 편향(inductive bias)이 없어 학습에 훨씬 더 많은 데이터가 필요하고, 이미지 크기가 커질수록 계산량이 제곱으로 증가한다는 점입니다.
- **Q. Swin Transformer의 Shifted Window Attention이 왜 중요한가요?**
  - **A.** Swin Transformer는 계산량을 줄이기 위해 윈도우 내부에서만 어텐션을 계산합니다. 만약 윈도우가 고정되어 있다면 각 윈도우는 독립적으로 처리되어 서로 정보를 교환할 수 없습니다. Shifted Window는 윈도우의 위치를 다음 레이어에서 이동시킴으로써, 이전 레이어에서는 다른 윈도우에 속했던 패치들이 새로운 윈도우에서 함께 어텐션을 계산할 수 있게 합니다. 이를 통해 윈도우 간 정보 교류가 가능해집니다.
- **Q. DeiT는 어떻게 적은 데이터로 ViT를 학습시킬 수 있었나요?**
  - **A.** DeiT는 지식 증류(Knowledge Distillation) 기법을 사용합니다. 강력한 성능을 가진 사전 학습된 교사 모델(CNN)의 예측 결과를 'soft label'로 활용하여 학생 모델(DeiT)을 학습시킵니다. 이를 통해 학생 모델은 실제 정답(hard label)뿐만 아니라 교사 모델이 학습한 데이터의 풍부한 분포 정보까지 배울 수 있어, 적은 데이터로도 효율적인 학습이 가능해집니다.
- **Q. ConvNeXt 모델의 등장이 시사하는 바는 무엇이라고 생각하시나요?**
  - **A.** ConvNeXt는 Swin Transformer와 같은 최신 트랜스포머 모델의 성공이 반드시 '어텐션'이라는 메커니즘 자체 때문만은 아닐 수 있음을 보여줍니다. 오히려 최적화 기법, 네트워크 구조 설계 등 세부적인 디자인 선택들이 성능에 큰 영향을 미친다는 것을 증명했습니다. 이는 CNN 아키텍처가 여전히 발전 가능성이 있으며, 특정 문제에 대해서는 트랜스포머보다 더 나은 선택지가 될 수 있음을 시사합니다.

______________________________________________________________________

## 5. 더 읽어보기 (Further Reading)

- [An Image is Worth 16x16 Words: Transformers for Image Recognition at Scale (ViT)](https://arxiv.org/abs/2010.11929)
- [Training data-efficient image transformers & distillation through attention (DeiT)](https://arxiv.org/abs/2012.12877)
- [Swin Transformer: Hierarchical Vision Transformer using Shifted Windows](https://arxiv.org/abs/2103.14030)
- [A ConvNet for the 2020s (ConvNeXt)](https://arxiv.org/abs/2201.03545)
