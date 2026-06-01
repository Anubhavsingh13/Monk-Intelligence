*[Decision Science & Game Theory](../../../../README.md) · [Module 2 — Game Theory Fundamentals](../2-overview.md) · Day 23 of 65*

# Day 23 — Evolutionary Game Theory

> **Today's one idea:** When strategies reproduce in proportion to their payoff performance in a population, the selection pressure drives populations toward Nash equilibria — without any player ever reasoning about the game.
> **Reading time:** ~35 min · **Prereqs:** [Day 16](day-16-nash-equilibrium.md), [Day 17](day-17-prisoners-dilemma.md), [Day 21](day-21-repeated-games.md)
> **Primary source for today:** Maynard Smith, John & Price, George R. "The Logic of Animal Conflict." *Nature*, 246:15–18, 1973. Also: Axelrod & Hamilton, *Science* 1981 (Day 21 primary source — biological section).

---

## The Hook

Imagine a world where no one reasons about strategy at all. Animals compete for resources; bacteria compete for nutrients; early humans competed for territory. There are no payoff matrices, no backward induction, no Nash equilibrium calculations. And yet, across millions of years and billions of interactions, remarkably stable patterns emerge: animals in the same species rarely fight to the death over territory; they display, threaten, retreat. Cooperation appears in ants, bees, vampire bats. Aggression is calibrated.

John Maynard Smith, a British evolutionary biologist, looked at this and asked: *Can game theory explain biological strategy without requiring the players to do game theory?*

The answer is yes — and the mechanism is natural selection. Strategies that produce higher payoffs leave more descendants. Over time, higher-payoff strategies become more common. The population "computes" Nash equilibria through reproduction, not reasoning.

Maynard Smith's 1973 paper introduced the **Evolutionarily Stable Strategy (ESS)** — a concept that turned out to be a refinement of Nash equilibrium, derived entirely from evolutionary first principles. It is one of the most striking instances of the same mathematical structure appearing independently in two completely different fields.

---

## Building the Intuition

### The Hawk-Dove game

Two animals contest a resource worth V. Each can play **Hawk** (escalate to a fight) or **Dove** (display and retreat if met with aggression).

When Hawk meets Hawk: fight erupts. One wins the resource (V), one is injured (cost C). Expected payoff: (V − C)/2 each.

When Hawk meets Dove: Dove retreats, Hawk gets the resource. Hawk: V. Dove: 0.

When Dove meets Dove: they display at each other and one eventually retreats; resource shared evenly. Each gets V/2.

**Payoff matrix:**

```
               Opponent: Hawk     Opponent: Dove
Player: Hawk   (V−C)/2            V
Player: Dove   0                  V/2
```

**Case 1: V > C (resource is worth more than injury cost)**

Playing Hawk strictly dominates playing Dove:
- Against Hawk: (V−C)/2 > 0 ✓ (since V > C)
- Against Dove: V > V/2 ✓

Nash equilibrium: everyone plays Hawk. But this requires V > C — the resource is worth the risk.

**Case 2: V < C (injury is costly relative to resource)**

Now (V−C)/2 < 0 — fighting is net negative when both Hawk. No pure strategy NE:
- If everyone plays Hawk, you'd rather switch to Dove (avoid injury)
- If everyone plays Dove, you'd rather switch to Hawk (get the full resource)

**Mixed strategy NE (and the ESS):** Mix Hawk with probability p* = V/C.

With V = 4 and C = 10: p* = 4/10 = 0.4. The population plays 40% Hawk and 60% Dove in equilibrium.

**Why is this an equilibrium?** At p* = V/C, Hawks and Doves have equal expected fitness — no selection pressure favours either. The population is stable. This is the Nash equilibrium's indifference condition appearing in evolutionary form.

---

### Evolutionarily Stable Strategy (ESS)

Nash equilibrium says: no player wants to deviate given what others are doing. ESS says something stronger: **a strategy (or mixed strategy) is ESS if, when the entire population plays it, a small group of "mutants" playing any other strategy cannot invade.**

**Formal condition:**

A strategy σ* is evolutionarily stable if, for any mutant strategy σ ≠ σ*:

```
u(σ*, σ*) > u(σ, σ*)                    [condition 1: σ* does better against itself than mutant does]
```

OR

```
u(σ*, σ*) = u(σ, σ*)   AND   u(σ*, σ) > u(σ, σ)    [condition 2: tie-breaker — σ* does better against the mutant]
```

The first condition is sufficient: if the incumbent strategy beats any mutant in a population of incumbents, mutants can't spread. The second handles the case where they tie — then σ* must do better against the mutant than the mutant does against itself.

**Key relationship:** Every ESS is a Nash equilibrium. Not every Nash equilibrium is an ESS. ESS is a refinement of Nash equilibrium.

---

### Replicator dynamics: watching evolution happen

The ESS tells you what is stable. **Replicator dynamics** tells you how populations get there.

In a population with two strategies A and B, suppose A-players make up a fraction x of the population (and B-players the remaining 1−x).

**A's payoff against the current mix** = (fraction that are A) × (payoff of A vs A) + (fraction that are B) × (payoff of A vs B).

**Average population payoff** = (fraction that are A) × (A's payoff) + (fraction that are B) × (B's payoff).

**The replicator rule:** Strategy A grows in share when it earns *above* the population average, and shrinks when it earns *below* the average. The bigger the advantage, the faster it spreads.

This is natural selection in continuous time. The equation has the same mathematical form as learning rules in game theory and gradient descent in machine learning — all three are special cases of a more general "fitness-proportionate update" principle.

**Applying to Hawk-Dove (V=4, C=10):**

Let x = fraction of Hawks. 

At x < 0.4 (too few Hawks): Hawk earns above average → Hawk fraction grows.
At x > 0.4 (too many Hawks): Hawk earns below average → Hawk fraction shrinks.
At x = 0.4: stable equilibrium. The replicator dynamics converge to the ESS.

```
Population fraction of Hawks
1.0 │                                ╲
    │                                  ╲
0.4 │─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ●  ESS
    │            ↗                    ↑ ↓
0.0 │───────────────────────────────────
    0                                time
       ←─────── converging from below
```

---

### The evolution of cooperation revisited

Axelrod's tournament showed Tit-for-Tat wins in a round-robin tournament. Evolutionary game theory asks: if strategies reproduce in proportion to their tournament scores, does Tit-for-Tat invade a population of Defectors? And once established, can it resist invasion?

**Can Tit-for-Tat invade AllDefect?**

In a population of AllDefect players, a single Tit-for-Tat mutant encounters almost all Defectors. TFT cooperates once (getting S = 0), then defects every subsequent round (getting P). AllDefect gets P every round. TFT's first-round loss means it earns slightly less than AllDefect — it cannot invade a population of AllDefect players if it is rare.

**But:** a *cluster* of Tit-for-Tat players can invade. When TFTs meet each other (cooperate every round, earning R), they outperform Defectors (earning P per round against TFTs). The key is spatial or network structure — if TFTs tend to interact with each other, they can escape the initial invasion problem.

This is one of the most important results in evolutionary game theory: cooperation can evolve not because individual agents reason about the future, but because spatial or network clustering allows cooperators to interact preferentially with each other.

**ESS of the repeated Prisoner's Dilemma:**

In populations where the stage game is repeated (with discount factor δ), AllCooperate and Tit-for-Tat are both ESS candidates under specific conditions — but neither is unconditionally stable. AllDefect is always an ESS in the one-shot game. The evolutionary stability of cooperation depends on δ, the population structure, and the mix of strategies in the environment. This mirrors Day 21's cooperation threshold exactly.

---

## The Formal Picture

**Evolutionarily Stable Strategy — the full condition:**

A strategy σ* is evolutionarily stable if, for any mutant strategy σ that differs from σ*:

The incumbent σ* earns a higher payoff than any rare mutant would, when both are tested against a population that is almost entirely incumbents (with only a tiny fraction of mutants mixed in).

**Two equivalent conditions (Maynard Smith's formulation):**

- **Condition 1:** The incumbent beats any mutant when both face a population of incumbents. (If σ* wins in its own territory, the mutant can't invade.)
- **Condition 2:** If Condition 1 is a tie — both get the same payoff against incumbents — then σ* must outperform the mutant when *both face a population of mutants*. (Tie-breaker: σ* is better in the mutant's own territory too.)

**Replicator dynamics — how the population moves:**

For each strategy in the population: **strategies that earn above the population average grow in share; strategies that earn below average shrink.** That is the entire rule. Written out for strategy i:

> Change in i's share = (i's current share) × (i's payoff − average population payoff)

The average population payoff is just the payoff of each strategy multiplied by how common it is, all added together.

**Relationship to Nash equilibrium:**

Every ESS is a Nash equilibrium — but not every Nash equilibrium is an ESS. The extra requirement ESS adds is *invasion stability*: the equilibrium must hold even when a small group of "mutants" tries a different strategy. Nash equilibrium only requires that no *single player* wants to deviate; ESS requires that no *invading group* can spread.

---

### Evolutionary game theory in AI and technology

**Learning algorithms as replicator dynamics:**

The replicator equation has the same mathematical form as online learning algorithms. Cross-entropy minimisation, multiplicative weights update, and replicator dynamics are all special cases of a general "follow the gradient of payoff" rule. Understanding that learning algorithms are evolutionary selection processes explains why they converge (or fail to converge) to Nash equilibria in game-theoretic settings.

**A/B testing as evolutionary selection:**

When you run an A/B test with traffic allocation based on conversion rates, you are running replicator dynamics on feature variants. The feature with higher "fitness" (conversion) gets more traffic. Multi-armed bandit algorithms (Day 42) formalise this: UCB and Thompson Sampling are optimised versions of the replicator dynamic that balance exploration and exploitation.

**Strategy spaces in multi-agent RL:**

When multiple RL agents co-train in a shared environment (Day 49), their policy updates implement something like replicator dynamics — policies that earn higher reward become more probable. Understanding EGT predicts which equilibria these systems converge to and which are stable under perturbation.

**Content moderation as ecosystem management:**

A social platform can be modelled as a population of content creators with strategies: high-quality content, clickbait, misinformation. The platform's recommendation algorithm defines the "payoff" each strategy receives. Replicator dynamics predicts: if clickbait earns more engagement (higher fitness), clickbait strategies spread. The moderation challenge is to change the fitness landscape so that high-quality content has higher evolutionary fitness — essentially, applying mechanism design to evolutionary dynamics.

---

## Where It Breaks / What It Is Not

**ESS assumes large populations and random matching.** If the population is small, drift dominates selection — random fluctuations can eliminate fit strategies and establish poor ones. This is the biological phenomenon of genetic drift applied to strategies.

**ESS assumes symmetric games.** Many important games are asymmetric: buyers and sellers, incumbents and entrants, employers and employees. Standard ESS theory requires extension (asymmetric ESS) to handle these — the analysis becomes more complex.

**Replicator dynamics are deterministic and mean-field.** The equation describes average behaviour in an infinite population. Real populations are finite, noisy, and often spatially structured. Spatial structure can dramatically change which strategies are ESS — cooperators can survive in spatial games where they'd be wiped out in well-mixed populations.

**Evolution is not optimisation.** ESS strategies are stable, not globally optimal. Natural selection produces local stability (no mutant can invade), not welfare-maximising outcomes. This is the evolutionary analogue of Nash equilibrium failing to be Pareto optimal — stability and efficiency are different criteria.

**Speed matters.** Replicator dynamics assume continuous time and small fitness differences. In technology markets, selection can be fast (a viral product captures 80% of market share in months) — so the quasi-static assumption of replicator dynamics may not hold. Punctuated equilibria (long stability, then rapid change) are common in tech but not well-captured by standard EGT.

---

## Try It Yourself

**Exercise 1 — Hawk-Dove with numbers (10 min)**

Resource value V = 6, injury cost C = 10.

a) Write out the full payoff matrix.
b) Is there a pure strategy Nash equilibrium? Check using the underline method.
c) Find the mixed strategy Nash equilibrium (= the ESS mixing probability).
d) At the ESS, what is the expected payoff per interaction?

<details>
<summary>Worked answer</summary>

**a)** Payoff matrix (player's payoff listed first):

```
                 Opponent: Hawk    Opponent: Dove
Player: Hawk     (V−C)/2 = −2       V = 6
Player: Dove     0                  V/2 = 3
```

**b)** Underline method:
- Opponent=Hawk: Hawk gets −2, Dove gets 0 → underline Dove's 0
- Opponent=Dove: Hawk gets 6, Dove gets 3 → underline Hawk's 6
- Player=Hawk: Opponent-Hawk gets −2, Opponent-Dove gets 6 → underline Opponent-Dove's 6... wait, payoffs are symmetric.

No cell has both players' payoffs underlined → **no pure strategy NE**.

**c)** ESS mixing probability: p* = V/C = 6/10 = 0.6. Population plays Hawk 60% of the time.

**d)** At ESS, Hawk and Dove have equal expected payoffs (by indifference):

Expected payoff of Hawk against ESS population:
= p* × (V−C)/2 + (1−p*) × V
= 0.6 × (−2) + 0.4 × 6
= −1.2 + 2.4 = 1.2

Expected payoff of Dove against ESS population:
= p* × 0 + (1−p*) × V/2
= 0 + 0.4 × 3 = 1.2 ✓

Both equal 1.2 — confirming indifference at the ESS.

</details>

---

**Exercise 2 — Is it an ESS? (10 min)**

In a symmetric 2-player game, strategy σ* gives:

```
u(σ*, σ*) = 4
u(σ, σ*)  = 4   [mutant gets same payoff against incumbent]
u(σ*, σ)  = 3
u(σ, σ)   = 5   [mutant does better against other mutants than σ* does]
```

Is σ* an ESS? Show your reasoning using the formal ESS conditions.

<details>
<summary>Answer</summary>

Check condition (1): u(σ*, σ*) > u(σ, σ*)? → 4 > 4? **No — they are equal.** Condition (1) fails.

Move to condition (2): u(σ*, σ) > u(σ, σ)? → 3 > 5? **No — mutant outperforms σ* against itself.**

**σ* is NOT an ESS.** The mutant strategy σ can invade: when rare, mutants mostly interact with incumbents (equal payoff); but as mutants become common, they interact more with each other and get payoff 5 vs. σ*'s 3. The mutant will spread and replace σ*.

</details>

---

**Exercise 3 — Design the fitness landscape (15 min)**

You are a product manager at a social media platform. Model the content creator ecosystem as an evolutionary game with three strategies:

- **Quality:** Produces high-quality, substantive content (high production cost, moderate engagement)
- **Clickbait:** Produces cheap, emotionally provocative content (low cost, high short-term engagement)  
- **Authentic:** Produces personal, niche content (low cost, small but loyal audience)

a) Sketch a payoff table (or describe qualitatively) showing how each strategy's "fitness" depends on the current population mix.

b) Under the current recommendation algorithm (maximises short-term engagement), which strategy is likely ESS? Use evolutionary logic — no formal calculation needed.

c) You change the recommendation algorithm to weight "return visits" and "saves" heavily alongside engagement. How does the fitness landscape shift? Which strategy becomes more evolutionarily viable?

d) Is there a risk of a mixed ESS where all three strategies coexist? Under what conditions?

---

## Connect It Back

Evolutionary game theory bridges biology and economics by stripping out the assumption that players reason. Nash equilibria emerge from selection pressure, not calculation. ESS refines Nash equilibrium by adding invasion stability — a property that turns out to be crucial for predicting which equilibria persist under perturbation.

The bridge to AI is direct: learning algorithms are replicator dynamics applied to strategy parameters. Multi-agent training is evolutionary selection on policies. Understanding EGT gives you a framework for predicting what multi-agent AI systems converge to — a foundation for Days 49–51.

Tomorrow (Day 24) introduces **coordination games and focal points** — the puzzle of how rational players select among multiple Nash equilibria when no dominant strategy or evolutionary pressure picks one out. The answer involves culture, salience, and the mysterious "meeting of minds" that Schelling called a focal point.

**The question you should be able to answer now:**
*Why is every ESS a Nash equilibrium, but not every Nash equilibrium an ESS — and what does the additional ESS requirement (invasion stability) add that Nash equilibrium lacks?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Maynard Smith, John & Price, George R. "The Logic of Animal Conflict." *Nature*, 246:15–18, 1973. Four pages. The original introduction of ESS and the Hawk-Dove game. The biological framing makes the math feel natural — these are real animals, real costs, real selection. Read it.

**If you want the deep version:**

1. Maynard Smith, John. *Evolution and the Theory of Games*. Cambridge University Press, 1982. Chapter 1 ("The Basic Model") and Chapter 2 ("Asymmetric Games"). — The book-length treatment of ESS, replicator dynamics, and the relationship to Nash equilibrium. Chapter 2's treatment of asymmetric ESS (different roles for different players) is directly relevant to market games with buyers and sellers.

2. Nowak, Martin A. & May, Robert M. "Evolutionary Games and Spatial Chaos." *Nature*, 359:826–829, 1992. — The landmark paper showing that spatial structure allows cooperators to survive in Prisoner's Dilemma games where they'd be wiped out in well-mixed populations. Two-page read; stunning result. Directly relevant to network effects in platforms.

3. Weibull, Jörgen W. *Evolutionary Game Theory*. MIT Press, 1995. Chapter 3 ("The Replicator Dynamics"). — The rigorous treatment of replicator dynamics as a dynamical system. Section 3.1–3.3 cover the connection between replicator dynamics, Nash equilibrium, and ESS. Use this if the formal connection between learning and evolution interests you for Days 49–51.


---

← [Day 22 — Rest & Synthesise](day-22-rest-synthesise) &nbsp;|&nbsp; [Day 24 — Coordination & Focal Points →](day-24-coordination-focal-points)
