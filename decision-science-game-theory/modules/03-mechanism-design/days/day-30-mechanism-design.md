*[Decision Science & Game Theory](../../../../README.md) · [Module 3 — Mechanism Design & Advanced Structures](../3-overview.md) · Day 30 of 65*

# Day 30 — Mechanism Design

> **Today's one idea:** Instead of analysing how players behave in a given game, mechanism design asks how to *construct* the game so that self-interested players, pursuing their own goals, produce the outcome the designer wants.
> **Reading time:** ~40 min · **Prereqs:** [Day 26](../../02-game-theory/days/day-26-auctions.md), [Day 29](day-29-screening-adverse-selection.md), [Day 16](../../02-game-theory/days/day-16-nash-equilibrium.md)
> **Primary source for today:** Myerson, Roger B. "Optimal Auction Design." *Mathematics of Operations Research*, 6(1):58–73, 1981. Also: Hurwicz, Leonid. "On Informationally Decentralized Systems." In *Decision and Organization*, McGuire & Radner (eds.), North-Holland, 1972.

---

## The Hook

In 2012, the Federal Communications Commission needed to reallocate radio spectrum from television broadcasters (who owned it) to mobile networks (who needed it). The problem: the government didn't know how much each broadcaster valued their licences. They couldn't just demand the spectrum back — they had to buy it. And they couldn't just hold a standard auction (mobile carriers bidding for spectrum) because the supply side (broadcasters) also needed to be incentivised to sell.

The FCC ran an **incentive auction** — a mechanism designed by economists Paul Milgrom and Robert Wilson (Nobel Prize 2020) that simultaneously ran a "reverse auction" (broadcasters bidding to give up spectrum) and a "forward auction" (mobile carriers bidding to acquire it). The mechanism collected honest bids from both sides and cleared the market efficiently. It raised $19.8 billion, repacked the TV spectrum into fewer channels, and freed up prime wireless spectrum for 5G.

This is mechanism design in practice: design the rules of the game before anyone plays, knowing exactly how rational players will respond to any set of rules, and choose rules that produce the outcome you want.

---

## Building the Intuition

### The mechanism designer's problem

Classic game theory takes the game as given and finds the equilibrium. Mechanism design inverts this:

**Classic game theory:** Given rules → find equilibrium behaviour.  
**Mechanism design:** Given desired outcome → find rules that produce it in equilibrium.

The designer has a goal (social welfare maximisation, revenue maximisation, efficient allocation) and can choose the rules of the game. But they cannot choose the players' preferences — those are private information. And they cannot force players to participate or to tell the truth.

**The three core constraints the designer faces:**

1. **Individual Rationality (IR):** Players must prefer participating to not participating. You can't force anyone to play.

2. **Incentive Compatibility (IC):** Players must prefer telling the truth (or taking the desired action) over any strategic misrepresentation. You can't observe types directly.

3. **Resource Feasibility:** The mechanism must balance its books — you can't promise more than you collect.

A mechanism that satisfies IC is called **incentive-compatible** or **strategyproof**. A mechanism that satisfies IR is called **individually rational**. The mechanism design problem is: find the rules that best achieve the designer's goal subject to these constraints.

---

### The Revelation Principle: why we only need to study truth-telling

From Day 29, you saw the Revelation Principle mentioned briefly. Today it becomes central.

**The Revelation Principle (Myerson 1979):**

For any mechanism M that produces an outcome via some equilibrium strategy profile, there exists a *direct mechanism* M' where:
1. Each player simply reports their type (their private information).
2. Truth-telling is an equilibrium of M'.
3. M' produces the same outcome as M.

**What this means:**

You don't need to think about complicated multi-round mechanisms, signals, auctions with complex bidding, or elaborate games. *Any outcome achievable by any mechanism can be achieved by a simple direct mechanism where everyone reports their type and tells the truth.*

This is the mechanism designer's equivalent of "without loss of generality" in mathematics. It collapses an enormous space of possible mechanisms into a single class: direct, incentive-compatible mechanisms.

**Why is this true?**

Because any equilibrium strategy profile of any complex mechanism can be "embedded" into a truth-telling mechanism: the mechanism just simulates what the complex mechanism would have produced if the players used their equilibrium strategies. Each player, knowing the mechanism simulates their optimal play, has no incentive to lie to it.

---

### Three canonical mechanism design results

**1. The Vickrey-Clarke-Groves (VCG) mechanism**

The generalization of Vickrey auctions to any setting where multiple resources are allocated. The VCG mechanism:
- Each player reports their value for each possible allocation.
- The allocation chosen maximises total reported value (efficiency).
- Each player pays a "Clarke tax" equal to the reduction in *other players'* total value caused by their presence.

**Example — two items, three bidders:**

Bidder 1 wants item A (value $100). Bidder 2 wants item B (value $80). Bidder 3 wants both A and B together (value $150).

Without Bidder 3: allocate A to Bidder 1 ($100) + B to Bidder 2 ($80) = $180 total.
With Bidder 3, optimal allocation: A+B to Bidder 3 ($150). Or A to 1, B to 2 ($180). The $180 allocation wins.

**Clarke tax for Bidder 3:** The allocation without Bidder 3 produces $180 for others; with Bidder 3, others get $0. Bidder 3's "tax" = $180 − $0 = $180. But Bidder 3 values the bundle at $150 < $180 — so Bidder 3 doesn't win. Allocation: $180 to Bidders 1 and 2.

**VCG properties:**
- **Strategyproof:** Each player's dominant strategy is to report their true value.
- **Efficient:** The allocation maximises total welfare.
- **Not necessarily revenue-maximising** (the designer may do better with a reserve price).

**2. Myerson's optimal auction**

When the designer maximises expected revenue (not welfare), the VCG mechanism is suboptimal. Myerson showed the revenue-maximising mechanism:

- Transform each bidder's true value into a "virtual value" — their true value minus an "information rent discount" that reflects how easily they could game the system by understating their type.
- Allocate to the bidder with the highest *virtual* value (not necessarily the highest true value).
- Set a reserve price: exclude bidders whose virtual value is negative.

The virtual value discounts each bidder's true value by their "information rent" — the surplus they'd extract by understating their type. Optimal allocation shades toward high virtual values (bidders with the least rent-extraction power).

**3. Gibbard-Satterthwaite Theorem (impossibility)**

If you want a mechanism that is:
- Strategyproof (dominant strategy truth-telling)
- Onto (any outcome can be produced)
- Non-dictatorial (no single agent always gets their way)

...then **such a mechanism does not exist** for three or more alternatives.

The impossibility: once you have three or more options to choose from, any strategyproof mechanism must be a dictatorship. This is the mechanism design analogue of Arrow's Impossibility Theorem (Day 31). The workaround: restrict to specific settings (money transfers, quasi-linear utility) where VCG-type mechanisms work.

---

### The mechanism design toolkit

| Problem | Mechanism | Key property |
|---------|-----------|--------------|
| Single item allocation | Vickrey (second-price) auction | Dominant-strategy truthful |
| Multiple items | VCG mechanism | Dominant-strategy truthful, efficient |
| Revenue maximisation | Myerson optimal auction | Incentive-compatible, optimal |
| Public goods provision | Lindahl mechanism / VCG | Efficient, IC |
| Matching markets | Gale-Shapley (deferred acceptance) | Strategyproof for one side |
| Spectrum reallocation | Incentive auction (Milgrom-Wilson) | Incentive-compatible, budget-balanced |

---

### Mechanism design in AI products

**Recommendation system design:**

A recommendation algorithm is a mechanism: it takes user signals (clicks, ratings, time spent) and produces ranked outputs. Users respond strategically to the mechanism — they click on titles, not articles. The mechanism designer's problem: design the signal-collection process so that user behaviour reveals their true preferences, not just their response to the algorithm's incentives.

Mechanism design insight: if the algorithm rewards high click-through rates, users (content creators) optimise for clicks, not quality. The mechanism creates an adversarial strategic response. A better mechanism rewards engagement that correlates with satisfaction — dwell time after click, return visits, saves — changing the IC constraint facing creators.

**Platform marketplace design:**

A two-sided marketplace (Airbnb, Upwork, App Store) is a mechanism for matching supply and demand. Key design choices:
- **Search ranking:** Who appears first? The IC constraint: sellers respond to ranking signals by optimising those signals (pricing, review solicitation, description keywords).
- **Review systems:** How are reviews collected? If reviews are public before the transaction, buyers post strategically. If reviews are simultaneous (Airbnb's double-blind review), the IC constraint changes — neither party can retaliate.
- **Fee structure:** Take rates, listing fees, subscription vs. per-transaction. The IC constraint for sellers: is it better to list many items cheaply or fewer at high margin?

**A/B testing as mechanism design:**

When you run an A/B test, you are implicitly designing a mechanism for learning user preferences. The mechanism design question: does the A/B test's design elicit honest user responses? If users know they're in a test, they may behave differently. The IC constraint: honest behaviour must be dominant strategy for both variants.

**RLHF (Reinforcement Learning from Human Feedback) as mechanism design (preview of Day 52):**

RLHF trains AI models using human preference judgments. The human raters are agents in a mechanism: they're asked to compare two outputs and pick the better one. The IC constraint: do raters have incentive to report their true preference, or do they strategically respond to the rating interface (anchoring on length, formatting, confident tone)? This is mechanism design applied to the AI training pipeline — the training signal is only as good as the mechanism that elicits it.

---

## The Formal Picture

**Mechanism design problem — the key elements:**

- There are n agents, each with a private type (their preferences, valuations, costs) that only they know.
- A social choice function describes the desired outcome for every possible combination of agent types.
- A mechanism is a set of rules: what each agent can communicate, and what outcome is produced from those communications.

**Implementation in dominant strategies:**

A mechanism implements a desired outcome in dominant strategies when each agent's best move — regardless of what others do — is to take the action the mechanism was designed around (e.g., report their true type). No agent can do better by lying or gaming the rules.

**Revelation Principle (plain version):**

Any complex mechanism with a dominant-strategy equilibrium can be replaced by a simple "tell-me-your-type" mechanism that produces the same outcome with truth-telling as the dominant strategy. This means designers only need to search among truth-telling mechanisms — every complex mechanism has an equivalent simple one.

**VCG mechanism:**

- Allocation: choose the outcome that maximises total reported value across all agents (efficiency).
- Payment for each agent: equal to the reduction in everyone *else's* total value caused by that agent's presence. (If you're not there, others share more; the VCG tax recovers this difference.)
- Each agent pays the harm they impose on others — giving them incentive to report honestly.

**Myerson's virtual value:**

Each bidder's "virtual value" discounts their true value by their information rent — the surplus they could extract by strategically understating their type. The optimal auction allocates to the bidder with the highest *virtual* value (not necessarily the highest true value), and excludes anyone with a negative virtual value (the reserve price mechanism).

---

## Where It Breaks / What It Is Not

**VCG is not budget-balanced.** The Clarke taxes collected may not equal the payments needed to finance the mechanism. In public goods settings, VCG often runs a deficit — it requires external financing. For markets that must be self-sustaining, VCG needs modification.

**Mechanism design assumes rational players.** If players make mistakes, misunderstand the mechanism, or have bounded rationality, the IC constraints become harder to satisfy. Real mechanism designers must account for the cognitive load the mechanism imposes. Simple mechanisms (second-price sealed-bid) are more robust to bounded rationality than complex ones (spectrum package auctions).

**Collusion breaks most mechanisms.** If a subset of agents can coordinate and share private information, the IC constraints face coalitional deviations, not just individual ones. Designing coalition-proof mechanisms is much harder — an open research frontier.

**The Gibbard-Satterthwaite theorem limits scope.** For discrete choice settings without money, no non-dictatorial mechanism is strategyproof. Most "real" mechanism design relies on money transfers to escape this impossibility — quasi-linear utility is the escape hatch.

**Implementation in practice ≠ implementation in theory.** Theoretical mechanisms often assume exact knowledge of type distributions (for Myerson's optimal auction) or exact rationality (for VCG). Real deployments require robustness analysis: what happens when the assumptions break slightly? The 2012 incentive auction succeeded partly because Milgrom and Wilson paid careful attention to practical constraints beyond theoretical optimality.

---

## Try It Yourself

**Exercise 1 — Apply the Revelation Principle (5 min)**

A firm runs an auction where bidders first submit a sealed bid and then have the option to raise their bid in a second round after seeing a signal. Describe (informally) the direct mechanism that produces the same equilibrium outcome via truth-telling.

<details>
<summary>Answer</summary>

The direct mechanism asks each bidder to report: (1) their value and (2) how they would revise their bid after seeing the signal. The mechanism then simulates the two-round process using these reports and produces the same allocation and payments. By the Revelation Principle, truth-telling in this direct mechanism is a dominant strategy, since the complex mechanism had an equilibrium.

Note: the direct mechanism may be more complex to specify than the original — the Revelation Principle guarantees equivalence, not simplicity.

</details>

---

**Exercise 2 — VCG computation (15 min)**

Three teams bid for two servers:
- Team A values Server 1 at $300, Server 2 at $200.
- Team B values Server 1 at $250, Server 2 at $350.
- Team C values Server 1 at $100, Server 2 at $150.

a) What is the efficient allocation (VCG outcome)?
b) Compute the Clarke tax for each team. (Clarke tax = total value of others without you − total value of others with you in the optimal allocation.)
c) Does each team have a dominant strategy to report truthfully? (Intuition, not full proof.)

<details>
<summary>Worked answer</summary>

**a)** Enumerate feasible allocations (each server goes to one team):

Option 1: S1→A ($300), S2→B ($350). Total: $650.
Option 2: S1→A ($300), S2→C ($150). Total: $450.
Option 3: S1→B ($250), S2→A ($200). Total: $450.
Option 4: S1→B ($250), S2→C ($150). Total: $400.
Option 5: S1→C ($100), S2→A ($200). Total: $300.
Option 6: S1→C ($100), S2→B ($350). Total: $450.

**Efficient allocation: S1→A, S2→B (total value $650).**

**b)** Clarke tax for each team:

**Team A's Clarke tax:**
- Without A: Best allocation for B and C: S1→B ($250) + S2→C ($150) = $400. OR S2→B ($350) + S1→C ($100) = $450. Best: $450.
- With A optimal: B gets $350 (S2), C gets $0. Others' value = $350.
- Clarke tax = $450 − $350 = **$100**. Team A pays $100.

**Team B's Clarke tax:**
- Without B: Best for A and C: S1→A ($300) + S2→C ($150) = $450.
- With B optimal: A gets $300 (S1), C gets $0. Others' value = $300.
- Clarke tax = $450 − $300 = **$150**. Team B pays $150.

**Team C's Clarke tax:**
- Without C: Best for A and B: S1→A ($300) + S2→B ($350) = $650.
- With C optimal: A gets $300, B gets $350. Others' value = $650.
- Clarke tax = $650 − $650 = **$0**. Team C pays nothing (C is "irrelevant" — they don't win anything and don't affect the allocation).

**c)** Yes — VCG has a dominant strategy to report truthfully. Your payment depends only on others' values, not your own report (the Clarke tax is computed from others' values). Your only influence over your own payment is through the allocation decision (which server you win). Overstating your value might win you a server you value less than you'd pay; understating might lose you a server you value more. True reporting maximises your net gain.

</details>

---

**Exercise 3 — Design a mechanism (15 min)**

You run a team of 10 engineers. You need to allocate one "20% time" project slot each month. Engineers have private preferences for each project (they care about the topic's match to their interests and career goals). You want an efficient allocation — the engineer who benefits most should get the slot.

a) Why can't you just ask each engineer "how much do you want this?" and pick the highest?
b) Design a mechanism that incentivises honest preference revelation. (Hint: think VCG, but you need a non-monetary "payment.")
c) What are the limitations of your mechanism in a real team context?

---

## Connect It Back

Mechanism design is the designer's superpower: instead of responding to game outcomes, you shape the game itself. The Revelation Principle reduces the design space to truth-telling mechanisms; VCG solves the efficiency problem; Myerson's optimal auction solves the revenue problem. The Gibbard-Satterthwaite theorem defines the limits.

Tomorrow (Day 31) confronts perhaps the most powerful impossibility result in social choice: **Arrow's Impossibility Theorem** — no voting system can simultaneously satisfy a short list of apparently reasonable fairness axioms. It is the political science equivalent of Gibbard-Satterthwaite, and its implications extend far beyond elections.

**The question you should be able to answer now:**
*Why does the Revelation Principle allow mechanism designers to restrict attention to direct, truth-telling mechanisms — and what does this assume about player rationality that may not hold in practice?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Myerson, Roger B. "Optimal Auction Design." *Mathematics of Operations Research*, 6(1):58–73, 1981. Read Sections 1–3 (pages 58–66). Section 1 states the model and the revelation principle; Section 2 derives the necessary and sufficient conditions for incentive compatibility; Section 3 states the optimal auction theorem. The virtual value formula is in Section 3 — it's worth understanding deeply.

**If you want the deep version:**

1. Nisan, Noam, Roughgarden, Tim, Tardos, Éva & Vazirani, Vijay V. (eds.). *Algorithmic Game Theory*. Cambridge University Press, 2007. Chapter 9 ("Algorithmic Mechanism Design"). — The computational perspective on mechanism design: when the VCG mechanism is computationally intractable, what approximations preserve incentive compatibility? Directly relevant to large-scale platform design where exact VCG is infeasible.

2. Milgrom, Paul. *Putting Auction Theory to Work*. Cambridge University Press, 2004. Chapter 1 ("Getting to Work") and Chapter 7 ("Package Auctions and Combinatorial Bidding"). — Milgrom's account of designing real-world auctions. Chapter 1 explains the gap between theory and practice; Chapter 7 covers the spectrum auction design that won him the Nobel Prize. Essential reading for anyone who will implement mechanisms rather than just study them.

3. Hurwicz, Leonid. "But Who Will Guard the Guardians?" *American Economic Review*, 98(3):577–585, 2008. — Hurwicz's Nobel Prize lecture. Surveys 60 years of mechanism design theory, from the socialist calculation debate to VCG to modern auction design. Non-technical and historically rich.


---

← [Day 29 — Screening & Adverse Selection](day-29-screening-adverse-selection) &nbsp;|&nbsp; [Day 31 — Voting & Arrow's Impossibility →](day-31-voting-arrows-impossibility)
