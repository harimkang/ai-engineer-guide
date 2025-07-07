---
title: "데이터 프로파일링 및 테스트"
date: "2025-07-07"
tags: ["Data Engineering", "Data Quality", "Data Profiling", "Data Testing", "Great Expectations", "dbt"]
difficulty: "medium"
---

# 데이터 프로파일링 및 테스트

## 1. 핵심 개념 (Core Concept)

**데이터 프로파일링**은 데이터의 구조, 내용, 품질을 이해하기 위해 데이터를 검사하고 통계적 정보를 요약하는 과정임. **데이터 테스트**는 이러한 프로파일링 결과를 바탕으로, 데이터가 사전에 정의된 규칙과 기대를 충족하는지 지속적으로 검증하는 자동화된 프로세스임. 이 두 가지는 "Garbage In, Garbage Out" 문제를 방지하고 신뢰할 수 있는 데이터 파이프라인을 구축하기 위한 핵심 활동임.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 데이터 프로파일링 (Data Profiling)

데이터를 본격적으로 사용하기 전, 데이터의 "신분증"을 확인하는 과정과 같음. 주로 다음과 같은 항목들을 분석함.

*   **기술적 프로파일링**:
    *   **데이터 타입 및 형식**: 각 컬럼의 데이터 타입(정수, 문자열, 날짜 등)과 형식을 확인. (예: `YYYY-MM-DD`)
    *   **값의 범위 및 분포**: 최솟값, 최댓값, 평균, 중앙값, 표준편차 등 통계 정보 확인.
    *   **고유값(Uniqueness)**: 각 컬럼의 고유한 값의 개수와 비율을 확인. (예: `user_id`는 100% 고유해야 함)
    *   **완전성(Completeness)**: Null 또는 빈 값의 개수와 비율을 확인.
*   **내용적 프로파일링**:
    *   **패턴 분석**: 정규표현식을 사용하여 전화번호, 이메일 주소 등의 형식이 올바른지 확인.
    *   **컬럼 간 관계**: 여러 컬럼 간의 의존성이나 상관관계를 분석.

### 2.2 데이터 테스트 (Data Testing)

데이터 파이프라인의 각 단계에서 데이터 품질을 보증하기 위한 자동화된 검증 절차. "데이터에 대한 단위 테스트"라고도 할 수 있음.

*   **주요 테스트 항목 (Expectations)**:
    *   `expect_column_values_to_not_be_null`: 특정 컬럼의 값이 Null이 아님을 기대.
    *   `expect_column_values_to_be_unique`: 특정 컬럼의 값이 고유함을 기대.
    *   `expect_column_values_to_be_in_set`: 특정 컬럼의 값이 주어진 집합 안에 포함됨을 기대. (예: `status` 컬럼은 `["completed", "pending", "failed"]` 중 하나여야 함)
    *   `expect_column_mean_to_be_between`: 특정 컬럼의 평균값이 주어진 범위 내에 있음을 기대.
    *   `expect_table_row_count_to_be_between`: 테이블의 전체 행 수가 예상 범위 내에 있음을 기대.

### 2.3 대표적인 도구

| 구분 | Great Expectations | dbt (data build tool) |
| :--- | :--- | :--- | 
| **핵심 기능** | 데이터 품질 검증 및 문서화에 특화된 Python 라이브러리. | 데이터 변환(Transformation) 워크플로우 내에서 테스트를 통합적으로 관리. | 
| **특징** | - 풍부하고 표현력 높은 기대(Expectation) 라이브러리 제공.<br>- 데이터 프로파일링을 통해 자동으로 기대 스위트(Expectation Suite) 생성.<br>- 데이터 품질 리포트인 "Data Docs" 자동 생성. | - SQL `SELECT` 문으로 간단하게 테스트 케이스 작성 가능.<br>- 데이터 변환(모델링)과 테스트를 하나의 워크플로우에서 관리.<br>- `dbt test` 명령어로 쉽게 테스트 실행. | 
| **사용 시나리오** | 파이프라인의 모든 단계(수집, 변환, 적재)에서 강력한 데이터 품질 검증이 필요할 때. | SQL 기반의 데이터 변환 작업이 중심이며, 변환된 데이터의 정합성을 보장하고 싶을 때. | 

---

## 3. 예시 (Example)

### 코드 예시 (Great Expectations)

```python
import great_expectations as gx

context = gx.get_context()

# 데이터 소스 연결 및 유효성 검사기(Validator) 생성
validator = context.sources.pandas_default.read_csv("my_data.csv")

# 기대(Expectation) 정의
validator.expect_column_values_to_not_be_null("user_id")
validator.expect_column_values_to_be_unique("user_id")
validator.expect_column_mean_to_be_between("age", 18, 65)
validator.expect_column_values_to_match_regex("phone_number", "^\\d{3}-\\d{4}-\\d{4}$")

# 기대 스위트(Expectation Suite) 저장
validator.save_expectation_suite(discard_failed_expectations=False)
```

### 코드 예시 (dbt)

`models/schema.yml` 파일에 테스트를 정의:
```yaml
models:
  - name: customers
    columns:
      - name: customer_id
        tests:
          - unique
          - not_null
      - name: number_of_orders
        tests:
          - dbt_expectations.expect_column_values_to_be_non_negative

  - name: orders
    columns:
      - name: status
        tests:
          - accepted_values:
              values: ['placed', 'shipped', 'completed', 'returned']
```
터미널에서 `dbt test` 명령어를 실행하여 위 테스트들을 검증.

---

## 4. 예상 면접 질문 (Potential Interview Questions)

*   **Q. 데이터 프로파일링과 데이터 테스팅의 관계는 무엇이며, 왜 둘 다 중요한가요?**
    *   **A.** 데이터 프로파일링은 데이터의 현재 상태를 이해하고 분석하는 **탐색적 과정**이고, 데이터 테스팅은 그 이해를 바탕으로 데이터가 특정 규칙을 만족하는지 지속적으로 검증하는 **자동화된 과정**입니다. 즉, 프로파일링을 통해 "우리 `age` 컬럼의 평균은 35이고, 최솟값은 20이구나"라는 사실을 알게 되면, 이를 바탕으로 "`age` 컬럼의 값은 항상 18 이상이어야 한다"는 테스트 케이스를 만들 수 있습니다. 둘 다 중요한 이유는, 프로파일링 없이는 어떤 테스트를 만들어야 할지 알 수 없고, 테스팅 없이는 데이터 품질이 시간이 지나도 계속 유지되는지 보장할 수 없기 때문입니다.
*   **Q. 데이터 파이프라인의 어느 단계에서 데이터 테스트를 적용하는 것이 가장 효과적일까요?**
    *   **A.** 여러 단계에서 적용하는 것이 가장 이상적입니다. 흔히 "Shift-left testing"이라고 하는데, 가능한 한 파이프라인의 **가장 왼쪽(소스)**에서부터 테스트를 시작하는 것이 좋습니다. 예를 들어, (1) **데이터 수집 직후**에 소스 데이터의 스키마나 기본 품질을 검증하고, (2) **데이터 변환(Transformation) 후**에 비즈니스 로직이 올바르게 적용되었는지 검증하며, (3) **데이터 적재(Load) 전**에 최종 목적지에 들어갈 데이터의 무결성을 검증하는 다단계 접근 방식이 효과적입니다. 문제를 조기에 발견할수록 수정 비용이 적게 들기 때문입니다.
*   **Q. Great Expectations와 dbt의 테스트 기능은 어떤 차이가 있으며, 어떤 기준으로 선택해야 할까요?**
    *   **A.** **Great Expectations**는 데이터 품질 그 자체에 초점을 맞춘 **독립적인 데이터 품질 프레임워크**입니다. 파이프라인의 어떤 단계에서도 사용할 수 있고, 풍부한 검증 규칙과 상세한 품질 리포트(Data Docs)가 강점입니다. 반면, **dbt 테스트**는 **데이터 변환 과정에 내장된** 기능입니다. SQL을 사용하여 데이터를 모델링하는 과정에서 변환된 결과물의 정합성을 테스트하는 데 매우 편리합니다. 따라서, 데이터 파이프라인 전반에 걸쳐 독립적이고 강력한 품질 게이트가 필요하다면 Great Expectations를, SQL 기반의 데이터 변환 워크플로우 내에서 간결하게 데이터 정합성을 검증하고 싶다면 dbt 테스트를 선택하는 것이 좋습니다. 두 도구를 함께 사용하는 경우도 많습니다.

---

## 5. 더 읽어보기 (Further Reading)

*   [Great Expectations Official Documentation](https://docs.greatexpectations.io/)
*   [dbt: About dbt tests](https://docs.getdbt.com/docs/build/tests)
*   [The Rise of Data Contracts (Blog Post)](https://www.getcensus.com/blog/the-rise-of-data-contracts)