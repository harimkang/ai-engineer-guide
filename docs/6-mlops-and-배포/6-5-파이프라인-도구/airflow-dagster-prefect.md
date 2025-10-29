---
title: '워크플로우 오케스트레이터: Airflow, Dagster, Prefect 비교'
date: '2025-07-07'
tags: [MLOps, Pipeline Tools, Workflow Orchestration, Airflow, Dagster, Prefect]
difficulty: hard
---

# 워크플로우 오케스트레이터: Airflow, Dagster, Prefect 비교

## 1. 핵심 개념 (Core Concept)

Airflow, Dagster, Prefect는 데이터 및 ML 파이프라인을 코드로 정의하고, 스케줄링하며, 실행을 모니터링하는 워크플로우 오케스트레이션 도구임. 세 도구 모두 파이프라인을 DAG(Directed Acyclic Graph) 형태로 관리하지만, 파이프라인을 바라보는 철학, 개발 경험, 데이터 처리 방식에서 차이점을 보이며, 이에 따라 사용 목적과 환경이 달라짐.

______________________________________________________________________

## 2. 상세 설명 (Detailed Explanation)

### 2.1 도구별 철학 및 특징

- **Apache Airflow**: **태스크 중심(Task-centric)**. 오랜 기간 데이터 엔지니어링 세계의 표준으로 자리 잡은 성숙한 도구. 파이썬 코드로 DAG를 정의하며, 강력한 확장성과 거대한 커뮤니티를 자랑함. 하지만 상대적으로 무겁고, 로컬 테스트 및 개발 경험이 다소 복잡하다는 단점이 있음.

- **Dagster**: **데이터 자산 중심(Data-asset-centric)**. 파이프라인이 생성하는 데이터(테이블, 파일, 모델 등)를 핵심으로 간주. 각 태스크가 어떤 데이터 자산을 만들고 사용하는지를 명시적으로 정의하여, 데이터 리니지(Lineage) 추적과 테스트 용이성에 강점을 가짐. 개발 단계부터 프로덕션 환경을 염두에 둔 설계가 특징.

- **Prefect**: **워크플로우 중심(Flow-centric) 및 동적 파이프라인**. 파이썬 함수를 최소한의 수정으로 프로덕션급 워크플로우(`Flow`)로 만들 수 있도록 설계됨. 동적이고 유연한 파이프라인 구성에 강점을 보이며, 간단한 API와 뛰어난 개발 경험을 제공. 실패한 태스크를 다루는 방식이 유연하고 강력함.

### 2.2 비교 분석 (2025년 기준)

| 구분              | Apache Airflow                                       | Dagster                                                    | Prefect                                                               |
| :---------------- | :--------------------------------------------------- | :--------------------------------------------------------- | :-------------------------------------------------------------------- |
| **패러다임**      | 태스크(Task) 중심                                    | 데이터 자산(Data Asset) 중심                               | 워크플로우(Flow) 중심                                                 |
| **개발 경험**     | 상대적으로 복잡, 많은 보일러플레이트 코드 필요       | 로컬 테스트 용이, 데이터 중심 디버깅 강력                  | 매우 간단, 파이썬 네이티브, 동적 워크플로우 지원                      |
| **데이터 처리**   | 태스크 간 데이터 전달이 명시적이지 않음 (XComs 사용) | 데이터 자산과 타입을 명시적으로 정의, 리니지 추적          | 태스크 간 데이터 흐름을 자동으로 관리, 결과 기반 실행                 |
| **핵심 장점**     | 성숙도, 안정성, 방대한 커뮤니티, 높은 확장성         | 데이터 품질 및 리니지 보장, 강력한 테스트 기능             | 개발 용이성, 유연성, 동적 파이프라인, 강력한 에러 핸들링              |
| **주요 업데이트** | 비동기 태스크 실행, OpenLineage 네이티브 지원        | 데이터 품질 검증(Assertion) 기능 강화, Column-level 리니지 | 하이브리드 실행 모델(Cloud/On-prem), 분산 컴퓨팅(Ray, Dask) 지원 강화 |
| **적합한 조직**   | 복잡한 ETL/데이터 파이프라인을 운영하는 대규모 조직  | 데이터 품질과 신뢰성이 최우선인 데이터 플랫폼 팀           | 빠른 개발 속도와 유연성이 중요한 스타트업 및 ML 팀                    |

______________________________________________________________________

## 3. 예시 (Example)

### 코드 스타일 비교

**Airflow:**

```python
from airflow.decorators import dag, task
from datetime import datetime

@dag(start_date=datetime(2023, 1, 1), schedule="@daily", catchup=False)
def etl_dag():
    @task
    def extract():
        return [1, 2, 3]

    @task
    def transform(data):
        return [x * 10 for x in data]

    @task
    def load(data):
        print(f"Loaded: {data}")

    load(transform(extract()))

etl_dag()
```

**Dagster:**

```python
from dagster import asset, Definitions

@asset
def extracted_data():
    return [1, 2, 3]

@asset
def transformed_data(extracted_data):
    return [x * 10 for x in extracted_data]

@asset
def loaded_data(transformed_data):
    print(f"Loaded: {transformed_data}")

defs = Definitions(assets=[extracted_data, transformed_data, loaded_data])
```

**Prefect:**

```python
from prefect import flow, task

@task
def extract():
    return [1, 2, 3]

@task
def transform(data):
    return [x * 10 for x in data]

@task
def load(data):
    print(f"Loaded: {data}")

@flow
def etl_flow():
    e = extract()
    t = transform(e)
    l = load(t)

if __name__ == "__main__":
    etl_flow()
```

______________________________________________________________________

## 4. 예상 면접 질문 (Potential Interview Questions)

- **Q. Airflow 대신 Dagster나 Prefect를 사용해야 하는 이유는 무엇일까요?**
  - **A.** Airflow는 성숙하고 강력하지만, ML 파이프라인의 특성을 모두 만족시키지는 못할 수 있습니다. **Dagster**는 데이터 품질과 리니지 추적이 매우 중요할 때 좋은 대안입니다. 모델 학습에 사용된 데이터와 그 출처를 명확히 추적하고, 각 단계에서 데이터의 유효성을 검증하는 기능이 내장되어 있어 파이프라인의 신뢰성을 높여줍니다. **Prefect**는 파이프라인이 동적으로 변하거나, 빠른 프로토타이핑과 개발 속도가 중요할 때 유리합니다. 파이썬 코드를 거의 그대로 워크플로우로 만들 수 있어 개발 경험이 뛰어나고, 실패한 태스크를 처리하는 메커니즘이 유연하여 복잡한 ML 실험을 관리하기에 용이합니다.
- **Q. Dagster의 '데이터 자산(Data Asset)'이라는 개념이 MLOps에서 왜 중요한가요?**
  - **A.** MLOps에서 재현성과 추적성은 매우 중요합니다. Dagster의 데이터 자산 개념은 파이프라인의 각 단계가 어떤 데이터를 입력으로 받고 어떤 데이터를 출력하는지를 명시적으로 정의하게 합니다. 이는 단순히 태스크의 성공/실패를 넘어, 생성된 데이터(전처리된 데이터셋, 학습된 모델, 평가 결과 등)의 버전과 상태를 관리할 수 있게 해줍니다. 결과적으로, 특정 모델이 어떤 데이터와 코드로 만들어졌는지 추적하는 **데이터 리니지**를 매우 쉽게 확보할 수 있어, 모델의 재현성을 보장하고 문제 발생 시 원인을 빠르게 파악하는 데 큰 도움이 됩니다.
- **Q. 세 가지 도구 모두 Python 코드로 파이프라인을 정의하는데, 개발자 경험(DX) 측면에서 가장 큰 차이점은 무엇이라고 생각하시나요?**
  - **A.** 가장 큰 차이는 \*\*'파이썬 친화성'\*\*과 \*\*'로컬 테스트의 용이성'\*\*이라고 생각합니다. Prefect는 `@task`, `@flow` 데코레이터만 붙이면 기존 파이썬 코드가 바로 워크플로우가 되므로 가장 파이썬 친화적이고 학습 곡선이 낮습니다. Dagster 또한 로컬에서 파이프라인 전체를 쉽게 실행하고 테스트할 수 있는 환경을 제공하여 개발 경험이 우수합니다. 반면, Airflow는 DAG 정의를 위한 고유의 개념과 보일러플레이트 코드가 많고, 로컬에서 프로덕션 환경과 동일하게 테스트하기가 상대적으로 번거로워 개발자 경험 측면에서는 다른 두 도구에 비해 아쉬운 점이 있습니다.

______________________________________________________________________

## 5. 더 읽어보기 (Further Reading)

- [Airflow Official Documentation](https://airflow.apache.org/docs/)
- [Dagster Official Documentation](https://docs.dagster.io/)
- [Prefect Official Documentation](https://docs.prefect.io/)
- [The State of Data Engineering 2025 (Blog Post)](https://www.getcensus.com/blog/the-state-of-data-engineering-2025)
