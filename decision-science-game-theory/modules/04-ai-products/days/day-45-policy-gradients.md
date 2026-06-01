*[Decision Science & Game Theory](../../../../README.md) · [Module 4 — Decision Science in AI Products](../4-overview.md) · Day 45 of 65*

# Day 45 — Policy Gradient Methods

> **Today's one idea:** Instead of learning a value function and deriving a policy from it, policy gradient methods directly optimise the policy parameters to maximise expected return — and the policy gradient theorem gives an exact formula for this gradient that is computable from sampled experience.
> **Reading time:** ~35 min · **Prereqs:** [Day 44](day-44-q-learning.md), [Day 43](day-43-markov-decision-processes.md)
> **Primary source for today:** Sutton, Richard S. et al. "Policy Gradient Methods for Reinforcement Learning with Function Approximation." *Advances in Neural Information Processing Systems* 12 (NIPS 1999). Also: Schulman, John et al. "Proximal Policy Optimization Algorithms." arXiv:1707.06347, 2017.

---

## The Hook

Q-learning has a structural limitation: the policy is always *implicit* — defined as argmax of the Q-function over actions. This forces actions to be discrete (you can't take an argmax over a continuous space) and the policy to be deterministic (argmax always picks one action).

Many real problems have continuous action spaces. A robot's joint angles are continuous. An ad bidding system sets a continuous bid. A dialogue system generating a sentence samples from a continuous distribution over vocabulary. For these, Q-learning fails structurally.

Policy gradient methods sidestep this by directly parameterising the policy π_θ(a|s) and optimising θ to maximise expected return. This is pure optimisation — the policy is the model, and gradient ascent finds the best one.

The same mathematical framework underlies **Proximal Policy Optimisation (PPO)** — the algorithm used to fine-tune GPT-4, Claude, Gemini, and nearly every major language model with RLHF. Understanding policy gradients is understanding how AI alignment training works at the algorithmic level.

---

## Building the Intuition

### The policy gradient setup

**Parameterised policy:** A neural network (weights = θ) that takes a state as input and outputs a probability distribution over actions. Sampling from this distribution is how the agent acts.

**Objective:** Maximise the expected cumulative discounted reward of trajectories the policy generates — averaged over all the random trajectories the policy might produce.

**Goal:** Find the weights θ that maximise this objective using gradient ascent: repeatedly nudge θ in the direction that increases expected return.

**The challenge:** The objective depends on the environment's transition probabilities — which we don't know. We need a way to estimate the gradient from sampled trajectories alone, without knowing how the environment works.

---

### The policy gradient theorem

**The fundamental result (Williams 1992; Sutton et al. 1999):**

The gradient of expected return equals the expected sum, across all time steps, of: (the gradient of the log-probability of the action taken at that step) multiplied by (the total return from that step onward).

Written compactly: **∇_θ J(θ) = E [ Σ_t ∇_θ log π_θ(aₜ|sₜ) · G_t ]**

**Why this formula works:**

The key trick: the environment's transition probabilities cancel out during derivation. The gradient contains only the gradient of the log-probability of the action — which we can compute because we defined the policy. The dynamics of the world disappear from the formula.

**Intuitive interpretation:**

- The log-probability gradient tells you the direction in weight-space that makes a given action more or less likely.
- The return G_t tells you whether the action was worth taking.

**The update rule:** Increase the probability of actions that led to high returns; decrease the probability of actions that led to low returns. This is exactly the "reinforce what works" intuition — formalised.

---

### REINFORCE: the vanilla algorithm

**Algorithm:**

```
for each episode:
  1. Run the current policy to generate a full trajectory.
  2. For each time step t:
       Compute the discounted return from t to end of episode (G_t).
       Update weights: θ ← θ + α · G_t · ∇_θ log π_θ(aₜ|sₜ)
```

To reduce noise, average the gradient estimate over N sampled episodes rather than updating after each one.

**Problem: high variance.** G_t includes rewards from all future steps — a noisy quantity. A lucky episode with a high total return will reinforce every action taken, even bad ones. Training with REINFORCE alone is slow and unstable.

---

### Reducing variance: baselines and advantages

**Key insight:** Subtracting any state-dependent baseline from G_t does not bias the gradient estimate (the math works out because the baseline's contribution averages to zero). But it can dramatically reduce variance.

**With a baseline:** Replace G_t with (G_t − baseline), where the baseline is the value function V(sₜ) — the expected return from that state under the current policy. The difference is the **advantage function**: how much better was the action taken than the average action from that state?

> **Advantage = actual return − expected return = Q(s, a) − V(s)**

Positive advantage: this action was better than average → increase its probability.  
Negative advantage: this action was worse than average → decrease its probability.

This is the **actor-critic framework**: the "actor" (the policy) takes gradient steps guided by advantage estimates provided by the "critic" (a separately learned value function).

---

### Proximal Policy Optimization (PPO): practical policy gradients

**The problem with raw policy gradients:** Large gradient steps can dramatically change the policy, causing it to collapse or diverge. The new policy might explore entirely different regions of action space, invalidating the collected experience.

**PPO's solution (Schulman et al. 2017):** Clip the policy update to prevent large changes.

**PPO-Clip objective:**

For each action taken, compute the ratio r = (new policy's probability of this action) / (old policy's probability of this action). This ratio measures how much the policy has changed.

PPO's update clips this ratio to the range [1−ε, 1+ε] (with ε ≈ 0.2), then multiplies by the advantage estimate. It takes the minimum of the clipped and unclipped versions:

- If the advantage is positive (action was good): increase the action's probability, but cap the ratio at (1+ε) — don't overshoot.
- If the advantage is negative (action was bad): decrease the action's probability, but floor the ratio at (1−ε) — don't crash.

This prevents the policy from changing too drastically in a single update, keeping training stable.

**Why PPO is so widely used:**

- Simple to implement (a few lines of code beyond vanilla policy gradients).
- Robust to hyperparameter choices.
- Works across a vast range of tasks: games, robotics, language models.
- The RLHF training loop for ChatGPT, Claude, and Gemini uses PPO as the RL optimiser.

---

### The policy gradient theorem: formal derivation sketch

The expected return can be written as an average over states the policy visits, weighted by how often it visits them — and over actions, weighted by the policy's action probabilities at each state.

Taking the gradient with respect to policy weights: only the action probabilities depend on θ, not the state distribution or Q-values (to first order). Applying the **log-derivative trick** — the gradient of a probability equals that probability times the gradient of its logarithm — converts the gradient into an expectation over (state, action) pairs sampled from the policy:

**∇_θ J(θ) = E_{state, action ~ π_θ} [ ∇_θ log π_θ(action|state) · Q(state, action) ]**

**Key:** The environment dynamics — how the world transitions between states — do not appear explicitly in this gradient. They are absorbed into the distribution over states that the policy visits, which we estimate simply by running the policy and collecting trajectories. The gradient is fully computable from rollouts.

---

### Policy gradients in RLHF

RLHF (Reinforcement Learning from Human Feedback) is the dominant technique for aligning large language models with human preferences. The pipeline:

**Step 1 — Supervised Fine-Tuning (SFT):**

Train the base language model on high-quality human demonstrations. This produces a good initial policy π_SFT.

**Step 2 — Reward Model Training:**

Collect pairs of model outputs (y₁, y₂) from prompt x. Have humans indicate which output they prefer. Train a reward model r_φ(x, y) to predict human preference. This is the mechanism design step: humans design the reward function through their preferences.

**Step 3 — PPO Fine-Tuning:**

Use PPO to fine-tune the language model to maximise the reward model's scores, while penalising it for drifting too far from the SFT baseline:

> **Maximise: reward model score − β × (KL divergence from SFT policy)**

The KL penalty — which measures how different the fine-tuned policy is from the original — prevents the model from optimising the reward signal in degenerate ways (e.g., generating gibberish that happens to score well). β controls the trade-off: larger β = stay closer to the original; smaller β = pursue higher reward more aggressively.

**The policy gradient at work:**

Each token generated by the LM is an "action." The sequence of tokens is the "trajectory." The reward model's score at the end of the sequence (plus token-level KL penalties) is the "return." PPO updates the policy to increase the probability of sequences that the reward model scores highly.

This is a language model viewed through the RL lens: the LM is the policy; the vocabulary is the action space; the conversation history is the state; human preference is the reward signal.

---

## Where It Breaks / What It Is Not

**High variance is the fundamental challenge.** Even with baselines and advantage functions, policy gradient estimates are noisy. Training requires many samples and careful hyperparameter tuning. This is the key advantage of Q-learning in discrete settings — lower variance through bootstrapping.

**On-policy learning is sample inefficient.** Basic policy gradients require on-policy samples — generated by the current policy. After each update, old samples are discarded. Importance sampling can reuse old samples, but is only effective for small policy changes (hence PPO's clipping). Off-policy policy gradient methods (like DDPG, SAC) use a replay buffer but require continuous action spaces.

**The policy gradient theorem assumes stationary distributions.** The derivation uses d^π(s), the stationary state distribution. In practice, episodes have finite horizon and the distribution is non-stationary. The theorem approximately holds for long episodes or discounted problems.

**PPO's clipping is a heuristic.** The ε = 0.2 clipping threshold works well empirically but has no formal optimality guarantee. Trust Region Policy Optimisation (TRPO) provides formal bounds using KL-divergence constraints — PPO approximates TRPO while being simpler to implement.

---

## Try It Yourself

**Exercise 1 — Policy gradient update by hand (10 min)**

A 2-action policy: π_θ(Left|s) = σ(θ) = 1/(1+e^{-θ}), π_θ(Right|s) = 1 − σ(θ).

θ = 0 (equal probabilities, π(Left) = π(Right) = 0.5).

Episode 1: took action Left, got return G = +3.
Episode 2: took action Right, got return G = −1.

Compute one REINFORCE gradient update (averaged over both episodes). α = 0.1.

<details>
<summary>Worked answer</summary>

Gradient of log-probability:
```
∇_θ log π(Left|s) = ∂/∂θ log σ(θ) = 1 − σ(θ) = 0.5  [at θ=0]
∇_θ log π(Right|s) = ∂/∂θ log(1−σ(θ)) = −σ(θ) = −0.5  [at θ=0]
```

Policy gradient estimate:
```
∇_θ J ≈ (1/2) × [(+3) × 0.5 + (−1) × (−0.5)]
       = (1/2) × [1.5 + 0.5]
       = (1/2) × 2.0 = 1.0
```

Update:
```
θ ← 0 + 0.1 × 1.0 = 0.1
```

After update: π(Left|s) = σ(0.1) ≈ 0.525. The policy has shifted slightly toward Left (which had a positive return), as expected. Right became slightly less probable (had negative return).

</details>

---

**Exercise 2 — Advantage function (5 min)**

State s. Action a taken. Return G_t = 8. Critic estimates V(s) = 5.

a) What is the advantage estimate Â(s, a)?
b) What does a positive advantage mean for the policy update?
c) If instead G_t = 3 (with V(s) = 5), what would the advantage be and what would happen to the probability of action a?

<details>
<summary>Answer</summary>

**a)** Â(s, a) = G_t − V(s) = 8 − 5 = **+3**. This action led to 3 units better return than average.

**b)** Positive advantage → increase the probability of action a. The policy gradient update for a will be positive, pushing θ to make π_θ(a|s) larger.

**c)** Â(s, a) = 3 − 5 = **−2**. Negative advantage → **decrease** the probability of action a. The policy was better off taking a different action in state s.

</details>

---

**Exercise 3 — RLHF design (15 min)**

You are designing the RLHF pipeline for a customer service AI assistant. The assistant must be helpful, accurate, and appropriately empathetic.

a) What is the policy (π_θ)? What are the "actions"?
b) How would you collect preference data for training the reward model? What comparison pairs would you show human raters?
c) What behaviours might the PPO-optimised policy exhibit that violate your intent? (Reward hacking — give 2 specific examples.)
d) How would the KL penalty (β · KL(π_θ || π_SFT)) prevent these failure modes? What are the limits of this protection?

---

## Connect It Back

Policy gradients provide the algorithmic backbone for training AI systems with human feedback — connecting the abstract machinery of RL (trajectory sampling, gradient estimation, bootstrapped advantage) to the concrete practice of aligning language models with human preferences. PPO is not exotic research — it runs inside the fine-tuning pipeline of nearly every deployed frontier AI model.

Tomorrow (Day 46) addresses **reward design** — the mechanism design problem underlying all of RL: how do you specify a reward function that makes the RL agent do what you actually want? This is where Goodhart's Law, the alignment problem, and mechanism design all converge.

**The question you should be able to answer now:**
*What does ∇_θ log π_θ(aₜ|sₜ) represent geometrically, and why does multiplying it by Gₜ create an update that increases the probability of rewarding actions?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Sutton et al. "Policy Gradient Methods for Reinforcement Learning with Function Approximation." *NIPS 1999*. Read Sections 1–3 (pages 1057–1061). Section 1 motivates direct policy parameterisation; Section 2 states and proves the policy gradient theorem; Section 3 gives the actor-critic algorithm. The proof of the theorem (Theorem 1) is 15 lines of algebra — follow it carefully.

**If you want the deep version:**

1. Schulman, John et al. "Proximal Policy Optimization Algorithms." arXiv:1707.06347, 2017. — The PPO paper. Read Sections 1–4 (about 5 pages). Section 3 derives the clipped objective; Section 4 describes the full algorithm. The ablation experiments in Section 7 show why clipping is more robust than alternative approaches.

2. Ouyang, Long et al. "Training language models to follow instructions with human feedback." *Advances in Neural Information Processing Systems* 35 (NeurIPS 2022). arXiv:2203.02155. — The InstructGPT paper describing OpenAI's RLHF pipeline. Sections 2–3 describe the SFT, reward model, and PPO stages with specific implementation details. Required reading for understanding how policy gradients are applied to language model alignment.

3. Williams, Ronald J. "Simple statistical gradient-following algorithms for connectionist reinforcement learning." *Machine Learning*, 8(3–4):229–256, 1992. — The original REINFORCE paper. Section 2 derives the gradient estimator; Section 3 proves it is unbiased. The log-derivative trick (called the "REINFORCE trick" here) is explained with unusual clarity.


---

← [Day 44 — Q-Learning & Temporal Difference Methods](day-44-q-learning) &nbsp;|&nbsp; [Day 46 — Reward Design & Goodhart's Law →](day-46-reward-design)
