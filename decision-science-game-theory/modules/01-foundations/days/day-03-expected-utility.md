*[Decision Science & Game Theory](../../../../README.md) · [Module 1 — The Science of Choosing](../1-overview.md) · Day 3 of 65*

# Day 3 — Expected Utility: Why $2M Isn't Twice $1M

> **Today's one idea:** Rational agents maximise expected *utility*, not expected *value* — because satisfaction is a curved function of wealth, not a straight line.
> **Reading time:** ~35 min · **Prereqs:** [Day 1](day-01-how-decisions-actually-happen.md), [Day 2](day-02-expected-value.md)
> **Primary source for today:** Tadelis, *Game Theory: An Introduction*, Chapter 2, Sections 2.3–2.5. Also: Kahneman, *Thinking, Fast and Slow*, Chapters 25–26.

---

## The Hook

Daniel Bernoulli, nephew of the man who posed the St. Petersburg Paradox, wrote a paper in 1738 that solved it — and in doing so laid the foundation for all of modern decision theory.

His insight: people do not value money in direct proportion to its amount. A poor person cares enormously about gaining $100. A billionaire barely notices. The *same dollar amount* has different value to different people — and to the same person at different wealth levels.

This seems obvious once stated. But its implications are profound. Working through them will change how you think about risk, insurance, startup equity, and user behaviour.

---

## Building the Intuition

### The curved value of money

Imagine your total wealth is currently $0 (just to keep the math clean). You are offered a choice:

- **Option A:** $500,000 for certain.
- **Option B:** Flip a fair coin. Heads: $1,000,000. Tails: $0.

Both have the same expected value: $500,000. EV says you should be indifferent. But almost everyone prefers Option A — not because they are bad at math, but because the *benefit* of going from $0 to $1M is less than twice the benefit of going from $0 to $500K.

Why? Think about what $500K actually buys you: financial security, reduced anxiety, the ability to make long-term plans. Going from $500K to $1M gives you more comfort — but not a second complete transformation of your life. The first $500K is *more valuable* than the second.

This is called **diminishing marginal utility**: each additional dollar provides less additional satisfaction than the one before it. Not because money stops mattering, but because your most pressing needs get met first.

Draw a curve of "satisfaction vs. wealth." If it were a straight line, every dollar would be worth the same. But for most people in most situations, the curve flattens as wealth grows:

```
Utility
  │
  │                          ●─────── (concave: diminishing returns)
  │                    ●────
  │              ●────
  │        ●────
  │  ●────
  │●
  └─────────────────────────────── Wealth
```

On a curve like this, the guaranteed midpoint ($500K) has *higher utility* than the average of the two extremes (average of $0 and $1M). This is what makes people prefer certainty over a fair gamble — it is called **risk aversion**, and it is not irrational. It is the correct response to how satisfaction actually scales with money.

---

### Why insurance is rational (and why EV says it shouldn't be)

Expected value says buying insurance is irrational. Here is why.

Your house is worth $400,000. There is a 0.2% chance it burns down each year. An insurer charges you $1,000/year for full coverage.

- EV of no insurance: −(0.002 × $400,000) = −$800 per year
- EV of insurance: −$1,000 per year

EV says do not insure — you lose more in premiums than in expected losses. But most rational people buy insurance anyway. Why?

Because the *satisfaction hit* of losing $400,000 — losing your home — is catastrophically worse than the satisfaction hit of losing $1,000 in premiums. You are not paying $1,000 to avoid an expected $800 loss. You are paying $1,000 to avoid a *possible catastrophe* that would devastate your life, even if its expected dollar cost is smaller.

Expected utility reconciles this: the utility cost of the premium is small; the utility cost of the catastrophe is enormous. Insurance buys you certainty at a price your utility function is willing to pay.

---

### The formal construction

**Step 1: A utility function converts outcomes into a satisfaction number.**

A utility function u(x) maps money (or any outcome) to a number representing how much you value it. The key property: u(A) > u(B) means you prefer A to B. The *shape* of the function captures your attitude toward risk.

**Step 2: Replace expected value with expected utility.**

Instead of multiplying probabilities by dollar amounts and summing, you multiply probabilities by *utility* amounts and sum:

```
EU = (probability of outcome 1 × utility of outcome 1) + (probability of outcome 2 × utility of outcome 2) + ...
```

**Step 3: The shape of u determines how you feel about risk.**

| Shape of your satisfaction curve | Risk attitude | Behaviour |
|----------------------------------|--------------|-----------|
| Bends downward (concave) | Risk-averse | Prefer certainty over a fair gamble |
| Straight line | Risk-neutral | Indifferent; expected utility equals expected value |
| Curves upward (convex) | Risk-seeking | Prefer the gamble over a sure thing |

Most people are risk-averse for moderate to large gains and losses. They are risk-seeking for small probabilities of large gains — which is why people buy lottery tickets despite negative EV. Day 8 (Prospect Theory) captures this pattern fully.

---

### The von Neumann-Morgenstern theorem — why this is not arbitrary

You might wonder: isn't the utility function just made up? Doesn't everyone have a different one?

In 1944, John von Neumann and Oskar Morgenstern proved something remarkable. **If your preferences satisfy four basic consistency conditions, then a utility function must exist** — one that you are already implicitly maximising whenever you make choices. You do not choose the utility function; your preferences reveal it.

The four conditions are:

1. **Completeness:** For any two options, you either prefer one, prefer the other, or are indifferent. You are never stuck without an opinion.
2. **Transitivity:** If you prefer A to B and B to C, you prefer A to C. No circular preferences.
3. **Continuity:** If you prefer A to C, there is some probability p where you are indifferent between B and a gamble (A with probability p, C with probability 1−p).
4. **Independence:** If you prefer A to B, mixing a third option C into both equally does not flip the preference. The background does not change your relative ranking of A and B.

The theorem says: if you satisfy these conditions, behaving as an expected utility maximiser is not an assumption — it is a *theorem*. Your preferences imply the utility function.

The practical upshot: expected utility is not just a convenient model. It is what rationality *requires*, given minimal consistency. When people violate it, they are violating one of these four conditions. Days 6–8 will catalogue exactly which ones break and how.

---

## The Formal Picture

**Expected utility formula:**

The calculation works just like expected value — but you replace dollar amounts with satisfaction scores:

```
EU = (probability of outcome 1 × satisfaction of outcome 1) + (probability of outcome 2 × satisfaction of outcome 2) + ...
```

The satisfaction score comes from your utility function — the curve that converts money into a personal value rating.

**Risk aversion:** You are risk-averse if your satisfaction curve bends downward as wealth grows. In plain terms: the satisfaction of a guaranteed amount is higher than the *average* satisfaction you'd get from a gamble with the same average payout. You prefer the sure thing.

**Common utility functions:**

Each of the following captures diminishing returns in a slightly different way. For this course, log utility is the one worth knowing.

| Function | Plain meaning | Key property |
|----------|--------------|-------------|
| Log utility | Satisfaction grows with each *doubling* of wealth, not with each dollar added — so going from £1K to £2K matters more than going from £100K to £200K | Each doubling of wealth adds the same satisfaction increment |
| Power utility | Like log utility, but you can tune how strongly diminishing returns kick in | Used in formal economics |
| Quadratic | A simple approximation that works for moderate wealth levels; breaks down at extremes | Tractable but unrealistic for very large or very small values |
| Exponential (CARA) | Your appetite for risk stays the same no matter how rich or poor you are | Risk aversion is constant regardless of wealth level |

For this course, log utility is the most important. It captures the intuition that each *doubling* of wealth provides the same increment of satisfaction — regardless of your starting level. It comes up in wealth, portfolio decisions, and career bets.

---

## Where It Breaks / What It Is Not

**Limitation 1: The utility function is unobservable.**

You cannot directly measure u(x). You can infer it from choices — but only if choices are consistent, which brings us to the next problem.

**Limitation 2: Real preferences violate the axioms.**

The independence axiom (condition 4 above) is violated systematically by real humans. This is the *Allais Paradox* (1953):

Choose between:
- **A:** $1M for certain.
- **B:** 89% chance of $1M, 10% chance of $5M, 1% chance of $0.

Most people choose A (prefer the certainty of $1M).

Now choose between:
- **C:** 11% chance of $1M, 89% chance of $0.
- **D:** 10% chance of $5M, 90% chance of $0.

Most people choose D (prefer the higher upside).

But choosing A and D together violates the independence axiom — you can prove this algebraically. This is not a thought experiment. Allais demonstrated it empirically. The violation is systematic and robust. Expected utility, as a *descriptive* model, fails here. This failure motivates Prospect Theory (Day 8).

**Limitation 3: What counts as an outcome?**

Expected utility theory is silent on whether you should measure outcomes as final wealth levels, changes from a reference point, or something else. This matters enormously in practice. Kahneman and Tversky showed (Day 8) that you evaluate outcomes as gains and losses relative to a reference point — not as final wealth states as EU assumes.

**What EU is for, precisely:**
- EU is the correct *normative* model: how a rational agent *should* decide if they have consistent preferences.
- EU is a reasonable *approximation* for small-stake decisions by sophisticated agents.
- EU is wrong as a *descriptive* model of how humans actually choose — especially under emotional arousal, for small probabilities, or when reference points are active.

---

## Try It Yourself

**Exercise 1 — Test your risk attitude (10 min)**

For each pair, which do you prefer?

a) $50 for certain vs. a 50% chance of $110, 50% chance of $0.
b) Lose $50 for certain vs. a 50% chance of losing $110, 50% chance of losing $0.
c) A 1% chance of $10,000 vs. $90 for certain.
d) A 1% chance of losing $10,000 vs. losing $90 for certain.

After answering, sketch what shape your utility function must have in the gains domain (based on a and c) and in the losses domain (based on b and d). Do they have the same shape?

<details>
<summary>What to look for</summary>

- If you chose certainty in (a): risk-averse in the gains domain (concave u).
- If you chose the gamble in (b): risk-seeking in the losses domain — this suggests a *convex* u for losses.
- If you chose the $90 certain in (c): risk-averse even at low probabilities of big gains.
- If you chose the −$90 certain in (d): risk-averse even at low probabilities of big losses.

Most people are risk-averse in gains, risk-seeking in losses, and overweight small probabilities. This asymmetric pattern is exactly what Prospect Theory (Day 8) captures.

</details>

---

**Exercise 2 — The insurance calculation (10 min)**

You are an early-stage startup founder. You have a 70% chance your company is worth $2M in 2 years, and a 30% chance it is worth $0 (the most common startup outcome).

An acquirer offers to buy you out now for $800K.

Assume your utility function is u(x) = √x — the square root of your wealth. This is a concave (downward-bending) curve: it means each extra dollar matters less as your wealth grows.

a) What is the EV of waiting?
b) What is the EU of waiting?
c) What is the EU of taking the $800K?
d) Which does EU say to prefer? Does this match your intuition?

<details>
<summary>Worked answer</summary>

**a)** EV = (0.70 × $2M) + (0.30 × $0) = **$1,400,000**

**b)** EU(wait) = (0.70 × √2,000,000) + (0.30 × √0)
= 0.70 × 1414.2 + 0.30 × 0
= **989.9**

**c)** EU(sell) = √800,000 = **894.4**

**d)** EU(wait) = 989.9 > EU(sell) = 894.4.

EU says **wait**. With √x utility, the founder is still better off waiting despite the risk, because the upside ($2M) is large enough. But notice: if the utility function were more concave (more risk-averse) — say u(x) = ln(x) — the EU of waiting might fall below the EU of selling, justifying the acquisition.

This is why founders with dependents, large mortgages, or other obligations (more risk-averse) are more likely to take early acquisitions than solo founders with no financial pressure.

</details>

---

**Exercise 3 — The Allais Paradox (stretch, L2)**

Prove algebraically that choosing A (certainty of $1M) in the first problem and D (10% chance of $5M, 90% chance of $0) in the second problem violates the independence axiom.

<details>
<summary>Hint</summary>

Rewrite both choice problems using a common structure: a lottery that involves a third outcome with probability 0.89. Show that if you prefer A to B, the independence axiom forces you to prefer C to D.

</details>

<details>
<summary>Worked answer</summary>

Rewrite the first problem:
- A: 11% chance of $1M + 89% chance of $1M = 100% $1M (trivially)
- B: 10% chance of $5M + 1% chance of $0 + 89% chance of $1M

Rewrite the second problem:
- C: 11% chance of $1M + 89% chance of $0
- D: 10% chance of $5M + 1% chance of $0 + 89% chance of $0

Notice: A vs B differ only in the "11%" component (A gives $1M there; B gives a 10/11 chance of $5M and 1/11 chance of $0). C vs D have the *same 11% component* replaced with the same options — and the 89% is $0 in both.

By the independence axiom, if you prefer the 11% component of A over the 11% component of B (which is what A > B implies), then you must prefer C over D (where exactly the same 11% components appear, mixed with identical 89% zero payoffs).

Choosing A and D simultaneously means: you prefer A's 11% component in one framing, but D's 11% component in another. The only thing that changed is the background (89% of $1M vs 89% of $0). The independence axiom says the background shouldn't matter. It does — which is why people systematically violate EU.

</details>

---

## Connect It Back

Yesterday you built the rational baseline: expected value. Today you fixed its most important flaw — it treats all dollars as equal regardless of context. Expected utility fixes this by curving the value function. Risk aversion is not irrationality. It is the correct response to diminishing marginal utility.

But notice what you discovered in Exercise 1: your utility function is probably *not* the same in the gains domain as in the losses domain. Most people are risk-averse for gains and risk-seeking for losses. And you may have chosen the $90 certainty in (c) despite 1% × $10,000 = $100 EV — suggesting you overweight small probabilities.

Neither EU theory nor diminishing marginal utility fully explains this pattern. Tomorrow (Day 4) and Day 5 build the probability toolkit you need. Then Days 6–8 construct the complete picture: **Prospect Theory**, which replaces EU as the descriptive model of choice.

**The question you should be able to answer now:**
*Why does a risk-averse agent prefer a certain $500K over a 50/50 gamble between $1M and $0 — even though the gamble has twice the expected value?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Tadelis, *Game Theory: An Introduction*, Sections 2.3–2.5 (pp. ~30–55). The cleanest formal derivation of expected utility theory in a textbook aimed at this level. Read it alongside today's formal section.

**If you want the deep version:**

1. von Neumann, John & Morgenstern, Oskar. *Theory of Games and Economic Behavior*. Princeton University Press, 1944. Chapter 3, Section 3.3 — The original axiomatisation. Historical reading; the notation is dated but the ideas are irreplaceable. Read Section 3.3.1–3.3.4 (~10 pages).

2. Kahneman, *Thinking, Fast and Slow*, Chapter 26 ("Prospect Theory"). — Kahneman's own account of how he and Tversky overturned EU as a descriptive model. Best read after Day 8 as a retrospective, but worth previewing now to see where we're going.

3. Allais, Maurice. "Le comportement de l'homme rationnel devant le risque: critique des postulats et axiomes de l'école américaine." *Econometrica*, 21(4):503–546, 1953. — The original Allais Paradox paper. Very long; read only the first 10 pages to see the paradox stated in its original form. In French, but the formal sections are legible without French fluency.


---

← [Day 2 — Expected Value](day-02-expected-value) &nbsp;|&nbsp; [Day 4 — Probability & Calibration →](day-04-probability-calibration)
