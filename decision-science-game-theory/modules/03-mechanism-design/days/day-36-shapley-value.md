*[Decision Science & Game Theory](../../../../README.md) · [Module 3 — Mechanism Design & Advanced Structures](../3-overview.md) · Day 36 of 65*

# Day 36 — Cooperative Game Theory & the Shapley Value

> **Today's one idea:** When players form coalitions and share the resulting surplus, the Shapley value gives each player their expected marginal contribution averaged over all possible orderings — and this is the unique fair division satisfying four compelling axioms.
> **Reading time:** ~35 min · **Prereqs:** [Day 14](../../02-game-theory/days/day-14-what-is-a-game.md), [Day 30](day-30-mechanism-design.md), [Day 32](day-32-public-goods-free-rider.md)
> **Primary source for today:** Shapley, Lloyd S. "A Value for n-Person Games." In *Contributions to the Theory of Games*, Vol. II, Kuhn & Tucker (eds.). Princeton University Press, 1953. pp. 307–317. Also: Roth, Alvin E. (ed.). *The Shapley Value: Essays in Honor of Lloyd S. Shapley*. Cambridge University Press, 1988. Chapter 1.

---

## The Hook

Three companies — a content creator, a distribution network, and an advertiser — are negotiating a partnership. If all three work together, they generate $120K per month in revenue. If content creator and distributor work without the advertiser, they generate $80K. If content creator and advertiser work without the distributor, they generate $50K. If distributor and advertiser work without the content creator, they generate $30K. Alone, each generates $20K, $15K, and $10K respectively.

The negotiation produces an obvious question: how should the $120K be divided?

"Equally" ($40K each) seems fair — but ignores that the content creator contributes far more to any coalition they join. "In proportion to solo earnings" ($20K/$45K, $15K/$45K, $10K/$45K) seems reasonable — but ignores the additional value that only emerges in coalition.

Lloyd Shapley, in 1953, gave an axiomatic answer: each player should receive the *average marginal contribution* they make across all possible orders in which the coalition could form. The result is unique, fair by four compelling criteria, and computable. It is now used to split costs in power grids, attribute credit in machine learning models, and divide revenue in joint ventures. Shapley received the Nobel Prize in 2012.

---

## Building the Intuition

### The characteristic function

Cooperative game theory models what each coalition can achieve, not how individual players act. A **characteristic function** v assigns a value to every possible coalition (subset of players).

**Formal setup:**

- Players: a set N of n individuals or firms.
- A **coalition** is any subset of players.
- The **value function** v assigns a number v(S) to every possible coalition S — the total payoff that coalition can guarantee itself if its members cooperate.
- By convention: the empty coalition produces nothing.

**Superadditivity (usually assumed):**

Combining two separate coalitions (that don't overlap) produces at least as much as the two coalitions generate separately. In other words: merging is never worse than staying split. This is what makes cooperation worthwhile.

**The partnership example:**

Let N = {C (content), D (distributor), A (advertiser)}.

```
v({C}) = 20,    v({D}) = 15,    v({A}) = 10
v({C,D}) = 80,  v({C,A}) = 50,  v({D,A}) = 30
v({C,D,A}) = 120
```

The question: what is the "right" allocation φ = (φ_C, φ_D, φ_A) such that φ_C + φ_D + φ_A = 120?

---

### Marginal contributions and the Shapley formula

**The intuition:**

Imagine the grand coalition forms one player at a time, in some random order. As each player joins, they add value — their *marginal contribution* to the coalition already assembled. Give each player their marginal contribution, averaged over all possible orderings.

**With three players, there are 3! = 6 orderings:**

| Ordering | Marginal contribution of C | Marginal contribution of D | Marginal contribution of A |
|----------|---------------------------|---------------------------|---------------------------|
| C, D, A  | v({C})−v(∅) = 20          | v({C,D})−v({C}) = 60      | v({C,D,A})−v({C,D}) = 40  |
| C, A, D  | v({C})−v(∅) = 20          | v({C,D,A})−v({C,A}) = 70  | v({C,A})−v({C}) = 30      |
| D, C, A  | v({C,D})−v({D}) = 65      | v({D})−v(∅) = 15          | v({C,D,A})−v({C,D}) = 40  |
| D, A, C  | v({C,D,A})−v({D,A}) = 90  | v({D})−v(∅) = 15          | v({D,A})−v({D}) = 15      |
| A, C, D  | v({C,A})−v({A}) = 40      | v({C,D,A})−v({C,A}) = 70  | v({A})−v(∅) = 10          |
| A, D, C  | v({C,D,A})−v({D,A}) = 90  | v({D,A})−v({A}) = 20      | v({A})−v(∅) = 10          |

**Shapley values (average over 6 orderings):**

```
φ_C = (20+20+65+90+40+90)/6 = 325/6 ≈ 54.2
φ_D = (60+70+15+15+70+20)/6 = 250/6 ≈ 41.7
φ_A = (40+30+40+15+10+10)/6 = 145/6 ≈ 24.2
```

Check: 54.2 + 41.7 + 24.2 ≈ 120. ✓

The content creator receives the most because they contribute the most to every coalition they join. The advertiser receives the least because their marginal contributions are consistently smaller.

---

### The Shapley formula

The Shapley value for player i is:

> The average of player i's marginal contribution across all possible orderings in which the grand coalition could form.

For each possible ordering (sequence in which players join), you calculate: how much extra value did player i add when they arrived? Average that across all orderings. The worked example above (with 6 orderings and 3 players) shows exactly how this is computed.

**The weighting logic:** Orderings where player i arrives into a coalition of size k occur with a specific frequency — proportional to how many arrangements have k players arriving before i, and n − k − 1 players arriving after. This gives each ordering equal probability, which is what "average over all orderings" means.

---

### The four Shapley axioms

Shapley showed his value is the *unique* function satisfying these axioms:

**Axiom 1 — Efficiency:**

The sum of all players' Shapley values equals the total value of the grand coalition. Everything gets distributed — nothing is wasted or left out.

**Axiom 2 — Symmetry:**
If players i and j are interchangeable (v(S∪{i}) = v(S∪{j}) for all S not containing i or j), then φᵢ = φⱼ. Symmetric players receive the same payoff.

**Axiom 3 — Dummy player:**
If player i adds nothing to any coalition (v(S∪{i}) = v(S) for all S), then φᵢ = 0. Players who contribute nothing receive nothing.

**Axiom 4 — Additivity:**
For two games v and w on the same player set, φᵢ(v+w) = φᵢ(v) + φᵢ(w). The value of combined games is the sum of the values in each game.

**Theorem (Shapley 1953):** The Shapley value is the unique function φ: (N,v) → ℝⁿ satisfying all four axioms.

The uniqueness is what makes the Shapley value compelling — it's not just one fair solution, it's the *only* solution satisfying these four axioms simultaneously.

---

### The core: when is the grand coalition stable?

The Shapley value tells you how to divide value — but will the grand coalition actually form? Players might defect if a subcoalition can do better on its own.

**The core** is the set of allocations where no coalition has incentive to defect: every coalition receives at least what it could generate on its own, without the remaining players. If each group's share is at least their standalone value, no group wants to break away.

**If the core is empty:** No allocation is stable — some group will always want to break away. Common in non-superadditive games or games where coalitional values are very unequal.

**If the core is non-empty:** The grand coalition is stable at any core allocation. The Shapley value is in the core for a large class of games (convex games, where marginal contributions are non-decreasing in coalition size).

---

### Shapley value in AI and technology

**Feature attribution (SHAP):**

The most important AI application of the Shapley value is **SHAP (SHapley Additive exPlanations)**, developed by Lundberg & Lee (2017). SHAP uses the Shapley value to attribute a machine learning model's prediction to its input features.

**Setup:** Treat the model as a coalition game. Players = features. Value of a coalition S = expected model output given only the features in S. The Shapley value of each feature = its average marginal contribution to the prediction.

**Example:** A credit scoring model predicts a 720 credit score for a customer. SHAP decomposes this into:
- Age 35 contributes +15 points (vs. average prediction of 680).
- Annual income $80K contributes +35 points.
- Recent late payment contributes −25 points.
- ... etc. (all contributions sum to 720 − 680 = 40 points above average.)

SHAP satisfies the Shapley axioms — Efficiency (contributions sum to the prediction), Symmetry, Dummy player, and Additivity — making it the "correct" attribution method in a rigorous axiomatic sense. It is now a standard tool for model interpretability.

**Revenue sharing in data partnerships:**

When two companies pool their data to train a shared model (a data coalition), how should the resulting revenue be split? The Shapley value attributes revenue based on each party's marginal contribution to model quality — a company with highly unique, irreplaceable data gets more than one whose data is redundant with the other's.

**Cost allocation in multi-product firms:**

Shared infrastructure (servers, data pipelines, customer support) serves multiple products. How should costs be allocated? Shapley value attributes each product its average marginal cost contribution — the extra cost caused by each product across all possible orderings. This produces fairer internal accounting than arbitrary overhead allocation rules.

**Team contribution in engineering:**

When a feature is built by multiple engineers across multiple sprints, how do you attribute the value created? The Shapley value (with engineers as players, sprint combinations as coalitions, and feature value as the coalition value) produces a fair attribution — useful for performance review, compensation, and team design.

---

## Where It Breaks / What It Is Not

**Computational complexity.** The Shapley formula requires computing 2ⁿ marginal contributions. For n = 20 features, that's over 1 million calculations. SHAP approximates the Shapley value using sampling and model-specific shortcuts (TreeSHAP for tree models computes exact values in polynomial time). For general games, exact computation is #P-hard.

**The core may be empty.** The Shapley value is always defined, but it may not lie in the core — meaning the grand coalition might not be stable at the Shapley allocation. Players whose Shapley value is below what they could achieve in a smaller coalition will defect.

**Additivity is contestable.** The additivity axiom (Axiom 4) is mathematically convenient but not always ethically compelling. In games with strong complementarities, the sum-of-games structure may not capture the real interaction. Alternative axiom systems (the Banzhaf value replaces additivity with a different normalisation) produce different, also defensible allocations.

**Shapley value vs. bargaining power.** The Shapley value reflects average marginal contribution, not actual bargaining power. A player with high Shapley value but low outside option may receive less than their Shapley share in actual negotiation — Day 25's bargaining theory governs the negotiated outcome, which can differ substantially from the Shapley value.

---

## Try It Yourself

**Exercise 1 — Compute the Shapley value (10 min)**

Three cities (A, B, C) share a water treatment facility. Costs if building separately: A = $90, B = $60, C = $45. Costs if building together:

```
v({A,B}) = 120,  v({A,C}) = 110,  v({B,C}) = 90
v({A,B,C}) = 150
```

(Here v(S) = cost savings from cooperation = sum of standalone costs − joint cost.)

Compute the Shapley value for each city's share of the cost savings. Then translate into each city's payment if total joint cost = 150 and standalone costs are A=$90, B=$60, C=$45 (total standalone = $195, total savings = $45).

<details>
<summary>Worked answer</summary>

Standalone values: v({A}) = 0, v({B}) = 0, v({C}) = 0 (no savings alone).

Savings values:
```
v({A,B}) = 90+60−120 = 30
v({A,C}) = 90+45−110 = 25
v({B,C}) = 60+45−90 = 15
v({A,B,C}) = 90+60+45−150 = 45
```

**6 orderings and marginal contributions:**

| Order | A's MC | B's MC | C's MC |
|-------|--------|--------|--------|
| A,B,C | 0 | 30 | 15 |
| A,C,B | 0 | 20 | 25 |
| B,A,C | 30 | 0 | 15 |
| B,C,A | 30 | 0 | 15 |
| C,A,B | 25 | 20 | 0 |
| C,B,A | 30 | 15 | 0 |

**Shapley values (average):**
- φ_A = (0+0+30+30+25+30)/6 = 115/6 ≈ 19.17
- φ_B = (30+20+0+0+20+15)/6 = 85/6 ≈ 14.17
- φ_C = (15+25+15+15+0+0)/6 = 70/6 ≈ 11.67

Check: 19.17+14.17+11.67 = 45 ✓ (total savings distributed)

**Payments:** Each city pays (standalone cost − Shapley savings):
- A pays: 90 − 19.17 = $70.83
- B pays: 60 − 14.17 = $45.83
- C pays: 45 − 11.67 = $33.33

Total: 70.83+45.83+33.33 = $150 ✓

</details>

---

**Exercise 2 — SHAP intuition (10 min)**

A binary classifier predicts whether a customer will churn (1 = churn, 0 = stay). For customer X, the model predicts 0.72 churn probability. The average prediction across all customers is 0.40.

Features for customer X: tenure = 2 months (short), last_login = 30 days ago (long), support_tickets = 5 (many), plan = "Free".

The model's feature contributions (SHAP values) for customer X:
- tenure: +0.15 (short tenure increases churn risk)
- last_login: +0.08 (long time since login increases churn risk)
- support_tickets: +0.06 (many tickets increases churn risk)
- plan: +0.03 (free plan correlates with churn)

a) Do these SHAP values satisfy the Efficiency axiom? Verify.
b) The team wants to reduce churn for customer X. Which feature should they target first based on SHAP values?
c) If tenure and last_login were perfectly correlated (always the same value), what does the Symmetry axiom imply about their SHAP values?

<details>
<summary>Answer</summary>

**a)** Efficiency: SHAP values should sum to (prediction − average prediction) = 0.72 − 0.40 = 0.32.
Sum: 0.15+0.08+0.06+0.03 = 0.32. ✓ Efficiency is satisfied.

**b)** Target **tenure** first — it has the largest SHAP value (+0.15), meaning it contributes most to the elevated churn probability. In practice, you can't change past tenure, but you can design interventions that simulate longer tenure effects (loyalty programmes, milestone rewards, early-stage onboarding improvements for future cohorts).

**c)** The Symmetry axiom says: if two features are interchangeable (same value in all coalitions), their SHAP values are equal. If tenure and last_login are perfectly correlated, the model treats them as interchangeable — each SHAP value would be (0.15+0.08)/2 = 0.115. The Symmetry axiom ensures that perfectly redundant features share credit equally.

</details>

---

**Exercise 3 — Design the revenue-sharing rule (15 min)**

Your company has three product lines (Enterprise, SMB, Consumer) sharing a common AI model that was trained on pooled data. A new enterprise deal generates $500K in revenue attributable to the AI model's capabilities.

The AI model's quality without each product line's training data:
- Without Enterprise data: model quality drops 40% → estimated revenue impact −$200K.
- Without SMB data: model quality drops 25% → estimated revenue impact −$125K.
- Without Consumer data: model quality drops 10% → estimated revenue impact −$50K.

Design a Shapley-value-based revenue sharing rule for the $500K. State your assumptions about the characteristic function v(S), compute the Shapley values, and explain why this is fairer than a 1/3–1/3–1/3 split.

---

## Connect It Back

The Shapley value answers a fundamental question — how should joint surplus be divided fairly? — with an answer that is unique, axiomatically grounded, and practically computable. Its application to ML model interpretability (SHAP) has made it one of the most practically relevant ideas from cooperative game theory in the last decade.

Tomorrow (Day 37) introduces **correlated equilibrium** — a generalisation of Nash equilibrium in which a mediator's public signal allows players to coordinate on outcomes that Nash equilibrium cannot achieve. It is both a theoretical refinement and a practical tool for platform designers.

**The question you should be able to answer now:**
*Why is the Shapley value the unique fair division — what would a division look like that violated each axiom, and why would each violation be considered unfair?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Shapley, Lloyd S. "A Value for n-Person Games." In *Contributions to the Theory of Games*, Vol. II (Kuhn & Tucker, eds.). Princeton University Press, 1953. pp. 307–317. — The original 11-page paper. The axioms are stated in Section 1; the formula is derived in Section 2; uniqueness is proved in Section 3. Unusually readable for a foundational math paper — every page rewards careful reading.

**If you want the deep version:**

1. Lundberg, Scott M. & Lee, Su-In. "A Unified Approach to Interpreting Model Predictions." *Advances in Neural Information Processing Systems*, 30, 2017. — The paper that introduced SHAP. Shows that the Shapley value is the unique attribution method satisfying local accuracy (efficiency), missingness (dummy player), and consistency (a form of symmetry) — connecting cooperative game theory axioms to ML interpretability. Read Sections 1–3.

2. Young, H. Peyton. "Monotonic Solutions of Cooperative Games." *International Journal of Game Theory*, 14(2):65–72, 1985. — Provides an alternative axiomatic characterisation of the Shapley value based on monotonicity (if a player's marginal contribution increases, their payoff should increase). Reveals a different angle on why the Shapley value is compelling. Read Theorem 1 and its proof.

3. Roth, Alvin E. (ed.). *The Shapley Value: Essays in Honor of Lloyd S. Shapley*. Cambridge University Press, 1988. Chapter 2 (Aumann, "The Shapley Value for Games Without Side Payments") and Chapter 12 (Shubik, "The Shapley Value for Political Power"). — Two application chapters showing how the Shapley value applies to games without transferable utility (voting power, political coalitions). The Banzhaf power index and Shapley-Shubik index for voting power are directly relevant to Day 31's voting content.


---

← [Day 35 — Rest & Synthesise](day-35-rest-synthesise) &nbsp;|&nbsp; [Day 37 — Correlated Equilibrium →](day-37-correlated-equilibrium)
