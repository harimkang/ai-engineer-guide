---
title: "하이퍼파라미터 탐색 (Optuna 예시)"
date: "2025-07-03"
tags: ["딥러닝", "학습", "최적화", "Python", "Optuna"]
difficulty: "medium"
---

# 하이퍼파라미터 탐색 (Hyperparameter Search)

## 1. 핵심 개념 (Core Concept)

하이퍼파라미터 탐색은 모델의 학습 과정에 영향을 미치는 하이퍼파라미터(예: 학습률, 배치 크기, 은닉층의 수)들의 최적 조합을 찾는 과정을 의미함. 이는 모델의 성능을 극대화하기 위한 필수적인 단계로, 자동화된 탐색 알고리즘을 통해 효율적으로 수행될 수 있음.

---

## 2. 상세 설명 (Detailed Explanation)

### 2.1 탐색 알고리즘 종류

다양한 하이퍼파라미터 탐색 알고리즘이 있으며, 각각의 장단점이 뚜렷함.

| 알고리즘 | 설명 | 장점 | 단점 |
| :--- | :--- | :--- | :--- |
| **Grid Search** | 사용자가 지정한 하이퍼파라미터 값들의 모든 조합을 시도함. | 간단하고 모든 조합을 탐색함. | 탐색 공간이 커지면 계산 비용이 기하급수적으로 증가함. |
| **Random Search** | 지정된 범위 내에서 하이퍼파라미터 값들을 무작위로 샘플링하여 탐색함. | Grid Search보다 계산 효율성이 높고, 중요한 파라미터에 더 많은 탐색 기회를 제공할 수 있음. | 최적해를 찾으리라는 보장이 없으며, 무작위성에 의존함. |
| **Bayesian Optimization** | 이전 탐색 결과를 바탕으로 다음 탐색할 하이퍼파라미터 지점을 확률적으로 추정하여 탐색함. Surrogate 모델(보통 Gaussian Process)을 사용하여 목적 함수를 근사함. | 적은 시도 횟수로 최적의 하이퍼파라미터를 찾을 가능성이 높음. | 구현이 복잡하고, 병렬 처리가 어려울 수 있음. |
| **Tree-structured Parzen Estimator (TPE)** | Optuna 등에서 사용하는 베이지안 최적화 기반 알고리즘. 하이퍼파라미터들의 분포를 모델링하여 유망한 영역을 탐색함. | 병렬화에 용이하고, 조건부 하이퍼파라미터 공간을 잘 처리함. | 다른 베이지안 최적화 기법과 유사한 단점을 가질 수 있음. |

### 2.2 Optuna 프레임워크

Optuna는 Python 기반의 자동 하이퍼파라미터 최적화 프레임워크로, TPE 알고리즘을 기본으로 사용함. 사용하기 쉬운 API와 다양한 시각화 기능을 제공하여 널리 사용됨.

*   **Define-by-Run API**: 사용자가 일반적인 Python 코드처럼 동적으로 탐색 공간을 구성할 수 있음.
*   **Pruning**: 성능이 좋지 않은 trial을 조기에 중단시켜 탐색 시간을 단축시킴.
*   **Visualization**: 탐색 과정, 파라미터 중요도 등을 시각적으로 분석할 수 있는 도구를 제공함.

---

## 3. 예시 (Example)

### 코드 예시 (Optuna & PyTorch)

PyTorch 모델의 학습률(learning rate)과 드롭아웃 비율(dropout rate)을 Optuna를 사용하여 탐색하는 예시.

```python
import torch
import torch.nn as nn
import torch.optim as optim
import optuna

# 1. 모델 및 데이터 (간단한 예시)
X_train, y_train = torch.randn(100, 10), torch.randn(100, 1)
X_valid, y_valid = torch.randn(50, 10), torch.randn(50, 1)

def get_model(trial):
    # 동적으로 드롭아웃 비율을 제안받음
    dropout_rate = trial.suggest_float("dropout_rate", 0.0, 0.5)
    return nn.Sequential(
        nn.Linear(10, 32),
        nn.ReLU(),
        nn.Dropout(dropout_rate),
        nn.Linear(32, 1)
    )

# 2. Objective 함수 정의
def objective(trial):
    # 하이퍼파라미터 제안
    lr = trial.suggest_float("lr", 1e-5, 1e-1, log=True)
    
    model = get_model(trial)
    optimizer = optim.Adam(model.parameters(), lr=lr)
    criterion = nn.MSELoss()

    # 학습 루프
    for step in range(100):
        model.train()
        optimizer.zero_grad()
        output = model(X_train)
        loss = criterion(output, y_train)
        loss.backward()
        optimizer.step()

        # Pruning (조기 종료)
        model.eval()
        with torch.no_grad():
            val_loss = criterion(model(X_valid), y_valid)
        trial.report(val_loss, step)
        if trial.should_prune():
            raise optuna.exceptions.TrialPruned()

    return val_loss

# 3. Study 생성 및 최적화 실행
study = optuna.create_study(direction="minimize", pruner=optuna.pruners.MedianPruner())
study.optimize(objective, n_trials=100)

# 4. 결과 확인
print("Best trial:")
trial = study.best_trial
print(f"  Value: {trial.value}")
print("  Params: ")
for key, value in trial.params.items():
    print(f"    {key}: {value}")
```

---

## 4. 예상 면접 질문 (Potential Interview Questions)

*   **Q. Grid Search와 Random Search의 장단점을 비교하고, 어떤 상황에서 Random Search가 더 효과적일 수 있는지 설명해주세요.**
    *   **A.** Grid Search는 모든 조합을 탐색하여 재현성이 높지만, 차원의 저주로 인해 계산 비용이 매우 큽니다. 반면 Random Search는 무작위로 점을 선택하여 탐색하므로 계산 효율성이 높습니다. 특히, 모델 성능에 큰 영향을 미치는 소수의 하이퍼파라미터가 존재할 때, Random Search는 해당 파라미터의 다양한 값을 탐색할 가능성이 높아져 Grid Search보다 더 빠르게 좋은 조합을 찾을 수 있습니다.
*   **Q. 베이지안 최적화(Bayesian Optimization)는 어떻게 동작하며, 다른 탐색 방법에 비해 어떤 장점이 있나요?**
    *   **A.** 베이지안 최적화는 Surrogate 모델(대리 모델)을 사용하여 실제 목적 함수(예: 검증 손실)를 근사하고, Acquisition 함수를 통해 다음으로 탐색할 가장 유망한 하이퍼파라미터 지점을 결정합니다. 이전에 평가된 지점들의 정보를 활용하기 때문에, Grid Search나 Random Search보다 훨씬 적은 시도 횟수로 최적점에 도달할 수 있는 장점이 있습니다.
*   **Q. Optuna와 같은 라이브러리에서 Pruning(가지치기) 기능은 왜 중요한가요?**
    *   **A.** Pruning은 학습 초기 단계에서 성능이 좋지 않을 것으로 예상되는 trial(시도)을 조기에 중단시키는 기능입니다. 이를 통해 유망하지 않은 하이퍼파라미터 조합에 소요되는 계산 자원을 절약하고, 전체 탐색 시간을 크게 단축시켜 더 많은 trial을 시도하거나 더 넓은 탐색 공간을 효율적으로 탐색할 수 있게 해줍니다.

---

## 5. 더 읽어보기 (Further Reading)

*   [Optuna 공식 문서](https://optuna.readthedocs.io/en/stable/)
*   [Random Search for Hyper-Parameter Optimization (Bergstra & Bengio, 2012)](http://www.jmlr.org/papers/volume13/bergstra12a/bergstra12a.pdf)
*   [A Tutorial on Bayesian Optimization (UCL)](https://www.cs.ucl.ac.uk/fileadmin/UCL-CS/research/Research_Notes/RN_11_01.pdf)