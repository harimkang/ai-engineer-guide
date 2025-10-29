---
title: "에코시스템: OpenDevin, AutoGen, CrewAI, LangGraph, LlamaIndex, MemGPT, AirOps"
date: "2025-10-29"
tags: ["Agentic AI", "Ecosystem", "Frameworks"]
difficulty: "easy"
---

# 에코시스템 개요

## 1. 핵심 개념 (Core Concept)

주요 오픈소스 프레임워크와 상용 도구를 지형도 관점에서 개괄하고, “언제 무엇을 선택할지” 기준을 제시합니다. 특정 프레임워크에 고정되지 않도록 “계약(스키마) 우선, 구현 교체 가능” 원칙을 권장합니다.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 오케스트레이션/워크플로우
- LangGraph(LangChain 차세대 오케스트레이션): 상태 머신·브랜치 제어에 강점
- CrewAI: 역할 기반 멀티-에이전트, 실무 템플릿 풍부
- AutoGen: 대화형 멀티-에이전트 프레임, 커스텀 대화 규약 정의 용이

### 2.2 데이터/문서 중심 에이전트
- LlamaIndex: 문서 인덱싱·RAG 파이프라인, 노드·리트리버 조합 유연
- MemGPT: 장기/단기 메모리 관리, 요약·교체 정책 내장

### 2.3 개발자·운영 에이전트
- OpenDevin: 개발 워크플로우(코드 편집/실행/테스트) 특화
- AirOps 등: 운영/자동화 도구 집합, 모니터링·템플릿 제공

### 2.4 선택 기준(Practitioner Tips)
- 제약: 조직 보안·네트워크·비용·언어 모델 제약을 먼저 확인
- 필요: 상태 머신·대화형·RAG·역할 협업 등 핵심 요구 충족 여부
- 교체 용이성: 프롬프트/툴/평가 스키마를 프레임워크 바깥에 정의

---

## 3. 예시 (Example)

- LangGraph로 코디네이터-워커 패턴 구현 개요.
- LlamaIndex로 제품 매뉴얼 인덱싱 → RAG + 에이전트 조합으로 지원봇 구성.

---

## 4. 예상 면접 질문 (Potential Interview Questions)

- LangChain과 LlamaIndex의 강점 차이를 설명하라.
- 오케스트레이션 프레임워크 교체 시 어떤 계약을 고정해 리스크를 줄이는가?

---

## 5. 더 읽어보기 (Further Reading)

- docs/references/google/Agents_Companion_v2.pdf
- docs/references/openai/a-practical-guide-to-building-agents-3.pdf

---

## 6. See also

- 시스템 개요: 5-1 → [overview](./overview.md)
- 구성 요소: 5-1 → [components](./components.md)
