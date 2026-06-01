*[Decision Science & Game Theory](../../../../README.md) · [Module 4 — Decision Science in AI Products](../4-overview.md) · Day 41 of 65*

# Day 41 — Reinforcement Learning as Sequential Decision-Making

> **Today's one idea:** Reinforcement learning is the computational realisation of sequential decision theory — an agent maximises cumulative reward by learning which actions lead to good outcomes through trial and error, and the Bellman equation is backward induction (Day 19) applied recursively to every state.
> **Reading time:** ~35 min · **Prereqs:** [Day 10](../../01-foundations/days/day-10-decision-trees.md), [Day 19](../../02-game-theory/days/day-19-sequential-games.md), [Day 2](../../01-foundations/days/day-02-expected-value.md)
> **Primary source for today:** Sutton, Richard S. & Barto, Andrew G. *Reinforcement Learning: An Introduction*, 2nd ed. MIT Press, 2018. Chapters 1–3 (freely available at incompleteideas.net).

---

## The Hook

In 2013, a team at DeepMind fed a computer program only raw pixel data from Atari games and a single number — the game score. No rules, no strategy guide, no human demonstrations. The program played randomly at first, failing spectacularly. Then slowly, through millions of game frames, it started to learn. After enough time, it surpassed human performance at 29 of 49 games.

The algorithm was DQN — Deep Q-Network. The paradigm was reinforcement learning.

RL is ancient in its intuition (Pavlov's dogs, Thorndike's cats) and modern in its engineering. What changed in 2013 was not the mathematical framework — Bellman wrote the foundations in the 1950s — but the scale of computation and the depth of the function approximators. The mathematics behind DQN is the same mathematics you built in Days 10 and 19: decision trees and backward induction. RL scales this to problems with millions of states, long time horizons, and no explicit model of the environment.

---

## Building the Intuition

### The RL framework: agent, environment, reward

An RL agent operates in a loop:

```
         Action aₜ
Agent ──────────────→ Environment
  ↑                        │
  │     Observation oₜ₊₁  │
  │     Reward rₜ₊₁        │
  └────────────────────────┘
```

At each time step t:
1. The agent observes the current state sₜ.
2. The agent selects action aₜ based on its policy π(aₜ|sₜ).
3. The environment transitions to a new state sₜ₊₁.
4. The agent receives a scalar reward rₜ₊₁.

The agent's goal: find a policy that maximises expected **cumulative discounted reward** — the sum of all future rewards, where rewards further in the future are shrunk by a discount factor γ (the same δ from Day 21's repeated games). The next reward counts in full; the reward after that counts at γ; the one after that at γ², and so on.

γ close to 1 means the agent values future rewards almost as much as immediate ones; γ = 0 is purely myopic — only the very next reward matters.

**The discount factor connects to economics:** In Day 21, cooperation in repeated games required δ ≥ (T−R)/(T−P). An RL agent with γ near 1 will "cooperate" with its future self — accepting short-term pain for long-term gain. An RL agent with γ = 0 is always myopic, equivalent to the last-round defection in finite games.

---

### The value function: backward induction at scale

From Day 10, backward induction rolled back expected values from terminal nodes. The RL **value function** does the same, but for every possible state:

**State value function V(s):**

The value of a state is the expected cumulative reward the agent collects from that state onward, following its policy. The **Bellman equation** defines this recursively: the value of a state equals the immediate reward you expect to receive, plus the discounted value of whatever state you land in next. That next state's value is itself defined the same way — and so on, recursively, all the way to the future.

This is backward induction: the value of each state depends on the values of the states it leads to. For finite problems, you compute it exactly by rolling back from the end. For infinite-horizon problems, you solve it by iteration.

**Action-value function Q(s, a):**

Rather than asking "how good is this state?", the Q-function asks "how good is taking this specific action in this state?" — then following the policy from there on.

The Q-function is more useful for learning: once you know the best Q-value for every (state, action) pair, the optimal policy is just "pick whichever action has the highest Q-value in the current state."

---

### The Bellman optimality equations

The **Bellman optimality equation** says: the optimal value of a state is whichever action gives the highest sum of (a) the immediate reward from that action plus (b) the discounted expected value of the state you land in — averaged across all the states the environment might transition you to, weighted by their probability.

This is exactly the rollback algorithm from Day 10, applied to every state simultaneously, with probabilistic transitions instead of fixed ones.

**Value iteration** starts from any arbitrary guess of state values, then repeatedly applies this equation — replacing each state's value estimate with the best-action calculation above. For any discount factor less than 1, this process is guaranteed to converge to the true optimal values. Value iteration is the simplest RL algorithm, and the foundation of Q-learning (Day 44).

---

### Three ways to solve RL problems

**Model-based:** Learn the transition model P(s'|s,a) and reward function R(s,a), then solve the Bellman equation explicitly. Efficient when the model is learnable; brittle when the environment is complex.

**Value-based:** Directly estimate Q*(s, a) without learning the model. Q-learning (Day 45) does this. Policy is implicit: π*(s) = argmax_a Q*(s, a).

**Policy-based:** Directly learn the policy π_θ(a|s) parameterised by θ. Policy gradient methods (Day 46) optimise θ to maximise expected return. Necessary for continuous action spaces.

**Actor-Critic:** Combine value-based and policy-based — an "actor" (policy) and a "critic" (value function) train together. Most modern deep RL uses actor-critic variants (PPO, SAC, A3C).

---

### The exploration-exploitation dilemma: the fundamental tension

An RL agent must balance:

**Exploitation:** Use what you already know to maximise immediate reward. Greedy policy.

**Exploration:** Try new actions to discover if they're better. Random or uncertain actions.

**The tension:** A purely exploitative agent converges to local optima — never discovers that other strategies might be better. A purely exploratory agent never cashes in on what it has learned.

This is the same trade-off as the value of information (Day 9): exploration is spending resources now to acquire information that improves future decisions. Day 42 formalises this into the multi-armed bandit problem — the cleanest model of exploration-exploitation.

---

### Connecting RL to the course so far

| Module 1/2 Concept | RL Equivalent |
|-------------------|---------------|
| Decision tree rollback (Day 10) | Bellman equation / value iteration |
| Discount factor δ (Day 21) | Discount factor γ in Bellman equation |
| Sequential game + backward induction (Day 19) | MDP + dynamic programming (Day 43/44) |
| Value of Information (Day 9) | Exploration bonus / Upper Confidence Bounds (Day 42) |
| Mechanism design — reward structure (Day 30) | Reward function design (Day 47) |
| Moral hazard — hidden effort (Day 27) | Agent's actions unobserved by principal; reward design challenge |
| Prisoner's Dilemma (Day 17) | Multi-agent RL — emergent defection (Day 48) |
| Evolutionary game theory (Day 23) | Population-based training; self-play RL |

The Bellman equation is the most important connection: it is backward induction applied continuously to every state of the environment. Once you see this, RL is not a separate field — it is sequential decision theory at scale.

---

## The Formal Picture

**Markov Decision Process (MDP):**

An MDP has five components: a set of states, a set of actions, a transition rule (for each state and action, a probability distribution over next states), a reward function (expected reward for each state-action pair), and a discount factor.

**The Markov property:** The environment's next state depends only on the current state and action — not on the full history of how you got here. This is what makes the Bellman equation work: you can summarise everything relevant in a single "current state."

**Bellman equation (for optimal Q-values):**

The optimal Q-value of taking action a in state s equals the immediate reward, plus the discounted expected value of the best action available in whatever state you land in next — averaged across all possible landing states, weighted by their probability.

**Policy improvement theorem:**

If there exists any action in any state that has a higher Q-value than the action your current policy would take, then switching to that action (keeping everything else the same) produces a policy that is at least as good everywhere. Repeating this improvement step — always switching to better actions whenever found — is guaranteed to converge to the optimal policy.

---

### RL in AI products

**Recommendation systems:**

Netflix, Spotify, TikTok — recommendation systems are RL agents. The "state" is the user's history; the "action" is the recommended item; the "reward" is the engagement signal (click, watch, listen). Long-term user value requires γ > 0: optimising only immediate engagement (γ = 0) produces the clickbait problem. Day 50 covers recommendation RL in detail.

**Chatbot dialogue management:**

A conversational AI is an RL agent operating in a sequential game against the user. Each response is an action; user satisfaction (explicit or implicit) is the reward. The state is the conversation history. Training the policy to maximise long-term user satisfaction — not just local response quality — is an RL problem.

**RLHF (Reinforcement Learning from Human Feedback):**

The alignment technique used to train ChatGPT, Claude, and similar models. A reward model is trained from human preference ratings; the language model policy is then fine-tuned using RL (typically PPO) to maximise the reward model's output. This is mechanism design (Day 30) + RL: humans design the reward signal; RL optimises the policy. Day 52 covers this in depth.

**Dynamic pricing:**

A pricing algorithm that updates prices in response to demand is an RL agent. State = current inventory and demand signals. Action = price to set. Reward = revenue. The long-term objective (maximise total revenue over the season, not just today) requires forward-looking optimisation — the Bellman equation.

---

## Where It Breaks / What It Is Not

**RL assumes the Markov property.** If the relevant state includes history that the agent doesn't observe (partial observability), the MDP framework requires extension to POMDPs (Partially Observable MDPs). Most real environments are partially observable — the agent doesn't see everything. Deep RL approximates this by using recurrent networks or frame stacking (giving the agent a window of recent observations).

**RL is sample inefficient.** Learning by trial and error in the real world is expensive. DQN played each Atari game for the equivalent of hundreds of hours of human play. In physical systems (robots, industrial processes), this is impractical. Model-based RL (learning a simulation first, then planning) reduces sample requirements at the cost of model accuracy.

**The reward signal must be carefully designed.** RL will optimise whatever reward you specify — including in ways you didn't intend. Goodhart's Law applies: "When a measure becomes a target, it ceases to be a good measure." This is the core of the alignment problem (Day 52). RL is a powerful optimiser that can find loopholes in a poorly specified reward function.

**Convergence is not guaranteed for function approximation.** Value iteration with tabular Q-functions converges. Deep Q-learning (function approximation with neural networks) can diverge — the combination of bootstrapping, off-policy learning, and function approximation (the "deadly triad") creates instability. DQN fixed some of these issues; modern methods (Rainbow, SAC) address more.

---

## Try It Yourself

**Exercise 1 — Bellman equation by hand (10 min)**

A two-state MDP: S = {A, B}. Actions: {Left, Right} in each state.

Transitions and rewards (deterministic):
- State A, action Right → State B, reward +1.
- State A, action Left → State A, reward 0.
- State B, action Right → State A, reward +2.
- State B, action Left → State B, reward 0.

γ = 0.9. Find V*(A) and V*(B) by writing the Bellman optimality equations and solving them.

<details>
<summary>Worked answer</summary>

Bellman equations:
```
V*(A) = max(0 + 0.9·V*(A),  1 + 0.9·V*(B))
V*(B) = max(0 + 0.9·V*(B),  2 + 0.9·V*(A))
```

From V*(A): options are 0.9·V*(A) (Left) or 1 + 0.9·V*(B) (Right).
From V*(B): options are 0.9·V*(B) (Left) or 2 + 0.9·V*(A) (Right).

Assume optimal policy takes Right in both states (try this and verify):
```
V*(A) = 1 + 0.9·V*(B)
V*(B) = 2 + 0.9·V*(A)
```

Substituting:
```
V*(A) = 1 + 0.9·(2 + 0.9·V*(A))
V*(A) = 1 + 1.8 + 0.81·V*(A)
0.19·V*(A) = 2.8
V*(A) ≈ 14.74

V*(B) = 2 + 0.9·14.74 ≈ 15.26
```

**Verify Left is suboptimal:**
- Left in A: 0.9·14.74 = 13.27 < 14.74 ✓
- Left in B: 0.9·15.26 = 13.74 < 15.26 ✓

**Optimal policy: always go Right.** V*(A) ≈ 14.74, V*(B) ≈ 15.26.

</details>

---

**Exercise 2 — Identify the RL components (10 min)**

For each AI product, identify: state, action space, reward signal, discount factor implication, and one key reward design challenge.

a) A content moderation system that decides whether to remove a post.
b) A customer service chatbot.
c) An algorithmic trading system.

---

**Exercise 3 — Discount factor consequences (5 min)**

An RL recommendation system is trained with γ = 0 (myopic) vs. γ = 0.99 (long-horizon).

a) What behaviour does the γ = 0 agent exhibit?
b) What behaviour does the γ = 0.99 agent exhibit?
c) Which produces better long-term user experience, and why? What is the cost of the γ = 0.99 approach?

<details>
<summary>Answer</summary>

**a)** γ = 0: The agent maximises only immediate reward — clicks, watch-time, immediate engagement. It recommends whatever gets the highest immediate response, ignoring whether the user will be satisfied tomorrow. Produces clickbait, engagement-maximising content regardless of user welfare.

**b)** γ = 0.99: The agent values the user's 100th future interaction almost as much as today's. It recommends content that builds long-term engagement — introducing new topics the user might enjoy, avoiding content that triggers regret or churn. Produces recommendations that maximise the user's lifetime value.

**c)** γ = 0.99 produces better long-term user experience. Cost: (1) harder to train — the learning signal for long-term effects is delayed and noisy; (2) the reward signal must capture long-term satisfaction (hard to measure); (3) computationally more expensive. The γ = 0 agent is easier to train but systematically produces worse outcomes for users.

</details>

---

## Connect It Back

Reinforcement learning is the point where the course's two major threads — decision theory (Module 1) and game theory (Module 2) — merge into a single computational framework. The Bellman equation is backward induction; the discount factor is the repeated game's δ; the exploration-exploitation dilemma is the value of information.

Tomorrow (Day 42) dives into **multi-armed bandits** — the simplest sequential decision problem and the cleanest formalisation of exploration-exploitation. Bandit algorithms (UCB, Thompson Sampling) are used in A/B testing, recommendation systems, and clinical trials wherever exploration-exploitation must be balanced against cumulative reward.

**The question you should be able to answer now:**
*Why is the Bellman equation a form of backward induction — and what does the discount factor γ have in common with the cooperation threshold δ in repeated games?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Sutton, Richard S. & Barto, Andrew G. *Reinforcement Learning: An Introduction*, 2nd ed. MIT Press, 2018. Chapter 1 ("The Reinforcement Learning Problem") and Chapter 3 ("Finite Markov Decision Processes"), Sections 3.1–3.5. Chapter 1 motivates RL with the agent-environment loop; Chapter 3 introduces MDPs and the Bellman equation with worked examples. The book is freely available at incompleteideas.net.

**If you want the deep version:**

1. Mnih, Volodymyr et al. "Human-level control through deep reinforcement learning." *Nature*, 518:529–533, 2015. — The DQN paper. Section 1 describes the approach; the Methods section details the architecture. The key innovations (experience replay, target network) are described clearly. Read the main paper text (~3 pages) — the supplementary methods section can wait.

2. Bellman, Richard. *Dynamic Programming*. Princeton University Press, 1957. Chapter 3 ("The Theory of Dynamic Programming"). — The original source. Bellman's exposition of the optimality principle ("An optimal policy has the property that whatever the initial state and initial decision are, the remaining decisions must constitute an optimal policy with regard to the state resulting from the first decision") is worth reading in the original. Four pages.

3. Silver, David. "Lectures on Reinforcement Learning." DeepMind / UCL, 2015. Available at davidsilver.uk/teaching. Lecture 1 ("Introduction to Reinforcement Learning") and Lecture 2 ("Markov Decision Processes"). — The best video lecture series on RL. Silver's lecture 2 derives the Bellman equation from first principles with exceptional clarity. 90 minutes total for both lectures.


---

← [Day 40 — Rest & Synthesise](day-40-rest-synthesise) &nbsp;|&nbsp; [Day 42 — Exploration vs. Exploitation →](day-42-exploration-exploitation)
