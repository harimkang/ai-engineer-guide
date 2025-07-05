---
title: "Masked LM (BERT 계열)"
date: "2025-07-05"
tags: ["LLM", "Pre-training", "MLM", "BERT"]
difficulty: "easy"
---

# Masked LM (BERT 계열)

## 1. 핵심 개념 (Core Concept)

마스크 언어 모델링(Masked Language Modeling, MLM)은 문장의 일부 토큰을 `[MASK]`라는 특수 토큰으로 무작위로 가린 뒤, 주변의 **양방향 문맥(bidirectional context)**을 모두 이용하여 원래 토큰이 무엇이었는지를 예측하도록 모델을 학습시키는 사전학습 목표입니다. 이는 BERT 계열 모델의 핵심으로, 모델이 단어의 의미를 문맥 속에서 깊이 이해하는 능력을 길러줍니다.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 학습 방식: 빈칸 채우기 문제 풀이

MLM은 마치 영어 시험의 빈칸 채우기 문제와 같습니다. 모델은 문장 전체를 보고 빈칸에 들어갈 가장 적절한 단어를 맞추는 훈련을 합니다.

*   **Original**: "The quick brown fox jumps over the lazy dog"
*   **Masked**: "The quick brown [MASK] jumps over the lazy dog"
*   **Prediction**: 모델은 `[MASK]` 위치에 "fox"가 와야 한다는 것을 예측해야 합니다.

이 과정을 통해 모델은 특정 단어의 의미를 파악하기 위해 그 단어의 앞(left context)과 뒤(right context) 정보를 모두 활용하는 방법을 배우게 됩니다. 이는 Causal LM이 오직 앞 단어들만 보는 것과의 근본적인 차이점입니다.

**BERT의 마스킹 전략:**
실제 BERT에서는 단순 `[MASK]` 치환 외에 더 정교한 전략을 사용합니다. 입력 토큰 중 15%를 예측 대상으로 삼되, 다음과 같이 처리합니다.
*   **80%**: 진짜 `[MASK]` 토큰으로 바꿉니다. (e.g., `fox` -> `[MASK]`)
*   **10%**: 어휘사전(vocabulary)의 다른 무작위 단어로 바꿉니다. (e.g., `fox` -> `cat`)
*   **10%**: 원래 단어를 그대로 둡니다. (e.g., `fox` -> `fox`)

이러한 전략은 모델이 사전학습(pre-training) 단계와 파인튜닝(fine-tuning) 단계 사이의 불일치를 줄이고, 모든 입력 토큰에 대해 풍부한 문맥적 표현(contextual representation)을 학습하도록 강제하는 효과가 있습니다.

### 2.2 아키텍처: Transformer Encoder-Only

MLM은 주로 **트랜스포머의 인코더(Encoder) 블록**만을 쌓아서 만듭니다. 인코더의 Self-Attention은 마스크 없이 모든 토큰이 서로 상호작용하므로, 양방향 문맥을 자연스럽게 처리할 수 있습니다. BERT, RoBERTa, ALBERT 등이 대표적인 인코더-온리(Encoder-Only) 모델입니다.

---

## 3. 예시 (Example)

### 문맥적 의미 이해 (Contextual Understanding)

MLM의 힘은 다의어(polysemous word)의 의미를 구분하는 데서 명확히 드러납니다.

*   **Sentence 1**: "I went to the **bank** to deposit money."
*   **Sentence 2**: "The river **bank** was muddy after the rain."

두 문장에서 "bank"는 다른 의미를 가집니다. MLM으로 학습된 모델은 `[MASK]`된 "bank"를 예측하기 위해 "deposit money"나 "river", "rain" 같은 주변 단어를 모두 참고합니다. 그 결과, 각 문장의 문맥에 맞는, 즉 서로 다른 벡터 표현(vector representation)을 "bank"에 부여할 수 있게 됩니다.

---

## 4. 예상 면접 질문 (Potential Interview Questions)

*   **Q. Masked LM이 Causal LM에 비해 갖는 장점은 무엇인가요? 어떤 태스크에 더 유리한가요?**
    *   **A.** Masked LM의 가장 큰 장점은 **깊은 양방향 문맥 이해 능력**입니다. 문장의 앞뒤 정보를 모두 활용하기 때문에, 문장의 전체적인 의미를 파악해야 하는 자연어 이해(NLU) 태스크에서 매우 강력한 성능을 보입니다. 구체적으로는 감성 분석, 질의응답(QA), 문장 관계 추론(NLI), 개체명 인식(NER)과 같은 분류(classification)나 추출(extraction) 기반의 태스크에 더 유리합니다.

*   **Q. BERT의 MLM에서 80%만 `[MASK]` 토큰으로 바꾸고, 20%는 다른 처리를 하는 이유는 무엇인가요?**
    *   **A.** 이는 사전학습과 파인튜닝 단계의 **불일치(mismatch)를 완화**하기 위함입니다. 만약 100% `[MASK]` 토큰으로만 학습하면, 모델은 실제 문장에는 존재하지 않는 `[MASK]` 토큰을 해결하는 데만 특화될 수 있습니다. 10%를 무작위 단어로 바꾸면 모델이 입력 단어가 진짜인지 가짜인지까지 판단하는 능력을 배우게 되고, 10%를 원래 단어 그대로 두면 모델이 모든 단어에 대해 올바른 표현을 학습하도록 강제하는 효과가 있습니다.

*   **Q. Masked LM 기반의 모델(BERT)은 텍스트 생성에 직접적으로 사용하기 어려운 이유는 무엇인가요?**
    *   **A.** 두 가지 주요 이유가 있습니다. 첫째, MLM은 입력 문장의 일부만 예측하도록 학습되어, 문장 전체를 순차적으로 생성하는 방법을 배우지 않았습니다. 둘째, 학습 시 입력 문장에 인위적인 `[MASK]` 토큰이 존재하지만, 실제 생성 시에는 어떤 부분을 마스킹해야 할지 알 수 없는 불일치 문제가 발생합니다. 따라서 자연스러운 문장을 처음부터 끝까지 생성하는 데는 부적합합니다.

---

## 5. 더 읽어보기 (Further Reading)

*   [BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding (BERT Paper)](https://arxiv.org/abs/1810.04805)
*   [The Illustrated BERT (Jay Alammar)](http://jalammar.github.io/illustrated-bert/)
*   [Hugging Face Docs: Masked Language Modeling](https://huggingface.co/docs/transformers/tasks/language_modeling#masked-language-modeling)