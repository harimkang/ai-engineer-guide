---
title: 3D 비전 & NeRF
date: 2025
  - 컴퓨터
  - 비전
  - 3D
  - 3D-Vision
  - NeRF
  - Computer-Vision
difficulty: hard
---

# 3D 비전 & NeRF

## 1. 핵심 개념 (Core Concept)

3D 비전은 다수의 2D 영상·센서 신호를 바탕으로 **공간 기하(geometry)**·**재질(appearance)** 정보를 추정하여 장면을 3차원으로 이해·재구성하는 학문임. NeRF(Neural Radiance Fields)는 연속 5D 좌표 $(x,y,z,\theta,\phi)$를 입력받아 밀도와 방사 복사량을 예측하는 **신경망 기반 장면 표현**으로, 소수의 이미지로부터 사진처럼 사실적인 새로운 시점을 합성함 ([arxiv.org](https://arxiv.org/abs/2003.08934)).

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 3D 비전 전통 기법 개요

- **다중 시점 기하(Multi‑View Geometry)** – 스테레오, Structure‑from‑Motion (SfM), Multi‑View Stereo(MVS) 등은 특징 매칭과 카메라 보정으로 포인트 클라우드를 계산함 ([r-5.org](https://www.r-5.org/files/books/computers/algo-list/image-processing/vision/Richard_Hartley_Andrew_Zisserman-Multiple_View_Geometry_in_Computer_Vision-EN.pdf), [cseweb.ucsd.edu](https://cseweb.ucsd.edu/classes/fa23/cse252A-a/lec10.pdf)).
    
- **SfM 파이프라인 예: COLMAP** – 특징 추출→매칭→본질/기본행렬 추정→번들 조정(Bundle Adjustment)으로 카메라 궤적과 3D 지점을 동시에 추정하며, 최신 v3.12.0은 멀티‑카메라 릭 지원을 포함함 ([colmap.github.io](https://colmap.github.io/), [github.com](https://github.com/colmap/colmap/releases)).
    
- **깊이 추정(Depth Estimation)** – 모노큘러 딥러닝 모델은 절대 깊이를 직접 회귀하여 실내·자율주행 등에서 활용됨 ([arxiv.org](https://arxiv.org/html/2501.11841v2)).
    

> **Why NeRF?** 전통 기법은 포인트 클라우드의 밀도·텍스처 한계와 복잡 지형에서의 매칭 오류가 존재. NeRF는 전역 볼륨 표현과 미분 가능 레이 마칭으로 이를 극복하여 렌더링 정확도를 대폭 향상시킴 ([arxiv.org](https://arxiv.org/abs/2003.08934)).

### 2.2 NeRF 구조와 학습 과정

```mermaid
graph LR
    A["이미지 & 카메라 포즈"] --> B["5D 좌표 샘플링"]
    B --> C["MLP f_θ"]
    C --> D["σ(밀도) & RGB(색)"]
    D --> E["볼륨 렌더링 적분"]
    E --> F["합성 이미지"]
    F --> G["재구성 손실 L"]
    G -.->|"역전파"| C
```

- **학습**: 레이마다 수십~수백 샘플을 MLP에 투입하여 색/밀도를 얻고, 터미네이션 공지수를 포함한 볼륨 렌더링 공식을 적분하여 픽셀 색을 예측함 ([arxiv.org](https://arxiv.org/abs/2003.08934)).
    
- **표현력 vs 속도**: 고해상도 장면은 수천 스텝의 레이 마칭이 필요해 느림. 이를 해결하기 위해 **Instant‑NGP**는 멀티해시 그리드와 CUDA 카운터 트릭으로 수 초 내 학습을 달성함 ([nvlabs.github.io](https://nvlabs.github.io/instant-ngp/)).
    
- **확장 변형**: 파노라마, 릴라이팅, 편집, LiDAR 융합 등 다양한 변형 모델이 연구되고 있음 ([arxiv.org](https://arxiv.org/abs/2306.03000), [arxiv.org](https://arxiv.org/abs/2404.00714)).
    

### 2.3 최신 트렌드

|기법|핵심 아이디어|장점|한계|
|:--|:--|:--|:--|
|NeRF (원본)|MLP + 볼륨 렌더링|품질 최고|학습·렌더링 느림|
|**Instant‑NGP**|해시 그리드 + CUDA|수 초 학습, 실시간 렌더|최적화된 구현 의존 ([nvlabs.github.io](https://nvlabs.github.io/instant-ngp/))|
|**3D Gaussian Splatting**|가우시안 엘립소이드로 장면 표면 근사|학습·추론 모두 고속, 실시간 적용 가능 ([repo-sam.inria.fr](https://repo-sam.inria.fr/fungraph/3d-gaussian-splatting/), [lemonde.fr](https://www.lemonde.fr/sciences/article/2024/05/08/une-technique-revolutionnaire-de-creation-de-scenes-en-3d_6232232_1650684.html))|파라미터 수 증가, 오클루전 처리 복잡|
|Nerfstudio|연구·프로덕션 통합 툴체인, 모듈화|학습 파이프라인 신속 구축|라이브러리 의존 ([github.com](https://github.com/nerfstudio-project/nerfstudio))|

### 2.4 응용 분야

- **로보틱스** – 경로 계획·객체 조작을 위한 장면 인식에 NeRF 기반 공간 맵이 활용됨 ([arxiv.org](https://arxiv.org/html/2405.01333v2)).
    
- **AR/VR·디지털 휴먼** – Meta Quest 3, Apple Vision Pro 등에서 실시간 3D 아바타·환경 캡처에 적용.
    
- **실내 측량·건축(BIM)** – 포토그램메트리보다 신뢰도 높은 텍스처·기하 품질을 확보.
    
- **영화·게임** – 스테이지 없이 가상 카메라 워크를 구현하여 제작비 절감.
    

---

## 3. 예시 (Example)

### 코드 예시 (Python)

```python
# Instant‑NGP 예시 (CUDA 12, PyTorch 2) – 간소화 버전
aimport tinycudann as tcnn  # 미니멀 해시 그리드 엔진
import torch, imageio, nerfacc

# 1) 카메라 파라미터 & 이미지 로딩 (COLMAP 출력)
poses, images = load_colmap("./dataset")

# 2) 모델 정의 (해시 그리드 + 2‑layer MLP)
encoding = tcnn.Encoding(n_input_dims=3, encoding_config={"otype":"HashGrid","n_levels":16,...})
network  = tcnn.Network(n_input_dims=encoding.n_output_dims, n_output_dims=4,
                        network_config={"otype":"FullyFusedMLP", "n_hidden_layers":2, "activation":"ReLU"})

# 3) 레이샘플러 & 손실
cascade = nerfacc.OccupancyGridEstimator(...)
optimizer = torch.optim.Adam(network.parameters(), lr=1e-2)

for step in range(max_steps):
    rays_o, rays_d, target = sample_random_rays(poses, images)
    loss = render_and_loss(rays_o, rays_d, target, encoding, network, cascade)
    optimizer.zero_grad(); loss.backward(); optimizer.step()
```

### 사용 사례 (Use Case)

> **문화재 디지털 트윈** – 소수의 고해상도 사진과 COLMAP 카메라를 이용해 NeRF를 학습하고, 가상 박물관 앱에서 관람자가 자유 시점으로 유물을 탐색하도록 제공.

---

## 4. 예상 면접 질문 (Potential Interview Questions)

- **Q. NeRF가 전통적인 포인트 클라우드‑기반 3D 재구성과 다른 점은?**
    
    - **A.** NeRF는 연속 체적 함수로 장면을 표현하여 밀도·색을 동시에 모델링하며, 미분 가능 렌더링으로 직접 픽셀 재구성 오류를 최소화함. 따라서 포인트 클라우드의 낮은 해상도·불완전 텍스처 문제를 해결할 수 있음 ([arxiv.org](https://arxiv.org/abs/2003.08934)).
        
- **Q. Instant‑NGP가 학습 속도를 가속하는 핵심 기술은?**
    
    - **A.** 멀티 해시 그리드로 고주파 세부를 압축 표현하고, CUDA Warp‑level 병렬성을 활용한 Fully‑Fused MLP 구현으로 메모리 접근·컴퓨팅 효율을 극대화함 ([nvlabs.github.io](https://nvlabs.github.io/instant-ngp/)).
        
- **Q. 3D Gaussian Splatting의 장점·한계는?**
    
    - **A.** 렌더링을 가우시안 블렌딩으로 근사해 레이 마칭을 생략, 실시간 추론이 가능하나 가우시안 수가 많아 메모리 소요가 클 수 있음 ([repo-sam.inria.fr](https://repo-sam.inria.fr/fungraph/3d-gaussian-splatting/), [lemonde.fr](https://www.lemonde.fr/sciences/article/2024/05/08/une-technique-revolutionnaire-de-creation-de-scenes-en-3d_6232232_1650684.html)).
        
- **Q. SfM 과정에서 번들 조정(Bundle Adjustment)의 역할은?**
    
    - **A.** 모든 카메라 파라미터와 3D 포인트 위치를 동시에 최적화하여 관측 재투영 오류를 최소화, 전체 모델 일관성을 확보함 ([cseweb.ucsd.edu](https://cseweb.ucsd.edu/classes/fa23/cse252A-a/lec10.pdf)).
        
- **Q. NeRF가 로보틱스에 기여할 수 있는 구체 예시는?**
    
    - **A.** 로봇 매니퓰레이터가 NeRF 기반 공간 맵을 사용해 물체 포즈를 정밀 추정하고, 가려진 영역의 시즈닝(see‑through)으로 안전한 궤적을 계획할 수 있음 ([arxiv.org](https://arxiv.org/html/2405.01333v2)).
        

---

## 5. 더 읽어보기 (Further Reading)

- Ben Mildenhall et al., “NeRF: Representing Scenes as Neural Radiance Fields for View Synthesis,” SIGGRAPH 2020 ([arxiv.org](https://arxiv.org/abs/2003.08934))
- Thomas Müller et al., “Instant NGP,” NVIDIA Research Project Page ([nvlabs.github.io](https://nvlabs.github.io/instant-ngp/))
- Comprehensive NeRF Survey (2024) ([arxiv.org](https://arxiv.org/abs/2306.03000))
- 3D Gaussian Splatting Project (Inria, Max Planck) ([repo-sam.inria.fr](https://repo-sam.inria.fr/fungraph/3d-gaussian-splatting/))
- NeRFs in Robotics Survey (2025) ([arxiv.org](https://arxiv.org/html/2405.01333v2))
- COLMAP Docs & Release Notes (2025) ([colmap.github.io](https://colmap.github.io/), [github.com](https://github.com/colmap/colmap/releases))
- Hartley & Zisserman, _Multiple View Geometry in Computer Vision_ (2nd Ed.) ([r-5.org](https://www.r-5.org/files/books/computers/algo-list/image-processing/vision/Richard_Hartley_Andrew_Zisserman-Multiple_View_Geometry_in_Computer_Vision-EN.pdf))
- Monocular Metric Depth Estimation Survey (2025) ([arxiv.org](https://arxiv.org/html/2501.11841v2))