*[Decision Science & Game Theory](../../../../README.md) · [Module 1 — The Science of Choosing](../1-overview.md) · Day 10 of 65*

# Day 10 — Decision Trees & Influence Diagrams

> **Today's one idea:** A decision tree converts a messy sequential choice into a structured diagram you solve by folding backward from the end — making implicit assumptions explicit and calculations tractable.
> **Reading time:** ~35 min · **Prereqs:** [Day 2](day-02-expected-value.md), [Day 5](day-05-bayesian-updating.md), [Day 9](day-09-value-of-information.md)
> **Primary source for today:** Clemen, Robert T. & Reilly, Terence. *Making Hard Decisions with DecisionTools*. 3rd ed., Cengage, 2014. Chapters 3–4.

---

## The Hook

A PM walks into a planning meeting with a genuinely complex decision: build a risky new feature that needs a new vendor integration, where the vendor's reliability is uncertain, the feature's market reception is uncertain, and the engineering cost depends on which path the integration takes.

Three people argue for 45 minutes. One person keeps saying "it depends." Nobody tracks the probabilities. The decision is made by whoever argued most confidently.

The PM who sketched a decision tree before the meeting says: "The EV is $180K if we use Vendor A and $40K if we use Vendor B — assuming our 30% market success estimate is right. Let me show you what changes the recommendation." The meeting ends in 10 minutes with a documented rationale.

Decision trees don't guarantee the right answer. They guarantee your reasoning is *explicit, auditable, and correctable*.

---

## Building the Intuition

### The three nodes

A decision tree has three types of nodes. Understanding each is the entire skill.

```
□ Decision node (square)
    Your choice. You control which branch you take.
    Each branch = one option available to you.

○ Chance node (circle)
    Nature's choice. You don't control this.
    Each branch = one possible outcome, with a probability.
    Probabilities must sum to 1.

◆ Terminal node (leaf / endpoint)
    The payoff. A single number at the end of this path.
```

**The one rule for solving a tree: fold back from right to left.**

At each chance node: compute the expected value (probability-weighted sum of downstream values).
At each decision node: take the maximum (or minimum if you're minimising cost).
Work from terminal nodes leftward until you reach the root.

---

### A minimal worked example

You are a founder deciding whether to pitch Investor A (big fund, low probability) or Investor B (small fund, higher probability):

**Investor A:** 20% chance of $2M investment, 80% chance of rejection (you get nothing).
**Investor B:** 60% chance of $500K investment, 40% chance of rejection.

You can only pitch one (pitching both isn't feasible in the time you have).

```
                                  ○──[Success 20%]──◆ $2,000K
                                  │
        □──[Pitch A]──────────────┤
        │                         └──[Rejection 80%]──◆ $0
[Root] ─┤
        │                         ○──[Success 60%]──◆ $500K
        │                         │
        └──[Pitch B]──────────────┤
                                  └──[Rejection 40%]──◆ $0
```

**Fold back:**

EV(Pitch A) = 0.20 × $2,000K + 0.80 × $0 = **$400K**
EV(Pitch B) = 0.60 × $500K + 0.40 × $0 = **$300K**

At the decision node: choose Pitch A (higher EV).

The tree makes the comparison precise and the assumptions visible — anyone can challenge the 20% or 60% estimates, which is the right conversation to have.

---

### Adding sequential structure

Now suppose you can pitch Investor B first, observe the outcome, and then decide whether to pitch Investor A. The sequence changes everything:

```
                               [Success 60%]──◆ $500K
                               │
[Root]──[Pitch B first]──○─────┤
                               │               [Success 20%]──◆ $2,000K
                               │               │
                               [Rejection 40%]──□──[Pitch A]──○
                                               │               └──[Rejection 80%]──◆ $0
                                               │
                                               └──[Stop]──◆ $0
```

**Fold back:**

At the "Pitch A vs Stop" decision node (reached after B rejects you):
- EV(Pitch A) = 0.20 × $2,000K + 0.80 × $0 = $400K
- EV(Stop) = $0
→ Choose Pitch A. Value here = $400K.

At the B chance node:
- EV = 0.60 × $500K + 0.40 × $400K = $300K + $160K = **$460K**

Pitching B first, then A if rejected, yields **$460K** — better than pitching A first ($400K).

The sequential structure captures information value: rejection from B keeps Investor A open and reframes your pitch. This is exactly the EVSI calculation from Day 9, expressed as a tree.

---

### Influence diagrams: the compact form

For decisions with many interconnected variables, a full decision tree explodes in size. An **influence diagram** is a graphical shorthand that shows the *relationships* between variables without drawing every path.

```
Nodes:
  □ = decision variable (you control)
  ○ = chance variable (uncertain)
  ◇ = value node (the payoff — always one node)

Arrows:
  A → B means "A influences B" (either informationally or causally)
```

**Example — launch decision:**

```
  [Market Size] ──────────────────────────────┐
       ○                                      │
                                              ▼
  [Launch?] ──────────→ [Feature Quality] → [Revenue] ◇
       □                       ○               ▲
                                               │
  [Competitor Response] ─────────────────────┘
       ○
```

Reading this: Revenue depends on Market Size, Feature Quality, and Competitor Response. Your decision (Launch?) influences Feature Quality by determining how much you invest. This compact form lets you reason about dependencies before drawing the full tree.

**Key rule:** An arrow from a chance node to a decision node means you observe that outcome before you decide. An arrow from one chance node to another means causation or correlation.

---

## The Formal Picture

**The rollback algorithm (backward induction on a decision tree):**

```
function rollback(node):
  if node is terminal:
    return payoff(node)
  
  if node is chance:
    return (probability of branch 1 × rollback(branch 1))
         + (probability of branch 2 × rollback(branch 2))
         + ...   (i.e. the expected value)
  
  if node is decision:
    return the highest value among all available branches
```

This runs in linear time — proportional to the number of nodes. It is the same backward induction that underpins sequential game solving (Day 19), Q-value computation in RL (Day 45), and any dynamic programming problem.

**Building a decision tree — the protocol:**

1. **Identify the decisions:** What choices are yours to make, and in what order?
2. **Identify the uncertainties:** What does nature control? What are the possible outcomes and their probabilities?
3. **Identify the payoffs:** What is the final outcome of each complete path?
4. **Structure the tree:** Your decisions first (left), then chance nodes, then payoffs (right). Time flows left to right; information flows left to right.
5. **Assign probabilities:** Use calibrated estimates (Day 4). State them explicitly — they are the most challengeable part.
6. **Roll back:** Compute EVs from right to left.
7. **Sensitivity analysis:** What probability estimate, if changed, would flip the optimal decision? This is the most important step.

**Sensitivity analysis:** After solving the tree, ask: which input, if changed, would change my recommended action? Then ask: is my estimate of that input reliable enough to trust the recommendation?

If the recommendation holds up under large changes to uncertain inputs, act confidently. If it flips on small changes, either gather more information (Day 9's EVSI) or choose the more conservative option.

---

### A full product decision example

You are deciding whether to build an AI-powered search feature. Key uncertainties:

- Technical feasibility: 70% chance it works well, 30% it requires a costly redesign
- Market adoption: if it works, 50% high adoption ($800K revenue), 50% low adoption ($100K revenue)
- Redesign cost if needed: $300K (assumed certain if redesign is required)

```
                                [High adoption 50%]──◆ +$800K
                                │
               [Works 70%]──○───┤
               │                └──[Low adoption 50%]──◆ +$100K
[Build?]──□──○─┤
               │                [Accept loss]──◆ −$300K
               │                │
               [Redesign 30%]───┤
                                └──[Kill feature]──◆ −$100K (sunk cost)
```

**Fold back:**

At the "Works" chance node:
EV = 0.50 × $800K + 0.50 × $100K = **$450K**

At the "Redesign" decision node:
- Accept: −$300K
- Kill (assume $100K already spent): −$100K
→ Kill is better: −$100K

At the root chance node:
EV(Build) = 0.70 × $450K + 0.30 × (−$100K) = $315K − $30K = **$285K**
EV(Don't build) = $0

→ Build the feature. EV = $285K.

**Sensitivity check:** At what technical feasibility probability does "Build" become worse than "Don't build"?

Set EV(Build) = 0: p × $450K + (1−p) × (−$100K) = 0
450p − 100 + 100p = 0
550p = 100
p = 100/550 ≈ 18%

The recommendation flips only if you believe there's less than an 18% chance of success — far below your 70% estimate. The decision is robust.

---

## Where It Breaks / What It Is Not

**Limitation 1: Trees grow exponentially.**
With n binary decisions and m binary uncertainties, the tree has 2^(n+m) terminal nodes. For complex decisions, trees become unmanageable. Influence diagrams handle structure; simulation (Monte Carlo) handles computation.

**Limitation 2: Trees require all probabilities upfront.**
You must specify P(success) before building the tree. If you have no basis for a number, the tree is only as good as your calibration. This is a feature, not a bug — it surfaces the fact that you are making probability judgements whether or not you write them down.

**Limitation 3: Trees assume sequential structure is known.**
In practice, the order of events and decisions is often itself uncertain. Influence diagrams handle this better than trees.

**Limitation 4: Trees are not game trees.**
A decision tree assumes one decision-maker (you) and nature. Once other strategic agents enter — competitors, users, regulators with their own objectives — you need game trees (Day 19) and backward induction over *multiple* players' optimal strategies, not just expected values.

---

## Try It Yourself

**Exercise 1 — Build and solve a tree (15 min)**

You are choosing between two job offers:

**Offer A (startup):** 25% chance the startup succeeds (you get equity worth $500K on top of $80K salary for 3 years), 75% chance it fails (just $80K/year for 3 years before you search again).

**Offer B (large company):** Certain $120K/year for 3 years, then you can negotiate a promotion (60% chance of a role worth $180K/year, 40% chance you stay at $120K).

Assume 3-year horizon, no discounting for simplicity. Build the decision tree, roll back, and choose.

<details>
<summary>Worked answer</summary>

**Offer A paths:**
- Success (25%): $80K×3 + $500K = $740K
- Failure (75%): $80K×3 = $240K

EV(A) = 0.25×$740K + 0.75×$240K = $185K + $180K = **$365K**

**Offer B paths:**
- Promotion (60%): $120K×3 + (next role = out of scope, treat as endpoint) = $360K over 3 years
- No promotion (40%): $120K×3 = $360K (same, oddly — the promotion applies to Year 4+)

For a 3-year horizon, Offer B = $360K certain.

EV(B) = **$360K**

On pure EV: Offer A ($365K) marginally wins. But note:
- $365K vs $360K is a tiny margin — within calibration error of your 25% success estimate
- Offer A has enormous variance (from $240K to $740K)
- Apply your risk attitude (Day 3): if you need stable income, the certainty of $360K may dominate

This is exactly where EU replaces EV — the "right" answer depends on your utility function.

</details>

---

**Exercise 2 — Sensitivity analysis (10 min)**

Using the AI search feature example from the page:

a) At what adoption probability does the "Build" decision become marginal (EV = EV of "Don't Build")?

b) At what redesign probability does "Build" become marginal?

<details>
<summary>Worked answers</summary>

**a) Adoption probability sensitivity:**

Let q = P(high adoption). EV(Works) = q×$800K + (1−q)×$100K
EV(Build) = 0.70×[q×$800K + (1−q)×$100K] + 0.30×(−$100K) = 0

0.70×(800q + 100 − 100q) − 30 = 0
0.70×(700q + 100) = 30
490q + 70 = 30
490q = −40 → q < 0

Implication: Even with 0% high adoption (all low: $100K), EV(Build) = 0.70×$100K + 0.30×(−$100K) = $70K − $30K = $40K > 0. The decision to build is robust to adoption uncertainty — it's profitable even in the worst adoption scenario.

**b) Redesign probability sensitivity:**

Let r = P(redesign). EV(Build) = (1−r)×$450K + r×(−$100K) = 0

450 − 450r − 100r = 0
450 = 550r
r = 450/550 ≈ 82%

The feature only becomes a bad investment if there's an 82% chance of a costly redesign. Well above your 30% estimate — robust.

</details>

---

**Exercise 3 — Draw an influence diagram (10 min, L2)**

Draw an influence diagram for the following decision: whether to use an external LLM API or train your own model for a new AI feature.

Variables to include:
- Your decision: external API vs. own model
- Uncertainty: user adoption rate, API cost trajectory, data quality for training
- Payoff: net revenue over 2 years

Draw the nodes and arrows. For each arrow, label it: is this a causal influence, an informational influence (you observe this before deciding), or a correlation?

---

## Connect It Back

Today you got the operational tool that makes everything from Days 2, 5, and 9 concrete. Expected value (Day 2) is what you compute at chance nodes. Bayesian updating (Day 5) is what changes probabilities when information arrives mid-tree. Value of information (Day 9) is computable directly from the tree by comparing EV with and without a branch that represents learning.

Tomorrow (Day 11) is a rest and synthesise day — you will re-derive the core framework from scratch and apply it to one real past decision. No new material: consolidation of Days 1–10.

**The question you should be able to answer now:**
*Why does the rollback algorithm work — and why does it produce the same answer as exhaustively evaluating every possible path through the tree?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Clemen & Reilly, *Making Hard Decisions with DecisionTools*, Chapter 3 ("Structuring Decisions"). The clearest practical guide to drawing decision trees correctly. Focus on the section on common structuring mistakes (pp. ~80–95).

**If you want the deep version:**

1. Howard, Ronald A. "Decision Analysis: Applied Decision Theory." *Proceedings of the 4th International Conference on Operational Research*, 1966. — Howard's original paper introducing decision analysis and influence diagrams. Historically important; 15 pages.

2. Shachter, Ross D. "Evaluating Influence Diagrams." *Operations Research*, 34(6):871–882, 1986. — The algorithm for solving influence diagrams directly (without converting to trees). Technically demanding; skim for the key variable-elimination idea.

3. For AI builders: the connection between decision trees and Q-value computation is direct — see Sutton & Barto, *Reinforcement Learning*, Chapter 3, Section 3.5 ("The Bellman Equation for Optimal Policies"). The rollback algorithm you learned today and the Bellman equation are the same idea at different scales.


---

← [Day 9 — Value of Information](day-09-value-of-information) &nbsp;|&nbsp; [Day 11 — Rest & Synthesise →](day-11-rest-synthesise)
