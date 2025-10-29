---
title: 정책 경사법과 PPO (Policy Gradient & PPO)
date: '2025-07-03'
tags: [강화학습, 정책 경사법, PPO, Actor-Critic]
difficulty: hard
---

# 정책 경사법과 PPO (Policy Gradient & PPO)

## 1. 핵심 개념 (Core Concept)

정책 경사법(Policy Gradient, PG)은 가치 함수(Value Function)를 학습하는 대신, 정책(Policy)을 직접적으로 최적화하는 강화학습 방법론입니다. 정책을 파라미터 `θ`를 가진 함수 `π_θ(a|s)`로 표현하고, 누적 보상의 기댓값을 최대화하는 방향으로 `θ`를 업데이트합니다. PPO(Proximal Policy Optimization)는 이러한 정책 경사법의 학습 불안정성 문제를 해결한 알고리즘으로, 정책 업데이트의 크기를 신뢰 영역(Trust Region) 내로 제한하여 안정적이면서도 효율적인 학습을 가능하게 합니다.

______________________________________________________________________

## 2. 상세 설명 (Detailed Explanation)

### 2.1 정책 경사법 (Policy Gradient)

가치 기반 방법(예: Q-러닝)이 최적 가치 함수를 찾고 그로부터 정책을 유도하는 간접적인 방식이라면, 정책 경사법은 정책 자체를 바로 학습하는 직접적인 방식입니다. 이는 연속적인 행동 공간(continuous action space)을 다루거나 확률적인 정책(stochastic policy)이 필요할 때 특히 유용합니다.

정책 경사법의 목표는 보상의 총합(Return) `G_t`의 기댓값을 최대화하는 목적 함수 `J(θ)`를 찾는 것입니다.
`J(θ) = E[G_t]`

이 목적 함수를 `θ`에 대해 미분하여 경사 상승법(Gradient Ascent)을 적용하면 정책을 업데이트할 수 있습니다. \*\*정책 경사 정리(Policy Gradient Theorem)\*\*에 따라 이 그래디언트는 다음과 같이 표현됩니다.

`∇_θ J(θ) ∝ E[G_t * ∇_θ log π_θ(a_t|s_t)]`

가장 기본적인 정책 경사법 알고리즘인 **REINFORCE**는 이 수식을 사용하여 매 에피소드가 끝난 후 정책을 업데이트합니다. 하지만 `G_t` 값의 분산(variance)이 매우 커서 학습이 불안정한 단점이 있습니다.

### 2.2 액터-크리틱 (Actor-Critic)

REINFORCE의 높은 분산 문제를 해결하기 위해 등장한 것이 액터-크리틱(Actor-Critic) 방법입니다.

- **액터 (Actor)**: 정책 `π_θ(a|s)`를 학습하며, 어떤 행동을 할지 결정합니다. (정책 경사법)
- **크리틱 (Critic)**: 가치 함수 `V(s)` 또는 `Q(s,a)`를 학습하며, 액터가 한 행동이 얼마나 좋았는지를 평가합니다. (가치 기반 방법)

크리틱은 `G_t` 대신 더 안정적인 가치 함수 추정치(예: 어드밴티지 함수 `A(s,a) = Q(s,a) - V(s)`)를 제공하여 그래디언트의 분산을 크게 줄여줍니다. 이로써 학습이 훨씬 안정적으로 진행됩니다.

### 2.3 PPO (Proximal Policy Optimization)

액터-크리틱 방법도 여전히 학습률(learning rate)에 민감하고 정책이 급격하게 변하면 학습이 발산할 수 있는 문제가 있습니다. TRPO(Trust Region Policy Optimization)는 정책 업데이트가 이전 정책에서 너무 멀리 벗어나지 않도록 제약 조건을 추가하여 이 문제를 해결하려 했지만, 계산이 매우 복잡했습니다.

PPO는 TRPO의 아이디어를 유지하면서 계산을 단순화한 알고리즘으로, 두 가지 주요 방법을 제안합니다.

1. **PPO-Clip**: 목적 함수를 수정하여 이전 정책 `π_θ_old`와 새로운 정책 `π_θ`의 비율 `r_t(θ)`가 특정 범위 `[1-ε, 1+ε]`를 벗어나지 않도록 클리핑(clipping)합니다. 이를 통해 정책이 한 번에 크게 변하는 것을 막아 학습 안정성을 확보합니다.

   `L^{CLIP}(θ) = E[min(r_t(θ)A_t, clip(r_t(θ), 1-ε, 1+ε)A_t)]`

1. **PPO-Penalty (or PPO-KL)**: KL 발산(KL Divergence)을 이용하여 두 정책 분포의 차이를 측정하고, 이 차이가 너무 커지지 않도록 목적 함수에 페널티 항을 추가합니다.

이 중 PPO-Clip 방식이 구현이 간단하고 성능이 뛰어나 널리 사용됩니다.

______________________________________________________________________

## 3. 예시 (Example)

### PPO-Clip의 목적 함수

PPO-Clip의 핵심 아이디어는 아래 목적 함수에 나타나 있습니다. `r_t(θ)`는 `π_θ(a_t|s_t) / π_θ_old(a_t|s_t)` 입니다.

```python
# PPO-Clip의 핵심 로직 의사코드
ratio = new_policy.prob(action) / old_policy.prob(action)
advantage = calculate_advantage(reward, value)

# 클리핑된 목적 함수
surr1 = ratio * advantage
surr2 = torch.clamp(ratio, 1 - clip_epsilon, 1 + clip_epsilon) * advantage
actor_loss = -torch.min(surr1, surr2).mean()

# 크리틱(가치 함수) 손실
critic_loss = F.mse_loss(new_value, target_value)

# 최종 손실
total_loss = actor_loss + critic_loss
```

### 사용 사례 (Use Case)

- **로보틱스**: 로봇 팔 제어, 보행 로봇 등 연속적인 행동 공간을 가진 문제에서 정교한 제어 정책을 학습하는 데 사용됩니다.
- **자율 주행**: 차량의 조향, 가속/감속 등 복잡한 운전 정책을 학습합니다.
- **게임 AI**: OpenAI Five가 Dota 2를 플레이하거나, AlphaStar가 StarCraft II를 플레이하는 등 복잡한 전략 게임에서 인간을 능가하는 AI를 만드는 데 핵심적인 역할을 했습니다.

______________________________________________________________________

## 4. 예상 면접 질문 (Potential Interview Questions)

- **Q. 가치 기반 방법(Q-러닝) 대비 정책 기반 방법(Policy Gradient)의 장단점은 무엇인가요?**
  - **A.** **장점**: 연속적인 행동 공간에 직접 적용할 수 있고, 확률적인 정책을 학습할 수 있어 탐험과 최적 정책 사이의 균형을 잘 맞출 수 있습니다. **단점**: 학습 과정의 분산이 커서 불안정하고, 지역 최적해(local optimum)에 수렴할 수 있습니다.
- **Q. PPO에서 'Proximal'의 의미는 무엇이며, 클리핑(clipping)은 어떤 역할을 하나요?**
  - **A.** 'Proximal'은 '가까운'이라는 뜻으로, 새로운 정책이 이전 정책에서 크게 벗어나지 않도록(가까운 위치에 있도록) 유지한다는 의미입니다. 클리핑은 정책 비율(ratio)이 특정 범위를 넘어설 경우, 그 값을 잘라내어 목적 함수에 반영함으로써 정책 업데이트의 크기를 강제로 제한하는 역할을 합니다. 이를 통해 학습 안정성을 크게 향상시킵니다.
- **Q. TRPO와 PPO의 관계에 대해 설명해주세요.**
  - **A.** PPO는 TRPO의 후속 연구로 볼 수 있습니다. 두 알고리즘 모두 정책 업데이트가 너무 크지 않도록 신뢰 영역(Trust Region)을 설정한다는 기본 아이디어를 공유합니다. 하지만 TRPO는 2차 미분을 사용하는 복잡한 최적화 문제를 풀어야 해서 계산 비용이 매우 높은 반면, PPO는 1차 미분 기반의 간단한 클리핑 기법을 사용하여 비슷한 효과를 내면서도 구현이 쉽고 계산 효율성이 훨씬 뛰어납니다.

______________________________________________________________________

## 5. 더 읽어보기 (Further Reading)

- [OpenAI Spinning Up: Policy Gradient](https://spinningup.openai.com/en/latest/algorithms/vpg.html)
- [Proximal Policy Optimization Algorithms (PPO) - 원 논문](https://arxiv.org/abs/1707.06347)
- [Hugging Face Deep RL Course: PPO](https://huggingface.co/learn/deep-rl-course/unit5/ppo)
