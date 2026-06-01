*[Decision Science & Game Theory](../../../../README.md) · [Module 2 — Game Theory Fundamentals](../2-overview.md) · Day 21 of 65*

# Day 21 — Repeated Games & the Emergence of Cooperation

> **Today's one idea:** When the same game is played repeatedly by the same players, the threat of future punishment can sustain cooperation as a Nash equilibrium — without any external enforcement, contract, or commitment device.
> **Reading time:** ~35 min · **Prereqs:** [Day 17](day-17-prisoners-dilemma.md), [Day 16](day-16-nash-equilibrium.md), [Day 20](day-20-commitment-credible-threats.md)
> **Primary source for today:** Axelrod, Robert & Hamilton, William D. "The Evolution of Cooperation." *Science*, 211(4489):1390–1396, 1981. Also: Dixit & Nalebuff, *The Art of Strategy*, Chapter 10 ("Repeated Dilemmas").

---

## The Hook

In the 1980s, Robert Axelrod ran a computer tournament. He invited game theorists, economists, and biologists to submit strategies for playing repeated Prisoner's Dilemma against each other. Fourteen strategies entered the first tournament. The winner — submitted by Anatol Rapoport — had exactly four lines of code:

1. On the first move, cooperate.
2. On every subsequent move, do whatever your partner did last round.
3. That's it.

The strategy is called **Tit-for-Tat**. It won. Then Axelrod ran a second tournament, told everyone that Tit-for-Tat had won the first one, invited 62 entries — and Tit-for-Tat won again.

How does the simplest strategy beat every sophisticated alternative? Because it threads a precise needle: it cooperates, it retaliates immediately when defected on, it forgives immediately when the partner returns to cooperation. It is nice, provocable, forgiving, and clear. The folk wisdom of "do unto others as they do unto you" turns out to be the game-theoretic optimum — under specific conditions that today's page makes precise.

---

## Building the Intuition

### Why repetition changes everything

In a one-shot Prisoner's Dilemma, defection is dominant. Cooperating makes you a sucker — you get S while they get T. No rational player cooperates once.

But add a twist: the same two players will meet again. And again. Now your choice today sends a signal about what you will do tomorrow. A player who defects today gets T instead of R — but may trigger retaliation that converts all future rounds from R to P. If the future is valuable enough, cooperation today buys cooperation tomorrow. The long shadow of the future can make cooperation individually rational.

**The key quantity: the discount factor δ.**

Players value future payoffs less than present ones (time preference, uncertainty about whether the game continues). If you receive a payoff of 1 next round, you value it as δ today, where 0 < δ < 1. Two rounds from now: δ². The infinite stream of 1s per round is worth 1/(1−δ) today.

Think of δ as measuring "how much does the future matter?" If δ = 0, you are purely myopic — only the current round counts. If δ ≈ 1, you care almost equally about every future round. Cooperation becomes rational precisely when δ is large enough.

---

### The Grim Trigger strategy

The simplest cooperation-enforcing strategy is **Grim Trigger**:

- Round 1: Cooperate.
- Every subsequent round: Cooperate if both players have always cooperated. If anyone ever defected, defect forever.

It's ruthless — one defection triggers eternal punishment — but it's analytically clean.

**When does Grim Trigger sustain cooperation?**

Consider the temptation to defect. Starting from mutual cooperation:

- **Payoff from cooperating forever:** R + δR + δ²R + ... = R/(1−δ)
- **Payoff from defecting once (then being punished forever):** T + δP + δ²P + ... = T + δP/(1−δ)

Cooperation is individually rational iff:

```
R/(1−δ) ≥ T + δP/(1−δ)
```

Rearranging:

```
R − R·δ ≥ T·(1−δ) + δP  [multiply through by (1-δ)]
R − δR ≥ T − δT + δP
R − T ≥ δ(R − T) + δ(P − ... )
```

Clean form after algebra:

```
δ ≥ (T − R) / (T − P)
```

**This is the cooperation threshold.** Call it δ*.

With the canonical PD payoffs (T=5, R=3, P=1):

```
δ* = (5 − 3) / (5 − 1) = 2/4 = 0.5
```

If players discount the future at a rate where they value next round at 50 cents per dollar of today's payoff (or better), cooperation is sustained. If players are highly impatient (δ < 0.5 here), defection wins.

---

### The Folk Theorem: cooperation is not the only possibility

The Grim Trigger analysis establishes that cooperation can be an equilibrium. But the **Folk Theorem** (informal precursor to Nash, later formalised by Aumann, Shapley, Rubinstein) says something stronger:

> In an infinitely repeated game, **any feasible payoff pair that gives each player more than their minimax value** can be sustained as a Nash equilibrium — for a sufficiently high discount factor δ.

**What this means:**

- There are many Nash equilibria in the repeated game — not just the cooperative one.
- You can sustain cooperation, partial cooperation, taking turns exploiting each other, and more.
- The repeated game does not uniquely predict cooperation — it opens a vast space of possible equilibria.

**The prediction problem:** The Folk Theorem is too permissive. It says almost anything is an equilibrium for high δ. The question becomes: which equilibrium actually emerges? Axelrod's tournament answers this empirically — Tit-for-Tat wins in practice — but the theory alone cannot select it.

---

### Tit-for-Tat vs. Grim Trigger: why the kinder strategy wins

Grim Trigger sustains cooperation but punishes defection *forever* — which is irrational if mistakes or noise occur. In noisy environments where a player might accidentally defect (a communication error, a supply chain delay that looks like defection), Grim Trigger locks both players into eternal punishment from a single error.

**Tit-for-Tat is forgiving:** if you defect once and then return to cooperation, I cooperate on the next move. The punishment is proportionate and temporary.

**Tit-for-Tat's four properties** (Axelrod's analysis):
1. **Nice:** Never defects first.
2. **Provocable:** Retaliates immediately after a defection (not pushover).
3. **Forgiving:** Returns to cooperation as soon as partner does.
4. **Clear:** Transparent enough that partners can learn what you're doing and adapt.

These properties collectively prevent being exploited while allowing reconciliation. In Axelrod's tournament, strategies that violated any of these lost.

---

### Finite vs. infinite repetition: the backward induction trap

What if the game has a **known, finite** end — say, exactly 100 rounds?

Apply backward induction. In round 100 (the last), this is effectively a one-shot game — no future consequences. Both players defect (dominant strategy).

Now move to round 99. Both players know that round 100 will involve defection regardless. So round 99 is also effectively a last round — both defect.

And round 98. And round 97. All the way back to round 1.

**Result: In a finitely repeated game with a known endpoint, the only subgame perfect equilibrium is to defect every round.**

The backward induction unravels cooperation from the end. This is mathematically correct — and empirically wrong. Real players in lab experiments cooperate for many rounds in finite games before defecting near the end (the "end-game effect").

The resolution: in practice, players face uncertainty about when the game ends (is this partner someone I'll see again?), their reputations extend beyond this game, or they're not fully rational in the backward induction sense. The infinite-horizon model better captures the intuition — treat δ as the probability of meeting again, not just time discounting.

---

## The Formal Picture

**Infinitely repeated game — the key elements:**

- The same stage game is played each round: round 1, round 2, round 3, and so on forever.
- Both players share a discount factor δ (a number between 0 and 1) that captures how much a future payoff is worth today.
- Both players see the full history of past moves before choosing each new action.
- A payoff stream of 1 unit per round (forever) is worth **1/(1−δ)** today — because each future unit is discounted by δ, δ², δ³, and so on, and these all add up to 1/(1−δ).

**Strategy in a repeated game:**

Your strategy can depend on everything that has happened before. This is what gives punishment its power: you can condition tomorrow's action on today's behaviour.

**Grim Trigger — in plain terms:**

- Round 1: Cooperate.
- Every later round: Cooperate if *both* players have cooperated in *every* round so far. Defect forever the moment anyone ever defects.

**The cooperation condition:**

Under Grim Trigger, mutual cooperation is a Nash equilibrium if and only if:

```
δ ≥ (T − R) / (T − P)
```

**Folk Theorem (informal):**

Any payoff outcome that (a) is physically achievable and (b) gives each player more than they could guarantee themselves by defecting no matter what the other does — can be sustained as a Nash equilibrium, as long as the discount factor δ is high enough.

In short: for patient-enough players, almost any mutually acceptable outcome can be an equilibrium in a repeated game. The repeated game does not predict a unique outcome — it opens many possibilities.

---

### Repeated games in AI and technology

**Vendor lock-in as repeated game:**

A platform (e.g., cloud provider) and its enterprise customers play a repeated game. Mutual cooperation: platform provides good service, customer remains loyal. The platform is tempted to extract value through price increases once the customer is locked in (defection). Customers, anticipating this, are reluctant to deepen reliance. The solution: long-term contracts and API stability commitments are commitment devices that approximate Grim Trigger — breaking them triggers customer defection. Seen this way, API versioning policies are folk-theorem equilibrium-selection mechanisms.

**Repeated auctions and bidding rings:**

Firms that interact repeatedly in procurement auctions may sustain collusion through Grim Trigger strategies ("if you bid aggressively on my contracts, I'll undercut you on yours"). This is why competition regulators are particularly suspicious of markets with repeated interactions among the same bidders.

**Recommendation system dynamics:**

Content creators and recommendation algorithms play a repeated game. If a creator produces high-quality long-form content, they expect sustained algorithmic promotion (cooperation). If the algorithm rewards clickbait (defecting on quality creators), creators respond by producing clickbait. The Folk Theorem says the outcome depends on how much creators value future algorithmic goodwill (their δ). Short-termism in creator monetisation → low δ → defection equilibrium → degraded content ecosystem.

**Multi-agent RL:**

When RL agents share an environment across many episodes, they can develop cooperation through experience without any explicit game-theoretic reasoning — effectively "learning" the Folk Theorem through trial and error. Day 49 covers this in detail.

---

## Where It Breaks / What It Is Not

**Repetition is not sufficient — δ must be large enough.** In markets with high churn (new customers every quarter, short sales cycles), players don't value future interactions highly. Cooperation is hard to sustain. This explains why customer service quality is lower in tourist traps than in local regulars' establishments.

**The Folk Theorem is a permissiveness result, not a prediction.** It tells you what *can* be sustained, not what *will* be. Without a theory of equilibrium selection, you cannot predict whether players land on the cooperative equilibrium or one of the punishing ones.

**Noise and mistakes break Tit-for-Tat.** In a deterministic world, Tit-for-Tat is stable. With noise (accidental defections), two Tit-for-Tat players can get locked in a mutual retaliation spiral from a single error. **Generous Tit-for-Tat** (cooperating even after one defection with some probability) outperforms pure Tit-for-Tat in noisy environments — but the principle of forgiveness requires careful calibration.

**Reputation requires observability.** The shadow of the future only works if your partner can observe your past actions. In anonymous markets, you have no reputation to protect. Online markets have invested heavily in review systems, verification, and persistent identities precisely to enable reputation-based cooperation.

---

## Try It Yourself

**Exercise 1 — Compute the cooperation threshold (5 min)**

For each payoff specification, compute δ* = (T−R)/(T−P). What does the threshold tell you about the difficulty of sustaining cooperation?

a) T = 4, R = 3, P = 1 (mild temptation game)
b) T = 10, R = 3, P = 1 (large temptation)
c) T = 4, R = 3, P = 3 (punishment = cooperation payoff)

<details>
<summary>Worked answers</summary>

**a)** δ* = (4−3)/(4−1) = 1/3 ≈ 0.33. Low threshold — cooperation is easy. Even players who heavily discount the future will cooperate. Low-temptation games are easy to sustain.

**b)** δ* = (10−3)/(10−1) = 7/9 ≈ 0.78. High threshold — players need to value the future at 78 cents per current dollar for cooperation to be rational. Large temptation requires a very long shadow.

**c)** δ* = (4−3)/(4−3) = 1/1 = 1. The threshold is 1 — cooperation is impossible for any δ < 1. When punishment returns the same payoff as cooperation, there is nothing to lose from defecting once cooperation breaks down. Punishment loses its bite.

**Lesson:** Effective punishment (P << R) is as important as high temptation being manageable. Mechanism designers should worry about both.

</details>

---

**Exercise 2 — The end-game unravelling (10 min)**

Two players play a 3-round Prisoner's Dilemma (T=5, R=3, P=1, S=0). They both know the game ends after round 3.

a) Using backward induction, show that (Defect, Defect) in every round is the unique SPE.
b) Now suppose the game ends after each round with probability (1−p), continuing with probability p. If p = 0.7, what is the effective discount factor δ = p? Does the threshold for cooperation depend on whether the uncertainty is about time (δ) or probability of continuation (p)?
c) Why do experiments show humans cooperate in finite repeated games despite the backward induction prediction?

<details>
<summary>Discussion</summary>

**a)** Round 3: last round, no future consequences. Both defect (dominant strategy in stage game). Round 2: both know round 3 will be (D,D) regardless of round 2's play. So round 2 is also a last round → both defect. Round 1: same logic. Every round is (D,D). Unique SPE.

**b)** The effective discount factor is δ = p = 0.7. The cooperation condition is δ ≥ (T−R)/(T−P) = 2/4 = 0.5. Since 0.7 > 0.5, cooperation can be sustained! Note: mathematically, uncertainty about continuation is identical to time discounting — both enter the payoff formula in the same way.

**c)** Several explanations: (1) players are not fully rational backward inductors; (2) social preferences (fairness, reciprocity) add non-monetary payoffs to cooperation; (3) players may have uncertainty about whether the game is truly finite (even in labs); (4) reputation extends beyond the lab experiment. This is the "Centipede Game" failure of backward induction from Day 19's "Where It Breaks" section.

</details>

---

**Exercise 3 — Design the repeated game (15 min)**

You are building a marketplace platform. Buyers and sellers interact repeatedly over time.

a) Identify the stage game: what is the Prisoner's Dilemma structure (what is T, R, P, S for sellers considering product quality)?

b) What mechanisms can the platform deploy to raise δ (increase the weight sellers place on future interactions)?

c) What mechanisms can the platform deploy to lower the temptation T or raise punishment P (change the stage game payoffs directly)?

d) For each mechanism, give a real product example. (Hint: star ratings, account suspension, delayed payouts, and seller certification are all relevant.)

---

## Connect It Back

Today's insight dissolves the Prisoner's Dilemma's apparent hopelessness: cooperation is not heroic irrationality. Given a long enough shadow of the future, cooperation is individually rational — the threat of tomorrow's punishment makes defection today too costly. The Folk Theorem shows that cooperation is just one of many sustainable equilibria; Tit-for-Tat's empirical dominance suggests that the right equilibrium requires niceness, provocation, and forgiveness in equal measure.

Tomorrow (Day 22) is a rest and synthesise day — you'll consolidate everything from Module 2 before the second half of game theory. Day 23 then moves to **evolutionary game theory**, where the same ideas resurface in a world without explicit reasoning: strategies survive or die based on payoff performance, not individual calculation.

**The question you should be able to answer now:**
*Why does backward induction unravel cooperation in finitely repeated games — and why does changing the game from finite to uncertain-horizon (with the same expected length) restore the possibility of cooperation?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Axelrod, Robert & Hamilton, William D. "The Evolution of Cooperation." *Science*, 211(4489):1390–1396, 1981. Read the full six pages. The tournament methodology, Tit-for-Tat's properties, and the biological extension to evolution of cooperation are all in the original paper — one of the most cited in social science.

**If you want the deep version:**

1. Axelrod, Robert. *The Evolution of Cooperation*. Basic Books, 1984 (revised 2006). Chapters 2 ("The Success of Tit for Tat") and 4 ("How to Choose Effectively"). — The book-length treatment of the tournament results and their biological, political, and business implications. Chapter 2 breaks down exactly why Tit-for-Tat succeeds against every other strategy submitted. Required reading for anyone working on multi-agent cooperation.

2. Fudenberg, Drew & Tirole, Jean. *Game Theory*. MIT Press, 1991. Chapter 5 ("Repeated Games"). — The rigorous treatment of the Folk Theorem, including the precise conditions under which different payoff vectors can be sustained. Section 5.1 (definitions) and 5.2 (the Folk Theorem statement) are most relevant. Harder than Axelrod, but the formal precision is valuable for understanding exactly what the theorem says and doesn't say.

3. Dixit & Nalebuff, *The Art of Strategy*, Chapter 10 ("Repeated Dilemmas"). — Business applications of repeated game logic: airline pricing, vendor relationships, and political cooperation. The most accessible treatment of why long-term relationships change strategic incentives. Read this if the formal treatment above feels dense.


---

← [Day 20 — Commitment & Credible Threats](day-20-commitment-credible-threats) &nbsp;|&nbsp; [Day 22 — Rest & Synthesise →](day-22-rest-synthesise)
