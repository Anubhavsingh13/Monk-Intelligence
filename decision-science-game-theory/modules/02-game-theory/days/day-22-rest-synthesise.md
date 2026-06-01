*[Decision Science & Game Theory](../../../../README.md) · [Module 2 — Game Theory Fundamentals](../2-overview.md) · Day 22 of 65*

# Day 22 — Rest & Synthesise: Module 2 Mid-Point

> **Today's one idea:** No new material — only consolidation. A framework you can reconstruct from memory is one you actually own.
> **Reading time:** ~35 min · **Prereqs:** Days 14–21
> **Primary source for today:** Your notes from the past eight days.

---

## How to Use This Day

No new concepts. No new definitions. Today is structured in three parts:

1. **Reconstruct** — without looking at previous pages, rebuild the Module 2 framework from scratch.
2. **Apply** — take a single real situation and run it through the full game theory toolkit.
3. **Self-test** — 12 sharp questions with collapsible answers; find your gaps now, before Module 3.

Do not skip this day. The concepts in Days 14–21 are load-bearing for everything in Modules 3, 4, and 5. An hour spent consolidating here saves three hours of re-reading later.

---

## Part 1 — Reconstruct the Framework (15 min, no notes)

Without referring to any previous pages, answer the following questions in writing (a notebook, a blank document, anything):

**The vocabulary:**

1. What three elements define a game? Give one example of each for a market you know.
2. What is the difference between a dominant strategy and a Nash equilibrium? Can a Nash equilibrium exist without dominant strategies?
3. Why does backward induction eliminate non-credible threats — but Nash equilibrium alone does not?
4. What makes a mixed strategy an equilibrium? Why do you set your mixing probabilities to make *your opponent* indifferent?
5. What is the Prisoner's Dilemma's payoff ordering? What is the unique Nash equilibrium, and why is it Pareto suboptimal?

**The mechanisms:**

6. Name three commitment mechanisms from Day 20. For each, give a real example you *haven't* seen in the course.
7. State the cooperation condition for infinitely repeated games. What happens to it when the punishment payoff P rises toward R?
8. What is the Folk Theorem? What does it predict — and what does it *fail* to predict?

**The connections:**

9. How does backward induction (Day 19) relate to the Bellman equation in reinforcement learning? (Don't worry if this is hazy — it will be formalised on Day 44.)
10. In what sense is the repeated game's shadow of the future a "natural commitment device" — and how does it differ from Day 20's explicit commitment mechanisms?

After writing, open the pages and compare. Mark any question you couldn't answer confidently. Those are your gaps to close this week.

---

## Part 2 — Apply the Full Toolkit (15 min)

Choose a real strategic situation from your own work or life — one that involves at least two parties with potentially different interests. It can be small (negotiating a project timeline, deciding whether to share code openly, pricing a freelance project).

Work through it systematically:

**Step 1 — Map the game:**
- Who are the players? (Be specific — "my company" is not a player; the product manager making the call is.)
- What strategies does each player have? (List them explicitly — resist vague answers.)
- What are the approximate payoffs? (You don't need numbers — sketch a qualitative ordering.)
- Is this simultaneous or sequential? One-shot or repeated? Complete or incomplete information?

**Step 2 — Solve it:**
- Are there dominant strategies? Eliminate dominated ones.
- What are the Nash equilibria? (Use the underline method if the game is 2×2.)
- If sequential: what does backward induction predict?
- Is there a Prisoner's Dilemma structure? If so, which of the three escape mechanisms (commitment, repetition, incentive redesign) is available?

**Step 3 — Identify the levers:**
- Is there a commitment device you could deploy (Day 20)?
- Is this a repeated game where reputation matters (Day 21)?
- Is there information asymmetry that changes the game? (Preview of Day 27.)

**Step 4 — Predict and act:**
- What does the theory predict will happen?
- What would you actually do — and where does it diverge from theory?
- What does the divergence tell you about the limits of the model for this situation?

Write this up, even briefly. The act of writing forces precision. Fuzzy strategy diagrams are not strategy.

---

## Part 3 — Self-Test: 12 Sharp Questions (10 min)

Answer each from memory, then check.

---

**Q1.** A game has players, strategies, and payoffs. Which of these three elements determines whether a game is zero-sum?

<details>
<summary>Answer</summary>

The **payoffs**. A game is zero-sum if the sum of all players' payoffs is constant across all strategy profiles — what one player gains, another loses. Players and strategies are irrelevant to this property.

</details>

---

**Q2.** Player A has a dominant strategy. Player B does not. Does IEDS (iterated elimination of dominated strategies) still help?

<details>
<summary>Answer</summary>

Yes. Eliminating A's dominated strategies may make previously non-dominated strategies of B become dominated (because B can now assume A won't play certain strategies). IEDS is applied iteratively — each round of elimination potentially enables the next. You don't need *both* players to have dominant strategies for IEDS to make progress.

</details>

---

**Q3.** The underline method finds Nash equilibria. But a Nash equilibrium need not be the *unique* prediction. Name two reasons a game might have multiple Nash equilibria.

<details>
<summary>Answer</summary>

(1) **Coordination games** like Battle of the Sexes have multiple pure strategy Nash equilibria — there is no single Schelling point to coordinate on without communication. (2) **Mixed strategy equilibria** — every finite game also has equilibria in mixed strategies, so even games with a unique pure-strategy NE have additional mixed-strategy NE.

</details>

---

**Q4.** You are told: "This is a Nash equilibrium, but it is not subgame perfect." What does that imply about the strategies involved?

<details>
<summary>Answer</summary>

It implies that the Nash equilibrium relies on a **non-credible threat** (or promise). Somewhere in the game tree, there is a subgame where one player's specified strategy is not a best response in that subgame — the player would prefer a different action if that subgame were actually reached. Because backward induction eliminates such threats, the SPE refines away this Nash equilibrium.

</details>

---

**Q5.** What is the key insight behind the mixed strategy Nash equilibrium? (The "paradoxical" part.)

<details>
<summary>Answer</summary>

You set your mixing probabilities to **make your opponent indifferent** between their strategies — not to maximise your own payoff. This is counterintuitive because you're "playing for the other side." But it's logically required: if your mixing made your opponent prefer one of their strategies, they would play it purely, and you'd exploit that pattern — defeating the purpose of mixing.

</details>

---

**Q6.** Write down the Prisoner's Dilemma payoff condition in two inequalities and explain in one sentence why T > R > P > S creates a dominant strategy.

<details>
<summary>Answer</summary>

**Condition:** T > R > P > S (and 2R > T + S for the dilemma to be social).

**Why dominant strategy:** Whether your partner cooperates or defects, defecting gives you a higher payoff: T > R (if partner cooperates) and P > S (if partner defects). So defect strictly dominates cooperate regardless of what the partner does.

</details>

---

**Q7.** A friend says: "We can escape the Prisoner's Dilemma if both players just reason more carefully and realise that mutual cooperation is better." Why is this wrong?

<details>
<summary>Answer</summary>

The Prisoner's Dilemma is not a *reasoning failure* — defection is the individually rational strategy given the payoff structure. Making the players smarter only confirms this. The dilemma is a *structural* feature of the payoff matrix. The solution requires changing the game (through repetition, commitment, or incentive redesign) — not improving the quality of reasoning.

</details>

---

**Q8.** What is the cooperation threshold δ* in an infinitely repeated Prisoner's Dilemma? What happens to δ* when T increases?

<details>
<summary>Answer</summary>

**δ* = (T − R) / (T − P)**

When T increases (temptation grows), the numerator (T − R) grows faster than the denominator (T − P) — since R and P are fixed. So δ* increases toward 1. A higher temptation makes cooperation harder to sustain: players need to care more about the future to resist the larger one-shot gain from defecting.

</details>

---

**Q9.** Why does backward induction unravel cooperation in finitely repeated games — but not in infinitely repeated games?

<details>
<summary>Answer</summary>

In finite games, the **last round** is a one-shot game with a dominant strategy (defect). Once players anticipate that the last round will be defection, the second-to-last round has no future to protect — and also becomes defection. This unravels all the way to round 1. In infinite games, there is no last round — the future threat of punishment remains credible at every stage, sustaining cooperation as an equilibrium.

</details>

---

**Q10.** Name three commitment mechanisms. For each, identify whether it works by (a) eliminating your own options, (b) making deviation costly, or (c) transferring decision authority.

<details>
<summary>Answer</summary>

| Mechanism | Category |
|-----------|----------|
| Burning ships / irreversible investment | (a) Eliminating options |
| Performance bond / prepayment / public announcement | (b) Making deviation costly |
| Delegation to algorithm / treaty / absent principal | (c) Transferring authority |

Reputation effects operate through (b) — the reputational cost of deviation raises the price of defecting from a commitment.

</details>

---

**Q11.** The Folk Theorem says "almost anything can be sustained as an equilibrium." Is this a useful result? What does it tell us — and what problem does it create?

<details>
<summary>Answer</summary>

**It is useful** because it shows cooperation is theoretically possible without external enforcement, given a long enough shadow of the future. It proves the promise of repetition. **It creates a problem** because it is too permissive — it cannot predict *which* of the many possible equilibria players will land on. The Folk Theorem proves existence but provides no equilibrium selection theory. Axelrod's tournament fills this gap empirically; formal equilibrium selection is still an open problem.

</details>

---

**Q12.** What are Tit-for-Tat's four properties, and which one breaks down in noisy environments?

<details>
<summary>Answer</summary>

**Nice** (never defects first), **provocable** (retaliates immediately after defection), **forgiving** (returns to cooperation as soon as partner does), **clear** (transparent strategy that partners can learn).

The property that breaks down in noisy environments is **provocability combined with immediacy**: if a player accidentally defects (noise), a Tit-for-Tat partner retaliates, triggering mutual retaliation spirals. Generous Tit-for-Tat (forgive with some probability even after defection) fixes this — sacrificing perfect provocability for robustness to noise.

</details>

---

## Closing Reflection

Before moving to Day 23, answer this privately:

*What is the single most surprising thing you learned in Module 2 so far — not the most important, but the most surprising?*

Surprises mark the edge of your prior knowledge. They are the places where new understanding has actually formed. Write it down. It will be useful when the capstone asks you to synthesise across the entire course.

---

## What's Next

Day 23 introduces **evolutionary game theory** — the same game-theoretic structure applied to populations of agents who don't reason at all. Strategies reproduce in proportion to payoff performance. The surprising result: Nash equilibria emerge from blind selection, not rationality, and cooperation can evolve without Tit-for-Tat players ever consciously calculating the cooperation threshold.

This connects Module 2's game theory to Module 4's reinforcement learning in a deep way: learning and evolution are both processes by which strategies improve without explicit game-theoretic reasoning — and both converge (in specific conditions) to equilibria of the underlying game.


---

← [Day 21 — Repeated Games & Cooperation](day-21-repeated-games) &nbsp;|&nbsp; [Day 23 — Evolutionary Game Theory →](day-23-evolutionary-game-theory)
