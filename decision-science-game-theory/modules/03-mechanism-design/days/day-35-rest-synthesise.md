*[Decision Science & Game Theory](../../../../README.md) · [Module 3 — Mechanism Design & Advanced Structures](../3-overview.md) · Day 35 of 65*

# Day 35 — Rest & Synthesise: Module 3 Mid-Point

> **Today's one idea:** No new material — only consolidation. The mechanism design toolkit is only useful if you can reach for the right tool under pressure.
> **Reading time:** ~35 min · **Prereqs:** Days 28–34
> **Primary source for today:** Your notes from the past seven days.

---

## How to Use This Day

Module 3 is the most demanding module in the course. The concepts build on each other:

- Information asymmetry (Day 27) creates the problem.
- Signalling (Day 28) and Screening (Day 29) are the informed and uninformed parties' solutions.
- Mechanism design (Day 30) generalises both.
- Voting systems (Day 31) show that aggregation is fundamentally limited.
- Public goods (Day 32) show how markets fail and communities can self-govern.
- Network effects (Day 33) show how markets tip and platforms must price both sides.
- Matching markets (Day 34) show how discrete assignment requires stability, not just efficiency.

Today you consolidate this arc before Days 36–40 add cooperative game theory, correlated equilibrium, the price of anarchy, and information design.

**Structure:** Part 1 (15 min) — reconstruct the toolkit from memory. Part 2 (15 min) — apply to one real problem. Part 3 (10 min) — self-test.

---

## Part 1 — Reconstruct the Framework (15 min, no notes)

Write answers to these questions before opening any prior pages:

**On information asymmetry:**

1. What is adverse selection? Give one example from insurance and one from labour markets.
2. What is the difference between moral hazard and adverse selection? Which occurs *before* contracting and which *after*?
3. In Akerlof's lemons model, why does the market fail even though gains from trade exist? What is the mechanism?

**On signalling and screening:**

4. What is the single-crossing condition in Spence's signalling model? Why must it hold for a separating equilibrium to exist?
5. In a screening menu, which type gets the distorted contract — H or L — and why?
6. What is information rent? Why must the designer leave it with the H type?

**On mechanism design:**

7. State the Revelation Principle in one sentence. What does it allow the designer to assume without loss of generality?
8. What are the two key constraints in any mechanism design problem? (Hint: IR and IC.)
9. What does VCG achieve that Myerson's optimal auction does not, and vice versa?

**On aggregation and markets:**

10. State Arrow's four axioms. Which one is violated by plurality voting — and how does the spoiler effect illustrate it?
11. What is the Samuelson condition for optimal public good provision? Why does the market produce too little?
12. What is a stable matching? Why is stability the right solution concept for matching markets rather than efficiency?

---

## Part 2 — Apply the Full Toolkit (15 min)

Choose a mechanism design problem from your own context. It could be:

- How your team allocates engineering time across projects.
- How your company prices its product across customer segments.
- How a community (a forum, a team, a neighbourhood) manages a shared resource.
- How a platform you work with matches supply and demand.

Work through it systematically:

**Step 1 — Identify the information problem:**

- Who has private information that others don't? (What is the type?)
- Is this adverse selection (hidden type before contracting) or moral hazard (hidden action after)?
- Is the informed party the one being served (customer, worker) or the one serving (firm, platform)?

**Step 2 — Identify the mechanism:**

- What rules govern the current interaction?
- Who proposes, who responds?
- Are the IR and IC constraints satisfied? (Do all parties participate? Do they report honestly?)

**Step 3 — Identify the failure:**

- Is there adverse selection (wrong types dominating)?
- Is there moral hazard (wrong actions being taken)?
- Is there underprovision of a public good?
- Is the matching unstable (blocking pairs forming outside the mechanism)?
- Is the market tipping in an undesirable direction (network effect dynamics)?

**Step 4 — Propose a fix:**

Which mechanism from Days 28–34 would help?

| Problem | Mechanism |
|---------|-----------|
| Adverse selection | Screening menu (Day 29) |
| Moral hazard | Performance-based contract (Day 27) |
| Hidden type (informed wants to reveal) | Signalling (Day 28) |
| Efficient allocation with private values | VCG (Day 30) |
| Revenue maximisation | Myerson optimal auction (Day 30) |
| Group preference aggregation | Voting (Day 31) — choose axiom to sacrifice |
| Public good underprovision | Pigouvian subsidy / Ostrom institution (Day 32) |
| Platform cold start | Subsidise the side with larger cross-externality (Day 33) |
| Discrete assignment with preferences | Gale-Shapley (Day 34) |

Write up your analysis in 200–400 words. The act of writing forces precision — "I would implement a VCG mechanism" is not an answer; "I would collect preference reports from the four project leads and allocate the two engineering slots using Clarke taxes proportional to their stated urgency" is closer.

---

## Part 3 — Self-Test: 14 Sharp Questions (10 min)

---

**Q1.** Two players in an asymmetric-information game. The informed player can take an observable action that is cheap for good types and expensive for bad types. This is most directly a _____ problem.

<details>
<summary>Answer</summary>

**Signalling** (Day 28). The informed party moves first with a costly observable action to credibly communicate their type. The single-crossing condition — cheaper for good types — is the defining feature.

</details>

---

**Q2.** A health insurer cannot observe how healthy its applicants are. It offers two plans: a comprehensive plan at $400/month and a high-deductible plan at $150/month. Which type (high-risk or low-risk) takes which plan — and why?

<details>
<summary>Answer</summary>

**High-risk types take the comprehensive plan** (worth more to them, since they expect to claim); **low-risk types take the high-deductible plan** (expect few claims, so the deductible is rarely triggered, and the low premium dominates). This is **screening** (Day 29) — the insurer designs a menu that induces self-selection by type, separating them without observing their health directly.

</details>

---

**Q3.** A mechanism is "incentive compatible." What does this mean, precisely?

<details>
<summary>Answer</summary>

Every player's equilibrium strategy is to report their true type (or take their desired action) — truth-telling is optimal given the mechanism's rules. No player can improve their outcome by misrepresenting their private information. Also called **strategyproof** when truth-telling is a *dominant* strategy (optimal regardless of others' reports).

</details>

---

**Q4.** VCG is efficient and strategyproof. Why doesn't every market use VCG?

<details>
<summary>Answer</summary>

Four practical problems: (1) VCG is **not budget-balanced** — it may run a deficit, requiring external financing. (2) VCG is **computationally hard** for large combinatorial allocation problems (finding the optimal allocation is NP-hard in general). (3) VCG is **not revenue-maximising** — Myerson's mechanism extracts more expected revenue. (4) VCG is **vulnerable to collusion** — if bidders coordinate, they can manipulate the mechanism. Practical market design must balance strategyproofness against these constraints.

</details>

---

**Q5.** State Arrow's Impossibility Theorem in one sentence.

<details>
<summary>Answer</summary>

No social welfare function over three or more alternatives can simultaneously satisfy Unrestricted Domain, Pareto Efficiency, Independence of Irrelevant Alternatives, and Non-Dictatorship.

</details>

---

**Q6.** What is the Condorcet paradox, and why does it matter for Arrow's theorem?

<details>
<summary>Answer</summary>

The Condorcet paradox: with three voters and three alternatives, majority preference can be cyclic (A beats B, B beats C, C beats A), even when each individual has transitive preferences. It matters because it shows that a simple aggregation rule (majority vote) can fail to produce a coherent social ranking — a concrete example of the impossibility Arrow's theorem proves generally.

</details>

---

**Q7.** What is the Samuelson condition, and why can't a private market satisfy it?

<details>
<summary>Answer</summary>

The Samuelson condition for optimal public good provision: **Σᵢ MBᵢ(G*) = MC(G*)** — the sum of all individuals' marginal benefits equals the marginal cost. A private market cannot satisfy this because a private provider can only collect from payers; non-payers free-ride (non-excludability). The private provider equates *one person's* marginal benefit to MC, not the sum. This produces severe underprovision.

</details>

---

**Q8.** What is Ostrom's most important empirical finding about common-pool resources?

<details>
<summary>Answer</summary>

Communities can sustainably manage common-pool resources through **locally developed institutions** — without privatisation or government regulation — provided they satisfy her eight design principles (clear boundaries, local rule-making, monitoring, graduated sanctions, etc.). This directly contradicts Hardin's "tragedy of the commons" prediction that commons inevitably collapse without top-down intervention.

</details>

---

**Q9.** In a two-sided market, one side has elastic demand and the other has inelastic demand. Which side should be subsidised?

<details>
<summary>Answer</summary>

The **elastic** side should be subsidised (or charged less). Elastic demand means users leave easily when prices rise — subsidising them increases participation, which generates cross-side network externalities for the inelastic side (who will pay for access to the larger pool of the elastic-side users). In practice: charge the side that "needs" the platform more (inelastic) and subsidise the side that brings value to the other.

</details>

---

**Q10.** What is the cold-start problem for platforms, and how does it relate to the theory of coordination games?

<details>
<summary>Answer</summary>

The cold-start problem: a platform with few users has low value, so new users don't join, so it stays small. This is a **coordination failure** (Day 24) — the low-value equilibrium (nobody joins) is self-fulfilling, just as the high-value equilibrium (everyone joins) is self-fulfilling. Getting from the bad equilibrium to the good one requires a discontinuous intervention: subsidise early users, seed with high-value users, or create a focal event that coordinates expectations.

</details>

---

**Q11.** What is a blocking pair in a matching market, and why does it matter?

<details>
<summary>Answer</summary>

A blocking pair is a doctor-hospital pair not currently matched who **both prefer each other** to their current matches. It matters because if a blocking pair exists, those two parties would bilaterally defect from the centralised matching — the matching unravels. A stable matching (no blocking pairs) is self-enforcing: no participant wants to deviate unilaterally or bilaterally from it.

</details>

---

**Q12.** The doctor-proposing Gale-Shapley algorithm is strategyproof for doctors. Does this mean hospitals should also report truthfully?

<details>
<summary>Answer</summary>

No. **Hospitals can benefit from strategic misrepresentation** of their preference rankings. The algorithm is strategyproof only for the proposing side (doctors). Hospitals (the receiving side) can potentially get better matches by submitting false preference rankings. This asymmetry is fundamental: no stable matching mechanism is strategyproof for both sides simultaneously (Roth 1982).

</details>

---

**Q13.** What is information rent, and why must the mechanism designer leave it with the high type?

<details>
<summary>Answer</summary>

Information rent is the **extra surplus** the high type (H) receives beyond their outside option in the optimal screening contract. The designer must leave it because the IC constraint requires H to find their designated contract at least as good as the L-type contract. If the L-type contract offers some surplus (which it must, to satisfy L's participation), H can always claim to be L and get that surplus plus any additional gain from H's superior valuation. The rent is the minimum bribe needed to keep H from mimicking L.

</details>

---

**Q14.** Name one setting where screening is better than signalling, and one where signalling is better.

<details>
<summary>Answer</summary>

**Screening better:** When the uninformed party has the design power (insurance companies designing plans, SaaS platforms designing pricing tiers). Screening lets the uninformed party structure the menu proactively, reducing information rent systematically.

**Signalling better:** When the informed party has private information that is hard for the uninformed party to elicit through menu design (e.g., a job candidate's specific skill set is complex; a standardised menu of "skill levels" can't capture it). Signalling lets the informed party communicate nuanced type information through observable actions (portfolio, talks, open-source work) that the uninformed party couldn't have elicited through a menu.

</details>

---

## Closing Reflection

Before moving to Day 36, answer privately:

*In the mechanism design problems you encounter most often (at work, in personal decisions, in communities), which failure mode is most common — adverse selection, moral hazard, underprovision of public goods, network coordination failure, or matching instability?*

This reflection will anchor the second half of Module 3 to your actual experience.

---

## What's Next

Days 36–40 complete Module 3 with four advanced topics:

- **Day 36:** Cooperative game theory and the Shapley value — how to divide surplus fairly when players can form coalitions.
- **Day 37:** Correlated equilibrium — a refinement of Nash equilibrium that uses public signals to coordinate, expanding the set of achievable outcomes.
- **Day 38:** Price of anarchy and algorithmic game theory — how bad can Nash equilibria be relative to the social optimum, and can algorithms enforce better outcomes?
- **Day 39:** Information design and Bayesian persuasion — how a sender with private information can strategically choose what to reveal to influence a receiver's actions.
- **Day 40:** Rest and synthesise — full Module 3 consolidation.


---

← [Day 34 — Matching Markets](day-34-matching-markets) &nbsp;|&nbsp; [Day 36 — The Shapley Value →](day-36-shapley-value)
