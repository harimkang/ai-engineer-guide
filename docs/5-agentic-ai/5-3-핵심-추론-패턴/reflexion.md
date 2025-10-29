---
title: "Reflexion (자기 반성·자기 수정 에이전트)"
date: "2025-10-29"
tags: ["Agentic AI", "Reflection", "Self-Critique"]
difficulty: "medium"
---

# Reflexion

## 1. 핵심 개념 (Core Concept)

에이전트가 자신의 시도를 평가·메모하고, 피드백을 반영해 다음 시도를 개선하는 루프를 형성합니다. 사전 정의된 실패 타입 카탈로그와 교정 전략을 연결해 재시도 품질을 체계적으로 끌어올립니다.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 실패 타입 기록과 전략 업데이트
- 실패 카탈로그 예시: 형식 불일치, 환각, 불충분 근거, 도구 실패, 시간 초과
- 메모리 기입: {문제, 시도 요약, 실패 타입, 교정 제안, 다음 시도 프롬프트}
- 전략 업데이트: 프롬프트 제약 강화, 도구 순서 변경, 임계값 상향, 예산 재분배

### 2.2 외부 평가자(LLM-as-a-Judge)와의 결합
- 산출물에 대한 품질 점수/피드백을 자동 생성 → 교정 프롬프트에 삽입
- 바이어스 완화: 다중 심사/다수결/메타평가

### 2.3 의사코드
```python
def reflexion(task, max_trials=3):
    memo = []
    for t in range(max_trials):
        out = agent_attempt(task, memo)
        score, fb = judge(out)
        if score >= pass_mark:
            return out
        memo.append({"trial": t, "summary": summarize(out), "feedback": fb})
        task = revise_task(task, fb)
    return best_of(memo)
```

---

## 3. 예시 (Example)

- 툴 실패 로그를 바탕으로 재시도 시 프롬프트·파라미터 변경.
- 형식 오류: “오직 JSON으로만, 스키마 준수, 불필요 텍스트 금지”를 강화.
- 근거 부족: “출처 ID를 2개 이상 인용” 제약 추가.

---

## 4. 예상 면접 질문 (Potential Interview Questions)

- Reflexion과 단순 재시도의 차이는?
- 실패 카탈로그를 어떻게 정의·유지·학습에 반영하는가?
- 과도한 재시도 루프를 어떻게 억제하는가?(예산/정지 조건)

---

## 5. 더 읽어보기 (Further Reading)

- docs/references/anthropic/building-effective-agents.md
- docs/references/openai/a-practical-guide-to-building-agents-3.pdf

---

## 6. See also

- 평가/벤치마크: 5-5 → [prompt-evaluation-and-benchmarks](../5-5-프롬프트-엔지니어링-and-평가/prompt-evaluation-and-benchmarks.md)
- 운영/모니터링: 5-6 → [evaluation-monitoring-ops](../5-6-agentops-운영-and-자동화/evaluation-monitoring-ops.md)
