*[Decision Science & Game Theory](../../../../README.md) · [Module 2 — Game Theory Fundamentals](../2-overview.md) · Day 18 of 65*

# Day 18 — Mixed Strategies: When Randomness Is Rational

> **Today's one idea:** When no pure strategy Nash equilibrium exists, the optimal strategy is a deliberate probability distribution over your actions — and the correct mixing probabilities are determined by making your opponent indifferent, not by flipping a coin.
> **Reading time:** ~35 min · **Prereqs:** [Day 16](day-16-nash-equilibrium.md), [Day 2](../../01-foundations/days/day-02-expected-value.md)
> **Primary source for today:** Tadelis, *Game Theory*, Chapter 7, Sections 7.1–7.3. Also: Dixit & Nalebuff, *The Art of Strategy*, Chapter 5 ("Choice and Chance").

---

## The Hook

A soccer player steps up for a penalty kick. The goalkeeper must dive left or right. If the kicker always aims left, the goalkeeper learns this and dives left every time. If the kicker mixes unpredictably, the goalkeeper cannot systematically exploit the pattern.

But "unpredictably" is not the same as "randomly." The kicker doesn't flip a coin. The optimal mix is determined by the specific payoff structure of the game. Kicking 70% left / 30% right might be the equilibrium — or 50/50, or 90/10 — depending on each player's relative skills in each direction.

This is not a failure of logic. It is logic. Randomisation in competitive settings is sometimes the *only* rational policy.

---

## Building the Intuition

### Why pure strategies fail in some games

Rock-Paper-Scissors:
```
           B: Rock    B: Paper    B: Scissors
A: Rock    (0, 0)     (−1, 1)    (1, −1)
A: Paper   (1, −1)    (0, 0)     (−1, 1)
A: Scissors (−1, 1)   (1, −1)    (0, 0)
```

Use the underline method to check for pure strategy Nash equilibria:

A's best response to B=Rock is Paper. Best response to B=Paper is Scissors. Best response to B=Scissors is Rock. No cell is underlined for both players simultaneously. **No pure strategy Nash equilibrium exists.**

But Nash's theorem guarantees at least one equilibrium exists in every finite game. It must be in **mixed strategies**.

---

### What a mixed strategy is

A **pure strategy** specifies one action deterministically.
A **mixed strategy** specifies a probability distribution over the set of pure strategies.

In Rock-Paper-Scissors: player A plays each option with probability 1/3. This is a mixed strategy. The claim is that (1/3, 1/3, 1/3) for both players is the Nash equilibrium.

**Why?** A mixed strategy Nash equilibrium requires that each player is *indifferent* between all strategies in their support (the strategies they play with positive probability). If A were not indifferent — if one pure strategy gave a higher expected payoff than another — A would switch to the dominant strategy and not mix.

With B playing (1/3, 1/3, 1/3):
- A's expected payoff from Rock = (1/3)(0) + (1/3)(−1) + (1/3)(1) = 0
- A's expected payoff from Paper = (1/3)(1) + (1/3)(0) + (1/3)(−1) = 0
- A's expected payoff from Scissors = (1/3)(−1) + (1/3)(1) + (1/3)(0) = 0

All three give expected payoff 0. A is indifferent — so any mixing is equally good. In particular, mixing 1/3 each is a valid best response. By symmetry, the same applies to B. **(1/3, 1/3, 1/3) for both** is the unique Nash equilibrium.

---

### Finding the mixing probabilities: the indifference condition

**The key insight:** In a mixed strategy Nash equilibrium, each player mixes in a way that makes *the other player* indifferent between their own strategies. You set your mix to neutralise your opponent's incentives, not to please yourself.

**The penalty kick game:**

Let's say: if the kicker aims at the kicker's strong side (Right), they score with probability 0.9 if the goalkeeper dives Right but 0.6 if the goalkeeper dives Left (correct direction helps). If the kicker aims Left (weaker side), they score 0.7 if goalkeeper dives Left but 0.4 if goalkeeper dives Right.

**Payoff matrix** (Kicker's payoff = P(score), Goalkeeper's payoff = 1 − P(score)):

```
                  Goalkeeper: Dive Left    Goalkeeper: Dive Right
Kicker: Kick Left     (0.7, 0.3)              (0.4, 0.6)
Kicker: Kick Right    (0.6, 0.4)              (0.9, 0.1)
```

**Step 1: Find Kicker's mixing probability that makes Goalkeeper indifferent.**

Let Kicker play Left with probability p and Right with probability (1−p).

Goalkeeper's expected payoff if Diving Left:
```
E[G | Dive Left] = p × 0.3 + (1−p) × 0.4
```

Goalkeeper's expected payoff if Diving Right:
```
E[G | Dive Right] = p × 0.6 + (1−p) × 0.1
```

Set them equal (Goalkeeper must be indifferent):
```
0.3p + 0.4(1−p) = 0.6p + 0.1(1−p)
0.3p + 0.4 − 0.4p = 0.6p + 0.1 − 0.1p
−0.1p + 0.4 = 0.5p + 0.1
0.3 = 0.6p
p = 0.5
```

Kicker kicks Left 50% of the time and Right 50%.

**Step 2: Find Goalkeeper's mixing probability that makes Kicker indifferent.**

Let Goalkeeper dive Left with probability q and Right with (1−q).

Kicker's expected payoff from Kick Left:
```
E[K | Kick Left] = 0.7q + 0.4(1−q) = 0.3q + 0.4
```

Kicker's expected payoff from Kick Right:
```
E[K | Kick Right] = 0.6q + 0.9(1−q) = −0.3q + 0.9
```

Set equal:
```
0.3q + 0.4 = −0.3q + 0.9
0.6q = 0.5
q = 5/6 ≈ 0.833
```

Goalkeeper dives Left 83% of the time and Right 17%.

**Mixed strategy Nash equilibrium:** Kicker plays (Left: 50%, Right: 50%), Goalkeeper plays (Dive Left: 83%, Dive Right: 17%).

**Reality check:** Research by Ignacio Palacios-Huerta (2003) analysed 1,417 actual penalty kicks in professional football. The empirical mixing proportions were not significantly different from the theoretical Nash equilibrium predictions. Professional players have, through experience, found the equilibrium without knowing game theory.

---

## The Formal Picture

**Mixed strategy — defined:**

A mixed strategy is a probability distribution over your available pure choices. Instead of always playing Rock, you play Rock 33%, Paper 33%, Scissors 33%.

The *support* of a mixed strategy is the set of choices you play with positive probability — the ones actually in your mix.

**Expected payoff under a mixed strategy:**

Your expected payoff is the probability-weighted average over all possible outcome combinations — calculated the same way as expected value (Day 2), but accounting for everyone's randomisation simultaneously.

**Nash equilibrium in mixed strategies:**

A mixed strategy Nash equilibrium has the same stability property as the pure-strategy version: no player can increase their expected payoff by switching to any strategy — pure or mixed — given what everyone else is doing.

**The indifference condition — the key tool:**

In a mixed strategy Nash equilibrium, every choice in your mix must give you the *same expected payoff*. If any choice were better than the others, you'd play it exclusively rather than mixing.

This has a counter-intuitive consequence:
> You set your mixing probabilities to make *your opponent* indifferent between their choices — not to maximise your own payoff from any single action.

**To find the equilibrium mix:** set up the equation "my opponent is indifferent between their choices given my mix." Solve for your mixing probabilities. Repeat for your opponent.

**Nash's existence theorem:**

Every finite game has at least one Nash equilibrium, possibly in mixed strategies. The proof uses a fixed-point theorem — the intuition is that "best responses to best responses" always has a self-consistent solution, even in complex games.

---

### Mixed strategies in AI and technology

**Adversarial ML:** An adversary trying to fool an image classifier should randomise over attack strategies. If the adversary always uses the same perturbation type, the defender can train against it. If the defender always uses the same defence, the adversary can find its blind spots. The Nash equilibrium of this cat-and-mouse game is a mixed strategy — the optimal attack and defence are randomised policies. This is why adversarial robustness in ML is genuinely a game theory problem.

**A/B testing allocation:** Suppose two competing features are both valuable but consume the same slot. Rather than committing 100% to one feature, a mixed strategy allocation (show Feature A 60% of the time, Feature B 40%) can be the equilibrium policy when both features have different optimal populations.

**Pricing under competition:** When two firms compete on price and the market is not fully transparent, mixed strategy pricing — randomising between high and low prices — can be an equilibrium. This explains the observed phenomenon of prices fluctuating randomly in competitive markets even when costs are stable.

**Security allocation:** A security team deciding which servers to audit, or which traffic to inspect, should randomise rather than follow a deterministic schedule. A predictable inspection schedule is exploitable; the Nash equilibrium of a security game is typically a mixed strategy. This is the foundation of security game theory used in airport security and wildlife patrol design.

---

## Where It Breaks / What It Is Not

**Mixed strategies require true randomisation, which is hard in practice.** People are notoriously bad at generating truly random sequences. The psychological literature shows that humans under-alternate — they produce too few long runs — when trying to randomise. This means "intentional randomisation" without a randomisation device (coin, dice, random number generator) will not achieve the equilibrium mixing proportions.

**Mixed strategy equilibria are fragile to small payoff changes.** The specific mixing proportions depend on the exact payoff values. A small change in the penalty-kick scoring probabilities changes the equilibrium mix. In real games where payoffs are estimated, not known exactly, the equilibrium mix is imprecisely specified.

**Mixed strategies are an equilibrium concept, not a prescriptive algorithm.** Finding the mixed strategy equilibrium tells you the stable outcome if players optimise. It does not tell you *how* to get there, or whether players will converge to it. In competitive AI systems, learning algorithms may or may not converge to Nash equilibria — this is an active research area (Day 49).

**The indifference condition is unintuitive.** The fact that you mix in a way that makes *your opponent* indifferent — not to maximise your own payoff — surprises most people. But it is logically necessary: if your mixing made you prefer one of your own strategies, you would play that strategy purely rather than mixing.

---

## Try It Yourself

**Exercise 1 — Find the mixed strategy equilibrium (10 min)**

**Matching Pennies:** Two players each choose Heads or Tails simultaneously. If they match, Player 1 wins $1 from Player 2. If they differ, Player 2 wins $1 from Player 1.

```
                    B: Heads     B: Tails
A: Heads            (1, −1)      (−1, 1)
A: Tails            (−1, 1)      (1, −1)
```

a) Show there is no pure strategy Nash equilibrium.
b) Find the mixed strategy Nash equilibrium.
c) What is each player's expected payoff at the equilibrium?

<details>
<summary>Worked answer</summary>

**a)** Best responses:
- B=Heads: A prefers Heads (1>-1)
- B=Tails: A prefers Tails (1>-1)
- A=Heads: B prefers Tails (1>-1)
- A=Tails: B prefers Heads (1>-1)

No cell has both payoffs underlined simultaneously. No pure NE.

**b)** Let A play Heads with probability p.

B's expected payoff from Heads: p×(−1) + (1−p)×1 = −p + 1 − p = 1 − 2p
B's expected payoff from Tails: p×1 + (1−p)×(−1) = p − 1 + p = 2p − 1

Set equal: 1 − 2p = 2p − 1 → 2 = 4p → p = 1/2.

By symmetry: q = 1/2 (B plays Heads with probability 1/2).

**Mixed NE:** Both play (Heads: 1/2, Tails: 1/2).

**c)** At equilibrium, A is indifferent: E[A] = 1/2×(1/2) + 1/2×(−1/2) = ... let's compute directly.
E[A] = (1/2)(1/2)(1) + (1/2)(1/2)(−1) + (1/2)(1/2)(−1) + (1/2)(1/2)(1) = 0.

Each player's expected payoff = **0**. This is a zero-sum game, so total payoff = 0 always.

</details>

---

**Exercise 2 — Compute mixing proportions (10 min)**

A cybersecurity team (Defender) decides each day whether to audit Server A or Server B. An attacker simultaneously decides which server to target. If they coincide (attacker targets the audited server), the attacker is caught (+10 to Defender, −10 to Attacker). If they don't coincide, the attack succeeds (−7 to Defender, +7 to Attacker).

```
                    Attacker: Target A    Attacker: Target B
Defender: Audit A      (10, −10)              (−7, 7)
Defender: Audit B      (−7, 7)                (10, −10)
```

Find the mixed strategy Nash equilibrium. What fraction of the time should the Defender audit each server?

<details>
<summary>Worked answer</summary>

Let Defender audit A with probability p.

Attacker's expected payoff from Target A: p×(−10) + (1−p)×7 = −10p + 7 − 7p = 7 − 17p
Attacker's expected payoff from Target B: p×7 + (1−p)×(−10) = 7p − 10 + 10p = 17p − 10

Set equal: 7 − 17p = 17p − 10 → 17 = 34p → p = 1/2.

Defender audits each server with probability **50%**.

By symmetry (payoff matrix is symmetric in this case): Attacker targets each server with probability **50%**.

Note: If the servers had different values (A is more critical than B), the equilibrium would be asymmetric — the Defender would audit the more valuable server more often. This is why security resource allocation should be based on the game's equilibrium, not just on asset value alone.

</details>

---

**Exercise 3 — Design a mixing device (5 min, L2)**

You are building an AI system that selects which of 3 content moderation strategies to apply to borderline content. A sophisticated adversarial poster will learn your policy if it is deterministic. You want to randomise in equilibrium.

a) Why is a uniform (1/3, 1/3, 1/3) distribution unlikely to be optimal?
b) What information would you need to compute the equilibrium mixing proportions?
c) What practical randomisation mechanism would you use in a production system?

---

## Connect It Back

Today completed the core solution concepts of simultaneous games: dominant strategies (Day 15), Nash equilibrium in pure strategies (Day 16), and Nash equilibrium in mixed strategies (today). Together, these cover every finite simultaneous game — Nash's theorem guarantees at least one equilibrium exists, and you now have the tools to find it.

Tomorrow (Day 19) you shift to **sequential games** — games where players move in order and observe prior moves before acting. Backward induction replaces best-response analysis, and commitment becomes strategically valuable in a new way. The chess match, the product launch race, and the multi-round negotiation are all sequential games.

**The question you should be able to answer now:**
*Why does a player set their mixing probabilities to make their opponent indifferent — rather than to maximise their own expected payoff from any single action?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Tadelis, *Game Theory*, Chapter 7, Sections 7.1–7.3. Tadelis's derivation of mixed strategy Nash equilibrium is unusually careful about the indifference condition and when it applies. The penalty kick example in Tadelis (if present in your edition) is worth working through carefully.

**If you want the deep version:**

1. Palacios-Huerta, Ignacio. "Professionals Play Minimax." *Review of Economic Studies*, 70(2):395–415, 2003. — The empirical test of mixed strategy equilibrium in professional penalty kicks. Professional players' empirical mixing proportions match the theoretical equilibrium. A landmark paper for behavioural game theory. Read Sections 1–3.

2. Chiappori, Pierre-André, Levitt, Steven & Groseclose, Timothy. "Testing Mixed-Strategy Equilibria When Players Are Heterogeneous: The Case of Penalty Kicks in Soccer." *American Economic Review*, 92(4):1138–1151, 2002. — Alternative analysis of the same phenomenon. Interesting disagreement with Palacios-Huerta on methodology.

3. Dixit & Nalebuff, *The Art of Strategy*, Chapter 5 ("Choice and Chance"). — Accessible treatment of mixed strategies with real-world examples beyond sports: pricing, inspection, and bluffing in negotiations.


---

← [Day 17 — Prisoner's Dilemma](day-17-prisoners-dilemma) &nbsp;|&nbsp; [Day 19 — Sequential Games →](day-19-sequential-games)
