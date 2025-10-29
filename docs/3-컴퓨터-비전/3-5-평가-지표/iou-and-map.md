---
title: IoU & mAP (Pascal / COCO)
date: 2025-07-04
tags:
  - 컴퓨터
  - 비전
  - 평가
  - 지표
  - Metrics
  - IoU
  - mAP
  - Object-Detection
difficulty: medium
---

# IoU & mAP (Pascal / COCO)

## 1. 핵심 개념 (Core Concept)

- **IoU(Intersection‑over‑Union)**: 예측 박스와 정답 박스 교집합을 합집합으로 나눈 비율로, **물체 위치 예측 정확도**를 단일 스칼라(0‑1)로 측정.

- **mAP(mean Average Precision)**: 각 클래스 **Precision‑Recall 곡선의 면적(AP)** 을 평균낸 지표로, 객체 검출 모델의 **정밀·재현 균형**을 집계함. Pascal VOC와 COCO는 **계산 방식·IoU 임계값**이 달라 직접 비교가 불가.

______________________________________________________________________

## 2. 상세 설명 (Detailed Explanation)

### 2.1 IoU 정의 및 특성

| 기호 | 수식                                                                                                                                         |
| :--- | :------------------------------------------------------------------------------------------------------------------------------------------- |
| IoU  | $\\text{IoU} = \\frac{\\lvert\\text{Area}_{pred} \\cap \\text{Area}_{gt}\\rvert}{\\lvert\\text{Area}_{pred} \\cup \\text{Area}_{gt}\\rvert}$ |

- 0: 전혀 겹치지 않음, 1: 완벽 일치.

- 일반 임계값: **0.5 (VOC)**, **0.5~0.95 (COCO)**.

- 배경·오버랩이 많은 데이터에서는 IoU ≥ 0.5라도 실제 시각 품질이 낮을 수 있어, 최근 벤치마크는 여러 임계값을 평균하여 엄격성을 높임.

### 2.2 Pascal VOC mAP(mAP50)

- **IoU ≥ 0.5** 를 TP로 간주.

- **11‑point interpolation**: 리콜 0,0.1,…,1 구간에서 최대 Precision을 샘플링해 평균 → AP. (VOC 2007)

- VOC 2010+ 는 **전체 P‑R 곡선 면적**(Continuous AP)로 변경.

- mAP = 모든 클래스 AP 평균(20/21 클래스).

### 2.3 COCO mAP(mAP@\[.50:.05:.95\])

- **10개 IoU 임계값(0.50:0.05:0.95)** 에서 AP를 산출 후 평균.

- 리콜 101 지점(0–1)을 이용한 **정밀한 적분**(101‑point interpolation).

- **분할 평가**: 객체 크기별(소·중·대) AP와 최대 검출 수(100·1)별 AR도 보고.

### 2.4 지표 비교

| 항목       | Pascal VOC                 | COCO                            |
| :--------- | :------------------------- | :------------------------------ |
| IoU 임계값 | 0.5 고정                   | 0.50→0.95 다중                  |
| AP 적분    | 11‑point(’07) / Continuous | 101‑point Continuous            |
| 클래스 수  | 20 (’07) · 21 (’12)        | 80                              |
| 결과 표기  | **mAP@0.5**                | **AP, AP50, AP75, APs/m/l, AR** |

> **실무 팁**: 연구 논문에서 `mAP@0.5`는 VOC 방식, `mAP@0.5:0.95`는 COCO 방식을 의미함.

### 2.5 계산 과정 간단 예시

1. **정렬**: 클래스별 예측을 신뢰도(score) 내림차순 정렬.

1. **IoU 매칭**: GT와 겹침 ≥ threshold면 TP, 아니면 FP.

1. **P‑R 곡선**: 누적 TP·FP로 Precision vs Recall 계산.

1. **AP**: 곡선 아래 면적(사다리꼴 적분 or 표준 샘플링).

1. **mAP**: 클래스별 AP 평균.

______________________________________________________________________

## 3. 예시 (Example)

### 코드 예시 (Python)

```python
import numpy as np

def iou(boxA, boxB):
    # box: [x1, y1, x2, y2]
    xa = max(boxA[0], boxB[0])
    ya = max(boxA[1], boxB[1])
    xb = min(boxA[2], boxB[2])
    yb = min(boxA[3], boxB[3])
    inter = max(0, xb - xa) * max(0, yb - ya)
    union = (boxA[2]-boxA[0])*(boxA[3]-boxA[1]) + (boxB[2]-boxB[0])*(boxB[3]-boxB[1]) - inter
    return inter / union if union else 0

# 예: VOC식 AP(11‑point) 계산 skeleton
from sklearn.metrics import precision_recall_curve

def voc_ap(rec, prec):
    ap = 0.0
    for t in np.linspace(0, 1, 11):
        ap += np.max(prec[rec >= t]) if np.sum(rec >= t) else 0
    return ap / 11
```

### 사용 사례 (Use Case)

- **모바일 자율주행 베타 테스트**: 실제 주행 로그에서 COCO mAP50이 0.6, mAP50:95가 0.35로 측정 → 고정 임계값 대비 다중 임계값 평과에서 성능 차이가 더 크게 나타나 안전성 개선 필요.

______________________________________________________________________

## 4. 예상 면접 질문 (Potential Interview Questions)

1. **VOC mAP과 COCO mAP 계산식 차이를 설명하라.**

1. **IoU 0.5를 TP 임계값으로 쓰면 생길 수 있는 문제점은?**

1. **AP75가 높고 AP50:95가 낮다는 의미는?**

1. **다중 예측이 하나의 GT와 매칭될 때 일반적으로 취하는 규칙은?**

1. **COCO에서 AR@10과 AR@100을 분리 측정하는 이유는?**

______________________________________________________________________

## 5. 더 읽어보기 (Further Reading)

- PyImageSearch – IoU Tutorial

- LearnOpenCV – mAP for Object Detection

- COCO Dataset Eval Docs

- Ultralytics YOLO Performance Metrics Guide

- V7 Labs – IoU Guide

- Picsellia – COCO Metrics Explained

- DigitalOcean – Evaluating Object Detectors with mAP

- GitHub – mAP Calculator (VOC & COCO)
