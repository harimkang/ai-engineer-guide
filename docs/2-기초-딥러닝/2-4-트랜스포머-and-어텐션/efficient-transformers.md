---
title: "효율적 트랜스포머 (Linformer, Performer, FlashAttention)"
date: "2025-07-03"
tags: ["딥러닝", "트랜스포머", "효율성", "Linformer", "Performer", "FlashAttention"]
difficulty: "hard"
---

# 효율적 트랜스포머 (Linformer, Performer, FlashAttention)

## 1. 핵심 개념 (Core Concept)

표준 트랜스포머의 셀프 어텐션(Self-Attention)은 시퀀스 길이(N)에 대해 **O(N²)의 계산 및 메모리 복잡도**를 가짐. 이로 인해 긴 시퀀스를 처리할 때 계산 비용이 기하급수적으로 증가하는 병목 현상이 발생함. 효율적 트랜스포머는 이러한 한계를 극복하기 위해 어텐션 메커니즘을 근사(Approximation)하거나 I/O 효율적으로 재구성하여 복잡도를 선형(O(N))에 가깝게 낮춘 모델들을 의미함.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 문제점: 표준 어텐션의 제곱 복잡도

표준 스케일드 닷-프로덕트 어텐션(Scaled Dot-Product Attention)은 `Softmax(QKᵀ/√d_k)V` 수식으로 계산됨. 여기서 `Q`, `K`, `V` 행렬의 크기는 `N x d_k` (N은 시퀀스 길이, d_k는 헤드 차원)임. `QKᵀ`를 계산하는 과정에서 `N x N` 크기의 거대한 어텐션 행렬이 생성되므로, 시퀀스 길이가 길어질수록 메모리 요구량과 계산량이 제곱으로 증가함.

### 2.2 Linformer: 저차원 근사 (Low-Rank Approximation)

*   **핵심 아이디어**: `N x N` 어텐션 행렬이 실제로는 저차원(low-rank) 행렬로 근사될 수 있다는 관찰에서 출발함. 즉, 정보의 대부분이 일부 특이값(singular value)에 집중되어 있음.
*   **작동 방식**: `N x d_k` 크기의 `K`와 `V` 행렬에 `k x N` 크기의 선형 투영(linear projection) 행렬을 곱하여 `k x d_k` 크기의 더 작은 행렬로 압축함 (`k`는 N보다 훨씬 작은 상수). 그 후, 이 압축된 `K`, `V` 행렬을 사용하여 어텐션을 계산함. 이를 통해 `N x N` 행렬을 직접 계산하지 않고 `N x k` 크기의 어텐션 행렬을 계산하게 되어 복잡도가 **O(N)**으로 감소함.
*   **장점**: 수학적으로 증명된 근사를 통해 선형 시간 및 메모리 복잡도를 달성함.

### 2.3 Performer: 무작위 특징 커널 (Random Feature Kernels)

*   **핵심 아이디어**: 소프트맥스 함수를 커널(kernel) 함수를 이용해 근사하여, `QKᵀ`를 직접 계산하는 것을 피함.
*   **작동 방식**: FAVOR+(Fast Attention Via Positive Orthogonal Random Features)라는 기법을 사용하여 `Q`와 `K`를 무작위 직교 특징(random orthogonal features)에 매핑함. 이 새로운 특징 공간에서는 어텐션 계산 순서를 변경하여 `(Q'K'ᵀ)V` 대신 `Q'(K'ᵀV)`를 먼저 계산할 수 있게 됨. 이로써 `N x N` 행렬을 생성할 필요가 없어지며 복잡도가 **O(N)**으로 감소함.
*   **장점**: 강력한 이론적 보장을 제공하며, 기존 어텐션 메커니즘을 대체하여 사용할 수 있음(drop-in replacement).

### 2.4 FlashAttention: I/O 효율적 재구성

*   **핵심 아이디어**: 어텐션 계산의 병목이 연산(FLOPs) 자체보다 GPU의 HBM(High Bandwidth Memory)과 SRAM(On-chip SRAM) 간의 반복적인 데이터 전송에 있음을 지적함. FlashAttention은 근사 기법이 아니라, **정확한 어텐션(exact attention)**을 I/O 효율적으로 재구성한 알고리즘임.
*   **작동 방식**:
    1.  **타일링 (Tiling)**: `N x N` 어텐션 행렬을 작은 블록(tile) 단위로 나누어, 각 블록이 GPU의 빠른 SRAM에 들어갈 수 있도록 함.
    2.  **커널 융합 (Kernel Fusion)**: 소프트맥스, 드롭아웃, 행렬 곱셈 등 여러 연산을 하나의 GPU 커널로 융합하여, HBM에 중간 계산 결과를 쓰고 다시 읽어오는 과정을 최소화함.
*   **장점**: 근사 없이 정확한 어텐션을 계산하면서도, 기존 방식보다 훨씬 빠르고 메모리를 적게 사용함. 현대 LLM 학습 및 추론의 표준으로 자리 잡음.

---

## 3. 비교 요약 (Comparison Table)

| 모델 | 접근 방식 | 복잡도 | 정확도 | 핵심 아이디어 |
| :--- | :--- | :--- | :--- | :--- |
| **Linformer** | 저차원 행렬 근사 | O(N) | 근사 | K, V를 작은 차원으로 투영 |
| **Performer** | 커널(무작위 특징) 근사 | O(N) | 근사 | Softmax(QKᵀ)를 커널로 근사 |
| **FlashAttention**| I/O 효율적 재구성 | O(N²) (실제로는 훨씬 빠름) | 정확함 | GPU 메모리 접근 최소화 |

---

## 4. 예상 면접 질문 (Potential Interview Questions)

*   **Q. 표준 트랜스포머 어텐션의 가장 큰 병목은 무엇이며, 이를 해결하기 위한 접근법에는 어떤 것들이 있나요?**
    *   **A.** 가장 큰 병목은 시퀀스 길이 N에 대해 O(N²)의 계산 및 메모리 복잡도를 갖는다는 점입니다. 이를 해결하기 위한 접근법은 크게 두 가지로 나뉩니다. 첫째는 Linformer나 Performer처럼 어텐션 행렬 자체를 수학적으로 근사하여 복잡도를 O(N)으로 낮추는 방식입니다. 둘째는 FlashAttention처럼 계산 자체는 동일하게 수행하되, GPU의 메모리 I/O를 최적화하여 실제 연산 속도를 크게 향상시키는 방식입니다.

*   **Q. FlashAttention은 어텐션을 근사하는 모델이 아닌데, 어떻게 기존보다 더 빠른 속도를 낼 수 있나요?**
    *   **A.** FlashAttention은 알고리즘의 수학적 본질을 바꾼 것이 아니라, 하드웨어(GPU)에서의 실행 방식을 최적화했기 때문입니다. 기존 어텐션은 거대한 중간 계산 결과(N x N 행렬)를 GPU의 느린 HBM 메모리에 반복적으로 읽고 써야 했습니다. FlashAttention은 타일링과 커널 융합 기법을 통해 이러한 메모리 접근을 최소화하고, 대부분의 연산이 GPU의 매우 빠른 SRAM 내에서 처리되도록 만들어, 결과적으로 동일한 계산을 훨씬 빠르게 수행할 수 있습니다.

*   **Q. Linformer와 Performer는 모두 어텐션을 근사하여 O(N) 복잡도를 달성합니다. 두 모델의 근본적인 차이점은 무엇인가요?**
    *   **A.** 근본적인 차이는 근사하는 방식에 있습니다. Linformer는 어텐션 행렬이 저차원(low-rank)이라는 가정 하에, 선형 투영을 통해 키(K)와 밸류(V) 행렬의 차원을 직접 줄여서 계산량을 줄입니다. 반면, Performer는 소프트맥스 함수 자체를 무작위 특징(random features)을 이용한 커널 함수로 대체하여, `QKᵀ`라는 거대 행렬 곱셈을 피하는 방식으로 복잡도를 낮춥니다. 즉, Linformer는 행렬의 차원을, Performer는 계산 과정을 근사하는 데 중점을 둡니다.

---

## 5. 더 읽어보기 (Further Reading)

*   [Linformer: Self-Attention with Linear Complexity (Wang et al., 2020)](https://arxiv.org/abs/2006.04768)
*   [Rethinking Attention with Performers (Choromanski et al., 2020)](https://arxiv.org/abs/2009.14794)
*   [FlashAttention: Fast and Memory-Efficient Exact Attention with IO-Awareness (Dao et al., 2022)](https://arxiv.org/abs/2205.14135)