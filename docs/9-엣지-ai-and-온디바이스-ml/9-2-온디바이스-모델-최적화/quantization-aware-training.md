---
title: "정확도 손실 최소화를 위한 양자화 인식 훈련 (QAT)"
date: "2025-07-08"
tags: ["엣지 AI", "최적화", "양자화", "QAT"]
difficulty: "hard"
---

# 정확도 손실 최소화를 위한 양자화 인식 훈련 (Quantization-Aware Training)

## 1. 핵심 개념 (Core Concept)

**양자화 인식 훈련(Quantization-Aware Training, QAT)**은 모델의 학습(또는 파인튜닝) 과정에 양자화로 인한 오차를 미리 시뮬레이션하여, 모델이 양자화에 강건해지도록(robust) 훈련시키는 고급 최적화 기법입니다. 이는 학습이 끝난 모델을 양자화하는 **학습 후 양자화(Post-Training Quantization, PTQ)**에 비해 정확도 손실을 최소화할 수 있어, 모델의 성능이 매우 중요하거나 INT4와 같이 매우 낮은 비트로 양자화할 때 필수적으로 사용됩니다.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 QAT의 필요성: PTQ의 한계

PTQ는 적용이 간편하고 빠르다는 장점이 있지만, 이미 학습된 가중치를 그대로 변환하기 때문에 민감한 가중치가 양자화 과정에서 손상되어 모델의 정확도가 크게 저하될 수 있습니다. 특히, MobileNet과 같이 이미 경량화된 모델이나 매우 낮은 비트로 양자화하는 경우 이 문제가 두드러집니다. QAT는 이러한 문제를 해결하기 위해 등장했습니다.

### 2.2 QAT의 작동 원리: 가짜 양자화(Fake Quantization)

QAT의 핵심은 학습 과정에 **가짜 양자화(Fake Quantization)** 노드를 삽입하는 것입니다. 이 노드는 순전파(forward pass) 시에는 실제 양자화와 동일하게 작동하여 정밀도 손실을 시뮬레이션하고, 역전파(backward pass) 시에는 손실된 정보에 대한 그래디언트를 근사하여(Straight-Through Estimator 사용) 가중치가 업데이트되도록 합니다.

```mermaid
graph TD
    subgraph "일반 학습 (FP32)"
        A["입력"] --> B["Conv Layer"]
        B --> C["ReLU"]
        C --> D["출력"]
    end

    subgraph "QAT (INT8 시뮬레이션)"
        A_qat["입력"] --> FQ1["Fake Quantize"]
        FQ1 --> B_qat["Conv Layer"]
        B_qat --> FQ2["Fake Quantize"]
        FQ2 --> C_qat["ReLU"]
        C_qat --> FQ3["Fake Quantize"]
        FQ3 --> D_qat["출력"]
    end

    style FQ1 fill:#f9f,stroke:#333,stroke-width:2px
    style FQ2 fill:#f9f,stroke:#333,stroke-width:2px
    style FQ3 fill:#f9f,stroke:#333,stroke-width:2px
```

1.  **순전파 (Forward Pass)**: 가중치와 활성화 값은 부동소수점(FP32)에서 정수(INT8)로 양자화되었다가, 다시 부동소수점으로 역양자화됩니다. `(FP32 -> INT8 -> FP32)` 이 과정을 통해 모델은 양자화로 인해 발생할 수 있는 정보 손실(오차)을 미리 경험합니다.
2.  **역전파 (Backward Pass)**: 모델은 이 오차까지 포함된 최종 손실(loss)을 기반으로 가중치를 업데이트합니다. 즉, 모델 스스로 양자화 오차에 강건해지는 방향으로 학습을 진행하게 됩니다.
3.  **최종 변환**: QAT 학습이 완료된 후, 모델은 실제 정수 연산만 수행하는 완전한 양자화 모델로 변환됩니다.

### 2.3 PTQ vs. QAT 비교

| 구분 | 학습 후 양자화 (PTQ) | 양자화 인식 훈련 (QAT) |
| :--- | :--- | :--- |
| **정확도** | 정확도 저하 가능성 있음 (특히 소형 모델) | **원래 모델과 거의 유사한 정확도 유지** |
| **프로세스** | 간단하고 빠름 (보정 단계만 필요) | **복잡하고 오래 걸림** (재학습 또는 파인튜닝 필요) |
| **데이터 요구량** | 소량의 보정용 데이터셋 | 대량의 학습용 데이터셋 |
| **주요 사용 사례** | 대형 모델, 빠른 배포가 중요할 때 | 경량 모델, 정확도가 매우 중요할 때, INT4 등 저정밀도 양자화 |

---

## 3. 예시 (Example)

### 코드 예시 (Python - TensorFlow Model Optimization Toolkit)

TensorFlow의 `tfmot` 라이브러리를 사용하면 기존 Keras 모델에 QAT를 쉽게 적용할 수 있습니다.

```python
import tensorflow as tf
import tensorflow_model_optimization as tfmot

# 1. 원본 FP32 모델 정의
model = tf.keras.Sequential([
    tf.keras.layers.InputLayer(input_shape=(28, 28, 1)),
    tf.keras.layers.Conv2D(32, 5, activation='relu'),
    tf.keras.layers.MaxPooling2D(pool_size=(2, 2)),
    tf.keras.layers.Flatten(),
    tf.keras.layers.Dense(10)
])

# 2. QAT 적용을 위해 모델에 양자화 어노테이션 추가
quantize_model = tfmot.quantization.keras.quantize_model

q_aware_model = quantize_model(model)

# 3. QAT 모델 컴파일 및 학습 (파인튜닝)
# QAT는 일반적인 학습 과정과 동일하게 진행됨
q_aware_model.compile(optimizer='adam',
              loss=tf.keras.losses.SparseCategoricalCrossentropy(from_logits=True),
              metrics=['accuracy'])

q_aware_model.fit(train_images, train_labels, epochs=1, validation_split=0.1)

# 4. QAT 학습 후, 실제 INT8 TFLite 모델로 변환
converter = tf.lite.TFLiteConverter.from_keras_model(q_aware_model)
converter.optimizations = [tf.lite.Optimize.DEFAULT]

quantized_tflite_model = converter.convert()
```

### 사용 사례 (Use Case)

*   **스마트폰 카메라 인물 모드**: 스마트폰에서 실행되는 인물 사진 배경 흐림 효과 모델은 매우 높은 정확도가 요구됩니다. 약간의 부정확성도 사용자가 바로 인지할 수 있기 때문입니다(예: 머리카락 경계가 부자연스럽게 처리됨). 이 경우, PTQ로 인한 약간의 정확도 저하도 허용하기 어려우므로, QAT를 통해 FP32 모델과 거의 동일한 품질을 유지하는 INT8 모델을 만들어 배포합니다.

---

## 4. 예상 면접 질문 (Potential Interview Questions)

*   **Q. QAT는 학습 과정에서 양자화를 "시뮬레이션"한다고 하는데, 왜 진짜 양자화 연산을 바로 사용하지 않나요?**
    *   **A.** 그 이유는 **역전파(Backpropagation)** 때문입니다. 딥러닝 모델은 경사 하강법(Gradient Descent)을 통해 가중치를 업데이트하는데, 이를 위해서는 연산이 미분 가능해야 합니다. 하지만 양자화의 핵심인 반올림(rounding) 연산은 미분 불가능한(non-differentiable) 함수입니다. 따라서 순전파 시에는 양자화의 효과를 흉내 내어 오차를 발생시키되(가짜 양자화), 역전파 시에는 이 미분 불가능한 지점을 건너뛰고 그래디언트가 잘 흐를 수 있도록 **Straight-Through Estimator (STE)** 와 같은 근사 기법을 사용합니다. 이처럼 시뮬레이션을 통해 학습을 진행하는 것이 QAT의 핵심입니다.

*   **Q. 어떤 경우에 PTQ 대신 QAT를 반드시 사용해야 할까요?**
    *   **A.** 세 가지 경우를 들 수 있습니다. 첫째, **모델의 정확도가 매우 중요한 미션 크리티컬한 애플리케이션**일 경우입니다. 예를 들어, 의료 영상 분석 모델은 작은 정확도 저하도 치명적일 수 있습니다. 둘째, **MobileNet과 같이 이미 파라미터 수가 적은 경량 모델**을 양자화할 때입니다. 이런 모델들은 가중치의 중복성(redundancy)이 적어 양자화로 인한 성능 저하에 더 민감합니다. 셋째, **INT8보다 더 낮은 비트(예: INT4, 이진화)**로 양자화를 시도할 때입니다. 정밀도 손실이 극심해지므로, 모델이 이에 적응하는 QAT 과정 없이는 유의미한 성능을 내기 어렵습니다.

*   **Q. QAT를 적용할 때 학습 데이터 전체가 다시 필요한가요? 파인튜닝만으로도 충분한가요?**
    *   **A.** 일반적으로는 **전체 학습 데이터로 처음부터 다시 학습(retraining)할 필요는 없으며, 사전 학습된 FP32 모델을 기반으로 일부 에포크(epoch) 동안 파인튜닝하는 것만으로도 충분**합니다. QAT의 목적은 모델이 양자화 오차에 "적응"하도록 하는 것이므로, 적은 양의 데이터와 짧은 학습 시간으로도 효과를 볼 수 있습니다. 이는 QAT의 계산 비용 부담을 크게 줄여주는 중요한 특징입니다.

---

## 5. 더 읽어보기 (Further Reading)

*   [TensorFlow Model Optimization - Quantization Aware Training](https://www.tensorflow.org/model_optimization/guide/quantization/training)
*   [PyTorch - Quantization-Aware Training](https://pytorch.org/docs/stable/quantization.html#quantization-aware-training)
*   [Quantization and Training of Neural Networks for Efficient Integer-Arithmetic-Only Inference (Google Paper)](https://arxiv.org/abs/1712.05877)