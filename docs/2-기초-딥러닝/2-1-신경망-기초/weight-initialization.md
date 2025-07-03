---
title: "가중치 초기화 (Xavier, He 초기화)"
date: "2025-07-03"
tags: ["딥러닝", "신경망", "가중치 초기화", "기울기 소실", "기울기 폭주", "Xavier", "He"]
difficulty: "medium"
---

# 가중치 초기화 (Xavier, He 초기화)

## 1. 핵심 개념 (Core Concept)

가중치 초기화(Weight Initialization)는 신경망 학습 시작 전에 가중치 행렬을 어떤 값으로 설정할지 결정하는 중요한 단계임. 초기화가 잘못되면 학습 과정에서 **기울기 소실(Vanishing Gradient)**이나 **기울기 폭주(Exploding Gradient)** 문제가 발생하여 학습이 불안정해지거나 실패할 수 있음. 따라서 각 층의 활성화 값과 역전파되는 기울기의 분산을 일정하게 유지하여 안정적인 학습을 보장하는 초기화 방법을 사용하는 것이 필수적임.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 가중치 초기화의 중요성

신경망의 학습은 역전파 알고리즘을 통해 가중치를 점진적으로 업데이트하는 과정임. 이때 초기 가중치 값에 따라 학습의 성패가 크게 좌우됨.

*   **모두 0 또는 같은 값으로 초기화**: 모든 뉴런이 동일한 출력을 내고 동일한 그래디언트로 업데이트되는 **대칭성(symmetry) 문제**가 발생함. 이는 여러 뉴런을 사용하는 의미를 없애고 모델의 표현력을 심각하게 저해함.
*   **너무 작은 값으로 초기화**: 층을 거치면서 활성화 값이 점점 작아져 0에 수렴하고, 결국 기울기 또한 소실되어 학습이 이루어지지 않음 (기울기 소실).
*   **너무 큰 값으로 초기화**: 층을 거치면서 활성화 값이 기하급수적으로 커져 발산하고, 기울기 또한 폭주하여 학습이 불안정해짐 (기울기 폭주).

### 2.2 Xavier/Glorot 초기화

Xavier 초기화는 이전 층의 노드 수(`fan_in`)와 다음 층의 노드 수(`fan_out`)를 모두 고려하여, 각 층의 입력과 출력의 분산을 최대한 동일하게 유지하도록 설계된 방법임. 이를 통해 신호가 층을 거치면서 소실되거나 폭주하는 것을 방지함.

*   **핵심 아이디어**: `Var(w) = 2 / (fan_in + fan_out)`
*   **적합한 활성화 함수**: **시그모이드(Sigmoid)**나 **하이퍼볼릭 탄젠트(tanh)**와 같이 출력의 중심이 0이고, 양쪽으로 대칭인 활성화 함수와 함께 사용될 때 좋은 성능을 보임.
*   **한계**: ReLU 활성화 함수와 함께 사용될 경우, ReLU가 음수 입력을 모두 0으로 만들기 때문에 활성화 값의 분산이 절반으로 줄어들어 여전히 기울기 소실 문제가 발생할 수 있음.

### 2.3 He 초기화

He 초기화는 ReLU 계열의 활성화 함수에 특화된 방법으로, Xavier 초기화의 한계를 극복하기 위해 제안됨. ReLU의 특성을 고려하여, 이전 층의 노드 수(`fan_in`)만을 사용하여 분산을 조절함.

*   **핵심 아이디어**: `Var(w) = 2 / fan_in`
*   **적합한 활성화 함수**: **ReLU**, Leaky ReLU, ELU 등 비대칭적인 활성화 함수와 함께 사용될 때 효과적임. ReLU가 활성화 값의 절반을 0으로 만드는 것을 보상하기 위해 분산을 Xavier 초기화보다 2배 크게 설정하여, 신호가 층을 거쳐도 분산이 유지되도록 함.

---

## 3. 비교 요약 (Comparison Table)

| 초기화 방법 | 주요 아이디어 | 분산(Variance) | 권장 활성화 함수 |
| :--- | :--- | :--- | :--- |
| **Xavier/Glorot** | 입력과 출력의 분산을 동일하게 유지 | `2 / (fan_in + fan_out)` | Sigmoid, Tanh |
| **He** | 입력의 분산을 유지 (ReLU 특성 고려) | `2 / fan_in` | ReLU, Leaky ReLU, ELU |

### 코드 예시 (PyTorch)

'''python
import torch.nn as nn

# 선형 레이어 정의
layer_sigmoid = nn.Linear(100, 200)
layer_relu = nn.Linear(100, 200)

# Xavier 초기화 (Sigmoid/Tanh와 함께 사용)
nn.init.xavier_uniform_(layer_sigmoid.weight)

# He 초기화 (ReLU와 함께 사용)
nn.init.kaiming_uniform_(layer_relu.weight)
'''

---

## 4. 예상 면접 질문 (Potential Interview Questions)

*   **Q. 가중치를 모두 0으로 초기화하면 왜 안 되나요?**
    *   **A.** 모든 가중치를 0으로 초기화하면, 모든 뉴런이 입력에 대해 동일한 계산을 수행하고 동일한 출력을 내보냅니다. 역전파 과정에서도 모든 가중치는 동일한 그래디언트 값을 받게 되어 동일하게 업데이트됩니다. 이 '대칭성' 문제로 인해 여러 개의 뉴런을 사용하는 의미가 사라지고, 모델은 사실상 하나의 뉴런처럼 동작하여 복잡한 패턴을 전혀 학습할 수 없게 됩니다.

*   **Q. ReLU 활성화 함수를 사용할 때 Xavier 초기화 대신 He 초기화를 사용하는 이유는 무엇인가요?**
    *   **A.** Xavier 초기화는 활성화 함수가 선형적이고 대칭적이라고 가정합니다. 하지만 ReLU는 음수 입력을 모두 0으로 만드는 비대칭적인 함수입니다. 이로 인해 ReLU를 통과한 활성화 값들의 분산은 입력 분산의 절반으로 줄어듭니다. Xavier 초기화를 그대로 사용하면 층이 깊어질수록 활성화 값이 점차 작아져 결국 기울기 소실 문제가 발생할 수 있습니다. He 초기화는 이러한 ReLU의 특성을 보상하기 위해 초기 가중치의 분산을 2배 크게 설정하여, 층이 깊어져도 활성화 값의 분산이 유지되도록 만들어 안정적인 학습을 가능하게 합니다.

*   **Q. 기울기 소실(Vanishing Gradient)과 기울기 폭주(Exploding Gradient)는 무엇이며, 가중치 초기화와 어떤 관련이 있나요?**
    *   **A.** 기울기 소실은 역전파 과정에서 그래디언트가 점차 작아져 0에 가까워지는 현상이고, 기울기 폭주는 반대로 그래디언트가 비정상적으로 커지는 현상입니다. 두 현상 모두 심층 신경망의 학습을 방해하는 주요 원인입니다. 가중치 초기화는 이러한 문제와 직접적인 관련이 있습니다. 초기 가중치가 너무 작으면 층을 거치면서 신호와 그래디언트가 모두 약해져 기울기 소실이 발생하기 쉽고, 반대로 초기 가중치가 너무 크면 신호와 그래디언트가 증폭되어 기울기 폭주가 발생하기 쉽습니다. 적절한 가중치 초기화는 이 신호의 크기를 안정적으로 유지하는 데 핵심적인 역할을 합니다.

---

## 5. 더 읽어보기 (Further Reading)

*   [Understanding the difficulty of training deep feedforward neural networks (Glorot & Bengio, 2010)](http://proceedings.mlr.press/v9/glorot10a/glorot10a.pdf) - Xavier 초기화 원 논문
*   [Delving Deep into Rectifiers: Surpassing Human-Level Performance on ImageNet Classification (He et al., 2015)](https://arxiv.org/abs/1502.01852) - He 초기화 원 논문
*   [Weight Initialization in Neural Networks: A Journey From the Basics to Kaiming](https://www.analyticsvidhya.com/blog/2021/05/how-to-initialize-weights-in-neural-networks/)