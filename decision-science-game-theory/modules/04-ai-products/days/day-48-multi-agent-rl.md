*[Decision Science & Game Theory](../../../../README.md) · [Module 4 — Decision Science in AI Products](../4-overview.md) · Day 48 of 65*

# Day 48 — Multi-Agent Reinforcement Learning

**Today's one idea:** When multiple learning agents share an environment, the strategic interactions studied in game theory re-emerge as emergent phenomena — agents learn to cooperate, defect, or coordinate without being programmed to do so, and the Nash equilibrium is no longer guaranteed to be the fixed point of learning.

**Prerequisites:** Day 46, Day 21 (repeated games), Day 23 (evolutionary game theory), Day 37 (correlated equilibrium)

**Primary source:** Shoham, Yoav & Leyton-Brown, Kevin. "Multiagent Systems: Algorithmic, Game-Theoretic, and Logical Foundations." Cambridge University Press, 2008. Chapter 7. Also: Lowe, Ryan et al. "Multi-Agent Actor-Critic for Mixed Cooperative-Competitive Environments." NIPS 2017. arXiv:1706.02275.

---

## The Hook

OpenAI trained agents to play hide-and-seek. After millions of episodes with no rules about tool use, seekers learned to use ramps to climb walls; hiders learned to lock ramps away; then seekers learned to surf boxes; then hiders learned to collapse ceilings. This arms race of emergent strategies wasn't programmed — it emerged from two populations each running their own RL update.

The single-agent RL framework breaks when other agents are in the environment: the "environment" is non-stationary because other agents are learning too. The Markov property fails — the environment's behaviour depends on other agents' policies, which are changing.

---

## Building the Intuition

### 1. Why single-agent RL fails in multi-agent settings

- **Non-stationarity:** From agent i's perspective, the other agents are part of the environment. But they're learning, so the effective transition probabilities P(s'|s,a) change over time. Convergence proofs require stationary environments.
- **The Nash equilibrium problem:** Even if we find a Nash equilibrium, there may be multiple equilibria (coordination problem from Day 24), and independent learners have no mechanism to select among them.

### 2. Three MARL settings (with the game-theory connection)

- **Fully cooperative (common reward):** Team of agents maximising joint reward. Connection to Day 36 (Shapley value — how to credit individual contributions).
- **Fully competitive (zero-sum):** One agent's gain is another's loss. Self-play converges to minimax strategy. Connection to Day 16 (minimax theorem).
- **Mixed cooperative-competitive:** Most real cases. Agents cooperate within teams, compete across teams. Connection to Day 38 (Price of Anarchy — social cost vs. individual optimisation).

### 3. Independent Q-Learning (IQL) and its failure

- Each agent runs its own Q-learning update treating other agents as part of the environment.
- Works sometimes in practice but has no convergence guarantee: each agent's Q-function is non-stationary because the others are learning.
- The tragedy: the simplest approach is the most commonly used in industry despite its theoretical problems.

### 4. Centralised Training, Decentralised Execution (CTDE)

- **During training:** Share information across agents (centralised critic knows everyone's observations and actions).
- **During execution:** Each agent acts on its own local observation only.
- **MADDPG (Lowe et al. 2017):** Actor for agent i uses only local observation oᵢ; critic for agent i uses all agents' observations and actions (o₁,...,oₙ, a₁,...,aₙ). The centralised critic gives a better advantage estimate during training.
- This is the dominant paradigm for cooperative MARL in production systems.

### 5. Self-play and emergent competition

- Train agent against copies of itself (AlphaGo/AlphaZero). The opponent is always at the agent's level — curriculum learning for free.
- **Convergence in zero-sum games:** Self-play in two-player zero-sum games converges to minimax (Nash) equilibrium (Brown's fictitious play result).
- **Limitation:** Self-play can cycle in non-zero-sum games (Rock-Paper-Scissors-like dynamics). Connection to Day 23's replicator dynamics.

### 6. Emergent cooperation and defection

- Prisoner's Dilemma (Day 21) re-emerges naturally: if two Q-learners play repeated PD with finite horizon, they learn to defect (backward induction unravelling). With infinite horizon and sufficient patience, they learn cooperation.
- The discount factor γ in RL IS the δ in repeated game analysis. High γ = patient agent = more cooperation.
- **Population-based training:** Maintain a population of agents with different strategies. Prevents collapse to a single strategy. Connection to Day 23's ESS.

---

## The Formal Picture

**Nash Q-values:**

In a multi-agent MDP, agent i's optimal Q-value for a joint action (all agents' actions simultaneously) equals agent i's immediate reward plus the discounted Q-value of the Nash equilibrium joint action in the next state.

**Problem:** Computing the Nash equilibrium action profile at every state is PPAD-hard (computationally as hard as finding a Nash equilibrium — see Day 37). In practice, approximations and heuristics are used.

**MADDPG centralised critic update:**

The critic for agent i is trained to minimise the squared TD error — the difference between (agent i's immediate reward + discounted critic estimate of the next joint state under all agents' updated policies) and the critic's current estimate of the current joint state. The critic sees everyone's observations and actions; the actor only uses local observation at execution time.

---

## Where It Breaks

- **Scalability:** Centralised critic becomes intractable with many agents (curse of dimensionality in joint action space). Mean-field approximation replaces individual interactions with a mean agent.
- **Communication vs. no-communication:** In many real settings, agents can't share observations at execution time. Emergent communication research tries to have agents learn their own communication protocols.
- **Reward attribution:** In cooperative settings with shared reward, which agent's action deserves credit? The COMA algorithm uses a counterfactual baseline to answer this — directly connected to Day 36's Shapley value.

---

## Try It Yourself

1. **Conceptual:** Two Q-learning agents play repeated PD with γ = 0.9. Both start cooperating. Construct an argument for why one agent might learn to defect, and what happens to the other's Q-values afterward.

2. **Design:** You're building a MARL system for autonomous vehicle intersection negotiation. Which of the three settings (cooperative/competitive/mixed) fits? Would you use CTDE? What's the reward function?

3. **Calculate:** In a 2-agent cooperative game with joint reward R, agent 1 takes action a₁ and agent 2 takes a₂. The COMA counterfactual baseline for agent 1 is: Σ_{a'₁} π₁(a'₁|s) · Q(s, a'₁, a₂). If Q(s, left, a₂) = 5, Q(s, right, a₂) = 3, π₁(left) = 0.7, π₁(right) = 0.3, and the actual action was left, what is the advantage estimate?

---

## Connect It Back

MARL is game theory in motion: the static equilibria of Module 2 become the attractors of learning dynamics. Policy gradients are the replicator equation in disguise.

**Tomorrow (Day 49):** Recommendation systems — the most deployed application of sequential decision-making in AI products, where users are partially cooperative, partially adversarial agents.

---

## Suggested Readings

- Shoham & Leyton-Brown, *Multiagent Systems*, Chapter 7
- Lowe et al., "Multi-Agent Actor-Critic for Mixed Cooperative-Competitive Environments," NIPS 2017, arXiv:1706.02275
- OpenAI hide-and-seek blog post: "Emergent Tool Use from Multi-Agent Interaction" (2019)


---

← [Day 47 — Rest & Synthesise](day-47-rest-synthesise) &nbsp;|&nbsp; [Day 49 — Recommendation Systems →](day-49-recommendation-systems)
