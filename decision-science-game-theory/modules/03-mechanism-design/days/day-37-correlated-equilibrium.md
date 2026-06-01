*[Decision Science & Game Theory](../../../../README.md) · [Module 3 — Mechanism Design & Advanced Structures](../3-overview.md) · Day 37 of 65*

# Day 37 — Correlated Equilibrium

> **Today's one idea:** A mediator who sends players private, coordinated signals can sustain outcomes that Nash equilibrium cannot reach — and correlated equilibrium, which formalises this, is in many ways a more natural solution concept than Nash equilibrium itself.
> **Reading time:** ~30 min · **Prereqs:** [Day 16](../../02-game-theory/days/day-16-nash-equilibrium.md), [Day 18](../../02-game-theory/days/day-18-mixed-strategies.md), [Day 24](../../02-game-theory/days/day-24-coordination-focal-points.md)
> **Primary source for today:** Aumann, Robert J. "Subjectivity and Correlation in Randomized Strategies." *Journal of Mathematical Economics*, 1(1):67–96, 1974. Also: Hart, Sergiu & Mas-Colell, Andreu. "A Simple Adaptive Procedure Leading to Correlated Equilibrium." *Econometrica*, 68(5):1127–1150, 2000.

---

## The Hook

You're driving toward an intersection at the same time as another car approaching from the left. Neither of you knows what the other will do. If you both go, there's a collision. If you both stop, you waste time. You each want to go, but only if the other stops.

This is a coordination problem with two Nash equilibria: (You go, Other stops) and (You stop, Other goes). Mixed strategy Nash equilibrium exists, but it assigns a collision probability of some p > 0. Neither pure equilibrium is selected without communication.

Enter the traffic light. A traffic light is a mediator: it sends a private signal to each player ("Go" or "Stop") from a correlated joint distribution. Crucially, the signals are correlated — when one driver sees Green, the other sees Red. Each driver, seeing their signal, has no incentive to deviate: if you see Green, you know the other sees Red, so going is safe and optimal.

The traffic light implements a **correlated equilibrium** — an outcome that neither pure strategy Nash equilibrium achieves on its own, made possible by a mediator coordinating the joint signal.

Robert Aumann introduced correlated equilibrium in 1974 and received the Nobel Prize in Economics in 2005 partly for this insight. The concept is not just theoretically elegant — it is more general than Nash equilibrium and, in some respects, more natural.

---

## Building the Intuition

### Nash equilibrium vs. correlated equilibrium

**Nash equilibrium:** Each player independently randomises over strategies. The mixed strategies are statistically *independent* — players don't condition on each other's randomisations.

**Correlated equilibrium:** Players receive a private signal from a shared (possibly correlated) distribution. Each player conditions on their own signal before choosing a strategy. Players follow their signal if doing so is individually optimal given what they infer about others' signals.

**The key difference:**

In a Nash equilibrium, players' randomisations are independent. In a correlated equilibrium, a mediator coordinates the joint randomisation — players' actions can be correlated. This strictly expands the set of achievable outcomes.

---

### The traffic light as correlated equilibrium

**Game matrix (Intersection):**

```
                     Other: Go     Other: Stop
You: Go              (−10, −10)    (1, 0)
You: Stop            (0, 1)        (0, 0)
```

**Nash equilibria:**
- Pure NE 1: (You Go, Other Stop) — payoffs (1, 0).
- Pure NE 2: (You Stop, Other Go) — payoffs (0, 1).
- Mixed NE: You go with p*, Other goes with q* (solving indifference conditions). At mixed NE, collision happens with positive probability.

**Correlated equilibrium via traffic light:**

Mediator draws one of two signals with probability 1/2 each:
- Signal A: You = Green (Go), Other = Red (Stop).
- Signal B: You = Red (Stop), Other = Green (Go).

Is this a correlated equilibrium? Each player, upon seeing their signal, must prefer to follow it.

*You see Green (probability 1/2):*
- You know: Other has Red → Other will Stop.
- Going gives payoff 1. Stopping gives payoff 0.
- **Follow signal (Go).** ✓

*You see Red (probability 1/2):*
- You know: Other has Green → Other will Go.
- Stopping gives payoff 0. Going gives payoff −10.
- **Follow signal (Stop).** ✓

Neither player has incentive to deviate from their signal. This is a correlated equilibrium.

**Expected payoff:** Each player expects (1/2)(1) + (1/2)(0) = 0.5. This is better than the mixed NE (which involves collision risk) and more equitable than either pure NE (where one player always defers).

---

### The formal definition

**Correlated equilibrium:**

A joint probability distribution over all possible combinations of players' strategies is a correlated equilibrium if it passes one test: for every player and every alternative action they could take instead of their recommended one, following the recommendation gives at least as high an expected payoff as deviating — given what that player can infer about the others' recommendations from their own signal.

In plain terms: a mediator draws from the joint distribution and privately tells each player what to do. A correlated equilibrium is a distribution where no player, upon receiving their private recommendation, wishes they had done something else instead.

---

### The relationship to Nash equilibrium

**Every Nash equilibrium is a correlated equilibrium.** (Any product distribution of independent mixed strategies is a correlated equilibrium — just a special case where the signals are uncorrelated.)

**The converse is false.** There exist correlated equilibria that cannot be written as a mixture of Nash equilibria.

**The set of correlated equilibria:**

- Nash equilibria: a finite set of points (or a small manifold).
- Correlated equilibria: a convex polytope (the intersection of linear constraints, one per player-strategy pair). Always non-empty (every Nash equilibrium is one).
- **The correlated equilibrium set contains the convex hull of Nash equilibria, and often more.**

```
CE set (convex polytope)
┌──────────────────────┐
│     ·   ·            │
│   ·  NE₁  ·          │
│       ·  NE₂          │
│           ·           │
└──────────────────────┘
```

The correlated equilibrium set is strictly larger than the convex hull of Nash equilibria in many games — reaching outcomes neither pure nor mixed Nash equilibria can sustain.

---

### Why correlated equilibrium is natural

Aumann's argument: Nash equilibrium is a special case of correlated equilibrium where the signals are independent. But in the real world, players' reasoning processes are often correlated — they read the same news, observe the same public signals, follow the same conventions. Correlated equilibrium models this naturally.

**The game of Chicken revisited (Day 24):**

```
                    B: Swerve     B: Drive
A: Swerve           (0, 0)        (−1, 1)
A: Drive            (1, −1)       (−10, −10)
```

Nash equilibria:
- Pure NE 1: (A swerves, B drives) — payoffs (−1, 1).
- Pure NE 2: (A drives, B swerves) — payoffs (1, −1).
- Mixed NE: probability of collision > 0, each player earns < 0 in expectation.

**A correlated equilibrium:**

Mediator chooses with equal probability:
- (A drives, B swerves) with probability 1/2.
- (A swerves, B drives) with probability 1/4.
- (A swerves, B swerves) with probability 1/4.

Check: if A is recommended "Drive," A knows B is recommended "Swerve" (only possible given the distribution), so A drives (payoff 1 > swerve payoff −1). If A is recommended "Swerve," A doesn't know which sub-case: either B is driving (probability 2/3) or B is swerving (probability 1/3). Expected payoff from swerving: (2/3)(−1) + (1/3)(0) = −2/3. Expected payoff from driving: (2/3)(−10) + (1/3)(1) = −19/3. Follow signal (swerve). ✓

Expected payoffs: A gets (1/2)(1) + (1/4)(−1) + (1/4)(0) = 1/4. B gets (1/2)(−1) + (1/4)(1) + (1/4)(0) = −1/4... *This specific distribution gives B negative expected payoff — a better CE would use symmetric weights. The point is that the CE set contains outcomes outside the Nash set.*

---

### Learning correlated equilibrium: regret minimisation

Aumann's original paper posed correlated equilibrium as a theoretical concept. Hart & Mas-Colell (2000) showed something remarkable: **simple learning dynamics converge to correlated equilibrium.**

**The regret minimisation algorithm:**

Each period, player i plays some strategy. After the period, i computes the "regret" for each alternative strategy: how much more they would have earned if they had played differently, given what opponents did.

Player i updates their strategy distribution to reduce regret. Over time, the *time-average* of play converges to a correlated equilibrium — even though no one is computing Shapley values, solving linear programs, or communicating with a mediator.

**Why this matters:**

1. **Correlated equilibrium is computationally tractable.** Finding a Nash equilibrium is PPAD-hard (computationally equivalent to hard problems in complexity theory). Finding a correlated equilibrium is a linear program — solvable in polynomial time.

2. **Correlated equilibrium emerges from learning.** In systems where agents update based on past experience (A/B tests, RL agents, repeated bidders), the time-average of outcomes converges to correlated equilibrium, not necessarily Nash equilibrium. This makes CE a more relevant solution concept for real deployed systems.

3. **No mediator is needed in the limit.** The joint distribution of play converges to a CE without any explicit correlation device — players' independently evolving strategies become correlated through the shared history of play.

---

### Correlated equilibrium in AI products

**Multi-armed bandit algorithms as CE learners:**

When multiple agents use regret-minimising bandit algorithms in the same environment, their joint play converges to correlated equilibrium. This is why multi-agent environments trained with regret-based RL can produce coordinated behaviour without explicit communication — the environment is the implicit mediator.

**Ad auction coordination:**

In repeated ad auctions where multiple advertisers bid, each advertiser running a regret-minimising bid strategy effectively creates a correlated equilibrium in the joint bidding distribution. Platforms can predict the emergent bidding patterns using correlated equilibrium as the solution concept.

**Feature flags as coordination signals:**

When a platform uses feature flags to roll out changes (e.g., gradually enabling a new pricing UI), the flag state acts as a public signal correlating user and internal team behaviour. This is a correlated equilibrium device: all stakeholders condition on the flag state, coordinating their actions without explicit communication.

**Platform rules as CE devices:**

Platform rules (posting guidelines, auction formats, ranking policies) function as correlated equilibrium mediators: they send implicit signals ("this is what other participants do here") that coordinate behaviour. A platform that designs its rules well selects among CE outcomes — choosing the one that best serves the platform's objectives.

---

## Where It Breaks / What It Is Not

**Correlated equilibrium requires a trusted mediator.** The signal must come from a party that all players trust not to favour one player over another. In practice, a neutral third party (arbitrator, platform, protocol) must be accepted as the mediator. If players distrust the mediator, they won't follow recommendations.

**CE allows outcomes that are collectively bad.** Just as Nash equilibria can be inefficient (Prisoner's Dilemma), correlated equilibria can sustain bad outcomes. The CE set contains Nash equilibria (which may be inefficient) and more. The mediator can select welfare-maximising CE, but this requires knowing players' payoffs.

**The mediator-free learning result requires many rounds.** Hart-Mas-Colell convergence is asymptotic — the time-average converges to CE over many periods. In fast-moving markets or games with few interactions, convergence may not be achieved before the environment changes.

**Not all CE are created equal.** The CE polytope is large — some CE are better than others. Selecting among correlated equilibria (analogous to focal point selection among Nash equilibria) requires additional criteria. The "correlated equilibrium maximising social welfare" (or some objective) is a well-posed linear program.

---

## Try It Yourself

**Exercise 1 — Verify a correlated equilibrium (10 min)**

Battle of the Sexes:
```
                    B: Ballet     B: Basketball
A: Ballet           (2, 1)        (0, 0)
A: Basketball       (0, 0)        (1, 2)
```

Consider the distribution: p(Ballet, Ballet) = 1/2, p(Basketball, Basketball) = 1/2, all other profiles = 0.

a) Is this distribution a correlated equilibrium? Verify by checking each player's incentive constraint for each signal.
b) What is each player's expected payoff under this CE?
c) Compare to the mixed strategy Nash equilibrium (which you can compute from Day 16's underline method). Which is better for each player?

<details>
<summary>Worked answer</summary>

**a)** Signals: A and B are both told the same outcome (either Ballet or Basketball, each with prob 1/2).

*A is told "Ballet":* A knows B is also told "Ballet" → B plays Ballet.
- A plays Ballet: payoff 2. A plays Basketball: payoff 0.
- Follow signal ✓.

*A is told "Basketball":* A knows B plays Basketball.
- A plays Basketball: payoff 1. A plays Ballet: payoff 0.
- Follow signal ✓.

Same analysis applies to B by symmetry. **This is a correlated equilibrium.**

**b)** Expected payoffs: each player gets (1/2)(first NE payoff) + (1/2)(second NE payoff).
- A: (1/2)(2) + (1/2)(1) = 1.5.
- B: (1/2)(1) + (1/2)(2) = 1.5.

**c)** Mixed NE (from Day 16): A plays Ballet with probability p where B is indifferent. Setting up: B indifferent ⟹ 1·p + 0·(1−p) = 0·p + 2·(1−p) → p = 2/3. By symmetry q = 1/3.

Expected payoffs at mixed NE:
- A: (2/3)(1/3)(2) + (2/3)(2/3)(0) + (1/3)(1/3)(0) + (1/3)(2/3)(1) = 4/9 + 2/9 = 6/9 = 2/3.
- B (by symmetry): 2/3.

**The CE dominates the mixed NE:** both players get 1.5 under CE vs. 2/3 under mixed NE. The mediator's coordinating signal allows both players to benefit simultaneously — Nash equilibrium's independence requirement prevents this.

</details>

---

**Exercise 2 — Finding a welfare-improving CE (10 min)**

Prisoner's Dilemma (canonical payoffs: T=5, R=3, P=1, S=0):

```
                    B: Cooperate    B: Defect
A: Cooperate        (3, 3)          (0, 5)
A: Defect           (5, 0)          (1, 1)
```

a) The unique Nash equilibrium is (Defect, Defect) with payoffs (1,1). Show that (Cooperate, Cooperate) is NOT a correlated equilibrium by finding a profitable deviation.

b) Is there a correlated equilibrium that achieves payoffs higher than (1,1) for both players? (Hint: Can a mediator signal "Cooperate" to both players in a way that makes following the signal individually rational?)

c) What does this imply about the limits of correlated equilibrium for resolving the Prisoner's Dilemma?

<details>
<summary>Answer</summary>

**a)** At (Cooperate, Cooperate): A is recommended "Cooperate," believes B plays Cooperate. A's payoff from Cooperate = 3. A's payoff from deviating to Defect = 5 > 3. **A deviates.** (Cooperate, Cooperate) is not a CE. The Prisoner's Dilemma's dominant-strategy structure defeats the mediator — no signal can make defection non-profitable.

**b)** No. In a CE, every player must prefer to follow the recommendation given their signal. But in the Prisoner's Dilemma, Defect strictly dominates Cooperate regardless of the signal — so any signal recommending Cooperate will be profitably deviated from. The only CE is (Defect, Defect) — the Nash equilibrium. The CE set contains only the one Nash equilibrium in this game.

**c)** Correlated equilibrium cannot resolve the Prisoner's Dilemma. The problem is that Defect is a *dominant strategy* — it's best regardless of what others do or what signals are sent. CE expands the achievable set relative to Nash equilibrium, but it cannot overcome dominant strategies. The Prisoner's Dilemma requires changing payoffs (mechanism design, Day 30) or repeated play (Day 21), not just a correlating mediator.

</details>

---

## Connect It Back

Correlated equilibrium generalises Nash equilibrium by allowing a mediator to coordinate players' joint randomisation. This enables outcomes Nash equilibrium cannot achieve (the intersection game, Battle of the Sexes), is computationally tractable (linear programming), and emerges naturally from simple regret-minimising learning dynamics. For AI systems trained over many rounds, CE is often a more relevant solution concept than Nash.

Tomorrow (Day 38) examines **the price of anarchy** — how much worse is the Nash equilibrium compared to the social optimum? And can algorithmic tools enforce better outcomes without a full mechanism redesign?

**The question you should be able to answer now:**
*Why is every Nash equilibrium a correlated equilibrium, but not every correlated equilibrium a Nash equilibrium — and why does this make CE computationally easier to find?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Aumann, Robert J. "Subjectivity and Correlation in Randomized Strategies." *Journal of Mathematical Economics*, 1(1):67–96, 1974. Read Sections 1–3 (pages 67–78). Section 1 introduces the concept through the traffic-light analogy; Section 2 provides the formal definition; Section 3 establishes the relationship to Nash equilibrium. The original is more readable than most secondary treatments.

**If you want the deep version:**

1. Hart, Sergiu & Mas-Colell, Andreu. "A Simple Adaptive Procedure Leading to Correlated Equilibrium." *Econometrica*, 68(5):1127–1150, 2000. — The regret-minimisation convergence result. Shows that uncoupled learning dynamics (no player needs to know others' payoffs) converge to CE. Sections 1–3 cover the algorithm and convergence theorem. Essential for understanding why CE is the right solution concept for learning systems.

2. Nisan, Noam, Roughgarden, Tim, Tardos, Éva & Vazirani, Vijay V. (eds.). *Algorithmic Game Theory*. Cambridge University Press, 2007. Chapter 1 ("Introduction to Mechanism Design") and Chapter 4 ("Computational Aspects of Game Theory"). — Chapter 4 covers the computational complexity of Nash equilibrium (PPAD-hard) vs. correlated equilibrium (polynomial via linear programming). The contrast is striking and practically important.

3. Cesa-Bianchi, Nicolò & Lugosi, Gábor. *Prediction, Learning, and Games*. Cambridge University Press, 2006. Chapter 7 ("Calibration and Correlated Equilibrium"). — The full treatment of regret minimisation, calibration, and convergence to correlated equilibrium. Chapter 7 connects online learning, forecasting, and game theory — directly relevant to multi-agent RL and adaptive pricing systems.


---

← [Day 36 — The Shapley Value](day-36-shapley-value) &nbsp;|&nbsp; [Day 38 — Price of Anarchy →](day-38-price-of-anarchy)
