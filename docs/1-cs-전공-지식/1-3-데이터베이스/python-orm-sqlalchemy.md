---
title: "Python ORM (SQLAlchemy) 사용 예시"
date: "2025-07-02"
tags: ["데이터베이스", "CS", "Python", "ORM", "SQLAlchemy"]
difficulty: "medium"
---

# Python ORM (SQLAlchemy) 사용 예시

## 1. 핵심 개념 (Core Concept)

**ORM(Object-Relational Mapping)**은 객체 지향 프로그래밍 언어를 사용하여 관계형 데이터베이스를 다루는 프로그래밍 기술입니다. 즉, 데이터베이스의 테이블을 파이썬의 **클래스(모델)**와 매핑(mapping)하고, 테이블의 행(row)을 **객체(인스턴스)**와 매핑하여, 개발자가 SQL 쿼리를 직접 작성하지 않고도 파이썬 코드로 데이터베이스를 조작할 수 있게 해주는 도구입니다. **SQLAlchemy**는 파이썬에서 가장 널리 사용되는 대표적인 ORM 라이브러리입니다.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 SQLAlchemy의 주요 구성 요소

SQLAlchemy는 크게 **Core**와 **ORM** 두 가지 구성 요소로 나뉩니다.

*   **Core**: SQL Expression Language를 제공하여, 파이썬 코드로 SQL 구문을 생성하고 데이터베이스와 상호작용할 수 있는 저수준 인터페이스입니다.
*   **ORM**: Core를 기반으로 하며, 사용자가 정의한 파이썬 클래스(모델)를 데이터베이스 테이블에 매핑하여 객체 지향적인 방식으로 데이터를 관리할 수 있게 해주는 고수준 인터페이스입니다.

```mermaid
graph TD;
    subgraph "Application Layer"
        A[Python Application]
    end
    subgraph "SQLAlchemy"
        ORM(ORM) --> Core(Core)
    end
    subgraph "Database Layer"
        DB[(Database)]
    end
    
    A --> ORM;
    Core --> DBAPI[DBAPI (psycopg2, mysql-connector)];
    DBAPI --> DB;
```

#### ORM의 핵심 개념
*   **Engine**: 데이터베이스와의 연결(Connection Pool)을 관리하는 중앙 역할을 합니다.
*   **Session**: ORM에서 데이터베이스와의 모든 상호작용을 처리하는 작업 공간(Workspace)입니다. 객체의 추가, 수정, 삭제 등의 작업을 Session을 통해 수행하고, 마지막에 커밋하여 데이터베이스에 반영합니다.
*   **Declarative Base**: 매핑할 모델 클래스들이 상속받는 기본 클래스입니다. 이 클래스를 상속받아 모델을 정의하면, SQLAlchemy가 해당 클래스를 데이터베이스 테이블과 자동으로 매핑합니다.
*   **Model**: 데이터베이스 테이블에 매핑되는 파이썬 클래스입니다. 클래스의 속성(attribute)은 테이블의 컬럼(column)에 매핑됩니다.

### 2.2 SQLAlchemy의 장단점

*   **장점**:
    *   **생산성 향상**: SQL 쿼리를 직접 작성하는 대신 파이썬 코드로 데이터베이스를 다룰 수 있어 개발 속도가 향상됩니다.
    *   **데이터베이스 종속성 감소**: SQLAlchemy가 각기 다른 DBMS의 SQL 문법 차이를 흡수해주므로, 데이터베이스 종류에 덜 의존적인 코드를 작성할 수 있습니다.
    *   **유지보수 용이성**: 객체 지향적인 코드는 가독성이 높고 재사용이 용이하여 유지보수에 유리합니다.
    *   **보안**: SQL 인젝션과 같은 보안 위협을 방어하는 데 도움이 됩니다.
*   **단점**:
    *   **학습 곡선**: ORM의 개념과 SQLAlchemy의 사용법을 별도로 학습해야 합니다.
    *   **복잡한 쿼리 작성의 어려움**: 복잡하고 세밀한 튜닝이 필요한 쿼리는 ORM으로 표현하기 어렵거나 성능 저하를 유발할 수 있습니다.
    *   **성능 저하 가능성**: ORM이 생성하는 쿼리가 직접 작성한 쿼리보다 비효율적일 수 있습니다.

---

## 3. 예시 (Example)

### SQLAlchemy를 이용한 CRUD 작업

`User` 모델을 정의하고 사용자를 추가, 조회, 수정, 삭제하는 간단한 예제 코드입니다.

```python
from sqlalchemy import create_engine, Column, Integer, String
from sqlalchemy.orm import sessionmaker, declarative_base

# 1. Engine 생성 (메모리 내 SQLite 데이터베이스 사용)
# echo=True 옵션은 실행되는 SQL 쿼리를 콘솔에 출력해줍니다.
engine = create_engine('sqlite:///:memory:', echo=True)

# 2. 매핑을 위한 Declarative Base 생성
Base = declarative_base()

# 3. User 모델 클래스 정의 (테이블 'users'에 매핑)
class User(Base):
    __tablename__ = 'users'

    id = Column(Integer, primary_key=True)
    name = Column(String(50))
    fullname = Column(String(50))
    nickname = Column(String(50))

    def __repr__(self):
        return f"<User(id={self.id}, name='{self.name}', nickname='{self.nickname}')>"

# 4. 모델을 기반으로 데이터베이스에 테이블 생성
Base.metadata.create_all(engine)

# 5. 데이터베이스와 상호작용할 Session 생성
Session = sessionmaker(bind=engine)
session = Session()

# --- CRUD 작업 ---

# 6. 데이터 생성 (Create)
print("\n--- CREATE ---")
new_user = User(name='John Doe', fullname='Johnathan Doe', nickname='johnny')
session.add(new_user)
session.commit() # 변경사항을 데이터베이스에 반영

# 7. 데이터 조회 (Read)
print("\n--- READ ---")
our_user = session.query(User).filter_by(name='John Doe').first()
print("조회된 사용자:", our_user)

# 8. 데이터 수정 (Update)
print("\n--- UPDATE ---")
our_user.nickname = 'johndoe_updated'
session.commit()
print("수정된 사용자:", our_user)

# 9. 데이터 삭제 (Delete)
print("\n--- DELETE ---")
session.delete(our_user)
session.commit()

# 삭제 확인
deleted_user = session.query(User).filter_by(name='John Doe').first()
print("삭제 후 조회:", deleted_user)

# 10. 세션 종료
session.close()
```

---

## 4. 예상 면접 질문 (Potential Interview Questions)

*   **Q. ORM을 사용하는 가장 큰 이유는 무엇인가요?**
    *   **A.** 가장 큰 이유는 **생산성 향상**과 **유지보수성 증대**입니다. 개발자가 SQL을 직접 작성하는 대신 익숙한 객체 지향 언어(Python)로 데이터베이스를 다룰 수 있게 하여 개발 속도를 높여줍니다. 또한, SQL 쿼리가 코드에 흩어져 있는 대신 모델과 비즈니스 로직에 집중된 코드를 작성하게 되어 가독성과 재사용성이 높아지고, 이는 장기적인 유지보수에 큰 이점을 줍니다.

*   **Q. SQLAlchemy의 `Session`은 어떤 역할을 하나요?**
    *   **A.** `Session`은 ORM에서 데이터베이스와의 모든 상호작용을 처리하는 **작업 공간** 또는 **중개자** 역할을 합니다. 모델 객체를 추가(`add`), 수정, 삭제(`delete`)하는 등의 모든 작업은 우선 `Session`의 관리 하에 놓입니다. 이러한 변경사항들은 `Session`에 누적되었다가, `commit()` 메서드가 호출될 때 비로소 실제 데이터베이스에 트랜잭션으로 반영됩니다. 즉, 데이터베이스와의 일관성 있고 원자적인 작업을 보장하는 역할을 합니다.

*   **Q. ORM을 사용하면 항상 성능이 좋을까요? 어떤 경우에 성능 저하가 발생할 수 있나요?**
    *   **A.** 항상 성능이 좋은 것은 아닙니다. ORM은 개발 편의성을 위해 때로는 비효율적인 SQL 쿼리를 생성할 수 있습니다. 특히, 여러 테이블을 복잡하게 조인하거나, 대용량 데이터를 집계하는 등의 작업에서는 ORM이 생성한 쿼리가 직접 작성한 최적화된 쿼리보다 성능이 떨어질 수 있습니다. 또한, 객체와 관계를 추적하는 내부 메커니즘으로 인해 약간의 오버헤드가 발생할 수 있습니다. 따라서 성능이 매우 중요한 부분에서는 ORM을 사용하더라도 실제 실행되는 SQL을 확인하고 튜닝하거나, 필요한 경우 순수 SQL을 함께 사용하는 것이 좋습니다.

---

## 5. 더 읽어보기 (Further Reading)

*   [SQLAlchemy 2.0 ORM Tutorial](https://docs.sqlalchemy.org/en/20/orm/tutorial.html)
*   [Object-Relational Mapping (Wikipedia)](https://en.wikipedia.org/wiki/Object%E2%80%93relational_mapping)
*   [What is an ORM? (Prisma Docs)](https://www.prisma.io/docs/concepts/overview/what-is-an-orm)