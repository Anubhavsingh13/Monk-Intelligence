*[Decision Science & Game Theory](../../../../README.md) · [Module 3 — Mechanism Design & Advanced Structures](../3-overview.md) · Day 32 of 65*

# Day 32 — Public Goods & the Free Rider Problem

> **Today's one idea:** Public goods are non-excludable and non-rival — which makes them prone to free-riding — and the mechanism design challenge is to induce voluntary provision without being able to exclude non-contributors.
> **Reading time:** ~35 min · **Prereqs:** [Day 17](../../02-game-theory/days/day-17-prisoners-dilemma.md), [Day 30](day-30-mechanism-design.md), [Day 21](../../02-game-theory/days/day-21-repeated-games.md)
> **Primary source for today:** Samuelson, Paul A. "The Pure Theory of Public Expenditure." *Review of Economics and Statistics*, 36(4):387–389, 1954. Also: Ostrom, Elinor. *Governing the Commons: The Evolution of Institutions for Collective Action*. Cambridge University Press, 1990. Chapter 1.

---

## The Hook

In 1974, a researcher studying tuna fishing in the Pacific made a striking observation. When fishing vessels operated in the same waters without coordination, each captain had a simple incentive: catch as many tuna as possible before others did. The result was systematic overfishing — stocks collapsed, and all captains ended up worse off than if they had collectively restrained their catch.

This is the "tragedy of the commons," named by Garrett Hardin in 1968. But here is what Hardin missed: in 1990, Elinor Ostrom published *Governing the Commons* showing that communities around the world — Swiss alpine villages, Japanese fishing villages, irrigation systems in Spain — had independently developed institutions to manage shared resources sustainably, without privatisation or government control.

The tuna collapse happened because the Pacific fishing grounds had no such institution. The Swiss alpine villages survived because they built one. The difference was not the resource — it was the governance mechanism.

Today's page is about why public goods and common resources generate free-rider problems, how standard markets fail to provide them, and what mechanisms — from Pigouvian taxes to Ostrom's community institutions to modern platform contributions — can resolve the problem.

---

## Building the Intuition

### Two dimensions: exclusion and rivalry

The standard 2×2 taxonomy of goods:

```
                      Excludable            Non-Excludable
                  ┌─────────────────┬──────────────────────┐
    Rival         │  Private goods  │  Common-pool resources│
                  │  (food, cars)   │  (fisheries, aquifers)│
                  ├─────────────────┼──────────────────────┤
    Non-rival     │  Club goods     │  Public goods        │
                  │  (Netflix,      │  (national defence,   │
                  │  toll roads)    │  basic research, air) │
                  └─────────────────┴──────────────────────┘
```

**Non-excludable:** Once provided, you cannot prevent people from consuming it. You can't stop a free rider from benefiting from national defence, clean air, or open-source software.

**Non-rival:** One person's consumption doesn't reduce availability for others. An additional person watching a Netflix movie doesn't reduce quality for existing viewers (though it may at scale).

**The market failure:**

Markets work for private goods because firms can exclude non-payers (price = entry). For non-excludable goods, exclusion is impossible — so firms can't charge. Without revenue, private provision is unsustainable. Markets underprovide (or don't provide at all) public goods.

---

### The free-rider problem as an n-player Prisoner's Dilemma

Consider n individuals deciding whether to contribute to a public good (clean neighbourhood, open-source library, safety research).

Each person's payoff:
- If they contribute (cost c): everyone benefits from the public good (value b each), but the contributor bears cost c.
- If they free-ride: everyone benefits from others' contributions without bearing any cost.

**With one contributor and n−1 free-riders:**
- Contributor gets: b − c.
- Free-riders get: b.

For any individual, free-riding is dominant (get b instead of b−c) as long as c > 0.

**Nash equilibrium:** Everyone free-rides. No one contributes. Public good is not provided (or severely underprovided).

**Efficient outcome:** Everyone contributes. Total value = nb − nc = n(b−c). If b > c (which we assume for the good to be worth providing at all), the efficient total surplus is positive — but the Nash equilibrium delivers zero.

The gap is n(b−c) − 0 = n(b−c). This is the social cost of the free-rider problem — and it grows with group size n.

---

### The Samuelson condition: optimal public good provision

What is the socially optimal level of a public good?

**Private good:** Optimal provision when marginal cost = each individual's marginal benefit. (You don't sum benefits across people — each unit goes to one person.)

**Public good:** Optimal provision when marginal cost = **sum** of all individuals' marginal benefits. Because the good is non-rival, each additional unit of the public good benefits *all* n people simultaneously.

**Samuelson condition:**

```
Marginal cost of one more unit = Sum of all users' marginal benefits
```

The marginal cost of an additional unit of the public good should equal the sum of all marginal benefits across all users.

**Why markets fail the Samuelson condition:**

In a market, a private provider charges at most one person's marginal benefit (they can only collect from payers; non-payers free-ride). Optimal provision requires collecting from all n people — impossible without excludability. The market's equilibrium provision is far below Samuelson's optimal.

---

### Three resolution mechanisms

**Mechanism 1: Pigouvian subsidy / taxation**

A government taxes private activities that cause public bads (carbon tax → correct for under-provision of clean air) or subsidises activities that cause public goods (R&D tax credit → correct for under-provision of basic research).

*Pigouvian subsidy:* Set subsidy = positive externality per unit. This makes the private marginal benefit equal to the social marginal benefit, restoring efficient provision.

*Problem:* Requires knowing the externality's true value — which is private information. Standard adverse selection returns: if government asks "how much do you value clean air?" everyone overstates (they pay a carbon tax) or understates (they want more subsidy).

**Mechanism 2: VCG for public goods (Clarke mechanism)**

The Clarke mechanism (from Day 30's VCG) can elicit honest values for public goods. Each agent states their value for the public good; the good is provided if the sum of stated values exceeds cost; each agent pays a "Clarke tax" equal to the cost they impose on others by changing the provision decision.

*Result:* Truth-telling is a dominant strategy. Efficient provision follows the Samuelson condition (approximately).

*Problem:* VCG for public goods runs a budget deficit (the Clarke taxes collected are less than the cost of provision). The mechanism requires external financing or a separate funding source.

**Mechanism 3: Community institutions (Ostrom's solution)**

Elinor Ostrom's Nobel Prize-winning insight: communities can manage common resources through locally developed rules, monitoring, and graduated sanctions — without government or markets.

**Ostrom's eight design principles for successful common-pool resource institutions:**

1. **Clearly defined boundaries:** Know who has rights to the resource.
2. **Rules fit local conditions:** One size doesn't fit all.
3. **Collective choice arrangements:** Those affected by rules participate in modifying them.
4. **Monitoring:** Behaviour and resource condition are monitored.
5. **Graduated sanctions:** Violations met with proportionate response (not immediately severe).
6. **Conflict resolution mechanisms:** Low-cost dispute resolution available.
7. **Minimal recognition of rights to organise:** External authorities recognise the local institution.
8. **Nested enterprises:** For large systems, governance in nested layers.

These principles are not abstract — they emerged from empirical study of hundreds of communities managing forests, fisheries, water systems, and pastures across centuries. The communities that followed them survived; those that didn't collapsed.

---

### Open-source software as a public goods solution

Open-source software is a canonical modern public good:
- **Non-excludable:** Anyone can download and use the code.
- **Non-rival:** Your use doesn't prevent others from using the same code.
- **Samuelson underprovision expected:** Markets should produce too little.

And yet Linux, Apache, Python, and thousands of critical infrastructure components are thriving public goods. How?

**Several mechanisms at work simultaneously:**

1. **Private benefits from public contribution:** Contributing to open source has private value (reputation, job market signalling from Day 28, learning). The contribution cost is offset by private benefits that are not available to free-riders.

2. **Repeated game dynamics (Day 21):** Major users of open-source software (Google, Meta, Microsoft) contribute back because they play a repeated game with the community. Defecting (taking without giving) triggers reputational damage. The shadow of the future sustains cooperation.

3. **Platform governance (Ostrom's principles):** Successful open-source projects have governance structures — clearly defined contribution rules, code review processes, release authorities, conflict resolution procedures. Linux Foundation, Apache Software Foundation, etc. implement Ostrom's principles digitally.

4. **Selective incentives:** GitHub's contribution graph, PyPI download counts, and conference talk opportunities are selective incentives (available only to contributors) that compensate for the non-excludability of the code itself.

---

### The tragedy of the commons vs. Ostrom's solution

Hardin (1968) argued that commons inevitably collapse unless privatised or regulated by government. Ostrom (1990) showed this is empirically false for many communities.

**The key variable is not the resource type — it is the community structure:**

Communities that sustainably manage commons share:
- Small size (repeated game dynamics are more feasible)
- Clear boundaries (who is in the group is known)
- Mutual monitoring (defections are observable)
- Graduated sanctions (punishment is proportionate)
- Historical relationships (reputation effects are long-lived)

Communities that fail tend to be large, anonymous, short-horizon, with weak monitoring. Pacific tuna fishing (large, international, anonymous) failed. Swiss alpine villages (small, historically rooted, with strong social norms) succeeded.

**The AI lab coordination problem (from Day 17, revisited):**

AI safety as a public good: safety research benefits all labs (reducing risk of catastrophic failure), but each lab bears the full cost of its own safety work. Free-rider incentives: don't do safety research, let others do it, benefit from the results.

Applying Ostrom: the AI safety coordination problem is more tractable if:
- Community size stays small (major labs know each other, repeated game dynamics apply)
- Monitoring is possible (model evaluations, capability thresholds are at least partially observable)
- Graduated sanctions exist (reputational costs for deploying unsafe models)
- Governance institutions are built (AI safety commitments, external audits, red-teaming)

This is Ostrom's framework applied to an emerging common-pool resource problem.

---

## The Formal Picture

**Public good (definition):**

A public good has two properties:
- **Non-excludable:** Once provided, everyone benefits — you cannot prevent non-payers from consuming it.
- **Non-rival:** One person consuming it does not reduce how much is available to others.

**Samuelson optimality condition:**

The socially optimal amount of a public good is found by maximising the sum of all agents' benefits minus the cost of providing the good. The optimality condition says:

> **Sum of all agents' marginal benefits = Marginal cost of provision**

This differs from private goods (where one person's marginal benefit = marginal cost) because the public good benefits everyone simultaneously. The optimal level is higher precisely because each additional unit benefits all n people.

**Nash equilibrium provision:**

When individuals voluntarily decide how much to contribute, each person only considers their *own* marginal benefit — ignoring the benefit they'd create for everyone else. Each person contributes until their own marginal benefit equals their private cost, which happens at a much lower level than Samuelson's optimum.

The gap: Nash equilibrium provides far too little. The shortfall grows with the number of people n — because each additional person means more free-riding and more ignored benefits.

**Lindahl equilibrium (theoretical optimum):**

Assign each person a personalised price for the public good equal to their own marginal willingness to pay. The personalised prices sum to the total marginal cost. If each person pays their personalised price and demands the good at that price, the result achieves Samuelson's optimum. The problem: you need to know each person's willingness to pay — private information that everyone has incentive to understate.

---

## Where It Breaks / What It Is Not

**The commons vs. public good distinction matters.** Common-pool resources (fisheries, aquifers) are rival but non-excludable — each fish caught is gone. Public goods (basic research, national defence) are non-rival — they don't deplete. The mechanism solutions differ: for commons, Ostrom's institutions manage depletion; for public goods, Samuelson's condition manages provision quantity.

**Club goods reveal a policy design lever.** By making a good excludable (toll roads, paywalled content), you convert it from public to club. This enables market provision but introduces the distortion of excluding people who could benefit at zero marginal cost. The trade-off: market sustainability vs. efficient access.

**Ostrom's solution requires specific conditions.** The design principles don't automatically produce sustainable governance — they describe conditions under which self-governance tends to succeed. Large, anonymous, short-horizon communities fail. Technology can help (monitoring via sensors, reputation systems at scale, digital contracts) — but the underlying game-theoretic challenge remains.

**Crowdfunding is a partial mechanism.** Kickstarter-style crowdfunding attempts to solve the public goods problem by making provision contingent (the good is only produced if a threshold is reached). This reduces the free-rider problem (individual contributions are pivotal near the threshold) but doesn't solve it fully — many campaigns underfund.

---

## Try It Yourself

**Exercise 1 — Classify the good (5 min)**

For each, classify as private good, club good, common-pool resource, or public good. Explain the key characteristic:

a) A congested highway during rush hour.
b) A public park (uncrowded).
c) A private gym membership.
d) Wikipedia.
e) Fish in an unclaimed ocean fishery.

<details>
<summary>Answers</summary>

**a) Common-pool resource.** The highway is rival (your car occupies space, creating congestion) but non-excludable (you can't prevent anyone from using a public road). Classic common-pool: leads to congestion externality (a tragedy of the commons for road capacity).

**b) Public good.** Uncrowded park is non-rival (your picnic doesn't prevent others' picnics) and non-excludable (fencing a public park is unusual). Subject to free-rider problems in maintenance funding.

**c) Club good.** Rival in the sense that crowding occurs (a gym with too many members has long queues), but excludable (you need a membership). Markets can provide club goods efficiently.

**d) Public good.** Wikipedia is non-rival (reading an article doesn't reduce others' access) and non-excludable (Wikimedia Foundation doesn't block non-payers). Classic free-rider problem: millions use it, far fewer donate. Wikipedia's donation model is a voluntary contribution mechanism.

**e) Common-pool resource.** Fish are rival (each fish caught is gone) and non-excludable in open ocean (no ownership rights). Subject to the tragedy of the commons — the original Hardin example.

</details>

---

**Exercise 2 — Compute underprovision (10 min)**

Five people share a neighbourhood. Each values a communal garden at v(G) = 10√G, where G is total investment. The cost of providing G is $G.

a) Find the Samuelson optimal G*.
b) Find the Nash equilibrium contribution per person (assuming symmetric equilibrium: each contributes g so G = 5g). 
c) Compute the welfare gap between G* and Nash equilibrium.

<details>
<summary>Worked answer</summary>

**a)** Samuelson condition: Σᵢ ∂uᵢ/∂G = ∂C/∂G.

Each ∂uᵢ/∂G = 10/(2√G) = 5/√G. Sum = 5 × 5/√G = 25/√G.

Set equal to marginal cost (= 1):
25/√G* = 1 → √G* = 25 → G* = 625.

Total social value: 5 × 10√625 = 5 × 250 = $1,250. Cost: $625. Net surplus: $625.

**b)** Nash equilibrium: each person sets their private MB = MC.

5/√G = 1 → √G = 5 → G = 25.

In symmetric equilibrium, each contributes g = G/5 = 5.

Total social value: 5 × 10√25 = 5 × 50 = $250. Cost: $25. Net surplus: $225.

**c)** Welfare gap = $625 − $225 = $400.

The Nash equilibrium captures only 225/625 = 36% of potential social surplus. 64% is lost to free-riding. Note that G_Nash/G* = 25/625 = 4% — the investment level is catastrophically below optimal.

</details>

---

**Exercise 3 — Ostrom's principles in your context (15 min)**

Identify a common resource, shared asset, or public good that your team, company, or community manages (a shared codebase, a team knowledge base, a shared budget, a community forum, meeting time as a collective resource).

For each of Ostrom's eight design principles, assess whether your current governance satisfies it (yes/partial/no) and identify the biggest gap.

Then propose one concrete change that would most improve the governance of this resource. Ground your proposal in today's mechanism design logic.

---

## Connect It Back

Public goods and common resources are where individual rationality most visibly fails collective welfare. The free-rider problem is the n-player Prisoner's Dilemma with the Samuelson condition as the efficiency benchmark. Market mechanisms fail because exclusion is impossible; government mechanisms fail because information is private; Ostrom's institutional solutions work when communities have the right structure.

Tomorrow (Day 33) moves to **network effects and platform economics** — where the value of a good depends on the number of users, creating coordination dynamics (Day 24) at market scale. Platform markets are simultaneously public goods problems (the platform's value is a quasi-public good) and coordination games (users need to coordinate on the same platform).

**The question you should be able to answer now:**
*Why does the Nash equilibrium provision of a public good satisfy each individual's optimality condition but violate the Samuelson condition — and why does this gap grow with group size n?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Ostrom, Elinor. *Governing the Commons: The Evolution of Institutions for Collective Action*. Cambridge University Press, 1990. Chapter 1 ("Reflections on the Commons"). — Ostrom's challenge to Hardin: empirical evidence that communities can self-govern. Read the full 28 pages — they contain the design principles, the case studies, and the theoretical framework. This chapter alone is worth the price of the book.

**If you want the deep version:**

1. Samuelson, Paul A. "The Pure Theory of Public Expenditure." *Review of Economics and Statistics*, 36(4):387–389, 1954. Three pages. The original derivation of the Samuelson condition. Dense but brief — the math is accessible with today's background. Read to see how the optimality condition emerges from first principles.

2. Hardin, Garrett. "The Tragedy of the Commons." *Science*, 162(3859):1243–1248, 1968. Six pages. The classic article that set up the commons debate. Read Hardin first, then Ostrom — the contrast is the lesson. Warning: Hardin's policy recommendations (coercive privatisation, population control) are controversial and widely criticised; focus on the problem framing.

3. Ledyard, John O. "Public Goods: A Survey of Experimental Results." In *Handbook of Experimental Economics*, Kagel & Roth (eds.). Princeton University Press, 1995. Pages 111–194. — The comprehensive survey of laboratory experiments on voluntary public goods provision. Covers the key results: contributions start high and decay over rounds, communication and reputation restore cooperation, punishment mechanisms dramatically increase provision. Essential empirical grounding for the theory.


---

← [Day 31 — Voting & Arrow's Impossibility](day-31-voting-arrows-impossibility) &nbsp;|&nbsp; [Day 33 — Network Effects & Two-Sided Markets →](day-33-network-effects-platforms)
