---
title: Dice, Jaccard, F1
date: 2025-07-04
tags:
  - 컴퓨터
  - 비전
  - 평가
  - 지표
  - Metrics
  - Dice
  - Jaccard
  - F1
difficulty: medium
---

# Dice, Jaccard, F1

## 1. 핵심 개념 (Core Concept)

두 집합(예측·정답) 간 **겹침 정도를 측정**하는 대표 지표로 \*\*Jaccard 계수(Intersection‑over‑Union, IoU)\*\*와 \*\*Dice 계수(Sørensen‑Dice)\*\*가 존재함. 이진 분류 관점에서 **F1‑score**는 **Precision·Recall의 조화 평균**으로 Dice와 동일한 수식을 가지며, 다중 클래스·세그멘테이션 등 다양한 태스크에서 성능을 직관적으로 비교하는 데 사용됨.

______________________________________________________________________

## 2. 상세 설명 (Detailed Explanation)

### 2.1 정의 및 관계식

| 지표      | 수식                                                                                                | 범위    |
| :-------- | :-------------------------------------------------------------------------------------------------- | :------ |
| Jaccard J | $J = \\frac{\\lvert A \\cap B \\rvert}{\\lvert A \\cup B \\rvert}$                                  | \[0,1\] |
| Dice D    | $D = \\frac{2\\lvert A \\cap B \\rvert}{\\lvert A \\rvert + \\lvert B \\rvert}$                     | \[0,1\] |
| F1 (이진) | $F1 = \\frac{2 \\cdot \\text{Precision} \\cdot \\text{Recall}}{\\text{Precision} + \\text{Recall}}$ | \[0,1\] |

- **관계**: $D = \\frac{2J}{1+J}$ → Jaccard는 Dice를 통해 쉽게 변환 가능.

- **일반화**: Tversky 지수 $T = \\frac{\\lvert A \\cap B \\rvert}{\\lvert A \\cap B \\rvert + \\alpha\\lvert A \\setminus B \\rvert + \\beta\\lvert B \\setminus A \\rvert}$ 는 $\\alpha = \\beta = 0.5$일 때 Dice, $\\alpha = \\beta = 1$일 때 Jaccard로 귀결됨.

### 2.2 사용 맥락

- **이미지 세그멘테이션** – 픽셀 단위 겹침 평가에 Dice·IoU를 사용하며, 불균형 클래스에서 민감도가 높음.

- **정보 검색·NLP** – 토큰·셋 유사도 측정에 Jaccard를 자주 사용함.

- **모델 학습** – Dice Loss =1−D를 손실 함수로 채택해 포어그라운드 극소 픽셀 문제를 완화함.

### 2.3 장단점 비교

| 지표    | 장점                                      | 한계                                      |
| :------ | :---------------------------------------- | :---------------------------------------- |
| Dice    | 직관적·불균형 강건                        | 수학적 집합 성질 부족(삼각 부등식 불만족) |
| Jaccard | 집합론 기반, 오브젝트 검출 IoU로 표준화   | 미분 가능한 변형 필요(loss)               |
| F1      | Precision·Recall 균형, 클래스 불균형 대응 | 임계값·스레숄드 선택 민감                 |

______________________________________________________________________

## 3. 예시 (Example)

### 코드 예시 (Python)

```python
import torch

def dice_coeff(pred: torch.Tensor, target: torch.Tensor, eps: float=1e-7):
    """pred, target: (N, H, W) binary masks"""
    inter = (pred & target).float().sum(dim=(1,2))
    union = pred.float().sum(dim=(1,2)) + target.float().sum(dim=(1,2))
    dice = (2 * inter + eps) / (union + eps)
    return dice.mean()
```

### 사용 사례 (Use Case)

> **의료 영상(CT 종양 세그멘테이션)** – Dice 0.85 이상을 임상 수용 기준으로 설정해 알고리즘 성능을 검증함.

______________________________________________________________________

## 4. 예상 면접 질문 (Potential Interview Questions)

- **Q. Dice와 IoU 중 어떤 지표가 작은 객체 세그멘테이션에서 더 안정적인가?**\
  **A.** Dice는 교집합에 두 배 가중치를 부여해 작은 객체에서도 값이 급락하지 않기에 통상 더 안정적임.

- **Q. F1‑score가 1인 조건을 Precision·Recall로 설명하라.**\
  **A.** Precision=Recall=1이어야 하며, 하나라도 1 미만이면 F1\<1로 감소함.

- **Q. Tversky 지수가 유용한 상황은?**\
  **A.** 불균형 클래스에서 false negative 비용이 false positive보다 클 때 α≠β를 조정해 민감도를 높일 수 있음.

- **Q. IoU 0.5 트레숄드가 객체 검출 mAP에 미치는 영향은?**\
  **A.** 낮은 임계값은 일치 판단을 느슨하게 해 recall이 증가하지만 localisation 정확도는 낮아진다.

______________________________________________________________________

## 5. 더 읽어보기 (Further Reading)

- Dice‑Sørensen coefficient(Wikipedia)

- Jaccard index & Intersection‑over‑Union guide(V7 Labs)

- scikit‑learn `f1_score` API

- Google ML Crash Course – Precision, Recall, F1

- Tversky Index overview

- PyTorch Forum – Dice Loss 구현
