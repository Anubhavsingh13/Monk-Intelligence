*[Decision Science & Game Theory](../../../../README.md) · [Module 1 — The Science of Choosing](../1-overview.md) · Day 8 of 65*

# Day 8 — Prospect Theory: The Behavioural Map of Choice

> **Today's one idea:** People evaluate outcomes as gains or losses from a reference point, weight losses roughly twice as heavily as equivalent gains, and distort probabilities nonlinearly — and this produces a precise, predictive model of real choice.
> **Reading time:** ~40 min · **Prereqs:** [Day 3](day-03-expected-utility.md), [Day 6](day-06-cognitive-biases-i.md), [Day 7](day-07-cognitive-biases-ii.md)
> **Primary source for today:** Kahneman, Daniel & Tversky, Amos. "Prospect Theory: An Analysis of Decision under Risk." *Econometrica*, 47(2):263–291, 1979. Read Sections I–IV.

---

## The Hook

By 1979, expected utility theory had been the standard model of rational choice for over 200 years. It was elegant, mathematically consistent, and backed by the authority of von Neumann and Morgenstern's axioms.

There was one problem: it was wrong.

Not wrong in the philosophical sense of missing a philosophical subtlety. Wrong in the empirical sense of *making predictions that real humans systematically violate in the laboratory and in life*.

Kahneman and Tversky spent the 1970s accumulating anomalies — choices that EU theory predicted one way and humans reliably made the other. In 1979, they published the model that explained all of them. They called it Prospect Theory, and it is the most-cited paper in the history of economics.

Today you get the full picture.

---

## Building the Intuition

### The four-fold pattern

Before we touch equations, here is the behaviour that Prospect Theory must explain. These four results are empirically robust — replicated across cultures, income levels, and experimental conditions:

| Domain | Probability | Typical Behaviour | Example |
|--------|------------|------------------|---------|
| Gains | High | Risk-averse | Prefer $900 certain over 90% chance of $1,000 |
| Gains | Low | Risk-seeking | Prefer 1% chance of $10,000 over $90 certain |
| Losses | High | Risk-seeking | Prefer 90% chance of losing $1,000 over losing $900 certain |
| Losses | Low | Risk-averse | Prefer losing $90 certain over 1% chance of losing $10,000 |

**Read this slowly.** In the gains domain, people flip from risk-averse (high probability) to risk-seeking (low probability). In the losses domain, they flip the other way. Expected utility with a concave utility function predicts *consistent* risk aversion everywhere. It cannot explain this pattern.

Prospect Theory explains all four cells with two mechanisms:
1. A value function that is S-shaped and reference-dependent
2. A probability weighting function that overweights small probabilities

---

### Mechanism 1: The value function

Three key properties, each doing real work:

**Property A — Reference dependence.** You do not evaluate outcomes as final wealth levels. You evaluate them as *gains or losses relative to a reference point*. The reference point is usually your current state (the status quo), but it can be shifted by expectations, social comparisons, or framing.

This explains why Person A (yesterday's hook, promoted from $100K to $122K) feels like a winner and Person B (expecting $140K, offered $122K) feels like a loser — despite identical outcomes.

**Property B — Diminishing sensitivity.** The marginal impact of a change *decreases as you move further from the reference point*. Going from 0 to $100 feels bigger than going from $1,000 to $1,100. Similarly, losing your first $100 hurts more than losing the 11th $100 in a series.

This means the value function is:
- **Concave** in the gains domain (standard risk aversion)
- **Convex** in the losses domain (risk-seeking when facing sure loss)

**Property C — Loss aversion.** The value function is *steeper for losses than gains*. Losing $x feels worse than gaining $x feels good, by a factor of roughly 2.25.

Put these three together:

```
Value
  ▲
  │         ●──────────────── gains: concave (risk-averse)
  │     ●───
  │ ●───
──●────────────────────────── x (outcome relative to reference)
  │ ●───
  │     ●───
  │         ●                 losses: steeper AND convex (risk-seeking)
  │              ●────────────
  ▼
```

The S-shape with asymmetric steepness is the signature of Prospect Theory.

---

### Why the four-fold pattern follows

**Gains, high probability (risk-averse):**
$900 certain vs. 90% chance of $1,000.
The value function is concave in gains — you're in the upper-left of the S-curve. The certain $900 has higher value than 90% × v($1,000). Take the certain $900.

**Gains, low probability (risk-seeking):**
$90 certain vs. 1% chance of $10,000.
Low probability of a large gain. Prospect Theory says small probabilities get *overweighted* (mechanism 2, below). The 1% chance feels more than 1% likely. The lottery becomes attractive. Buy the ticket.

**Losses, high probability (risk-seeking):**
Lose $900 certain vs. 90% chance of losing $1,000.
Now you're on the convex side of the S-curve. The value function is convex in losses — equal units of additional loss matter less and less. The 90% gamble feels better than the certain loss. Gamble.

**Losses, low probability (risk-averse):**
$90 loss certain vs. 1% chance of $10,000 loss.
Small probability of catastrophe. Combined with loss aversion (losses feel worse than equivalent gains feel good) and probability overweighting: the 1% catastrophe feels terrifying. Pay the $90 insurance. This is why we buy insurance for unlikely disasters even when the math says not to.

---

### Mechanism 2: Probability weighting

Expected utility treats probabilities literally — 1% means 1%. Prospect Theory says humans distort probabilities in a systematic, predictable way:

```
Decision weight vs. stated probability

Decision weight
1.0 │                                      ●──● (ideal: diagonal)
    │                              ●──────
    │                        ●────     ╲  actual curve
    │                  ●────             ╲
    │          ●──────                    ●
    │   ●─────                         ───●
0.0 └──────────────────────────────────────────── Stated probability
    0                0.5                  1.0
```

The curve has three critical features:
1. **Overweighting of small probabilities:** A 1% chance *feels* bigger than 1%. This explains why people buy lottery tickets and insure against rare disasters even when the expected value is negative.
2. **Underweighting of moderate-to-high probabilities:** A 70% chance *feels* smaller than 70%. This pushes people toward the certainty option, which they treat as special.
3. **Certainty is disproportionately valued:** The step from 99% to 100% feels far larger than any other 1-percentage-point change. A sure thing is treated as categorically different from a very-likely thing — even though the probability difference is tiny.

The overweighting of small probabilities combined with the value function's concavity in gains produces the classic "certainty effect": people pay a premium to eliminate risk entirely, even when the probability is already very low.

---

## The Formal Picture

**The Prospect Theory value function** (Kahneman & Tversky, 1992):

The S-shaped curve from the Building the Intuition section has three parameters. You don't need to memorise them — just know what each does:

- **Gains side:** bends gently downward (risk aversion). The curve is mild — almost a straight line (curvature α ≈ 0.88).
- **Losses side:** steeper overall and bends upward (risk-seeking for large losses). Same mild curvature (β ≈ 0.88), but pulled downward by the loss aversion factor.
- **Loss aversion coefficient λ ≈ 2.25** — this single number explains why losses feel 2.25× worse than equivalent gains feel good.

**The probability weighting function** *(L2 — the exact formula is in the primary source for those who want to compute with it):*

The overweighting of small probabilities and underweighting of high ones follows a specific S-shaped curve with a curvature parameter (γ ≈ 0.65 for gains). For most applications, knowing the *shape* is more useful than the formula: small probabilities are inflated, high probabilities are deflated, and certainty is treated as categorically special.

**The Prospect Theory value of a gamble:**

For a two-outcome gamble (gain x with probability p, or loss y with probability q):

```
Value = decision_weight(p) × value(gain x) + decision_weight(q) × value(loss y)
```

This mirrors the expected value formula from Day 2 — but with distorted probabilities (decision weights, not raw numbers) and distorted values (the S-shaped curve, not dollar amounts).

**What this model predicts:**

1. **Reference dependence** — outcomes are evaluated as gains or losses from a starting point, not as final wealth levels
2. **Loss aversion** — losses feel ~2.25× worse than equivalent gains feel good
3. **Diminishing sensitivity** — each additional gain or loss matters less than the one before
4. **Probability distortion** — small probabilities are overweighted; high probabilities are underweighted; certainty is treated as categorically special

---

### Prospect Theory in AI product design

**Reference points in pricing:**
The "anchor" (Day 6) is a reference point setter. "Was $299, now $149" sets $299 as the reference point, making $149 feel like a $150 gain. This is Prospect Theory, not just anchoring — the gain from the reference point is processed differently from the absolute price.

**The endowment effect in freemium:**
Free trial users who receive features for free experience those features as their reference point. When the trial ends, removing features feels like a *loss* — not a failure to gain. This is why free trials convert better than never having the features: loss aversion drives upgrade.

**Loss framing in notifications:**
"You've lost your 5-day streak" vs. "Start building a new streak" — identical information. The loss frame activates the steeper side of the value function. Duolingo, Snapchat, and fitness apps use this deliberately.

**Risk tolerance in AI models:**
A model that gives a confident wrong answer triggers loss aversion in users (trust loss) more than a cautious correct answer triggers utility gain. This predicts why AI products that confidently confabulate damage trust more than expected — the loss is weighted ~2× the gain.

---

## Where It Breaks / What It Is Not

**Limitation 1: The reference point is endogenous and context-dependent.**
Prospect Theory assumes you can identify the reference point — but in real decisions, the reference point is often ambiguous, manipulable, or multiple. You can be comparing to last year's salary, a colleague's salary, your own expectation, or a negotiation anchor simultaneously. The theory does not specify how reference points are determined — only how choices are made once they are set.

**Limitation 2: Prospect Theory is descriptive, not prescriptive.**
It describes how people *actually* choose. It does not say they *should* choose this way. When making important decisions, the goal is to *correct* for the biases Prospect Theory describes — using the expected utility framework as a normative guide — not to embrace them.

**Limitation 3: The parameters are population-level estimates.**
λ ≈ 2.25 and γ ≈ 0.65 are averages across populations. Individual loss aversion coefficients vary substantially. Do not use 2.25 as a precise individual prediction.

**Limitation 4: It doesn't handle all violations of EU.**
The Allais Paradox (Day 3, Exercise 3) is explained by Prospect Theory's probability weighting. But some anomalies — like preference reversals between bids and choices — require additional mechanisms not in the standard model.

---

## Try It Yourself

**Exercise 1 — Classify using the four-fold pattern (5 min)**

For each situation, identify which cell of the four-fold pattern applies, and predict what Prospect Theory says the person will do:

a) An entrepreneur is offered a certain $500K acquisition for their startup, or can continue for a 60% chance of $1M (40% chance of $0).

b) A PM is choosing between a certain $200K budget cut to their team (the "safe" option) or a 50% chance of a $600K budget cut if a risky bet fails.

c) A user is offered 1% chance of winning a year of premium service free vs. $5 off their next month.

d) A company faces a 1% chance of a $10M regulatory fine. They can buy insurance for $150K.

<details>
<summary>Prospect Theory predictions</summary>

a) **Gains, high probability** → risk-averse. Prospect Theory predicts taking the certain $500K. (The entrepreneur is in the gains domain, and 60% is a moderate-to-high probability where underweighting applies. The certain option dominates in utility terms.)

b) **Losses, high probability** → risk-seeking. The PM is more likely to take the risky bet than accept the certain loss. This is the "double or nothing" mentality in loss domains — empirically robust.

c) **Gains, low probability** → risk-seeking. The 1% chance is overweighted. The lottery (1% × year of service) likely feels more valuable than $5 off, even if EV is similar. This is why raffles and sweepstakes work.

d) **Losses, low probability** → risk-averse. Buy the insurance. The 1% catastrophic loss is overweighted; $150K feels like a small certain loss to avoid a terrifying possibility.

</details>

---

**Exercise 2 — Find the reference point (10 min)**

For each scenario, identify (a) what the likely reference point is and (b) how changing the reference point would change the decision:

1. A user considering upgrading from a free to a paid plan after a 30-day trial.
2. A job candidate comparing a new $130K offer against their current $110K salary.
3. A product team deciding whether to remove a feature that 15% of users actively use.

---

**Exercise 3 — Design against loss aversion (15 min, L2)**

You are redesigning the cancellation flow for a subscription product. Currently, users who try to cancel are shown:

> "Cancel subscription — you will lose access to all premium features."

Rewrite this message in three ways:
1. A version that uses loss aversion ethically (reminds of genuine value, does not manipulate)
2. A version that uses loss aversion manipulatively (exaggerates or misrepresents what is lost)
3. A neutral version (no loss framing)

For each: predict the cancellation rate effect relative to the current message, and justify using Prospect Theory.

---

## Connect It Back

Days 1–7 built the problem piece by piece: System 1 proposes; calibration fails; biases corrupt perception and evaluation. Today you got the unified descriptive model. Prospect Theory says: take everything you learned about loss aversion, framing, and diminishing sensitivity — these are not separate quirks. They are properties of a single value function and a probability weighting function that together predict choice across all four domains.

This is the last page of the "what humans actually do" arc. Days 9–13 now equip you with the *tools* for making better decisions despite these features: the value of information (Day 9), decision trees (Day 10), and frameworks for deep uncertainty (Day 13). You know the terrain. Now you get the map.

**The question you should be able to answer now:**
*Why does Prospect Theory predict that people will accept a certain loss rather than gamble on a higher potential loss — but will gamble on a low-probability catastrophic loss rather than accept a small certain payment?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Kahneman & Tversky, "Prospect Theory: An Analysis of Decision under Risk," *Econometrica*, 47(2):263–291, 1979. Read Sections I–IV (pages 263–279). This is the primary source — 16 pages of landmark science. Section II presents the experimental violations of EU; Section III introduces the value function; Section IV covers the weighting function.

**If you want the deep version:**

1. Tversky, Amos & Kahneman, Daniel. "Advances in Prospect Theory: Cumulative Representation of Uncertainty." *Journal of Risk and Uncertainty*, 5(4):297–323, 1992. — The refined "CPT" version with cumulative probability weighting that handles multi-outcome prospects correctly. Read Sections 1–3 for the key improvements.

2. Kahneman, *Thinking, Fast and Slow*, Chapters 26–28 ("Prospect Theory," "The Endowment Effect," "Bad Events"). — Kahneman's own retrospective on how the theory was built, with vivid examples and a frank account of what it cannot explain.

3. Thaler, Richard H. "Toward a Positive Theory of Consumer Choice." *Journal of Economic Behavior & Organization*, 1(1):39–60, 1980. — The foundational mental accounting paper that applies Prospect Theory to consumer behaviour. Directly relevant to pricing and subscription design. Read Sections 1–3.


---

← [Day 7 — Cognitive Biases II](day-07-cognitive-biases-ii) &nbsp;|&nbsp; [Day 9 — Value of Information →](day-09-value-of-information)
