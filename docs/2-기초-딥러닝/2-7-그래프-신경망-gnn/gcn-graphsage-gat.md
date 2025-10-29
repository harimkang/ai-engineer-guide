---
title: GCN, GraphSAGE, GAT
date: '2025-07-03'
tags: [딥러닝, GNN]
difficulty: medium
---

# GCN, GraphSAGE, GAT

## 1. 핵심 개념 (Core Concept)

GCN(Graph Convolutional Networks), GraphSAGE, GAT(Graph Attention Networks)는 그래프 구조 데이터를 처리하기 위한 대표적인 그래프 신경망(GNN) 모델입니다. 이들은 노드 특징과 그래프 구조를 활용하여 노드 분류, 링크 예측, 그래프 분류 등 다양한 그래프 관련 태스크를 수행합니다.

______________________________________________________________________

## 2. 상세 설명 (Detailed Explanation)

### 2.1 GCN (Graph Convolutional Networks)

GCN은 그래프 데이터를 위한 컨볼루션 연산을 정의하여, 각 노드의 특징을 이웃 노드의 특징과 집계(aggregate)하여 업데이트합니다. 이는 스펙트럴 그래프 이론(Spectral Graph Theory)에 기반하며, 그래프 라플라시안(Graph Laplacian)을 사용하여 그래프 컨볼루션 필터를 정의합니다.

- **작동 방식**: 각 노드는 자신의 특징과 이웃 노드의 특징을 가중 평균하여 새로운 특징 표현을 생성합니다. 이때 가중치는 그래프의 연결성(인접 행렬)과 노드의 차수(degree)에 따라 결정됩니다.
- **수식**: $H^{(l+1)} = \\sigma(\\tilde{D}^{-\\frac{1}{2}}\\tilde{A}\\tilde{D}^{-\\frac{1}{2}}H^{(l)}W^{(l)})$ (여기서 $\\tilde{A} = A + I$, $\\tilde{D}$는 $\\tilde{A}$의 차수 행렬, $H^{(l)}$은 $l$번째 계층의 노드 특징, $W^{(l)}$은 학습 가능한 가중치 행렬, $\\sigma$는 활성화 함수)
- **장점**: 간단하고 효율적이며, 노드 분류와 같은 태스크에서 좋은 성능을 보입니다.
- **단점**: 고정된 그래프 구조에 의존하며, inductive learning(새로운, 보지 못한 그래프에 대한 일반화)에 약합니다.

### 2.2 GraphSAGE (Graph Sample and Aggregate)

GraphSAGE는 GCN의 inductive learning 한계를 극복하기 위해 제안된 모델입니다. 각 노드의 임베딩을 학습할 때 전체 그래프를 사용하는 대신, 이웃 노드를 샘플링하고 집계하는 방식을 사용합니다. 이를 통해 대규모 그래프나 동적으로 변하는 그래프에서도 효율적으로 작동합니다.

- **작동 방식**: 각 노드는 자신의 이웃 노드들을 샘플링하고, 이 샘플링된 이웃들의 특징을 다양한 집계 함수(평균, LSTM, 풀링 등)를 사용하여 집계합니다. 이 집계된 정보와 자신의 특징을 결합하여 새로운 특징 표현을 생성합니다.
- **장점**: Inductive learning이 가능하며, 대규모 그래프에 적용하기 용이합니다.
- **단점**: 이웃 샘플링 방식에 따라 성능이 달라질 수 있습니다.

### 2.3 GAT (Graph Attention Networks)

GAT는 어텐션 메커니즘을 그래프에 도입하여, 각 노드가 이웃 노드로부터 정보를 집계할 때 중요한 이웃에 더 큰 가중치를 부여하는 방식입니다. 이는 이웃 노드 간의 중요도를 동적으로 학습하여 GCN의 고정된 가중치 문제를 해결합니다.

- **작동 방식**: 각 노드는 자신의 특징과 이웃 노드의 특징을 사용하여 어텐션 계수(attention coefficient)를 계산합니다. 이 어텐션 계수는 이웃 노드의 중요도를 나타내며, 이를 가중치로 사용하여 이웃 노드의 특징을 집계합니다.
- **장점**: 이웃 노드 간의 중요도를 동적으로 학습하여 모델의 표현력을 높이고, inductive learning이 가능합니다.
- **단점**: 어텐션 계산으로 인해 GCN보다 계산 비용이 높을 수 있습니다.

______________________________________________________________________

## 3. 예시 (Example)

### 코드 예시 (Python)

```python
# PyTorch Geometric을 이용한 GCN, GraphSAGE, GAT 구현 예시
import torch
import torch.nn.functional as F
from torch_geometric.nn import GCNConv, SAGEConv, GATConv
from torch_geometric.datasets import Planetoid
from torch_geometric.data import Data

# 데이터셋 로드 (Cora 논문 인용 네트워크)
dataset = Planetoid(root='/tmp/Cora', name='Cora')
data = dataset[0]

# GCN 모델
class GCN(torch.nn.Module):
    def __init__(self, in_channels, hidden_channels, out_channels):
        super().__init__()
        self.conv1 = GCNConv(in_channels, hidden_channels)
        self.conv2 = GCNConv(hidden_channels, out_channels)

    def forward(self, x, edge_index):
        x = self.conv1(x, edge_index)
        x = F.relu(x)
        x = F.dropout(x, training=self.training)
        x = self.conv2(x, edge_index)
        return F.log_softmax(x, dim=1)

# GraphSAGE 모델
class GraphSAGE(torch.nn.Module):
    def __init__(self, in_channels, hidden_channels, out_channels):
        super().__init__()
        self.conv1 = SAGEConv(in_channels, hidden_channels)
        self.conv2 = SAGEConv(hidden_channels, out_channels)

    def forward(self, x, edge_index):
        x = self.conv1(x, edge_index)
        x = F.relu(x)
        x = F.dropout(x, training=self.training)
        x = self.conv2(x, edge_index)
        return F.log_softmax(x, dim=1)

# GAT 모델
class GAT(torch.nn.Module):
    def __init__(self, in_channels, hidden_channels, out_channels, heads=1):
        super().__init__()
        self.conv1 = GATConv(in_channels, hidden_channels, heads=heads, dropout=0.6)
        self.conv2 = GATConv(hidden_channels * heads, out_channels, heads=1, concat=False, dropout=0.6)

    def forward(self, x, edge_index):
        x = F.dropout(x, p=0.6, training=self.training)
        x = self.conv1(x, edge_index)
        x = F.elu(x)
        x = F.dropout(x, p=0.6, training=self.training)
        x = self.conv2(x, edge_index)
        return F.log_softmax(x, dim=1)

# 모델 학습 예시 (GCN)
# model = GCN(dataset.num_node_features, 16, dataset.num_classes)
# optimizer = torch.optim.Adam(model.parameters(), lr=0.01, weight_decay=5e-4)

# model.train()
# for epoch in range(200):
#     optimizer.zero_grad()
#     out = model(data.x, data.edge_index)
#     loss = F.nll_loss(out[data.train_mask], data.y[data.train_mask])
#     loss.backward()
#     optimizer.step()

# model.eval()
# _, pred = model(data.x, data.edge_index).max(dim=1)
# correct = float(pred[data.test_mask].eq(data.y[data.test_mask]).sum().item())
# acc = correct / data.test_mask.sum().item()
# print(f'Accuracy: {acc:.4f}')
```

### 사용 사례 (Use Case)

- **노드 분류 (Node Classification)**: 소셜 네트워크에서 사용자의 관심사 예측, 논문 인용 네트워크에서 논문의 주제 분류.
- **링크 예측 (Link Prediction)**: 소셜 네트워크에서 친구 추천, 지식 그래프에서 관계 예측.
- **그래프 분류 (Graph Classification)**: 분자 구조의 특성 예측, 사기 거래 탐지.
- **추천 시스템**: 사용자-아이템 상호작용 그래프에서 사용자에게 아이템 추천.

______________________________________________________________________

## 4. 예상 면접 질문 (Potential Interview Questions)

- **Q. GCN, GraphSAGE, GAT의 주요 차이점은 무엇인가요?**
  - **A.** GCN은 고정된 그래프 구조에서 이웃 노드의 특징을 가중 평균하여 집계하는 반면, GraphSAGE는 이웃 노드를 샘플링하고 다양한 집계 함수를 사용하여 inductive learning을 가능하게 합니다. GAT는 어텐션 메커니즘을 도입하여 이웃 노드 간의 중요도를 동적으로 학습하여 집계합니다.
- **Q. Inductive learning이 GNN에서 왜 중요한가요? GraphSAGE는 이를 어떻게 해결하나요?**
  - **A.** Inductive learning은 모델이 학습 과정에서 보지 못한 새로운 노드나 그래프에 대해서도 일반화된 성능을 보이는 능력입니다. GCN은 고정된 그래프에 의존하여 inductive learning에 약하지만, GraphSAGE는 이웃 샘플링 및 집계 방식을 통해 새로운 노드의 임베딩을 효율적으로 생성하여 이 문제를 해결합니다.
- **Q. GAT에서 어텐션 메커니즘을 사용하는 이점은 무엇인가요?**
  - **A.** GAT는 어텐션 메커니즘을 통해 각 노드가 이웃 노드로부터 정보를 집계할 때, 이웃 노드들의 중요도를 동적으로 학습하여 가중치를 부여합니다. 이는 모델의 표현력을 높이고, 노드 간의 복잡한 관계를 더 잘 포착할 수 있게 합니다.

______________________________________________________________________

## 5. 더 읽어보기 (Further Reading)

- [GCN 논문: Semi-Supervised Classification with Graph Convolutional Networks](https://arxiv.org/abs/1609.02907)
- [GraphSAGE 논문: Inductive Representation Learning on Large Graphs](https://arxiv.org/abs/1706.02216)
- [GAT 논문: Graph Attention Networks](https://arxiv.org/abs/1710.10903)
- [PyTorch Geometric 공식 문서](https://pytorch-geometric.readthedocs.io/en/latest/)
- [그래프 신경망(GNN) 튜토리얼](https://distill.pub/2021/gnn-intro/)
