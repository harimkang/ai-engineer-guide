---
title: 'ML 플랫폼 파이프라인: Kubeflow, Vertex AI, SageMaker 비교'
date: '2025-07-07'
tags: [MLOps, Pipeline Tools, Kubeflow, Vertex AI, SageMaker]
difficulty: hard
---

# ML 플랫폼 파이프라인: Kubeflow, Vertex AI, SageMaker 비교

## 1. 핵심 개념 (Core Concept)

Kubeflow, Vertex AI Pipelines, Amazon SageMaker Pipelines는 머신러닝 모델의 개발부터 배포, 관리에 이르는 전체 생명주기를 지원하는 엔드투엔드(End-to-End) MLOps 플랫폼의 핵심 파이프라인 도구임. 세 도구 모두 컨테이너 기반으로 재현 가능하고 확장성 있는 ML 워크플로우를 구축하는 것을 목표로 하지만, 오픈소스 기반의 유연성, 특정 클라우드에 최적화된 완전 관리형 서비스라는 점에서 차이를 보임.

______________________________________________________________________

## 2. 상세 설명 (Detailed Explanation)

### 2.1 도구별 철학 및 특징

- **Kubeflow Pipelines**: **쿠버네티스 네이티브(Kubernetes-Native) 및 오픈소스**. 쿠버네티스 위에서 ML 워크플로우를 구축하기 위한 오픈소스 프로젝트. 특정 클라우드에 종속되지 않고, 온프레미스나 멀티 클라우드 환경에서 이식성 높은 ML 파이프라인을 구축할 수 있는 유연성을 제공. 사용자가 직접 인프라를 관리해야 하는 부담이 있지만, 커스터마이징의 자유도가 높음.

- **Google Cloud Vertex AI Pipelines**: **통합 및 완전 관리형(Unified & Fully-Managed)**. Google Cloud의 MLOps 플랫폼인 Vertex AI에 완전히 통합된 서비스. Kubeflow Pipelines를 기반으로 구축되어 오픈소스의 유연성을 가지면서도, Google의 다른 서비스(BigQuery, GCS, Vertex AI Feature Store 등)와 긴밀하게 연동됨. 인프라 관리에 대한 부담 없이 ML 파이프라인을 구축하고 실행하는 데 초점을 맞춤.

- **Amazon SageMaker Pipelines**: **AWS 생태계 중심의 완전 관리형**. AWS의 대표적인 MLOps 서비스인 SageMaker의 일부. 데이터 준비, 모델 학습, 튜닝, 배포 등 SageMaker의 다양한 기능들을 파이프라인으로 엮어 전체 워크플로우를 자동화함. AWS 서비스에 대한 의존도가 높지만, AWS 환경에서는 가장 강력하고 포괄적인 기능을 제공.

### 2.2 비교 분석 (2025년 기준)

| 구분              | Kubeflow Pipelines                                               | Vertex AI Pipelines                                                      | SageMaker Pipelines                                                        |
| :---------------- | :--------------------------------------------------------------- | :----------------------------------------------------------------------- | :------------------------------------------------------------------------- |
| **기반 기술**     | 쿠버네티스 (오픈소스)                                            | Kubeflow 기반 (Google Cloud 관리형)                                      | AWS 네이티브 (AWS 관리형)                                                  |
| **관리 주체**     | 사용자 (직접 설치 및 운영)                                       | Google Cloud                                                             | Amazon Web Services                                                        |
| **핵심 장점**     | 클라우드 독립성, 높은 유연성, 강력한 커스터마이징                | 사용 편의성, Google Cloud 서비스와 완벽한 통합, 서버리스 실행            | 풍부한 기능, AWS 서비스와 완벽한 통합, 성숙도                              |
| **개발 경험**     | 쿠버네티스 및 YAML에 대한 높은 이해도 필요                       | 직관적인 UI 및 SDK, 낮은 인프라 관리 부담                                | SageMaker Studio 기반의 통합 개발 환경 제공                                |
| **주요 업데이트** | 멀티유저 격리 및 보안 강화, 주요 클라우드 ML 서비스와 통합 개선  | LLM 지원(튜닝, 배포) 강화, 사용자 경험 간소화                            | MLflow Tracking을 SageMaker Experiments 대체 기능으로 도입                 |
| **적합한 조직**   | 쿠버네티스 전문성을 보유하고, 특정 클라우드 종속을 피하려는 조직 | Google Cloud를 메인으로 사용하며, 빠르고 간편한 MLOps 환경을 원하는 조직 | AWS를 메인으로 사용하며, 가장 포괄적이고 성숙한 MLOps 플랫폼을 원하는 조직 |

______________________________________________________________________

## 3. 예시 (Example)

### 파이프라인 정의 방식

세 도구 모두 Python SDK를 사용하여 파이프라인을 정의하지만, 사용하는 컴포넌트와 환경 설정에서 차이가 드러남.

**Kubeflow Pipelines (kfp v2):**

```python
from kfp import dsl
from kfp.compiler import Compiler

@dsl.component
def say_hello(name: str) -> str:
    return f"Hello, {name}!"

@dsl.pipeline(name="hello-world-pipeline")
def hello_pipeline(recipient: str = "World"):
    hello_task = say_hello(name=recipient)

Compiler().compile(pipeline_func=hello_pipeline, package_path="pipeline.yaml")
```

*컴파일된 `pipeline.yaml` 파일을 Kubeflow UI에 업로드하여 실행.*

**Vertex AI Pipelines:**

```python
from kfp import dsl
from google.cloud import aiplatform

# (Kubeflow와 동일한 컴포넌트 및 파이프라인 정의 사용)
# ...

aiplatform.init(project="my-project", location="us-central1")

aiplatform.PipelineJob(
    display_name="hello-world-pipeline",
    template_path="pipeline.yaml",
    parameter_values={"recipient": "Vertex AI"}
).run()
```

*컴파일된 파일을 GCS에 업로드하고, Vertex AI SDK 또는 UI를 통해 실행.*

**SageMaker Pipelines:**

```python
import sagemaker
from sagemaker.workflow.pipeline import Pipeline
from sagemaker.workflow.steps import ProcessingStep
from sagemaker.processing import ScriptProcessor

processor = ScriptProcessor(
    image_uri="my-image", command=["python3"], ...
)

step_process = ProcessingStep(
    name="HelloWorldStep",
    processor=processor,
    code="hello_world.py"
)

pipeline = Pipeline(
    name="HelloWorldPipeline",
    steps=[step_process]
)

pipeline.upsert(role_arn="my-role-arn")
pipeline.start()
```

*SageMaker 고유의 `Step`, `Processor` 등을 사용하여 파이프라인을 구성하고 실행.*

______________________________________________________________________

## 4. 예상 면접 질문 (Potential Interview Questions)

- **Q. Kubeflow를 직접 구축하는 대신 Vertex AI나 SageMaker 같은 관리형 서비스를 사용하는 것의 장단점은 무엇인가요?**
  - **A.** 가장 큰 장점은 **인프라 관리 부담 감소**와 **생산성 향상**입니다. Vertex AI나 SageMaker를 사용하면 쿠버네티스 클러스터를 직접 설치, 설정, 운영할 필요 없이 곧바로 ML 파이프라인 개발에 집중할 수 있습니다. 또한, 해당 클라우드의 다른 데이터 서비스(예: BigQuery, S3)와 완벽하게 통합되어 있어 데이터 접근이 용이합니다. 단점은 **특정 클라우드 벤더에 대한 종속성**이 생기고, Kubeflow와 같은 오픈소스에 비해 **유연성이나 커스터마이징의 자유도가 낮을 수 있다**는 점입니다.
- **Q. Vertex AI Pipelines가 Kubeflow를 기반으로 하면서도 차별화되는 점은 무엇인가요?**
  - **A.** Vertex AI Pipelines는 Kubeflow의 오픈소스 유연성을 기반으로 하면서 Google Cloud의 **완전 관리형 서비스**라는 점이 가장 큰 차별점입니다. 사용자는 쿠버네티스 인프라를 전혀 신경 쓸 필요가 없는 **서버리스(Serverless)** 실행 환경의 이점을 누릴 수 있습니다. 또한, Vertex AI의 다른 기능들, 예를 들어 Feature Store, Model Registry, AutoML 등과 하나의 UI 및 API로 완벽하게 통합되어 있어, 파편화된 도구들을 따로 조합할 필요 없이 매끄러운 엔드투엔드 MLOps 경험을 제공합니다.
- **Q. 어떤 상황에서 SageMaker Pipelines 대신 Kubeflow를 선택하는 것이 더 합리적일까요?**
  - **A.** **멀티 클라우드 또는 하이브리드 클라우드 전략**을 사용하는 경우, 또는 **특정 클라우드 벤더에 대한 종속을 피하고 싶을 때** Kubeflow가 더 합리적인 선택입니다. Kubeflow는 쿠버네티스가 실행되는 환경이라면 어디든(AWS, GCP, Azure, 온프레미스) 동일한 파이프라인을 배포하고 실행할 수 있는 이식성을 제공합니다. 또한, 파이프라인의 모든 구성 요소를 매우 세밀하게 제어하고 커스터마이징해야 하는 특별한 요구사항이 있을 경우에도 오픈소스인 Kubeflow가 더 많은 자유도를 제공할 수 있습니다.

______________________________________________________________________

## 5. 더 읽어보기 (Further Reading)

- [Kubeflow Official Documentation](https://www.kubeflow.org/docs/)
- [Google Cloud Vertex AI Pipelines Documentation](https://cloud.google.com/vertex-ai/docs/pipelines)
- [Amazon SageMaker Pipelines Documentation](https://docs.aws.amazon.com/sagemaker/latest/dg/pipelines-sdk.html)
- [MLOps Platforms: Vertex AI vs. SageMaker vs. Azure ML (Databricks Blog)](https://www.databricks.com/blog/2023/02/15/mlops-platforms-vertex-ai-vs-sagemaker-vs-azure-ml.html)
