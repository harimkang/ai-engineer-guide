---
title: "탐지 모델: Faster R-CNN, SSD, RetinaNet, YOLO (v8)"
date: "2025-07-03"
tags: ["컴퓨터 비전", "대표 모델", "객체 탐지"]
difficulty: "hard"
---

# 탐지 모델: Faster R-CNN, SSD, RetinaNet, YOLO (v8)

## 1. 핵심 개념 (Core Concept)

객체 탐지(Object Detection)는 이미지나 영상 내에서 객체의 위치(바운딩 박스)와 종류(클래스)를 식별하는 컴퓨터 비전의 핵심 기술임. **Faster R-CNN**은 높은 정확도를 위한 2단계(Two-stage) 탐지기의 대표 주자이며, **SSD**와 **YOLO**는 빠른 속도를 위한 1단계(One-stage) 탐지기의 선두 주자임. **RetinaNet**은 1단계 탐지기의 속도와 2단계 탐지기의 정확도를 모두 잡기 위해 Focal Loss를 도입한 모델임.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 Faster R-CNN

Faster R-CNN은 2단계 객체 탐지 모델의 대표적인 예시로, 높은 정확도를 자랑함. 객체 탐지 과정을 두 단계로 나눔:

1.  **Region Proposal Network (RPN)**: 이미지에서 객체가 있을 법한 영역(Region Proposal)을 제안함. 기존의 Selective Search를 신경망으로 대체하여 속도를 크게 향상시켰음.
2.  **Classification & Regression**: 제안된 후보 영역에 대해 객체의 클래스를 분류하고, 바운딩 박스의 위치를 정밀하게 조정함.

*   **장점**: 2단계 접근 방식 덕분에 높은 정확도를 가지며, 특히 작은 객체 탐지 성능이 우수함.
*   **단점**: 구조가 복잡하고 여러 단계를 거치므로 추론 속도가 느려 실시간 처리가 어려움.

### 2.2 SSD (Single Shot MultiBox Detector)

SSD는 1단계 객체 탐지 모델로, 후보 영역 제안과 분류를 한 번의 네트워크 통과(Single Pass)로 동시에 처리함. VGG와 같은 백본 네트워크 위에 여러 개의 추가적인 컨볼루션 레이어를 쌓아 다양한 크기의 특징 맵(Feature Map)을 생성하고, 각 특징 맵에서 미리 정의된 다양한 크기와 종횡비의 기본 상자(Default Box)를 사용하여 객체를 탐지함.

*   **장점**: 1단계 구조 덕분에 추론 속도가 매우 빠르며, 실시간 탐지에 적합함.
*   **단점**: Faster R-CNN에 비해 정확도가 다소 낮으며, 특히 작은 객체 탐지에 어려움을 겪는 경향이 있음.

### 2.3 YOLO (You Only Look Once)

YOLO는 이미지를 그리드(Grid)로 나누고, 각 그리드 셀이 객체의 바운딩 박스와 클래스 확률을 직접 예측하는 독창적인 방식을 사용함. 이미지를 단 한 번만 보고 객체 탐지를 완료하여 극도로 빠른 속도를 구현함. YOLOv3, YOLOv4, YOLOv5, YOLOv8 등 지속적으로 성능이 개선된 버전들이 발표되었음.

*   **장점**: 현존하는 객체 탐지 모델 중 가장 빠른 속도를 자랑하며, 실시간 영상 처리 분야에서 압도적인 강점을 보임.
*   **단점**: 초기 모델들은 작은 객체 탐지 성능과 정확도가 다른 모델에 비해 떨어졌으나, 최신 버전들은 정확도 면에서도 큰 발전을 이루었음.

### 2.4 RetinaNet

RetinaNet은 1단계 탐지기의 속도와 2단계 탐지기의 정확도를 모두 잡기 위해 개발되었음. 핵심은 **Focal Loss**라는 새로운 손실 함수를 도입한 것임. Focal Loss는 학습 과정에서 쉽게 분류되는 배경 샘플들의 가중치를 낮추고, 탐지하기 어려운 객체에 집중하도록 만들어 1단계 탐지기의 고질적인 문제였던 클래스 불균형(Class Imbalance)을 효과적으로 해결함. 또한, 다양한 크기의 특징을 활용하는 **Feature Pyramid Network (FPN)** 구조를 채택하여 여러 스케일의 객체를 효과적으로 탐지함.

*   **장점**: 1단계 탐지기임에도 불구하고 2단계 탐지기 수준의 높은 정확도를 달성했으며, 특히 촘촘하거나 작은 객체 탐지에 강점을 보임.
*   **단점**: Focal Loss와 FPN 구조로 인해 SSD나 YOLO에 비해 다소 복잡하고 속도가 느릴 수 있음.

---

## 3. 비교 (Comparison)

| 모델 | 아키텍처 유형 | 주요 특징 | 정확도 (mAP) | 속도 (FPS) | 강점 | 약점 |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Faster R-CNN** | 2-Stage | Region Proposal Network (RPN) | 매우 높음 | 느림 | 높은 정확도, 작은 객체 탐지 | 느린 추론 속도 |
| **SSD** | 1-Stage | Multi-scale Feature Maps | 중간~높음 | 빠름 | 실시간 처리, 간단한 구조 | 작은 객체 탐지 성능 저하 |
| **YOLO** | 1-Stage | Unified Grid-based Detection | 중간~높음 | 매우 빠름 | 압도적인 실시간 처리 속도 | 초기 버전의 정확도 및 작은 객체 탐지 한계 |
| **RetinaNet** | 1-Stage | Focal Loss, FPN | 매우 높음 | 중간~빠름 | 정확도와 속도의 균형, 클래스 불균형 문제 해결 | SSD/YOLO 대비 상대적으로 느림 |

---

## 4. 예상 면접 질문 (Potential Interview Questions)

*   **Q. 1단계 탐지기와 2단계 탐지기의 주요 차이점은 무엇이며, 각각의 장단점은 무엇인가요?**
    *   **A.** 1단계 탐지기(예: YOLO, SSD)는 객체 위치 제안과 분류를 한 번에 처리하여 속도가 빠르지만, 정확도가 상대적으로 낮을 수 있습니다. 2단계 탐지기(예: Faster R-CNN)는 먼저 객체 후보 영역을 제안한 후 각 영역을 분류하므로 정확도가 높지만, 속도가 느립니다.

*   **Q. RetinaNet에서 Focal Loss를 사용하는 주된 이유는 무엇인가요?**
    *   **A.** Focal Loss는 1단계 객체 탐지기에서 발생하는 심각한 클래스 불균형 문제를 해결하기 위해 도입되었습니다. 배경 영역이 객체 영역보다 훨씬 많아 학습이 배경에 편향되는 것을 방지하고, 학습하기 어려운(hard) 샘플에 더 집중하여 모델의 정확도를 향상시킵니다.

*   **Q. YOLO 계열 모델이 실시간 객체 탐지에 특히 강점을 보이는 이유는 무엇인가요?**
    *   **A.** YOLO는 이미지를 그리드 시스템으로 나누고, 각 그리드 셀이 직접 바운딩 박스와 클래스 확률을 예측하는 단일 네트워크 구조를 가집니다. 이처럼 객체 탐지 과정을 단일 회귀 문제로 접근하여 매우 빠른 추론 속도를 달성할 수 있습니다.

---

## 5. 더 읽어보기 (Further Reading)

*   [Faster R-CNN: Towards Real-Time Object Detection with Region Proposal Networks](https://arxiv.org/abs/1506.01497)
*   [SSD: Single Shot MultiBox Detector](https://arxiv.org/abs/1512.02325)
*   [YOLOv8 Documentation](https://docs.ultralytics.com/yolov8/)
*   [Focal Loss for Dense Object Detection (RetinaNet)](https://arxiv.org/abs/1708.02002)