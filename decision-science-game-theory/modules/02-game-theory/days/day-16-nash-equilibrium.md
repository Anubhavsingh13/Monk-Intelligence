*[Decision Science & Game Theory](../../../../README.md) · [Module 2 — Game Theory Fundamentals](../2-overview.md) · Day 16 of 65*

# Day 16 — Nash Equilibrium

> **Today's one idea:** A Nash equilibrium is a strategy profile where no single player can improve their payoff by switching to a different strategy — given that everyone else holds theirs fixed. It is the central solution concept of game theory.
> **Reading time:** ~40 min · **Prereqs:** [Day 14](day-14-what-is-a-game.md), [Day 15](day-15-dominant-strategies.md)
> **Primary source for today:** Nash, John F. "Equilibrium Points in N-Person Games." *PNAS*, 36(1):48–49, 1950. (Read in full — it is two pages.) Also: Tadelis, *Game Theory*, Chapter 6.

---

## The Hook

It is 1950. John Nash, a 22-year-old graduate student at Princeton, submits a two-page paper to the *Proceedings of the National Academy of Sciences*. The paper proves, using a fixed-point theorem, that every finite game has at least one equilibrium in which no player has an incentive to unilaterally change their strategy.

The concept he defines becomes the unifying foundation of modern economics, evolutionary biology, political science, and — now — multi-agent AI. It is used to analyse how prices emerge in markets, why arms races spiral, how biological strategies evolve, how auctions should be designed, and how autonomous agents interact when deployed simultaneously.

The equilibrium is called a Nash equilibrium. Nash received the Nobel Prize in Economics in 1994, 44 years after publishing this two-page paper.

Today you understand it well enough to find Nash equilibria in real games and know what they predict — and what they don't.

---

## Building the Intuition

### The stability idea

Forget equations for a moment. Think about stability.

You are in a situation with at least one other player. A strategy profile is *stable* if, given what everyone else is doing, no one can do better by switching. If you could do better by switching, you would — and that means the current situation was not a stable prediction. If no one can do better by switching, the situation is self-reinforcing. Nobody has a reason to deviate. That's a Nash equilibrium.

**A concrete example — driving conventions:**

In the UK, everyone drives on the left. In the US, everyone drives on the right. 

In the UK: given that *everyone else* drives on the left, your best response is to drive on the left. If you unilaterally switched to the right, you would crash. So "drive on left" given "everyone drives on left" is a Nash equilibrium.

In the US: same logic, mirrored. "Drive on right" given "everyone drives on right" is a Nash equilibrium.

Notice: *both* conventions are Nash equilibria. Neither is "more correct" — they are both stable outcomes that self-reinforce once established. The model does not tell you *which* equilibrium will emerge (that's Day 24's focal points), only that once established, neither can be unilaterally broken.

---

### Finding Nash equilibria: the underline method

For small payoff matrices, the easiest method is to underline the best responses.

**Step 1:** For each column (each strategy of B), underline A's highest payoff in that column.
**Step 2:** For each row (each strategy of A), underline B's highest payoff in that row.
**Step 3:** Any cell where *both* payoffs are underlined is a Nash equilibrium.

**Example — Battle of the Sexes:**

Two partners want to spend the evening together. Partner A prefers the ballet; Partner B prefers a basketball game. But both prefer being together to being apart.

```
                    B: Ballet     B: Basketball
A: Ballet           (2, 1)        (0, 0)
A: Basketball       (0, 0)        (1, 2)
```

**Step 1** — underline A's best response to each B strategy:
- B plays Ballet: A gets 2 (Ballet) vs 0 (Basketball) → underline A's 2 in (Ballet, Ballet)
- B plays Basketball: A gets 0 (Ballet) vs 1 (Basketball) → underline A's 1 in (Basketball, Basketball)

**Step 2** — underline B's best response to each A strategy:
- A plays Ballet: B gets 1 (Ballet) vs 0 (Basketball) → underline B's 1 in (Ballet, Ballet)
- A plays Basketball: B gets 0 (Ballet) vs 2 (Basketball) → underline B's 2 in (Basketball, Basketball)

**Step 3** — cells where both payoffs are underlined:

```
                    B: Ballet     B: Basketball
A: Ballet           (2̲, 1̲)        (0, 0)
A: Basketball       (0, 0)        (1̲, 2̲)
```

Both (Ballet, Ballet) and (Basketball, Basketball) are Nash equilibria. 

This is a **coordination game with multiple equilibria** — neither equilibrium is obviously correct. The model predicts stability at both, but not which one. Tomorrow (Day 24 in due course) will address coordination; today just note that equilibria can be multiple.

---

### The Prisoner's Dilemma revisited

Yesterday you found that Defect is the dominant strategy for both players. Let's verify this is the Nash equilibrium using the underline method:

```
                    B: Cooperate    B: Defect
A: Cooperate           (3, 3)        (0, 5)
A: Defect              (5, 0)        (1, 1)
```

**A's best responses:**
- B=Cooperate: 5 > 3 → underline A's 5 in (Defect, Cooperate)
- B=Defect: 1 > 0 → underline A's 1 in (Defect, Defect)

**B's best responses:**
- A=Cooperate: 5 > 3 → underline B's 5 in (Cooperate, Defect)
- A=Defect: 1 > 0 → underline B's 1 in (Defect, Defect)

```
                    B: Cooperate    B: Defect
A: Cooperate           (3, 3)        (0, 5̲)
A: Defect              (5̲, 0)        (1̲, 1̲)
```

Only **(Defect, Defect)** has both payoffs underlined. Unique Nash equilibrium — and it is also the dominant strategy outcome. This confirms: when a dominant strategy equilibrium exists, it is the unique Nash equilibrium.

Notice: (Cooperate, Cooperate) with payoffs (3, 3) is *better for everyone* but is not a Nash equilibrium — because given the other plays Cooperate, you can defect and get 5. The equilibrium is collectively suboptimal. Day 17 explores this at length.

---

## The Formal Picture

**Nash equilibrium — the definition in plain terms:**

A strategy profile (one strategy per player) is a Nash equilibrium if, for every single player, no alternative strategy would give them a higher payoff *given what everyone else is doing*. Nobody can unilaterally switch and come out ahead.

**Equivalently:** every player is playing their *best response* to everyone else. A best response is simply: the choice that gives you the highest payoff given others' strategies.

A Nash equilibrium is a state of *mutual best responses* — each player is doing as well as they can, given what everyone else is doing.

**Nash's existence theorem (1950):**

Every finite game — any game with a limited number of players and a limited number of strategies — has at least one Nash equilibrium, possibly in mixed strategies (Day 18). Nash proved this using a fixed-point theorem: the intuition is that "best response to best responses" always has a self-consistent solution.

The theorem guarantees that at least one equilibrium *exists*. It does not guarantee uniqueness (Battle of the Sexes has two), nor does it tell you which equilibrium players will actually play.

---

### What Nash equilibrium predicts — and what it doesn't

**What it predicts:**
Nash equilibrium is the set of *stable* strategy profiles — outcomes that, once reached, no player has incentive to leave. It is a *necessary condition* for a stable, self-fulfilling prediction: if rational players correctly anticipate each other's strategies, the outcome must be a Nash equilibrium.

**What it doesn't predict:**
1. *Which* equilibrium is played when multiple exist. The model is silent on equilibrium selection — Day 24 (focal points) and Day 38 (correlated equilibrium) address this.
2. *How* players arrive at the equilibrium. Nash equilibrium is a static concept — it describes rest points, not dynamics or learning paths.
3. *Whether* equilibrium will be reached at all. In one-shot games with no prior interaction, there is no guarantee players coordinate on any equilibrium.
4. *Whether* the equilibrium is Pareto efficient. The Prisoner's Dilemma proves it often isn't.

**The most important caveat:** Nash equilibrium requires that each player's strategy is a best response to the *actual* strategies of others. In practice, players have beliefs about others' strategies that may be wrong. The equilibrium assumption — that beliefs are correct — is strong. Behavioural game theory (not covered in this course) studies what happens when beliefs are systematically miscalibrated.

---

### Nash equilibria in technology markets

**Platform pricing (two-sided markets):**
A ride-sharing platform sets prices for riders and drivers. A competing platform does the same. Each platform's optimal price depends on the other's price — if the competitor cuts driver fees, you may need to match to maintain supply. The equilibrium prices are Nash equilibria of this pricing game. The direction of competitive pressure (toward higher or lower prices) depends on the game's structure.

**Standard wars (VHS vs. Betamax, Blu-ray vs. HD DVD):**
When network effects are strong, the game has two Nash equilibria: "everyone uses format A" and "everyone uses format B." History selects one. Once selected, no single user has incentive to switch. The equilibrium is self-reinforcing — this is exactly the structure of Battle of the Sexes at scale.

**Ad auctions:**
In Google's generalised second-price auction (preview of Day 26), bidding your true value for each keyword is a Nash equilibrium of the game — but not necessarily a dominant strategy in the generalised multi-unit case. Advertisers who deviate from the equilibrium bidding strategy lose expected revenue.

**Multi-agent AI systems:**
When multiple RL agents share an environment (Day 49), their learned policies converge (if they converge at all) to Nash equilibria of the underlying game — not necessarily efficient ones. Understanding the game structure of the multi-agent environment is prerequisite to understanding what behaviour will emerge.

---

## Where It Breaks / What It Is Not

**Nash equilibrium is not the "best" outcome.** The Prisoner's Dilemma proves this definitively — the unique Nash equilibrium (Defect, Defect) is worse for everyone than the non-equilibrium outcome (Cooperate, Cooperate). "Equilibrium" means "stable given self-interest," not "optimal."

**Nash equilibrium is not always unique.** Battle of the Sexes has two. Some games have infinitely many. The model predicts that the outcome will be *some* Nash equilibrium, but not which one. This is a significant limitation for practical prediction.

**Rationality alone doesn't get you to equilibrium in a one-shot game.** For players to coordinate on a Nash equilibrium without prior communication, they need a shared way of selecting one. In the Battle of the Sexes, two perfectly rational players might both pick their preferred equilibrium and end up at (Ballet, Basketball) — which is not an equilibrium at all. Real equilibrium play requires common knowledge of *which* equilibrium to play, not just that an equilibrium exists.

**Most games have mixed strategy equilibria too** — we haven't covered those yet (Day 18). Every finite game has at least one Nash equilibrium, but it may be in mixed strategies rather than pure strategies. Rock-Paper-Scissors has no pure strategy Nash equilibrium.

---

## Try It Yourself

**Exercise 1 — Find all Nash equilibria (10 min)**

Find all pure strategy Nash equilibria using the underline method:

**Game A:**
```
                    B: Left     B: Centre    B: Right
A: Top              (4, 3)      (2, 2)       (3, 4)
A: Bottom           (3, 4)      (2, 2)       (4, 3)
```

**Game B:**
```
                    B: Left     B: Right
A: Top              (1, 1)      (3, 0)
A: Bottom           (0, 3)      (2, 2)
```

<details>
<summary>Worked answers</summary>

**Game A — underline best responses:**

A's best responses:
- B=Left: 4>3 → underline A=Top's 4
- B=Centre: 2=2 → underline both (tie)
- B=Right: 4>3 → underline A=Bottom's 4

B's best responses:
- A=Top: 4>2, 4>3, so B=Right with 4 → underline B=Right's 4 in (Top, Right)... wait, B=Left gives 3, Centre gives 2, Right gives 4. Underline B's 4 in (Top, Right).
- A=Bottom: B=Left gives 4, Centre gives 2, Right gives 3. Underline B's 4 in (Bottom, Left).

Cells with both underlined:
- (Top, Right): A gets 3̲, B gets 4̲ ✓
- (Bottom, Left): A gets 3̲, B gets 4̲ ✓

**Two Nash equilibria: (Top, Right) and (Bottom, Left).** Both have payoffs (3,4) — symmetric game with symmetric equilibria.

**Game B:**

A's best responses:
- B=Left: 1>0 → underline A=Top's 1
- B=Right: 3>2 → underline A=Top's 3

A's dominant strategy: Top (3>2 and 1>0). Underline both A=Top entries.

B's best responses:
- A=Top: 1>0 → underline B=Left's 1
- A=Bottom: 3>2 → underline B=Left's 3

Wait: A=Top, B=Left: (1,1). A=Top, B=Right: (3,0). B prefers Left (1>0) when A=Top.
A=Bottom, B=Left: (0,3). A=Bottom, B=Right: (2,2). B prefers Left (3>2) when A=Bottom.

B's dominant strategy: Left.

**Unique Nash equilibrium: (Top, Left)** with payoffs (1,1). Found by dominant strategies — consistent with Day 15.

</details>

---

**Exercise 2 — The product launch game (15 min)**

Two competing firms simultaneously decide whether to launch an AI assistant product this quarter (Q1) or next quarter (Q2). The payoff depends on who launches first: first-mover advantage is significant in AI products.

Construct a payoff matrix with reasonable payoffs for the following scenarios:
1. Both launch Q1 (race to market, split the early adopters, higher cost)
2. Both launch Q2 (both miss early mover advantage but save launch costs)
3. Firm A launches Q1, Firm B launches Q2 (A gets first mover advantage)
4. Firm B launches Q1, Firm A launches Q2 (B gets first mover)

Find all Nash equilibria. What does the equilibrium predict about real-world AI product launch timing?

---

**Exercise 3 — Verify from the exercise in Day 14 (5 min)**

Return to the payoff matrix you built in Day 14, Exercise 2 (the delivery apps exclusive/non-exclusive game). Apply the underline method to find the Nash equilibrium. Is it the same as the dominant strategy outcome?

---

## Connect It Back

Today's concept — Nash equilibrium — is the centre of gravity for everything in Modules 2 and 3. Every game you'll encounter from here gets solved by finding its Nash equilibria and asking whether they are unique, efficient, and stable.

Tomorrow (Day 17) you meet the most famous game in all of game theory: the **Prisoner's Dilemma**. It is a Nash equilibrium story — the unique Nash equilibrium is (Defect, Defect) — but it is also a story about why individually rational behaviour can be collectively catastrophic. The Prisoner's Dilemma is not just a game; it is a template that appears in climate agreements, price wars, open-source funding, and AI safety coordination.

**The question you should be able to answer now:**
*Why is a Nash equilibrium a necessary condition for a stable, self-fulfilling prediction — but not sufficient to guarantee that rational players will actually end up there?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Nash, John F. "Equilibrium Points in N-Person Games." *PNAS*, 36(1):48–49, 1950. Read it in full — it is two pages. The density of insight per page is extraordinary. Nash's original proof uses Kakutani's fixed-point theorem; you don't need to follow every step, but understanding the structure of the proof gives you deep respect for why existence was non-trivial to prove.

**If you want the deep version:**

1. Tadelis, *Game Theory*, Chapter 6 ("Rationalizable Strategies and Nash Equilibrium"). — Tadelis connects Nash equilibrium to rationalizability — a weaker concept based on iterated best responses without full equilibrium requirements. The connection clarifies exactly what common knowledge Nash equilibrium requires.

2. Dixit & Nalebuff, *The Art of Strategy*, Chapter 4 ("The Beautiful Mind"). — Dixit & Nalebuff's accessible account of Nash's insight, with the original film's scene dissected (which gets the game theory wrong). Good intuition builder.

3. Camerer, Colin F. *Behavioral Game Theory: Experiments in Strategic Interaction*. Princeton University Press, 2003. Chapter 1 ("Introduction"). — The empirical record of how real humans play games compared to Nash equilibrium predictions. Essential context for the gap between theory and practice.


---

← [Day 15 — Dominant Strategies](day-15-dominant-strategies) &nbsp;|&nbsp; [Day 17 — Prisoner's Dilemma →](day-17-prisoners-dilemma)
