---
title: "평가 지표 (FID, IS, CLIP-Score)"
date: "2025-07-03"
tags: ["딥러닝", "생성 모델"]
difficulty: "medium"
---

# 평가 지표 (FID, IS, CLIP-Score)

## 1. 핵심 개념 (Core Concept)

생성 모델의 평가 지표는 모델이 생성한 데이터의 품질, 다양성, 그리고 실제 데이터와의 유사성을 정량적으로 측정하는 데 사용됩니다. 이는 모델의 성능을 객관적으로 비교하고 개선 방향을 제시하는 데 필수적입니다.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 FID (Fréchet Inception Distance)
FID는 생성된 이미지와 실제 이미지 분포 간의 거리를 측정하는 지표입니다. Inception-v3 모델의 특징 추출기를 사용하여 이미지의 고수준 특징을 추출한 후, 이 특징들의 가우시안 분포를 가정하여 두 분포 간의 Fréchet 거리를 계산합니다. FID 값이 낮을수록 생성된 이미지가 실제 이미지와 유사하고 품질이 좋다고 평가됩니다.

### 2.2 IS (Inception Score)
IS는 생성된 이미지의 품질과 다양성을 동시에 측정하는 지표입니다. Inception-v3 모델을 사용하여 이미지의 클래스 확률 분포를 얻고, 이 분포의 엔트로피를 계산하여 이미지의 선명도(클래스 예측의 불확실성)를 측정합니다. 또한, 생성된 이미지들의 클래스 분포가 균일할수록 다양성이 높다고 평가합니다. IS 값이 높을수록 좋은 모델로 간주됩니다.

### 2.3 CLIP-Score
CLIP-Score는 텍스트-이미지 생성 모델에서 생성된 이미지가 주어진 텍스트 프롬프트와 얼마나 잘 일치하는지(텍스트-이미지 정합성)를 측정하는 지표입니다. CLIP(Contrastive Language-Image Pre-training) 모델을 사용하여 이미지와 텍스트 프롬프트의 임베딩을 얻은 후, 이들 간의 코사인 유사도를 계산합니다. CLIP-Score가 높을수록 생성된 이미지가 텍스트 프롬프트의 의미를 잘 반영한다고 평가됩니다.

---

## 3. 예시 (Example)

### 코드 예시 (Python)
'''python
# FID 계산 예시 (PyTorch 및 TorchMetrics 라이브러리 사용)
import torch
from torchmetrics.image.fid import FrechetInceptionDistance
from torchvision import transforms
from PIL import Image

# 예시 데이터 (실제 이미지와 생성된 이미지)
# 실제 환경에서는 실제 데이터셋과 모델이 생성한 이미지 배치 사용
real_images = torch.randn(10, 3, 299, 299) # 예시: 10개의 299x299 RGB 이미지
generated_images = torch.randn(10, 3, 299, 299) # 예시: 10개의 299x299 RGB 이미지

# FID 계산기 초기화
fid = FrechetInceptionDistance(feature=2048, normalize=True)

# 이미지 전처리 (0-255 범위의 uint8로 변환)
# FID 계산기는 0-255 범위의 uint8 이미지를 기대합니다.
real_images_uint8 = (real_images * 255).to(torch.uint8)
generated_images_uint8 = (generated_images * 255).to(torch.uint8)

# 특징 업데이트
fid.update(real_images_uint8, real=True)
fid.update(generated_images_uint8, real=False)

# FID 값 계산
fid_score = fid.compute()
print(f"FID Score: {fid_score}")
'''

### 사용 사례 (Use Case)
*   **생성 모델 성능 비교**: 다양한 생성 모델(GAN, VAE, Diffusion Model 등)의 이미지 생성 품질을 객관적으로 비교하고 순위를 매깁니다.
*   **모델 학습 과정 모니터링**: 학습 중 FID, IS 등의 지표를 주기적으로 계산하여 모델의 수렴 여부와 성능 개선 추이를 확인합니다.
*   **하이퍼파라미터 튜닝**: 최적의 생성 성능을 달성하기 위한 모델 아키텍처나 학습 하이퍼파라미터를 탐색할 때 평가 지표를 활용합니다.
*   **텍스트-이미지 모델 개발**: CLIP-Score를 사용하여 텍스트 프롬프트와 생성된 이미지 간의 의미론적 일치도를 평가하고 모델을 개선합니다.

---

## 4. 예상 면접 질문 (Potential Interview Questions)

*   **Q. 생성 모델 평가에 FID를 사용하는 주된 이유는 무엇인가요?**
    *   **A.** FID는 생성된 이미지와 실제 이미지의 고수준 특징 분포를 비교하여 이미지의 품질과 다양성을 동시에 측정할 수 있기 때문입니다. Inception-v3 모델의 특징 추출기를 사용하므로 사람의 시각적 인지 특성을 어느 정도 반영합니다.
*   **Q. IS와 FID의 차이점은 무엇이며, 각각 어떤 상황에 더 적합한가요?**
    *   **A.** IS는 단일 모델의 생성 품질과 다양성을 평가하는 데 유용하며, 특히 이미지 분류 모델의 성능에 기반합니다. 반면, FID는 두 이미지 분포 간의 거리를 측정하므로, 생성 모델 간의 상대적인 성능 비교에 더 적합합니다. FID는 IS보다 실제 이미지 분포와의 유사성을 더 잘 반영한다고 알려져 있습니다.
*   **Q. CLIP-Score는 어떤 종류의 생성 모델 평가에 주로 사용되며, 그 이유는 무엇인가요?**
    *   **A.** CLIP-Score는 주로 텍스트-이미지 생성 모델(예: DALL-E, Stable Diffusion) 평가에 사용됩니다. 이는 CLIP 모델이 이미지와 텍스트 간의 의미론적 관계를 학습했기 때문에, 생성된 이미지가 주어진 텍스트 프롬프트의 의미를 얼마나 잘 표현하는지 정량적으로 평가할 수 있기 때문입니다.

---

## 5. 더 읽어보기 (Further Reading)

*   [FID 논문: GANs Trained by a Two Time-Scale Update Rule Converge to a Local Nash Equilibrium](https://arxiv.org/abs/1706.08500)
*   [IS 논문: Improved Techniques for Training GANs](https://arxiv.org/abs/1606.03498)
*   [CLIP 논문: Learning Transferable Visual Models From Natural Language Supervision](https://arxiv.org/abs/2103.00020)
*   [TorchMetrics FID 구현](https://torchmetrics.readthedocs.io/en/stable/image/frechet_inception_distance.html)
