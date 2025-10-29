---
title: 피처 스토어 개요 (Feast, Tecton)
date: '2025-07-07'
tags: [MLOps, Feature Store, Feast, Tecton]
difficulty: medium
---

# 피처 스토어 개요 (Feast, Tecton)

## 1. 핵심 개념 (Core Concept)

피처 스토어(Feature Store)는 머신러닝을 위한 피처(Feature)를 중앙에서 관리, 저장, 검색, 공유하는 시스템임. 데이터 과학자와 엔지니어가 피처를 일관된 방식으로 정의하고, 모델 학습(오프라인)과 실시간 추론(온라인) 환경 모두에서 동일한 피처를 사용할 수 있도록 하여 생산성과 모델 성능의 일관성을 높임. Feast와 Tecton은 대표적인 피처 스토어 솔루션임.

______________________________________________________________________

## 2. 상세 설명 (Detailed Explanation)

### 2.1 피처 스토어의 필요성

피처 스토어는 다음과 같은 문제를 해결하기 위해 등장했음:

- **학습-서빙 스큐 (Training-Serving Skew)**: 모델 학습 시 사용한 피처와 실제 서빙 시 사용한 피처가 달라 발생하는 성능 저하 문제.
- **피처 중복 개발**: 여러 팀에서 유사한 피처를 각자 개발하여 발생하는 비효율과 자원 낭비.
- **피처 공유 및 재사용의 어려움**: 잘 만들어진 피처를 다른 프로젝트에서 재사용하기 어려운 문제.
- **복잡한 데이터 파이프라인**: 배치 및 스트리밍 데이터를 처리하여 온라인/오프라인 스토어에 피처를 공급하는 파이프라인 관리의 복잡성.

### 2.2 Feast (Feature Store for ML)

Feast는 구글과 Gojek이 개발한 오픈소스 피처 스토어임. 기존 데이터 인프라(데이터 웨어하우스, 스트리밍 플랫폼 등) 위에 구축되어 피처를 정의하고 서빙하는 데 초점을 맞춤.

- **주요 특징**:
  - **선언적 피처 정의**: Python 코드로 피처를 명확하게 정의하고 버전 관리(Git) 가능.
  - **데이터 소스 유연성**: Snowflake, BigQuery, Redshift, Kafka 등 다양한 데이터 소스와 연동.
  - **Point-in-Time Correctness**: 특정 시점 기준으로 정확한 피처를 조회하여 데이터 누수(Data Leakage) 없이 학습 데이터셋을 생성.
  - **오픈소스**: 특정 벤더에 종속되지 않고 자유롭게 사용 및 수정 가능.

### 2.3 Tecton

Tecton은 Databricks 창시자들이 만든 상용 엔터프라이즈 피처 플랫폼임. 피처 서빙뿐만 아니라, 피처 계산 및 파이프라인 관리를 자동화하는 데 강점을 가짐.

- **주요 특징**:
  - **완전 관리형(Fully Managed)**: 피처 파이프라인의 오케스트레이션, 모니터링, 백필(Backfill) 등을 자동화하여 인프라 관리 부담을 줄여줌.
  - **엔터프라이즈급 기능**: 세분화된 접근 제어, 감사 로그, SLO(서비스 수준 목표) 등 기업 환경에 필요한 기능 제공.
  - **피처 변환 지원**: SQL, Python(PySpark, Snowpark) 등 다양한 방식으로 피처 변환 로직을 정의하고 실행.
  - **Databricks/Snowflake 네이티브**: Databricks 및 Snowflake 환경과 긴밀하게 통합되어 높은 성능을 제공.

### 2.4 Feast vs. Tecton 비교

| 구분            | Feast                                                                           | Tecton                                                                                            |
| :-------------- | :------------------------------------------------------------------------------ | :------------------------------------------------------------------------------------------------ |
| **라이선스**    | 오픈소스 (Apache 2.0)                                                           | 상용 (Commercial)                                                                                 |
| **핵심 기능**   | 피처 정의, 저장, 서빙                                                           | 피처 파이프라인 자동화, 변환, 모니터링, 서빙                                                      |
| **관리 주체**   | 사용자 (직접 구축 및 운영)                                                      | Tecton (완전 관리형 서비스)                                                                       |
| **주요 장점**   | 유연성, 확장성, 비용 효율성                                                     | 생산성, 안정성, 엔터프라이즈 기능                                                                 |
| **적합한 환경** | 기존 데이터 인프라를 활용하고 싶거나, 특정 기술 스택에 종속되고 싶지 않은 조직. | 피처 엔지니어링 및 파이프라인 관리 자동화에 대한 요구가 크고, 안정적인 운영이 중요한 대규모 조직. |

______________________________________________________________________

## 3. 예시 (Example)

### 코드 예시 (Feast)

```python
# 1. 데이터 소스 정의 (BigQuery)
from feast import BigQuerySource

driver_stats_source = BigQuerySource(
    table="my_project.my_dataset.driver_stats",
    timestamp_field="event_timestamp",
    created_timestamp_column="created",
)

# 2. 엔티티 및 피처 뷰 정의
from feast import Entity, FeatureView, Field
from feast.types import Float32, Int64

driver = Entity(name="driver_id", join_keys=["driver_id"])

driver_stats_fv = FeatureView(
    name="driver_hourly_stats",
    entities=[driver],
    ttl=timedelta(days=1),
    schema=[
        Field(name="conv_rate", dtype=Float32),
        Field(name="acc_rate", dtype=Float32),
    ],
    source=driver_stats_source,
)

# 3. 학습 데이터셋 생성
from feast import FeatureStore

store = FeatureStore(repo_path=".")

training_df = store.get_historical_features(
    entity_df=entity_df, # "driver_id", "event_timestamp" 컬럼 포함
    features=[
        "driver_hourly_stats:conv_rate",
        "driver_hourly_stats:acc_rate",
    ],
).to_df()

# 4. 온라인 피처 서빙
online_features = store.get_online_features(
    features=[
        "driver_hourly_stats:conv_rate",
        "driver_hourly_stats:acc_rate",
    ],
    entity_rows=[{"driver_id": 1001}],
).to_dict()
```

______________________________________________________________________

## 4. 예상 면접 질문 (Potential Interview Questions)

- **Q. 피처 스토어를 도입했을 때 얻을 수 있는 가장 큰 이점은 무엇이라고 생각하나요?**
  - **A.** 가장 큰 이점은 학습-서빙 스큐 문제를 해결하여 모델의 신뢰성을 높이는 것입니다. 피처 스토어는 동일한 피처 정의를 통해 모델 학습과 실시간 추론에서 일관된 데이터를 제공합니다. 이를 통해 오프라인에서 검증된 모델 성능이 온라인 환경에서도 그대로 재현될 가능성을 크게 높여줍니다. 또한, 피처의 재사용성을 높여 팀 간의 협업을 촉진하고 중복 개발을 방지하는 효과도 큽니다.
- **Q. Feast와 같은 오픈소스 피처 스토어와 Tecton과 같은 상용 솔루션 중 어떤 것을 선택하시겠습니까? 선택의 기준은 무엇인가요?**
  - **A.** 선택은 조직의 성숙도, 규모, 예산, 기술 스택에 따라 달라질 것입니다. 초기 단계의 스타트업이나 특정 클라우드에 종속되고 싶지 않은 조직이라면, 유연하고 비용 효율적인 Feast를 선택하여 기존 인프라 위에서 시작하는 것이 합리적일 수 있습니다. 반면, 데이터 팀의 규모가 크고 여러 프로젝트에서 안정적으로 피처를 관리해야 하며, 피처 파이프라인 운영에 드는 리소스를 최소화하고 싶다면, 완전 관리형 서비스와 엔터프라이즈급 기능을 제공하는 Tecton이 더 나은 선택일 수 있습니다.
- **Q. 피처 스토어에서 "Point-in-Time Correctness"가 왜 중요한가요?**
  - **A.** 데이터 누수(Data Leakage)를 방지하여 모델의 실제 성능을 정확하게 평가하기 위해 중요합니다. Point-in-Time Correctness는 학습 데이터셋을 만들 때, 예측 시점(timestamp) 이전에 발생한 피처 값만 사용하도록 보장합니다. 만약 미래의 정보를 담고 있는 피처를 사용하여 모델을 학습시키면, 오프라인 테스트에서는 성능이 비정상적으로 높게 나오지만 실제 서빙 환경에서는 사용할 수 없는 정보이므로 성능이 크게 저하됩니다. 피처 스토어는 이를 자동적으로 처리해줍니다.

______________________________________________________________________

## 5. 더 읽어보기 (Further Reading)

- [Feast Official Documentation](https://docs.feast.dev/)
- [Tecton AI Official Website](https://www.tecton.ai/)
- [Feature Store for MLOps (Tecton Blog)](https://www.tecton.ai/blog/what-is-a-feature-store/)
