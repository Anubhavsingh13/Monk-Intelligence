*[Decision Science & Game Theory](../../../../README.md) · [Module 2 — Game Theory Fundamentals](../2-overview.md) · Day 19 of 65*

# Day 19 — Sequential Games & Backward Induction

> **Today's one idea:** In sequential games, you solve from the last move backward — each player anticipates what rational players will do at every future decision point — and this produces a uniquely sharp prediction called the subgame perfect equilibrium.
> **Reading time:** ~35 min · **Prereqs:** [Day 16](day-16-nash-equilibrium.md), [Day 10](../../01-foundations/days/day-10-decision-trees.md)
> **Primary source for today:** Tadelis, *Game Theory*, Chapter 9, Sections 9.1–9.3. Also: Dixit & Nalebuff, *The Art of Strategy*, Chapter 2 ("Anticipating Your Rival's Response").

---

## The Hook

In 1981, the US government decided to break up AT&T's telephone monopoly. AT&T knew the antitrust case was coming for years. What did AT&T do?

They massively expanded into adjacent businesses — cable, computers, satellite. The strategy looked aggressive and perhaps irrational: they were investing in areas where they had no particular advantage, right before a forced break-up.

But the logic was sequential. AT&T reasoned forward: *if we don't build these positions, after the break-up we'll have nothing outside our core business. If we do build them, even poorly, we have chips to negotiate with and assets that survive.* They were playing a long sequential game and backward-inducing from the end.

Sequential reasoning — thinking ahead and working backward — is the core skill of Day 19. It is what distinguishes good strategic thinking from good decision-making under uncertainty.

---

## Building the Intuition

### The game tree (extensive form)

Simultaneous games are represented as matrices. Sequential games need a different representation: the **game tree** (extensive form).

A game tree has:
- **Decision nodes:** Points where a player makes a choice.
- **Branches:** The available actions at each decision node.
- **Terminal nodes:** End states with payoffs for all players.
- **Information sets:** What the acting player knows at each decision node (we'll use perfect information today — each player sees all prior moves).

**A simple example — Entry Deterrence:**

An Incumbent holds a monopoly. An Entrant considers entering the market. If the Entrant enters, the Incumbent can Fight (price war, costly for both) or Accommodate (share the market).

```
                    [Fight]      ───── (Entrant: −1, Incumbent: −1)
                   /
[Entrant: Enter]──●
         |         \
         |          [Accommodate] ── (Entrant: 1, Incumbent: 1)
         |
[Entrant: Stay Out] ─────────────── (Entrant: 0, Incumbent: 3)
```

(Payoffs: Fight is costly for both — war destroys value. Accommodate splits the market. Stay Out leaves Incumbent as monopolist.)

**Who moves first?** The Entrant. The Incumbent observes the entry decision and then responds.

---

### Backward induction: solving from the end

The solution method for perfect information sequential games:

1. Start at the final decision nodes (the nodes closest to terminal nodes).
2. At each, the acting player chooses the branch maximising their payoff.
3. Record the optimal action and the resulting payoff.
4. Fold back: replace that decision node with the payoff it produces.
5. Move one step earlier in the tree. Repeat until the root.

**Solving Entry Deterrence:**

**Step 1 — Incumbent's decision (if entry occurred):**
- Fight: Incumbent gets −1.
- Accommodate: Incumbent gets 1.
→ Incumbent chooses **Accommodate**. Fold back: if Entrant enters, outcome = (1, 1).

**Step 2 — Entrant's decision:**
Now the tree looks like:
```
[Enter]  ──── (Entrant: 1, Incumbent: 1)   [after backward induction of Incumbent's move]
[Stay Out] ── (Entrant: 0, Incumbent: 3)
```
- Enter → Entrant gets 1.
- Stay Out → Entrant gets 0.
→ Entrant chooses **Enter**.

**Prediction:** Entrant enters, Incumbent accommodates. Payoffs: (1, 1).

---

### Why backward induction is powerful: it eliminates non-credible threats

The Incumbent might *threaten* to Fight if Entry occurs: "Enter, and I'll destroy you with a price war." This sounds menacing.

But backward induction exposes the threat as non-credible. *At the moment of decision*, if Entry has occurred, fighting yields −1 while accommodating yields 1. A rational Incumbent will not carry out a threat that hurts them. The Entrant, reasoning backward, knows this — and enters anyway.

This is one of the most important insights in game theory: **a strategy that involves carrying out a threat (or promise) that would be irrational to execute when the time comes is not credible, and rational opponents will ignore it.**

Day 20 (Commitment) is entirely about how to make threats credible by changing the game's structure before the threat becomes relevant.

---

### Subgame Perfect Equilibrium (SPE)

Backward induction produces strategies that are not just Nash equilibria — they are Nash equilibria in *every subgame* of the sequential game. This stronger property is called **subgame perfection**.

A **subgame** is any part of the game tree that could be a standalone game — a subtree rooted at a single decision node where all information is complete.

**Definition (Subgame Perfect Equilibrium):**

A strategy profile is a Subgame Perfect Equilibrium if it specifies a Nash equilibrium in every subgame — including subgames that are never reached in equilibrium play.

SPE eliminates Nash equilibria sustained by non-credible threats. In Entry Deterrence, there is a Nash equilibrium where: Entrant stays out, Incumbent threatens to fight (and would fight if entry occurred). This is a Nash equilibrium — the Entrant's best response to "Incumbent fights if I enter" is to stay out, and the Incumbent's strategy of fighting is never tested. But it is *not* an SPE, because "fight if entered" is not optimal in the subgame beginning after entry.

**The SPE is the prediction that takes non-credible threats off the table.**

---

## The Formal Picture

**A sequential game — the key elements:**

A complete-information sequential game has:
- A **game tree**: nodes where players choose, branches for each available action, and terminal nodes with final payoffs
- At each decision node: **who is acting** (which player moves here)
- At each node: **what choices are available**
- At terminal nodes: **payoffs for all players**

**Backward induction algorithm:**

```
function backward_induction(node):
  if node is terminal:
    return the payoffs at this node
  
  the acting player looks at all available choices:
    for each choice, apply backward_induction down that branch to find its value
  
  the acting player picks the choice that gives them the highest payoff
  record this as their optimal action at this node
  return the payoff from the optimal choice
```

The recorded optimal actions at every node form the subgame perfect equilibrium strategy.

**Zermelo's theorem (1913):** In any finite two-player zero-sum game of perfect information, one player has a winning strategy, or both can guarantee a draw. (Chess, checkers, tic-tac-toe — one of these three outcomes is determined by optimal play.) Proved by backward induction. Zermelo's theorem preceded Nash by 37 years.

**SPE and backward induction:**

In games of perfect information, the backward induction outcome is the unique SPE. In games of imperfect information (where players don't observe prior moves), additional tools are needed — but the backward induction intuition carries over.

---

### Sequential games in AI and technology

**Product launch timing (sequential):**

Firm A launches first, then Firm B decides whether to enter and at what scale. This is a sequential game. Firm A's launch strategy must anticipate Firm B's best response. Backward-inducing: What will B do if A launches a premium product? A budget product? A developer tool?

The SPE strategy for A might be to launch at a *deliberately low price* — not because A prefers low prices, but to make entry less attractive for B (reducing the market size B would enter into). This is called *entry deterrence pricing* — it is directly derived from sequential game reasoning.

**AI model release strategy:**

When should a lab release a model? The sequential game involves competitors, regulators, and users. If a lab anticipates that a competitor's next model will be released in 3 months, the optimal release time is not simply "when our model is ready" — it depends on the sequential game with the competitor. Releasing early with a good model might deter the competitor's entry into a sub-market; releasing late might mean being second.

**Multi-step RL (preview of Day 44):**

Reinforcement learning is the computational version of backward induction at scale. An RL agent in a sequential environment (MDP) must reason about the consequences of its actions through time. The Bellman equation — V(s) = max_a [R(s,a) + γ × E[V(s')]] — is backward induction in recursive form. Day 44 will make this connection precise.

**Negotiation (preview of Day 25):**

Every negotiation is a sequential game. The proposer makes an offer; the responder accepts or rejects (and possibly makes a counter-offer). Backward induction in an infinite-horizon negotiation game gives Rubinstein's (1982) alternating-offers solution — a unique SPE that predicts the exact deal struck as a function of each party's impatience.

---

## Where It Breaks / What It Is Not

**Backward induction requires perfect information.** If players do not observe prior moves, backward induction does not apply directly. Imperfect information requires beliefs and Bayes' rule to update them — covered in Days 27–29.

**Backward induction can produce counterintuitive results in long games.** The "Centipede Game" (Rosenthal, 1981): two players take turns, each choosing to "Pass" (growing the pot) or "Take" (ending the game and claiming the pot). Backward induction says Player 1 should Take immediately on the first move. But in experiments, players cooperate for many rounds before defecting. The backward induction prediction is technically correct given rationality, but deeply inconsistent with observed human behaviour.

**Backward induction requires common knowledge of rationality.** If you suspect your opponent is not fully rational — they will carry out a non-credible threat out of pride or anger — backward induction's prediction changes. Real-world negotiators often act irrationally for strategic reputation-building. Day 20 (Commitment) directly exploits this.

**Subgames must be identifiable.** In games with imperfect information (information sets containing multiple nodes), it may not be possible to define subgames at every node. SPE's requirement that every subgame have an equilibrium cannot be applied directly. More refined equilibrium concepts (sequential equilibrium, perfect Bayesian equilibrium) are needed — these appear in Days 27–29.

---

## Try It Yourself

**Exercise 1 — Solve by backward induction (10 min)**

The Ultimatum Game: Player 1 proposes a split of $10 (offers Player 2 some amount x, keeping $10−x). Player 2 accepts or rejects. If rejected, both get $0.

Payoff tree (simplified to two proposals — offer $3 or offer $7):

```
                              [Accept] → (P1: 7, P2: 3)
                 [Offer $3] - ●
                /              [Reject] → (P1: 0, P2: 0)
P1 ────●
                \              [Accept] → (P1: 3, P2: 7)
                 [Offer $7] - ●
                              [Reject] → (P1: 0, P2: 0)
```

a) Solve by backward induction. What does P2 do at each decision node? What does P1 offer?
b) What does backward induction predict that actual humans often reject in experiments? Why?
c) What does the divergence between theory and experiment tell you about the limits of the model?

<details>
<summary>Worked answer</summary>

**a)** 

P2's decisions:
- If offered $3: Accept ($3) > Reject ($0). P2 accepts.
- If offered $7: Accept ($7) > Reject ($0). P2 accepts both.

P1's decision:
- Offer $3 → P2 accepts → P1 gets $7.
- Offer $7 → P2 accepts → P1 gets $3.
→ P1 offers **$3** (keeping $7 for themselves).

SPE prediction: P1 offers $3, P2 accepts. Payoffs: (7, 3).

**b)** In experiments, offers below $2–3 (20–30%) are frequently rejected. Humans reject unfair offers even at a cost to themselves — a violation of pure payoff maximisation.

**c)** The model assumes payoffs are purely monetary and rationality is common knowledge. Humans have social preferences: they care about fairness, relative outcomes, and the act of being treated unfairly. The "payoff" includes more than money. The backward induction prediction is correct given the stated payoffs; the stated payoffs don't capture what actually matters to human players. This is not a failure of game theory — it is a reminder that the game-theoretic model is only as good as the payoff specification.

</details>

---

**Exercise 2 — The entry deterrence game (10 min)**

A new AI startup (Entrant) is considering entering a market dominated by an Incumbent with deep pockets. The Incumbent can either fight (sustained price war, aggressive marketing) or accommodate (let the Entrant take some share). Assign your own payoffs to the four outcomes (Entrant enters + Fight; Entrant enters + Accommodate; Entrant stays out; choose carefully so the non-credible threat structure holds).

a) Draw the game tree.
b) Solve by backward induction.
c) Change the payoffs so that the Incumbent's threat to fight becomes credible (subgame perfect). What structural change would create this situation?

---

**Exercise 3 — Backward induction in your work (10 min)**

Identify a recent decision in your work that had a sequential structure — your action was observed by another party who then responded. 

Reconstruct the decision as a game tree (even if approximate). Apply backward induction: what should each party have done at each node? Did actual behaviour match? If not, what explains the gap (non-credible threats, bounded rationality, incomplete information)?

---

## Connect It Back

Sequential games add time and order to strategic reasoning. The key tools — backward induction and subgame perfection — give you uniquely sharp predictions by eliminating the non-credible threats that inflate Nash equilibria in simultaneous games.

Tomorrow (Day 20) directly addresses the question that backward induction raises: if threats must be credible to matter, *how do you make a threat credible?* The answer is commitment — binding yourself to a course of action before the game unfays. This is one of game theory's most powerful and counterintuitive insights: voluntarily giving up options can make you stronger.

**The question you should be able to answer now:**
*Why does backward induction eliminate Nash equilibria sustained by non-credible threats — and why does this make SPE a more reliable prediction than Nash equilibrium alone?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Tadelis, *Game Theory*, Chapter 9, Sections 9.1–9.2 ("Backward Induction" and "Subgame Perfect Equilibrium"). The formal definition of SPE and the entry deterrence example are handled with unusual clarity. Read through the formal proof that backward induction produces the unique SPE in perfect information games.

**If you want the deep version:**

1. Dixit & Nalebuff, *The Art of Strategy*, Chapter 2 ("Anticipating Your Rival's Response"). — Real-world sequential game examples including the AT&T break-up strategy, product launch timing, and baseball salary arbitration. Accessible and highly relevant to AI product strategy.

2. Rosenthal, Robert W. "Games of Perfect Information, Predatory Pricing, and the Chain Store Paradox." *Journal of Economic Theory*, 25(1):92–100, 1981. — The Centipede Game and Chain Store Paradox: cases where backward induction's prediction is theoretically correct but empirically and intuitively wrong. Essential reading for understanding the limits of the model.

3. Rubinstein, Ariel. "Perfect Equilibrium in a Bargaining Model." *Econometrica*, 50(1):97–109, 1982. — The foundational paper on alternating-offers bargaining as a sequential game with a unique SPE. Preview of Day 25. Read Sections 1–3 to see how backward induction produces a unique bargaining outcome.


---

← [Day 18 — Mixed Strategies](day-18-mixed-strategies) &nbsp;|&nbsp; [Day 20 — Commitment & Credible Threats →](day-20-commitment-credible-threats)
