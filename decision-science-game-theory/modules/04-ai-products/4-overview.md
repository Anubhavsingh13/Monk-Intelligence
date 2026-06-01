*[Decision Science & Game Theory](../../README.md) · Module 4 of 5*

# Module 4 — Decision Science in AI Products
*Days 41–55 · Reinforcement learning, reward design, alignment, and behavioural product craft*

---

## What This Module Earns You

By the end of Day 55 you will be able to analyse any AI product through three simultaneous lenses — decision theory, game theory, and behavioural economics — and identify where each lens reveals a failure mode or design opportunity. Specifically:

- Model any sequential AI decision problem as an MDP and identify the right solution method.
- Reason about the exploration-exploitation tradeoff and choose the right bandit algorithm for a product experiment.
- Implement Q-learning and understand its connection to RLHF in LLM fine-tuning.
- Design reward functions and identify misspecification risks before deployment.
- Analyse multi-agent AI systems as games and predict their equilibrium behaviour.
- Evaluate a recommendation system as a preference-learning mechanism.
- Apply mechanism design principles to ad auctions, API pricing, and talent matching platforms.
- Audit an AI feature for alignment risks, behavioural nudge effects, and incentive misalignments.
- Run rigorous A/B tests framed as Bayesian decisions rather than p-value rituals.

---

## The Story of This Module

This module is where Modules 1–3 become tools, not subjects. Every major idea in the preceding 40 days shows up here with a new face:

- Decision trees (Day 10) → Markov Decision Processes (Day 44)
- Bayesian updating (Day 5) → Bayesian A/B testing (Day 54)
- Nash equilibrium (Day 16) → Multi-agent RL equilibria (Day 49)
- Mechanism design (Day 30) → Ad auction design (Day 51)
- Prospect Theory (Day 8) → Nudge architecture in onboarding (Day 53)
- Shapley value (Day 37) → SHAP feature attribution (Day 37, revisited Day 50)

The module begins by reframing reinforcement learning as *sequential decision theory under uncertainty* — which you already understand from Module 1. It then climbs the RL stack from bandits through MDPs to policy gradients, with the AI product application made explicit at each step. It ends with the hardest problem: reward misspecification, alignment, and the cases where optimising the specified objective destroys the intended one.

The capstone rest day (Day 55) asks you to perform a full decision-science audit of one AI feature you know well. This is the deliverable that proves Module 4 has been absorbed.

---

## Days at a Glance

| Day | Title | One Idea |
|-----|-------|----------|
| 41 | RL as Sequential Decision-Making | Decision theory with a learning agent |
| 42 | Exploration vs Exploitation | The tradeoff that never goes away |
| 43 | Multi-Armed Bandits | The simplest RL problem |
| 44 | Markov Decision Processes | States, actions, transitions, rewards |
| 45 | Value Functions & Q-Learning | Learn value without a model |
| 46 | Policy Gradient Methods | Directly optimise the policy |
| 47 | Reward Design | The hardest problem |
| **48** | **Rest & Synthesise** | **MDP for a real product feature** |
| 49 | Multi-Agent RL | When agents are in a game |
| 50 | Recommendation Systems | Preference learning at scale |
| 51 | Mechanism Design in AI | Auctions, pricing, matching |
| 52 | Alignment | When AI goals diverge |
| 53 | Behavioural Design & Nudge | Choice architecture in product |
| 54 | A/B Testing as Bayesian Decisions | Decide, don't just test |
| **55** | **Rest & Synthesise** | **Full AI feature audit** |

---

## Primary Sources
- Sutton & Barto. *Reinforcement Learning: An Introduction*. 2nd ed., MIT Press, 2018.
- Silver, David. *Introduction to Reinforcement Learning*. DeepMind/UCL, 2015 (YouTube).
- Brunskill, Emma. *CS234: Reinforcement Learning*. Stanford, 2023 (YouTube).
- Thaler & Sunstein. *Nudge*. Yale University Press, 2008.

*(Full annotations in [bibliography.md](../../bibliography.md))*

← **Previous module:** [Module 3 — Mechanism Design](../03-mechanism-design/3-overview.md)
→ **Next module:** [Module 5 — Personal Decision-Making](../05-life-career/5-overview.md)
