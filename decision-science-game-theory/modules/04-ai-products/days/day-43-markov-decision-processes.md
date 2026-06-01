*[Decision Science & Game Theory](../../../../README.md) · [Module 4 — Decision Science in AI Products](../4-overview.md) · Day 43 of 65*

# Day 43 — Markov Decision Processes

> **Today's one idea:** An MDP makes the sequential decision problem precise — a state space, action space, transition probabilities, and reward function — and dynamic programming solves it exactly by propagating value backward through the state space until convergence.
> **Reading time:** ~35 min · **Prereqs:** [Day 41](day-41-reinforcement-learning.md), [Day 10](../../01-foundations/days/day-10-decision-trees.md), [Day 19](../../02-game-theory/days/day-19-sequential-games.md)
> **Primary source for today:** Bellman, Richard. *Dynamic Programming*. Princeton University Press, 1957. Chapter 3. Also: Sutton & Barto, *Reinforcement Learning: An Introduction*, 2nd ed. Chapter 3 (Sections 3.5–3.8) and Chapter 4.

---

## The Hook

In 1953, Richard Bellman was working on a US Air Force contract, trying to formalise multistage decision processes — situations where decisions made today affect the options available tomorrow. He needed a name for the mathematical framework.

He later wrote: "I thought dynamic programming was a good name. It was something not even a Congressman could object to. So I used it as an umbrella for my activities."

The "principle of optimality" he discovered — that an optimal policy has the property that whatever the current state and decision, the remaining decisions must form an optimal policy for the remaining problem — is one of the most powerful ideas in applied mathematics. It is backward induction (Day 19) stated as a general principle. Everything in RL flows from it.

Today you formalise the MDP and work through two canonical algorithms: policy evaluation and value iteration. By the end, you will understand exactly what a "trained RL agent" computes.

---

## Building the Intuition

### From decision trees to MDPs

A decision tree (Day 10) is a finite, acyclic graph of decisions and chance nodes. Each path leads to a unique terminal payoff. Backward induction computes the optimal decision at each node by rolling back expected values.

An MDP generalises this in two ways:

1. **States can recur.** The agent can return to a state it's been in before — the graph is cyclic, not a tree. This requires iterative solution methods, not simple backward recursion.

2. **The horizon may be infinite.** The agent may interact indefinitely, accumulating discounted reward. This requires the discount factor γ to ensure convergence.

**The MDP as a controlled Markov chain:**

```
s₀ ─→ s₁ ─→ s₂ ─→ s₃ ─→ ...
  a₀    a₁    a₂    a₃
  r₁    r₂    r₃    r₄
```

At each time step: agent in state sₜ, chooses action aₜ, transitions to sₜ₊₁ ~ P(·|sₜ, aₜ), receives reward rₜ₊₁ = R(sₜ, aₜ).

**The Markov property:** sₜ₊₁ depends only on (sₜ, aₜ), not on the full history. The state encodes all relevant information. This is the "state representation" problem in practice: choosing what to put in the state so the Markov property approximately holds.

---

### Policies and value functions

**Policy:** A rule mapping each state to an action (deterministic) or to a probability distribution over actions (stochastic).

**State-value function V(s):**

The value of a state under a given policy is the expected total discounted reward the agent will collect starting from that state and following the policy indefinitely.

**Bellman equation for V:**

The value of a state equals the expected immediate reward, averaged across all actions the policy might take and all states the environment might transition you to, plus the discounted expected value of wherever you land.

**Action-value function Q(s, a):**

The Q-value of a (state, action) pair is the expected return from taking that specific action in that state, then following the policy from the resulting state onward. It equals the immediate reward of the action plus the discounted value of the resulting state.

The relationship between the two: the value of a state equals the average Q-value of the actions the policy takes there, weighted by how likely the policy is to choose each action.

---

### Policy evaluation: computing V

Given a fixed policy, the Bellman equation for every state simultaneously forms a system of equations — one unknown (the state's value) per state. For small state spaces, you can solve this system exactly using standard linear algebra. For large state spaces, you solve it iteratively:

**Iterative policy evaluation:**

```
Init: V₀(s) = 0 for all s
Repeat:
  For each state s:
    V_{n+1}(s) = expected immediate reward in s under the policy
                 + γ × expected value of the resulting next state
Until the largest change across all states falls below a small threshold ε
```

This repeatedly applies the Bellman equation until values stop changing. It contracts toward the true V at rate γ — so it always converges for γ < 1.

---

### Policy iteration: finding the optimal policy

Given V^π, can you improve π? Yes:

**Policy improvement theorem:** For any state s, if Q^π(s, a') > V^π(s) for some action a' ≠ π(s), then the policy π' that takes a' in s (and follows π elsewhere) satisfies V^{π'} ≥ V^π everywhere.

**Policy iteration algorithm:**

```
Init: any starting policy
Repeat:
  1. Policy Evaluation: compute the value of every state under the current policy.
  2. Policy Improvement: for each state, switch to whichever action has
     the highest Q-value (immediate reward + discounted value of next state).
Until the policy doesn't change (no further improvement is possible)
```

**Convergence:** Policy iteration converges to the optimal policy in a finite number of steps — there are only finitely many deterministic policies, each iteration either strictly improves the policy or terminates, so it must reach the optimal.

---

### Value iteration: the direct approach

Policy iteration alternates evaluation and improvement. Value iteration collapses both into a single update:

**Value iteration:**

```
Init: V₀(s) = 0 for all s (or any initial values)
Repeat:
  For each state s:
    V_{n+1}(s) = best action's (immediate reward + γ × expected next-state value)
Until the largest change across all states falls below a small threshold ε

Extract policy: in each state, take the action with the highest Q-value under V*
```

**This is the Bellman optimality operator applied iteratively.** Each sweep propagates value from high-reward states backward through the graph.

**Convergence rate:** The error shrinks by a factor of γ with every sweep. After n iterations, the remaining error is at most γⁿ times the initial error — so with γ = 0.9, 100 iterations cuts the error by a factor of 0.9¹⁰⁰ ≈ 0.00003.

---

### A worked MDP example: the grid world

**Setup:** 4×4 grid. Agent starts at top-left (state 0). Goal: reach bottom-right (state 15). Actions: Up, Down, Left, Right. Moving into a wall stays in place. Reward: −1 per step, 0 at goal. γ = 1 (undiscounted). The agent wants to reach the goal in as few steps as possible.

**Value function after convergence (V*):**

```
 0  -1  -2  -3
-1  -2  -3  -4
-2  -3  -4  -5
-3  -4  -5   0
```

Each state's value = negative of the minimum number of steps to the goal. The optimal policy from each state: move toward the goal (down or right if possible).

**The Bellman equation at state (0,0):**

```
V*(0,0) = max_a [−1 + 1 · V*(s')]
        = −1 + max(V*(0,1), V*(1,0), V*(0,0), V*(0,0))  [right, down, up=stay, left=stay]
        = −1 + max(−1, −1, 0, 0)     [considering undiscounted: correct values]
```

Actually: after convergence, V*(0,0) = −6 (6 steps to corner) in the full grid. The recurrence propagates the −1 step cost backward from the goal.

---

### The three planning settings

**Full model known (dynamic programming):** You have P(s'|s,a) and R(s,a). Solve Bellman equations directly with policy/value iteration. Used in game-playing AI (AlphaZero uses MCTS + value function trained with DP-like self-play).

**Model unknown, reset available (model-free RL):** You can try actions and observe outcomes, but don't know P or R. Must estimate value functions from experience. Q-learning (Day 44) and policy gradients (Day 45) work here.

**Model unknown, no reset (real-world RL):** The hardest setting. Every interaction is costly. Model-based RL learns a model from limited data, then plans with the learned model. Used in robotics, drug discovery, and clinical decision support.

---

### MDPs in AI product contexts

**Dialogue management:**

A conversational AI's state = the conversation history (or a compressed representation). Actions = possible responses. Reward = user satisfaction (hard to measure; approximated by engagement, task completion, or explicit ratings). The MDP captures the sequential nature of conversation — early responses constrain later ones. Training a chatbot with RL requires defining the state, action, and reward carefully — exactly the MDP specification problem.

**Search engine ranking:**

State = user query + session context. Action = the ranking of returned documents. Reward = user clicks, dwell time, subsequent query patterns (did the user find what they needed?). The full sequential nature (users refine queries across multiple interactions) makes this an MDP, not a single-shot ranking problem. Session-level RL training (vs. per-query learning) captures the temporal dependencies.

**Supply chain optimisation:**

State = inventory levels, demand forecasts, supplier status. Actions = order quantities, routing decisions. Reward = profit − stockout cost − holding cost. The MDP formulation allows long-horizon planning (ordering today affects options weeks later) that myopic (γ = 0) policies miss. Amazon, Alibaba, and major retailers use MDP-based inventory management.

**Treatment planning in healthcare:**

State = patient health indicators (lab values, vital signs, diagnosis). Actions = treatments, dosages, procedures. Reward = health outcomes (survival, quality-adjusted life years). The sequential nature — treatment today affects patient state tomorrow — makes this an MDP. RL-based treatment policies have been developed for sepsis management, diabetes control, and cancer treatment sequencing.

---

## The Formal Picture

**MDP definition:**

A Markov Decision Process has five components:
- A set of **states** (everything the environment could look like)
- A set of **actions** (what the agent can do)
- A **transition rule**: for each state and action, a probability distribution over what state the environment moves to next
- A **reward function**: for each state and action, the expected immediate reward
- A **discount factor** γ between 0 and 1

**Bellman optimality equations:**

The optimal value of a state is the Q-value of its best action. The optimal Q-value of a (state, action) pair is the immediate reward plus the discounted expected optimal value of the resulting next state — where the expectation is taken over all possible next states, weighted by their transition probabilities.

**Existence and uniqueness:** For any MDP with γ < 1, a unique optimal value function V* exists. The optimal policy is simply: in each state, take the action with the highest Q-value under V*.

**Contraction mapping theorem (the formal basis):**

The Bellman optimality update is a contraction mapping — applying it to any two guesses for V brings them closer together by a factor of γ. By a classical theorem in analysis (Banach's fixed-point theorem), repeated application of any contraction must converge to a unique fixed point — and that fixed point is V*. This is why value iteration converges from any starting guess.

---

## Where It Breaks / What It Is Not

**The curse of dimensionality.** Tabular dynamic programming requires storing and updating |S| × |A| values. For real problems — chess (≈ 10⁴³ states), Atari (≈ 10²⁸ states from pixel values) — this is computationally infeasible. Function approximation (neural networks) is the solution — but loses exact convergence guarantees.

**The model must be known.** DP requires P(s'|s,a) and R(s,a). In most real problems, these are unknown. Q-learning (Day 44) learns V* without knowing the model. Model-based RL learns the model first, then applies DP.

**The Markov property may not hold.** If the state doesn't fully capture the relevant history, the Markov property fails and the MDP formulation is only an approximation. Partial observability (POMDPs) requires tracking a belief state — a distribution over possible states — which itself becomes the state in the augmented problem.

**Reward specification.** The MDP framework assumes you know R(s,a). Specifying the reward to capture what you actually want is often the hardest part of RL in practice — the alignment problem (Day 52) is fundamentally about reward specification for MDPs with human-level capability.

---

## Try It Yourself

**Exercise 1 — Value iteration by hand (10 min)**

3-state MDP. States: {A, B, C}. C is a terminal absorbing state (reward +10, stays in C).

From A: action Right → B with probability 1, reward 0. Action Stay → A, reward −1.
From B: action Right → C with probability 1, reward 0. Action Left → A with probability 1, reward 0.

γ = 0.9. Run value iteration for 3 steps from V₀ = (0, 0, 10). What does V₃ look like?

<details>
<summary>Worked answer</summary>

V₀ = (A: 0, B: 0, C: 10).

**Step 1:**
```
V₁(A) = max(0 + 0.9·V₀(B), −1 + 0.9·V₀(A)) = max(0, −1) = 0
V₁(B) = max(0 + 0.9·V₀(C), 0 + 0.9·V₀(A)) = max(9, 0) = 9
V₁(C) = 10 (absorbing)
```

**Step 2:**
```
V₂(A) = max(0 + 0.9·V₁(B), −1 + 0.9·V₁(A)) = max(8.1, −1) = 8.1
V₂(B) = max(0 + 0.9·10, 0 + 0.9·0) = max(9, 0) = 9
V₂(C) = 10
```

**Step 3:**
```
V₃(A) = max(0 + 0.9·V₂(B), −1 + 0.9·V₂(A)) = max(8.1, 7.29−1) = max(8.1, 6.29) = 8.1
V₃(B) = max(9, 0) = 9
V₃(C) = 10
```

V₃ ≈ (8.1, 9, 10). Optimal policy: always go Right (A→B→C). The value of A is converging to 0.9² × 10 = 8.1 — the discounted reward of reaching C in 2 steps.

</details>

---

**Exercise 2 — State representation (10 min)**

You are building an RL-based pricing agent for a SaaS product. The agent sets prices each day.

a) Propose a state representation. What variables should be included to satisfy the Markov property?
b) What is the action space? Is it discrete or continuous?
c) What reward function would you use? What are the risks of your chosen reward signal?
d) Would you use γ close to 0 or close to 1? Justify in terms of the business objective.

---

**Exercise 3 — Policy evaluation (10 min)**

A simple 2-state MDP: states {Sunny, Rainy}. Policy π: always "Go Outside."

Transitions: Sunny → Sunny (prob 0.8), Sunny → Rainy (prob 0.2). Rainy → Sunny (prob 0.4), Rainy → Rainy (prob 0.6).

Rewards: R(Sunny, Go) = 5. R(Rainy, Go) = −2. γ = 0.9.

Write the Bellman equations for V^π(Sunny) and V^π(Rainy) and solve them.

<details>
<summary>Worked answer</summary>

Bellman equations:
```
V(Sunny) = 5 + 0.9·(0.8·V(Sunny) + 0.2·V(Rainy))
V(Rainy) = −2 + 0.9·(0.4·V(Sunny) + 0.6·V(Rainy))
```

Expanding:
```
V(S) = 5 + 0.72·V(S) + 0.18·V(R)    → 0.28·V(S) − 0.18·V(R) = 5   ... (1)
V(R) = −2 + 0.36·V(S) + 0.54·V(R)   → −0.36·V(S) + 0.46·V(R) = −2 ... (2)
```

From (1): V(S) = (5 + 0.18·V(R)) / 0.28

Substitute into (2):
```
−0.36 × (5 + 0.18·V(R)) / 0.28 + 0.46·V(R) = −2
−(1.8 + 0.0648·V(R)) / 0.28 + 0.46·V(R) = −2
−6.43 − 0.2314·V(R) + 0.46·V(R) = −2
0.2286·V(R) = 4.43
V(R) ≈ 19.38

V(S) = (5 + 0.18 × 19.38) / 0.28 = (5 + 3.49) / 0.28 = 8.49 / 0.28 ≈ 30.32
```

**V^π(Sunny) ≈ 30.3, V^π(Rainy) ≈ 19.4.**

Makes sense: Sunny state is more valuable because it has higher immediate reward and a higher probability of staying sunny.

</details>

---

## Connect It Back

MDPs are the formal language of sequential decision-making. The Bellman equation operationalises backward induction; value iteration and policy iteration solve it exactly when the model is known. Everything in model-free RL — Q-learning, policy gradients — is an attempt to solve the Bellman equation from samples alone, without access to the transition model.

Tomorrow (Day 44) introduces **Q-learning** — the landmark algorithm that learns the optimal Q-function from experience without knowing the model. It is the foundation of DQN and most modern deep RL.

**The question you should be able to answer now:**
*Why does value iteration converge to V* — what property of the Bellman operator guarantees convergence, and how fast does it converge?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Sutton & Barto, *Reinforcement Learning*, 2nd ed. Chapter 4 ("Dynamic Programming"), Sections 4.1–4.4. Policy evaluation (4.1), policy improvement (4.2), policy iteration (4.3), and value iteration (4.4) are each explained with the 4×4 grid world example. Work through the example in detail — the intuition from this simple case carries directly to deep RL.

**If you want the deep version:**

1. Bertsekas, Dimitri P. *Dynamic Programming and Optimal Control*, Vol. 1, 4th ed. Athena Scientific, 2017. Chapter 1 ("The Dynamic Programming Algorithm"). — The rigorous treatment of DP for finite-horizon and infinite-horizon problems. Section 1.3 (the principle of optimality) is the mathematical heart of the Bellman equation. Sections 1.1–1.3 (~30 pages) provide the full formal foundation.

2. Bellman, Richard. *Dynamic Programming*. Princeton University Press, 1957. Chapter 3 ("The Theory of Dynamic Programming"). — The original. Bellman's prose is remarkably readable. The "principle of optimality" is stated with crystalline clarity in about one page. Read it for historical and conceptual depth.

3. Puterman, Martin L. *Markov Decision Processes: Discrete Stochastic Dynamic Programming*. Wiley, 1994. Chapter 4 ("Finite Horizon Markov Decision Processes") and Chapter 6 ("Infinite Horizon Models"). — The comprehensive mathematical reference. Chapters 4 and 6 cover finite and infinite horizon MDPs with full proofs of convergence. Use as a reference rather than sequential reading.


---

← [Day 42 — Exploration vs. Exploitation](day-42-exploration-exploitation) &nbsp;|&nbsp; [Day 44 — Q-Learning & Temporal Difference Methods →](day-44-q-learning)
