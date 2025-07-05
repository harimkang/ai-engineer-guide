---
title: "Sequence-to-Sequence & Text-to-Text (T5, mT5)"
date: "2025-07-05"
tags: ["LLM", "Pre-training", "Seq2Seq", "T5"]
difficulty: "medium"
---

# Sequence-to-Sequence & Text-to-Text (T5, mT5)

## 1. 핵심 개념 (Core Concept)

시퀀스-투-시퀀스(Sequence-to-Sequence, Seq2Seq)는 하나의 시퀀스를 입력받아 다른 시퀀스를 출력하는 모델 구조로, 주로 인코더-디코더 아키텍처를 사용합니다. 구글의 T5(Text-to-Text Transfer Transformer)는 이 개념을 극대화하여, **모든 NLP 문제를 "텍스트를 입력받아 텍스트를 출력하는" 단일한 프레임워크로 통합**한 획기적인 모델입니다.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 T5: "모든 것은 Text-to-Text 문제"

T5의 핵심 아이디어는 모든 NLP 태스크를 동일한 형식으로 변환하는 것입니다. 예를 들어,

*   **번역**: `"translate English to German: That is good."` -> `"Das ist gut."`
*   **요약**: `"summarize: state authorities dispatched ..."` -> `"state authorities sent emergency ..."`
*   **분류**: `"cola sentence: The course is jumping well."` -> `"not acceptable"` (문법성 판단)
*   **회귀**: `"stsb sentence1: ... sentence2: ..."` -> `"3.8"` (문장 유사도 점수)

이처럼 태스크에 맞는 **접두사(prefix)**를 입력 텍스트에 추가함으로써, 동일한 모델이 전혀 다른 종류의 작업을 수행하도록 지시할 수 있습니다. 이 접근법은 모델 구조나 학습 과정을 태스크마다 바꿀 필요가 없어 연구와 개발을 매우 단순화시켰습니다.

### 2.2 사전학습 목표: Span Corruption

T5는 BERT의 Masked LM과 유사하지만 다른, **Span Corruption**이라는 목표로 사전학습됩니다.

1.  **Span Masking**: 입력 텍스트에서 연속된 토큰 덩어리(span)를 하나의 마스크 토큰(e.g., `<X>`)으로 대체합니다.
2.  **Reconstruction**: 디코더가 마스크 토큰에 해당하는 원래의 텍스트 덩어리를 순서대로 생성하도록 학습합니다.

```
# 예시

Original: Thank you for inviting me to your party last week.

Input: Thank you <X> me to your party <Y> week.
Target: for inviting <X> last <Y>
```

이 방식은 BERT처럼 양방향 문맥을 학습하면서도, 출력으로 가변적인 길이의 텍스트를 생성하는 Seq2Seq 구조에 더 적합합니다.

### 2.3 mT5: T5의 다국어 확장

mT5(multilingual T5)는 T5의 성공을 다국어 환경으로 확장한 모델입니다. 101개의 언어를 포함하는 `mC4` 데이터셋으로 사전학습되었으며, T5와 거의 동일한 구조와 학습 방식을 따릅니다. 이를 통해 단일 모델로 다양한 언어에 대한 NLP 태스크를 수행할 수 있게 되었습니다.

---

## 3. 아키텍처: 표준 인코더-디코더

T5와 mT5는 Causal LM(디코더-온리)이나 Masked LM(인코더-온리)과 달리, **트랜스포머의 인코더와 디코더를 모두 사용하는** 표준적인 Seq2Seq 아키텍처를 가집니다.

*   **인코더**: 입력 시퀀스 전체(예: `"translate English to German: ..."`)를 양방향으로 처리하여 문맥적 표현(contextual representation)을 생성합니다.
*   **디코더**: 인코더의 출력과 이전에 자신이 생성한 출력 토큰들을 입력받아, 다음 출력 토큰을 자기회귀적(auto-regressive)으로 생성합니다.

---

## 4. 예상 면접 질문 (Potential Interview Questions)

*   **Q. T5의 "Text-to-Text" 프레임워크가 기존 방식에 비해 갖는 장점은 무엇인가요?**
    *   **A.** 가장 큰 장점은 **통일성과 단순성**입니다. 번역, 요약, 분류 등 각기 다른 태스크를 위해 별도의 모델 구조나 학습 방식을 설계할 필요 없이, 모든 것을 "텍스트 입/출력" 문제로 통일했습니다. 이로 인해 다양한 태스크에 동일한 모델, 하이퍼파라미터, 손실 함수를 적용할 수 있어 개발 및 실험 과정이 매우 간결하고 효율적이게 됩니다.

*   **Q. T5의 사전학습 방식(Span Corruption)은 BERT의 MLM과 어떻게 다른가요?**
    *   **A.** 두 방식 모두 양방향 문맥을 학습하지만, 예측 대상과 방식이 다릅니다. MLM은 각 `[MASK]` 토큰의 위치에서 **단일 토큰**을 예측하는 분류 문제에 가깝습니다. 반면, Span Corruption은 여러 토큰으로 구성된 **연속된 스팬(span)**을 예측하는 생성 문제에 가깝습니다. 또한, T5는 여러 스팬을 하나의 마스크 토큰으로 대체하고 디코더가 이를 순서대로 생성하게 함으로써, 더 긴 텍스트를 생성하는 능력을 효과적으로 학습합니다.

*   **Q. T5와 같은 인코더-디코더 모델이 GPT(디코더-온리)나 BERT(인코더-온리)와 비교했을 때, 어떤 종류의 태스크에 가장 적합한가요?**
    *   **A.** T5는 입력 시퀀스를 완전히 새로운 시퀀스로 변환하는 **생성 기반의 변환(generative transformation)** 태스크에 가장 적합합니다. 대표적으로 **기계 번역(Machine Translation)**과 **텍스트 요약(Summarization)**이 있습니다. 입력의 의미를 깊게 이해(인코더)하고, 이를 바탕으로 유창한 새로운 문장(디코더)을 만들어내야 하기 때문입니다. 반면, 순수 생성에는 GPT가, 문맥 이해 기반의 분류/추출에는 BERT가 더 특화되어 있다고 볼 수 있습니다.

---

## 5. 더 읽어보기 (Further Reading)

*   [Exploring the Limits of Transfer Learning with a Unified Text-to-Text Transformer (T5 Paper)](https://arxiv.org/abs/1910.10683)
*   [mT5: A Massively Multilingual Pre-trained Text-to-Text Transformer (mT5 Paper)](https://arxiv.org/abs/2010.11934)
*   [Hugging Face Blog: The T5 Model](https://huggingface.co/blog/t5)