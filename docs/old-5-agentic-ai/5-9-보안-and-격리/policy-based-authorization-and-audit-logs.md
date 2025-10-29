---
title: "정책 기반 인가 & 감사 로그"
date: "2025-07-07"
tags: ["Agentic AI", "보안", "PBAC", "OPA", "Audit Log"]
difficulty: "hard"
---

# 정책 기반 인가 & 감사 로그 (Policy-Based Authorization & Audit Logs)

## 1. 핵심 개념 (Core Concept)

**정책 기반 인가(Policy-Based Authorization)**는 "누가, 무엇을, 언제, 어떻게 접근할 수 있는가"를 중앙화된 정책(Policy)으로 상세하게 정의하고 강제하는 유연한 접근 제어 모델입니다. **감사 로그(Audit Logs)**는 에이전트의 모든 중요한 활동과 정책 결정 과정을 시간 순서대로 기록하여, 사후 분석, 책임 추적, 보안 사고 대응을 가능하게 하는 필수적인 기록입니다.

---

## 2. 상세 설명 (Detailed Explanation)

에이전트 시스템이 복잡해지고 다양한 사용자와 리소스를 다루게 되면서, 단순한 역할 기반 제어(RBAC)만으로는 충분하지 않습니다. 정책 기반 인가와 상세한 감사 로그는 복잡한 환경에서 보안과 신뢰성을 확보하는 핵심 요소입니다.

### 2.1 정책 기반 인가 (Policy-Based Authorization, PBAC)

PBAC는 전통적인 역할 기반 접근 제어(RBAC)를 확장하여, 다양한 속성(Attribute)을 기반으로 접근 규칙을 동적으로 평가합니다.

*   **구성 요소**:
    *   **주체 (Subject)**: 사용자, 그룹, 또는 다른 서비스/에이전트의 속성 (예: 역할, 부서, 위치)
    *   **객체 (Object)**: 접근 대상 리소스의 속성 (예: 파일의 소유자, 데이터의 민감도 등급)
    *   **행위 (Action)**: 주체가 객체에 수행하려는 작업 (예: `read`, `write`, `execute`)
    *   **환경 (Environment)**: 요청이 발생하는 맥락 정보 (예: 시간, IP 주소, 사용 기기)
*   **정책 엔진 (Policy Engine)**: 이러한 속성들을 평가하여 접근 허용/거부를 결정하는 중앙화된 컴포넌트입니다. 대표적으로 **OPA(Open Policy Agent)**가 있습니다.
*   **장점**:
    *   **유연성 및 세분화**: "재무팀의 매니저는, 업무 시간 중에, 회사 네트워크에서만, '결제' 툴을 사용할 수 있다"와 같은 복잡하고 세분화된 규칙을 쉽게 정의할 수 있습니다.
    *   **중앙화 및 분리**: 인가 로직을 애플리케이션 코드와 분리하여, 보안 정책을 중앙에서 일관되게 관리하고 코드 변경 없이 정책을 업데이트할 수 있습니다.

### 2.2 감사 로그 (Audit Logs)

감사 로그는 "무슨 일이 있었는지"에 대한 명확하고 변경 불가능한 증거를 제공합니다. 잘 설계된 감사 로그는 시스템의 투명성을 높이고 문제 해결을 용이하게 합니다.

*   **5W1H 원칙**: 효과적인 감사 로그는 다음 정보를 포함해야 합니다.
    *   **Who (누가)**: 요청을 시작한 사용자 또는 시스템의 식별자
    *   **When (언제)**: 이벤트가 발생한 정확한 시간 (타임스탬프, UTC 기준)
    *   **Where (어디서)**: 요청이 발생한 소스 (IP 주소, 서비스 이름)
    *   **What (무엇을)**: 접근한 리소스나 호출된 툴 이름
    *   **How (어떻게)**: 수행된 구체적인 작업과 사용된 파라미터
    *   **Outcome (결과)**: 작업의 성공/실패 여부 및 정책 엔진의 결정 (허용/거부)
*   **로깅 대상**:
    *   사용자 프롬프트 및 LLM 응답 전체
    *   에이전트의 내부 추론 과정 (Chain of Thought)
    *   호출된 모든 툴, 전달된 인수, 반환된 결과
    *   인가 정책 평가 요청 및 결정 내용
    *   사용자 승인/거부 이벤트
*   **보안**: 감사 로그는 위변조를 방지하기 위해 쓰기 전용(append-only) 및 불변(immutable) 스토리지(예: AWS S3 Object Lock, 전용 로깅 서비스)에 저장해야 합니다.

---

## 3. 예시 (Example)

### 정책 예시 (OPA Rego)

OPA에서 사용하는 `Rego` 언어로 작성된 정책 예시입니다.

```rego
package agent.authz

# 기본 정책: 모든 요청은 기본적으로 거부
default allow = false

# 'admin' 역할을 가진 사용자는 모든 툴 사용을 허용
allow {
    input.subject.attributes.role == "admin"
}

# 'developer'는 'dev' 환경에서 'code_executor' 툴을 사용할 수 있음
allow {
    input.subject.attributes.role == "developer"
    input.action.name == "execute"
    input.resource.name == "code_executor"
    input.environment.context.env == "dev"
}

# 'billing' 툴은 'finance' 팀원만, 업무 시간(9-18시)에만 허용
allow {
    input.subject.attributes.team == "finance"
    input.resource.name == "billing"
    input.environment.time.hour >= 9
    input.environment.time.hour < 18
}
```

### 감사 로그 예시 (JSON 형식)

```json
{
  "eventId": "evt_1a2b3c4d5e6f",
  "timestamp": "2025-07-07T10:30:00Z",
  "source": {
    "ip": "203.0.113.42",
    "service": "api-gateway"
  },
  "subject": {
    "id": "user-5678",
    "role": "developer"
  },
  "action": {
    "name": "execute",
    "parameters": {
      "tool_name": "code_executor",
      "code": "print('hello world')"
    }
  },
  "resource": {
    "name": "code_executor",
    "type": "tool"
  },
  "authorization": {
    "decision": "deny",
    "policy": "agent.authz",
    "reason": "Execution environment was 'production', not 'dev'."
  },
  "outcome": {
    "status": "failure",
    "message": "Permission denied by policy."
  }
}
```

---

## 4. 예상 면접 질문 (Potential Interview Questions)

*   **Q. AI 에이전트 시스템에서 감사 로그가 왜 중요한가요? 어떤 내용을 기록해야 할까요?**
    *   **A.** 감사 로그는 에이전트의 행동을 투명하게 추적하고, 보안 사고 발생 시 원인을 분석하며, 규제 준수 요구사항을 충족하기 위해 필수적입니다. 기록해야 할 핵심 내용은 5W1H 원칙에 따라 사용자 식별자(Who), 타임스탬프(When), 접근 소스(Where), 호출된 툴과 파라미터(What/How), 그리고 실행 결과와 정책 결정(Outcome)입니다. 특히 LLM의 입력과 출력 전체를 로깅하여 재현성을 확보하는 것이 중요합니다.
*   **Q. 역할 기반 접근 제어(RBAC)에 비해 정책 기반 접근 제어(PBAC)가 가지는 장점은 무엇인가요?**
    *   **A.** RBAC는 사용자의 '역할'에 따라 권한이 정적으로 결정되지만, PBAC는 역할뿐만 아니라 리소스의 속성, 환경(시간, IP 주소) 등 다양한 '속성'을 조합하여 훨씬 더 동적이고 세분화된(fine-grained) 접근 제어를 할 수 있습니다. 예를 들어, "의사(역할)는 자신의 환자(리소스 속성)의 차트만, 병원 네트워크(환경) 내에서만 열람할 수 있다"와 같은 복잡한 실제 시나리오를 규칙으로 쉽게 구현할 수 있습니다.
*   **Q. OPA(Open Policy Agent)와 같은 정책 엔진을 도입하면 아키텍처 관점에서 어떤 이점이 있나요?**
    *   **A.** 가장 큰 이점은 **인가 로직의 분리(decoupling)**입니다. 정책 결정을 위한 로직을 애플리케이션의 비즈니스 로직에서 떼어내어 중앙화된 정책 엔진으로 위임합니다. 이로 인해 개발자는 보안 정책에 신경 쓰지 않고 기능 개발에 집중할 수 있으며, 보안팀은 코드 배포 없이 독립적으로 정책을 수정하고 관리할 수 있습니다. 이는 시스템의 유연성과 확장성을 크게 향상시키고, 일관된 정책 적용을 보장합니다.

---

## 5. 더 읽어보기 (Further Reading)

*   [Open Policy Agent (OPA) Documentation](https://www.openpolicyagent.org/docs/latest/)
*   [Policy-as-Code: The Future of Access Control](https://www.pomerium.com/blog/2022/03/policy-as-code)
*   [Logging Cheat Sheet - OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html)