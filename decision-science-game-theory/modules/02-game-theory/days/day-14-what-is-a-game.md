*[Decision Science & Game Theory](../../../../README.md) · [Module 2 — Game Theory Fundamentals](../2-overview.md) · Day 14 of 65*

# Day 14 — What Is a Game?

> **Today's one idea:** A game is any situation where your best choice depends on what others choose — and they know that, and you know they know that — which means Module 1's toolkit, applied in isolation, will lead you astray.
> **Reading time:** ~35 min · **Prereqs:** [Day 1](../../01-foundations/days/day-01-how-decisions-actually-happen.md), [Day 2](../../01-foundations/days/day-02-expected-value.md)
> **Primary source for today:** Dixit & Nalebuff, *The Art of Strategy*, Chapter 1 ("Ten Tales of Strategy"). Also: Tadelis, *Game Theory: An Introduction*, Chapter 1.

---

## The Hook

Two coffee shops open on the same street. One decides to offer free Wi-Fi to attract remote workers. The other watches, then matches — free Wi-Fi too. Now both are offering a benefit that costs them money, while neither has a competitive advantage. They are in the same position as before, except with higher operating costs.

Neither shop *wanted* to offer free Wi-Fi. Each would have preferred a world where neither offered it. But once one did, the other had no choice. And the first shop, anticipating this, was probably right to do it — because if they hadn't, the other shop would have, and *they* would have been at the disadvantage.

This is not a decision problem. It is a **game** — and Module 1's decision tree has no place for it, because the "chance nodes" are not nature's draws but another rational agent's choices. A rational agent who is also thinking about you.

---

## Building the Intuition

### What makes something a game

Module 1 gave you the full toolkit for deciding when *you* are the only player. Three conditions move you from a decision problem into a game:

**Condition 1: Multiple players.** There are at least two agents, each with their own objective. You, and at least one other.

**Condition 2: Interdependence.** What is best for you depends on what others do. Your payoff is a function of everyone's actions, not just yours.

**Condition 3: Mutual awareness.** Each player knows the others are strategic — they are not just obstacles or random processes, but reasoners who are also trying to do well given what they expect you to do.

**Examples of games:** Pricing decisions when you have a direct competitor. Salary negotiation. A startup deciding when to launch relative to a competitor. A recommendation algorithm competing for user attention against another algorithm. A job candidate deciding how much to disclose in an interview. Two countries negotiating a treaty.

**Examples of non-games (decision problems):** Deciding which machine learning model to deploy (no strategic opponent). Planning a hiking route. Choosing which book to read next. Picking the fastest queue at the airport (unless you think the person behind you will pick the same queue).

The boundary is fuzzy. Most real-world decisions have *some* game-theoretic element once you zoom out enough — but the game-theoretic element is only decision-relevant when other players' strategic responses materially affect your optimal action.

---

### The three components of a game

Every game is fully specified by three things. Memorise this triple — every concept in Module 2 is a function of these three.

**1. Players:** *N* = {1, 2, ..., n}. Who is in the game? Are they individuals, firms, algorithms, or countries? 

*Note:* "Players" need not be human. In multi-agent AI systems, each RL agent is a player. In a platform economy, users and businesses are different player classes with different objectives.

**2. Strategies:** For each player *i*, a set *Sᵢ* of available actions. A strategy specifies *what a player will do in every possible situation they might face* — not just one action, but a complete contingent plan.

*Important distinction:* An **action** is a single choice. A **strategy** is a complete plan (e.g., "play Rock on the first move, then mirror whatever my opponent played last"). In simultaneous games, strategies are often just actions. In sequential games, strategies must specify what to do at every decision node.

**3. Payoffs:** For each combination of strategies, a payoff for each player. The payoff function *uᵢ(s₁, s₂, ..., sₙ)* maps the profile of strategies to player *i*'s outcome.

**The strategic form (normal form) of a game:**

For two players with two strategies each, the entire game is captured in a 2×2 matrix. Row player chooses the row; column player chooses the column; each cell contains (row player payoff, column player payoff):

```
                    Column player
                    Left (L)    Right (R)
Row player  Up (U)  (3, 2)      (1, 4)
            Down (D)(2, 1)      (4, 3)
```

Row player has strategies {U, D}. Column player has strategies {L, R}. If Row plays U and Column plays L, Row gets 3 and Column gets 2. This matrix contains *everything* needed to analyse the game.

---

### A taxonomy of games

Before diving into solution concepts (Days 15–18), you need to recognise the major game types. Each type requires different reasoning.

**Simultaneous vs. sequential:**
- *Simultaneous:* Players choose actions at the same time, without observing others' choices first. Rock-Paper-Scissors. Sealed bid auctions. Pricing by competitors who move once without coordination.
- *Sequential:* Players move in order, observing previous moves. Chess. Negotiation over multiple rounds. A startup that watches a competitor launch and then responds.

**Complete vs. incomplete information:**
- *Complete information:* All players know all players' payoff functions. No hidden preferences or types. (Uncommon in the real world, but the clean starting case.)
- *Incomplete information:* Some players have private information others don't — their costs, willingness to pay, capabilities. Job interviews, insurance, auctions. Module 3 is largely about this.

**Zero-sum vs. non-zero-sum:**
- *Zero-sum:* One player's gain is exactly another's loss — the total payout stays the same no matter what anyone does. Every dollar gained by one player is a dollar lost by another. Examples: poker (chips just move between players), fixed-size market share. Pure adversarial AI.
- *Non-zero-sum:* Players can jointly create or destroy value. The possible total payoff varies by strategy profile. Most real situations: trade (creates value), price wars (destroys value for both), cooperation on shared infrastructure.

**One-shot vs. repeated:**
- *One-shot:* The game is played once. No future interactions to consider.
- *Repeated:* The game is played multiple times between the same players. Reputation, punishment, and cooperation become possible. (Day 21.)

---

## The Formal Picture

**A game defined precisely:**

Every game in this module consists of:
- A set of **players** — everyone with their own objective (you, a competitor, a user, an RL agent)
- For each player, a set of **strategies** — their available choices
- A **payoff function** for each player — their outcome given the full combination of everyone's choices

The shorthand **s₋ᵢ** (read "s minus i") means "everyone's strategy *except* player i's." It lets you ask: "What's the best choice for player i, given what everyone else is doing?" This shorthand appears throughout Module 2 — it is just a compact way to say "all other players."

**Rationality assumption:** A player is *rational* if they choose the strategy that gives them the best outcome, given their beliefs about what others will do. This is the same maximisation as in Module 1 — the new ingredient is that beliefs about others must themselves be strategic.

**Common knowledge of rationality:** Classical game theory assumes all players are rational, all players *know* all players are rational, all players *know that all players know* this, and so on infinitely. This is a strong assumption — one we will test throughout Module 2.

---

### Why Module 1 fails in games

Consider the Wi-Fi example. A coffee shop using Module 1's expected value framework might calculate:

- If competitor offers Wi-Fi: offering Wi-Fi keeps some customers ($0 advantage), not offering loses customers (−$30K/year).
- If competitor doesn't offer Wi-Fi: offering Wi-Fi gains customers (+$20K/year), not offering is baseline ($0).

EV favours offering Wi-Fi if you put any weight on the competitor acting.

But the competitor is doing the *exact same calculation about you*. And you know that. And they know you know. The outcome is not one player optimising against nature's coin flip — it is two players in a simultaneous strategic interaction. Expected value, applied to each player independently, gives you each player's dominant strategy (Day 15) or Nash equilibrium (Day 16) — but the reasoning path is different. You are not computing probability-weighted payoffs over nature; you are reasoning about rational opponents.

---

## Where It Breaks / What It Is Not

**Misconception 1: "Game theory assumes people are purely selfish."**

No. Game theory assumes players maximise their own payoff function — but the payoff function can include *anything* the player cares about: money, fairness, others' wellbeing, reputation. A player whose payoff function penalises unequal outcomes will behave cooperatively in a game. Game theory does not impose preferences; it analyses the consequences of whatever preferences players have.

**Misconception 2: "Game theory applies only to zero-sum competition."**

Most interesting games are non-zero-sum. Supply chain negotiation, platform design, team dynamics, international agreements — all are non-zero games where cooperation can create value that competition cannot. The richest applications of Module 2 are in non-zero-sum territory.

**Misconception 3: "The Nash equilibrium is what will happen."**

Nash equilibrium is a *prediction* under specific assumptions (rationality, common knowledge, etc.). Real humans deviate from Nash equilibrium in systematic ways — especially in novel games, coordination problems, and situations with social norms. Day 16 will be precise about what Nash equilibrium does and does not claim.

**Misconception 4: "Games with more players are just bigger versions of two-player games."**

No. With n ≥ 3 players, coalitions, alliances, and externalities create qualitatively new phenomena. Module 3 (cooperative game theory, Shapley value) addresses this specifically.

---

## Try It Yourself

**Exercise 1 — Classify (5 min)**

For each situation, identify: (a) who are the players, (b) what are the strategies, (c) what type of game is it (simultaneous/sequential, zero-sum/non-zero-sum, one-shot/repeated)?

1. Two streaming services simultaneously setting their monthly subscription price.
2. A startup deciding when to launch relative to a large incumbent (the startup moves first, the incumbent observes and responds).
3. Two countries negotiating a trade agreement over multiple rounds.
4. An AI agent and a human operator deciding — simultaneously — whether to approve a potentially risky action.

---

**Exercise 2 — Build a payoff matrix (15 min)**

Two food delivery apps (A and B) are deciding whether to offer restaurant partners an exclusive contract (lock in the restaurant to only one platform) or non-exclusive (restaurant can list on both).

Think through the payoffs:
- If both offer exclusive contracts: restaurants must choose one platform, leading to a price war. Both apps gain some market share but at high cost. Estimate payoffs.
- If both offer non-exclusive: restaurants list everywhere, apps compete on user experience, more stable. Estimate payoffs.
- If A offers exclusive and B doesn't: A locks in the best restaurants, B loses quality supply. A gains significantly, B loses. Estimate payoffs. (Symmetric for the reverse case.)

Build the 2×2 payoff matrix. Does this look like any game structure you know? (Hint: think about what you'll learn on Day 17.)

---

**Exercise 3 — Identify the game in your work (10 min)**

Think of a recurring strategic decision in your work or career that involves at least one other rational agent with their own objectives.

Write out:
1. Who are the players?
2. What are the available strategies for each?
3. What does the payoff depend on (both players' choices)?
4. Is the game one-shot or repeated? Complete or incomplete information?

We will return to this exercise on Days 16–17 to find its equilibrium.

---

## Connect It Back

For the first 13 days, the world had one decision-maker and the rest was nature. Today that changed. There is now a second agent — rational, strategic, reasoning about you. The rest of Module 2 develops the solution concepts for this new world: how to find the right action when your best action depends on what a rational opponent does.

Tomorrow (Day 15) starts with the easiest case: **dominant strategies** — situations where one action is best for you regardless of what anyone else does. No need to predict the opponent. No circular reasoning. These are the games with uniquely clean solutions — and understanding them precisely will make the hard cases (Days 16–18) much clearer by contrast.

**The question you should be able to answer now:**
*Why does applying Module 1's expected value framework — treating the opponent as a random process — fail in a strategic game between rational agents?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Dixit & Nalebuff, *The Art of Strategy*, Chapter 1 ("Ten Tales of Strategy"). Ten real-world examples, each a different game structure. The chapter gives you pattern recognition before the formalism. Read it quickly — 30–40 minutes — and note which of the ten examples connects to your own domain.

**If you want the deep version:**

1. Tadelis, *Game Theory: An Introduction*, Chapter 1 ("The Single-Person Decision Problem") and Chapter 2 (transition to games). — Tadelis explicitly connects individual decision theory to game theory, making the transition precise. Chapter 1 reviews Module 1; Chapter 2 introduces the strategic form.

2. Schelling, Thomas C. *The Strategy of Conflict*. Harvard University Press, 1960. Chapter 1 ("An Essay on Bargaining"). — The book that won the Nobel Prize and introduced focal points, commitment, and the idea that game theory is about more than zero-sum competition. Chapter 1 is accessible and transformative.

3. For AI builders: Shoham, Yoav & Leyton-Brown, Kevin. *Multiagent Systems: Algorithmic, Game-Theoretic, and Logical Foundations*. Cambridge University Press, 2009. Free PDF at masfoundations.org. Chapter 3 ("Introduction to Noncooperative Game Theory"). — The AI-oriented introduction to game theory used in Stanford's multiagent systems course.


---

← [Day 13 — Deep Uncertainty](day-13-deep-uncertainty) &nbsp;|&nbsp; [Day 15 — Dominant Strategies →](day-15-dominant-strategies)
