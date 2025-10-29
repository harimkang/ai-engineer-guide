---
title: ResNet과 변형들 (ResNeXt, WideResNet, EfficientNet)
date: '2025-07-03'
tags: [딥러닝, CNN, ResNet, ResNeXt, WideResNet, EfficientNet, 아키텍처]
difficulty: hard
---

# ResNet과 변형들 (ResNeXt, WideResNet, EfficientNet)

## 1. 핵심 개념 (Core Concept)

ResNet(Residual Network)은 \*\*잔차 학습(Residual Learning)\*\*과 \*\*스킵 연결(Skip Connection)\*\*을 도입하여, 네트워크가 깊어질수록 오히려 성능이 저하되는 **퇴화(degradation) 문제**를 해결한 혁신적인 아키텍처임. 이를 통해 전례 없이 깊은 신경망(100층 이상)의 학습을 가능하게 함. ResNet의 성공 이후, 그 아이디어를 발전시킨 ResNeXt, WideResNet, EfficientNet과 같은 다양한 변형 모델들이 등장하여 CNN 아키텍처의 발전을 이끎.

______________________________________________________________________

## 2. 상세 설명 (Detailed Explanation)

### 2.1 ResNet (Residual Network)

- **문제의식**: 신경망은 층이 깊어질수록 더 복잡한 특징을 학습할 수 있어야 하지만, 실제로는 특정 깊이를 넘어서면 학습이 더 어려워지고 성능이 저하되는 퇴화 현상이 발생함.
- **핵심 아이디어 (잔차 학습)**: 네트워크가 직접 목표 출력 `H(x)`를 학습하는 대신, 입력 `x`와의 차이인 **잔차(residual)** `F(x) = H(x) - x`를 학습하도록 구조를 변경함. 원래의 목표 출력은 `H(x) = F(x) + x`로 쉽게 복원할 수 있음.
- **스킵 연결 (Skip Connection)**: 잔차 학습을 구현하기 위해 입력 `x`를 몇 개의 층을 건너뛰어 출력에 바로 더해주는 구조. 이 연결은 그래디언트가 하위 층까지 소실되지 않고 잘 전달되도록 돕는 **고속도로(highway)** 역할을 하여 깊은 네트워크의 학습을 안정화시킴.
- **구조**: 스킵 연결을 포함하는 \*\*잔차 블록(Residual Block)\*\*을 반복적으로 쌓아 구성됨. 깊은 모델(ResNet-50 이상)에서는 연산 효율성을 위해 1x1, 3x3, 1x1 컨볼루션으로 구성된 \*\*병목 블록(Bottleneck Block)\*\*을 사용함.

### 2.2 ResNeXt

- **핵심 아이디어 (Aggregated Residual Transformations)**: ResNet의 잔차 블록을 여러 개의 병렬 경로로 나누고, 각 경로의 출력을 합산하는 방식을 제안함. 이는 "더 깊게(deeper)" 또는 "더 넓게(wider)" 가는 대신, \*\*"더 많은 경로(cardinality)"\*\*를 갖는 것이 효과적일 수 있다는 아이디어에 기반함.
- **특징**: Inception 모듈과 유사하게 여러 경로의 특징을 합치지만, 모든 경로가 동일한 구조를 가져 설계가 단순함. 적은 파라미터 증가로 성능을 효과적으로 향상시킴.

### 2.3 WideResNet (WRN)

- **핵심 아이디어**: 무작정 네트워크를 깊게 만드는 것보다, \*\*너비를 넓히는 것(채널 수를 늘리는 것)\*\*이 더 효율적이고 좋은 성능을 낼 수 있다는 것을 보여줌.
- **특징**: ResNet에 비해 깊이는 얕추고(예: 50층 -> 28층), 대신 각 컨볼루션 레이어의 채널 수를 k배(widening factor)로 늘림. 이를 통해 더 적은 층으로도 기존의 깊은 ResNet과 동등하거나 더 나은 성능을 달성하며, 병렬화에 유리하여 학습 속도도 더 빠름.

### 2.4 EfficientNet

- **핵심 아이디어 (Compound Scaling)**: 기존에는 네트워크의 깊이(depth), 너비(width), 입력 이미지의 해상도(resolution)를 개별적으로 조절했지만, EfficientNet은 이 세 가지 요소를 **균형 있게 동시에 확장**하는 것이 최적의 효율을 가져온다는 것을 증명함.
- **복합 계수(Compound Coefficient)** `φ`를 사용하여, 정해진 규칙에 따라 세 가지 차원을 체계적으로 확장함.
- **기반 구조 (EfficientNet-B0)**: 신경망 구조 탐색(NAS)을 통해 최적의 기본 모델을 설계하고, 이 모델을 복합 스케일링 방법으로 확장하여 B1부터 B7까지의 모델 시리즈를 만듦.
- **결과**: 훨씬 적은 파라미터와 연산량(FLOPS)으로 기존의 SOTA 모델들을 뛰어넘는 정확도를 달성하여, 이름 그대로 "효율적인" 네트워크의 새로운 기준을 제시함.

______________________________________________________________________

## 3. 비교 요약 (Comparison Table)

| 모델             | 주요 아이디어        | 확장 방식                    | 특징                                       |
| :--------------- | :------------------- | :--------------------------- | :----------------------------------------- |
| **ResNet**       | 잔차 학습, 스킵 연결 | 깊이 (Depth)                 | 깊은 네트워크 학습의 길을 염               |
| **ResNeXt**      | 다중 경로 집계       | 카디널리티 (Cardinality)     | Inception과 ResNet의 장점 결합             |
| **WideResNet**   | 너비 확장            | 너비 (Width)                 | 깊이보다 너비가 더 효율적일 수 있음을 보임 |
| **EfficientNet** | 복합 스케일링        | 깊이, 너비, 해상도 동시 확장 | 파라미터 및 연산량 대비 최고의 효율성 달성 |

______________________________________________________________________

## 4. 예상 면접 질문 (Potential Interview Questions)

- **Q. ResNet이 해결하고자 했던 핵심적인 문제는 무엇이며, 어떤 방법으로 해결했나요?**

  - **A.** ResNet은 네트워크가 깊어질수록 오히려 훈련 에러가 증가하는 '퇴화(degradation)' 문제를 해결하고자 했습니다. 이는 스킵 연결(skip connection)을 이용한 '잔차 학습(residual learning)'이라는 방법으로 해결했습니다. 네트워크가 직접적인 출력을 학습하는 대신 입력과의 차이, 즉 잔차를 학습하도록 만들어, 최소한 입력 그대로를 전달하는 항등 변환(identity mapping)을 쉽게 학습할 수 있도록 했습니다. 이 덕분에 층을 추가해도 성능이 저하되지 않아 매우 깊은 네트워크의 학습이 가능해졌습니다.

- **Q. ResNet의 병목 블록(Bottleneck Block)은 왜 사용되며, 어떻게 구성되어 있나요?**

  - **A.** 병목 블록은 깊은 ResNet(ResNet-50 이상)에서 연산 효율성을 높이기 위해 사용됩니다. 이는 `1x1`, `3x3`, `1x1` 세 개의 컨볼루션 레이어로 구성됩니다. 첫 번째 1x1 컨볼루션은 채널 수를 줄여(병목) 3x3 컨볼루션의 연산량을 감소시키고, 마지막 1x1 컨볼루션은 다시 원래의 채널 수로 복원하는 역할을 합니다. 이를 통해 더 적은 계산 비용으로 깊은 네트워크를 구성할 수 있습니다.

- **Q. EfficientNet이 기존 모델들과 다른 가장 큰 차별점은 무엇인가요?**

  - **A.** 가장 큰 차별점은 \*\*복합 스케일링(Compound Scaling)\*\*이라는 방법론을 제안한 것입니다. 기존 연구들이 네트워크의 깊이, 너비, 해상도 중 한 가지 차원만을 주로 고려하여 모델을 확장했던 것과 달리, EfficientNet은 이 세 가지 요소가 서로 독립적이지 않으며, 최적의 성능을 위해서는 정해진 비율에 따라 균형 있게 함께 확장해야 한다는 것을 보여주었습니다. 이를 통해 파라미터와 연산량 대비 전례 없는 수준의 효율성과 정확도를 달성했습니다.

______________________________________________________________________

## 5. 더 읽어보기 (Further Reading)

- [Deep Residual Learning for Image Recognition (He et al., 2015)](https://arxiv.org/abs/1512.03385) - ResNet 원 논문
- [Aggregated Residual Transformations for Deep Neural Networks (Xie et al., 2017)](https://arxiv.org/abs/1611.05431) - ResNeXt 원 논문
- [Wide Residual Networks (Zagoruyko & Komodakis, 2016)](https://arxiv.org/abs/1605.07146) - WideResNet 원 논문
- [EfficientNet: Rethinking Model Scaling for Convolutional Neural Networks (Tan & Le, 2019)](https://arxiv.org/abs/1905.11946) - EfficientNet 원 논문
