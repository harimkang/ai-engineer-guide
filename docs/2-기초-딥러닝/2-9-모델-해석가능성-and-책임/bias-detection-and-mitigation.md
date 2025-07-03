---
title: "Bias 탐지 & 완화 기법"
date: "2025-07-03"
tags: ["딥러닝", "XAI", "AI 윤리", "공정성"]
difficulty: "medium"
---

# Bias 탐지 & 완화 기법

## 1. 핵심 개념 (Core Concept)

AI 모델의 편향(Bias) 탐지 및 완화는 모델이 학습 데이터에 내재된 사회적, 통계적 편향을 학습하여 특정 인구 집단에 불공정한 예측을 하는 것을 방지하는 기술입니다. 모델의 예측 결과가 성별, 인종, 연령 등 민감한 특성(Sensitive Attribute)에 영향을 받지 않도록 편향을 정량적으로 측정하고, 이를 완화하여 모델의 공정성(Fairness)과 신뢰성을 확보하는 것을 목표로 합니다.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 Bias의 종류

*   **데이터 편향 (Data Bias)**: 데이터 수집 및 처리 과정에서 발생하는 편향입니다.
    *   **표본 편향 (Sample Bias)**: 특정 그룹의 데이터만 과도하게 수집되거나 누락되어 현실 세계를 제대로 대표하지 못하는 경우입니다.
    *   **선택 편향 (Selection Bias)**: 데이터 선택 과정 자체에 편향이 개입되는 경우입니다. (예: 특정 지역 사용자 데이터만 분석)
    *   **사회적 편향 (Societal Bias)**: 사회에 만연한 고정관념이나 차별이 데이터에 그대로 반영된 경우입니다. (예: 특정 성별은 특정 직업군에만 연관되는 데이터)

*   **알고리즘 편향 (Algorithmic Bias)**: 모델 자체의 구조나 최적화 목표로 인해 발생하는 편향입니다. 단순히 정확도(Accuracy)만 높이도록 설계된 모델은 소수 집단에 대한 오류를 무시하는 경향이 생길 수 있습니다.

### 2.2 Bias 탐지 기법 (공정성 지표)

모델의 공정성은 다양한 통계적 지표로 측정할 수 있으며, 어떤 지표를 사용할지는 문제 상황과 공정성의 정의에 따라 달라집니다.

| 지표 (Metric) | 설명 | 수식 (그룹 A, B) |
| :--- | :--- | :--- |
| **Demographic Parity** | 그룹 A와 그룹 B가 긍정적인 예측을 받을 확률이 동일해야 함 | P(Ŷ=1\|G=A) = P(Ŷ=1\|G=B) |
| **Equal Opportunity** | 실제 값이 긍정적인 경우, 그룹 A와 B가 긍정적으로 예측될 확률(TPR)이 동일해야 함 | P(Ŷ=1\|Y=1, G=A) = P(Ŷ=1\|Y=1, G=B) |
| **Equalized Odds** | Equal Opportunity 조건과 더불어, 실제 값이 부정적인 경우에도 그룹 A와 B가 긍정적으로 예측될 확률(FPR)이 동일해야 함 | P(Ŷ=1\|Y=y, G=A) = P(Ŷ=1\|Y=y, G=B) for y in {0, 1} |

### 2.3 Bias 완화 기법

Bias 완화 기법은 데이터 처리, 모델 학습, 후처리 단계에 적용할 수 있습니다.

*   **전처리 (Pre-processing)**: 모델 학습 전 데이터셋을 수정합니다.
    *   **재가중 (Reweighing)**: 소수 집단에 속한 데이터 샘플에 더 높은 가중치를 부여하여 학습 시 더 큰 영향을 주도록 합니다.
    *   **샘플링 (Sampling)**: 소수 집단 데이터를 늘리거나(Oversampling) 다수 집단 데이터를 줄여서(Undersampling) 그룹 간 데이터 불균형을 해소합니다.
    *   **데이터 변환**: 민감한 정보와 상관관계를 갖는 특성을 제거하거나 변환합니다.

*   **학습 중 처리 (In-processing)**: 모델 학습 과정에 공정성 제약 조건을 추가합니다.
    *   **Adversarial Debiasing**: 주 예측 모델과 별개로, 민감한 속성을 예측하려는 '적대자(Adversary)' 모델을 함께 학습시킵니다. 주 모델은 적대자가 민감 정보를 맞추지 못하도록 방해하는 방향으로 학습되어, 결과적으로 민감 정보에 둔감한 예측을 하게 됩니다.
    *   **공정성 제약 추가**: 위에서 언급된 공정성 지표(예: Equalized Odds)를 손실 함수(Loss Function)의 규제 항(Regularization Term)으로 추가하여, 모델이 정확도와 공정성을 함께 최적화하도록 유도합니다.

*   **후처리 (Post-processing)**: 학습된 모델의 예측 결과를 보정합니다.
    *   **임계값 조정 (Thresholding)**: 각 그룹에 대해 서로 다른 예측 임계값(Classification Threshold)을 적용하여 공정성 지표를 맞춥니다. 예를 들어, A 그룹은 0.5, B 그룹은 0.6을 기준으로 예측을 결정할 수 있습니다.

---

## 3. 예시 (Example)

### 코드 예시 (Fairlearn 라이브러리)

Microsoft의 `Fairlearn`은 Bias 탐지 및 완화 기능을 제공하는 대표적인 Python 라이브러리입니다.

'''python
from fairlearn.metrics import MetricFrame, demographic_parity_difference
from fairlearn.reductions import ExponentiatedGradient, DemographicParity

# 1. Bias 탐지
y_true = [0, 1, 1, 1, 0, 1, 0, 1]
y_pred = [0, 0, 1, 0, 1, 1, 0, 1]
sensitive_features = ["A", "B", "B", "A", "A", "B", "B", "A"]

metrics = {
    'accuracy': accuracy_score,
    'demographic_parity': demographic_parity_difference
}
metric_frame = MetricFrame(metrics=metrics,
                           y_true=y_true,
                           y_pred=y_pred,
                           sensitive_features=sensitive_features)

print(metric_frame.by_group)
#           accuracy  demographic_parity
# sensitive_feature
# A              0.5                 0.5
# B              0.5                 0.5

# 2. Bias 완화 (In-processing)
# 모델과 공정성 제약조건(DemographicParity)을 사용해 새로운 모델 학습
mitigator = ExponentiatedGradient(LogisticRegression(), DemographicParity())
mitigator.fit(X_train, y_train, sensitive_features=s_train)
y_pred_mitigated = mitigator.predict(X_test)
'''

### 사용 사례 (Use Case)

*   **대출 심사**: 신용 점수 모델이 특정 인종이나 출신 지역에 불리한 결정을 내리지 않도록 `Equal Opportunity`를 보장합니다.
*   **채용 시스템**: 이력서 스크리닝 AI가 성별이나 학교 이름과 같은 편향된 요소 대신 직무 능력 중심으로 후보자를 평가하도록 `Adversarial Debiasing`을 적용합니다.

---

## 4. 예상 면접 질문 (Potential Interview Questions)

*   **Q. 모델의 공정성을 평가하는 지표 3가지(Demographic Parity, Equal Opportunity, Equalized Odds)의 차이점을 설명해주세요.**
    *   **A.** Demographic Parity는 민감한 그룹별로 긍정 예측 비율이 같은지를 봅니다. Equal Opportunity는 실제 정답이 참(True)인 케이스에 한정하여, 그룹 간 정답률(TPR)이 같은지를 확인합니다. Equalized Odds는 한발 더 나아가, 실제 정답이 참일 때(TPR)와 거짓일 때(FPR) 모두에서 그룹 간 예측률이 동일할 것을 요구하는 가장 엄격한 조건입니다.
*   **Q. 데이터 편향을 완화하기 위한 전처리(Pre-processing) 기법과 후처리(Post-processing) 기법의 장단점은 무엇인가요?**
    *   **A.** 전처리 기법은 모델 학습 전에 데이터 자체를 수정하므로 모델 종류에 구애받지 않고 적용할 수 있는 장점이 있지만, 데이터의 원래 분포를 왜곡할 수 있다는 단점이 있습니다. 후처리 기법은 이미 학습된 모델을 그대로 사용하면서 예측 결과만 보정하므로 적용이 간편하지만, 모델의 근본적인 편향 자체를 해결하는 것은 아니라는 한계가 있습니다.
*   **Q. Adversarial Debiasing은 어떤 원리로 동작하며, 어떤 장점이 있나요?**
    *   **A.** 예측 모델과 적대자 모델을 경쟁적으로 학습시키는 원리입니다. 예측 모델은 정확한 예측을 하려 하고, 적대자 모델은 그 예측 결과를 바탕으로 민감한 정보(성별, 인종 등)를 맞추려고 합니다. 예측 모델은 적대자가 정답을 맞추지 못하도록, 즉 자신의 예측 결과에서 민감 정보 관련 단서를 지우는 방향으로 학습됩니다. 이를 통해 별도의 데이터 라벨링 없이도 공정성을 높일 수 있는 장점이 있습니다.

---

## 5. 더 읽어보기 (Further Reading)

*   [Fairlearn 공식 문서](https://fairlearn.org/)
*   [AI Fairness 360 (AIF360) GitHub](https://github.com/Trusted-AI/AIF360)
*   [Equality of Opportunity in Supervised Learning (Hardt et al., 2016)](https://arxiv.org/abs/1610.02413)