---
title: "보안 & 가드레일: 프롬프트 인젝션, 격리, 권한, 감사"
date: "2025-10-29"
tags: ["Agentic AI", "Security", "Guardrails"]
difficulty: "medium"
---

# 보안 & 가드레일

## 1. 핵심 개념 (Core Concept)

프롬프트 인젝션 방어, 샌드박스/컨테이너 격리, 최소 권한(Principle of Least Privilege), 정책 기반 인가, 감사 로그로 안전한 실행을 보장합니다. “계약 우선 스키마(입·출력·툴)”와 “격리/감사/취소 가능성”이 핵심입니다.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 위협 모델(대표)
- 프롬프트 인젝션/지시 우회: 사용자가 컨텍스트/문서로 정책 무력화 시도
- 데이터 유출(Exfiltration): 비공개 지식/비밀키가 답변/로그로 노출
- SSRF/네트워크 오남용: 임의 내부 주소 접근, 금지 도메인 호출
- 툴 오남용: 과도 권한으로 파일/DB/결제/티켓 조작
- 공급망: 모델/플러그인/패키지/데이터셋 변조·악성 코드

### 2.2 입력 가드레일(Pre-LLM)
- 정책 프롬프트 결합: 시스템 지시문 고정(불변), 사용자 입력과 분리
- 정규화/필터: 금지어/링크/PII/명령 패턴 차단·마스킹
- 컨텍스트 위생: 외부 문서에서 “Ignore previous…”류 공격 구문 제거/주석 처리

### 2.3 출력 가드레일(Post-LLM)
- 스키마 검증: JSON Schema/정형 템플릿 강제, 불일치 시 자동 교정
- 콘텐츠 안전: 유해/PII/정책 위반 감지 → 수정/차단/검토 큐
- 인용/근거 강제: 외부 지식 응답에는 출처 ID 필수(환각·유출 방지)

### 2.4 실행 가드레일(툴/네트워크/파일)
- 툴 권한: 툴 스코프·권한·쿼터(속도/비용), 고위험 툴은 휴먼 승인 필요
- 샌드박스: 파일/코드 실행은 격리 컨테이너/VM, 네트워크 이그레스 화이트리스트
- 요청 서명/감사: 외부 API는 서명/추적 ID 포함, 재현 가능한 로그 보관

### 2.5 권한 위임/정책 엔진
- 사용자/테넌트/에이전트/툴 단위 RBAC/ABAC 설계(리소스·행동·컨텍스트)
- 정책 엔진 연동(예: OPA): 선언적 규칙으로 허용/거부·마스킹·레이트 리밋
- 위임(Delegation): 상위 권한으로 하위 세션 임시 토큰 발급(만료/범위 제한)

### 2.6 공급망 보안
- 모델/가중치: 해시·서명 검증, 출처·라이선스 추적
- 패키지/런타임: 고정 버전/서명, SBOM 생성, 취약점 스캔
- 플러그인/툴: 스키마 레지스트리, 검증/승인 워크플로, 격리 배포

---

## 3. 예시 (Example)

### 3.1 네트워크/툴 가드레일 정책(개념 YAML)
```yaml
egress_allowlist:
  - https://api.mycorp.com/*
  - https://docs.mycorp.com/*
tools:
  file_write: {enabled: false}
  ticket_create: {approval: human, rate_limit: 10/h}
secrets:
  mask: [AWS_KEY, DB_URL]
policies:
  deny_prompts: ["ignore previous", "disregard all instructions"]
```

### 3.2 안전 응답 템플릿(요약)
- “근거가 없으면 모른다고 답하라”, “출처를 [Doc §] 형태로 인용하라”, “PII는 마스킹하라”

---

## 4. 예상 면접 질문 (Potential Interview Questions)

- 최소 권한 설계의 핵심 원칙은?
- 프롬프트 인젝션/데이터 유출을 어떻게 탐지·차단하는가?
- 고위험 툴 호출에 휴먼 승인/서킷 브레이커를 어떻게 설계하는가?

---

## 5. 더 읽어보기 (Further Reading)

- docs/references/anthropic/building-effective-agents.md
- docs/references/openai/a-practical-guide-to-building-agents-3.pdf
- docs/references/google/Agents_Companion_v2.pdf

---

## 6. See also

- 툴 스키마/권한: 5-9 → [tool-schemas-jsonrpc-openapi](./tool-schemas-jsonrpc-openapi.md)
- 운영/게이팅: 5-6 → [evaluation-monitoring-ops](../5-6-agentops-운영-and-자동화/evaluation-monitoring-ops.md)
