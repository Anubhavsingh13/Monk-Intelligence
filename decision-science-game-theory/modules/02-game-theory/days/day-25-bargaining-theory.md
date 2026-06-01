*[Decision Science & Game Theory](../../../../README.md) · [Module 2 — Game Theory Fundamentals](../2-overview.md) · Day 25 of 65*

# Day 25 — Bargaining Theory

> **Today's one idea:** When two players split a surplus, the outcome is determined not by fairness intuitions but by the credibility of each player's threat to walk away — and Rubinstein's alternating-offers model proves that the player who is more patient captures a larger share.
> **Reading time:** ~35 min · **Prereqs:** [Day 19](day-19-sequential-games.md), [Day 20](day-20-commitment-credible-threats.md), [Day 16](day-16-nash-equilibrium.md)
> **Primary source for today:** Rubinstein, Ariel. "Perfect Equilibrium in a Bargaining Model." *Econometrica*, 50(1):97–109, 1982. Sections 1–3. Also: Dixit & Nalebuff, *The Art of Strategy*, Chapter 11 ("Bargaining").

---

## The Hook

Two people discover a $100 bill on the ground. They must agree on how to split it, or neither gets anything. What happens?

Your intuition probably says: $50/$50. It's fair. They'll split it evenly.

But wait — suppose one of them has somewhere to be in 5 minutes and the other is in no hurry. Does the impatient one end up with less? What if one of them already has $500 in their pocket and the other is broke? Does the desperate one get less, even though fairness says $50/$50?

Game theory says: yes, and yes. The fair split is the outcome only when the players are symmetric — equal patience, equal outside options. Break the symmetry, and the equilibrium splits asymmetrically. The theory is precise about how: the more patient player extracts more, and the degree of asymmetry is pinned down by the discount factors.

This is Rubinstein's 1982 bargaining model — one of the cleanest applications of backward induction to a real economic problem, producing a unique subgame perfect equilibrium from a seemingly open-ended negotiation.

---

## Building the Intuition

### The Nash bargaining solution: axiomatic approach

Before Rubinstein, Nash (1950) took a different approach: instead of modelling the bargaining process, he axiomatised the outcome.

**The setup:** Two players split a surplus S. If they fail to agree, each gets their disagreement payoff (d₁, d₂) — what they walk away with. The net surplus to divide is (S − d₁ − d₂).

**Nash's four axioms for a fair solution:**

1. **Efficiency:** They don't leave value on the table.
2. **Symmetry:** If both players are identical, they split equally.
3. **Scale invariance:** The solution doesn't change if you rescale one player's payoffs.
4. **Independence of irrelevant alternatives:** If you remove options that aren't chosen, the solution doesn't change.

**The Nash bargaining solution:** The unique outcome satisfying all four axioms is:

```
Maximise (u₁ − d₁)(u₂ − d₂)
```

The product of the players' gains over their disagreement payoffs is maximised. With equal disagreement payoffs (d₁ = d₂ = 0), this gives the 50/50 split. With unequal disagreement payoffs, the player with a higher outside option (higher dᵢ) captures a larger share — because the solution maximises the *product of gains*, not total surplus.

**Why does the outside option matter?**

If Player 1's outside option is $30 (they can walk away with $30), and Player 2's is $0, the Nash solution for splitting $100 is:

```
Maximise (u₁ − 30)(u₂ − 0)   subject to u₁ + u₂ = 100
```

Let u₁ = x, u₂ = 100 − x:
```
Maximise (x − 30)(100 − x)
```

Take derivative and set to zero:
```
(100 − x) − (x − 30) = 0
130 − 2x = 0
x = 65
```

Player 1 gets $65, Player 2 gets $35. The player with the better outside option captures more — not because of power in the moment, but because their disagreement point is higher.

---

### Rubinstein's alternating-offers model: the process matters

Nash's axiomatic approach tells you the outcome but says nothing about the bargaining process. Rubinstein asked: what if we model the actual back-and-forth?

**The setup:**

- Player 1 makes an offer at time t=0.
- Player 2 accepts or rejects. If accept: done. If reject: Player 2 makes a counter-offer at t=1.
- Player 1 accepts or rejects. If reject: Player 1 counter-offers at t=2.
- Continue forever (infinite horizon).
- Each round of delay is costly: Player 1 discounts future payoffs by δ₁, Player 2 by δ₂.

**Key insight:** Delay is costly to both sides. This makes the game finite-equivalent — neither player wants to drag it out indefinitely.

**Solving by backward induction:**

Because the game is infinite, we can't literally start from the end. Instead, we use the game's **stationarity** — at any odd period where Player 1 proposes, the subgame looks identical to the game at t=0. Let s₁ be Player 1's equilibrium share in this subgame.

**Player 2's acceptance condition:**

Player 2 accepts Player 1's offer of (s₁, 1−s₁) if it's at least as good as rejecting and making their own offer next period.

If Player 2 rejects and proposes next period, they'll offer Player 1 their equilibrium share from a P2-proposing subgame. Let s₂ be Player 2's equilibrium share when Player 2 proposes. Player 1 accepts if offered at least δ₁·s₁ (the discounted value of their share).

So Player 2, proposing, offers Player 1 exactly δ₁·s₁ and keeps (1 − δ₁·s₁). Hence s₂ = 1 − δ₁·s₁.

**Player 1's acceptance condition:**

By symmetry, when Player 1 proposes, Player 2 accepts any offer ≥ δ₂·s₂. Player 1 offers exactly δ₂·s₂ to Player 2 and keeps (1 − δ₂·s₂). Hence s₁ = 1 − δ₂·s₂.

**Solving the two equations:**

```
s₁ = 1 − δ₂·s₂
s₂ = 1 − δ₁·s₁
```

Substitute s₂:
```
s₁ = 1 − δ₂(1 − δ₁·s₁)
s₁ = 1 − δ₂ + δ₁·δ₂·s₁
s₁(1 − δ₁·δ₂) = 1 − δ₂
```

**Rubinstein's solution:**

```
s₁* = (1 − δ₂) / (1 − δ₁·δ₂)

s₂* = δ₂(1 − δ₁) / (1 − δ₁·δ₂)
```

And the agreement happens **immediately** — in round 0, Player 1 offers (s₁*, 1−s₁*) and Player 2 accepts. No delay.

---

### Reading the formula

**Symmetric case (δ₁ = δ₂ = δ):**

```
s₁* = (1 − δ) / (1 − δ²) = (1 − δ) / ((1 − δ)(1 + δ)) = 1/(1 + δ)
s₂* = δ/(1 + δ)
```

Player 1 gets 1/(1+δ) and Player 2 gets δ/(1+δ). First-mover advantage — Player 1 gets more because they propose first. As δ → 1 (both very patient), s₁* → 1/2. The first-mover advantage vanishes in the limit — very patient players split evenly.

**Asymmetric patience:**

If Player 1 is more patient (δ₁ > δ₂ — they discount the future less), then (1 − δ₂) in the numerator is larger, and δ₂ in the denominator is smaller. Player 1 gets more.

**The key intuition:** The cost of delay falls disproportionately on the *impatient* player. The impatient player accepts worse deals to avoid delay. The patient player extracts the gains.

**Numerical example:**

δ₁ = 0.9 (very patient), δ₂ = 0.5 (impatient). Splitting $100.

```
s₁* = (1 − 0.5) / (1 − 0.9 × 0.5) = 0.5 / (1 − 0.45) = 0.5 / 0.55 ≈ 0.909
```

Player 1 gets $90.90, Player 2 gets $9.10. Patience is extremely valuable here.

---

### The connection between Rubinstein and Nash

As the time between offers becomes very short (near-continuous bargaining), Rubinstein's solution converges to the **asymmetric Nash bargaining solution**:

> Each player's share is inversely proportional to their discount rate — the more impatient you are (higher discount rate), the smaller your share.

Nash's axiomatic solution and Rubinstein's process-based solution are consistent — they predict the same outcome. This is one of the beautiful convergences in game theory: the axioms capture exactly the forces that emerge from the strategic process.

---

## The Formal Picture

**Nash Bargaining Problem:**

A bargaining problem has two elements: a set of feasible payoff outcomes the players can agree on, and a disagreement point — what each player gets if talks break down.

The Nash bargaining solution picks the outcome that maximises the product of both players' gains above their disagreement point:

```
Maximise (Player 1's payoff − Player 1's disagreement payoff)
       × (Player 2's payoff − Player 2's disagreement payoff)
```

subject to the outcome being feasible and each player getting at least their disagreement payoff.

**Rubinstein's Theorem (1982):**

In the infinite-horizon alternating-offers game, there exists a unique subgame perfect equilibrium. Player 1 proposes (s₁*, 1−s₁*) at t=0; Player 2 accepts. Equilibrium shares:

```
s₁* = (1 − δ₂) / (1 − δ₁δ₂)
s₂* = δ₂(1 − δ₁) / (1 − δ₁δ₂)
```

**Note:** s₁* + s₂* = (1−δ₂ + δ₂ − δ₁δ₂) / (1 − δ₁δ₂) = (1 − δ₁δ₂) / (1 − δ₁δ₂) = 1. The solution is efficient — no surplus wasted.

**Outside options:**

If Player i can walk away at any point and receive outside option oᵢ, the equilibrium is modified: any proposed share must exceed oᵢ or the player exercises the outside option immediately. Outside options function like disagreement points — they set a floor on what each player accepts.

---

### Bargaining in AI and technology

**Salary negotiation:**

The classic application. Rubinstein says: the offer you make first (if you have proposer power), combined with your relative patience (how long you can afford the process to drag on) and your outside option (best competing offer), determine the equilibrium. Specific implications:
- Never make a first offer you're not serious about — the SPE predicts immediate acceptance.
- Your outside option (competing offer) is the most powerful lever — it shifts d₁, directly changing the NBS.
- Patience is a real asset — if you can wait longer without pain (financial cushion, current job), you extract more.

**Platform-developer revenue sharing:**

When a platform negotiates app store fees with a developer, the developer's outside option (other distribution channels) and the platform's impatience (growth targets) determine the split. Apple's 30% fee structure held for years because developers had no credible outside option — their d₂ was very low. The Epic Games lawsuit was an attempt to change the game by establishing (legally enforced) alternative distribution as an outside option.

**M&A negotiations:**

Acquirer and target negotiate a valuation. Rubinstein says the acquirer's patience (can wait for another target) and the target's urgency (burn rate, competing acquisition offers) determine the price. Founders in dire financial straits negotiate from a weaker position — low δ, small outside option.

**API pricing negotiations (B2B SaaS):**

A startup selling a data API to enterprise clients bargains over price. The enterprise's outside option (build internally, buy from competitor) and their procurement timeline (quarterly budget cycles = lower patience in Q4) determine the equilibrium contract price. Sales cycles are bargaining processes, and Rubinstein's model predicts the outcome precisely.

---

## Where It Breaks / What It Is Not

**Rubinstein's model assumes complete information.** Both players know each other's discount factors. In practice, discount rates are private information — a key source of strategic misrepresentation in negotiations. "We're very flexible on timing" can be a lie. Day 27 (information asymmetry) covers how incomplete information changes bargaining.

**The model abstracts away from relationship and reputation.** The Rubinstein equilibrium is cold and precise: propose the exact equilibrium share, accept only if at equilibrium. Real negotiators care about the relationship, future interactions, and social norms. The $50 split is not the Rubinstein solution for equal players — it's the Nash bargaining solution, which real humans often converge on faster than the alternating-offers process would predict.

**Unique SPE doesn't mean the outcome is predictable in practice.** The game has a unique SPE, but reaching it requires that both players know the SPE exists and can compute it. In experiments, outcomes diverge from the SPE prediction when the equilibrium is complex. Simpler games (like the Ultimatum Game from Day 19) show that humans reject unfair offers — the "optimal" SPE offer of $0.01 to Player 2 gets rejected in experiments.

**Outside options can be complex.** In real negotiations, outside options are themselves the result of other negotiations. Your competing job offer is the result of another bargaining process. Modelling this requires sequential bargaining trees that quickly become analytically intractable.

---

## Try It Yourself

**Exercise 1 — Compute the Rubinstein split (10 min)**

Two firms negotiate a joint venture. They will split $1M in surplus. Firm A discounts at δ_A = 0.8 per round; Firm B discounts at δ_B = 0.6. Firm A proposes first.

a) Compute s_A* and s_B*.
b) Which firm gets more, and why?
c) If Firm B can improve their outside option from $0 to $300K (Firm B's disagreement payoff), how does the negotiation change qualitatively?

<details>
<summary>Worked answer</summary>

**a)**
```
s_A* = (1 − δ_B) / (1 − δ_A × δ_B)
     = (1 − 0.6) / (1 − 0.8 × 0.6)
     = 0.4 / (1 − 0.48)
     = 0.4 / 0.52
     ≈ 0.769

s_B* = 1 − 0.769 = 0.231
```

Firm A gets ~$769K; Firm B gets ~$231K.

**b)** Firm A gets more for two reasons: (1) first-mover advantage in proposing, and (2) Firm A is more patient (δ_A = 0.8 vs. δ_B = 0.6). Firm B bears a higher cost of delay. The first-mover advantage compounds the patience advantage.

**c)** With Firm B's outside option at $300K, Firm B will not accept any offer below $300K. The feasible surplus to split is now $1M − $300K = $700K above Firm B's floor. Qualitatively: Firm B's effective "floor" increases, Firm A must concede at least $300K to Firm B, and the Rubinstein split then applies to the remaining $700K. Firm B's effective share increases substantially — outside options are the most powerful lever in negotiation.

</details>

---

**Exercise 2 — The ultimatum game (10 min)**

A simplified one-round Rubinstein: Player 1 makes a take-it-or-leave-it offer to Player 2 of a split of $100. Player 2 accepts or rejects; no counter-offer.

a) What is the SPE? What does Player 1 offer?
b) In experiments, offers below $20–25 are frequently rejected. What does this tell you about the payoff model?
c) Suppose Player 1 can choose to offer either before a public audience or privately. In which condition do you expect offers to be higher, and why? What game-theoretic concept does this invoke?

<details>
<summary>Discussion</summary>

**a)** SPE: Player 1 offers Player 2 $0.01 (the minimum positive amount), keeping $99.99. Player 2 accepts, since any positive amount is better than $0. The SPE is technically $0 for Player 2 if offers must be integers — Player 1 offers $1 and keeps $99.

**b)** The model assumes Player 2's utility is purely monetary. But humans have **social preferences**: they care about fairness and are willing to pay a cost (rejecting a positive offer) to punish unfair behaviour. The payoff function is missing terms for fairness and spite. The backward induction is correct given the stated payoffs — the payoffs are wrong.

**c)** Offers are higher when made publicly. The public audience introduces **reputation effects** — Player 1 cares about being seen as fair (social image, future interactions with observers). This converts a one-shot game into a repeated-game-like context, where the "future interaction" is with the audience's perception. The game-theoretic concept: reputation as a commitment device (Day 20) — public commitments make fair offers credible.

</details>

---

**Exercise 3 — Map a real negotiation (15 min)**

Pick a negotiation you've been involved in or observed (salary, vendor contract, project scope).

a) Identify: Who is Player 1 (first proposer)? What were the discount factors qualitatively — who was more impatient? What was each party's outside option?

b) What did the Rubinstein model predict, qualitatively?

c) What was the actual outcome? Where did it diverge from the model's prediction? Was the divergence due to social preferences, incomplete information, or a different game structure than assumed?

---

## Connect It Back

Bargaining theory shows that "splitting the difference" is only the right answer when both players are symmetric. Asymmetric patience and different outside options drive outcomes far from equal splits — and the theory is precise about how far. The key levers are patience (δ), the outside option (disagreement point), and proposer advantage.

Tomorrow (Day 26) introduces **auction theory** — a special case of mechanism design where the seller designs the rules of the game before it's played, and the optimal design depends on strategic reasoning about how bidders will respond. Auctions combine all of Module 2's tools: Nash equilibrium, information asymmetry, and the strategic value of truthful revelation.

**The question you should be able to answer now:**
*Why does the more patient player capture a larger share in Rubinstein's model — and how does improving your outside option have the same effect as becoming more patient?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Rubinstein, Ariel. "Perfect Equilibrium in a Bargaining Model." *Econometrica*, 50(1):97–109, 1982. Read Sections 1–3 (pages 97–103). Section 1 sets up the model; Section 2 states the theorem; Section 3 proves uniqueness. The proof is a beautiful backward induction argument — follow it carefully.

**If you want the deep version:**

1. Osborne, Martin J. & Rubinstein, Ariel. *A Course in Game Theory*. MIT Press, 1994 (freely available at arielrubinstein.tau.ac.il). Chapter 7 ("Bargaining Games"). — The rigorous treatment of both Nash and Rubinstein bargaining, with formal proofs and extensions to asymmetric information. Sections 7.1 and 7.2 cover today's material precisely.

2. Lax, David A. & Sebenius, James K. *3-D Negotiation: Powerful Tools to Change the Game in Your Most Important Deals*. Harvard Business Review Press, 2006. Chapters 4–5. — The practitioner version of bargaining theory: how to improve your outside option ("walk away" alternatives), how to restructure the negotiation game, and how to manage sequence. Directly applicable to career and business negotiations.

3. Nash, John F. "The Bargaining Problem." *Econometrica*, 18(2):155–162, 1950. — Nash's original axiomatic treatment. Eight pages, dense but foundational. The four axioms and the derivation of the Nash product are presented with unusual clarity. Read alongside today's page to see how the axiomatic and procedural approaches connect.


---

← [Day 24 — Coordination & Focal Points](day-24-coordination-focal-points) &nbsp;|&nbsp; [Day 26 — Auctions →](day-26-auctions)
