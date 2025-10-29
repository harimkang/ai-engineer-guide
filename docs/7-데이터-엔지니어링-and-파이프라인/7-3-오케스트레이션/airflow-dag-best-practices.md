---
title: Airflow DAG 설계 모범 사례 (Best Practices)
date: '2025-07-07'
tags: [Data Engineering, Orchestration, Airflow, DAG, Best Practices]
difficulty: hard
---

# Airflow DAG 설계 모범 사례 (Best Practices)

## 1. 핵심 개념 (Core Concept)

Airflow DAG(Directed Acyclic Graph)를 효과적으로 설계하는 것은 안정적이고, 유지보수 가능하며, 확장성 있는 데이터 파이프라인을 구축하는 데 매우 중요함. 모범 사례는 DAG를 원자적(atomic)이고 멱등성(idempotent)을 갖도록 구성하며, 코드의 재사용성을 높이고, 스케줄러의 부하를 줄이는 데 초점을 맞춤. 2025년 현재, TaskFlow API와 같은 현대적인 기능을 적극적으로 활용하는 것이 권장됨.

______________________________________________________________________

## 2. 상세 설명 (Detailed Explanation)

### 2.1 멱등성(Idempotency)과 원자성(Atomicity)

- **멱등성 (Idempotent Tasks)**: 모든 태스크는 여러 번 실행되어도 항상 동일한 결과를 내도록 설계해야 함. 이는 재시도(retry)나 백필(backfill) 시 데이터의 정합성을 보장하는 데 필수적임. 예를 들어, 특정 날짜의 데이터를 처리하는 태스크는 재실행 시 기존 데이터를 덮어쓰거나, 이미 처리된 경우 건너뛰도록 구현해야 함.

- **원자성 (Atomic Tasks)**: 각 태스크는 가능한 한 작고, 하나의 논리적인 작업만 수행해야 함. 예를 들어, "데이터 추출, 변환, 적재"를 하나의 태스크에서 모두 처리하는 대신, "추출", "변환", "적재"를 별개의 태스크로 분리해야 함. 이는 실패 시 문제의 원인을 파악하고, 특정 부분만 재실행하기 용이하게 만듦.

### 2.2 현대적인 DAG 작성법 (TaskFlow API & Providers)

- **TaskFlow API 활용**: 기존의 `PythonOperator` 대신 `@task` 데코레이터를 사용하는 TaskFlow API를 적극적으로 활용해야 함. 이를 통해 파이썬 함수를 직접 태스크로 변환하고, XComs를 통한 데이터 전달을 자동으로 처리하여 훨씬 간결하고 가독성 높은 DAG를 작성할 수 있음.

- **공식 제공자(Providers) 활용**: 외부 시스템(e.g., Snowflake, Databricks, BigQuery)과 연동할 때는 직접 코드를 작성하기보다 공식적으로 제공되는 Provider 패키지를 사용하는 것이 좋음. Provider는 잘 테스트된 Operator와 Hook을 제공하여 안정성과 표준을 보장함.

### 2.3 DAG 관리 및 성능 최적화

- **상수와 설정 중앙 관리**: DAG 내에서 반복적으로 사용되는 변수나 설정은 Airflow Variables나 `params`를 사용하여 중앙에서 관리하는 것이 좋음. 이는 코드 변경 없이 설정을 바꿀 수 있게 해줌.
- **동적 DAG 생성 최소화**: 동적 DAG는 유사한 구조의 파이프라인을 생성할 때 유용하지만, 너무 많은 DAG를 생성하면 스케줄러의 성능을 저하시킬 수 있음. 꼭 필요한 경우에만 사용하고, 생성되는 DAG의 수를 제어해야 함.
- **Top-level 코드 최소화**: DAG 파일의 최상위 레벨(Top-level)에서는 DAG 구조를 정의하는 것 외에 무거운 계산이나 외부 I/O 작업을 수행해서는 안 됨. 스케줄러는 이 코드를 주기적으로 파싱하므로, 부하가 큰 작업은 태스크 내부에서 실행되어야 함.

______________________________________________________________________

## 3. 예시 (Example)

### 모범 사례가 적용된 DAG 예시 (TaskFlow API 사용)

```python
from __future__ import annotations

import pendulum

from airflow.decorators import dag, task
from airflow.providers.postgres.hooks.postgres import PostgresHook

@dag(
    dag_id="etl_best_practices_dag",
    start_date=pendulum.datetime(2023, 1, 1, tz="UTC"),
    schedule="@daily",
    catchup=False,
    doc_md="""A best-practice compliant ETL DAG.""",
    params={"table_name": "my_table"}, # 파라미터 중앙 관리
)
def etl_pipeline():
    """An example ETL pipeline demonstrating Airflow best practices."""

    @task
    def extract_data(table_name: str) -> list[dict]:
        """Extracts data from a PostgreSQL table."""
        pg_hook = PostgresHook(postgres_conn_id="my_postgres_conn")
        conn = pg_hook.get_conn()
        cursor = conn.cursor()
        cursor.execute(f"SELECT * FROM {table_name};")
        # ... (멱등성을 위해 특정 날짜 데이터만 추출)
        return cursor.fetchall()

    @task
    def transform_data(data: list[dict]) -> list[dict]:
        """Performs a simple transformation."""
        # ... (원자적인 변환 로직)
        return [{**row, "processed": True} for row in data]

    @task
    def load_data(transformed_data: list[dict], table_name: str):
        """Loads transformed data into a target table."""
        # ... (원자적이고 멱등성 있는 적재 로직)
        print(f"Loaded {len(transformed_data)} rows into {table_name}_target.")

    # TaskFlow API를 통한 데이터 흐름 정의
    raw_data = extract_data(table_name="{{ params.table_name }}")
    processed_data = transform_data(raw_data)
    load_data(processed_data, table_name="{{ params.table_name }}")

etl_pipeline()
```

______________________________________________________________________

## 4. 예상 면접 질문 (Potential Interview Questions)

- **Q. Airflow 태스크를 멱등성(idempotent) 있게 만들어야 하는 이유는 무엇이며, 구체적인 구현 방법을 예시로 들어 설명해주세요.**
  - **A.** 멱등성은 태스크가 예기치 않게 재실행되더라도 데이터 파이프라인의 최종 결과가 항상 동일하도록 보장하기 위해 필수적입니다. 예를 들어, 특정 날짜의 데이터를 처리하여 요약 테이블에 쓰는 태스크가 있다고 가정해 보겠습니다. 이 태스크가 멱등성이 없다면, 재실행될 때마다 데이터가 중복으로 쌓여 잘못된 요약 결과를 만들 것입니다. 멱등성을 구현하기 위해서는, 태스크 시작 시 대상 테이블에서 해당 날짜의 데이터를 먼저 삭제(`DELETE`)한 후, 새로운 데이터를 삽입(`INSERT`)하는 `DELETE-INSERT` 패턴을 사용할 수 있습니다. 이렇게 하면 몇 번을 실행하든 항상 해당 날짜의 정확한 데이터만 남게 됩니다.
- **Q. Airflow의 TaskFlow API가 기존의 Operator 방식에 비해 갖는 장점은 무엇인가요?**
  - **A.** TaskFlow API의 가장 큰 장점은 **가독성**과 **단순성**입니다. 기존 방식에서는 `PythonOperator`를 정의하고, 태스크 간 데이터 전달을 위해 XComs를 수동으로 `push`, `pull` 해야 해서 코드가 장황해졌습니다. 하지만 TaskFlow API는 `@task` 데코레이터 하나로 일반적인 파이썬 함수를 바로 태스크로 만들고, 함수 간의 반환 값을 Airflow가 알아서 XComs를 통해 전달해줍니다. 결과적으로 순수 파이썬 코드를 작성하는 것처럼 자연스럽게 DAG를 개발할 수 있어 생산성과 유지보수성이 크게 향상됩니다.
- **Q. DAG 파일의 Top-level에 무거운 로직을 두면 안 되는 이유는 무엇인가요?**
  - **A.** Airflow 스케줄러는 DAG 파일을 주기적으로(기본값 5분) 파싱하여 DAG의 구조를 파악하고 태스크를 스케줄링합니다. 만약 DAG 파일의 최상위 코드에 데이터베이스에 연결하거나 대용량 파일을 읽는 등의 무거운 로직이 있다면, 이 코드는 스케줄링 주기마다 계속해서 실행됩니다. 이는 스케줄러에 심각한 부하를 주어 전체 Airflow 시스템의 성능을 저하시키고, 다른 DAG들의 스케줄링을 지연시키는 원인이 될 수 있습니다. 따라서 이러한 작업들은 반드시 `@task` 데코레이터가 붙은 함수 내부에서 실행되어야 합니다.

______________________________________________________________________

## 5. 더 읽어보기 (Further Reading)

- [Airflow Official Documentation: Best Practices](https://airflow.apache.org/docs/apache-airflow/stable/best-practices.html)
- [Astronomer: Best Practices for Writing Airflow DAGs](https://www.astronomer.io/guides/dag-best-practices/)
- [Airflow TaskFlow API Tutorial](https://airflow.apache.org/docs/apache-airflow/stable/tutorial/taskflow.html)
