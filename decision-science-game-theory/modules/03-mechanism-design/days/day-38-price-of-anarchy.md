*[Decision Science & Game Theory](../../../../README.md) · [Module 3 — Mechanism Design & Advanced Structures](../3-overview.md) · Day 38 of 65*

# Day 38 — Price of Anarchy & Algorithmic Game Theory

> **Today's one idea:** The Price of Anarchy measures how much worse selfish equilibrium play is compared to a coordinated social optimum — and algorithmic game theory shows that for many real systems (routing, auctions, load balancing) this ratio is bounded, small, and sometimes improvable by mechanism tweaks.
> **Reading time:** ~35 min · **Prereqs:** [Day 16](../../02-game-theory/days/day-16-nash-equilibrium.md), [Day 17](../../02-game-theory/days/day-17-prisoners-dilemma.md), [Day 30](day-30-mechanism-design.md)
> **Primary source for today:** Koutsoupias, Elias & Papadimitriou, Christos. "Worst-Case Equilibria." *STACS 1999*, Lecture Notes in Computer Science, vol. 1563, Springer. pp. 404–413. Also: Roughgarden, Tim & Tardos, Éva. "How Bad Is Selfish Routing?" *Journal of the ACM*, 49(2):236–259, 2002.

---

## The Hook

Every morning, millions of drivers independently choose their route to work. Each driver picks the route that minimises their own travel time, given current traffic. The result is the Nash equilibrium of the road network.

Is this efficient? Could central coordination — a benevolent traffic authority that assigns every driver a route — reduce total travel time?

Dietrich Braess observed in 1968 that adding a road can *increase* average travel time in a selfish routing Nash equilibrium. Drivers rushing to use the new shortcut each end up slower than before. This is **Braess's Paradox** — the equilibrium of the new, apparently better network is worse than the original.

The **Price of Anarchy** (PoA), introduced by Koutsoupias and Papadimitriou in 1999, quantifies exactly how bad the Nash equilibrium can be relative to the social optimum. For selfish routing with affine cost functions (which models realistic highway congestion), Roughgarden & Tardos proved in 2002 that the PoA is at most 4/3 — selfish drivers can be at most 33% worse than optimal. This turns out to be tight: there exist networks where selfish routing is exactly 4/3 worse than coordinated routing.

---

## Building the Intuition

### The Price of Anarchy defined

**Social optimum:** The outcome maximising total welfare (minimising total cost, maximising total value) — as if a central planner assigned strategies.

**Nash equilibrium:** The outcome where no player can improve by unilaterally deviating.

**Price of Anarchy (PoA):**

```
PoA = (Cost of worst Nash equilibrium) / (Cost of social optimum)
```

(For maximisation problems, flip to OPT/NE.)

PoA ≥ 1 always (Nash equilibrium is never better than the optimum, by definition of optimum). PoA = 1 means Nash equilibrium is socially optimal. PoA = 2 means the worst Nash equilibrium is twice as costly as coordinated play.

**Price of Stability (PoS):**

```
PoS = (Cost of best Nash equilibrium) / (Cost of social optimum)
```

PoS measures how good the best-case equilibrium is. PoS = 1 means there exists a Nash equilibrium that achieves social optimum — the coordination problem is selecting it. PoS ≤ PoA always.

---

### Braess's Paradox: a worked example

**Network:**

```
        ─────── B ──────
       /   cost = 1      \
Start ─                   ─ End
       \   cost = x/n    /
        ─────── C ──────
```

Where x = number of drivers using that road, n = total drivers. Road B: constant travel time 1 hour. Road C: travel time proportional to traffic (x/n hours).

**Without cross-link:** At Nash equilibrium, half drivers use B (cost 1) and half use C (cost 1/2). Total cost = n × (1/2 × 1 + 1/2 × 1/2) = 3n/4. Wait, equilibrium here is where both routes have equal cost: 1 = x/n → x = n/2 drivers use each route. Each driver's cost = 1 hour for B, (n/2)/n = 1/2 for C. Not equal — equilibrium is all drivers use C at cost x/n = 1 (when x = n). Or half-half at some cost. Let me use the standard Braess example.

**Standard Braess setup (simplified):**

- Road A→C has cost = x/n (congestion-dependent).
- Road A→B has cost = 1 (fixed, fast).
- Road C→D: cost = 1 (fixed).
- Road B→D: cost = x/n (congestion-dependent).
- New road C→B added with cost = 0 (free shortcut).

**Without the free road:** Equilibrium splits traffic 50/50 between routes A→C→D and A→B→D. Each route costs 1/2 + 1 = 3/2, or 1 + 1/2 = 3/2. Total cost = 3n/2.

**With the free road:** Every driver takes A→C→B→D (using the free shortcut). But now:
- A→C carries all n drivers → cost = 1.
- C→B is free.
- B→D carries all n drivers → cost = 1.
- Each driver's cost = 2.

**Everyone ends up slower** — from 3/2 to 2 — because the free road created a dominant strategy (take the shortcut) that everyone follows, causing congestion everywhere.

The social optimum (ignoring the new road or splitting traffic) achieves 3/2. The Nash equilibrium with the new road achieves 2. PoA = 2/(3/2) = 4/3.

**The lesson:** Adding capacity can hurt everyone in a Nash equilibrium. Removing the free road restores the 3/2 equilibrium. Seoul, South Korea closed a highway through the city centre in 2002 and traffic flow improved — an empirical validation of Braess's paradox.

---

### PoA for common game classes

**Selfish routing (affine costs):**

Roughgarden & Tardos (2002) proved: for networks with affine latency functions (cost = a + b × flow), PoA ≤ 4/3. The bound is tight.

For polynomial latency functions of degree d, PoA grows with d — congestion games with steep congestion functions have much worse anarchy.

**Congestion games:**

A congestion game is a game where players share resources and each resource's cost increases with the number of users. Selfish routing is a special case. Key result: every congestion game has a pure strategy Nash equilibrium (Rosenthal 1973) — existence is guaranteed. The PoA for congestion games with polynomial costs of degree d is Θ(d/log d).

**Auctions and mechanism design:**

For simple auctions (first-price, posted prices), the PoA relative to optimal VCG-based allocation is bounded — often within a constant factor of the optimal revenue or social welfare. This justifies using simple mechanisms even when they're not optimal.

**Load balancing:**

n jobs assigned to m machines; each job independently picks the least-loaded machine. PoA = 4/3 − 1/(3m) for identical machines. For unrelated machines, PoA = Θ(log m / log log m).

---

### Smoothness: a unified PoA analysis

Roughgarden (2009) introduced **smoothness**, a single condition that implies PoA bounds for a wide range of games:

A game is "smooth" (with constants λ and μ) if, when you measure the benefit each player would gain from switching to the socially optimal strategy — given everyone else is still playing selfishly — that total benefit is at least λ times the social optimum minus μ times the current social cost.

This captures the intuition that the optimal outcome must be "reachable" from any Nash equilibrium via a systematic improvement. When a game is smooth, the PoA is bounded by **μ / (1 − λ)** — a ratio determined entirely by the two constants, not by the specific network or player count.

Smoothness is powerful because:
1. It provides PoA bounds in a unified framework.
2. The bounds extend to correlated equilibria and coarse correlated equilibria — not just pure Nash.
3. It identifies which game features drive inefficiency.

---

### Improving the Price of Anarchy: mechanism tweaks

If the PoA is too large, can the mechanism designer intervene to reduce it?

**Toll roads:**

In Braess's Paradox, charging a toll on the congested roads can restore the social optimum. Players who would take the dominant (free) strategy now face a price that reflects their congestion externality. This is Pigouvian taxation applied to game-theoretic networks — the toll internalises the externality.

**Coordination devices:**

From Day 37: a mediator's correlated signal can select a better equilibrium. The PoS bounds apply at the best equilibrium; a mediator can potentially select near-optimal equilibria even when the PoA is large.

**Restructuring the game:**

The PoA can be reduced by changing the payoff structure — exactly the mechanism design approach from Day 30. If you can tax or subsidise actions, you can shift equilibria toward the social optimum.

---

### Algorithmic game theory: computation meets strategy

Algorithmic Game Theory (AGT) combines computer science's emphasis on computation with game theory's emphasis on strategic incentives. Three key questions:

**1. Computational complexity of equilibria:**

Finding a Nash equilibrium is PPAD-complete (Chen & Deng 2006) — believed to be computationally hard, even though equilibrium existence is guaranteed by Nash's theorem. Finding a correlated equilibrium is much easier (a linear programme — polynomial time). This is one reason CE is preferred in algorithmic settings.

**2. Mechanism design with computational constraints:**

Optimal mechanisms (Myerson's auction) may require exponential computation for combinatorial problems (spectrum auctions, package bidding). AGT asks: what is the best computationally tractable mechanism? Often, simple mechanisms (posted prices, sequential auctions) are nearly optimal and vastly easier to implement.

**3. Learning and convergence:**

Do real strategic systems — bidding algorithms, routing protocols, multi-agent RL — converge to equilibria? If so, which ones? Regret-minimising algorithms converge to correlated equilibria (Day 37). But convergence can be slow, and in changing environments, the "equilibrium" is always in motion.

---

### Price of Anarchy in AI systems

**Recommendation systems:**

Each recommendation algorithm selfishly maximises its own platform's engagement metric. The Nash equilibrium of competing recommendation systems may produce an "attention fragmentation" that is worse for overall user welfare than coordinated content allocation — a PoA for the attention economy.

**Multi-agent RL training:**

When multiple RL agents train in a shared environment, their joint policy convergences to some equilibrium. If agents are self-interested, this may be a poor Nash equilibrium. The PoA of multi-agent RL training measures how far from optimal the emergent joint policy is — a key metric for cooperative AI systems.

**Ad auctions:**

Advertisers using automated bidding algorithms play a repeated auction game. The PoA of the resulting bidding equilibrium relative to the VCG-optimal allocation measures the efficiency loss from selfish automated bidding. For the generalised second-price auction, the PoA has been studied and is bounded (Lucier & Paes Leme 2011).

**Traffic routing in networks:**

Modern traffic navigation apps (Google Maps, Waze) route individual drivers selfishly. The Braess paradox applies to real road networks. Empirically, coordinated routing (all drivers follow an app's collective routing recommendation) can outperform the Nash equilibrium — the app functions as a correlated equilibrium mediator when users trust it.

---

## The Formal Picture

**Congestion game:**

A congestion game has a set of players and a set of shared resources. Each player chooses a subset of resources to use (e.g., a set of roads forming a path through a network). Each resource has a cost that rises with the number of players using it. Each player's total cost is the sum of costs across all the resources in their chosen path.

**Social cost:**

The social cost of a situation is the sum of every player's individual cost — a measure of how much total harm selfish play inflicts. It equals the total load on each resource, multiplied by that resource's cost at that load, summed across all resources.

**Price of Anarchy:**

The PoA is the ratio of two numbers: the cost of the *worst* Nash equilibrium (the most damaging way selfish play can land), divided by the cost of the *social optimum* (the best possible outcome under coordinated routing). A PoA of 4/3 means even the worst selfish outcome is at most one-third more costly than the ideal.

**Roughgarden-Tardos theorem (2002):**

For selfish routing where each road's travel time grows linearly with traffic (the realistic highway model), **PoA ≤ 4/3**. The bound is tight — there exist specific networks where selfish routing is exactly 4/3 worse than coordinated routing, and no network makes it worse.

**Proof idea:** Show that the total benefit players would gain by switching to the optimal routes — given everyone else is routing selfishly — is always at least 3/4 of the optimal social cost. This "smoothness" property implies the 4/3 bound through a short algebraic argument.

---

## Where It Breaks / What It Is Not

**PoA bounds are worst-case.** The PoA tells you the worst possible ratio across all networks and cost functions in a class. Typical real networks may have much better PoA — the 4/3 bound for selfish routing is achieved only on specific pathological networks.

**PoA ignores dynamics.** PoA compares static equilibria to the social optimum. In dynamic systems where demand and supply change continuously, the relevant question is whether the system converges to a good equilibrium at all — not just what the equilibrium looks like.

**PoA assumes players are strategic.** If players use simple heuristics (always take the same route, never update bids), the relevant analysis changes. Behavioural game theory (not covered in this course) studies PoA when players are boundedly rational.

---

## Try It Yourself

**Exercise 1 — Compute PoA in a simple game (10 min)**

Two players each choose to use Resource A or Resource B.

Cost functions: A has cost = x (number of users). B has cost = 3 (constant).

Both players simultaneously choose.

a) What are the pure strategy Nash equilibria?
b) What is the social optimum (minimise total cost)?
c) Compute the Price of Anarchy.

<details>
<summary>Worked answer</summary>

**Payoff matrix** (each entry = (cost to Row, cost to Column)):

```
               Col: A          Col: B
Row: A         (2, 2)          (1, 3)
Row: B         (3, 1)          (3, 3)
```

**a)** Nash equilibria:
- (A, A): Row cost 2, Col cost 2. Deviation? Row → B: cost 3 > 2. Col → B: cost 3 > 2. No profitable deviation. **Nash equilibrium.**
- (B, B): Row cost 3, Col cost 3. Deviation? Row → A: cost 1 < 3. Profitable deviation! Not NE.
- (A, B): Row cost 1, Col cost 3. Row deviates to B? Cost 3 > 1. No. Col deviates to A? Cost 2 < 3. Yes! Not NE.
- (B, A): Symmetric to (A, B). Not NE.

**Unique pure strategy NE: (A, A)** with total cost = 4.

**b)** Social optimum: minimise total cost.
- (A, A): total cost = 4.
- (A, B): total cost = 1 + 3 = 4.
- (B, A): total cost = 3 + 1 = 4.
- (B, B): total cost = 6.

**Social optimum = 4** (achieved at (A,A) or the asymmetric profiles).

**c)** PoA = 4/4 = **1**. No price of anarchy in this game — Nash equilibrium achieves the social optimum. Not all games have PoA > 1.

</details>

---

**Exercise 2 — Braess's Paradox in a product context (10 min)**

You manage a content delivery network with two routing paths. Each path has cost = (load × 2). Total load = 100 requests/second, split evenly: each path carries 50 rps at cost 100.

You add a free "express lane" between the two paths (cost = 0). Every server now routes through the express lane, causing each path to carry 100 rps.

a) What is the new cost per path?
b) What is the Nash equilibrium total cost before and after the express lane?
c) You can charge a toll on the express lane. What toll makes the Nash equilibrium revert to the 50/50 split?

---

**Exercise 3 — PoA in your system (15 min)**

Identify a system in your work where multiple agents make selfish decisions that affect a shared resource (bandwidth, engineering time, customer attention, ad budget).

a) Describe the congestion game: players, resources, cost functions.
b) What is the Nash equilibrium of this system (current behaviour)?
c) What is the social optimum (ideal coordinated behaviour)?
d) Estimate the PoA qualitatively: are selfish decisions causing significant welfare loss?
e) What mechanism tweak (toll, coordination signal, rule change) could move the system toward the social optimum?

---

## Connect It Back

The Price of Anarchy places a quantitative bound on the cost of self-interested behaviour — showing that for many real systems, the inefficiency of Nash equilibrium is bounded and often small. This is both reassuring (markets are not infinitely wasteful) and actionable (targeted interventions can reduce the gap). Algorithmic game theory connects this to computation: finding good equilibria and designing computationally tractable mechanisms are engineering problems as much as economic ones.

Tomorrow (Day 39) covers **Information Design & Bayesian Persuasion** — a recent and practically important extension: when can a sender strategically design *what information to reveal* to shift a receiver's beliefs and actions in the sender's favour? This is the "dual" of mechanism design: instead of designing the rules of the game, you design what the other player knows.

**The question you should be able to answer now:**
*What is the Price of Anarchy, how does Braess's Paradox illustrate that adding capacity can worsen Nash equilibrium outcomes, and what mechanism intervention (tolls, coordination) can reduce the PoA?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Roughgarden, Tim & Tardos, Éva. "How Bad Is Selfish Routing?" *Journal of the ACM*, 49(2):236–259, 2002. Read Sections 1–3 (pages 236–248). Section 1 introduces the model and states the 4/3 bound; Sections 2–3 prove it using a clean potential function argument. One of the most elegant proofs in algorithmic game theory.

**If you want the deep version:**

1. Nisan, Noam, Roughgarden, Tim, Tardos, Éva & Vazirani, Vijay V. (eds.). *Algorithmic Game Theory*. Cambridge University Press, 2007. Chapter 17 ("Selfish Load Balancing") and Chapter 18 ("The Price of Anarchy and the Design of Scalable Resource Allocation Mechanisms"). — The comprehensive treatment. Chapter 17 covers load balancing PoA; Chapter 18 introduces smoothness and the unified PoA framework. Both are accessible after today's page.

2. Roughgarden, Tim. *Twenty Lectures on Algorithmic Game Theory*. Cambridge University Press, 2016. Lectures 3–5 ("Selfish Routing and the Price of Anarchy"). — Roughgarden's lecture notes, freely available online. The most pedagogically clear treatment of PoA, smoothness, and Braess's paradox. Lectures 3–5 cover the material of today's page at a deeper level.

3. Koutsoupias, Elias & Papadimitriou, Christos. "Worst-Case Equilibria." *STACS 1999*, Lecture Notes in Computer Science, vol. 1563:404–413, Springer. — The original paper introducing the term "Price of Anarchy." Four pages. The load-balancing example that started the field. Read this before Roughgarden-Tardos for historical context.


---

← [Day 37 — Correlated Equilibrium](day-37-correlated-equilibrium) &nbsp;|&nbsp; [Day 39 — Information Design & Bayesian Persuasion →](day-39-information-design)
