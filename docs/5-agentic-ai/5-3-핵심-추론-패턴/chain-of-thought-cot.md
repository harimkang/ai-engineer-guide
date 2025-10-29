---
title: "생각의 사슬 (Chain-of-Thought, CoT)"
date: "2025-10-29"
tags: ["Agentic AI", "Reasoning", "CoT"]
difficulty: "medium"
---

# Chain-of-Thought (CoT)

## 1. 핵심 개념 (Core Concept)

문제 해결을 중간 추론 단계로 분해하여 해설형 출력을 유도, 정답률과 투명성을 높입니다. Self-Consistency(다중 시도 샘플링 후 다수결)와 결합하면 복잡한 추론 문제의 견고성이 향상됩니다.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 CoT 템플릿 설계
- 구조: 문제 재진술 → 핵심 정보 추출 → 단계별 계산/논증 → 최종 결론
- 지시: “단계를 번호로 나열하고, 각 단계의 근거를 간결히 설명하라.”
- 출력: 최종 답을 별도로 명시(answer: ...)

### 2.2 Self-Consistency(자기 일관성)
- 동일 문제를 temperature>0로 N번 생성 → 최종 답 다수결 → 중간 추론은 요약 저장
- 이점: 단일 경로 오류를 완화, 특히 수학/논증 문제에 효과적

### 2.3 정답 유도 vs 과도한 노출(보안·프라이버시)
- 외부 노출이 불필요한 내부 사유는 압축 요약으로 대체(“간단한 근거 요약”)
- 민감 정보 포함 위험 시, 근거는 인용 ID/출처만 남기는 방식을 권장

---

## 3. 예시 (Example)

### 3.1 수학 문제 CoT
문제: 한 상자에 사과가 3개씩 담겨 있다. 7상자에 사과는 몇 개인가?
추론:
1) 한 상자=3개  2) 7상자=3×7=21  3) 정답=21
정답: 21

### 3.2 Self-Consistency 의사코드
```python
def cot_answer(q, N=5):
    answers = []
    for _ in range(N):
        steps = llm.generate_cot(q, temperature=0.7)
        ans = extract_final(steps)
        answers.append(ans)
    return majority_vote(answers)
```

---

## 4. 예상 면접 질문 (Potential Interview Questions)

- CoT가 언제 역효과를 낼 수 있는가?
- Self-Consistency의 비용·지연을 줄이는 방법은?
- CoT와 ReAct/ToT의 선택 기준은?

---

## 5. 더 읽어보기 (Further Reading)

- docs/references/google/Prompt Engineering_v7.pdf
- docs/references/openai/a-practical-guide-to-building-agents-3.pdf
- docs/references/anthropic/building-effective-agents.md

---

## 6. See also

- 평가/벤치마크: 5-5 → [prompt-evaluation-and-benchmarks](../5-5-프롬프트-엔지니어링-and-평가/prompt-evaluation-and-benchmarks.md)
- 루프/운영: 5-1 → [core-loop-rpa](../5-1-시스템-설계/core-loop-rpa.md)
