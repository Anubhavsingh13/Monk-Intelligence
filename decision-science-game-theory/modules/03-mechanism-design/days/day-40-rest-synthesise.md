*[Decision Science & Game Theory](../../../../README.md) · [Module 3 — Mechanism Design & Advanced Structures](../3-overview.md) · Day 40 of 65*

# Day 40 — Rest & Synthesise: Module 3 Complete

> **Today's one idea:** No new material — only consolidation. Module 3 is the most conceptually dense in the course; everything in Modules 4 and 5 builds on it.
> **Reading time:** ~35 min · **Prereqs:** Days 28–39
> **Primary source for today:** Your notes from the past twelve days.

---

## How to Use This Day

Module 3 covered twelve distinct topics — more than any other module. Today you consolidate them into a single coherent framework before Module 4 applies all of it to AI products.

**Three parts:**

1. **Reconstruct** — the Module 3 map, from memory.
2. **Apply** — run a single real problem through the full Module 3 toolkit.
3. **Self-test** — 15 sharp questions with collapsible answers. Find your gaps now.

Do not rush this day. The Module 4 AI applications (reward design, RLHF, recommendation systems, alignment) will be much clearer if you enter them with Module 3 firmly in place.

---

## Part 1 — The Module 3 Map (10 min, no notes)

Before opening any previous pages, draw the following from memory:

**The information asymmetry tree:**

```
Information asymmetry (Day 27)
        │
        ├── Informed party moves first → Signalling (Day 28)
        │       └── Cost differential separates types
        │
        └── Uninformed party designs mechanism
                │
                ├── Direct revelation → Screening (Day 29)
                │       └── IC + IR constraints; info rent; no distortion at top
                │
                └── Mechanism design (Day 30)
                        └── Revelation Principle; Myerson; VCG
```

**The collective choice problems:**

```
Many agents, shared outcomes
        │
        ├── Voting rules (Day 31)
        │       └── Arrow's Impossibility; Condorcet; majority rule limits
        │
        ├── Public goods (Day 32)
        │       └── Free rider; VCG-Clarke; voluntary contribution mechanisms
        │
        ├── Network effects (Day 33)
        │       └── Two-sided markets; Rochet-Tirole; tipping; critical mass
        │
        └── Matching markets (Day 34)
                └── Gale-Shapley; stability; strategyproofness
```

**The equilibrium refinements:**

```
Nash equilibrium (Module 2 foundation)
        │
        ├── Cooperative games → Shapley value (Day 36)
        │       └── Marginal contributions; 4 axioms; SHAP for ML
        │
        ├── Correlated equilibrium (Day 37)
        │       └── Mediator; linear program; regret learning convergence
        │
        └── Price of Anarchy (Day 38)
                └── PoA = worst NE / optimum; smoothness; Braess's paradox
```

**Information design (Day 39):**

```
Sender designs information structure → shapes receiver's beliefs → influences receiver's actions
        └── Concavification characterises optimal disclosure
        └── Persuasion helps when sender's payoff is non-concave at prior
```

After drawing this, check it against your notes. Mark any branch you couldn't reconstruct.

---

## Part 2 — Full Toolkit Application (15 min)

**The scenario:** You are designing the economics of a two-sided AI marketplace — connecting ML engineers (supply) with product teams (demand). You need to make decisions on pricing, matching, information, and governance.

Work through each module 3 tool and identify what it tells you:

**Step 1 — Information asymmetry (Day 27):**
- What does each side know that the other doesn't?
- Which type of asymmetry dominates: adverse selection (type hidden before joining), moral hazard (effort hidden after joining), or both?

**Step 2 — Signalling and screening (Days 28–29):**
- What signals do ML engineers send? Are they credible (single-crossing)?
- Design a menu of service tiers that screens engineers by quality. Write out the IR and IC constraints informally.

**Step 3 — Mechanism design (Day 30):**
- Should the platform use a VCG-based matching (efficient) or a revenue-maximising mechanism?
- What is the platform's information structure — does it know valuations, or must it elicit them?

**Step 4 — Collective choice (Days 31–32):**
- How should platform rules be set? If engineers vote on pricing standards, which voting rule applies and what does Arrow's theorem imply?
- Is there a free-rider problem in the community forum (knowledge sharing)?

**Step 5 — Network effects (Day 33):**
- Which side has stronger cross-side network effects? Which side should be subsidised first?
- What is the critical mass condition for this marketplace?

**Step 6 — Matching (Day 34):**
- Should the platform use deferred acceptance? Which side proposes (engineers or product teams)?
- What stability guarantees does Gale-Shapley provide?

**Step 7 — Equilibrium refinements (Days 36–38):**
- When engineers and teams collaborate on shared projects, how should revenue be split? (Shapley value.)
- If multiple product teams submit requests simultaneously, is the resulting allocation a correlated equilibrium?
- What is the Price of Anarchy if engineers and teams independently negotiate without the platform?

**Step 8 — Information design (Day 39):**
- The platform knows more about engineer track records than product teams do. What is the optimal disclosure policy?
- How much of an engineer's profile should be visible before commitment?

Write 2–3 sentences for each step. The goal is to practice applying the full toolkit to one coherent problem, not to produce a complete answer.

---

## Part 3 — Self-Test: 15 Sharp Questions (10 min)

---

**Q1.** Akerlof's lemons model shows that markets can fail even when gains from trade exist. What is the precise mechanism — why don't buyers simply pay more to attract good sellers?

<details>
<summary>Answer</summary>

Buyers cannot distinguish good from bad sellers before purchase. If buyers pay a higher price to attract good sellers, bad sellers mimic the good sellers (also accepting the higher price). The pool of sellers at any positive price is contaminated by bad types. Rational buyers, knowing this, lower their offer to reflect average quality — which causes the best sellers to exit, further lowering average quality. The adverse selection spiral destroys the market even though gains from trade exist between buyers and the highest-quality sellers. The problem is structural (information asymmetry), not solvable by price adjustment alone.

</details>

---

**Q2.** What is the single-crossing condition, and why is it necessary for a separating equilibrium in signalling?

<details>
<summary>Answer</summary>

The single-crossing condition states that the marginal cost of the signal is lower for the high type than for the low type: ∂c_H/∂e < ∂c_L/∂e. Geometrically, indifference curves in (signal, wage) space cross exactly once.

It is necessary because without it, any signal level that high types find worth acquiring is equally or more worth acquiring for low types — no signal can separate the two types. With single-crossing, the sender can find a signal level e* such that high types find it worthwhile (wage gain ≥ cost for H) and low types do not (wage gain < cost for L). Without single-crossing, no such e* exists.

</details>

---

**Q3.** What are the two binding constraints in the optimal screening contract, and why are the other two slack?

<details>
<summary>Answer</summary>

**Binding:** IR for L (low type earns zero surplus — just willing to participate) and IC for H (high type is indifferent between their contract and L's contract — any better treatment of H would allow the designer to extract more from H without losing them).

**Slack:** IR for H (H earns information rent — strictly positive surplus; tightening IR-H would violate IC-H) and IC for L (L strictly prefers their own contract to H's, because H's contract requires costly high quality/quantity from L).

The designer tightens the binding constraints because doing so extracts maximum revenue. The slack constraints cannot be tightened without violating a binding one.

</details>

---

**Q4.** State the Revelation Principle in one sentence. What does it allow the mechanism designer to assume without loss of generality?

<details>
<summary>Answer</summary>

The Revelation Principle states: *any equilibrium of any mechanism can be replicated by a direct mechanism in which each player truthfully reports their type and the mechanism implements the same outcome.*

It allows the designer to **restrict attention to incentive-compatible direct mechanisms** — games where players simply report types and truth-telling is optimal — without losing any achievable outcomes. This converts an unconstrained search over all possible game forms into a constrained optimisation problem: maximise the designer's objective subject to IC and IR constraints.

</details>

---

**Q5.** Arrow's Impossibility Theorem says no voting rule satisfies all four axioms simultaneously. Name the axioms and explain which one most voting systems sacrifice.

<details>
<summary>Answer</summary>

**Arrow's four axioms:** (1) Unrestricted domain (works for any preference profile), (2) Pareto efficiency (if everyone prefers A to B, A is ranked above B), (3) Independence of irrelevant alternatives (ranking of A vs. B doesn't depend on C), (4) Non-dictatorship (no single voter determines the social ranking).

**Most commonly sacrificed:** Independence of irrelevant alternatives (IIA). Most voting systems — Borda count, ranked-choice, Condorcet — violate IIA: introducing a third candidate C can change the collective ranking of A vs. B. This is the "spoiler effect" in electoral politics. Dictatorship satisfies all other axioms, which is why it's the only "satisfying" rule — proving that non-dictatorship must conflict with the others.

</details>

---

**Q6.** Why does the free-rider problem in public goods provision not disappear even when the good benefits everyone?

<details>
<summary>Answer</summary>

Public goods are non-excludable (you can't prevent non-payers from benefiting) and non-rival (one person's consumption doesn't reduce another's). This structure means each person benefits whether or not they contribute. The dominant strategy is to free-ride — let others contribute and enjoy the benefit for free. This is the n-player Prisoner's Dilemma: collectively, everyone contributing is better than nobody contributing, but individually, not contributing dominates contributing regardless of what others do. The good benefits everyone, but the individual incentive to free-ride remains unchanged.

</details>

---

**Q7.** A two-sided platform subsidises one side heavily. What determines which side should be subsidised?

<details>
<summary>Answer</summary>

The optimal pricing rule (Rochet-Tirole) says subsidise the side that generates larger **cross-side network externalities** for the other side, and/or has more **elastic demand** (is more price-sensitive). Specifically: subsidise the side that (a) is more valuable to the other side per additional user, and (b) is harder to attract to the platform first (resolving the chicken-and-egg problem). In practice: if advertisers value readers enormously, subsidise readers (charge advertisers instead). If developers' presence is critical to user adoption, subsidise developers in early growth.

</details>

---

**Q8.** Explain stable matching and why the Gale-Shapley algorithm achieves it.

<details>
<summary>Answer</summary>

A matching is **stable** if there is no blocking pair — no pair (man m, woman w) who are not matched to each other but both prefer each other to their current matches. Stability is the "no regret" condition for matching markets: a stable matching survives because no one has an incentive to defect.

Gale-Shapley achieves stability through **deferred acceptance**: proposers make offers in order of preference; receivers tentatively hold their best offer and reject the rest. Rejected proposers move to their next choice. No accepted offer is finalised until all proposals are resolved. The algorithm terminates with a stable matching because: if a blocking pair (m, w) existed, w would have received an offer from m at some point; she either held it (m is matched to w, contradiction) or rejected it (she had someone better, but then her final match is better than m — contradiction with m preferring w to his final match). Therefore no blocking pair exists.

</details>

---

**Q9.** The Shapley value satisfies four axioms. Name them and explain why the "dummy player" axiom is ethically important.

<details>
<summary>Answer</summary>

**Axioms:** Efficiency (Σφᵢ = v(N)), Symmetry (interchangeable players get equal shares), Dummy player (zero-marginal-contribution players get zero), Additivity (φ(v+w) = φ(v) + φ(w)).

**Why dummy player matters:** Without it, a player who contributes nothing to any coalition could still receive a positive share — essentially extracting rent without value creation. The dummy player axiom enforces that allocation is based on contribution, not mere participation. In organisational contexts, it prevents "passengers" from claiming credit for outcomes they didn't influence. In SHAP for ML, it ensures that features that don't affect the prediction receive zero attribution — a critical interpretability property.

</details>

---

**Q10.** Why is finding a correlated equilibrium computationally easier than finding a Nash equilibrium?

<details>
<summary>Answer</summary>

A Nash equilibrium is a fixed point of the best-response correspondence — finding it is PPAD-complete (believed to be computationally hard). A correlated equilibrium is defined by a set of linear constraints (one per player-strategy deviation pair) on a probability distribution over strategy profiles. This is a **linear feasibility problem**, solvable in polynomial time by linear programming. The Nash equilibrium requirement that strategies be independent (a product distribution) makes it non-linear; correlated equilibrium drops this independence requirement, converting the problem to a linear one.

</details>

---

**Q11.** Define the Price of Anarchy. Why is PoA ≤ 4/3 a reassuring result for selfish routing?

<details>
<summary>Answer</summary>

PoA = (worst Nash equilibrium cost) / (social optimum cost). It measures how much selfish behaviour degrades total welfare compared to coordinated optimum.

PoA ≤ 4/3 for affine selfish routing is reassuring because it means the worst case — with fully selfish drivers on any network — is at most 33% worse than optimal coordination. For a system as complex and decentralised as a road network with millions of independent drivers, a 33% efficiency guarantee is remarkably good. It also tells mechanism designers that moderate interventions (small tolls, nudges) can recover most of the efficiency loss — you don't need to solve a hard centralised optimisation problem to get close to optimal.

</details>

---

**Q12.** The Kamenica-Gentzkow theorem says the optimal information structure achieves the concavification of the sender's payoff. When does persuasion NOT help the sender?

<details>
<summary>Answer</summary>

Persuasion does not help the sender when the sender's payoff function V(μ) is **already concave** at the prior μ₀. In this case, the concavification equals V itself, so no information structure does better than full transparency (or no disclosure). Intuitively: if the sender's payoff is concave in the receiver's belief, averaging over "good news" and "bad news" posteriors reduces the sender's expected payoff compared to the prior — so the optimal strategy is no signal (keep the receiver at the prior). Persuasion only helps when V is convex at μ₀ — generating extreme beliefs (via the signal) increases expected payoff compared to the prior belief.

</details>

---

**Q13.** Connect screening (Day 29) to mechanism design (Day 30) in one paragraph.

<details>
<summary>Answer</summary>

Screening is mechanism design applied to private type information in a buyer-seller or insurer-customer context. The mechanism designer (insurer, platform, employer) designs a menu of contracts (the mechanism) that induces each type to self-select into the contract intended for them (the incentive-compatibility condition). The Revelation Principle guarantees this is without loss of generality — any indirect mechanism (requiring costly signals, applications, portfolios) can be replicated by a direct mechanism where types simply report themselves. Myerson's optimal mechanism generalises the screening result to n bidders with private values: the revenue-maximising mechanism allocates to the highest virtual value (which is the screening IC condition applied to the optimal auction setting). Both screening and Myerson's auction share the same mathematical structure: IR and IC constraints, information rent for high types, and downward distortion for low types.

</details>

---

**Q14.** Give an example where the Price of Stability is much lower than the Price of Anarchy. What does this imply for a mechanism designer?

<details>
<summary>Answer</summary>

**Network design games:** Multiple players want to build a shared network (routing traffic between cities). The worst Nash equilibrium (PoA) may be very inefficient — players duplicate infrastructure, choosing selfish routes that waste total cost. But the *best* Nash equilibrium (PoS) may be close to optimal — the efficient shared network is a Nash equilibrium if cost-sharing is designed correctly.

**Implication for mechanism designer:** If PoS ≪ PoA, the designer's priority should be **equilibrium selection** (guiding players to the good equilibrium) rather than mechanism redesign (changing the game). A cheap coordination signal, a default rule, or a focal point (Day 24) can select the efficient Nash equilibrium without requiring the designer to change payoffs or implement a complex mechanism. PoS bounds tell you the best-case ceiling for coordination-based interventions.

</details>

---

**Q15.** Design a two-sentence "elevator pitch" for mechanism design to a non-economist.

<details>
<summary>Answer</summary>

*Sample answer:* "Mechanism design is the science of engineering incentives — figuring out what rules to set so that people acting in their own self-interest produce the outcome you actually want. It's the difference between hoping people behave well and designing systems where behaving well is also in their interest."

*(Accept any crisp explanation that captures: (1) designing rules, not just analysing them, and (2) self-interest producing desired outcomes.)*

</details>

---

## The Module 3 Snapshot

Before continuing to Module 4, you should be able to place every day's concept on this map:

```
Private information
│
├── Before transaction → Adverse selection (D27)
│       ├── Informed reveals → Signalling (D28) [single-crossing, cost differential]
│       └── Uninformed designs → Screening (D29) [IR + IC, info rent, distortion]
│
├── After transaction → Moral hazard (D27)
│       └── Optimal contract → Mechanism Design (D30) [VCG, Myerson, Revelation Principle]
│
└── Designer controls → Information Design (D39) [Bayesian Persuasion, concavification]

Collective outcomes
│
├── Voting → Arrow's Impossibility (D31) [no perfect voting rule]
├── Public goods → Free rider, VCG-Clarke (D32)
├── Platforms → Two-sided markets, network effects (D33) [Rochet-Tirole]
└── Matching → Gale-Shapley, stability (D34)

Equilibrium quality
│
├── Cooperative → Shapley value, core (D36) [SHAP for ML]
├── Correlated → Correlated equilibrium, regret learning (D37)
└── Selfish → Price of Anarchy, smoothness, Braess (D38)
```

---

## What's Next

Module 4 (Days 41–55) applies everything here to AI products:

- **Days 41–44:** Reinforcement learning as sequential decision-making — the Bellman equation is backward induction (Day 19) applied to Markov Decision Processes.
- **Days 45–46:** Exploration-exploitation and multi-armed bandits — the PoA of greedy algorithms and the optimal information value tradeoff.
- **Days 47–48:** Reward design and alignment — mechanism design applied to AI systems; Goodhart's Law as moral hazard (Day 27).
- **Days 49–51:** Multi-agent RL — evolutionary game theory (Day 23), correlated equilibrium (Day 37), and Price of Anarchy (Day 38) all appear explicitly.
- **Days 52–54:** Recommendation systems, platform mechanism design, and RLHF — screening (Day 29), information design (Day 39), and signalling (Day 28) applied to AI alignment.

The investment you made in Module 3 pays dividends immediately in Module 4.


---

← [Day 39 — Information Design & Bayesian Persuasion](day-39-information-design) &nbsp;|&nbsp; [Day 41 — Reinforcement Learning: The Framework →](day-41-reinforcement-learning)
