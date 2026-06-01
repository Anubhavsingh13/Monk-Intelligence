*[Decision Science & Game Theory](../../../../README.md) · [Module 2 — Game Theory Fundamentals](../2-overview.md) · Day 17 of 65*

# Day 17 — The Prisoner's Dilemma & Social Dilemmas

> **Today's one idea:** Individually rational choices can produce collectively terrible outcomes — and the Prisoner's Dilemma is the precise mathematical model of this tragedy, which appears in climate negotiations, price wars, open-source funding, and AI safety coordination.
> **Reading time:** ~35 min · **Prereqs:** [Day 15](day-15-dominant-strategies.md), [Day 16](day-16-nash-equilibrium.md)
> **Primary source for today:** Dixit & Nalebuff, *The Art of Strategy*, Chapter 3 ("Prisoners' Dilemmas and How to Resolve Them").

---

## The Hook

Two suspects are arrested. The police separate them and offer each the same deal:

- If you testify against your partner and they stay silent: you go free, they get 10 years.
- If both testify against each other: you each get 6 years.
- If both stay silent: you each get 1 year (minor charge).

Each prisoner reasons:
- *"If my partner stays silent: I can testify and go free (better than 1 year)."*
- *"If my partner testifies: I'd better testify too (6 years beats 10 years)."*

Testifying (defecting) is the dominant strategy for both. Both testify. Both get 6 years. Had they both stayed silent, they'd each have served 1 year. The individually rational outcome is collectively disastrous.

This is not a thought experiment. It is a mathematical structure that appears with startling frequency in the real world — wherever individual incentives and collective welfare diverge.

---

## Building the Intuition

### The structure of the game

The Prisoner's Dilemma has a precise payoff structure. Let's name the payoffs:

- **T (Temptation):** What you get by defecting when your partner cooperates.
- **R (Reward):** What both get when both cooperate.
- **P (Punishment):** What both get when both defect.
- **S (Sucker):** What you get when you cooperate and your partner defects.

**The Prisoner's Dilemma requires:** T > R > P > S, *and* 2R > T + S.

The first condition (T > R > P > S) creates the dominant strategy to defect:
- Defect beats Cooperate when partner cooperates (T > R).
- Defect beats Cooperate when partner defects (P > S).

The second condition (2R > T + S) ensures that mutual cooperation is *collectively better* than the expected value of the mixed outcome — this is what makes it a dilemma, not just a game where defection is optimal everywhere.

**The canonical numerical version:**
```
                    B: Cooperate    B: Defect
A: Cooperate           (R, R) = (3,3)    (S, T) = (0,5)
A: Defect              (T, S) = (5,0)    (P, P) = (1,1)
```

Nash equilibrium: (Defect, Defect) with payoffs (1,1).
Pareto optimal outcome: (Cooperate, Cooperate) with payoffs (3,3).
The gap between 3 and 1 is the cost of the dilemma.

---

### Real-world Prisoner's Dilemmas

The structure T > R > P > S appears everywhere rational agents interact:

**Arms races:** Two nations. Cooperate = no weapons build-up. Defect = arm up.
- If the other nation disarms and you arm: you have military dominance (T).
- If both arm: mutual vulnerability and high costs (P).
- If both disarm: security and saved resources (R).
- If you disarm while they arm: you're exposed (S).
T > R > P > S. Both arm. Both worse off than mutual disarmament.

**Price wars:** Two airlines. Cooperate = maintain premium pricing. Defect = cut prices.
- Undercut while the other holds: you steal market share (T).
- Both undercut: price war, thin margins for everyone (P).
- Both hold: healthy margins, shared market (R).
- Hold while the other undercuts: you lose market share (S).
T > R > P > S. Race to the bottom.

**Open-source funding:** Many companies benefit from an open-source library. Cooperate = contribute engineering. Defect = free-ride.
- You free-ride while others contribute: maximum benefit, no cost (T).
- No one contributes: library degrades (P).
- All contribute: healthy library, shared cost (R).
- You contribute alone: you bear the cost, others benefit (S).
T > R > P > S. Under-investment in public goods. (Formalised on Day 33.)

**AI safety coordination:** Multiple AI labs. Cooperate = slow down for safety testing. Defect = race ahead.
- You race while others slow: first-mover advantage in deployment (T).
- All race: high risk, competitive pressure on safety (P).
- All slow down: safer ecosystem, shared costs (R).
- You slow while others race: you fall behind competitively (S).
T > R > P > S. Racing dynamics, under-investment in safety.

---

### Why the dilemma is "rational" but tragic

The genius of the Prisoner's Dilemma is that defection is *individually rational* — it satisfies all the axioms of rationality you've built since Day 1. Each player, reasoning correctly, arrives at a worse outcome than if they had reasoned differently.

This cannot be fixed by making the players smarter, more informed, or more carefully reasoning. Defection is dominant. The problem is not a reasoning failure — it is a *structural* feature of the payoff matrix.

This is the core insight: **collective action problems are not solved by better individual reasoning. They are solved by changing the game.** The mechanisms that resolve Prisoner's Dilemmas operate at the structural level:

1. **Repetition:** If the game is played repeatedly, cooperation can become individually rational. (Day 21.)
2. **Communication and binding agreements:** If players can make credible commitments, mutual cooperation can be enforced. (Day 20.)
3. **Incentive redesign:** A third party (regulator, platform, mechanism designer) changes payoffs so that R ≥ T — removing the temptation to defect. (Day 30.)
4. **Changing who is playing:** If players with aligned interests replace individually motivated players, cooperation can emerge without mechanism design.

---

## The Formal Picture

**Definition (Prisoner's Dilemma):**

A two-player symmetric game with strategies {Cooperate (C), Defect (D)} is a Prisoner's Dilemma if:

```
T > R > P > S    and    2R > T + S
```

The payoff matrix:
```
                    B: Cooperate    B: Defect
A: Cooperate        (R, R)          (S, T)
A: Defect           (T, S)          (P, P)
```
The game is symmetric — each player faces the same structure.

**Key properties:**
1. Defect is a strictly dominant strategy for both players.
2. (Defect, Defect) is the unique Nash equilibrium.
3. (Cooperate, Cooperate) is *better for everyone* than (Defect, Defect) — but it is not stable, because either player can defect and do better individually.
4. The equilibrium is collectively inefficient: rational individual play destroys shared value.

**The social dilemma generalisation (n players):**

A social dilemma is any game where:
1. Each player has a dominant strategy.
2. When all players follow their dominant strategy, the outcome is worse for *everyone* than if all had played differently.

The public goods game (Day 33) is the n-player generalisation of the Prisoner's Dilemma.

---

### The Prisoner's Dilemma in AI product design

**Ad auction dynamics:** Publishers want to charge high CPMs. Advertisers want to pay low. Each publisher defects from a "hold prices high" cartel by offering the advertiser a deal. Result: downward price pressure on ad inventory — a classic price war version of the dilemma.

**Data sharing for model training:** Multiple health systems could collectively produce better AI diagnostics by sharing patient data. Each system has incentive to receive others' data but not share (privacy risk, competitive advantage in fine-tuned models). Free-riding on a public data commons is T; all sharing is R; no one sharing is P. The result: under-investment in shared medical AI.

**Model evaluation transparency:** AI labs benefit collectively from transparent evaluation methodologies (it builds public trust). Each lab has individual incentive to use proprietary benchmarks that flatter their models. Race to the bottom on evaluation quality — until external pressure (regulation, high-profile failures) changes the incentives.

**Spam and content quality:** On user-generated platforms, "post low-quality content for engagement" dominates "post high-quality content for value" when the platform algorithm rewards engagement equally. Each creator defects; aggregate content quality degrades. Platform mechanisms (algorithmic down-ranking of low-quality content) are attempts to reshape the game's payoffs.

---

## Where It Breaks / What It Is Not

**The Prisoner's Dilemma is a model, not a law.** Real situations that look like Prisoner's Dilemmas often have additional structure: repeated play, reputation effects, private information, or non-binary strategies. Before invoking the dilemma, verify that the payoff structure actually satisfies T > R > P > S.

**"Collective action problem" ≠ Prisoner's Dilemma.** Not all collective action problems have the PD structure. Coordination games (Day 24) have a different structure: both parties want to cooperate, but need to agree on *which* equilibrium. Stag Hunt games have T < R — cooperation is *preferred* given trust, and the failure is about risk. Chicken games have T > R > S > P — the roles of defection and cooperation are different. Each requires different solutions.

**Resolving a Prisoner's Dilemma requires changing the game, not the reasoning.** Exhortation — "be altruistic," "think of the common good" — does not change the payoff matrix. Players who respond to exhortation are effectively changing their own payoff function (T is no longer greater than R if you care about the outcome for others). Mechanism design (Day 30) is the systematic approach.

**Not all apparent dilemmas are actual dilemmas.** In repeated games between the same players, cooperation can emerge as a Nash equilibrium — so the "dilemma" disappears. Day 21 covers this in full. Today's analysis applies to one-shot games or games between strangers.

---

## Try It Yourself

**Exercise 1 — Classify the dilemma (5 min)**

For each scenario, determine whether it has the Prisoner's Dilemma structure (T > R > P > S). If not, identify which condition fails:

a) Two restaurants both advertise heavily. Advertising costs $10K each. If neither advertises, they split the market equally ($30K each). If both advertise, they still split the market ($30K each) but net $20K each after costs. If one advertises and the other doesn't, the advertiser gets $45K, the non-advertiser gets $15K.

b) Two neighbours each decide whether to shovel the shared driveway. Shoveling costs effort (−2 utility). A clear driveway benefits both (+4 utility). If neither shovels, both get 0. If one shovels, both get the clear driveway benefit.

<details>
<summary>Analysis</summary>

**a)** Let's map the payoffs:
- T (advertise when partner doesn't): $45K
- R (both don't advertise): $30K
- P (both advertise): $20K
- S (don't advertise when partner advertises): $15K

T ($45K) > R ($30K) > P ($20K) > S ($15K). ✓

Check 2R > T + S: 2×$30K = $60K > $45K + $15K = $60K. NOT strictly greater — it ties.

This is a marginal case. In the standard strict PD, 2R > T + S strictly. Here 2R = T + S, which is the "Tit-for-Tat is neutral" boundary. The dominant strategy (advertise) still holds, but the social loss is exactly zero — mutual non-advertising is no better in total welfare than mixed outcomes. More like a Stag Hunt at the boundary.

**b)** Payoffs:
- Shovel alone: benefit $4 − effort cost $2 = $2 for you. Partner gets $4 − $0 = $4.
- Neither shovels: both get 0.
- Both shovel: both get $4 − $2 = $2.
- If partner shovels, you don't: you get $4, partner gets $2.

Mapping: T = $4 (free ride), R = $2 (both shovel), P = $0 (neither shovels), S = $2 (shovel alone).

Check: T ($4) > R ($2) > P ($0)... but S ($2) = R ($2). This is NOT a standard PD because S = R — you're indifferent between shoveling alone and both shoveling. This is closer to a public goods game with partial contributions.

**Lesson:** The Prisoner's Dilemma has a specific payoff structure. Not every collective action problem qualifies.

</details>

---

**Exercise 2 — Design the escape (15 min)**

The AI safety coordination dilemma: multiple AI labs face the structure T > R > P > S for "race ahead vs. slow for safety."

Design *three distinct mechanisms* that could shift this from a Prisoner's Dilemma to a cooperative outcome. For each mechanism, specify:
- What structural change it makes to the payoff matrix
- Which of the four conditions (T > R, R > P, P > S, 2R > T+S) it targets
- A real-world analogue of this mechanism in another domain

---

**Exercise 3 — The n-player version (10 min, L2)**

Consider n AI systems on a platform, each deciding whether to consume computational resources conservatively (Cooperate) or aggressively (Defect). Each system's defection slightly degrades performance for all others.

Let n = 100. Cooperate: each gets payoff 10. Defect: you get 12, but every other system's payoff drops by 0.1.

a) What is the total welfare if all cooperate? If all defect?
b) Is defection individually dominant?
c) What is the social cost of the dilemma (total welfare loss)?

<details>
<summary>Worked answer</summary>

**a)** All cooperate: 100 × 10 = **1,000 total welfare**

All defect: Each gets 12 (own gain) but suffers 99 × 0.1 = 9.9 loss from others' defection.
Net per agent: 12 − 9.9 = 2.1. Total: 100 × 2.1 = **210 total welfare**

**b)** Is defection dominant? Compare for any single agent:
- If others cooperate (all 99 cooperate): Defect → 12 − 0 = 12 vs. Cooperate → 10 − 0 = 10. Defect wins.
- If others defect (all 99 defect): Defect → 12 − 9.9 = 2.1 vs. Cooperate → 10 − 9.9 = 0.1. Defect wins.

Yes, **defection is dominant**.

**c)** Social cost = 1,000 − 210 = **790 units of welfare** destroyed by the dilemma. Nearly 80% of total potential welfare is destroyed by individually rational defection. This is an extreme case — real systems rarely have this level of congestion externality — but it illustrates why platform designers care deeply about rate-limiting, resource allocation, and mechanism design.

</details>

---

## Connect It Back

Today's page is the pivot of Module 2. The Prisoner's Dilemma demonstrates that Nash equilibrium can be collectively terrible — which immediately raises the question: how do we escape it? Three answers follow in sequence:

- **Day 20 (Commitment):** You can credibly commit to cooperation, changing the game's structure.
- **Day 21 (Repeated games):** When the game repeats, cooperation can be sustained as a Nash equilibrium through the threat of punishment.
- **Day 30 (Mechanism design):** A third party can redesign the payoffs so cooperation is dominant.

Tomorrow (Day 18), however, we handle a different puzzle: what happens when a game has *no* Nash equilibrium in pure strategies? The answer involves randomisation — and it changes how you think about everything from sports to pricing to AI adversarial robustness.

**The question you should be able to answer now:**
*Why can't the Prisoner's Dilemma be solved by making the players smarter or more rational — and what type of intervention can actually change the outcome?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Dixit & Nalebuff, *The Art of Strategy*, Chapter 3 ("Prisoners' Dilemmas and How to Resolve Them"). The best accessible treatment of real-world Prisoner's Dilemmas and the mechanisms that resolve them. Read the full chapter — ~25 pages, densely packed with examples.

**If you want the deep version:**

1. Axelrod, Robert & Hamilton, William D. "The Evolution of Cooperation." *Science*, 211(4489):1390–1396, 1981. — Preview of Day 23; shows that Tit-for-Tat solves the repeated Prisoner's Dilemma. Six pages — read it now alongside today's page to see where the story goes.

2. Hardin, Garrett. "The Tragedy of the Commons." *Science*, 162(3859):1243–1248, 1968. — The n-player version of the PD applied to shared resources. Seminal paper; 6 pages. Warning: Hardin's proposed solution (coercive privatisation) is controversial — focus on the problem framing, not the policy conclusion.

3. Ostrom, Elinor. *Governing the Commons: The Evolution of Institutions for Collective Action*. Cambridge University Press, 1990. Chapter 1. — Ostrom's Nobel Prize-winning response to Hardin: communities can self-govern shared resources without privatisation or top-down regulation, using locally evolved norms. The empirical counter to the pure PD prediction.


---

← [Day 16 — Nash Equilibrium](day-16-nash-equilibrium) &nbsp;|&nbsp; [Day 18 — Mixed Strategies →](day-18-mixed-strategies)
