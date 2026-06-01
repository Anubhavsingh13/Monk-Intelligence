*[Decision Science & Game Theory](../../../../README.md) · [Module 2 — Game Theory Fundamentals](../2-overview.md) · Day 15 of 65*

# Day 15 — Dominant Strategies

> **Today's one idea:** A strategy is dominant if it is your best move regardless of what anyone else does — and when one exists, you play it without needing to predict, model, or outguess your opponent.
> **Reading time:** ~30 min · **Prereqs:** [Day 14](day-14-what-is-a-game.md)
> **Primary source for today:** Tadelis, *Game Theory: An Introduction*, Chapter 4, Sections 4.1–4.2. Also: Dixit & Nalebuff, *The Art of Strategy*, Chapter 3.

---

## The Hook

You are playing a card game. You hold a hand of cards. Before you play, you notice something: one card in your hand beats every card your opponent could possibly hold. Not "usually beats" — beats regardless.

The decision is trivial. You don't need to figure out what your opponent is holding, what they will play, or what they expect you to do. The reasoning chain collapses to a single step: play the dominant card.

This is the cleanest situation in all of game theory. Not all games have it — most don't. But recognising when it exists cuts through the strategic complexity instantly. And recognising when it *doesn't* exist tells you exactly how hard the problem is.

---

## Building the Intuition

### The idea

Consider this game: Two competing AI startups (A and B) each decide whether to open-source their core model or keep it proprietary.

**Payoff matrix** (A's payoff, B's payoff — in units of market value):

```
                      B: Open-source    B: Proprietary
A: Open-source           (4, 4)            (6, 2)
A: Proprietary           (2, 6)            (5, 5)
```

Look at A's payoffs. If B open-sources:
- A open-sources → payoff 4
- A stays proprietary → payoff 2
→ A prefers open-source.

If B stays proprietary:
- A open-sources → payoff 6
- A stays proprietary → payoff 5
→ A prefers open-source.

Open-source is better for A **regardless of what B does**. Open-source is A's dominant strategy.

By symmetry (same payoff structure), open-source is also B's dominant strategy.

So the outcome is (Open-source, Open-source) with payoffs (4, 4) — even though (Proprietary, Proprietary) gives (5, 5) and both would be better off in that world. We'll see tomorrow that this is the Nash equilibrium. Day 17 will name this the Prisoner's Dilemma.

---

### Strict vs. weak dominance

**Strict dominance:** Strategy A strictly dominates strategy B if A gives you a *strictly higher* payout than B in *every possible scenario* — regardless of what anyone else does. No ties allowed. Strictly better everywhere.

**Weak dominance:** Strategy A weakly dominates strategy B if A gives you an *equal or higher* payout in every scenario, and a *strictly higher* payout in at least one scenario. Equal everywhere except at least one win.

**Why the distinction matters:** If a strategy is strictly dominant, eliminating it from consideration is unambiguously correct — no rational player would ever play a strictly dominated strategy. Weak dominance is trickier: the weakly dominated strategy ties in some cases, and subtle context can make it reasonable.

**The decision rule:** If you have a strictly dominant strategy, play it. If your opponent has a strictly dominated strategy, they will not play it — you can eliminate it from your analysis.

---

### Iterated elimination of dominated strategies (IEDS)

Even when no player has a dominant strategy directly, we can sometimes solve the game by repeatedly eliminating dominated strategies.

**The logic:** If strategy X is dominated for Player 2, Player 2 will never play X. If Player 1 knows this, they can eliminate column X from their analysis. Now some of Player 1's strategies might become dominated. Eliminate those. Repeat until no further eliminations are possible.

**Example — a 3×3 game:**

```
                    B: Left    B: Centre    B: Right
A: Top              (3, 1)     (2, 3)       (1, 2)
A: Middle           (1, 2)     (4, 1)       (2, 3)
A: Bottom           (2, 3)     (0, 2)       (3, 1)
```

**Step 1:** Does B have a dominated strategy?
- Left vs Centre: B prefers L if A plays Top (1>3? No, 1<3), prefers C if A plays Middle (2>1? No, wait): let me read B's payoffs — second number in each cell.
  - B's payoffs: Top row: (1, 3, 2), Middle row: (2, 1, 3), Bottom row: (3, 2, 1)
  - Left vs Right for B: Top → 1 vs 2 (Right better), Middle → 2 vs 3 (Right better), Bottom → 3 vs 1 (Left better). Neither dominates.
  - Left vs Centre: Top → 1 vs 3 (Centre better), Middle → 2 vs 1 (Left better), Bottom → 3 vs 2 (Left better). Neither dominates.
  - Centre vs Right: Top → 3 vs 2 (Centre better), Middle → 1 vs 3 (Right better), Bottom → 2 vs 1 (Centre better). Neither dominates.
- No dominated strategy for B in the full game.

**Step 2:** Does A have a dominated strategy?
- A's payoffs: Top row: (3,2,1), Middle row: (1,4,2), Bottom row: (2,0,3)
- Top vs Bottom: B=Left: 3 vs 2 (Top better), B=Centre: 2 vs 0 (Top better), B=Right: 1 vs 3 (Bottom better). No dominance.
- Middle vs Bottom: B=Left: 1 vs 2 (Bottom better), B=Centre: 4 vs 0 (Middle better), B=Right: 2 vs 3 (Bottom better). No dominance.
- Top vs Middle: B=Left: 3 vs 1 (Top), B=Centre: 2 vs 4 (Middle), B=Right: 1 vs 2 (Middle). No dominance.
- No dominated strategy for A either.

This game requires Nash equilibrium analysis (Day 16) — IEDS does not solve it.

---

### When IEDS does solve the game: a clean example

Consider a pricing game:

```
                    Rival: Low price    Rival: Medium    Rival: High
You: Low            (2, 2)             (4, 1)           (5, 0)
You: Medium         (1, 4)             (3, 3)           (4, 2)
You: High           (0, 5)             (2, 4)           (3, 3)
```

**Step 1:** Is "High price" ever dominated for You?
- High vs Low: Rival=Low: 0<2 (Low better), Rival=Medium: 2<3 (Medium better), Rival=High: 3=3 (tie). High is weakly dominated by Low (in the strict case: not always worse, so no strict dominance over Low). But is High strictly dominated by Medium? Rival=Low: 0<1 (yes), Rival=Medium: 2<3 (yes), Rival=High: 3=3 (tie, not strictly). Not strict.

(This example is getting messy — let me use a cleaner one for IEDS.)

**A cleaner IEDS example:**

```
                    B: Left    B: Right
A: Top              (3, 3)     (1, 4)
A: Bottom           (4, 1)     (2, 2)
```

**Step 1:** Check A. Is Top dominated?
- Top vs Bottom: B=Left: 3 vs 4 (Bottom better), B=Right: 1 vs 2 (Bottom better).
- **Bottom strictly dominates Top for A.** Eliminate Top.

After eliminating Top, the game reduces to:
```
                    B: Left    B: Right
A: Bottom           (4, 1)     (2, 2)
```

**Step 2:** Check B in the reduced game. Is Left dominated?
- Left vs Right for B: A=Bottom: 1 vs 2 (Right better).
- **Right strictly dominates Left for B.** Eliminate Left.

Solution: **(A=Bottom, B=Right)** with payoffs (2, 2). This is the unique dominant strategy equilibrium.

**The key theorem:** If IEDS produces a unique outcome, that outcome is the unique Nash equilibrium (Day 16). And it is the prediction you can be most confident in — because it relies only on rationality and common knowledge of rationality, not on coordination or equilibrium selection.

---

## The Formal Picture

**Strict dominance — in plain terms:**

Strategy A strictly dominates strategy B for player i if, for every combination of strategies the other players could choose, playing A gives player i a strictly higher payoff than playing B. A is *always better* than B — no exceptions.

**Dominant strategy equilibrium:**

A game has a dominant strategy equilibrium when every player has a strictly dominant strategy. Each player just plays their best-regardless-of-others strategy. No coordination needed, no opponent-modelling needed.

**Theorem (iterated elimination of dominated strategies):**

When you eliminate strictly dominated strategies round by round, the *order doesn't matter* — you always arrive at the same survivors. If only one strategy per player survives all the rounds, that combination is the unique Nash equilibrium — and the most robust prediction you can make.

**Why IEDS works:** The first elimination is justified by rationality (rational players don't play strictly dominated strategies). The second elimination is justified by the fact that rational players know others are rational (common knowledge of rationality, first order). The third by second-order common knowledge. And so on. IEDS requires exactly as many levels of common knowledge as there are rounds of elimination.

---

### Dominant strategies in AI and product contexts

**Vickrey auctions (Day 26 preview):** In a second-price sealed-bid auction (highest bidder wins, pays the second-highest price), bidding your true value is a dominant strategy. You do not need to guess what others bid. This property — strategy-proofness — is exactly the dominant strategy condition, applied to mechanism design.

**Dominant strategies in recommendation systems:** If a content algorithm always favours click-through rate over session satisfaction, for every possible user state, CTR optimisation is a "dominant strategy" for the algorithm. The mechanism designer's job (Module 3) is to design payoffs so that the socially desirable strategy is also dominant.

**Dominant strategies in negotiations:** If your BATNA (Best Alternative to a Negotiated Agreement) is strong enough, refusing to move below a certain price is dominant — regardless of how the other party threatens or cajoles. Day 25 formalises this.

---

## Where It Breaks / What It Is Not

**Most games don't have dominant strategies.** In Rock-Paper-Scissors, no strategy is dominant — each is beaten by one and beats another. In most pricing games, the optimal price depends on the competitor's price. IEDS is powerful when applicable, but it applies to a minority of real-world games.

**Iterated weak dominance can be order-dependent.** Unlike strict dominance elimination, weakly dominated strategy elimination can produce different outcomes depending on the order of elimination. Use weak dominance with caution; prefer strict dominance reasoning when possible.

**Common knowledge of rationality is a strong assumption.** IEDS relies on each player knowing others are rational, knowing others know this, etc. If you're playing against a boundedly rational agent (an RL model, a heuristic-driven algorithm, or a naive human), IEDS predictions may be misleading — the opponent may not eliminate dominated strategies from their own analysis.

---

## Try It Yourself

**Exercise 1 — Find dominant strategies (10 min)**

For each payoff matrix, find dominant strategies (if any) and solve by IEDS if possible:

**Game 1:**
```
                    B: Cooperate    B: Defect
A: Cooperate           (3, 3)        (0, 5)
A: Defect              (5, 0)        (1, 1)
```

**Game 2:**
```
                    B: Left    B: Right
A: Up               (4, 2)     (2, 4)
A: Down             (3, 3)     (3, 3)
```

<details>
<summary>Worked answers</summary>

**Game 1:**

A's payoffs: Cooperate → (3, 0); Defect → (5, 1).
- If B cooperates: A prefers Defect (5>3).
- If B defects: A prefers Defect (1>0).
**Defect strictly dominates for A.**

By symmetry: **Defect strictly dominates for B.**

IEDS solution: **(Defect, Defect)** with payoffs (1, 1). This is the classic Prisoner's Dilemma — both defect even though (Cooperate, Cooperate) gives (3,3). Day 17.

**Game 2:**

A's payoffs: Up → (4, 2); Down → (3, 3).
- If B plays Left: Up gives 4 > Down's 3.
- If B plays Right: Up gives 2 < Down's 3.
No dominant strategy for A.

B's payoffs: Left column: (2, 3); Right column: (4, 3).
- If A plays Up: B prefers Right (4>2).
- If A plays Down: B is indifferent (3=3).
Right weakly dominates Left for B (not strictly — ties in Down row).

This game has no strict dominant strategy elimination. Need Nash equilibrium (Day 16).

</details>

---

**Exercise 2 — Three-player elimination (10 min, L2)**

Three startups simultaneously choose whether to enter a new market or stay out. If all three enter, the market is too competitive — all lose $1M. If two enter, they split $3M and the outsider gets $0. If one enters alone, they get $5M. If none enter, all get $0.

a) Write the payoff function for Player 1 as a function of how many *others* enter (0, 1, or 2 others).
b) Is there a dominant strategy? Does "always enter" or "always stay out" dominate?

<details>
<summary>Worked answer</summary>

**a)** If Player 1 enters:
- 0 others enter: Player 1 gets $5M
- 1 other enters: Player 1 gets $3M/2 = $1.5M
- 2 others enter: Player 1 gets −$1M

If Player 1 stays out:
- Any number enter: Player 1 gets $0

**b)** "Enter" vs "Stay out":
- 0 others enter: Enter ($5M) > Stay out ($0) ✓
- 1 other enters: Enter ($1.5M) > Stay out ($0) ✓
- 2 others enter: Enter (−$1M) < Stay out ($0) ✗

"Enter" is not dominant — it is worse when both others enter.
"Stay out" is not dominant — it is worse when 0 or 1 others enter.

No dominant strategy. This game will require Nash equilibrium analysis. (Spoiler: there are Nash equilibria where exactly one or two players enter — see Day 16 for the concept and Day 18 for the mixed-strategy equilibrium.)

</details>

---

## Connect It Back

Today you learned the simplest and most powerful solution concept in game theory: dominance. When a dominant strategy exists, you don't need to model your opponent's mind — you just play your dominant strategy. When IEDS solves the game, you don't need to coordinate on a solution — it emerges from layered rationality reasoning.

Tomorrow (Day 16) you get the general solution concept that handles all the cases dominance can't: **Nash equilibrium** — the state where no player can improve their outcome by switching unilaterally. It is the centre of gravity for all of game theory, and everything in Modules 2–3 orbits it.

**The question you should be able to answer now:**
*Why is it irrational to play a strictly dominated strategy even once — even if you believe your opponent will play the strategy that makes the dominated strategy look good?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Tadelis, *Game Theory*, Chapter 4, Sections 4.1–4.2. Precise formal treatment of dominance and IEDS with clean examples. The transition from Section 4.1 (strict dominance) to 4.2 (iterated elimination) is the key intellectual move.

**If you want the deep version:**

1. Dixit & Nalebuff, *The Art of Strategy*, Chapter 3 ("Prisoners' Dilemmas and How to Resolve Them"). — Goes immediately from dominance into the Prisoner's Dilemma (tomorrow's topic) with vivid real-world examples. Read as a preview of Day 17.

2. Osborne, Martin J. *An Introduction to Game Theory*. Oxford University Press, 2004. Chapter 2 ("Nash Equilibrium: Theory") — free PDF at `economics.utoronto.ca/osborne/igt/`. — Osborne's treatment of dominance is unusually clear on the distinction between strict and weak cases, with good exercises.

3. Aumann, Robert J. "Rationality and Bounded Rationality." *Games and Economic Behavior*, 21(1-2):2–14, 1997. — Aumann (Nobel Prize 2005) reflects on what "rationality" actually requires for game-theoretic predictions to hold. Important context for why real-world agents deviate from dominant strategy play.


---

← [Day 14 — What Is a Game?](day-14-what-is-a-game) &nbsp;|&nbsp; [Day 16 — Nash Equilibrium →](day-16-nash-equilibrium)
