---
title: "에이전트 간 통신 프로토콜 (ACP & A2A)"
date: "2025-07-06"
tags: ["Agentic AI", "Protocol", "Multi-Agent", "ACP", "A2A"]
difficulty: "hard"
---

# 에이전트 간 통신 프로토콜 (ACP & A2A)

## 1. 핵심 개념 (Core Concept)

**에이전트 간 통신 프로토콜(Agent Communication Protocols)**은 서로 다른 플랫폼과 프레임워크 위에서 개발된 AI 에이전트들이 원활하게 상호작용하고 협업할 수 있도록 만들어진 표준화된 규약임. 대표적으로 Linux Foundation이 주관하는 **ACP(Agent Communication Protocol)**와 여러 기술 기업이 연합하여 만든 **A2A(Agent-to-Agent) Protocol**이 있음. 이 프로토콜들은 에이전트들이 서로를 발견하고, 작업을 요청하며, 결과를 교환하는 방식을 정의하여, 진정한 의미의 개방형 멀티-에이전트 생태계를 구축하는 것을 목표로 함.

---

## 2. 상세 설명 (Detailed Explanation)

과거에도 FIPA-ACL과 같은 에이전트 통신 언어가 있었지만, 최신의 ACP와 A2A는 현대적인 웹 기술을 기반으로 실용적인 상호운용성에 더 중점을 둠.

### 2.1 ACP (Agent Communication Protocol)

ACP는 IBM Research의 BeeAI에 의해 시작되어 현재 Linux Foundation이 관리하는 개방형 표준임. 기술적인 중립성과 기존 웹 인프라와의 호환성을 강조함.

*   **핵심 철학**: 파편화된 에이전트들을 연결하여 상호 운용 가능한 시스템을 만들고, "고립된 전문가"를 "상호 연결된 팀원"으로 전환하는 것을 목표로 함.
*   **기술적 특징**:
    *   **REST 기반**: 표준 HTTP 메소드(GET, POST, PUT, DELETE)를 사용하여 통신하므로, 기존 웹 개발자들에게 매우 친숙하고 특별한 SDK가 필요 없음.
    *   **비동기 우선(Async-first)**: 긴 시간이 소요되는 작업을 처리하기 위해 비동기 통신을 기본으로 설계함.
    *   **MIME 타입 활용**: 데이터 형식을 MIME 타입으로 지정하여, 텍스트, 이미지, 비디오 등 모든 종류의 데이터를 별도 프로토콜 변경 없이 확장 가능함.

### 2.2 A2A (Agent-to-Agent) Protocol

A2A는 Google, Atlassian, Salesforce 등 50개 이상의 기술 기업이 협력하여 만든 개방형 표준으로, 에이전트 간의 안전한 발견과 협업에 중점을 둠.

*   **핵심 철학**: 자율적인 AI 에이전트들이 서로를 찾고, 안전하게 통신하며, 함께 작업할 수 있는 생태계를 만드는 것을 목표로 함.
*   **기술적 특징**:
    *   **JSON-RPC 기반**: 경량 원격 프로시저 호출(RPC) 프로토콜인 JSON-RPC를 사용하여 작업 중심으로 통신함.
    *   **에이전트 카드 (Agent Card)**: 각 에이전트는 자신의 능력, 전문 분야, 호출 방법 등을 기술한 JSON 형식의 "에이전트 카드"를 게시함. 다른 에이전트는 이 카드를 보고 특정 작업에 가장 적합한 에이전트를 동적으로 찾을 수 있음.
    *   **작업 중심 라이프사이클**: 작업의 생성, 실행, 피드백, 완료에 이르는 명확한 라이프사이클을 정의함.

### 2.3 비교 분석

| 구분 | ACP (Agent Communication Protocol) | A2A (Agent-to-Agent) Protocol |
| :--- | :--- | :--- |
| **주요 지지자** | IBM, Linux Foundation | Google, Atlassian, Salesforce 등 50+ 기업 |
| **통신 방식** | REST (HTTP 표준 메소드) | JSON-RPC | 
| **에이전트 발견** | 패키지 메타데이터 (오프라인) | 에이전트 카드 (동적, 온라인) |
| **핵심 구조** | 리소스 중심 (웹 표준과 유사) | 작업/태스크 중심 (RPC) |
| **확장성** | MIME 타입을 통한 데이터 형식 확장 | 메시지 파트를 통한 콘텐츠 협상 |

---

## 3. 예시 (Example)

### 사용 사례: 여행 계획 멀티-에이전트 시스템

*   **상황**: `여행 플래너 에이전트`가 `항공권 검색 에이전트`와 `숙소 예약 에이전트`와 협업하는 시나리오.

*   **A2A 프로토콜 방식**:
    1.  `여행 플래너`는 "항공권 검색" 능력을 가진 에이전트를 찾기 위해 에이전트 레지스트리를 검색함.
    2.  `항공권 검색 에이전트`의 "에이전트 카드"를 발견하고, 카드에 명시된 `search_flights` 메소드를 확인.
    3.  `여행 플래너`는 JSON-RPC 형식으로 `search_flights` 태스크를 `항공권 검색 에이전트`에게 요청함.
    4.  `항공권 검색 에이전트`는 작업을 비동기적으로 수행하고, 완료되면 `여행 플래너`에게 결과를 콜백으로 알려줌.

*   **ACP 프로토콜 방식**:
    1.  `여행 플래너`는 `항공권 검색 에이전트`의 알려진 엔드포인트(예: `/tasks`)에 POST 요청을 보냄.
    2.  요청 본문에는 작업의 상세 내용(목적지, 날짜 등)이 포함됨. `Content-Type` 헤더는 `application/json`으로 설정.
    3.  `항공권 검색 에이전트`는 요청을 수락하고, 작업 상태를 확인할 수 있는 URL(예: `/tasks/{task_id}`)을 응답으로 반환.
    4.  `여행 플래너`는 해당 URL에 주기적으로 GET 요청을 보내 작업의 진행 상태를 확인하고, 완료되면 결과를 가져옴.

---

## 4. 예상 면접 질문 (Potential Interview Questions)

*   **Q. ACP와 A2A 프로토콜의 가장 큰 철학적 차이는 무엇인가요?**
    *   **A.** ACP는 기존의 검증된 웹 표준(REST, HTTP)을 최대한 활용하여 에이전트 통신을 웹의 일부로 자연스럽게 통합하려는 철학에 가깝습니다. 반면, A2A는 에이전트 간의 '작업(Task)' 수행과 '발견(Discovery)'이라는 특정 문제에 집중하여, 이를 위한 새로운 표준('에이전트 카드', JSON-RPC 기반 태스크)을 정의하는 데 더 중점을 둡니다.

*   **Q. A2A 프로토콜의 '에이전트 카드'는 어떤 장점을 가지나요?**
    *   **A.** 에이전트 생태계의 동적인 확장성을 크게 향상시킵니다. 새로운 에이전트가 추가될 때마다 자신의 능력을 기술한 카드를 레지스트리에 등록하기만 하면, 다른 에이전트들이 별도의 설정 변경 없이도 이 새로운 에이전트를 발견하고 그 능력을 활용할 수 있습니다. 이는 중앙의 통제 없이도 시스템이 유기적으로 성장할 수 있게 만듭니다.

*   **Q. 왜 이 두 프로토콜 모두 비동기(asynchronous) 통신을 중요하게 생각할까요?**
    *   **A.** 에이전트가 수행하는 작업, 예를 들어 복잡한 리서치나 여러 툴을 사용하는 작업은 완료하는 데 수 초에서 수 분 이상이 걸릴 수 있기 때문입니다. 만약 동기식으로 통신한다면, 요청자 에이전트는 작업이 끝날 때까지 아무것도 못하고 기다려야만 합니다. 비동기 통신을 통해 요청자 에이전트는 작업을 위임한 뒤 다른 일을 계속할 수 있고, 나중에 작업이 완료되었다는 알림을 받아 효율적으로 시스템을 운영할 수 있습니다.

---

## 5. 더 읽어보기 (Further Reading)

*   [Agent Communication Protocol (ACP) Official Site](https://agentcommunicationprotocol.dev/)
*   [A2A (Agent-to-Agent) Protocol Official Site](https://a2aprotocol.ai/)
*   [Google's blog post on the A2A protocol](https://blog.google/technology/ai/google-ai-agents-research-and-products/)
*   [IBM's blog post on ACP](https://research.ibm.com/blog/beeai-agent-communication-protocol)
