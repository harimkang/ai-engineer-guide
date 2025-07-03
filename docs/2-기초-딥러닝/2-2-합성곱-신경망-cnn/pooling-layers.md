---
title: "풀링 (최대 풀링, 평균 풀링, 전역 평균 풀링)"
date: "2025-07-03"
tags: ["딥러닝", "CNN", "풀링", "Max Pooling", "Average Pooling", "Global Average Pooling"]
difficulty: "easy"
---

# 풀링 (최대 풀링, 평균 풀링, 전역 평균 풀링)

## 1. 핵심 개념 (Core Concept)

풀링(Pooling)은 합성곱 신경망(CNN)에서 특징 맵(Feature Map)의 공간적 차원(너비와 높이)을 의도적으로 줄이는 **다운샘플링(Downsampling)** 과정임. 이를 통해 연산량을 감소시키고, 모델이 이미지 내 객체의 미세한 위치 변화에 덜 민감하게 반응하도록(위치 불변성 확보) 만들어 과적합을 방지하는 효과를 얻음. 대표적으로 최대 풀링, 평균 풀링이 있으며, 전역 평균 풀링은 특별한 형태의 풀링 기법임.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 풀링의 목적과 역할

1.  **계산 효율성 증대**: 특징 맵의 크기를 줄여 이후 레이어의 파라미터 수와 계산량을 감소시킴.
2.  **위치 불변성(Translation Invariance) 확보**: 특정 영역 내의 특징을 하나의 값으로 요약하므로, 입력 이미지에서 특징의 위치가 약간 변하더라도 풀링 후의 결과는 크게 달라지지 않음. 이는 모델을 더 강건(robust)하게 만듦.
3.  **과적합 방지**: 특징 맵을 압축하여 모델이 너무 세부적이거나 노이즈가 낀 특징에 과도하게 의존하는 것을 막아 일반화 성능을 높임.

### 2.2 최대 풀링 (Max Pooling)

지정된 영역(filter/window) 내에서 **가장 큰 값(최댓값)**을 대표값으로 선택함. 이 방식은 각 영역에서 가장 두드러진 특징(예: 엣지, 질감)을 보존하는 데 효과적이며, 결과적으로 더 날카롭고 명확한 특징을 다음 레이어로 전달함. CNN에서 가장 널리 사용되는 풀링 방식임.

### 2.3 평균 풀링 (Average Pooling)

지정된 영역 내 **모든 값의 평균**을 대표값으로 선택함. 이 방식은 영역 내의 모든 정보를 종합하므로, 특정 값에 치우치지 않고 전반적인 특성을 부드럽게 요약함. 노이즈를 줄이는 효과가 있지만, 최대 풀링에 비해 강한 특징이 희석될 수 있음.

### 2.4 전역 평균 풀링 (Global Average Pooling, GAP)

전역 평균 풀링은 각 특징 맵 **전체**를 하나의 값으로 압축하는 풀링 방식임. 즉, `H x W` 크기의 각 채널별 특징 맵을 해당 맵의 모든 픽셀 값의 평균을 계산하여 `1x1` 크기로 만듦. 예를 들어, `H x W x C` 크기의 특징 맵이 GAP 레이어를 통과하면 `1 x 1 x C` 크기의 벡터가 됨.

*   **주요 용도**: 주로 CNN 아키텍처의 마지막 부분에서 **완전 연결 계층(Fully Connected Layer)을 대체**하기 위해 사용됨.
*   **장점**:
    *   **과적합 방지**: 학습해야 할 가중치가 많은 완전 연결 계층을 대체하므로 파라미터 수가 크게 줄어 과적합 위험을 감소시킴.
    *   **모델 해석력 향상**: 각 특징 맵이 특정 클래스의 신뢰도 맵(confidence map)처럼 해석되도록 유도하여 모델의 결정을 더 직관적으로 이해할 수 있게 함.

---

## 3. 예시 (Example)

### 풀링 연산 시각화 (2x2 필터, Stride=2)

```mermaid
graph TD
    subgraph Input Feature Map (4x4)
        direction LR
        A1[1] --- A2[1] --- A3[2] --- A4[4]
        B1[5] --- B2[6] --- B3[7] --- B4[8]
        C1[3] --- C2[2] --- C3[1] --- C4[0]
        D1[1] --- D2[2] --- D3[3] --- D4[4]
    end

    subgraph Output after Max Pooling (2x2)
        direction LR
        O1[6] --- O2[8]
        O3[3] --- O4[4]
    end

    subgraph Output after Average Pooling (2x2)
        direction LR
        O1_avg[3.25] --- O2_avg[5.25]
        O3_avg[2.0] --- O4_avg[2.0]
    end

    Input Feature Map -->|Max Pooling| Output after Max Pooling
    Input Feature Map -->|Average Pooling| Output after Average Pooling
```

---

## 4. 예상 면접 질문 (Potential Interview Questions)

*   **Q. 최대 풀링(Max Pooling)과 평균 풀링(Average Pooling)의 차이점은 무엇이며, 각각 어떤 상황에서 사용하는 것이 좋습니까?**
    *   **A.** 최대 풀링은 특정 영역에서 가장 두드러진 특징(최댓값)을 추출하여 엣지나 코너 같은 중요한 정보를 보존하는 데 유리합니다. 반면, 평균 풀링은 영역 내 모든 픽셀의 평균을 계산하여 전체적인 정보를 부드럽게 요약하고 노이즈를 완화하는 효과가 있습니다. 일반적으로 객체의 명확한 특징을 잡아야 하는 이미지 분류와 같은 태스크에서는 최대 풀링이 더 선호됩니다.

*   **Q. 전역 평균 풀링(GAP)이 완전 연결 계층(Fully Connected Layer)에 비해 갖는 장점은 무엇인가요?**
    *   **A.** GAP는 학습할 파라미터가 없기 때문에 완전 연결 계층에 비해 과적합의 위험이 훨씬 적고 모델의 크기가 작아집니다. 또한, 각 특징 맵과 최종 분류 카테고리 간의 대응 관계를 강화하여 모델의 해석력을 높여줍니다. 완전 연결 계층이 입력 이미지의 크기에 제약을 받는 것과 달리, GAP는 다양한 크기의 입력에 대해 유연하게 동작할 수 있습니다.

*   **Q. 풀링 레이어 대신 Stride=2인 컨볼루션을 사용하여 다운샘플링할 수도 있습니다. 두 방식의 차이점은 무엇인가요?**
    *   **A.** 풀링 레이어는 파라미터 없이 고정된 연산(최댓값 또는 평균)을 수행하는 반면, Stride=2인 컨볼루션은 학습 가능한 파라미터(커널)를 통해 다운샘플링을 수행합니다. 따라서 컨볼루션 방식은 데이터로부터 최적의 다운샘플링 방법을 스스로 학습할 수 있다는 장점이 있어 최근의 많은 모델(ResNet 등)에서 풀링 레이어를 대체하는 경향이 있습니다. 하지만 풀링은 구현이 간단하고 계산적으로 더 가볍다는 장점이 있습니다.

---

## 5. 더 읽어보기 (Further Reading)

*   [Network in Network (Lin et al., 2013)](https://arxiv.org/abs/1312.4400) - 전역 평균 풀링 제안 논문
*   [Striving for Simplicity: The All Convolutional Net (Springenberg et al., 2014)](https://arxiv.org/abs/1412.6806) - 풀링 대신 컨볼루션을 사용한 다운샘플링
*   [CS231n: Pooling Layer](https://cs231n.github.io/convolutional-networks/#pool)