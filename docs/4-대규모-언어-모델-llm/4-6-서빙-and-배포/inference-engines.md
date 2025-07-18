---
title: "추론 엔진: vLLM, TensorRT-LLM, TGI"
date: "2025-07-06"
tags: ["LLM", "서빙", "추론 엔진", "최적화"]
difficulty: "hard"
---

# 추론 엔진: vLLM, TensorRT-LLM, TGI

## 1. 핵심 개념 (Core Concept)

LLM 추론 엔진은 학습된 대규모 언어 모델을 실제 서비스 환경에서 효율적으로 실행하기 위한 최적화된 소프트웨어 라이브러리입니다. 단순히 모델을 구동하는 것을 넘어, GPU 메모리를 효율적으로 관리하고, 요청을 병렬로 처리하며, 연산을 최적화하여 **높은 처리량(Throughput)**과 **낮은 지연 시간(Latency)**을 달성하는 것을 목표로 합니다. vLLM, TensorRT-LLM, TGI는 현재 가장 널리 사용되는 대표적인 LLM 추론 엔진입니다.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 vLLM

*   **개발 주체**: UC 버클리 (오픈소스 커뮤니티 주도)
*   **핵심 기술**: **PagedAttention**입니다. 이는 운영체제의 가상 메모리 페이징(Paging) 기법에서 영감을 얻은 기술로, 어텐션 연산의 핵심인 KV 캐시(Key-Value Cache)를 불연속적인 메모리 공간에 저장하고 관리합니다. 이를 통해 메모리 파편화를 최소화하고, 여러 요청 간에 프롬프트의 KV 캐시를 공유하여 메모리 사용량을 크게 줄입니다.
*   **주요 특징**: PagedAttention과 함께, 들어오는 요청을 GPU 유휴 시간 없이 계속해서 처리하는 **연속 배치(Continuous Batching)** 기술을 사용하여 처리량을 극대화합니다. 사용법이 간단하고(pip 설치), NVIDIA 외에 AMD GPU도 지원하는 등 유연성이 높습니다.

### 2.2 TensorRT-LLM

*   **개발 주체**: NVIDIA
*   **핵심 기술**: NVIDIA의 딥러닝 추론 SDK인 **TensorRT**를 기반으로 LLM에 특화된 최적화를 수행합니다. 여러 연산을 하나의 커널로 병합하는 **커널 퓨전(Kernel Fusion)**, FP8, INT4 등 낮은 정밀도를 사용하여 연산 속도를 높이는 **양자화(Quantization)**, 그리고 모델을 여러 GPU에 분산하는 **텐서 병렬 처리(Tensor Parallelism)** 등 하드웨어 수준의 최적화 기술을 집약했습니다.
*   **주요 특징**: NVIDIA GPU 하드웨어에 대한 깊은 이해를 바탕으로 최고의 성능, 특히 단일 요청에 대한 **가장 낮은 지연 시간(Latency)**을 목표로 합니다. NVIDIA Triton 추론 서버와 긴밀하게 통합되어 안정적인 프로덕션 환경을 구축하는 데 유리하지만, 모델을 TensorRT 엔진 형식으로 컴파일하는 과정이 복잡하고 NVIDIA 생태계에 종속적이라는 단점이 있습니다.

### 2.3 TGI (Text Generation Inference)

*   **개발 주체**: Hugging Face
*   **핵심 기술**: **Hugging Face 생태계와의 완벽한 통합**이 가장 큰 특징입니다. Rust 언어로 작성되어 안정성과 성능을 확보했으며, vLLM이나 TensorRT-LLM이 사용하는 연속 배치, PagedAttention, FlashAttention과 같은 최신 최적화 기술들을 대부분 내장하고 있습니다.
*   **주요 특징**: Docker를 이용한 간편한 배포, OpenTelemetry를 통한 분산 추적, Prometheus 메트릭 등 **운영(Production)에 필요한 기능**들이 잘 갖추어져 있어 사용 편의성과 안정성의 균형이 뛰어납니다. Hugging Face Hub에 있는 수많은 모델을 몇 줄의 코드로 쉽게 배포할 수 있다는 것이 가장 큰 장점입니다.

---

## 3. 기능 및 성능 비교

| 구분 | vLLM | TensorRT-LLM | TGI (Text Generation Inference) |
| :--- | :--- | :--- | :--- |
| **개발 주체** | UC Berkeley (오픈소스) | NVIDIA | Hugging Face |
| **핵심 기술** | PagedAttention, 연속 배치 | TensorRT 기반 커널 최적화, In-flight Batching | Hugging Face 생태계 통합, Rust 기반 서버 |
| **주요 장점** | **높은 처리량**, 쉬운 사용법, 하드웨어 유연성 | **낮은 지연 시간**, NVIDIA GPU에서 최고의 성능 | **사용 편의성**, 프로덕션 기능(모니터링 등) |
| **주요 단점** | 단일 요청 지연 시간은 상대적으로 높을 수 있음 | 복잡한 설치/컴파일, **NVIDIA GPU 종속** | 순수 성능은 TensorRT-LLM 대비 낮을 수 있음 |
| **사용 편의성** | **매우 높음** (pip으로 간편 설치) | **낮음** (별도 컴파일 및 설정 필요) | **높음** (Docker 기반 간편 배포) |

---

## 4. 예상 면접 질문 (Potential Interview Questions)

*   **Q. vLLM의 PagedAttention이 기존의 KV 캐시 관리 방식보다 효율적인 이유는 무엇인가요?**
    *   **A.** 기존 방식에서는 모든 요청의 KV 캐시가 연속된 메모리 블록에 할당되어야 했습니다. 이로 인해 요청마다 길이가 달라 메모리 파편화가 심하게 발생하고, 남는 공간이 있어도 새로운 요청을 처리하지 못하는 비효율이 있었습니다. PagedAttention은 KV 캐시를 작은 페이지(Page) 단위로 나누어 불연속적인 공간에 저장함으로써 이러한 파편화를 해결합니다. 덕분에 메모리를 빈틈없이 사용할 수 있고, 여러 요청이 동일한 프롬프트를 공유할 때 해당 페이지를 공유하여 메모리 사용량을 획기적으로 줄일 수 있습니다.

*   **Q. 실시간 챗봇 서비스를 개발해야 할 때, vLLM, TensorRT-LLM, TGI 중 어떤 추론 엔진을 우선적으로 고려하시겠습니까? 그 이유는 무엇인가요?**
    *   **A.** **TensorRT-LLM**을 우선적으로 고려하겠습니다. 실시간 챗봇은 사용자와의 상호작용이 중요하므로, 많은 요청을 한 번에 처리하는 처리량(Throughput)보다는 각 요청에 대한 응답 속도, 즉 지연 시간(Latency)이 더 중요한 성능 지표입니다. TensorRT-LLM은 NVIDIA GPU 하드웨어에 직접 최적화된 커널과 양자화 기술을 통해 세 엔진 중 가장 낮은 지연 시간을 제공하므로, 사용자 경험을 극대화하는 데 가장 유리합니다. 물론, NVIDIA GPU 환경이 필수이고 초기 설정이 복잡하다는 점은 감수해야 할 트레이드오프입니다.

*   **Q. Hugging Face의 TGI가 다른 엔진들과 차별화되는 가장 큰 장점은 무엇이라고 생각하시나요?**
    *   **A.** TGI의 가장 큰 장점은 **Hugging Face 생태계와의 완벽한 통합성과 프로덕션 레벨의 편의성**입니다. 개발자들은 Hugging Face Hub에 있는 수만 개의 최신 모델을 별도의 복잡한 변환 과정 없이 몇 줄의 설정만으로 즉시 배포할 수 있습니다. 또한, 모니터링, 로깅, 쉬운 배포를 위한 Docker 이미지 등 실제 서비스를 운영하는 데 필수적인 기능들이 잘 갖추어져 있어, 순수 성능 최적화에 집중하는 다른 엔진들과 달리 개발부터 운영까지 전체 사이클의 편의성을 제공한다는 점에서 차별화됩니다.

---

## 5. 더 읽어보기 (Further Reading)

*   [vLLM Project (Official Website)](https://vllm.ai/)
*   [NVIDIA TensorRT-LLM (Official GitHub)](https://github.com/NVIDIA/TensorRT-LLM)
*   [Hugging Face Text Generation Inference (Official Documentation)](https://huggingface.co/docs/text-generation-inference/index)