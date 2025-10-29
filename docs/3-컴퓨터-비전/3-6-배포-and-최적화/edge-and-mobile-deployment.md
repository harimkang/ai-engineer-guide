---
title: 엣지·모바일 배포 (TFLite, CoreML)
date: 2025-07-04
tags:
  - 컴퓨터
  - 비전
  - 배포
  - 최적화
  - Edge-AI
  - TensorFlow-Lite
  - Core-ML
  - Mobile-Deployment
difficulty: medium
---

# 엣지·모바일 배포 (TFLite, CoreML)

## 1. 핵심 개념 (Core Concept)

TensorFlow Lite(TFLite)와 Core ML은 각각 **Android·임베디드** 및 **iOS·macOS** 생태계를 대상으로 하는 경량 추론 런타임임. 두 프레임워크 모두 **모델 변환 → 최적화(양자화·프루닝) → 하드웨어 가속(NNAPI, GPU, ANE 등)** 단계를 거쳐 온‑디바이스 추론을 최소 지연·저전력으로 수행하도록 설계됨.

______________________________________________________________________

## 2. 상세 설명 (Detailed Explanation)

### 2.1 TensorFlow Lite 배포 파이프라인

- **모델 변환** – `tf.lite.TFLiteConverter.from_saved_model()`을 사용해 SavedModel을 `.tflite` 플랫버퍼로 직렬화.

- **양자화** – Post‑Training Integer/Float16 Quantization, Quantization‑Aware Training(QAT)를 지원하여 모델 크기를 최대 4× 줄이고 CPU·DSP‑NN 가속기 성능을 향상.

- **Delegate 아키텍처** – GPU, NNAPI, Hexagon, XNNPack, Metal(Apple) 등의 하드웨어 백엔드 선택 가능한 플러그인 구조.

- **LiteRT** – 2024년에 도입된 새로운 인터프리터 API로, 실행 그래프 캐시와 자동 스레딩으로 지연 시간을 추가 단축.

- **TFLite Micro** – \< 256 KB Flash·RAM 환경을 대상으로 하는 포팅; CMSIS‑NN, ARC EM, ESP32 등 마이크로컨트롤러 전용 커널 포함.

### 2.2 Core ML 배포 파이프라인

- **모델 변환** – `coremltools.convert()`로 PyTorch·TensorFlow 모델을 `.mlmodel` 생성, 빌드 시 `.mlmodelc`로 컴파일.

- **Compute Units** – `ANE` (Neural Engine), `GPU`, `CPU` 조합을 Xcode에서 선택; 런타임에서 자동 폴백 지원.

- **모델 최적화도구** – Palettization(클러스터 양자화), 8‑bit W8A8, 4‑bit Weight Quantization, 구조적 스파시티 적용 API.

- **ML Program/Neural Engine Back‑Deployment** – iOS 17 이후 ANE 코드 생성이 확장되어 비전‑프레임워크 없이도 Swift UI에서 호출 가능.

### 2.3 TFLite vs Core ML 비교

| 특성             | TensorFlow Lite           | Core ML                            |
| :--------------- | :------------------------ | :--------------------------------- |
| 기본 타깃        | Android, Linux, MCU       | iOS, macOS, visionOS               |
| 변환 툴          | `TFLiteConverter`         | `coremltools`                      |
| 기본 포맷        | `.tflite` (FlatBuffer)    | `.mlmodel`(protobuf) → `.mlmodelc` |
| 양자화           | PTQ Int8/Float16, QAT     | W8A8·W4A8, Palettization           |
| 하드웨어 가속    | NNAPI·GPU·Hexagon·EdgeTPU | ANE·GPU·CPU·Metal                  |
| 마이크로컨트롤러 | **Lite Micro** 지원       | 공식 지원 없음                     |

### 2.4 최적화 체크리스트

1. **모델 구조 단순화** – Depthwise + Pointwise, MobileNet‑V3, EfficientNet‑Lite.

1. **연산 융합** – Conv+BN+ReLU, GEMM‑based Attention.

1. **하드웨어별 Delegate/Compute Unit 조정** – Android 12 NNAPI 1.3, iOS 17 ANE Batch 8.

1. **프로파일링** – TFLite Benchmark Tool, Xcode Metrics.

______________________________________________________________________

## 3. 예시 (Example)

### 코드 예시 (Python)

```python
# TFLite Post‑Training Int8 Conversion
import tensorflow as tf
converter = tf.lite.TFLiteConverter.from_keras_model(model)
converter.optimizations = [tf.lite.Optimize.DEFAULT]
converter.target_spec.supported_ops = [tf.lite.OpsSet.TFLITE_BUILTINS_INT8]
converter.representative_dataset = rep_dataset
converter.inference_input_type = tf.uint8
converter.inference_output_type = tf.uint8
tflite_model = converter.convert()
open("model_int8.tflite", "wb").write(tflite_model)
```

```python
# PyTorch → Core ML 8‑bit Quantization
import coremltools as ct
import torch, torchvision
model = torchvision.models.mobilenet_v3_small(pretrained=True).eval()
example = torch.rand(1,3,224,224)
traced = torch.jit.trace(model, example)
mlmodel = ct.convert(traced, inputs=[ct.ImageType(shape=example.shape)], compute_units="ALL")
mlmodel.save("mobilenetv3_w8a8.mlmodel")
```

### 사용 사례 (Use Case)

\* **오프라인 번역 앱** – 한국어 ↔ 영어 Transformer를 TFLite Float16로 변환·GPU Delegate 사용 시 중급 휴대폰에서도 \<100 ms 추론.\
\* **AR 실시간 메시 인식** – iPad Pro M4에서 Core ML ANE 실행, 4‑bit Quantization 모델을 통해 60 fps 유지.

______________________________________________________________________

## 4. 예상 면접 질문 (Potential Interview Questions)

1. **TFLite PTQ와 QAT의 차이를 설명하고 성능 trade‑off를 논하라.**

1. **Core ML Weight Palettization이 ANE latency에 미치는 효과는?**

1. **Lite Micro가 MCU에서 Tensor Ops를 지원하기 위해 취하는 메모리 전략은?**

1. **NNAPI와 GPU Delegate를 동시에 사용할 수 없는 이유는?**

1. **`.mlmodel`을 `.mlmodelc`로 컴파일할 때 이루어지는 최적화 단계를 설명하라.**

______________________________________________________________________

## 5. 더 읽어보기 (Further Reading)

- TensorFlow Lite Guide – [https://www.tensorflow.org/lite](https://www.tensorflow.org/lite)

- Google AI Edge LiteRT Docs – [https://ai.google.dev/edge](https://ai.google.dev/edge)

- Core ML Developer Site – [https://developer.apple.com/machine-learning/core-ml/](https://developer.apple.com/machine-learning/core-ml/)

- coremltools GitHub – [https://github.com/apple/coremltools](https://github.com/apple/coremltools)

- WWDC 2024 Session “Optimize Core ML Models”

- TFLite Micro GitHub – [https://github.com/tensorflow/tflite-micro](https://github.com/tensorflow/tflite-micro)
