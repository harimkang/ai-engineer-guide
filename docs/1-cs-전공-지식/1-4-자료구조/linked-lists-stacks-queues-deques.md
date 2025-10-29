---
title: 연결 리스트, 스택, 큐, 데크
date: '2025-07-02'
tags: [자료구조, CS, 연결리스트, 스택, 큐, 데크]
difficulty: easy
---

# 연결 리스트, 스택, 큐, 데크

## 1. 핵심 개념 (Core Concept)

**연결 리스트, 스택, 큐, 데크**는 데이터를 선형(Linear)으로 저장하고 관리하는 기본적인 자료구조입니다. **연결 리스트**는 노드들이 포인터로 연결된 구조로 데이터의 삽입/삭제가 용이하며, \*\*스택(Stack)\*\*과 \*\*큐(Queue)\*\*는 각각 후입선출(LIFO)과 선입선출(FIFO)이라는 특정 접근 방식을 가지는 추상 자료형(ADT)입니다. \*\*데크(Deque)\*\*는 스택과 큐의 기능을 모두 가진 자료구조로, 양쪽 끝에서 삽입과 삭제가 모두 가능합니다.

______________________________________________________________________

## 2. 상세 설명 (Detailed Explanation)

### 2.1 연결 리스트 (Linked List)

연결 리스트는 데이터와 다음 노드를 가리키는 포인터(링크)로 구성된 \*\*노드(Node)\*\*들이 연결된 형태의 자료구조입니다. 배열과 달리 메모리상에 비연속적으로 데이터를 저장할 수 있습니다.

- **구조**: 각 노드는 `[Data | Pointer]` 형태로 구성됩니다.
- **장점**: 데이터의 삽입과 삭제가 O(1)로 매우 빠릅니다. 크기가 동적으로 변할 수 있습니다.
- **단점**: 특정 원소를 찾기 위해서는 처음부터 순차적으로 탐색해야 하므로 탐색 속도가 O(n)으로 느립니다. 또한, 포인터를 저장하기 위한 추가 메모리 공간이 필요합니다.
- **종류**: 단일 연결 리스트(Singly), 이중 연결 리스트(Doubly), 원형 연결 리스트(Circular) 등이 있습니다.

### 2.2 스택 (Stack)

스택은 **후입선출(LIFO, Last-In, First-Out)** 원칙에 따라 동작하는 자료구조입니다. 가장 나중에 삽입된 데이터가 가장 먼저 삭제됩니다.

- **주요 연산**:
  - **Push**: 스택의 맨 위(top)에 데이터를 추가합니다.
  - **Pop**: 스택의 맨 위(top)에서 데이터를 제거하고 반환합니다.
- **특징**: 데이터의 입출력이 한쪽 끝에서만 일어나며, 모든 연산이 O(1)로 매우 빠릅니다.
- **구현**: 배열이나 연결 리스트를 사용하여 구현할 수 있습니다.

### 2.3 큐 (Queue)

큐는 **선입선출(FIFO, First-In, First-Out)** 원칙에 따라 동작하는 자료구조입니다. 가장 먼저 삽입된 데이터가 가장 먼저 삭제됩니다.

- **주요 연산**:
  - **Enqueue**: 큐의 뒤(rear)에 데이터를 추가합니다.
  - **Dequeue**: 큐의 앞(front)에서 데이터를 제거하고 반환합니다.
- **특징**: 데이터가 들어온 순서대로 처리되는 것을 보장하며, 모든 연산이 O(1)로 빠릅니다.
- **구현**: 배열이나 연결 리스트를 사용하여 구현할 수 있습니다.

### 2.4 데크 (Deque, Double-ended Queue)

데크는 스택과 큐의 기능을 모두 가진 자료구조로, **양쪽 끝에서 모두 데이터의 삽입과 삭제가 가능**합니다.

- **주요 연산**:
  - `append`, `pop`: 오른쪽 끝에서의 삽입/삭제 (스택처럼 사용)
  - `appendleft`, `popleft`: 왼쪽 끝에서의 삽입/삭제 (큐처럼 사용)
- **특징**: 스택과 큐의 연산을 모두 O(1)로 지원하여 매우 유연합니다.
- **구현**: 파이썬에서는 `collections.deque`를 통해 효율적인 데크를 제공합니다.

### 2.5 비교 요약

| 자료구조        | 데이터 접근 방식 | 주요 연산             | 시간 복잡도 |
| :-------------- | :--------------- | :-------------------- | :---------- |
| **연결 리스트** | 순차 접근        | 삽입/삭제, 탐색       | O(1) / O(n) |
| **스택**        | 후입선출 (LIFO)  | Push, Pop             | O(1)        |
| **큐**          | 선입선출 (FIFO)  | Enqueue, Dequeue      | O(1)        |
| **데크**        | 양방향 접근      | 양쪽 끝에서 삽입/삭제 | O(1)        |

______________________________________________________________________

## 3. 예시 (Example)

### Python 코드 예시

```python
from collections import deque

# 스택 (Stack) - 일반 list로 구현
stack = []
stack.append(1) # Push
stack.append(2)
stack.append(3)
print("Stack:", stack)
print("Pop from stack:", stack.pop()) # 3
print("Stack after pop:", stack)

# 큐 (Queue) - collections.deque로 구현
queue = deque()
queue.append(1) # Enqueue
queue.append(2)
queue.append(3)
print("\nQueue:", queue)
print("Dequeue from queue:", queue.popleft()) # 1
print("Queue after dequeue:", queue)

# 데크 (Deque)
d = deque([1, 2, 3])
d.appendleft(0)  # 왼쪽에 추가
d.append(4)      # 오른쪽에 추가
print("\nDeque:", d)
print("Pop from right:", d.pop())      # 4
print("Pop from left:", d.popleft()) # 0
print("Deque after pops:", d)
```

### 사용 사례 (Use Case)

- **연결 리스트**: 데이터의 삽입/삭제가 빈번한 동적 데이터 관리 (예: 음악 플레이리스트)
- **스택**: 웹 브라우저의 '뒤로 가기' 기능, 함수 호출 스택, 괄호 검사
- **큐**: 프린터 인쇄 대기열, 너비 우선 탐색(BFS), 메시지 큐 시스템
- **데크**: 실행 취소/다시 실행(Undo/Redo) 기능, 슬라이딩 윈도우 알고리즘

______________________________________________________________________

## 4. 예상 면접 질문 (Potential Interview Questions)

- **Q. 배열과 연결 리스트의 장단점을 비교 설명해주세요.**

  - **A.** 배열은 데이터가 메모리에 연속적으로 저장되어 인덱스를 통한 접근 속도가 O(1)로 매우 빠르지만, 크기가 고정되어 있고 중간에 데이터를 삽입/삭제할 때 O(n)의 비용이 듭니다. 반면, 연결 리스트는 데이터의 삽입/삭제가 O(1)로 빠르고 크기가 동적이지만, 특정 데이터에 접근하려면 처음부터 순차 탐색해야 하므로 O(n)의 시간이 걸립니다.

- **Q. 스택 두 개를 사용하여 큐를 어떻게 구현할 수 있을까요?**

  - **A.** 두 개의 스택, `in_stack`과 `out_stack`을 사용합니다. Enqueue(삽입) 시에는 `in_stack`에 push합니다. Dequeue(삭제) 시에는, 먼저 `out_stack`이 비어있는지 확인합니다. 비어있다면 `in_stack`의 모든 원소를 pop하여 `out_stack`에 push합니다. 이 과정에서 `in_stack`의 LIFO 순서가 `out_stack`에서는 FIFO 순서로 역전됩니다. 그 후 `out_stack`에서 pop하면 큐처럼 선입선출로 데이터를 꺼낼 수 있습니다.

- **Q. 파이썬에서 큐를 구현할 때 일반 `list` 대신 `collections.deque`를 사용하는 이유는 무엇인가요?**

  - **A.** 파이썬의 일반 `list`는 동적 배열로 구현되어 있어, 리스트의 맨 뒤에 원소를 추가(`append`)하거나 제거(`pop`)하는 것은 O(1)로 빠릅니다. 하지만 리스트의 맨 앞에 원소를 추가(`insert(0, x)`)하거나 제거(`pop(0)`)하려면 모든 원소를 한 칸씩 이동시켜야 하므로 O(n)의 시간이 걸립니다. 반면, `collections.deque`는 이중 연결 리스트(Doubly Linked List)로 구현되어 있어, 양쪽 끝에서의 삽입과 삭제가 모두 O(1)의 시간 복잡도를 보장합니다. 따라서 큐의 FIFO 동작을 효율적으로 구현하기 위해 `deque`를 사용합니다.

______________________________________________________________________

## 5. 더 읽어보기 (Further Reading)

- [Python `collections.deque` (Official Docs)](https://docs.python.org/3/library/collections.html#collections.deque)
- [Stack, Queue, and Deque (realpython.com)](https://realpython.com/python-stack-queue-deque/)
- [Linked List Data Structure (GeeksforGeeks)](https://www.geeksforgeeks.org/data-structures/linked-list/)
