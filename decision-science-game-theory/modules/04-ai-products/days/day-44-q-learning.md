*[Decision Science & Game Theory](../../../../README.md) · [Module 4 — Decision Science in AI Products](../4-overview.md) · Day 44 of 65*

# Day 44 — Q-Learning & Temporal Difference Methods

> **Today's one idea:** Q-learning learns the optimal action-value function directly from experience — without a model of the environment — by bootstrapping estimates from the very next step, and this single update rule underlies almost all modern deep reinforcement learning.
> **Reading time:** ~35 min · **Prereqs:** [Day 43](day-43-markov-decision-processes.md), [Day 41](day-41-reinforcement-learning.md)
> **Primary source for today:** Watkins, Christopher J.C.H. & Dayan, Peter. "Q-learning." *Machine Learning*, 8(3–4):279–292, 1992. Also: Sutton & Barto, *Reinforcement Learning*, 2nd ed. Chapters 6–7.

---

## The Hook

In 1989, Christopher Watkins wrote his PhD thesis at Cambridge. In it, he introduced a deceptively simple update rule for learning to make sequential decisions from raw experience — no model, no planning, just: observe a transition, update a number. He called it Q-learning.

The update rule is four variables and two lines:

```
target = r + γ · max_{a'} Q(s', a')
Q(s, a) ← Q(s, a) + α · (target − Q(s, a))
```

Twenty-five years later, DeepMind replaced the tabular Q-function with a neural network and this same update rule — with two engineering fixes — produced DQN, which beat humans at Atari games. The mathematical core was unchanged. The insight was unchanged.

Q-learning is worth understanding deeply because it is the clearest example of **bootstrapping** — using your own estimates to improve your estimates — and because every modern deep RL algorithm is a variation on the same theme.

---

## Building the Intuition

### The problem with dynamic programming

From Day 43, value iteration requires:
1. **The full model**: P(s'|s,a) and R(s,a) for every (s,a,s').
2. **The full state space**: V(s) for every s ∈ S.

In real problems, you have neither. You can sample experience — transitions (s, a, r, s') — but you don't know the underlying transition probabilities.

**The sample-based alternative:**

Instead of computing the full Bellman update — which requires knowing the probability of every possible next state — use a single observed transition (s, a, r, s') to form a **one-sample estimate**:

```
TD target = r + γ · max_{a'} Q(s', a')    [only uses one observed transition]
```

Then update Q(s,a) toward this target:

```
Q(s,a) ← Q(s,a) + α · [r + γ · max_{a'} Q(s', a') − Q(s,a)]
```

This is the **Q-learning update**. It requires only: observe (s, a, r, s'), compute the TD target, and move Q toward it.

---

### Temporal Difference (TD) learning: the fundamental idea

**Bootstrapping:** Using your current estimate of V(s') to improve your estimate of V(s) — without waiting for the actual return from s.

**Compare three approaches:**

1. **Monte Carlo:** Run an episode to completion, observe the total return G_t, update:
   ```
   V(s) ← V(s) + α · [G_t − V(s)]
   ```
   Pro: unbiased (uses true returns). Con: must wait for episode end; high variance.

2. **Dynamic Programming:** Update using the full known model — average the Bellman equation over all possible next states, weighted by their transition probabilities. Pro: low variance. Con: requires the model.

3. **TD(0):** Bootstrap from one step:
   ```
   V(s) ← V(s) + α · [r + γ · V(s') − V(s)]
   ```
   Pro: no model needed, updates after every step. Con: biased (V(s') is an estimate, not the true value).

**TD learning sits between Monte Carlo and DP on the bias-variance spectrum.** It's biased (because V(s') is wrong at first) but low-variance (one sample vs. full episode). As V(s') improves, the bias decreases.

The TD error: δ = r + γ · V(s') − V(s). This is the "surprise" — how much better or worse things turned out than expected. It's the quantity that, in neuroscience, is correlated with dopamine firing in the brain. Temporal difference learning is the computational model of biological reward learning.

---

### Q-learning: the off-policy version

**SARSA (on-policy TD):**

Learn Q^π — the value function for the policy you're actually following:

```
Q(s,a) ← Q(s,a) + α · [r + γ · Q(s', a') − Q(s,a)]
```

where a' is the action actually taken by policy π in state s'. SARSA updates using the actual next action.

**Q-learning (off-policy TD):**

Learn Q* — the optimal value function — regardless of the policy you're following:

```
Q(s,a) ← Q(s,a) + α · [r + γ · max_{a'} Q(s', a') − Q(s,a)]
```

Q-learning always uses the *best* next action in its target, even if it didn't take that action. This is **off-policy learning**: the behaviour policy (what the agent does) can differ from the target policy (what Q-learning is learning). This allows learning from old experience, human demonstrations, or random exploration.

**Why off-policy matters:** Off-policy learning enables **experience replay** — storing past transitions in a buffer and replaying them to update Q. This breaks the temporal correlations in sequential experience, improving stability. DQN's experience replay is the direct consequence of Q-learning's off-policy property.

---

### Deep Q-Network (DQN): Q-learning at scale

The tabular Q-function requires one value per (s, a) pair. For Atari games with pixel states, |S| ≈ 10²⁸ — completely intractable.

**DQN solution:** Represent Q(s, a; θ) as a neural network with parameters θ. The network takes a state (stack of 4 frames = 84×84×4 pixels) and outputs Q-values for all actions simultaneously.

**Two engineering innovations that made DQN work:**

1. **Experience replay:** Store transitions (s, a, r, s', done) in a buffer D of size 10⁶. At each update, sample a random mini-batch from D. This:
   - Breaks temporal correlations (sequential samples are highly correlated; random batches are not).
   - Allows data reuse (each transition is used many times).

2. **Target network:** Maintain two networks — the online network Q(·; θ) and the target network Q(·; θ⁻). The target network's parameters θ⁻ are updated slowly (every 10,000 steps) rather than every update. This stabilises the TD target:
   ```
   target = r + γ · max_{a'} Q(s', a'; θ⁻)
   ```
   Without the target network, the target is computed from the same network being updated — causing chasing behaviour and divergence.

**DQN loss:**

The network is trained to minimise the squared TD error — the difference between the target value (reward + discounted best Q-value of next state, from the frozen target network) and the online network's current Q-value estimate. This turns Q-learning into a supervised regression problem: the target label is the bootstrapped TD estimate, and stochastic gradient descent updates the network weights.

---

### The deadly triad: when function approximation breaks TD

Sutton & Barto identified three ingredients that together can cause divergence in TD learning:

1. **Function approximation** (neural network for Q)
2. **Bootstrapping** (TD target uses Q estimates)
3. **Off-policy learning** (behaviour differs from target policy)

Any two of these are fine. All three together can cause Q-values to diverge to infinity or oscillate. DQN fixes some of these issues with experience replay and target networks, but the deadly triad remains an active research challenge.

**Modern solutions:** Double DQN (reduces overestimation bias), Prioritised Experience Replay (samples important transitions more frequently), Dueling Networks (separate advantage and value streams), Rainbow (combines all improvements).

---

### Q-learning beyond tabular settings

| Setting | Algorithm | Notes |
|---------|-----------|-------|
| Small discrete state space | Tabular Q-learning | Exact convergence guaranteed |
| Large discrete state space | DQN | Function approximation; needs target network + replay |
| Continuous state, discrete actions | DQN variants | Same as above; most product recommendation settings |
| Continuous state and actions | DDPG, SAC, TD3 | Actor-critic; Q-function + policy network |
| Partial observability | DRQN (recurrent DQN) | LSTM processes state sequence |
| Sparse rewards | Hindsight Experience Replay | Relabels goals retrospectively |

---

### Q-learning in AI products

**Personalised content ranking:**

The Q-function Q(user_state, content_item) estimates the long-term value of showing a particular item to a user in a particular context. DQN-style training learns this from logged user interaction data (off-policy, because the logged policy was different from the learned one). Netflix, YouTube, and LinkedIn use variants of this for session-level recommendation optimisation.

**Conversational AI action selection:**

A dialogue system's Q-function Q(conversation_state, response) estimates the long-term conversation quality from selecting a given response. Training requires human ratings or downstream task completion as the reward signal. Q-learning allows learning from historical conversations (off-policy from human agents or previous models).

**Automated trading:**

Q(market_state, order) estimates the expected P&L from placing an order given current market conditions. Tabular Q-learning (discretised states) works for simple strategies; DQN works for raw market data (order book features, price history). The challenge: the reward (P&L) is noisy, the market is non-stationary, and exploration is expensive (real money).

**Game-playing and simulation:**

AlphaGo, AlphaZero, and OpenAI Five all use Q-learning components within larger systems. AlphaZero combines Monte Carlo Tree Search (planning) with neural network value functions (learned via self-play — a form of off-policy Q-learning against past versions of itself).

---

## The Formal Picture

**Q-learning update (Watkins & Dayan 1992):**

Given transition (s, a, r, s'), update:

```
Q(s,a) ← (1 − α) · Q(s,a) + α · [r + γ · max_{a'} Q(s',a')]
```

**Convergence theorem (Watkins & Dayan 1992):**

Tabular Q-learning converges to the true Q* with probability 1, provided: (a) every state-action pair is visited infinitely often (sufficient exploration), and (b) the learning rate decays over time at an appropriate rate — slow enough that early experience is not immediately discarded, but fast enough that the algorithm can eventually settle.

**Note:** This convergence guarantee holds only for tabular Q-learning. With function approximation (neural networks), no general convergence theorem exists — convergence depends on the architecture, hyperparameters, and problem.

**DQN loss (with target network):**

Average the squared TD error over a mini-batch of randomly sampled past transitions. The target for each transition uses the frozen target network to compute the best Q-value of the next state. Optimise with Adam or RMSprop; clip gradient norms to prevent instability.

---

## Where It Breaks / What It Is Not

**Q-learning overestimates Q-values.** The max operator introduces an overestimation bias — max of noisy estimates is higher in expectation than the true max. Double DQN (van Hasselt et al. 2016) fixes this by using the online network to select actions and the target network to evaluate them.

**Q-learning is sample inefficient.** Even with experience replay, DQN needs millions of environment interactions to converge on Atari. Model-based RL (learning a world model, then planning) is 10–100× more sample efficient — at the cost of model accuracy.

**Q-learning requires discrete action spaces.** The max_{a'} operator requires enumeration of all actions. For continuous actions (e.g., setting a continuous price), DDPG or SAC are needed — they replace the max with a learned policy network.

**Sparse rewards make Q-learning slow.** If the reward is only given at the end of a long episode (like winning a chess game), the TD signal propagates slowly through the state space. Reward shaping, curriculum learning, and hindsight experience replay are common mitigations.

---

## Try It Yourself

**Exercise 1 — Q-learning update by hand (10 min)**

Initial Q-table (all zeros). Single transition: (s=A, a=Right, r=1, s'=B). Q(B, Left) = 0, Q(B, Right) = 5. γ = 0.9, α = 0.5.

a) Compute the TD target.
b) Compute the updated Q(A, Right).
c) After this update, what is the TD error?

<details>
<summary>Worked answer</summary>

**a)** TD target = r + γ · max_{a'} Q(s', a') = 1 + 0.9 · max(0, 5) = 1 + 4.5 = **5.5**

**b)** Q(A, Right) ← Q(A, Right) + α · [target − Q(A, Right)]
                = 0 + 0.5 · [5.5 − 0] = **2.75**

**c)** TD error δ = target − Q(A, Right) = 5.5 − 2.75 = **2.75**

*(The error after the update is 2.75 because we only moved halfway — α=0.5 means we close half the gap each update.)*

</details>

---

**Exercise 2 — SARSA vs Q-learning (10 min)**

In a "windy cliff" environment, the agent must walk from start to goal along a cliff edge. Falling off the cliff gives −100 reward. The optimal path skirts the cliff; a safe path avoids it.

a) SARSA learns an on-policy Q-function. If the agent explores with ε-greedy (ε=0.1), will SARSA prefer the safe or optimal path?
b) Q-learning learns an off-policy Q-function (always bootstraps from max). Which path does Q* represent?
c) In deployment (ε=0), which algorithm gives better performance and why?

<details>
<summary>Answer</summary>

**a)** SARSA learns Q^π where π is ε-greedy. During exploration, the agent occasionally takes random actions near the cliff — and falls off. SARSA's Q-values incorporate this risk, leading it to prefer the **safe path** (far from the cliff, where random actions are less catastrophic).

**b)** Q-learning bootstraps from max_{a'} Q(s', a') — the optimal next action, ignoring the exploration noise. Q* represents the **optimal (cliff-skirting) path** — the best possible path assuming no exploration.

**c)** Q-learning's Q* is better in deployment (ε=0), because with no exploration, the agent actually follows the optimal path without falling. SARSA's safe policy was an artefact of on-policy ε-greedy exploration. At test time, both algorithms would actually behave the same — but Q-learning's Q-function has learned the true optimal, so if you then fine-tune with ε=0, Q-learning's policy is correct.

**Lesson:** SARSA is safer during training (incorporates exploration risk). Q-learning is optimal at test time (ignores exploration risk). Choose based on whether training safety or deployment performance matters more.

</details>

---

**Exercise 3 — DQN design for a product (15 min)**

You're building a DQN-based system for personalised push notification timing — deciding when to send a push notification to maximise user engagement without causing unsubscribes.

a) Define: state space, action space (when to send), reward signal.
b) Why is experience replay important in this setting? What would go wrong without it?
c) Why use a target network? What instability would occur without it?
d) The reward (unsubscribe) is a rare, delayed event. How does this create sparse reward problems, and what would you do about it?

---

## Connect It Back

Q-learning bridges model-free experience and optimal decision-making: you don't need to know the rules of the game, just observe transitions and bootstrap toward Q*. The off-policy property enables experience replay, which stabilises deep RL. The TD error — the gap between predicted and actual value — is both the mathematical update signal and the computational correlate of dopamine-based biological learning.

Tomorrow (Day 45) covers **policy gradient methods** — an alternative to Q-learning that directly optimises the policy without estimating a value function. Policy gradients handle continuous actions, enable stochastic policies, and are the foundation of RLHF (Proximal Policy Optimisation is a policy gradient method).

**The question you should be able to answer now:**
*Why is Q-learning called "off-policy" — what does this mean, and what engineering advantage does it enable (experience replay)?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Sutton & Barto, *Reinforcement Learning*, 2nd ed. Chapter 6 ("Temporal-Difference Learning"), Sections 6.1–6.5. Section 6.1 introduces TD prediction; 6.4 covers SARSA; 6.5 covers Q-learning. The cliff-walking example (Section 6.5) directly illustrates the on-policy vs. off-policy distinction.

**If you want the deep version:**

1. Watkins, Christopher J.C.H. & Dayan, Peter. "Q-learning." *Machine Learning*, 8(3–4):279–292, 1992. — The original paper. The convergence proof is in Section 4. The mathematical argument is clean and worth following carefully — it uses a stochastic approximation theorem (Dayan & Sejnowski 1994 gives the cleaner version).

2. Mnih, Volodymyr et al. "Human-level control through deep reinforcement learning." *Nature*, 518:529–533, 2015. — DQN. The main text (4 pages) describes experience replay and the target network and their motivation. The Extended Data Methods section gives the full training procedure. Essential reading for understanding deep RL in practice.

3. van Hasselt, Hado, Guez, Arthur & Silver, David. "Deep Reinforcement Learning with Double Q-learning." *AAAI 2016*. arXiv:1509.06461. — Double DQN. Proves that standard Q-learning systematically overestimates Q-values; shows how separating action selection and evaluation fixes this. Read Section 2 (the overestimation proof) and Section 4 (experimental results).


---

← [Day 43 — Markov Decision Processes](day-43-markov-decision-processes) &nbsp;|&nbsp; [Day 45 — Policy Gradient Methods →](day-45-policy-gradients)
