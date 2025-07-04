---
title: 정확도, Top-K Error

date: 2025-07-04
tags:
  - 컴퓨터
  - 비전
  - 평가
  - 지표
  - Metrics
  - Accuracy
  - Top-K
  - Evaluation
difficulty: easy
---

# 정확도, Top‑K Error

## 1. 핵심 개념 (Core Concept)

**정확도(Accuracy)**는 전체 예측 값 중 정답을 맞힌 비율을 의미하며, **Top‑K 정확도**는 모델이 출력한 상위 $K$개의 후보 안에 정답이 포함될 때 ‘맞음’으로 간주하는 지표임. 반대로 **Top‑K Error**는 $1-\text{Top‑K 정확도}$로 정의되어 대규모 다중 클래스 문제(예: ImageNet)에서 모델의 실질적 성능을 표현하는 데 널리 쓰임. ([flowhunt.io](https://www.flowhunt.io/glossary/top-k-accuracy/?utm_source=chatgpt.com), [stats.stackexchange.com](https://stats.stackexchange.com/questions/156471/imagenet-what-is-top-1-and-top-5-error-rate?utm_source=chatgpt.com))

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 혼동 행렬과 정확도

이진 분류 기준 **정확도**는

$$\text{Accuracy} = \frac{TP + TN}{TP + FP + TN + FN}$$

으로 참(T)·거짓(F), 양성(P)·음성(N)의 합계 대비 올바른 예측 횟수를 측정한다. ([en.wikipedia.org](https://en.wikipedia.org/wiki/Confusion_matrix?utm_source=chatgpt.com))

다중 클래스에서는 샘플 수 $N$에 대해

$$\text{Accuracy} = \frac{1}{N}\sum_{i=1}^{N}\mathbf{1}[\hat{y}_i = y_i]$$

와 같이 계산한다. ([developers.google.com](https://developers.google.com/machine-learning/crash-course/classification/accuracy-precision-recall?utm_source=chatgpt.com))

> **제한점**: 클래스로 불균형한 데이터셋에서 정확도는 다수 클래스만 예측해도 높은 값이 나와 지표가 왜곡된다. ([machinelearningmastery.com](https://www.machinelearningmastery.com/failure-of-accuracy-for-imbalanced-class-distributions/?utm_source=chatgpt.com), [pmc.ncbi.nlm.nih.gov](https://pmc.ncbi.nlm.nih.gov/articles/PMC10741524/?utm_source=chatgpt.com))

### 2.2 Top‑K 정확도 & Error

Top‑K 정확도는

$$\text{Top-K Acc} = \frac{1}{N}\sum_{i=1}^{N}\mathbf{1}[y_i \in \text{TopK}(\hat{p}_i)]$$

로 정의되고, **Top‑K Error** $= 1 - \text{Top‑K Accuracy}$ ([scikit-learn.org](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.top_k_accuracy_score.html?utm_source=chatgpt.com))

- **K=1**일 때 Top‑1 정확도/에러는 일반 정확도/오류와 동일하다. ([stackoverflow.com](https://stackoverflow.com/questions/37668902/evaluation-calculate-top-n-accuracy-top-1-and-top-5?utm_source=chatgpt.com))
    
- ImageNet 대회는 Top‑1과 **Top‑5** 오차를 공식 지표로 사용하여, 모델이 상위 5개 추측 안에 정답을 포함했는지를 평가한다. ([stats.stackexchange.com](https://stats.stackexchange.com/questions/129395/imagenet-what-does-top-five-error-means?utm_source=chatgpt.com), [wiki.math.uwaterloo.ca](https://wiki.math.uwaterloo.ca/statwiki/index.php?title=Evaluating_Machine_Accuracy_on_ImageNet&utm_source=chatgpt.com))
    
- 2025년 현재 최고 모델(‘Model Soup’ 계열)은 Top‑1 90 % 이상, Top‑5 99 % 이상을 기록한다. ([paperswithcode.com](https://paperswithcode.com/sota/image-classification-on-imagenet?utm_source=chatgpt.com))
    

### 2.3 프레임워크별 구현

|라이브러리|함수/클래스|특징|
|:--|:--|:--|
|**PyTorch**|`torch.topk`, 사용자 함수 예시 아래 코드 참고|텐서 차원별 상위 K 인덱스 반환 ([stackoverflow.com](https://stackoverflow.com/questions/59474987/how-to-get-top-k-accuracy-in-semantic-segmentation-using-pytorch?utm_source=chatgpt.com))|
|**scikit‑learn**|`top_k_accuracy_score`|확률 입력 필요, 다중 라벨 지원 ([scikit-learn.org](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.top_k_accuracy_score.html?utm_source=chatgpt.com))|
|**TensorFlow/Keras**|`top_k_categorical_accuracy`, `SparseTopKCategoricalAccuracy`|모델 컴파일 시 메트릭 지정 가능 ([keras.io](https://keras.io/api/metrics/accuracy_metrics/?utm_source=chatgpt.com))|

---

## 3. 예시 (Example)

### 코드 예시 (Python)

```python
import torch

def topk_accuracy(output: torch.Tensor, target: torch.Tensor, ks=(1, 5)):
    """
    output: (batch, num_classes) logit scores
    target: (batch,) ground‑truth labels
    """
    with torch.no_grad():
        maxk = max(ks)
        _, pred = output.topk(maxk, dim=1, largest=True, sorted=True)
        pred = pred.t()             # (maxk, batch)
        correct = pred.eq(target.view(1, -1).expand_as(pred))
        res = {}
        for k in ks:
            res[f"top{k}"] = correct[:k].reshape(-1).float().mean().item()
        return res
```

_위 구현은 GitHub Gist에서 널리 인용되는 `accuracy` 유틸리티를 단순화한 버전임._ ([gist.github.com](https://gist.github.com/weiaicunzai/2a5ae6eac6712c70bde0630f3e76b77b?utm_source=chatgpt.com))

### 사용 사례 (Use Case)

> **모바일 이미지 검색** – 사용자가 찍은 사진에 대해 모델이 상위 5개 제품 후보를 반환할 때, **Top‑5 정확도**가 실제 검색 성공률과 직결되므로 KPI로 활용된다. ([logicballs.com](https://logicballs.com/glossary/top-k-accuracy/?utm_source=chatgpt.com))

---

## 4. 예상 면접 질문 (Potential Interview Questions)

- **Q. 왜 ImageNet은 Top‑5 Error를 평가 지표로 선택했는가?**  
    **A.** 1,000개 클래스 중 시각적으로 유사한 객체가 많아 Top‑1 에러만으로는 모델 개선이 미세하게 반영되지 않기 때문이며, 사용자는 추천식 상위 후보가 있으면 만족할 가능성이 높기 때문이다. ([stats.stackexchange.com](https://stats.stackexchange.com/questions/156471/imagenet-what-is-top-1-and-top-5-error-rate?utm_source=chatgpt.com), [stackoverflow.com](https://stackoverflow.com/questions/37668902/evaluation-calculate-top-n-accuracy-top-1-and-top-5?utm_source=chatgpt.com))
    
- **Q. 정확도가 높은데 실제 서비스 성능이 나쁜 경우는?**  
    **A.** 클래스 불균형 문제로, 예를 들어 95 %가 정상(Negative)인 데이터에서 모든 샘플을 ‘정상’이라 예측하면 정확도 95 %를 달성하지만 양성(Positive) 탐지는 0 %다. ([machinelearningmastery.com](https://www.machinelearningmastery.com/failure-of-accuracy-for-imbalanced-class-distributions/?utm_source=chatgpt.com), [towardsai.net](https://towardsai.net/p/l/why-accuracy-is-not-a-good-metric-for-imbalanced-data?utm_source=chatgpt.com))
    
- **Q. Top‑K를 늘리면 항상 좋은가?**  
    **A.** K가 커질수록 정확도는 비단순히 증가하지만 실용적 가치와 모델 응답 길이, 계산 비용을 함께 고려해야 한다. ([scikit-learn.org](https://scikit-learn.org/stable/modules/model_evaluation.html?utm_source=chatgpt.com))
    
- **Q. Top‑K 정확도와 Recall의 차이는?**  
    **A.** Recall은 실제 양성 중 검출 비율로 **Ground Truth 기준**, Top‑K 정확도는 **모델 출력 기준**이 상위 K에 포함되는지 판단한다. ([en.wikipedia.org](https://en.wikipedia.org/wiki/Precision_and_recall?utm_source=chatgpt.com))
    
- **Q. 멀티라벨 문제에서 Top‑K를 사용할 때 주의점은?**  
    **A.** 라벨 수가 샘플마다 달라 Subset Accuracy가 어려우므로 `top_k_accuracy_score`의 `labels` 파라미터나 Micro/Macro 평균을 같이 검토해야 한다. ([discuss.pytorch.org](https://discuss.pytorch.org/t/top-k-accuracy-for-multilabel-classification/112939?utm_source=chatgpt.com))
    

---

## 5. 더 읽어보기 (Further Reading)

- Wikipedia – _Confusion matrix_, _Precision and recall_ ([en.wikipedia.org](https://en.wikipedia.org/wiki/Confusion_matrix?utm_source=chatgpt.com), [en.wikipedia.org](https://en.wikipedia.org/wiki/Precision_and_recall?utm_source=chatgpt.com))
- scikit‑learn Documentation – _Model evaluation_ & `top_k_accuracy_score` ([scikit-learn.org](https://scikit-learn.org/stable/modules/model_evaluation.html?utm_source=chatgpt.com), [scikit-learn.org](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.top_k_accuracy_score.html?utm_source=chatgpt.com))
- Keras Docs – _Accuracy Metrics_ ([keras.io](https://keras.io/api/metrics/accuracy_metrics/?utm_source=chatgpt.com))
- FlowHunt & Logicballs – Glossary entries on Top‑K Accuracy ([flowhunt.io](https://www.flowhunt.io/glossary/top-k-accuracy/?utm_source=chatgpt.com), [logicballs.com](https://logicballs.com/glossary/top-k-accuracy/?utm_source=chatgpt.com))
- Jason Brownlee – _Failure of Accuracy for Imbalanced Classification_ ([machinelearningmastery.com](https://www.machinelearningmastery.com/failure-of-accuracy-for-imbalanced-class-distributions/?utm_source=chatgpt.com))
- Papers with Code – ImageNet Leaderboard (Top‑1 / Top‑5) ([paperswithcode.com](https://paperswithcode.com/sota/image-classification-on-imagenet?utm_source=chatgpt.com))