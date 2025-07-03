---
title: "활성화 함수 (ReLU, GELU, SiLU)"
date: "2025-07-03"
tags: ["딥러닝", "신경망", "활성화 함수", "ReLU", "GELU", "SiLU"]
difficulty: "easy"
---

# 활성화 함수 (ReLU, GELU, SiLU)

## 1. 핵심 개념 (Core Concept)

활성화 함수는 신경망에서 뉴런의 입력 신호를 비선형(non-linear) 출력 신호로 변환하는 함수임. 이 비선형 변환을 통해 신경망은 복잡한 패턴을 학습할 수 있는 능력을 갖게 됨. 대표적인 최신 활성화 함수로는 ReLU, GELU, SiLU가 있으며, 각각의 특성과 장단점에 따라 다양한 모델에서 활용됨.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 ReLU (Rectified Linear Unit)

ReLU는 가장 널리 사용되는 활성화 함수 중 하나로, 구현이 간단하고 계산 효율성이 높음.

*   **수식**: `f(x) = max(0, x)`
*   **특징**:
    *   **장점**: 양수 입력에 대해 기울기가 1로 일정하여 시그모이드(Sigmoid)나 하이퍼볼릭 탄젠트(tanh) 함수에서 발생하는 **기울기 소실(Vanishing Gradient) 문제를 완화**함. 또한, 음수 입력을 0으로 만들어 **희소 활성화(Sparse Activation)**를 유도하여 계산 효율성을 높임.
    *   **단점**: 입력이 음수일 때 기울기가 0이 되어 뉴런이 더 이상 학습되지 않는 **'죽은 ReLU(Dying ReLU)' 현상**이 발생할 수 있음.

### 2.2 GELU (Gaussian Error Linear Unit)

GELU는 입력에 가우시안 누적 분포 함수(CDF)를 곱하여 확률적으로 활성화를 조절하는 함수로, 특히 트랜스포머(Transformer) 기반 모델에서 뛰어난 성능을 보임.

*   **수식**: `GELU(x) ≈ 0.5 * x * (1 + tanh(sqrt(2/π) * (x + 0.044715 * x^3)))` (근사치)
*   **특징**:
    *   **장점**: ReLU와 달리 모든 지점에서 미분 가능하고 곡선이 부드러워 안정적인 학습을 도움. 입력의 크기에 따라 활성화를 조절하며, 음수 입력에 대해서도 작은 음수 값을 가질 수 있어 '죽은 ReLU' 문제를 방지함.
    *   **단점**: 계산이 ReLU보다 복잡함.

### 2.3 SiLU (Sigmoid Linear Unit)

SiLU는 Swish 함수라고도 불리며, 입력값에 시그모이드(Sigmoid) 함수를 곱한 형태임. 자기 제어(self-gating) 메커니즘을 통해 성능과 효율성의 균형을 맞춤.

*   **수식**: `SiLU(x) = x * σ(x)` (여기서 `σ(x)`는 시그모이드 함수)
*   **특징**:
    *   **장점**: GELU처럼 부드러운 비단조(non-monotonic) 함수로, 안정적인 학습을 도움. 양수 입력이 커지면 ReLU와 유사하게 동작하면서도, 음수 값을 일부 허용하여 정보 손실을 줄임. GELU보다 계산 효율성이 높음.
    *   **단점**: ReLU보다는 계산 비용이 높음.

### 2.4 비교 요약

| 특징 | ReLU | GELU | SiLU (Swish) |
| :--- | :--- | :--- | :--- |
| **수식** | `max(0, x)` | `x * Φ(x)` | `x * σ(x)` |
| **곡선 형태** | 꺾인 선형 | 부드러운 곡선 | 부드러운 곡선 |
| **미분 가능성** | x=0에서 미분 불가 | 모든 지점에서 미분 가능 | 모든 지점에서 미분 가능 |
| **주요 장점** | 계산 효율성, 기울기 소실 완화 | 안정적 학습, 트랜스포머에서 성능 입증 | 성능과 효율성의 균형 |
| **주요 단점** | Dying ReLU 문제 | 높은 계산 비용 | ReLU보다 높은 계산 비용 |
| **주요 사용처** | CNN 등 일반적인 딥러닝 모델 | 트랜스포머 (BERT, GPT 등) | 컴퓨터 비전 (YOLO 등) |

---

## 3. 예시 (Example)

### 코드 예시 (Python)

'''python
import torch
import torch.nn.functional as F
import numpy as np
import matplotlib.pyplot as plt

# 입력 데이터 생성
x = torch.linspace(-5, 5, 100)

# 활성화 함수 적용
relu_y = F.relu(x)
gelu_y = F.gelu(x)
silu_y = F.silu(x)

# 시각화
plt.figure(figsize=(10, 6))
plt.plot(x.numpy(), relu_y.numpy(), label="ReLU", color="red")
plt.plot(x.numpy(), gelu_y.numpy(), label="GELU", color="blue")
plt.plot(x.numpy(), silu_y.numpy(), label="SiLU (Swish)", color="green")
plt.title("Comparison of Activation Functions")
plt.xlabel("Input (x)")
plt.ylabel("Output")
plt.grid(True)
plt.legend()
plt.show()
'''

---

## 4. 예상 면접 질문 (Potential Interview Questions)

*   **Q. ReLU 함수의 장점과 단점은 무엇인가요? '죽은 ReLU' 문제는 어떻게 해결할 수 있나요?**
    *   **A.** ReLU의 장점은 계산이 간단하고 빠르며, 기울기 소실 문제를 완화하는 것입니다. 단점은 '죽은 ReLU' 문제로, 입력이 음수이면 뉴런이 비활성화되어 학습이 중단될 수 있습니다. 이 문제는 Leaky ReLU, PReLU, ELU, GELU, SiLU 등 음수 입력에 대해 작은 기울기를 허용하는 변형 함수들을 사용하여 해결할 수 있습니다.

*   **Q. 트랜스포머 모델에서 ReLU 대신 GELU를 사용하는 이유는 무엇인가요?**
    *   **A.** GELU는 ReLU와 달리 부드러운 곡선 형태를 가지며 모든 지점에서 미분 가능하여 학습을 더 안정적으로 만듭니다. 또한, 입력 값에 따라 확률적으로 활성화를 조절하는 특성이 모델의 표현력을 높여주어, 특히 자연어 처리와 같은 복잡한 태스크를 다루는 트랜스포머 모델에서 더 나은 성능을 보입니다.

*   **Q. SiLU(Swish) 함수가 ReLU에 비해 갖는 이점은 무엇인가요?**
    *   **A.** SiLU는 ReLU의 비선형성은 유지하면서도, 음수 값을 완전히 0으로 만들지 않고 일부 허용하여 정보의 손실을 줄입니다. 또한, 함수 곡선이 부드러워 최적화 과정에서 더 안정적인 경사 하강을 가능하게 합니다. 이는 결과적으로 많은 딥러닝 모델에서 ReLU보다 더 나은 정확도를 보여주는 요인이 됩니다.

---

## 5. 더 읽어보기 (Further Reading)

*   [GELU (Gaussian Error Linear Unit) 논문 (Hendrycks & Gimpel, 2016)](https://arxiv.org/abs/1606.08415)
*   [SiLU (Swish) 탐색 논문 (Ramachandran et al., 2017)](https://arxiv.org/abs/1710.05941)
*   [DeepLearning.AI: Activation Functions](https://www.deeplearning.ai/resources/natural-language-processing/activation-functions/)