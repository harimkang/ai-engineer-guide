---
title: 'Agentic AI: 개요 & 로드맵'
date: '2025-10-29'
tags: [Agentic-AI, Guide, Roadmap]
difficulty: easy
---

# Agentic AI: 개요 & 로드맵

에이전트 시스템 설계부터 운영, 평가, 보안·프로토콜, 트렌드까지 한눈에 조망하고, 각 하위 섹션으로 빠르게 이동할 수 있는 인덱스입니다.

______________________________________________________________________

## 섹션 바로가기 (Quick Links)

- 5-1 시스템 설계: [개요](./5-1-%EC%8B%9C%EC%8A%A4%ED%85%9C-%EC%84%A4%EA%B3%84/overview.md), [구성요소](./5-1-%EC%8B%9C%EC%8A%A4%ED%85%9C-%EC%84%A4%EA%B3%84/components.md), [RPA 루프](./5-1-%EC%8B%9C%EC%8A%A4%ED%85%9C-%EC%84%A4%EA%B3%84/core-loop-rpa.md), [에이전트 상호작용/프로토콜](./5-1-%EC%8B%9C%EC%8A%A4%ED%85%9C-%EC%84%A4%EA%B3%84/agent-interaction-and-protocols.md), [에코시스템](./5-1-%EC%8B%9C%EC%8A%A4%ED%85%9C-%EC%84%A4%EA%B3%84/ecosystem.md)
- 5-2 메모리/컨텍스트: [메모리 아키텍처](./5-2-%EB%A9%94%EB%AA%A8%EB%A6%AC-and-%EC%BB%A8%ED%85%8D%EC%8A%A4%ED%8A%B8-%EA%B4%80%EB%A6%AC/memory-architecture.md), [컨텍스트 압축/관리](./5-2-%EB%A9%94%EB%AA%A8%EB%A6%AC-and-%EC%BB%A8%ED%85%8D%EC%8A%A4%ED%8A%B8-%EA%B4%80%EB%A6%AC/context-compression-management.md), [구현 기법](./5-2-%EB%A9%94%EB%AA%A8%EB%A6%AC-and-%EC%BB%A8%ED%85%8D%EC%8A%A4%ED%8A%B8-%EA%B4%80%EB%A6%AC/memory-implementation-techniques.md)
- 5-3 핵심 추론 패턴: [CoT](./5-3-%ED%95%B5%EC%8B%AC-%EC%B6%94%EB%A1%A0-%ED%8C%A8%ED%84%B4/chain-of-thought-cot.md), [ToT](./5-3-%ED%95%B5%EC%8B%AC-%EC%B6%94%EB%A1%A0-%ED%8C%A8%ED%84%B4/tree-of-thoughts-tot.md), [GoT](./5-3-%ED%95%B5%EC%8B%AC-%EC%B6%94%EB%A1%A0-%ED%8C%A8%ED%84%B4/graph-of-thoughts-got.md), [ReAct](./5-3-%ED%95%B5%EC%8B%AC-%EC%B6%94%EB%A1%A0-%ED%8C%A8%ED%84%B4/react.md), [Reflexion](./5-3-%ED%95%B5%EC%8B%AC-%EC%B6%94%EB%A1%A0-%ED%8C%A8%ED%84%B4/reflexion.md)
- 5-4 RAG: [기본 파이프라인](./5-4-retrieval-augmented-generation-rag/basic-rag-pipeline.md), [임베딩/벡터DB](./5-4-retrieval-augmented-generation-rag/embeddings-and-vector-dbs.md), [에이전틱 RAG](./5-4-retrieval-augmented-generation-rag/advanced-agentic-rag.md)
- 5-5 프롬프트/평가: [설계·최적화](./5-5-%ED%94%84%EB%A1%AC%ED%94%84%ED%8A%B8-%EC%97%94%EC%A7%80%EB%8B%88%EC%96%B4%EB%A7%81-and-%ED%8F%89%EA%B0%80/prompt-design-optimization.md), [평가/벤치마크](./5-5-%ED%94%84%EB%A1%AC%ED%94%84%ED%8A%B8-%EC%97%94%EC%A7%80%EB%8B%88%EC%96%B4%EB%A7%81-and-%ED%8F%89%EA%B0%80/prompt-evaluation-and-benchmarks.md)
- 5-6 AgentOps: [라이프사이클·운영](./5-6-agentops-%EC%9A%B4%EC%98%81-and-%EC%9E%90%EB%8F%99%ED%99%94/agent-lifecycle-ops.md), [오케스트레이션](./5-6-agentops-%EC%9A%B4%EC%98%81-and-%EC%9E%90%EB%8F%99%ED%99%94/orchestration-workflow-management.md), [평가/모니터링](./5-6-agentops-%EC%9A%B4%EC%98%81-and-%EC%9E%90%EB%8F%99%ED%99%94/evaluation-monitoring-ops.md)
- 5-7 LLM 아키텍처/최적화: [LLM 구조](./5-7-llm-%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98-and-%EC%B5%9C%EC%A0%81%ED%99%94/llm-architecture.md), [추론 최적화·서빙](./5-7-llm-%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98-and-%EC%B5%9C%EC%A0%81%ED%99%94/inference-optimization-and-serving.md), [파인튜닝/적응](./5-7-llm-%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98-and-%EC%B5%9C%EC%A0%81%ED%99%94/fine-tuning-and-adaptation.md)
- 5-8 데이터/인프라: [데이터 엔지니어링](./5-8-%EB%8D%B0%EC%9D%B4%ED%84%B0-and-%EC%9D%B8%ED%94%84%EB%9D%BC/data-engineering-for-agents.md), [CI/CD·자동화](./5-8-%EB%8D%B0%EC%9D%B4%ED%84%B0-and-%EC%9D%B8%ED%94%84%EB%9D%BC/ci-cd-and-automation.md), [인프라/툴링](./5-8-%EB%8D%B0%EC%9D%B4%ED%84%B0-and-%EC%9D%B8%ED%94%84%EB%9D%BC/infra-and-tooling.md)
- 5-9 보안/프로토콜: [보안·가드레일](./5-9-%EB%B3%B4%EC%95%88-and-%ED%94%84%EB%A1%9C%ED%86%A0%EC%BD%9C/security-and-guardrails.md), [표준 프로토콜(A2A/ACP/MCP)](./5-9-%EB%B3%B4%EC%95%88-and-%ED%94%84%EB%A1%9C%ED%86%A0%EC%BD%9C/standard-protocols-a2a-acp-mcp.md), [툴 스키마(JSON-RPC/OpenAPI)](./5-9-%EB%B3%B4%EC%95%88-and-%ED%94%84%EB%A1%9C%ED%86%A0%EC%BD%9C/tool-schemas-jsonrpc-openapi.md)
- 5-10 트렌드/생태계: [트렌드 개요](./5-10-%ED%8A%B8%EB%A0%8C%EB%93%9C-and-%EC%83%9D%ED%83%9C%EA%B3%84/trends-and-ecosystem.md), [멀티모달 에이전트](./5-10-%ED%8A%B8%EB%A0%8C%EB%93%9C-and-%EC%83%9D%ED%83%9C%EA%B3%84/multimodal-agents.md)

______________________________________________________________________

## 추천 학습 로드맵

1. 시스템 설계 → 2) 메모리/컨텍스트 → 3) 추론 패턴 → 4) RAG → 5) 프롬프트/평가 → 6) AgentOps → 7) LLM 아키텍처/최적화 → 8) 데이터/인프라 → 9) 보안/프로토콜 → 10) 트렌드.

______________________________________________________________________

## 교차 참조(핵심 연계)

- Function/Tool Calling 프롬프트 스키마: 5-5 ↔ 5-9
- LLM-as-a-Judge & T-Eval: 5-5 ↔ 5-6
- 지연/비용 최적화: 5-5 ↔ 5-6 ↔ 5-8
- 메모리·RAG 연계: 5-2 ↔ 5-4

______________________________________________________________________

## 참고

섹션별 상세 링크는 각 문서의 "더 읽어보기"와 "See also"를 참고하세요.
