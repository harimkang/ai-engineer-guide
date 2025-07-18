---
title: "트랜스포머 아키텍처: 인코더-디코더 vs. 디코더-온리"
date: "2025-07-03"
tags: ["딥러닝", "트랜스포머", "아키텍처", "인코더-디코더", "디코더-온리", "LLM"]
difficulty: "medium"
---

# 트랜스포머 아키텍처: 인코더-디코더 vs. 디코더-온리

## 1. 핵심 개념 (Core Concept)

트랜스포머는 문제의 종류와 목적에 따라 크게 두 가지 주요 아키텍처로 나뉨. **인코더-디코더(Encoder-Decoder)** 구조는 입력 시퀀스를 이해하고 이를 바탕으로 새로운 출력 시퀀스를 생성하는 데 특화되어 있으며(예: 번역), **디코더-온리(Decoder-Only)** 구조는 주어진 컨텍스트(prompt)를 바탕으로 다음 단어를 예측하며 텍스트를 생성하는 데 최적화되어 있음(예: GPT). 이 두 구조의 선택은 모델이 수행할 핵심 태스크에 따라 결정됨.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 인코더-디코더 (Encoder-Decoder) 아키텍처

오리지널 트랜스포머 논문("Attention Is All You Need")에서 제안된 구조로, 두 개의 주요 부분으로 구성됨.

*   **인코더 (Encoder)**:
    *   **역할**: 입력 시퀀스 전체를 읽고, 각 단어의 문맥적 의미를 이해하여 풍부한 정보가 담긴 표현(representation)을 생성함.
    *   **어텐션**: **양방향(Bi-directional) 어텐션**을 사용함. 즉, 각 단어는 시퀀스 내의 모든 다른 단어(앞, 뒤 모두)와 상호작용하여 문맥을 파악함.
    *   **출력**: 입력 시퀀스와 동일한 길이의 문맥화된 벡터 시퀀스(contextualized embeddings)를 생성함.

*   **디코더 (Decoder)**:
    *   **역할**: 인코더의 출력(문맥 벡터)과 이전에 생성된 출력 단어들을 바탕으로, 다음 단어를 순차적으로 예측하여 새로운 시퀀스를 생성함.
    *   **어텐션**: 두 종류의 어텐션을 사용함.
        1.  **마스크된 자기-어텐션 (Masked Self-Attention)**: 디코더 내부에서 이미 생성된 단어들 간의 관계만 보도록 하여, 미래의 정답을 미리 엿보는 것을 방지함 (**단방향(Uni-directional) 어텐션**).
        2.  **교차-어텐션 (Cross-Attention)**: 디코더의 현재 상태와 인코더의 전체 출력 사이의 관계를 계산하여, 입력 시퀀스의 어떤 부분에 집중해야 할지 결정함.

*   **주요 사용처**: 기계 번역(T5, BART), 텍스트 요약 등 입력과 출력이 명확히 구분되는 **Sequence-to-Sequence** 태스크.

### 2.2 디코더-온리 (Decoder-Only) 아키텍처

GPT(Generative Pre-trained Transformer) 시리즈로 대표되는 구조로, 이름처럼 트랜스포머의 디코더 부분만을 사용함.

*   **구조**: 인코더와 교차-어텐션 부분이 없음. 오직 마스크된 자기-어텐션만을 사용하여 텍스트를 생성함.
*   **작동 방식**: 입력으로 주어진 텍스트(프롬프트)를 바탕으로, 다음에 올 단어를 순차적으로 예측하는 과정을 반복함. 생성된 단어는 다음 예측을 위한 새로운 입력의 일부가 됨.
*   **어텐션**: **단방향(Uni-directional) 또는 인과적(Causal) 어텐션**을 사용함. 각 단어는 예측 시점에서 자기 자신과 그 이전에 나온 단어들만 참조할 수 있음.
*   **주요 사용처**: 텍스트 생성, 대화형 AI, 질의응답 등 **언어 모델링(Language Modeling)** 자체에 중점을 둔 태스크. (GPT, LLaMA, PaLM 등 대부분의 LLM)

---

## 3. 비교 요약 (Comparison Table)

| 특징 | 인코더-디코더 (Encoder-Decoder) | 디코더-온리 (Decoder-Only) |
| :--- | :--- | :--- |
| **주요 구성** | 인코더 + 디코더 | 디코더만 사용 |
| **핵심 태스크** | 시퀀스 변환 (번역, 요약) | 텍스트 생성 (언어 모델링) |
| **어텐션 종류** | 인코더: 양방향<br>디코더: 단방향 + 교차-어텐션 | 단방향 (인과적) 어텐션 |
| **정보 흐름** | 입력 전체 이해 후 출력 생성 | 이전 컨텍스트 기반으로 다음 토큰 예측 |
| **대표 모델** | T5, BART, mBART | GPT, LLaMA, PaLM, BLOOM |

---

## 4. 예상 면접 질문 (Potential Interview Questions)

*   **Q. 기계 번역 태스크에 디코더-온리 모델 대신 인코더-디코더 모델을 사용하는 이유는 무엇인가요?**
    *   **A.** 기계 번역은 소스 언어(입력)의 전체 문맥을 완벽하게 이해한 후, 이를 타겟 언어(출력)로 변환해야 하는 태스크입니다. 인코더-디코더 구조에서 인코더는 양방향 어텐션을 통해 소스 문장 전체의 복잡한 문법 구조와 의미를 온전히 파악하는 역할을 합니다. 디코더는 이렇게 잘 요약된 정보(인코더 출력)를 교차-어텐션을 통해 적극적으로 참조하면서 번역을 생성하므로, 문맥을 놓치지 않는 고품질 번역이 가능합니다. 디코더-온리 모델은 주로 생성에 초점이 맞춰져 있어, 소스 문장의 전체적인 이해보다는 주어진 프롬프트에 이어 자연스러운 문장을 만드는 데 더 강점이 있습니다.

*   **Q. GPT와 같은 LLM이 디코더-온리 아키텍처를 주로 채택하는 이유는 무엇인가요?**
    *   **A.** LLM의 핵심 목표는 주어진 텍스트(프롬프트)를 이해하고, 그에 따라 인간과 유사한 자연스러운 텍스트를 생성하는 것입니다. 이는 본질적으로 '다음에 올 단어 예측'이라는 언어 모델링 태스크의 연속입니다. 디코더-온리 구조의 단방향(인과적) 어텐션은 바로 이 태스크에 최적화되어 있습니다. 이전 단어들을 바탕으로 다음 단어를 예측하는 과정을 반복하며 일관성 있는 긴 글을 생성할 수 있기 때문입니다. 별도의 인코더 없이도 방대한 데이터 학습을 통해 문맥 이해와 생성을 동시에 수행할 수 있어, 범용적인 언어 능력을 갖추는 데 매우 효율적입니다.

*   **Q. 인코더-디코더 모델의 교차-어텐션(Cross-Attention)은 어떤 역할을 하나요?**
    *   **A.** 교차-어텐션은 디코더가 인코더의 정보를 "참조"하는 다리 역할을 합니다. 디코더가 각 타임 스텝에서 새로운 단어를 생성할 때, 교차-어텐션은 디코더의 현재 상태를 쿼리(Query)로, 인코더의 전체 출력 시퀀스를 키(Key)와 밸류(Value)로 사용하여, 입력 문장의 어떤 부분에 가장 집중해야 할지를 결정합니다. 예를 들어, 번역 모델이 문장의 주어를 생성할 때, 교차-어텐션은 소스 문장의 주어 부분에 높은 가중치를 부여하게 됩니다. 이를 통해 디코더는 입력 시퀀스의 관련 정보를 효과적으로 활용하여 정확한 출력을 생성할 수 있습니다.

---

## 5. 더 읽어보기 (Further Reading)

*   [Attention Is All You Need (Vaswani et al., 2017)](https://arxiv.org/abs/1706.03762) - 오리지널 트랜스포머 논문
*   [The Illustrated Transformer (Jay Alammar)](http://jalammar.github.io/illustrated-transformer/)
*   [Hugging Face: Encoder-decoder models](https://huggingface.co/docs/transformers/model_doc/encoder-decoder)