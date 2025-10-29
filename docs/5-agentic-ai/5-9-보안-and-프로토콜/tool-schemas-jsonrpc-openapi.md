---
title: '툴 스키마: JSON-RPC & OpenAPI'
date: '2025-10-29'
tags: [Agentic AI, Tool Schema, JSON-RPC, OpenAPI]
difficulty: medium
---

# 툴 스키마: JSON-RPC & OpenAPI

## 1. 핵심 개념 (Core Concept)

함수/툴 호출을 표준 스키마(JSON Schema/JSON-RPC/OpenAPI)로 정의해 안전성과 상호운용성을 확보하고, 프롬프트 스키마와 연결하여 실패를 줄입니다. “입력 검증·오류 모델·권한·쿼터”가 스키마의 핵심 구성요소입니다.

______________________________________________________________________

## 2. 상세 설명 (Detailed Explanation)

### 2.1 파라미터·응답 스키마와 검증

- JSON Schema로 `type/required/enum/format` 지정, 예: 이메일/URL/날짜 형식 검증
- 응답은 성공/오류를 분리, 성공 시 최소/최대 필드 정의(스키마 진화 고려)

### 2.2 오류/예외 모델링과 재시도 정책

- 오류 코드 분류: `INVALID_INPUT`, `NOT_FOUND`, `RATE_LIMIT`, `INTERNAL`
- 재시도 힌트: `retryable: true/false`, `backoff: exponential`, `retry_after`
- 관찰: 요청 ID/추적 ID 포함, 정책 엔진에서 게이팅

### 2.3 권한·쿼터·보안

- 권한: scope(읽기/쓰기/결제 등)와 리소스 경계(프로젝트/사용자)
- 쿼터/레이트 리밋: 분당/시간당 호출, 비용 상한, 고위험 툴은 휴먼 승인
- 보안: 입력 길이/패턴 제한, URL 화이트리스트, 내부망 차단 규칙

### 2.4 프롬프트 설계와의 연계(→ 5-5)

- 시스템 지시문에 툴 용도/제약/스키마 요약 포함, 금지 시나리오 명시
- 스키마 위반 시 자동 교정 프롬프트로 재생성 유도

______________________________________________________________________

## 3. 예시 (Example)

### 3.1 JSON-RPC 예시(개념)

```json
{
  "jsonrpc":"2.0","method":"weather.get","id":1,
  "params":{"city":"Seoul","unit":"C"}
}
{
  "jsonrpc":"2.0","result":{"temp":21.5,"desc":"cloudy"},"id":1
}
{
  "jsonrpc":"2.0","error":{"code":"RATE_LIMIT","message":"Too many requests","retry_after":60},"id":1
}
```

### 3.2 OpenAPI 스니펫(개념)

```yaml
openapi: 3.1.0
paths:
  /weather:
    get:
      parameters:
        - in: query
          name: city
          schema: {type: string}
          required: true
        - in: query
          name: unit
          schema: {enum: [C,F], default: C}
      responses:
        '200': {content: {application/json: {schema: {type: object, properties: {temp: {type: number}, desc: {type: string}}}}}}
        '429': {content: {application/json: {schema: {type: object, properties: {retry_after: {type: integer}}}}}}
```

______________________________________________________________________

## 4. 예상 면접 질문 (Potential Interview Questions)

- 툴 스키마 설계 시 안전성 보장 포인트는?
- 스키마 진화(호환성)와 버전 관리는 어떻게 하는가?
- 오류/재시도 힌트를 어디서 누구에게 제공하는가?
- 툴 설명/제약을 프롬프트에 어떻게 반영하는가?

______________________________________________________________________

## 5. 더 읽어보기 (Further Reading)

- docs/references/openai/a-practical-guide-to-building-agents-3.pdf

______________________________________________________________________

## 6. See also

- 프롬프트 설계/스키마 연계: 5-5 → [prompt-design-optimization](../5-5-%ED%94%84%EB%A1%AC%ED%94%84%ED%8A%B8-%EC%97%94%EC%A7%80%EB%8B%88%EC%96%B4%EB%A7%81-and-%ED%8F%89%EA%B0%80/prompt-design-optimization.md), [prompt-evaluation-and-benchmarks](../5-5-%ED%94%84%EB%A1%AC%ED%94%84%ED%8A%B8-%EC%97%94%EC%A7%80%EB%8B%88%EC%96%B4%EB%A7%81-and-%ED%8F%89%EA%B0%80/prompt-evaluation-and-benchmarks.md)
- 에이전트 상호작용/프로토콜: 5-1 → [agent-interaction-and-protocols](../5-1-%EC%8B%9C%EC%8A%A4%ED%85%9C-%EC%84%A4%EA%B3%84/agent-interaction-and-protocols.md)
