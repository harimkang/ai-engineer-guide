---
title: "차세대 오케스트레이터: Dagster vs. Prefect 비교"
date: "2025-07-07"
tags: ["Data Engineering", "Orchestration", "Dagster", "Prefect"]
difficulty: "hard"
---

# 차세대 오케스트레이터: Dagster vs. Prefect 비교

## 1. 핵심 개념 (Core Concept)

Dagster와 Prefect는 Airflow의 대안으로 주목받는 현대적인 데이터 오케스트레이션 도구임. **Dagster**는 파이프라인이 생성하는 **데이터 자산(Data Assets)**을 중심으로, 데이터의 계보(lineage)와 품질을 보장하는 데 초점을 맞춘 프레임워크임. 반면, **Prefect**는 파이썬 코드의 유연성을 극대화하여 **동적이고 복잡한 워크플로우**를 손쉽게 만들고, 실패를 효과적으로 처리하는 데 강점을 가진 프레임워크임.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 핵심 철학의 차이

*   **Dagster: "데이터 자산을 정의하고 관찰하라"**
    *   Dagster의 모든 것은 **소프트웨어 정의 자산(Software-Defined Assets, SDA)**에서 시작함. 파이프라인의 목적은 코드를 실행하는 것이 아니라, 신뢰할 수 있는 데이터 자산(예: 데이터베이스 테이블, 파일, ML 모델)을 만드는 것이라고 봄.
    *   `@asset` 데코레이터를 사용하여 함수가 어떤 데이터 자산을 생성하는지 명시적으로 선언. 이를 통해 데이터 계보(lineage)를 자동으로 추적하고, 자산의 최신 상태를 시각화하며, 데이터 품질 테스트를 통합 관리할 수 있음.
    *   개발 단계부터 테스트와 데이터 품질을 강조하여, 파이프라인의 신뢰성과 관찰 가능성(Observability)을 높이는 데 집중.

*   **Prefect: "어떤 파이썬 코드든 워크플로우로 만들어라"**
    *   Prefect는 파이썬의 유연성을 최대한 살리는 데 중점을 둠. `@flow`와 `@task` 데코레이터만으로 기존 파이썬 코드를 거의 변경 없이 강력한 워크플로우로 전환할 수 있음.
    *   실행 중에 태스크가 동적으로 생성되거나, 조건에 따라 흐름이 바뀌는 복잡한 로직을 매우 쉽게 구현할 수 있음.
    *   실패는 자연스러운 것이라는 철학 아래, 정교한 재시도(retry) 로직, 타임아웃, 알림 등 실패를 다루고 복구하는 데 매우 강력한 기능을 제공.

### 2.2 비교 분석 (2025년 기준)

| 구분 | Dagster | Prefect |
| :--- | :--- | :--- |
| **패러다임** | 데이터 자산(Data Asset) 중심 | 워크플로우(Flow) 중심 |
| **핵심 강점** | 데이터 계보(Lineage), 테스트 용이성, 관찰 가능성 | 동적 파이프라인, 유연성, 강력한 오류 처리 |
| **개발 경험** | 데이터 중심의 명시적 정의, 로컬 테스트 환경 우수 | 파이썬 친화적, 최소한의 코드로 워크플로우 생성 | 
| **UI/대시보드** | 자산 그래프, 실행 로그, 자산의 구체화(materialization) 이력 등 데이터 중심 뷰 제공 | 실시간 실행 상태, 스케줄링, 이벤트 로그 등 워크플로우 실행 중심 뷰 제공 |
| **적합한 사용 사례** | - 데이터 품질과 신뢰성이 최우선인 경우<br>- 데이터 계보 추적이 중요한 복잡한 데이터 플랫폼<br>- dbt, Fivetran 등 다른 도구와 통합된 자산 관리 | - ML 모델 학습/추론 파이프라인<br>- 실시간 데이터 처리 등 예측 불가능한 워크플로우<br>- 빠른 프로토타이핑과 유연한 실행이 중요한 경우 |

---

## 3. 예시 (Example)

### 패러다임 차이를 보여주는 예시

**상황**: 원본 데이터를 받아, 정제한 후, ML 모델을 학습시키는 파이프라인

**Dagster의 접근 방식:**
```python
from dagster import asset

@asset
def raw_data():
    # 원본 데이터 로드
    return load_raw_data()

@asset
def cleaned_data(raw_data):
    # 데이터 정제
    return clean(raw_data)

@asset
def trained_model(cleaned_data):
    # 모델 학습
    return train_model(cleaned_data)
```
*`raw_data`, `cleaned_data`, `trained_model`이라는 3개의 **데이터 자산**을 정의. Dagster는 이 자산들 간의 의존성을 파악하여 파이프라인을 실행하고, 각 자산의 상태를 추적함.*

**Prefect의 접근 방식:**
```python
from prefect import flow, task

@task
def load_raw_data_task():
    return load_raw_data()

@task
def clean_data_task(data):
    return clean(data)

@task
def train_model_task(data):
    return train_model(data)

@flow
def ml_pipeline():
    raw = load_raw_data_task.submit()
    cleaned = clean_data_task.submit(raw)
    model = train_model_task.submit(cleaned)
    return model.result()
```
*데이터를 처리하는 3개의 **태스크**와 이들의 실행 순서를 정의한 **워크플로우**를 정의. 데이터 자체가 아닌, 코드의 실행 흐름에 집중함.*

---

## 4. 예상 면접 질문 (Potential Interview Questions)

*   **Q. Dagster와 Prefect 중 어떤 도구를 선택하시겠습니까? 선택의 기준은 무엇인가요?**
    *   **A.** 선택은 프로젝트의 핵심 요구사항에 따라 달라질 것입니다. 만약 프로젝트의 목표가 **신뢰할 수 있는 데이터 플랫폼을 구축**하는 것이고, 데이터의 출처와 품질, 최신 상태를 명확하게 추적하는 것이 중요하다면 **Dagster**를 선택하겠습니다. 데이터 자산 중심 접근법은 데이터 거버넌스와 관찰 가능성 확보에 매우 유리하기 때문입니다. 반면, 프로젝트가 **복잡한 비즈니스 로직이나 ML 모델 실험**처럼 예측 불가능하고 동적인 워크플로우를 자동화하는 것이 목표라면 **Prefect**를 선택하겠습니다. 파이썬 친화적인 API와 강력한 동적 실행 기능은 복잡한 로직을 빠르고 유연하게 구현하는 데 더 적합합니다.
*   **Q. Dagster의 '소프트웨어 정의 자산(Software-Defined Assets)' 개념이 기존의 태스크 중심 오케스트레이션(e.g., Airflow)과 비교하여 어떤 장점을 가지나요?**
    *   **A.** 가장 큰 장점은 **관점의 전환**입니다. 태스크 중심 오케스트레이션은 "코드가 성공적으로 실행되었는가?"에만 관심이 있지만, 자산 중심 오케스트레이션은 "우리가 원하는 데이터가 성공적으로, 그리고 올바르게 만들어졌는가?"에 관심을 둡니다. 이로 인해 (1) **데이터 계보(Lineage)가 자동으로 생성**되어 데이터의 흐름을 파악하기 쉽고, (2) 각 자산에 대한 **데이터 품질 테스트를 통합**하기 용이하며, (3) 자산이 최신 상태인지 아닌지를 한눈에 파악하여 **파이프라인을 효율적으로 실행** (예: 최신 상태인 자산은 건너뛰기)할 수 있는 등 데이터의 신뢰성과 관련된 많은 문제를 근본적으로 해결해줍니다.
*   **Q. Prefect가 동적 워크플로우(Dynamic Workflows)에 강점을 가진다는 것은 구체적으로 어떤 의미인가요?**
    *   **A.** 일반적인 워크플로우는 실행 전에 모든 구조가 정적으로 정의되어야 합니다. 하지만 Prefect는 워크플로우 실행 중에 얻은 결과에 따라 다음에 실행할 태스크의 수나 종류를 동적으로 결정할 수 있습니다. 예를 들어, 한 태스크에서 처리해야 할 파일 목록을 반환하면, 그 파일의 개수만큼 다음 태스크를 동적으로 생성하여 병렬로 처리할 수 있습니다. 이러한 기능은 데이터의 내용이나 외부 상황에 따라 실행 계획이 바뀌어야 하는 복잡한 시나리오를 매우 간결하고 유연하게 구현할 수 있게 해줍니다.

---

## 5. 더 읽어보기 (Further Reading)

*   [Dagster Official Documentation: Key Concepts](https://docs.dagster.io/concepts)
*   [Prefect Official Documentation: Core Concepts](https://docs.prefect.io/latest/core-concepts/)
*   [Dagster vs. Prefect vs. Airflow: A Guide to Data Orchestrators (Blog Post)](https://www.getcensus.com/blog/dagster-vs-prefect-vs-airflow)