*[Decision Science & Game Theory](../../../../README.md) · [Module 4 — Decision Science in AI Products](../4-overview.md) · Day 49 of 65*

# Day 49 — Recommendation Systems as Sequential Decision-Making

**Today's one idea:** A recommendation system is not a prediction problem (what will the user click next?) but a sequential decision-making problem (what sequence of recommendations maximises long-term user value?) — and treating it as the former, optimising for immediate engagement, is the source of most recommendation pathologies.

**Prerequisites:** Day 46 (reward design), Day 44 (Q-learning), Day 33 (network effects), Day 39 (information design)

**Primary source:** Chen, Minmin et al. "Top-K Off-Policy Correction for a REINFORCE Recommender System." WSDM 2019. arXiv:1812.02353. Also: Covington, Paul et al. "Deep Neural Networks for YouTube Recommendations." RecSys 2016.

---

## The Hook

Netflix's recommendation algorithm is the most consequential deployed RL system most people interact with. 80% of content watched on Netflix comes from its recommendation system. But the original system optimised click-through rate — Goodhart's Law (Day 46) in production at scale. A thumbnail that maximises clicks doesn't maximise watch completion or long-term subscription renewal.

The transition from "what will the user click?" (supervised learning) to "what should we show to maximise 3-month retention?" (RL) is a paradigm shift most recommendation teams haven't fully made.

---

## Building the Intuition

### 1. The supervised learning framing and why it fails

- **Standard approach:** Treat as classification/regression. Given user history, predict what they'll click/rate.
- **Problem 1:** Optimising immediate feedback (clicks, ratings) ≠ optimising long-term satisfaction. A sensational clickbait headline maximises clicks, minimises satisfaction.
- **Problem 2:** Position bias — items shown higher get more clicks regardless of quality. Training on click data trains a model that perpetuates the ranking that generated the data (feedback loop).
- **Problem 3:** Exploration is absent — the model only learns about items it already shows. Unknown items remain unknown (cold start).
- These are all Goodhart failures (Day 46) combined with Bandit exploration failures (Day 42).

### 2. The RL framing

- **State:** User history + context (time of day, device, session length so far)
- **Action:** Which item(s) to recommend (from a corpus of millions)
- **Reward:** Immediate signal (click, watch time, rating) or delayed signal (retention, satisfaction survey)
- **Transition:** Next state = updated user history after seeing/consuming the recommendation

This is an MDP (Day 43), but with three challenges unique to recommender systems:

a. Action space is massive (millions of items) — can't enumerate like Atari's 18 actions  
b. Reward is delayed (3-month retention can't be the only training signal)  
c. The data is logged (off-policy) — we only observe rewards for items we actually showed, not counterfactual items we didn't show

### 3. The core algorithmic challenge — off-policy learning

- The system logs (state, action, reward) from the current deployed policy π_β (logging policy).
- We want to learn an improved policy π_θ without re-deploying it first.
- **Naive approach:** Train π_θ on logged data → biased toward actions the logging policy took.
- **Importance sampling correction:** Weight each logged transition by π_θ(a|s) / π_β(a|s). This corrects for the distribution mismatch.
- **Problem:** Ratios can be very large (high variance) or very small (clipping information). Chen et al. (2019) use a capped importance ratio: min(π_θ/π_β, c) for threshold c.

### 4. The exploration problem at industrial scale

- Production systems can't explore randomly — showing a user a terrible recommendation to "learn" is costly.
- ε-greedy is too crude: explore among millions of items uniformly → almost always explore garbage.
- **Better:** Contextual bandits (Day 42) with item embeddings. Explore in embedding space — items similar to known-good items in representation space.
- **Practical solution:** Maintain a separate "exploration policy" that occasionally serves non-greedy items with user consent (or in offline testing).

### 5. Multi-objective reward design

- **True objective O:** Long-term user wellbeing and retention.
- **Observable proxies:** Clicks (immediate), watch time (short-term), thumbs up/down (explicit), don't-recommend-again (negative), churn (long-term).
- **Goodhart (Day 46):** Optimise watch time → addictive content; optimise clicks → sensational thumbnails.
- **Industry practice:** Composite reward r = w₁·click + w₂·watch_completion + w₃·satisfaction_survey − w₄·regret_click − w₅·suppress_request
- The weights w are the mechanism design problem (Day 30). There's no correct answer — they encode the platform's values.

### 6. The feedback loop as game theory (Day 48 connection)

- **Content creators** are agents optimising for recommendation exposure (their reward).
- **Users** are agents whose behaviour is shaped by what's recommended (platform's influence on user priors — Day 39 information design).
- **Platform** is the mechanism designer (Day 30) setting the rules that determine both.
- A recommendation system is a three-sided market (Day 33): users, creators, and platform, each with their own objective. The "recommendation algorithm" is the platform's mechanism for balancing them.

---

## The Formal Picture

**The REINFORCE recommender (Chen et al. 2019) — off-policy correction:**

The standard policy gradient (Day 45) sums over transitions generated by the current policy. When learning from logged data generated by a different (older) policy, each transition is reweighted by the ratio of (new policy's probability of that action) to (logging policy's probability of that action). This corrects for the distribution mismatch. To prevent extreme ratios (which cause high variance), the weight is capped at a threshold c.

**Top-K softmax policy:** Instead of recommending one item, the policy is a probability distribution over all items, from which K items are sampled simultaneously.

**User utility model:**

Total user utility for a recommendation = immediate utility (click, watch, rating) + discounted expected future utility given what they consumed. The RL value function directly models this decomposition, capturing the long-horizon objective that supervised learning ignores.

---

## Where It Breaks

- **The reward model is wrong:** Clicks/watch time are poor proxies for wellbeing. No proxy fully captures what we care about.
- **Non-stationarity:** Users evolve (Day 48 MARL — they're learning agents too). A model trained on last month's users fails on next month's.
- **Privacy constraints:** Full user histories aren't always available. Federated learning, differential privacy, and short context windows all limit what the state can contain.
- **Filter bubbles:** RL naturally exploits the user's current preferences — showing them more of what they already like. Diversity constraints (Day 29 screening) are needed to counteract this.

---

## Try It Yourself

1. **Diagnosis:** A recommendation system's reward is r = click + 0.5 · watch_time (normalised). A new content type gets high clicks but watch time = 0 (users immediately leave). What Goodhart failure is this? What would you change?

2. **Off-policy correction:** A logging policy showed item A with probability 0.8. The learned policy assigns probability 0.2. The observed reward was 3. Compute the importance-weighted contribution of this transition to the policy gradient. With cap c = 2, what is the capped contribution?

3. **Design:** Design the reward function for a music recommendation system. What are your proxies? What hard constraints (Day 46 constrained MDP) would you impose? What Goodhart failures do you anticipate?

---

## Connect It Back

Recommendation systems are the site where RL, mechanism design, information design, and network effects all converge. Every concept from the last 19 days has a direct application here.

**Tomorrow (Day 50):** RLHF & AI Alignment — the deeper treatment of how we align language model behaviour using the policy gradient machinery from Days 45-46, now with the MARL and reward design intuitions from Days 47-49.

---

## Suggested Readings

- Chen, Minmin et al. "Top-K Off-Policy Correction for a REINFORCE Recommender System." WSDM 2019. arXiv:1812.02353
- Covington, Paul et al. "Deep Neural Networks for YouTube Recommendations." RecSys 2016
- Ie, Eugene et al. "RecSim: A Configurable Simulation Platform for Recommender Systems." arXiv:1909.04847


---

← [Day 48 — Multi-Agent Reinforcement Learning](day-48-multi-agent-rl) &nbsp;|&nbsp; [Day 50 — RLHF & Constitutional AI →](day-50-rlhf-alignment)
