*[Decision Science & Game Theory](../../../../README.md) · [Module 1 — The Science of Choosing](../1-overview.md) · Day 4 of 65*

# Day 4 — Probability & Calibration

> **Today's one idea:** A well-calibrated forecaster assigns probabilities that match actual frequencies — most people are systematically overconfident, and calibration is a learnable skill.
> **Reading time:** ~35 min · **Prereqs:** [Day 1](day-01-how-decisions-actually-happen.md), [Day 2](day-02-expected-value.md)
> **Primary source for today:** Tetlock, Philip & Gardner, Dan. *Superforecasting: The Art and Science of Prediction*. Crown, 2015. Chapters 1–3. Also: Kahneman, *Thinking, Fast and Slow*, Chapter 14 ("Tom W's Specialty").

---

## The Hook

During the Cold War, the US intelligence community employed thousands of analysts whose job was to forecast geopolitical events: Would the Soviet Union invade Poland? Would oil prices spike? Would this regime fall?

Philip Tetlock — a psychologist at Penn — spent 20 years systematically tracking these forecasts against outcomes. His finding, published in 2005, was devastating: expert political forecasters were barely better than chance. They were overconfident, resistant to updating, and prone to fitting narratives to whatever happened.

But buried in the data was something else: a small subset of forecasters were genuinely good. Significantly better than chance. Better than CIA analysts with access to classified information. What made them different was not expertise in any particular domain. It was a set of habits: they broke problems down, updated beliefs when evidence arrived, expressed genuine uncertainty in numbers rather than words, and tracked their own track record obsessively.

Tetlock called them **superforecasters**. And the most important skill they shared was calibration.

---

## Building the Intuition

### What calibration means

Suppose you answer 100 trivia questions and for each one, you say "I'm 70% confident this answer is correct." If you are perfectly calibrated, exactly 70 of those 100 answers should be right.

If only 50 are right, you are *overconfident* — your 70% confidence corresponds to only 50% accuracy.
If 85 are right, you are *underconfident*.

Most people are overconfident. Experiments consistently show that when people say they are "90% sure," they are right about 70–75% of the time. When they say something is "certain," they are wrong 20–30% of the time.

Here is a calibration curve for a typical overconfident forecaster vs. a well-calibrated one:

```
Actual
accuracy
100% │                                    ● Perfect calibration
     │                              ●────
 80% │                        ●────      ╲
     │                  ●────             ╲ Overconfident
 60% │            ●────                    ╲forecaster
     │      ●────                           ●────
 40% │ ●────                                     ●────
     │                                                ●────●
  0% └────────────────────────────────────────────────────────
     0%      20%     40%     60%     80%    100%
                    Stated confidence
```

The 45-degree line is perfect calibration. An overconfident forecaster's curve sits below and to the right — they claim high confidence in events that turn out to be less certain.

---

### The two kinds of uncertainty

Before calibration can be trained, you need to distinguish two fundamentally different types of uncertainty:

**Aleatoric uncertainty** (from Latin *alea* — die) — irreducible randomness. Even if you knew everything knowable, a fair die would still land 1 through 6 with equal probability. No amount of additional information reduces this uncertainty.

**Epistemic uncertainty** — uncertainty due to lack of knowledge. In principle, more information could reduce it. "Will it rain tomorrow?" has a true answer — it either will or it won't. Your uncertainty is about which.

This distinction matters because:
1. Calibration is only meaningful for epistemic uncertainty — for aleatoric uncertainty, the "right" probability is determined by physics, not your knowledge.
2. Many business and life decisions involve primarily epistemic uncertainty — you don't know competitor plans, user behaviour, or regulatory outcomes. This means calibration is trainable and improvable.

---

### How probability actually works

Three interpretations of probability exist, and you need to know which one you are using:

**Frequentist probability:** The long-run frequency of an event in a series of identical trials. P(Heads) = 0.5 because in infinitely many coin flips, half will be Heads. This is mathematically clean but limited — you cannot apply it to one-off events ("probability the EU breaks up in 5 years") because there is no reference class of EU break-ups.

**Bayesian/subjective probability:** Your degree of belief that a proposition is true. Expressed as a number between 0 and 1, reflecting your state of knowledge. This is what superforecasters use. It accommodates one-off events, is updated when evidence arrives, and is the interpretation underpinning all of Bayesian reasoning (Day 5).

**Logical probability:** A probability assigned by pure logic given certain evidence — a less common view. Not needed for this course.

**For this course, use Bayesian probability.** When you say "there's a 30% chance this feature will fail in production," you are expressing a degree of belief based on your current knowledge — not a frequency from 100 identical launches. This is the right interpretation and the one consistent with Bayesian updating (Day 5).

---

### Common calibration errors

**Overconfidence (the most common):** Stated confidence exceeds accuracy. Driven by:
- Confirmation bias: you notice evidence that supports your belief, discount evidence against it
- Narrative fluency: a coherent story feels more probable than it is
- Inside view: focusing on the specific case rather than the base rate of similar cases

**Scope insensitivity:** People's stated probability barely changes when the magnitude of a scenario changes. "Probability of a major earthquake in California in the next year" vs. "in the next 10 years" — people give surprisingly similar numbers despite a tenfold difference in the time window.

**Conjunction fallacy:** Estimating that two events together are more probable than one alone. A famous demonstration from Kahneman and Tversky:

> Linda is 31, single, outspoken, and very bright. She majored in philosophy. As a student, she was deeply concerned with issues of discrimination and social justice. 
>
> Which is more probable?
> A) Linda is a bank teller.
> B) Linda is a bank teller who is active in the feminist movement.

85% of people in early studies chose B. But the probability of two things both being true can never be higher than the probability of either one alone. Linda being a feminist bank teller requires her to be a bank teller — so it cannot be more likely. The description makes B feel more *representative* of Linda, which System 1 converts into "more probable." This is pure calibration failure.

---

## The Formal Picture

**Calibration defined:** A forecaster is perfectly calibrated if, for every confidence level they use, the fraction of events that actually happen matches that confidence level. If you say "70% confident" about 100 different things, roughly 70 of them should come true — not 50, not 90. If you said "90% confident" about another batch and only 60 came true, you are overconfident.

**Brier score** — the standard metric for grading probabilistic forecasts:

For a forecast *f* (a number from 0 to 1) and a binary outcome *o* (either 0 = didn't happen, or 1 = did happen):

```
Brier = (f − o)²
```

Lower Brier score = better. Perfect forecast: 0. Random (always 0.5): 0.25. Always wrong: 1.0.

The Brier score penalises both overconfidence (saying 0.9 when wrong) and poor resolution (always saying 0.5 rather than committing). The skill is in being both accurate and confident when accuracy is achievable.

**Resolution vs. calibration:**

These are distinct dimensions of forecasting quality:
- **Calibration:** When you say 70%, it happens 70% of the time.
- **Resolution:** You distinguish between 70% events and 90% events — your probabilities *vary* with actual probability.

A forecaster who always says "50%" is technically well-calibrated for the average event, but has zero resolution — they convey no information. Good forecasting requires both.

---

### Reference class forecasting

The most reliable tool for improving calibration on one-off events is to find the **reference class** — the set of similar past events — and start from its base rate, then adjust for specific factors.

**Example:** You are launching a new B2B SaaS feature and want to estimate the probability it becomes a top-3 revenue driver within 12 months.

*Inside view (wrong starting point):* "Our team is strong, the market need is clear, we've been iterating on this for 6 months. I'd say 60%."

*Outside view / reference class:* How often do new enterprise SaaS features become top-3 revenue drivers within 12 months across the industry? If the base rate is 15%, your starting point should be 15%. Then you adjust up or down based on specific factors (your team's track record, the size of the opportunity, competitive dynamics).

The outside view consistently produces better-calibrated forecasts than the inside view. The inside view feels more relevant — it is your specific situation. But it systematically ignores the denominator: how often do situations like yours actually succeed.

---

## Where It Breaks / What It Is Not

**Calibration is not the same as accuracy.** A forecaster who says "50% chance of rain" every day and a forecaster who says "80% on rainy days, 10% on sunny days" can have the same calibration but very different resolution. Accuracy (Brier score) captures both.

**Calibration is not certainty.** A well-calibrated forecaster who says "30% chance" is not wrong if the event happens. Being calibrated means: among all the times you said 30%, roughly 30% of those events occurred. Individual predictions carry irreducible uncertainty.

**Confidence intervals are systematically too narrow.** A classic calibration exercise: answer 10 factual questions with a 90% confidence interval (you are 90% sure the true answer falls in your range). Most people are right only 50–60% of the time. Their intervals are too narrow. This is called *interval overconfidence* — different from probability overconfidence but equally pervasive.

**"I don't know" is not a probability.** Epistemic humility does not mean refusing to assign a number. The right response to genuine uncertainty is "40%," not "I can't know." A number forces intellectual honesty — you must commit, you can be measured, you can improve. Refusing to number-ise is often a way to avoid being wrong.

---

## Try It Yourself

**Exercise 1 — Your own calibration (15 min)**

Answer each question with your best estimate **and** a 90% confidence interval (you're 90% sure the true answer is in your range):

a) Distance in km from Delhi to Mumbai (straight line)
b) Year the first iPhone was released
c) Global daily active users of YouTube (as of 2024, approximate)
d) Number of bones in the adult human body
e) Year the Berlin Wall fell
f) Area of Antarctica in km²
g) Speed of light in km/s (round number)
h) Year Amazon was founded
i) Population of Brazil (2023 estimate)
j) Number of episodes in the original Star Trek series

After looking up the answers: how many of your 10 confidence intervals contained the true answer? A well-calibrated person aiming for 90% intervals should get 9 out of 10. If you got 6 or fewer, your intervals are too narrow — you are overconfident.

*(Answers available via web search — look them up after completing your estimates, not before.)*

---

**Exercise 2 — Reference class forecasting (10 min)**

You are a product manager deciding whether to prioritise a new AI-powered search feature. Before giving your inside-view estimate of success, find your reference class:

1. What is the *category* of this initiative? (e.g., "AI-powered search improvements in mid-market SaaS products")
2. What data exists on similar initiatives at similar companies? (Even rough industry data is better than none.)
3. What is the base rate of success for this class?
4. What specific factors about your situation adjust you above or below the base rate?
5. What is your calibrated estimate?

Write this out. Notice how it changes (or doesn't) from your original intuition.

---

**Exercise 3 — Conjunction probabilities (5 min)**

For each pair, decide which is more probable *without calculating*, then verify your intuition:

a) "Our next feature ships on time" vs. "Our next feature ships on time AND receives positive user feedback"

b) "The model improves accuracy" vs. "The model improves accuracy AND doesn't increase latency"

c) For your current project: pick any single-condition success criterion and a conjunction of two conditions. Estimate both probabilities. Does the two-condition probability stay lower than the single-condition one? It must logically — two things both being true can never be more likely than just one of them being true.

---

## Connect It Back

Days 2–3 gave you the machinery for computing rational choices given probabilities. Today's question is: where do those probabilities come from, and how do you know if you can trust them?

The answer is: most of the time, your initial probability estimates are overconfident and driven by inside-view thinking. Reference class forecasting and tracking your calibration over time are the correctives. Tomorrow (Day 5) you will learn the formal machinery for *updating* these probabilities as new evidence arrives — Bayesian reasoning. Calibration is the starting state; Bayesian updating is how you move from there.

**The question you should be able to answer now:**
*What is the difference between being wrong about a 70% prediction and being miscalibrated? Can a well-calibrated forecaster be wrong?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Tetlock & Gardner, *Superforecasting*, Chapter 2 ("Illusions of Knowledge") and Chapter 3 ("Keeping Score"). Chapter 2 diagnoses overconfidence; Chapter 3 explains the Brier score and what it means to be measurably good at prediction.

**If you want the deep version:**

1. Kahneman, *Thinking, Fast and Slow*, Chapter 14 ("Tom W's Specialty") and Chapter 15 ("Linda: Less Is More"). — The classic demonstrations of representativeness heuristic and conjunction fallacy. Short chapters, high-density insight.

2. Lichtenstein, Sarah et al. "Calibration of probabilities: The state of the art to 1980." In Kahneman, Slovic & Tversky (eds.), *Judgment Under Uncertainty: Heuristics and Biases*. Cambridge University Press, 1982. — The foundational empirical paper establishing human overconfidence. Read Sections 1–3.

3. **Metaculus** (`metaculus.com`) and **Manifold Markets** (`manifold.markets`) — Real-time forecasting platforms where you can track your calibration score across hundreds of questions. Best "deliberate practice" for calibration available. Register and make 10 predictions today.


---

← [Day 3 — Expected Utility](day-03-expected-utility) &nbsp;|&nbsp; [Day 5 — Bayesian Updating →](day-05-bayesian-updating)
