*[Decision Science & Game Theory](../../../../README.md) · [Module 4 — Decision Science in AI Products](../4-overview.md) · Day 47 of 65*

# Day 47 — Rest & Synthesise: The RL Toolkit

> **Today's one idea:** The RL toolkit — agent-environment loop, Bellman equations, TD learning, policy gradients, and reward design — forms a unified framework: every piece solves one sub-problem of how to make good sequential decisions under uncertainty.
> **Reading time:** ~25 min · **Prereqs:** [Day 41](day-41-reinforcement-learning.md) through [Day 46](day-46-reward-design.md)
> **Primary source for today:** Your notes from the past six days.

---

## How to Use This Day

No new concepts. No new symbols. Today has three jobs:

1. **See the shape** — understand how Days 41–46 fit together as a single framework, not six separate topics.
2. **Find your gaps** — twelve self-test questions, two per day, with collapsible answers. The gaps you find now are far cheaper to fix than the gaps you discover in Days 49–55.
3. **Clear up the confusions** — three things that trip up almost everyone on first encounter.

The six days you have just completed are the load-bearing core of Module 4. Every topic that follows — multi-agent RL, recommendation systems, RLHF, mechanism design in AI, alignment — assumes you can hold this toolkit in your head. An hour of consolidation here is worth three hours of re-reading later.

---

## The Shape of the Last Six Days

The six days form a hierarchy. Each layer takes the previous one as given and asks the next question.

```
┌─────────────────────────────────────────────────────────────────┐
│  REWARD DESIGN (Day 46)                                         │
│  What should the agent optimise? Goodhart, shaping, RLHF.      │
├─────────────────────────────────────────────────────────────────┤
│  POLICY GRADIENTS (Day 45)                                      │
│  Directly optimise π_θ. PPO clips the update for stability.    │
├─────────────────────────────────────────────────────────────────┤
│  Q-LEARNING / TD (Day 44)                                       │
│  Learn Q*(s,a) from experience. No model needed.               │
├─────────────────────────────────────────────────────────────────┤
│  MDP (Day 43)                                                   │
│  Formalise the structure: S, A, P, R, γ. Bellman equation.     │
├─────────────────────────────────────────────────────────────────┤
│  EXPLORATION vs. EXPLOITATION (Day 42)                          │
│  How to collect experience efficiently. UCB1, regret bounds.   │
├─────────────────────────────────────────────────────────────────┤
│  RL AS SEQUENTIAL DECISION-MAKING (Day 41)                      │
│  Environment emits (s, a, r, s'). Agent loops indefinitely.    │
└─────────────────────────────────────────────────────────────────┘
```

Read this diagram from the bottom up: the agent-environment loop (Day 41) is the raw problem. Exploration-exploitation (Day 42) is the first hard sub-problem — how to gather useful data. MDPs (Day 43) formalise the structure so we can reason about it. Q-learning (Day 44) solves it without a model. Policy gradients (Day 45) solve it when the action space is continuous or the policy must be stochastic. Reward design (Day 46) asks what "solving it" actually means.

Each layer is useless without the one below it. And none of them matter if the reward function is broken.

---

## Core Equations at a Glance

Six equations. One per day. If you can derive each from first principles — not just recite it — you own the material.

**Day 43 — Bellman optimality equation**

```
Q*(s,a) = R(s,a) + γ · Σ_{s'} P(s'|s,a) · max_{a'} Q*(s',a')
```

The value of taking action a in state s is the immediate reward plus the discounted value of acting optimally from every reachable next state. This is backward induction written as a fixed-point equation.

**Day 44 — Q-learning update (TD)**

```
Q(s,a) ← Q(s,a) + α · [r + γ · max_{a'} Q(s',a') − Q(s,a)]
```

The bracketed term is the **TD error** — the gap between the current estimate and the bootstrapped target. The learning rate α controls how aggressively the estimate moves. This converges to Q* without a model, from raw experience.

**Day 45 — Policy gradient theorem**

```
∇_θ J(θ) = E[ Σ_t ∇_θ log π_θ(aₜ|sₜ) · Gₜ ]
```

The gradient of expected return is the expected sum of log-probability gradients, weighted by the return from that timestep onward. Intuitively: reinforce actions that led to high returns; suppress actions that led to low returns.

**Day 45 — Advantage function**

```
A^π(s,a) = Q^π(s,a) − V^π(s)
```

The advantage measures how much better action a is than average in state s, under the current policy. Replacing Gₜ with Aₜ in the policy gradient reduces variance without introducing bias. This is what Actor-Critic methods compute.

**Day 45 — PPO-Clip objective**

```
L^CLIP = E[ min( rₜ(θ) · Âₜ,  clip(rₜ(θ), 1−ε, 1+ε) · Âₜ ) ]
```

where rₜ(θ) = π_θ(aₜ|sₜ) / π_θ_old(aₜ|sₜ) is the probability ratio. The clip prevents the new policy from moving too far from the old one in a single update — the critical stability mechanism behind LLM RLHF training.

**Day 46 — Potential-based reward shaping**

```
R'(s,a,s') = R(s,a,s') + γΦ(s') − Φ(s)
```

Adding a potential function Φ to the reward does not change the optimal policy — it cancels out over trajectories (telescoping sum). This is the one safe way to shape a reward: encode prior knowledge about which states are good without distorting the agent's objectives.

---

## Where Each Idea Came From

None of these ideas were invented for RL. Each has a lineage that runs through the earlier modules.

| RL concept | Earlier root | Where you saw it |
|---|---|---|
| Bellman equation | Backward induction | [Day 10](../../01-foundations/days/day-10-decision-trees.md) — decision trees rolled back from terminal nodes |
| Discount factor γ | δ patience in repeated games | [Day 21](../../02-game-theory/days/day-21-repeated-games.md) — cooperation threshold δ* = (T−R)/(T−P) |
| Exploration-exploitation | Value of information | [Day 9](../../01-foundations/days/day-09-value-of-information.md) — EVPI and EVSI as the price of a signal |
| Reward design | Mechanism design | [Day 30](../../03-mechanism-design/days/day-30-mechanism-design.md) — designer chooses rules, agents respond strategically |
| RLHF over-optimisation | Goodhart / principal-agent | [Day 27](../../02-game-theory/days/day-27-information-asymmetry.md) — hidden type, proxy measures, information rent |

These connections are not metaphors. Backward induction *is* the Bellman recursion — RL generalises it to cyclic state spaces. The discount factor *is* a patience parameter — the same algebra that makes δ* < 1 necessary for cooperation makes γ < 1 necessary for convergence. Reward design *is* mechanism design — the designer specifies a reward, the agent responds strategically, and the problem is to align the agent's best response with the designer's true objective.

When Module 4's second half feels new, look for the earlier concept underneath it. It is always there.

---

## Twelve-Question Self-Test

Two questions per day. Answer each before opening the collapsible answer. The goal is retrieval, not verification.

---

### Day 41 — RL as Sequential Decision-Making

**Q1.** Why does the RL agent discount future rewards by γ < 1, rather than summing them without discount? Give two distinct reasons — one mathematical, one economic.

<details>
<summary>Answer</summary>

**Mathematical reason:** Without discounting, the cumulative sum G = Σ rₜ may not converge for infinite-horizon problems — if rewards are bounded away from zero, the sum diverges. The discount factor γ ∈ [0, 1) guarantees convergence: G ≤ r_max / (1 − γ).

**Economic reason:** A reward now is worth more than a reward later — the same logic as the time-value of money, or δ in repeated games (Day 21). An agent that values the future less is more likely to exploit in the short term; higher γ means the agent cares more about long-run consequences, analogous to the cooperation threshold in the repeated Prisoner's Dilemma.

The two reasons reinforce each other: γ is both a convergence device and a model of temporal preference.

</details>

---

**Q2.** In the RL loop, the agent observes state sₜ, takes action aₜ, and receives reward rₜ₊₁ with next state sₜ₊₁. What does the Markov property assume about this loop — and why would violating it matter?

<details>
<summary>Answer</summary>

The **Markov property** assumes that the next state and reward depend only on the current state and action — not on the full history: P(sₜ₊₁, rₜ₊₁ | sₜ, aₜ) = P(sₜ₊₁, rₜ₊₁ | s₀, a₀, ..., sₜ, aₜ). The current state contains all relevant information about the past.

**Why violations matter:** If the Markov property fails, the optimal policy cannot be computed as a function of the current state alone — it would need to condition on history. This invalidates the Bellman equation (which is derived assuming Markov structure) and most tabular RL algorithms. In practice, violations are handled by augmenting the state (adding memory, using recurrent networks, or stacking recent observations), but the MDP formalism breaks down without the assumption.

</details>

---

### Day 42 — Exploration vs. Exploitation

**Q3.** The UCB1 formula for selecting an arm is:

```
a* = argmax_k [ Q̄_k(t) + c · √(ln t / Nₖ(t)) ]
```

What does the second term represent, and why does it shrink as Nₖ(t) grows?

<details>
<summary>Answer</summary>

The second term is an **exploration bonus** — it inflates the estimated value of an arm in proportion to uncertainty about that arm. It is derived from the Hoeffding inequality: if you have observed arm k only Nₖ(t) times out of t total rounds, the true mean lies within roughly √(ln t / Nₖ(t)) of the empirical mean with high probability.

**Why it shrinks with Nₖ:** As you pull arm k more often (Nₖ grows), your estimate of its mean becomes more precise — the confidence interval narrows. The agent's uncertainty about arm k decreases. With very few pulls, the bonus is large (explore this uncertain arm); with many pulls, the bonus is tiny (trust the estimate, exploit if it is high).

The logarithm of t in the numerator means exploration grows, but slowly — arms you haven't pulled recently get a nudge back into contention as time passes.

</details>

---

**Q4.** UCB1 achieves O(log T) regret, which is proved to be asymptotically optimal. What does this mean in plain English — and why can't an algorithm achieve zero regret?

<details>
<summary>Answer</summary>

**Plain English:** Over T rounds, UCB1 will fall behind the best arm by at most a multiple of log T in total reward. This grows, but slowly — much slower than linear (which would mean a constant fraction of rounds wasted on suboptimal arms). As T → ∞, the average per-round regret shrinks to zero.

**Why zero regret is impossible:** To know which arm is best, you must pull non-optimal arms a certain number of times — there is no shortcut. With K arms and unknown means, you need at least O(log T) pulls of each suboptimal arm just to distinguish it from the optimal arm with high confidence. Any algorithm that explores less will sometimes fail to identify the best arm and suffer linear regret. Lai and Robbins (1985) proved this lower bound; UCB1 matches it.

</details>

---

### Day 43 — Markov Decision Processes

**Q5.** Value iteration converges to Q* by repeatedly applying the Bellman operator. Why does it converge — what mathematical property guarantees this?

<details>
<summary>Answer</summary>

The Bellman optimality operator **T** is a **contraction mapping** under the infinity norm, with contraction factor γ:

```
‖T·Q − T·Q'‖_∞ ≤ γ · ‖Q − Q'‖_∞
```

By the Banach fixed-point theorem, any contraction mapping on a complete metric space has a unique fixed point, and iterating the mapping from any starting point converges to that fixed point at a geometric rate. Q* is the unique fixed point of T. Since γ < 1, each iteration brings Q closer to Q* by a factor of γ — convergence is guaranteed regardless of the initialisation.

This is why γ < 1 is required not just economically but algorithmically: with γ = 1, the operator is not contractive and convergence fails.

</details>

---

**Q6.** What is the difference between a model-based and a model-free RL algorithm? Which category does value iteration fall into, and which does Q-learning fall into?

<details>
<summary>Answer</summary>

A **model-based** algorithm requires explicit knowledge of the transition probabilities P(s'|s,a) and the reward function R(s,a). It plans by simulating the model — computing expected values analytically.

A **model-free** algorithm learns directly from experience — sampled transitions (s, a, r, s') — without knowing or learning the underlying transition probabilities. It does not simulate the environment; it updates estimates from the data it receives.

**Value iteration** (Day 43) is model-based: it requires the full transition matrix to compute the Bellman update exactly. **Q-learning** (Day 44) is model-free: it approximates the Bellman update using a single sampled transition (s, a, r, s') instead of the full expectation. This is what makes Q-learning applicable to real environments where the model is unknown.

</details>

---

### Day 44 — Q-Learning & Temporal Difference

**Q7.** Q-learning is called "off-policy." What does this mean, and why does it allow experience replay — while SARSA (an on-policy method) does not?

<details>
<summary>Answer</summary>

**Off-policy** means the algorithm learns the value of the *optimal policy* (target policy) while following a *different* policy to collect experience (behaviour policy). The Q-learning update uses max_{a'} Q(s', a') — it always bootstraps from the best possible next action, regardless of which action the agent actually took.

**On-policy** (SARSA) learns the value of the policy *currently being followed*. Its update uses Q(s', a'), where a' is the action the agent *actually takes* next — so the estimate depends on the behaviour policy.

**Why this matters for experience replay:** Experience replay stores past transitions (s, a, r, s') in a buffer and samples them randomly during training. Old transitions were collected under an older behaviour policy. Q-learning can use these old transitions without bias, because its target (max Q(s', a')) does not depend on which policy generated the transition. SARSA cannot: its target requires knowing the action the current policy *would have taken* at s', which may differ from the action actually taken when the old transition was collected.

</details>

---

**Q8.** In the Q-learning update, α is the learning rate. What happens if α is too large? Too small? What determines the right value in practice?

<details>
<summary>Answer</summary>

**Too large (α → 1):** The estimate jumps fully to each new TD target, discarding accumulated experience. The estimates are highly noisy — each new sample overwrites the previous estimate. In stochastic environments, this causes oscillation and failure to converge.

**Too small (α → 0):** The estimates move extremely slowly, requiring a prohibitive number of samples to converge. The algorithm learns, but impractically slowly. If α decays to zero too fast (faster than 1/t), convergence stalls.

**The right value:** The Robbins-Monro conditions require Σ αₜ = ∞ (the learning rate decays slowly enough to reach Q*) and Σ αₜ² < ∞ (it decays fast enough to suppress noise). In tabular Q-learning, αₜ ∝ 1/t satisfies these. In deep RL, α is typically a small constant (0.0001–0.001) chosen by validation, and the noise is managed through batch averaging and target networks rather than α decay.

</details>

---

### Day 45 — Policy Gradient Methods

**Q9.** PPO clips the probability ratio rₜ(θ) = π_θ(aₜ|sₜ) / π_θ_old(aₜ|sₜ). Why is this clipping necessary? What goes wrong without it?

<details>
<summary>Answer</summary>

**Without clipping:** Standard policy gradient (REINFORCE) takes a gradient step in the direction of higher-return actions. If the gradient step is large, the new policy π_θ can move far from π_θ_old — into a region where the policy gradient estimate (computed under the old policy) is no longer accurate. This leads to **catastrophic policy collapse**: the new policy is so different from the data-generating policy that the gradient estimate is badly off, causing a disastrous update that the algorithm cannot easily recover from.

**With clipping:** PPO limits how much rₜ(θ) can deviate from 1. When advantage Â is positive (the action was good), the objective is capped at (1+ε)·Â — the policy cannot increase the probability of that action beyond a factor of (1+ε). When advantage is negative (the action was bad), the cap at (1−ε) prevents over-penalising. The pessimistic min ensures the update is never overconfident in either direction.

This is trust-region optimisation implemented through a simple clip — the policy changes only as much as the local approximation can be trusted.

</details>

---

**Q10.** The policy gradient theorem says we can compute ∇_θ J(θ) without knowing the environment's transition probabilities. Why is this remarkable — and what is the trick that makes it possible?

<details>
<summary>Answer</summary>

It is remarkable because J(θ) = E_{τ ~ π_θ}[G(τ)] is an expectation over trajectories whose distribution depends on both the policy *and* the environment's transition probabilities. Naively, differentiating through this expectation requires knowing ∂P(s'|s,a)/∂θ — the gradient of the environment's dynamics, which is unknown.

**The trick:** The log-derivative trick (also called the REINFORCE trick or score function estimator). For any distribution p_θ(x):

```
∇_θ E[f(x)] = E[ f(x) · ∇_θ log p_θ(x) ]
```

Applied to trajectories: the gradient of the trajectory probability factors into a sum of ∇_θ log π_θ(aₜ|sₜ) terms — one per action — plus terms from the transition probabilities. The transition probability terms have gradient zero with respect to θ (the environment doesn't change when you change the policy parameters). So they drop out. What remains is a gradient that depends only on the policy's log-probabilities, which are known and differentiable, weighted by the return Gₜ.

The environment's dynamics appear only through the sampled trajectory — not through their gradient.

</details>

---

### Day 46 — Reward Design & Goodhart's Law

**Q11.** Describe the four types of Goodhart's Law that apply to reward misspecification. Give one RL example for each.

<details>
<summary>Answer</summary>

**1. Regressional Goodhart:** The proxy R was an imperfect measure of the true objective O even before optimisation — the correlation is weak. Optimising R hard reveals this weakness. *Example:* A content recommendation system optimises for clicks as a proxy for user satisfaction. Clicks correlate with satisfaction in normal usage, but the correlation is loose — sensational content gets clicks without satisfaction. Optimising clicks aggressively exposes this.

**2. Extremal Goodhart:** R was a reliable proxy of O in the training distribution, but the relationship breaks down at the extremes induced by optimisation. *Example:* An LLM fine-tuned to maximise reward model scores for helpfulness learns to produce responses that look helpful (verbose, confident, well-formatted) but are substantively wrong, because the reward model was only calibrated on human preferences in the normal distribution, not at the extremes the optimised model reaches.

**3. Causal Goodhart:** R is correlated with O but is not causally upstream of O — intervening on R does not produce O. *Example:* A hiring algorithm trained on "years of experience" as a proxy for performance optimises for credentials that correlate with experience without producing performance.

**4. Adversarial Goodhart:** An agent deliberately exploits the proxy because they know it is being measured. *Example:* An RL agent in a simulated physics environment maximises a "standing upright" reward by exploiting a bug in the physics engine to achieve a degenerate pose that scores highly. The agent is not adversarial in intent, but its optimisation pressure acts as adversarial pressure on the reward specification.

</details>

---

**Q12.** Potential-based reward shaping is guaranteed not to change the optimal policy. Intuition: why does the γΦ(s') − Φ(s) term telescope out over a trajectory?

<details>
<summary>Answer</summary>

Over a trajectory (s₀, a₀, s₁, a₁, ..., sₙ), the shaped reward adds γΦ(s') − Φ(s) at each step. Summing over all steps:

```
Σₜ [γΦ(sₜ₊₁) − Φ(sₜ)]
= γΦ(s₁) − Φ(s₀)
+ γΦ(s₂) − Φ(s₁)
+ γΦ(s₃) − Φ(s₂)
+ ...
= γΦ(sₙ) − Φ(s₀)   (telescoping)
```

The internal terms cancel. The total shaping bonus over any trajectory depends only on the first and last states — not on the path taken. Since the optimal policy is determined by comparing trajectories, and every trajectory's shaping bonus differs only by the same initial term Φ(s₀) (fixed for a given starting state), the relative ordering of trajectories — and therefore the optimal policy — is unchanged.

The γ factor is essential: without it, the telescoping is imperfect and the shaping does change value function estimates in a way that can alter the optimal policy.

</details>

---

## Three Things That Confuse Everyone

### Confusion 1: Q-Learning vs. Policy Gradients — When to Use Which

Both are model-free RL algorithms. Both converge (under conditions). They are not interchangeable.

Use **Q-learning (value-based methods)** when:
- The action space is **discrete and small** — you can take an argmax over all actions.
- You want **data efficiency** — off-policy learning lets you reuse old experience via replay buffers.
- You are working in a setting like Atari (discrete actions, dense rewards).

Use **policy gradients** when:
- The action space is **continuous** — joint angles, bid prices, token probabilities. Q-learning cannot take an argmax over a continuous space.
- The **optimal policy is stochastic** — mixed strategies, randomised policies. Q-learning's argmax always produces a deterministic policy.
- You are fine-tuning a language model (RLHF) — the "action" is the next token, the "policy" is the language model, and you need gradient-based updates through the policy network.

The clearest heuristic: if you are doing anything with language models or robotics, you are almost certainly using policy gradients (PPO, REINFORCE, or a variant). If you are doing discrete game-playing or recommendation with a finite item set, you are more likely using Q-learning or its deep variants (DQN, Double DQN).

---

### Confusion 2: On-Policy vs. Off-Policy — Why It Matters for Experience Replay

**On-policy** algorithms (SARSA, PPO, A3C) must train on data generated by the *current* policy. Every time you update the policy, the old data becomes stale and cannot be reused.

**Off-policy** algorithms (Q-learning, DQN) learn the optimal policy value regardless of which policy generated the data. Old experience is still valid — the target (max Q(s', a')) does not assume the transition was generated by the current policy.

**Why this matters practically:**

Experience replay — storing transitions in a buffer and sampling mini-batches — is one of the most important stability and efficiency tricks in deep RL. It breaks temporal correlations in the training data (sequential transitions are highly correlated; random samples from a large buffer are not). It allows the algorithm to revisit rare but informative transitions.

Experience replay is **only valid off-policy**. PPO technically uses a small replay window (the rollout buffer from the current batch) but immediately discards it after updating — it cannot reuse data from many episodes ago without introducing bias. DQN maintains a large replay buffer of hundreds of thousands of transitions precisely because Q-learning is off-policy and can legitimately learn from all of them.

The short version: if you see a large replay buffer, you are looking at an off-policy algorithm. If you see data discarded after each gradient step, you are looking at an on-policy algorithm.

---

### Confusion 3: Reward Hacking vs. Over-Optimisation — Both Are Goodhart, Different Mechanisms

Both terms describe Goodhart failures. They are not synonyms.

**Reward hacking (specification gaming)** occurs when the agent finds an unintended but technically valid strategy that maximises the specified reward without achieving the intended objective. The reward function is exploited — the agent found a loophole in the specification. This is a problem with the reward *definition*.

*Examples:* The boat racer that spins for power-ups. The robot that falls forward to "move fast." The agent that pauses the game to avoid losing.

**Over-optimisation** (the RLHF version of Goodhart) occurs when the reward function is a proxy estimated by a model (e.g., a human preference reward model), and optimising against that proxy too aggressively pushes the policy into regions where the proxy diverges from true human preferences — even though the proxy was a reasonable approximation in the normal range. The reward function is *accurate in-distribution but wrong at the extremes reached by optimisation*. This is Extremal Goodhart.

*Examples:* An LLM that learns to produce confident-sounding, verbose, maximally-formatted responses because the reward model was calibrated on human preferences in normal-usage distributions, not at the distribution the optimised model occupies.

**The practical difference:** Reward hacking is diagnosable from the agent's behaviour — you can look at the policy and see the loophole. Over-optimisation is insidious because the policy *looks* good by the reward model's measure and only fails when evaluated by true human preferences. This is why RLHF pipelines use KL penalties (keeping the fine-tuned model close to the base model) — to prevent the policy from reaching the extremes where the reward model is no longer trustworthy.

Both are solved by better reward design. But the fixes are different: reward hacking needs a more complete specification; over-optimisation needs regularisation (KL constraints, reward model retraining, or potential-based shaping) to limit how far the policy departs from the training distribution.

---

## What Module 4's Second Half Adds

Days 48–55 take the single-agent RL toolkit and stress-test it in the situations where it breaks or requires extension.

**Day 48 — Multi-Agent RL:** When multiple RL agents interact, each agent's reward depends on the others' policies. The environment becomes non-stationary from any single agent's perspective — the "transition probabilities" shift as others learn. Single-agent RL convergence guarantees fail. You will see how Nash equilibria re-enter as the solution concept, and how cooperative and competitive multi-agent games require fundamentally different training regimes.

**Day 49 — Recommendation Systems:** A recommender is a preference-learning mechanism trained on implicit feedback (clicks, watch time, ratings). You will analyse recommendation as a bandit problem (Day 42), as a reward design problem (Day 46), and as a principal-agent problem (Day 27) — the user's revealed preferences may not match their true preferences.

**Day 50 — Deeper RLHF:** The full pipeline — supervised fine-tuning, reward model training, PPO fine-tuning, KL regularisation — and where each step can fail. You will apply the reward design taxonomy from Day 46 to real documented failures in LLM alignment.

**Day 51 — Platform Mechanism Design:** Ad auctions, API pricing, and talent matching platforms. Mechanism design (Day 30) meets the RL-trained bidder. You will see how GSP auctions create incentives for strategic bidding, and how RLHF-trained models interacting with platform reward signals create nested principal-agent problems.

**Day 52 — Nudge Design:** Prospect Theory (Day 8) applied to product choice architecture. When is a nudge a helpful default and when is it manipulation? You will apply the behavioural lens to onboarding flows, notification systems, and dark patterns.

**Day 53 — A/B Testing as RL:** Bayesian A/B testing reframed as a bandit problem. When should you stop an experiment? How do sequential tests relate to UCB1? You will connect the regret framework from Day 42 to the decision-theoretic framework from Day 9.

**Day 54 — AI Safety:** The alignment problem in full — specification, robustness, interpretability, oversight. Goodhart's Law (Day 46) at the scale of superintelligent systems. This is where the reward design intuitions meet their hardest open problems.

**Day 55 — Rest & Synthesise:** Full Module 4 consolidation. You will perform a complete decision-science audit of one AI feature you know well — the capstone deliverable that proves Module 4 has been absorbed.

---

## Closing Reflection

Before Day 48, write one sentence to each of these three prompts:

*The single RL concept from Days 41–46 that I feel least confident about is _____, and the specific question I cannot yet answer fluently is _____.*

*The connection between an earlier module and the RL toolkit that surprised me most is _____.*

*The AI product I work with or use most often — and one specific place where I now suspect its reward function is misspecified — is _____.*

These reflections will anchor Days 48–55 to your actual experience. The second half of Module 4 is only useful if you bring concrete examples to it.

---

*← [Day 46 — Reward Design](day-46-reward-design.md) · [Module 4 Overview](../4-overview.md) · Day 48 — Multi-Agent RL →*


---

← [Day 46 — Reward Design & Goodhart's Law](day-46-reward-design) &nbsp;|&nbsp; [Day 48 — Multi-Agent Reinforcement Learning →](day-48-multi-agent-rl)
