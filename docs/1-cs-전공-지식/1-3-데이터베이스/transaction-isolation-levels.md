---
title: "트랜잭션 격리 수준 & 잠금"
date: "2025-07-02"
tags: ["데이터베이스", "CS", "트랜잭션", "잠금", "격리수준"]
difficulty: "hard"
---

# 트랜잭션 격리 수준 & 잠금

## 1. 핵심 개념 (Core Concept)

**트랜잭션 격리 수준(Transaction Isolation Level)**은 여러 트랜잭션이 동시에 실행될 때, 각 트랜잭션이 다른 트랜잭션의 작업에 얼마나 노출될지를 결정하는 기준입니다. 격리 수준은 데이터의 **일관성**과 **동시성** 사이의 트레이드오프 관계를 가지며, 이를 구현하는 핵심 기술이 바로 **잠금(Lock)**입니다. 잠금은 여러 트랜잭션이 동일한 데이터에 동시에 접근하는 것을 제어하여 데이터의 무결성을 유지하는 메커니즘입니다.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 트랜잭션 격리 수준 (Transaction Isolation Levels)

ANSI/ISO SQL 표준에서는 4가지 격리 수준을 정의하며, 수준이 높아질수록 동시성은 떨어지지만 데이터 일관성은 높아집니다.

#### 동시성 문제점
*   **Dirty Read**: 한 트랜잭션이 아직 **커밋되지 않은** 다른 트랜잭션의 수정 사항을 읽는 현상.
*   **Non-Repeatable Read (반복 불가능 읽기)**: 한 트랜잭션 내에서 같은 행을 두 번 이상 조회했을 때, 그 값이 다르게 나타나는 현상. (다른 트랜잭션이 중간에 **수정(UPDATE)** 후 커밋)
*   **Phantom Read (유령 읽기)**: 한 트랜잭션이 일정 범위의 데이터를 두 번 이상 읽을 때, 첫 번째 쿼리에서는 없었던 유령 같은 행이 두 번째 쿼리에서 나타나는 현상. (다른 트랜잭션이 **삽입(INSERT)** 후 커밋)

#### 격리 수준별 특징

| 격리 수준 | Dirty Read | Non-Repeatable Read | Phantom Read |
| :--- | :---: | :---: | :---: |
| **Read Uncommitted** | **발생** | **발생** | **발생** |
| **Read Committed** | 미발생 | **발생** | **발생** |
| **Repeatable Read** | 미발생 | 미발생 | **발생** |
| **Serializable** | 미발생 | 미발생 | 미발생 |

1.  **Read Uncommitted (레벨 0)**
    *   **특징**: 가장 낮은 격리 수준. 커밋되지 않은 데이터를 다른 트랜잭션이 읽는 것을 허용합니다.
    *   **문제점**: 모든 동시성 문제가 발생할 수 있어 데이터 정합성에 심각한 문제가 생길 수 있으므로 거의 사용되지 않습니다.

2.  **Read Committed (레벨 1)**
    *   **특징**: **커밋이 완료된** 트랜잭션의 변경사항만 다른 트랜잭션에서 조회할 수 있습니다. 대부분의 상용 데이터베이스에서 **기본값**으로 채택합니다.
    *   **문제점**: Dirty Read는 방지되지만, Non-Repeatable Read와 Phantom Read는 여전히 발생할 수 있습니다.

3.  **Repeatable Read (레벨 2)**
    *   **특징**: 트랜잭션이 시작되기 전에 커밋된 내용에 대해서만 조회가 가능하도록 보장합니다. 즉, 한 트랜잭션 내에서는 일관된 데이터를 보게 됩니다.
    *   **문제점**: Non-Repeatable Read는 방지되지만, Phantom Read는 여전히 발생할 수 있습니다. (MySQL의 InnoDB 엔진은 Gap Lock을 통해 Phantom Read를 방지합니다.)

4.  **Serializable (레벨 3)**
    *   **특징**: 가장 엄격한 격리 수준. 트랜잭션을 순차적으로 실행시켜 완벽한 데이터 일관성을 보장합니다.
    *   **문제점**: 읽기 작업에도 공유 잠금을 설정하므로 동시성이 가장 낮아 성능이 떨어지고, 교착 상태(Deadlock)가 발생할 확률이 높습니다.

### 2.2 잠금 (Lock)

잠금은 여러 트랜잭션이 동일한 자원에 대한 접근을 제어하는 메커니즘입니다.

#### 잠금의 종류
*   **공유 잠금 (Shared Lock, Read Lock)**:
    *   데이터를 **읽을 때** 사용하는 잠금입니다.
    *   여러 트랜잭션이 동시에 공유 잠금을 설정할 수 있습니다.
    *   공유 잠금이 설정된 데이터에는 다른 트랜잭션이 배타적 잠금을 설정할 수 없습니다. (읽기는 가능, 쓰기는 불가능)
*   **배타적 잠금 (Exclusive Lock, Write Lock)**:
    *   데이터를 **변경(INSERT, UPDATE, DELETE)할 때** 사용하는 잠금입니다.
    *   배타적 잠금이 설정된 데이터에는 다른 어떤 트랜잭션도 공유 잠금이나 배타적 잠금을 설정할 수 없습니다. (읽기/쓰기 모두 불가능)

#### 잠금의 수준 (Lock Granularity)
잠금의 범위가 넓을수록(테이블 락) 동시성은 떨어지지만 잠금 관리는 용이하고, 범위가 좁을수록(행 락) 동시성은 높아지지만 잠금 관리가 복잡해지고 오버헤드가 증가할 수 있습니다.
*   **행 수준 잠금 (Row-level Lock)**: 가장 일반적으로 사용되는 잠금 방식.
*   **테이블 수준 잠금 (Table-level Lock)**: 테이블 전체에 잠금을 설정.

---

## 3. 예시 (Example)

### Non-Repeatable Read 시나리오

쇼핑몰에서 상품의 재고를 확인하는 상황을 가정해봅시다. (격리 수준: Read Committed)

1.  **트랜잭션 A**: 사용자가 상품 A의 재고(10개)를 확인합니다. `SELECT stock FROM products WHERE id = 1;` -> 결과: 10
2.  **트랜잭션 B**: 동시에 다른 사용자가 상품 A를 3개 구매하여 재고가 7개로 변경되고, 이 트랜잭션이 **커밋**됩니다. `UPDATE products SET stock = 7 WHERE id = 1; COMMIT;`
3.  **트랜잭션 A**: 사용자가 잠시 후 같은 상품의 재고를 다시 확인합니다. `SELECT stock FROM products WHERE id = 1;` -> 결과: 7
4.  **결과**: 트랜잭션 A 내에서 동일한 쿼리를 두 번 실행했지만, 다른 결과를 얻게 되는 **Non-Repeatable Read** 문제가 발생했습니다.

만약 격리 수준이 **Repeatable Read**였다면, 트랜잭션 A는 자신이 시작된 시점의 데이터 스냅샷을 계속 참조하므로, 두 번째 쿼리에서도 재고는 여전히 10개로 보입니다.

---

## 4. 예상 면접 질문 (Potential Interview Questions)

*   **Q. 트랜잭션의 격리 수준이 필요한 이유는 무엇인가요?**
    *   **A.** 데이터베이스는 여러 사용자가 동시에 접근하는 다중 사용자 환경이기 때문입니다. 만약 격리 수준이 없다면, 여러 트랜잭션이 동시에 실행될 때 서로의 작업에 영향을 주어 데이터가 오염되거나(Dirty Read), 일관성이 깨지는(Non-Repeatable Read, Phantom Read) 등의 심각한 문제가 발생할 수 있습니다. 격리 수준은 이러한 문제들을 방지하고 데이터의 무결성을 보장하기 위해 필요합니다.

*   **Q. `Read Committed`와 `Repeatable Read`의 차이점은 무엇인가요?**
    *   **A.** `Read Committed`는 다른 트랜잭션이 커밋한 변경 사항을 즉시 볼 수 있는 반면, `Repeatable Read`는 트랜잭션이 시작된 시점의 데이터만을 볼 수 있다는 차이가 있습니다. 이로 인해 `Read Committed`에서는 한 트랜잭션 내에서 같은 데이터를 두 번 읽었을 때 결과가 달라지는 `Non-Repeatable Read`가 발생할 수 있지만, `Repeatable Read`에서는 이 문제가 발생하지 않습니다.

*   **Q. 잠금(Lock)으로 인해 발생할 수 있는 문제점과 해결 방안은 무엇인가요?**
    *   **A.** 가장 대표적인 문제점은 **교착 상태(Deadlock)**입니다. 교착 상태는 두 개 이상의 트랜잭션이 각각 잠금을 설정한 상태에서, 서로 상대방의 잠금이 해제되기만을 기다리며 무한 대기 상태에 빠지는 현상입니다. 해결 방안으로는 트랜잭션들이 잠금을 획득하는 순서를 동일하게 하거나, 잠금 대기 시간을 설정(Lock Timeout)하여 일정 시간이 지나면 트랜잭션을 자동으로 롤백시키는 방법이 있습니다.

---

## 5. 더 읽어보기 (Further Reading)

*   [Transaction Isolation (Wikipedia)](https://en.wikipedia.org/wiki/Isolation_(database_systems))
*   [MySQL 8.0 Reference Manual - Transaction Isolation Levels](https://dev.mysql.com/doc/refman/8.0/en/innodb-transaction-isolation-levels.html)
*   [Lock (database) (Wikipedia)](https://en.wikipedia.org/wiki/Lock_(database))