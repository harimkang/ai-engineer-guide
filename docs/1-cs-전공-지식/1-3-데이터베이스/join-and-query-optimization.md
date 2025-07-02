---
title: "조인 & 쿼리 최적화 (EXPLAIN 플랜)"
date: "2025-07-02"
tags: ["데이터베이스", "CS", "조인", "쿼리최적화", "SQL"]
difficulty: "hard"
---

# 조인 & 쿼리 최적화 (EXPLAIN 플랜)

## 1. 핵심 개념 (Core Concept)

**조인(JOIN)**은 관계형 데이터베이스에서 두 개 이상의 테이블을 연결하여 데이터를 검색하는 핵심적인 방법입니다. **쿼리 최적화(Query Optimization)**는 작성된 SQL 쿼리를 가장 효율적인 방식으로 실행하기 위해 데이터베이스 관리 시스템(DBMS) 내부의 **옵티마이저(Optimizer)**가 수행하는 과정입니다. 개발자는 **실행 계획(Execution Plan)**을 분석하여 옵티마이저가 더 나은 선택을 하도록 쿼리를 개선할 수 있으며, 이때 `EXPLAIN` 명령어가 사용됩니다.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 조인 (JOIN)의 종류와 동작 원리

조인은 여러 테이블에 흩어져 있는 정보를 모아 하나의 결과 집합으로 만드는 데 사용됩니다.

| 조인 종류 | 설명 |
| :--- | :--- |
| **INNER JOIN** | 두 테이블에 공통된 값이 있는 행만 반환합니다. |
| **LEFT OUTER JOIN** | 왼쪽 테이블의 모든 행을 반환하고, 오른쪽 테이블은 조인 조건에 맞는 행만 반환 (없으면 NULL). |
| **RIGHT OUTER JOIN** | 오른쪽 테이블의 모든 행을 반환하고, 왼쪽 테이블은 조인 조건에 맞는 행만 반환 (없으면 NULL). |
| **FULL OUTER JOIN** | 양쪽 테이블의 모든 행을 반환. 조인 조건이 맞지 않으면 해당 부분은 NULL. |
| **CROSS JOIN** | 두 테이블의 모든 행을 조합하여 가능한 모든 경우의 수(Cartesian Product)를 반환. |
| **SELF JOIN** | 하나의 테이블을 자기 자신과 조인. 계층 구조 데이터 표현에 유용. |

#### 조인 알고리즘
데이터베이스 옵티마이저는 테이블의 크기, 인덱스 유무 등을 고려하여 가장 효율적인 조인 알고리즘을 선택합니다.
*   **Nested Loop Join (NLJ)**: 하나의 테이블(Outer Table)에서 행을 하나씩 읽고, 다른 테이블(Inner Table)을 반복적으로 탐색하며 조인 조건을 확인합니다. Outer Table의 크기가 작고 Inner Table에 인덱스가 있을 때 효율적입니다.
*   **Sort Merge Join (SMJ)**: 두 테이블을 조인 컬럼 기준으로 각각 정렬한 뒤, 정렬된 결과를 병합하면서 조인합니다. 대용량 데이터 조인에 유리합니다.
*   **Hash Join**: 하나의 테이블로 해시 테이블을 만들고, 다른 테이블을 스캔하며 해시 값을 비교하여 조인합니다. 동등(=) 조인 조건에서 매우 효율적입니다.

### 2.2 쿼리 최적화 (Query Optimization)

쿼리 최적화의 목표는 **"어떻게 하면 최소의 비용(I/O, CPU)으로 원하는 결과를 얻을 것인가?"** 입니다.

#### 주요 최적화 원칙
1.  **불필요한 데이터 접근 최소화**:
    *   `SELECT *` 대신 필요한 컬럼만 명시적으로 지정합니다.
    *   `WHERE` 절을 사용하여 조회할 데이터의 범위를 최대한 좁힙니다.
2.  **인덱스(Index) 활용 극대화**:
    *   `WHERE` 절의 조건 컬럼은 인덱스를 효과적으로 사용할 수 있도록 작성해야 합니다.
    *   **SARGable 쿼리**: `WHERE indexed_col = 'value'` 처럼 인덱스를 사용할 수 있는 형태.
    *   **Non-SARGable 쿼리**: `WHERE SUBSTRING(indexed_col, 1, 3) = 'val'` 처럼 인덱스 컬럼에 함수를 사용하면 인덱스를 활용할 수 없습니다.
3.  **조인 순서 최적화**:
    *   일반적으로 데이터 크기가 작은 테이블부터 조인하여 중간 결과 집합의 크기를 최소화하는 것이 유리합니다.
4.  **연산자 사용 주의**:
    *   `LIKE '%text'` 처럼 와일드카드가 앞에 오면 인덱스를 사용할 수 없습니다.
    *   `DISTINCT`나 `UNION`은 중복 제거를 위한 정렬 과정 때문에 비용이 클 수 있습니다. `UNION ALL`을 우선적으로 고려합니다.

### 2.3 실행 계획 (EXPLAIN) 분석

`EXPLAIN`은 작성한 쿼리가 데이터베이스에서 어떻게 실행될 것인지를 보여주는 '실행 계획'을 확인하는 명령어입니다. 이를 통해 쿼리의 비효율적인 부분을 찾아내고 최적화할 수 있습니다.

#### `EXPLAIN` 결과의 핵심 컬럼
| 컬럼 | 설명 | **분석 포인트** |
| :--- | :--- | :--- |
| **`type`** | **(가장 중요)** 테이블 접근 방식. | `ALL`(Full Scan)이나 `index`(Full Index Scan)가 나타나면 인덱스 사용 검토. `const`, `eq_ref`, `ref`, `range` 순으로 효율적. |
| **`key`** | 실제로 사용된 인덱스. | `NULL`이라면 인덱스를 사용하지 못했다는 의미. `possible_keys`와 비교하여 옵티마이저의 선택을 분석. |
| **`rows`** | 쿼리 수행을 위해 스캔한 대략적인 행의 수. | 이 값이 예상보다 너무 크다면 쿼리가 비효율적일 가능성이 높음. |
| **`Extra`** | 추가적인 중요 정보. | `Using filesort` (비효율적 정렬 발생), `Using temporary` (임시 테이블 사용)는 최적화가 필요한 주요 신호. |

---

## 3. 예시 (Example)

### 쿼리 최적화 전/후

**최적화 전 (Non-SARGable)**: `users` 테이블의 `signup_date`는 `DATETIME` 타입이고 인덱스가 걸려있다고 가정.
```sql
EXPLAIN SELECT user_id, user_name 
FROM users 
WHERE YEAR(signup_date) = 2024;
```
*   **예상 실행 계획**: `type`이 `ALL`로 표시될 가능성이 높습니다. `YEAR()` 함수 때문에 `signup_date` 인덱스를 사용할 수 없어 전체 테이블을 스캔합니다.

**최적화 후 (SARGable)**:
```sql
EXPLAIN SELECT user_id, user_name 
FROM users 
WHERE signup_date >= '2024-01-01 00:00:00' AND signup_date < '2025-01-01 00:00:00';
```
*   **예상 실행 계획**: `type`이 `range`로 표시될 가능성이 높습니다. 인덱스를 사용하여 특정 범위만 효율적으로 스캔합니다. `rows` 값도 크게 줄어듭니다.

---

## 4. 예상 면접 질문 (Potential Interview Questions)

*   **Q. `INNER JOIN`과 `LEFT JOIN`의 차이점은 무엇인가요?**
    *   **A.** `INNER JOIN`은 두 테이블에 공통적으로 존재하는 데이터, 즉 조인 조건을 만족하는 행만 반환합니다. 반면, `LEFT JOIN`은 왼쪽 테이블의 모든 행을 기준으로, 오른쪽 테이블에서는 조인 조건을 만족하는 데이터만 가져오고, 만약 일치하는 데이터가 없으면 해당 컬럼들을 `NULL`로 채워서 반환합니다.

*   **Q. 인덱스가 있음에도 불구하고 쿼리가 느리게 동작할 수 있는 이유는 무엇일까요?**
    *   **A.** 여러 이유가 있습니다. 첫째, `WHERE` 절에서 인덱스가 걸린 컬럼을 함수로 가공하거나 변형하여 **인덱스를 사용하지 못하는 경우**가 있습니다. 둘째, 데이터의 분포도에 따라 인덱스를 사용하는 것보다 **전체 테이블을 스캔하는 것이 더 빠르다고 옵티마이저가 판단**하는 경우도 있습니다. 셋째, 인덱스는 있지만 조회하려는 데이터가 테이블의 대부분을 차지하는 경우에도 인덱스의 효율이 떨어질 수 있습니다.

*   **Q. `EXPLAIN`을 통해 쿼리를 튜닝할 때 가장 먼저 확인해야 할 컬럼은 무엇이며, 그 이유는 무엇인가요?**
    *   **A.** 가장 먼저 `type` 컬럼을 확인해야 합니다. `type`은 데이터베이스가 테이블에 어떻게 접근했는지를 보여주는 가장 핵심적인 지표이기 때문입니다. 만약 `type`이 `ALL` (Full Table Scan)로 나온다면, 쿼리가 인덱스를 전혀 사용하지 못하고 테이블 전체를 읽고 있다는 의미이므로 인덱스 생성이나 쿼리 구조 변경을 통해 최적화할 여지가 매우 큽니다. 그 다음으로 `key`, `rows`, `Extra` 컬럼을 종합적으로 분석하여 튜닝 방향을 결정합니다.

---

## 5. 더 읽어보기 (Further Reading)

*   [SQL Joins (W3Schools)](https://www.w3schools.com/sql/sql_join.asp)
*   [MySQL 8.0 Reference Manual - EXPLAIN Statement](https://dev.mysql.com/doc/refman/8.0/en/explain.html)
*   [Use The Index, Luke (A Guide to Database Performance for Developers)](https://use-the-index-luke.com/)