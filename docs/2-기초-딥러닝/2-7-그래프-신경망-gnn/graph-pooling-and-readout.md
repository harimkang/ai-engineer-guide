---
title: "그래프 풀링 & 읽기 연산"
date: "2025-07-03"
tags: ["딥러닝", "GNN"]
difficulty: "medium"
---

# 그래프 풀링 & 읽기 연산

## 1. 핵심 개념 (Core Concept)

그래프 풀링(Graph Pooling)과 읽기 연산(Readout)은 그래프 신경망(GNN)에서 그래프 수준의 표현(Graph-level representation)을 생성하는 데 사용되는 핵심 기법입니다. 이는 노드 수준의 특징을 통합하여 전체 그래프의 특성을 요약하고, 그래프 분류나 회귀와 같은 태스크에 활용됩니다.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 그래프 풀링 (Graph Pooling)
그래프 풀링은 그래프의 노드 수를 줄이면서 중요한 정보를 보존하는 과정입니다. 이는 컨볼루션 신경망(CNN)의 풀링 계층과 유사하게 작동하지만, 그래프의 불규칙한 구조를 고려해야 합니다. 주요 그래프 풀링 기법은 다음과 같습니다:

*   **Top-K 풀링 (Top-K Pooling)**: 각 노드에 대해 학습 가능한 스코어(score)를 계산하고, 스코어가 높은 상위 K개의 노드만 선택하여 다음 계층으로 전달합니다. 선택되지 않은 노드들은 버려집니다.
*   **DiffPool (Differentiable Pooling)**: 각 계층에서 노드들을 클러스터로 그룹화하고, 각 클러스터를 새로운 노드로 표현합니다. 이 과정은 미분 가능하여 엔드-투-엔드 학습이 가능합니다.
*   **SAGPool (Self-Attention Graph Pooling)**: 각 노드의 특징과 그래프 구조를 기반으로 셀프 어텐션 메커니즘을 사용하여 중요한 노드를 식별하고 풀링합니다.
*   **EdgePool (Edge Pooling)**: 노드를 풀링하는 대신, 엣지를 풀링하여 그래프를 축소합니다. 유사한 엣지를 병합하거나 중요도가 낮은 엣지를 제거하는 방식입니다.

### 2.2 읽기 연산 (Readout Functions)
읽기 연산은 GNN의 마지막 계층에서 모든 노드의 특징을 통합하여 단일 그래프 수준의 특징 벡터를 생성하는 함수입니다. 이는 그래프 분류, 그래프 회귀 등 그래프 전체의 특성을 예측하는 태스크에 필수적입니다. 주요 읽기 연산은 다음과 같습니다:

*   **평균 풀링 (Mean Pooling)**: 모든 노드 특징 벡터의 평균을 계산합니다. 가장 간단한 방법 중 하나입니다.
*   **최대 풀링 (Max Pooling)**: 모든 노드 특징 벡터에서 각 차원별 최댓값을 선택합니다.
*   **합계 풀링 (Sum Pooling)**: 모든 노드 특징 벡터의 합계를 계산합니다.
*   **어텐션 기반 읽기 (Attention-based Readout)**: 각 노드에 어텐션 가중치를 부여하여 중요한 노드의 특징에 더 큰 가중치를 주어 통합합니다. 이는 그래프의 특정 부분에 집중하여 특징을 추출할 수 있게 합니다.
*   **Set2Set**: 순환 신경망(RNN)을 사용하여 노드 특징들을 순서에 상관없이 통합하는 방법입니다.

---

## 3. 예시 (Example)

### 코드 예시 (Python)
'''python
# PyTorch Geometric을 이용한 그래프 풀링 및 읽기 연산 예시
import torch
import torch.nn.functional as F
from torch_geometric.nn import GCNConv, global_mean_pool, TopKPooling
from torch_geometric.data import Data

# 예시 그래프 데이터
# 5개 노드, 각 노드당 16차원 특징
x = torch.randn(5, 16)
# 엣지 인덱스
edge_index = torch.tensor([
    [0, 1], [1, 0],
    [1, 2], [2, 1],
    [2, 3], [3, 2],
    [3, 4], [4, 3]
], dtype=torch.long).t().contiguous()

# 배치 정보 (단일 그래프이므로 모든 노드가 같은 배치에 속함)
batch = torch.zeros(5, dtype=torch.long)

data = Data(x=x, edge_index=edge_index, batch=batch)

class GNNWithPoolingAndReadout(torch.nn.Module):
    def __init__(self, in_channels, hidden_channels, out_channels):
        super().__init__()
        self.conv1 = GCNConv(in_channels, hidden_channels)
        # Top-K 풀링 계층 (예: 5개 노드 중 3개 선택)
        self.pool1 = TopKPooling(hidden_channels, ratio=0.6) # ratio=3/5 = 0.6
        self.conv2 = GCNConv(hidden_channels, out_channels)

    def forward(self, data):
        x, edge_index, batch = data.x, data.edge_index, data.batch

        x = self.conv1(x, edge_index)
        x = F.relu(x)

        # 풀링 적용
        x, edge_index, _, batch, _, _ = self.pool1(x, edge_index, None, batch)

        x = self.conv2(x, edge_index)
        x = F.relu(x)

        # 읽기 연산 (평균 풀링)
        # global_mean_pool은 각 그래프의 노드 특징을 평균하여 그래프 임베딩 생성
        graph_embedding = global_mean_pool(x, batch)
        return graph_embedding

# 모델 초기화
model = GNNWithPoolingAndReadout(in_channels=16, hidden_channels=32, out_channels=64)

# 그래프 임베딩 얻기
graph_representation = model(data)

print("Graph Representation (after pooling and readout):\n", graph_representation)
'''

### 사용 사례 (Use Case)
*   **그래프 분류**: 분자 구조의 독성 예측, 소셜 네트워크의 사기 그래프 탐지.
*   **그래프 회귀**: 화학 물질의 특정 물리적 특성 예측.
*   **그래프 유사도 측정**: 두 그래프가 얼마나 유사한지 비교.
*   **그래프 요약 및 시각화**: 복잡한 그래프를 더 작은 크기로 축소하여 분석 및 시각화를 용이하게 합니다.

---

## 4. 예상 면접 질문 (Potential Interview Questions)

*   **Q. 그래프 풀링과 읽기 연산의 목적은 무엇인가요?**
    *   **A.** 그래프 풀링은 그래프의 노드 수를 줄여 계산 효율성을 높이고, 계층적 특징을 추출하는 데 사용됩니다. 읽기 연산은 GNN의 마지막 계층에서 모든 노드의 특징을 통합하여 그래프 전체를 대표하는 단일 특징 벡터를 생성하는 데 사용됩니다.
*   **Q. Top-K 풀링과 DiffPool의 주요 차이점은 무엇인가요?**
    *   **A.** Top-K 풀링은 각 노드에 스코어를 부여하여 상위 K개의 노드만 선택하는 반면, DiffPool은 노드들을 클러스터로 그룹화하고 각 클러스터를 새로운 노드로 표현합니다. DiffPool은 미분 가능하여 엔드-투-엔드 학습이 가능하며, 계층적인 그래프 표현 학습에 더 적합합니다.
*   **Q. 그래프 분류 태스크에서 읽기 연산이 왜 중요한가요? 몇 가지 읽기 연산 방법을 설명해 주세요.**
    *   **A.** 그래프 분류는 그래프 전체의 특성을 예측하는 태스크이므로, 노드 수준의 특징을 그래프 수준의 특징으로 통합하는 읽기 연산이 필수적입니다. 주요 방법으로는 모든 노드 특징의 평균을 취하는 평균 풀링, 최댓값을 취하는 최대 풀링, 합계를 취하는 합계 풀링, 그리고 중요한 노드에 가중치를 부여하는 어텐션 기반 읽기 등이 있습니다.

---

## 5. 더 읽어보기 (Further Reading)

*   [Graph Pooling: Methods and Applications](https://arxiv.org/abs/2007.00481)
*   [DiffPool 논문: Hierarchical Graph Representation Learning with Differentiable Pooling](https://arxiv.org/abs/1806.08804)
*   [SAGPool 논문: Self-Attention Graph Pooling](https://arxiv.org/abs/1904.08082)
*   [PyTorch Geometric 공식 문서](https://pytorch-geometric.readthedocs.io/en/latest/)
*   [Graph Neural Networks: A Review of Methods and Applications](https://arxiv.org/abs/1812.08434)
