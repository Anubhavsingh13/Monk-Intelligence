*[Decision Science & Game Theory](../../../../README.md) · [Module 1 — The Science of Choosing](../1-overview.md) · Day 2 of 65*

# Day 2 — Expected Value: The Rational Baseline

> **Today's one idea:** The "correct" choice under uncertainty is the one that maximises the probability-weighted average outcome — and this rule is more counterintuitive than it sounds.
> **Reading time:** ~35 min · **Prereqs:** [Day 1](day-01-how-decisions-actually-happen.md)
> **Primary source for today:** Kahneman, *Thinking, Fast and Slow*, Chapter 25 ("Bernoulli's Errors"). Also: Tadelis, *Game Theory: An Introduction*, Chapter 2 (decision-making under risk).

---

## The Hook

It is 1654. The Chevalier de Méré, a French nobleman with a gambling problem, has noticed something strange. He has been winning consistently by betting that a 6 will appear at least once in four rolls of a die. But a similar bet — that double sixes appear at least once in 24 rolls of two dice — is losing him money. Both bets feel roughly equivalent. Why is one profitable and one not?

He writes to his friend Blaise Pascal. Pascal writes to Pierre de Fermat. The two mathematicians exchange a series of letters that effectively invent probability theory.

At the centre of their exchange is a single idea that every subsequent decision theory rests on: **when outcomes are uncertain, the rational way to value a gamble is to weight each outcome by its probability and sum.** That is expected value.

Before we critique it (Day 3 will show it is incomplete as a model of human choice), you need to understand it well enough to use it — and to know when it fails.

---

## Building the Intuition

Start with the simplest possible gamble. A fair coin: Heads you win $10, Tails you win $0. What is this gamble *worth*?

Half the time you get $10, half the time you get $0. On average, you get $5. The gamble is worth $5.

That calculation — multiply each outcome by its probability, then add them up — is expected value (EV).

```
EV = (0.5 × $10) + (0.5 × $0) = $5
```

Now, the Chevalier de Méré's puzzle.

**Bet 1:** Roll one die four times. Win if at least one 6 appears.
- P(no 6 in one roll) = 5/6
- P(no 6 in four rolls) = (5/6)⁴ = 625/1296 ≈ 0.4823
- P(at least one 6) = 1 − 0.4823 = **0.5177**

The probability of winning is slightly above 50%. With a fair bet (win $1, lose $1):

```
EV = (0.5177 × $1) + (0.4823 × −$1) = +$0.035
```

Positive — a winning bet. De Méré was right to play it.

**Bet 2:** Roll two dice 24 times. Win if at least one double-6 appears.
- P(no double-6 in one roll) = 35/36
- P(no double-6 in 24 rolls) = (35/36)²⁴ ≈ 0.5086
- P(at least one double-6) = 1 − 0.5086 = **0.4914**

Probability of winning is slightly *below* 50%.

```
EV = (0.4914 × $1) + (0.5086 × −$1) = −$0.017
```

Negative — a losing bet. By a small margin, but over thousands of plays the house wins. De Méré's instinct that the bets were "equivalent" was wrong by exactly this margin.

**The lesson:** EV turns vague intuitions about "roughly equivalent" into precise comparisons. Small differences in probability compound into large differences in long-run outcome.

---

### EV in AI product decisions

Expected value is not just a gambling tool. Every A/B test decision is an EV calculation. Every feature prioritisation is an EV comparison. Every model deployment involves uncertain outcomes weighted by probabilities.

Here is a concrete product example.

You are deciding whether to ship a recommendation algorithm change. Your best estimates:
- 60% chance it increases 7-day retention by 2 percentage points (worth $400K/month in revenue)
- 30% chance it has no effect
- 10% chance it causes a spike in user complaints that takes 2 weeks to diagnose and fix (costs $200K in eng time + reputational damage)

```
EV = (0.60 × +$400K) + (0.30 × $0) + (0.10 × −$200K)
   = $240K + $0 − $20K
   = +$220K/month
```

Ship it. But notice what this calculation forces you to do: **explicitly state your probabilities and outcomes**. Most product decisions skip this step — they argue about the feature in vague terms. EV forces precision.

---

## The Formal Picture

For a decision with multiple possible outcomes, each with a probability and a value, the expected value is:

```
EV = (probability of outcome 1 × value 1) + (probability of outcome 2 × value 2) + ...
```

You will only need the more advanced continuous version on Day 9 (Value of Information). The version above is sufficient for everything through Day 8.

---

**The law of large numbers — why EV matters**

EV is most meaningful as a long-run average. If you play a gamble with EV = $5 once, you might win $10 or $0. If you play it 10,000 times, your average payoff per play will be close to $5 — and the gap from $5 shrinks as the number of plays grows.

**The critical implication:** EV reasoning is most reliable when you will face *many similar decisions* — not when you face a decision once. A venture capital firm making 100 investments should maximise EV. An individual making one career-bet of their life cannot simply apply EV mechanics, because repeating the situation thousands of times is not an option. This distinction will be crucial on Day 3 and Day 13.

---

**Computing EV with a decision table**

For more than two outcomes, a table keeps things organised:

| Outcome | Probability | Value | p × V |
|---------|------------|-------|-------|
| Great   | 0.25       | +$100 | $25   |
| OK      | 0.50       | +$20  | $10   |
| Bad     | 0.20       | −$40  | −$8   |
| Catastrophic | 0.05  | −$200 | −$10  |
| **Total** | **1.00** |       | **$17** |

EV = $17. Whether to take this gamble depends on your alternative — and on whether EV is even the right model, which is tomorrow's question.

---

## Where It Breaks / What It Is Not

**Limitation 1: EV ignores variance.**

Two gambles can have identical EVs but wildly different risk profiles.
- Gamble A: Win $50,000 with probability 1. EV = $50,000.
- Gamble B: Win $100,000 with probability 0.5, $0 with probability 0.5. EV = $50,000.

EV says these are equivalent. But if you need exactly $40,000 to pay your mortgage this month, Gamble B is potentially catastrophic. EV as a sole criterion ignores what happens to you on the bad path.

**Limitation 2: The St. Petersburg Paradox.**

Consider this gamble: flip a fair coin repeatedly until Heads appears. Win $2ⁿ where n is the number of flips.
- Heads on flip 1: win $2. Probability = 1/2.
- Heads on flip 2: win $4. Probability = 1/4.
- Heads on flip n: win $2ⁿ. Probability = 1/2ⁿ.

The expected value of this game is infinite. Yet no sane person would pay more than ~$20 to play. EV fails here because the huge but astronomically improbable payoffs carry too much weight. This paradox — posed by Nicolas Bernoulli in 1713 — motivated the invention of utility theory, which is Day 3's subject.

**Limitation 3: EV requires calibrated probabilities.**

Expected value is only as good as your probability estimates. If your 60% is actually 30%, your EV calculation is wrong. We will address this directly on Days 4–5 (probability calibration and Bayesian updating). For now: treat EV calculations as only as reliable as the probability estimates that feed them.

**Limitation 4: EV describes the rational model, not actual human behaviour.**

Humans do not maximise expected value — they systematically deviate from it in predictable ways (Days 6–8). This is not a reason to abandon EV. It is a reason to use it as a *prescription* for how to decide and as a *diagnostic* for spotting your own biases.

---

## Try It Yourself

**Exercise 1 — Chevalier's third bet (5 min)**

A new bet: roll one die six times. Win if at least one 6 appears.
- What is the probability of winning?
- Is this a positive EV bet if you win $1 and lose $1?

<details>
<summary>Worked answer</summary>

P(no 6 in 6 rolls) = (5/6)⁶ ≈ 0.335

P(at least one 6) = 1 − 0.335 = **0.665**

EV = (0.665 × $1) + (0.335 × −$1) = **+$0.33 per play**

Yes, strongly positive. A much better bet than the original four-roll bet.

</details>

---

**Exercise 2 — The feature launch (10 min)**

You are a PM deciding whether to launch Feature X. Your engineering team says:
- 50% chance the feature ships on time in 3 weeks with full quality
- 35% chance it ships in 5 weeks with a known performance regression you'd need to patch
- 15% chance a dependency fails and the feature needs a full redesign (add 8 weeks)

Business values (revenue impact, discounted for delay):
- On-time, full quality: +$300K
- 5-week, regression: +$150K
- 8-week redesign: −$50K (sunk costs + delayed opportunity)

a) What is the EV of launching now?
b) Your manager offers an alternative: pay $30K for a 2-week technical spike that would reduce the 15% redesign risk to 5% (the remaining 10% shifts to the 5-week scenario). Should you take it?

<details>
<summary>Worked answer</summary>

**a)** EV(launch now) = (0.50 × $300K) + (0.35 × $150K) + (0.15 × −$50K)
= $150K + $52.5K − $7.5K = **$195K**

**b)** With the spike, the new distribution is:
- 50% on-time: +$300K
- 45% 5-week regression: +$150K (35% original + 10% shifted from redesign)
- 5% redesign: −$50K

EV(with spike) = (0.50 × $300K) + (0.45 × $150K) + (0.05 × −$50K)
= $150K + $67.5K − $2.5K = $215K

Cost of spike = $30K.
Net EV(with spike) = $215K − $30K = **$185K**

$185K < $195K, so **do not take the spike**. It costs more than the risk reduction is worth in EV terms.

Note: This changes if the catastrophic −$50K scenario involves reputational damage, team morale costs, or other effects not captured in the simple financial model.

</details>

---

**Exercise 3 — The St. Petersburg Paradox (stretch, L2)**

The paradox has an EV of infinity, yet you wouldn't pay more than ~$20.

a) What is the EV of the game if it is capped at a maximum payout of $1 million (i.e., the game stops if the payout exceeds $1M)?

b) Now try to articulate, in your own words, *why* EV fails as a decision criterion here, before you read Day 3. What is missing from the EV framework?

<details>
<summary>Hint for b)</summary>

Think about the difference between winning $1M and winning $2M. Is the second million worth as much to you as the first? What does that suggest about how you value money?

</details>

---

## Connect It Back

Yesterday you learned that most decisions feel like System 2's work but are often System 1's. Today you built System 2's primary tool: a precise, computable way to evaluate uncertain choices. Expected value is the rational baseline — the answer to "what *should* a rational person do?"

But notice what we brushed past: two gambles with identical expected values can be very different propositions depending on your financial situation, your tolerance for risk, and — crucially — the shape of your relationship to money. A gain of $100 is not twice as valuable to most people as a gain of $50. The EV model assumes it is.

Tomorrow we fix this. The fix is **utility theory** — and it will explain why buying insurance is rational, why startups take asymmetric bets, and why the St. Petersburg Paradox disappears once you stop measuring value in dollars.

**The question you should be able to answer now:**
*Why is EV most reliable as a decision rule when you face many similar decisions, and potentially misleading when you face a one-time, high-stakes choice?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Kahneman, *Thinking, Fast and Slow*, Chapter 25 ("Bernoulli's Errors"), pp. 270–278. Kahneman explains why EV fails as a descriptive model of human choice — the essential bridge to tomorrow's Day 3. Read this after completing today's exercises, not before.

**If you want the deep version:**

1. Tadelis, *Game Theory: An Introduction*, Chapter 2, Sections 2.1–2.2. — Formal treatment of lotteries and expected utility from a game-theory perspective. Clean notation; recommended for L2 readers building toward the game theory modules.

2. Hacking, Ian. *An Introduction to Probability and Inductive Logic*. Cambridge University Press, 2001. Chapters 3–4. — A beautifully accessible treatment of probability basics, including the frequentist and subjective interpretations. Recommended before Day 4 if your probability intuition feels shaky.

3. The original Chevalier de Méré problem is reconstructed in: David, F.N. *Games, Gods and Gambling: The Origins and History of Probability and Statistical Ideas*. Griffin, 1962. — For the historically curious; not required for the course.


---

← [Day 1 — How Decisions Actually Happen](day-01-how-decisions-actually-happen) &nbsp;|&nbsp; [Day 3 — Expected Utility →](day-03-expected-utility)
