---
title: "에코시스템: MetaGPT, OpenDevin, AutoGen, CrewAI, LangGraph, LlamaIndex"
date: "2025-10-29"
tags: ["Agentic AI", "Ecosystem", "Frameworks", "MetaGPT", "MCP"]
difficulty: "medium"
---

# 에코시스템 개요

## 1. 핵심 개념 (Core Concept)

주요 오픈소스 프레임워크와 상용 도구를 지형도 관점에서 개괄하고, “언제 무엇을 선택할지” 기준을 제시합니다. 특정 프레임워크에 고정되지 않도록 “계약(스키마) 우선, 구현 교체 가능” 원칙을 권장합니다.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 오케스트레이션/워크플로우
- **LangGraph (LangChain)**: 상태 머신(State Machine) 기반으로 에이전트의 순환, 분기, 상태 공유를 제어하는 데 강점이 있습니다.
- **CrewAI**: 역할 기반(Role-based) 멀티-에이전트 협업에 특화되어 있으며, 실무적인 팀 구성 템플릿이 풍부합니다.
- **AutoGen (Microsoft)**: 유연한 대화형 멀티-에이전트 프레임워크로, 복잡한 대화 패턴과 워크플로우를 직접 정의할 수 있습니다.

### 2.2 데이터/문서 중심 에이전트
- **LlamaIndex**: 문서 인덱싱 및 RAG(Retrieval-Augmented Generation) 파이프라인 구축에 강점을 가집니다. 데이터 로딩, 인덱싱, 검색, 합성을 위한 다양한 컴포넌트를 제공합니다.
- **MemGPT**: LLM의 제한된 컨텍스트 창 문제를 해결하기 위해 가상 메모리 관리 기법을 도입하여, 장기/단기 기억을 효율적으로 관리합니다.

### 2.3 개발자/운영 에이전트
- **MetaGPT**: 가상 소프트웨어 회사를 시뮬레이션하는 멀티-에이전트 프레임워크입니다. Product Manager, Architect, Engineer 등 역할이 부여된 에이전트들이 표준 운영 절차(SOP)에 따라 협업하여 요구사항 분석부터 코드 작성까지 전체 개발 프로세스를 자동화합니다.
- **OpenDevin**: 코드 작성, 실행, 디버깅 등 개발자의 작업을 자동화하는 데 특화된 오픈소스 에이전트입니다.

### 2.4 주요 프로토콜 및 연구 동향
- **Google Agents Companion**: Google이 제시하는 에이전트 개발의 청사진입니다. 에이전트의 생명주기를 관리하는 'Agent Ops', 다중 에이전트 협업, 향상된 RAG(Agentic RAG) 등 엔터프라이즈 환경에서 에이전트를 안정적으로 구축하고 운영하기 위한 핵심 개념을 포함합니다.
- **MCP (Model Context Protocol)**: AI 모델과 외부 도구/데이터를 연결하는 표준화된 방법을 제공하는 오픈소스 프로토콜입니다. 'AI를 위한 USB-C'를 표방하며, 각기 다른 도구에 대한 커스텀 통합 없이 일관된 인터페이스로 에이전트의 능력을 확장할 수 있게 해줍니다.

### 2.5 선택 기준 (Practitioner Tips)
- **제약**: 조직의 보안, 네트워크, 비용, 사용 가능한 LLM 등 기술 외적 제약 조건을 먼저 확인합니다.
- **필요**: 구현하려는 작업이 상태 머신, 역할 기반 협업, 데이터 증강(RAG) 중 어디에 가까운지 핵심 요구사항을 정의합니다.
- **교체 용이성**: 프롬프트, 툴 스키마, 평가 기준 등 핵심 로직을 특정 프레임워크에 종속되지 않도록 외부에서 정의하고 주입하는 방식을 고려합니다.

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
