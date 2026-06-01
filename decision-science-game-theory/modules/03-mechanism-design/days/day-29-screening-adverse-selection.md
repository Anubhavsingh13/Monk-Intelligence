*[Decision Science & Game Theory](../../../../README.md) · [Module 3 — Mechanism Design & Advanced Structures](../3-overview.md) · Day 29 of 65*

# Day 29 — Screening & Adverse Selection

> **Today's one idea:** The uninformed party can solve information asymmetry by designing a menu of contracts that makes different types voluntarily reveal themselves — but doing so always requires leaving "information rent" on the table for the types you most want to attract.
> **Reading time:** ~35 min · **Prereqs:** [Day 27](../../02-game-theory/days/day-27-information-asymmetry.md), [Day 28](day-28-signalling.md), [Day 12](../../01-foundations/days/day-12-multi-criteria-decisions.md)
> **Primary source for today:** Rothschild, Michael & Stiglitz, Joseph E. "Equilibrium in Competitive Insurance Markets: An Essay on the Economics of Imperfect Information." *Quarterly Journal of Economics*, 90(4):629–649, 1976. Also: Tirole, Jean. *The Theory of Industrial Organization*, MIT Press, 1988. Chapter 2, Sections 2.1–2.2.

---

## The Hook

You offer car insurance. You have two types of customers: careful drivers who have a 10% chance of an accident; reckless drivers who have a 40% chance. An accident costs $10,000. You cannot observe which type any given customer is — but they know.

If you offer one policy at a price reflecting the average risk (say, 25% × $10,000 = $2,500), only reckless drivers buy (careful drivers subsidise them, decide it's not worth it). This is Akerlof's lemons — adverse selection drives out good risks.

But here's the key insight: you don't have to offer one policy. You can offer *two* policies and design them so that each type *chooses* the one intended for them.

- Offer Policy A: $500 premium, $10,000 coverage — attractive to careful drivers (low risk, happy to pay a bit for full coverage).
- Offer Policy B: $1,200 premium, $3,000 coverage — cheap, but deductible is high — reckless drivers avoid this because they expect to claim frequently; they'd rather pay the higher premium for full coverage.

The reckless driver won't take Policy A (they'd have to pay less but expect more claims — the math doesn't work). The careful driver won't take Policy B (limited coverage for their low-risk situation is less valuable).

You've screened the market without observing types directly. The policies *self-select* the types into the right buckets.

This is screening — and it is one of the most elegant mechanisms in all of economics.

---

## Building the Intuition

### The screening menu: designing for self-selection

Screening works by exploiting *preference differences between types*. The uninformed party (insurer, employer, platform) designs a set of contracts (the "menu") such that each type finds the contract intended for them weakly better than any contract intended for another type.

**The two key constraints (for a two-type model):**

Denote types H (high value/quality) and L (low value/quality). A menu of contracts (C_H, C_L) works iff:

1. **Individual Rationality (IR) — participation:**
   - H prefers C_H to not participating: u_H(C_H) ≥ 0
   - L prefers C_L to not participating: u_L(C_L) ≥ 0

2. **Incentive Compatibility (IC) — self-selection:**
   - H doesn't mimic L: u_H(C_H) ≥ u_H(C_L)
   - L doesn't mimic H: u_L(C_L) ≥ u_L(C_H)

The designer's goal: maximise expected profit subject to IR and IC.

**The binding constraints:**

In the optimal screening solution:
- L's IR binds: L gets zero surplus (L is the "bad" type the designer cares less about retaining at full value).
- H's IC binds: H is just indifferent between C_H and C_L.

Why? If H's IC didn't bind, you could make H's contract slightly worse for H and increase profit. You can keep tightening until H is exactly indifferent between their contract and the one designed for L. This gives H type their **information rent** — the extra surplus H must receive to truthfully report their type.

---

### The information rent and the efficiency distortion

**Information rent:** The extra surplus the uninformed party must leave to the high type to prevent mimicry. It arises because the H type could "pretend" to be L — the menu must make this unattractive.

**Efficiency distortion:** To reduce the information rent paid to H, the designer distorts the contract offered to L. The L contract is made *less attractive* than the first-best contract for L — smaller quantity, higher price, higher deductible. This reduces the attractiveness of L's contract to H types, reducing the incentive to mimic, reducing the information rent.

**The trade-off:**

```
Profit = Revenue from H − information rent to H
       + Revenue from L − efficiency distortion cost for L
```

As you distort L's contract more:
- Information rent to H decreases (benefit)
- Efficiency lost from L's sub-optimal contract increases (cost)

The optimal distortion equates these at the margin.

**The fundamental result:**

In the optimal screening contract:
- **H type gets the first-best contract** (no distortion at the top).
- **L type gets a distorted contract** (less than first-best quantity/quality).
- **H type earns information rent** (positive surplus).
- **L type earns zero rent** (just at participation threshold).

"No distortion at the top" is a hallmark of optimal screening in virtually every model.

---

### The Rothschild-Stiglitz insurance model

**Setup:**

- Two types: safe drivers (low risk p_L = 0.1) and risky drivers (high risk p_H = 0.4).
- Both have wealth W = $10,000; accident reduces wealth by D = $5,000.
- Expected loss: safe = $500; risky = $2,000.
- Insurers compete and offer contracts (α₁, α₂) = (premium, coverage).

**First-best (observable types):**

If types were observable:
- Safe: Full insurance at actuarially fair premium p_L × D = $500.
- Risky: Full insurance at actuarially fair premium p_H × D = $2,000.
- Both get full insurance; each pays their true expected cost. Efficient outcome.

**Second-best (unobservable types):**

If types are unobservable and an insurer tries to offer full insurance to safe drivers at $500:
- Risky drivers would buy this too (great deal for them — worth $2,000 but costs $500).
- Adverse selection: all risky drivers buy; the $500 policy loses money.

**Screening solution:**

Design a menu (A, B):
- Policy A: High premium, full coverage — intended for risky drivers.
- Policy B: Low premium, *partial* coverage (high deductible) — intended for safe drivers.

Safe drivers prefer B (don't expect to claim much; low premium is worth the limited coverage risk). Risky drivers prefer A (expect to claim frequently; full coverage is worth the premium).

**The critical result (Rothschild-Stiglitz):**

In equilibrium:
- Risky types get **full insurance at their actuarially fair premium** (first-best for them).
- Safe types get **partial insurance** — less than full coverage — at a lower premium.

The safe types are the "L types" who get the distorted contract. They would prefer full insurance (first-best) but can only get it at the risky premium — which is worse than partial insurance at the safe premium. The partial coverage is the screening tool that separates types.

---

### SaaS pricing as screening

This is where the theory becomes immediately practical. Every product that offers multiple pricing tiers is implementing a screening menu.

**Typical SaaS menu:**

| Plan | Features | Monthly Price | Target type |
|------|---------|---------------|-------------|
| Free | Core features, 1 user | $0 | Hobbyists, low-willingness-to-pay |
| Pro | All features, 5 users | $25 | Individual professionals (L type) |
| Business | All features, unlimited users, priority support | $99 | Small teams (H type) |
| Enterprise | Custom, SLA, dedicated support | $500+ | Large organisations (H type) |

The menu is designed so that:
- Enterprise customers don't downgrade to Business (IC: Business doesn't serve their needs — limited users, no SLA).
- Business customers don't downgrade to Pro (IC: 5-user limit would require painful restructuring).
- The pricing captures information rent from each type.

**The information rent problem in SaaS:**

Enterprise customers would pay $2,000/month if they had to. The platform extracts some of this through differential pricing, but must leave rent on the table — if Enterprise pricing is too high, they switch to Business and live with the constraints. The Enterprise discount (relative to willingness to pay) is the information rent.

**The distortion:**

The "Pro" plan is deliberately limited — not because 5 users is a natural technical constraint, but because a less constrained version would attract Business customers who don't need the full Business package. The artificial constraint on the Pro plan is the Rothschild-Stiglitz efficiency distortion in action.

---

## The Formal Picture

**The screening problem — key constraints:**

The designer offers a menu of two contracts: one aimed at H types (high-value customers), one at L types.

Each contract specifies a quality level and a price. The designer must satisfy four constraints:

- **H participates:** H's contract gives H at least as much as walking away.
- **L participates:** L's contract gives L at least as much as walking away.
- **H self-selects:** H prefers their own contract to the one meant for L.
- **L self-selects:** L prefers their own contract to the one meant for H.

The designer maximises expected revenue subject to all four constraints.

**What the optimal solution looks like:**

- L's participation constraint binds: L gets exactly zero surplus (the designer extracts everything from L).
- H's self-selection constraint binds: H is just indifferent between their contract and L's contract.
- **H gets the first-best contract** — no distortion at the top.
- **L gets a distorted contract** — less quality than L would ideally want, to make it less attractive to H types.
- **H earns information rent** — extra surplus the designer must leave H to prevent mimicry. This rent equals (the difference in H and L's valuation) × (the quality offered to L).
- The designer reduces L's quality specifically to reduce this rent — trading L's efficiency for lower payments to H.

**Revelation Principle:**

Any game or mechanism, no matter how complex, can be replaced by a simpler direct mechanism: just ask everyone to report their type, and give them the same outcome they would have received in the complex mechanism. Truth-telling is then an equilibrium. This means you can always search among truth-telling mechanisms — a huge simplification for mechanism design (Day 30).

---

### Screening vs. signalling: the two-sided view

| Dimension | Signalling (Day 28) | Screening (Day 29) |
|-----------|--------------------|--------------------|
| Who acts first | Informed party (sender) | Uninformed party (designer) |
| Mechanism | Costly observable action | Menu of contracts |
| Separation device | Differential signal cost | Differential contract attractiveness |
| Equilibrium type | Perfect Bayesian Equilibrium | Nash equilibrium of contract game |
| Information rent | Paid by uninformed party's lost profit | Explicitly captured by design |
| Example | Education signals ability | Insurance tiers separate risk types |

In practice, both mechanisms operate simultaneously — firms screen candidates with job descriptions *and* candidates signal with portfolios. The equilibrium involves both.

---

## Where It Breaks / What It Is Not

**Screening requires knowing type distributions.** To design an optimal menu, the uninformed party must know π_H and π_L (the proportions of each type) and the range of possible valuations. When these are unknown, the optimal menu cannot be computed. Firms often use approximate screening (a/b tests of pricing tiers) to learn the distribution.

**Types aren't always binary.** Two-type models are analytically clean but reality is continuous. With a continuous distribution of types, the screening problem has a continuous menu — each type gets a slightly different contract. The efficiency distortion applies throughout the distribution except at the top. This is the Myerson (1981) optimal auction result from Day 26 in a different guise.

**Screening can be degraded by competition.** In the Rothschild-Stiglitz model, competitive equilibrium may not exist! If one insurer offers a pooling contract (flat premium for all), another insurer can "cream-skim" the safe types by offering a contract that's slightly better for safe types but unattractive for risky types. This cream-skimming unravels the pooling contract — but there's no separating equilibrium either (the distorted safe contract is always dominated by a better offer from a competitor). The famous non-existence of equilibrium in competitive insurance markets is a deep result.

**Screening creates welfare distortions.** The L-type's distorted contract is inefficient — they get less than the first-best quantity/quality. The information rent paid to H types is also a transfer, not a social loss (H types are happy), but the distortion in L's contract is a genuine efficiency cost of information asymmetry.

---

## Try It Yourself

**Exercise 1 — Identify the screening mechanism (5 min)**

For each, explain how the pricing structure screens types and what IC constraint it exploits:

a) Amazon Prime ($139/year) vs. free shipping on individual orders ($5.99 each).
b) Business class vs. economy on airlines — both go to the same place.
c) A gym offering a 1-year membership at $500 or a monthly pay-as-you-go at $60/month.

<details>
<summary>Analysis</summary>

**a)** Screen: frequent vs. infrequent buyers. IC: frequent buyers (high type) rationally choose Prime ($139/year < 25 orders × $5.99 = $150). Infrequent buyers (low type) rationally choose pay-per-order (fewer orders, don't cross the $139 threshold). The price structure reveals ordering frequency without Amazon needing to observe it directly. Information rent: Prime members would pay much more than $139 for the full benefit — Amazon captures some of this through subscription pricing.

**b)** Screen: time-sensitive/comfort-preferring business travellers vs. leisure travellers. IC: business class is too expensive for leisure travellers (their WTP is well below the premium). The comfort and time-saving features of business class are worth the premium to business travellers (who bill the cost to clients or have high opportunity cost of time). The distortion: economy seats are made deliberately cramped — not because it's cheaper, but to make economy unattractive to business travellers, preserving the incentive to pay for business class.

**c)** Screen: committed vs. sporadic gym users. IC: committed users (high type, will use it every week) rationally choose annual ($500/year = ~$41.67/month). Sporadic users (low type, go occasionally) rationally choose monthly pay-as-you-go (only pay for months they use). Information rent: committed users would pay much more than $500 — the gym leaves substantial surplus with them.

</details>

---

**Exercise 2 — Design a screening menu (15 min)**

You run a cloud AI API with two customer types:

- **Startups** (L type): Maximum monthly spend = $500; value accurate pricing predictions at $1 per prediction.
- **Enterprises** (H type): Maximum monthly spend = $10,000; value accurate pricing predictions at $5 per prediction.

You can offer predictions at two quality levels: Standard (cost $0.20 each) and Premium (cost $0.80 each).

Design a two-tier pricing menu {(q_L, t_L), (q_H, t_H)} that maximises your expected profit. Assume equal proportions of each type.

a) What are the IR and IC constraints?
b) What contracts maximise profit? (Hint: start by setting L's IR to bind.)
c) What is the information rent you must leave for the H type?
d) Is there a distortion in the L-type contract? What is it?

---

**Exercise 3 — The Rothschild-Stiglitz insight (10 min)**

In competitive insurance markets, Rothschild and Stiglitz showed that a pooling equilibrium cannot survive cream-skimming.

a) Describe the cream-skimming argument: why can a competitor always unravel a pooling contract?
b) Why does this mean a separating equilibrium is the only candidate for a stable outcome?
c) But Rothschild and Stiglitz also showed that the separating equilibrium may not exist. What is the condition under which it fails?
d) What does the non-existence of equilibrium imply about the role of government or collective action in insurance markets?

<details>
<summary>Answer sketch for (a) and (b)</summary>

**a)** Suppose all types are offered a pooling contract with premium p̄ (average risk). Safe types are overcharged relative to their risk; risky types are undercharged. A competitor can offer a contract designed to attract safe types only: lower premium with a higher deductible. Safe types (who rarely claim) prefer this. Risky types (who claim often) prefer full coverage. The competitor attracts only safe drivers, leaving the original insurer with only risky drivers — the original insurer now loses money on their pooling contract. Cream-skimming unravels the pooling equilibrium.

**b)** If pooling cannot survive, only separating contracts (where types self-select into different plans) can be stable. But separating contracts require the safe-type plan to have a deductible (to deter risky types from mimicking), meaning safe types get less than full insurance.

</details>

---

## Connect It Back

Screening and signalling are two sides of the same information asymmetry problem. Day 28 (signalling): informed party moves first with a costly action. Day 29 (screening): uninformed party moves first with a menu design. In both cases, information rent flows to the high type, and some efficiency is lost.

Tomorrow (Day 30) — **Mechanism Design** — generalises both: instead of asking how to respond to information asymmetry, it asks how to *design the game itself* to achieve desired outcomes. The mechanism designer knows that players will respond strategically to any rules they set, and designs rules accordingly. The Revelation Principle (mentioned today) is the key tool: any mechanism can be replicated by a truth-telling direct mechanism, so we can always restrict attention to incentive-compatible designs.

**The question you should be able to answer now:**
*Why does the optimal screening contract distort the L-type's contract below first-best efficiency — and why is there "no distortion at the top" for H types?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Rothschild, Michael & Stiglitz, Joseph E. "Equilibrium in Competitive Insurance Markets." *Quarterly Journal of Economics*, 90(4):629–649, 1976. Read Sections I–III (pages 629–642). Section I sets up the model with clear notation; Section II derives the separating equilibrium; Section III shows pooling equilibrium cannot survive. The non-existence result in Section IV is more technical — read the intuition.

**If you want the deep version:**

1. Tirole, Jean. *The Theory of Industrial Organization*. MIT Press, 1988. Chapter 2, Sections 2.1–2.3 ("Adverse Selection and Price Discrimination"). — The standard treatment of second-degree price discrimination as a screening problem, connecting today's theory to practical product design. The SaaS pricing example is directly derived from this section.

2. Laffont, Jean-Jacques & Tirole, Jean. *A Theory of Incentives in Procurement and Regulation*. MIT Press, 1993. Chapter 1 ("The Theory of the Firm Under Moral Hazard and Adverse Selection"). — The bible of mechanism design applied to real-world contracting. Chapter 1 surveys both moral hazard and adverse selection in a unified framework. Dense but the formal setup is clean. Read Chapter 1, Sections 1.1–1.3.

3. Wilson, Robert. "Nonlinear Pricing." *Oxford University Press*, 1993. Chapter 1 ("Introduction: The Economics of Nonlinear Pricing"). — The treatment of screening as a nonlinear pricing problem. The connection between screening theory and the practice of designing pricing schedules (quantity discounts, two-part tariffs, tiered subscriptions) is made explicit. Read the introduction chapter — about 20 pages.


---

← [Day 28 — Signalling](day-28-signalling) &nbsp;|&nbsp; [Day 30 — Mechanism Design →](day-30-mechanism-design)
