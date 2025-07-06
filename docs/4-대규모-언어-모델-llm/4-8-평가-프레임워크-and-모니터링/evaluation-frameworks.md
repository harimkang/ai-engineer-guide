---
title: "LLM 평가 프레임워크: OpenAI Evals, RAGAS, TruLens"
date: "2025-07-06"
tags: ["LLM", "평가", "RAG", "MLOps"]
difficulty: "medium"
---

# LLM 평가 프레임워크: OpenAI Evals, RAGAS, TruLens

## 1. 핵심 개념 (Core Concept)

LLM(대규모 언어 모델) 및 이를 기반으로 구축된 애플리케이션의 성능을 체계적으로 테스트하고 검증하기 위한 평가 프레임워크는 필수적입니다. **OpenAI Evals**, **RAGAS**, **TruLens**는 이러한 목적을 위해 설계된 대표적인 오픈소스 도구들로, 각각 다른 초점과 방법론을 가지고 있습니다. OpenAI Evals는 일반적인 LLM 성능 평가에, RAGAS는 RAG(Retrieval-Augmented Generation) 파이프라인 평가에 특화되어 있으며, TruLens는 LLM 애플리케이션의 심층적인 평가 및 추적에 강점을 가집니다.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 OpenAI Evals

OpenAI Evals는 LLM의 특정 작업에 대한 성능을 측정하기 위한 유연하고 범용적인 프레임워크입니다. 질문 답변, 코드 생성과 같은 일반적인 사용 사례를 위한 사전 구축된 평가 레지스트리를 제공하며, 개발자가 자체 데이터를 사용하여 커스텀 평가를 생성할 수 있도록 합니다. 모델의 출력을 이상적인 답변(Ground Truth)과 비교하여 정확도와 같은 지표를 계산하거나, 다른 LLM을 사용하여 생성된 응답의 품질을 평가하는 모델-기반 평가(Model-graded evaluations)도 지원합니다.

### 2.2 RAGAS (Retrieval-Augmented Generation Assessment Suite)

RAGAS는 RAG(Retrieval-Augmented Generation) 파이프라인 평가에 특화된 프레임워크입니다. RAG 모델은 검색(Retrieval)과 생성(Generation)이라는 두 가지 주요 구성 요소를 가지므로, RAGAS는 이 둘을 모두 평가하기 위한 다양한 지표를 제공합니다. 주요 지표로는 `faithfulness`(생성된 답변이 검색된 컨텍스트와 사실적으로 일치하는지), `answer_relevancy`(답변이 질문과 관련성이 있는지), `context_precision`(검색된 컨텍스트가 질문과 관련성이 있는지), `context_recall`(질문에 필요한 모든 정보가 컨텍스트에 포함되었는지) 등이 있습니다. RAGAS의 독특한 기능 중 하나는 문서에서 합성 테스트 데이터(질문 및 Ground Truth 답변)를 생성할 수 있다는 점입니다.

### 2.3 TruLens

TruLens는 LLM 기반 애플리케이션의 평가 및 추적을 위한 오픈소스 도구로, 특히 RAG 시스템에 대한 강력한 지원을 제공합니다. "피드백 함수(Feedback Functions)"를 사용하여 LLM 체인의 입력, 출력 및 중간 결과를 프로그래밍 방식으로 평가함으로써 심층적인 계측(Instrumentation) 및 설명 가능성(Explainability)을 강조합니다. TruLens는 RAG 애플리케이션을 위한 세 가지 중요한 평가 지표인 "RAG Triad"로 잘 알려져 있습니다: `Context Relevance`(컨텍스트 관련성), `Groundedness`(사실 기반 여부, faithfulness와 유사), `Answer Relevance`(답변 관련성). 또한 실험을 추적하고, 애플리케이션의 다른 버전을 비교하며, 성능 문제를 디버깅하기 위한 대시보드를 제공합니다.

---

## 3. 예시 (Example)

### 평가 프레임워크 비교 (Table)

| 기능/특징 | OpenAI Evals | RAGAS | TruLens |
| :--- | :--- | :--- | :--- |
| **주요 초점** | 정의된 작업에 대한 일반 LLM 성능 | RAG 파이프라인의 End-to-End 평가 | LLM 앱의 실험 추적 및 심층 평가 (특히 RAG) |
| **이상적인 사용 사례** | 다양한 모델 벤치마킹, 파인튜닝 검증, CI/CD 품질 보증 | RAG 시스템의 검색 및 생성 구성 요소 최적화 | LLM 애플리케이션의 반복 개발, 디버깅 및 품질 이해/개선 |
| **주요 지표** | `match`, `includes`, `fuzzy_match`, 모델-기반 분류 | `faithfulness`, `answer_relevancy`, `context_precision`, `context_recall` | "RAG Triad": `context_relevance`, `groundedness`, `answer_relevance`, 커스텀 피드백 함수 |
| **워크플로우** | YAML 파일에 평가 정의, 데이터셋 준비(JSONL), CLI 또는 API를 통해 실행 | 질문, 답변, 컨텍스트가 포함된 데이터셋 준비; 평가 실행하여 각 지표 점수 획득 | 피드백 함수를 사용하여 LLM 애플리케이션을 TruLens 계측으로 래핑하여 실험 로깅 및 평가 |
| **Ground Truth 필요성** | 프롬프트 및 이상적인 완성 데이터셋 필요 | Ground Truth 없이도 작동 가능하나, 일부 지표는 필요 | Ground Truth 없이도 평가 가능, LLM 기반 피드백 활용 |
| **고유 기능** | 광범위한 작업을 위한 사전 구축된 벤치마크 레지스트리 | 문서에서 테스트 데이터셋(질문/답변) 직접 합성 가능 | 실험 추적 및 평가 결과 시각화를 위한 상세 대시보드 제공 |

### 코드 예시 (Python - RAGAS)

다음은 RAGAS를 사용하여 RAG 파이프라인을 평가하는 간단한 예시입니다.

```python
from datasets import Dataset
from ragas import evaluate
from ragas.metrics import faithfulness, answer_relevancy, context_recall

# 예시 데이터셋 (실제로는 RAG 파이프라인의 출력)
data = {
    'question': ["What is the capital of France?", "Who painted the Mona Lisa?"],
    'answer': ["Paris is the capital of France.", "Leonardo da Vinci painted the Mona Lisa."],
    'contexts': [["Paris is a major European city."], ["Leonardo da Vinci was an Italian polymath."]],
    'ground_truths': [["Paris"], ["Leonardo da Vinci"]]
}

dataset = Dataset.from_dict(data)

# 평가 실행
result = evaluate(
    dataset,
    metrics=[
        faithfulness,
        answer_relevancy,
        context_recall,
    ],
)

print(result)
```

### 사용 사례 (Use Case)

*   **모델 벤치마킹**: 새로운 LLM 모델이나 파인튜닝된 모델의 성능을 기존 모델과 비교 평가합니다.
*   **RAG 시스템 최적화**: 검색(Retrieval) 단계의 효율성이나 생성(Generation)된 답변의 정확도를 개선하기 위해 RAGAS를 사용하여 병목 지점을 식별합니다.
*   **지속적인 통합/배포 (CI/CD)**: LLM 애플리케이션의 코드 변경이 성능 저하를 일으키지 않도록 CI/CD 파이프라인에 평가 프레임워크를 통합합니다.
*   **애플리케이션 디버깅**: TruLens 대시보드를 통해 LLM 애플리케이션의 동작을 시각적으로 분석하고, 예상치 못한 결과의 원인을 파악합니다.

---

## 4. 예상 면접 질문 (Potential Interview Questions)

*   **Q. LLM 평가 프레임워크를 사용하는 주된 이유는 무엇인가요?**
    *   **A.** LLM은 비결정적(non-deterministic) 특성을 가지며, 다양한 입력에 대해 복잡한 출력을 생성합니다. 따라서 전통적인 소프트웨어 테스트 방식만으로는 품질을 보장하기 어렵습니다. 평가 프레임워크는 LLM의 성능을 정량적으로 측정하고, 특정 사용 사례에 대한 적합성을 검증하며, 모델 개선 방향을 제시하는 데 필수적입니다. 또한, 지속적인 통합/배포(CI/CD) 환경에서 모델의 회귀(regression)를 방지하고 품질을 유지하는 데 기여합니다.
*   **Q. RAGAS가 일반적인 LLM 평가 프레임워크와 다른 점은 무엇이며, 어떤 지표를 주로 사용하나요?**
    *   **A.** RAGAS는 RAG(Retrieval-Augmented Generation) 시스템에 특화된 평가 프레임워크입니다. 일반적인 LLM 평가 프레임워크가 모델의 전반적인 생성 능력에 초점을 맞춘다면, RAGAS는 RAG 시스템의 핵심 구성 요소인 검색(Retrieval)과 생성(Generation)의 품질을 분리하여 평가합니다. 주요 지표로는 `faithfulness`(생성된 답변이 검색된 컨텍스트에 얼마나 충실한지), `answer_relevancy`(답변이 질문과 얼마나 관련성이 있는지), `context_precision`(검색된 컨텍스트가 질문에 얼마나 정확한지), `context_recall`(질문에 필요한 정보가 컨텍스트에 얼마나 잘 포함되어 있는지) 등이 있습니다.
*   **Q. TruLens의 "피드백 함수"는 무엇이며, LLM 애플리케이션 디버깅에 어떻게 활용될 수 있나요?**
    *   **A.** TruLens의 피드백 함수는 LLM 애플리케이션의 입력, 출력, 그리고 중간 단계의 결과를 프로그래밍 방식으로 평가하는 사용자 정의 가능한 함수입니다. 예를 들어, 답변의 유해성, 특정 키워드 포함 여부, 또는 답변의 길이 등을 평가하는 피드백 함수를 정의할 수 있습니다. 이를 통해 개발자는 LLM 체인의 각 단계에서 어떤 문제가 발생하는지 정확히 파악하고, 대시보드를 통해 시각적으로 분석하여 성능 저하의 원인을 신속하게 디버깅하고 개선할 수 있습니다.

---

## 5. 더 읽어보기 (Further Reading)

*   [OpenAI Evals GitHub 저장소](https://github.com/openai/evals)
*   [RAGAS 공식 문서](https://docs.ragas.io/en/latest/)
*   [TruLens 공식 문서](https://trulens.org/)