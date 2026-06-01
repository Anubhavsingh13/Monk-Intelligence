*[Decision Science & Game Theory](../../../../README.md) · [Module 1 — The Science of Choosing](../1-overview.md) · Day 5 of 65*

# Day 5 — Bayesian Updating: Beliefs as Living Things

> **Today's one idea:** New evidence should shift your starting belief in exact proportion to how much more likely it makes the evidence — and this rule is derivable from first principles, not a preference.
> **Reading time:** ~35 min · **Prereqs:** [Day 2](day-02-expected-value.md), [Day 4](day-04-probability-calibration.md)
> **Primary source for today:** Kahneman, *Thinking, Fast and Slow*, Chapters 14–16. Also: Any introductory Bayesian statistics text — recommended: McElreath, Richard. *Statistical Rethinking*. CRC Press, 2020. Chapter 2.

---

## The Hook

The year is 1763. Thomas Bayes, an English minister, has just died. Among his papers his friend Richard Price finds a manuscript: an attempt to solve a puzzle that had nagged Bayes for years.

The puzzle: You have a billiard table. A ball has been placed at a random position. You can't see the table — but a friend rolls another ball and tells you whether it landed to the left or right of the first ball. Each roll is a piece of evidence. How do you update your estimate of where the first ball is?

The solution Bayes derived is now one of the most important equations in science. It's used in everything from spam filters to medical diagnosis to how the Large Hadron Collider finds new particles.

More importantly for this course: it describes precisely how a rational person should change their mind.

---

## Building the Intuition

### A detective's method

Forget equations for a moment. Think about how a good detective reasons.

Sherlock Holmes arrives at a crime scene. Before he sees any evidence, he has a rough starting belief about what happened. It's based on how common different crimes are in London, the neighbourhood, the victim's profile. Call it a rough distribution over possible explanations.

Then he sees a clue. A muddy footprint. A burned letter. The position of the body. For each clue, Holmes asks: *"How much more likely would I see this if Theory A were true, compared to Theory B?"*

If the clue is three times more likely under Theory A, Holmes shifts his probability three times more toward Theory A. If the clue is equally likely under both theories, it tells him nothing — he doesn't move.

This is Bayesian reasoning. Evidence updates beliefs in proportion to its *diagnostic value* — how much it separates one hypothesis from another.

---

### The medical test example (most important example in the course)

A medical test for a rare disease has these properties:
- If you have the disease, the test is positive 99% of the time (sensitivity = 99%)
- If you don't have the disease, the test is positive 1% of the time (false positive rate = 1%)
- The disease affects 1 in 1,000 people (prevalence = 0.1%)

You take the test. It comes back positive. What is the probability you actually have the disease?

**Gut answer:** "99% accurate test, positive result — I almost certainly have it. Around 99%."

**Correct answer:** Let's count people out of 100,000:

```
People with the disease:     100   (0.1% of 100,000)
People without the disease:  99,900

Among the 100 with disease:
  Test positive: 99   (99% sensitivity)
  Test negative: 1

Among the 99,900 without disease:
  Test positive: 999   (1% false positive rate)
  Test negative: 98,901

Total test positives: 99 + 999 = 1,098
True positives among them: 99

P(disease | positive test) = 99 / 1,098 ≈ 9%
```

**You have approximately a 9% chance of actually having the disease — not 99%.**

This is not an unusual situation. It happens with any rare condition and an imperfect test. The reason most people get this badly wrong is that gut thinking focuses on the test's accuracy and ignores how rare the disease actually is. The rarity (0.1%) is your *starting belief* (called the prior). The test result is the *evidence*. Bayes' theorem correctly integrates both.

---

### The formula, derived from a picture

Draw two things: the total population, and what the test does to it.

```
      All people (100,000)
      ┌────────────────────────────────────────┐
      │                                        │
   Sick (100)                         Healthy (99,900)
      │                                        │
  ┌───┴───┐                              ┌─────┴─────┐
Test+  Test-                         Test+      Test-
  99     1                             999       98,901
```

We want: P(Sick | Test+) = P(Sick AND Test+) / P(Test+)
= 99 / (99 + 999)
= 99 / 1098
≈ 0.09

This is Bayes' theorem in visual form. The formula just formalises this rectangle-within-rectangle reasoning.

---

## The Formal Picture

Here is the key formula. It has one moving part: it updates your starting belief using how diagnostic the evidence is.

```
P(H | E) = P(E | H) · P(H)
           ─────────────────
                P(E)
```

Where:
- **H** = the hypothesis you're testing (e.g., "patient has the disease")
- **E** = the evidence you just saw (e.g., "test came back positive")
- **P(H)** = your *starting belief* — how likely H was before you saw E. This is called the **prior**.
- **P(E | H)** = how likely you'd see E *if H were true* — read this as "the probability of E, given that H is true." This is called the **likelihood**.
- **P(E)** = how likely E is overall, regardless of H. This is called the **marginal likelihood**.
- **P(H | E)** = your *updated belief* — how likely H is now that you've seen E. This is called the **posterior**.

The bottom of the fraction expands to:

```
P(E) = P(E | H) · P(H)  +  P(E | not-H) · P(not-H)
```

In plain English: the overall chance of a positive test equals (the chance among sick people × how common sickness is) plus (the chance among healthy people × how common health is).

**In the medical example:**
- P(H) = 0.001 (prior: how rare the disease is)
- P(E | H) = 0.99 (sensitivity)
- P(E | not-H) = 0.01 (false positive rate — how often the test is positive in healthy people)

```
P(H | E) = (0.99 × 0.001)
           ─────────────────────────────────────────
           (0.99 × 0.001) + (0.01 × 0.999)

         = 0.00099 / 0.01098
         ≈ 0.09
```

Exactly matching our count.

---

### The odds form (easier to use in practice)

There is a cleaner way to do this that avoids the messy denominator. Convert your probability to *odds*:

```
Odds(H) = P(H) / P(¬H)
```

Then the update rule becomes:

```
Posterior odds = Prior odds × Likelihood ratio
```

The *likelihood ratio* is how much more likely the evidence is if H is true versus if H is false: P(E | H) / P(E | not-H).

**Medical example:**
- Prior odds = 0.001 / 0.999 ≈ 0.001 (roughly 1-in-1000)
- Likelihood ratio = 0.99 / 0.01 = 99
- Posterior odds = 0.001 × 99 = 0.099
- Posterior probability = 0.099 / (1 + 0.099) ≈ 0.09

The likelihood ratio is your *update multiplier*. A ratio of 99 means the positive test makes the disease hypothesis 99 times more likely than before. But 99× a tiny number is still a small number — starting at 1/1000 and multiplying by 99 only gets you to about 9%.

This makes something important clear: **the strength of evidence is not your final probability — it is the likelihood ratio.** Strong evidence on a rare hypothesis still gives you a moderate answer.

---

### Sequential updating

A beautiful property of this framework: you can update in stages. Yesterday's updated belief becomes today's starting belief.

**Example:** Three witnesses independently say they saw a suspect running from a scene. Each witness is 80% reliable (P(says yes | guilty) = 0.8, P(says yes | innocent) = 0.2). Your starting probability of guilt is 10%.

```
After witness 1:
  Prior odds = 0.1/0.9 ≈ 0.111
  Likelihood ratio = 0.8/0.2 = 4
  Posterior odds = 0.111 × 4 = 0.444
  P(guilty) ≈ 0.31

After witness 2:
  Prior odds = 0.31/0.69 ≈ 0.449
  Likelihood ratio = 4
  Posterior odds = 0.449 × 4 = 1.80
  P(guilty) ≈ 0.64

After witness 3:
  Prior odds = 0.64/0.36 ≈ 1.78
  Likelihood ratio = 4
  Posterior odds = 1.78 × 4 = 7.11
  P(guilty) ≈ 0.88
```

Three independent witnesses — each 80% reliable — move the probability from 10% to 88%. This sequential property is why Bayesian updating is the natural framework for any learning system, including AI models trained on data.

---

### Bayesian reasoning in AI product decisions

This is the framework underpinning:

**A/B testing (Day 54):** Your starting belief is P(variant B is better). Evidence from the experiment updates it. A Bayesian A/B test gives you the probability that B beats A — not just whether p < 0.05.

**Recommendation systems (Day 50):** A recommender's model of your preferences is a starting belief. Each click, skip, or purchase updates it. The system is literally running these updates on your user model.

**Spam filters:** P(spam | email contains "Nigerian prince") is a Bayesian updated probability. Naive Bayes classifiers — which power many spam filters — are direct implementations of this formula.

**LLM fine-tuning (Days 45–46):** RLHF can be interpreted as Bayesian updating of a policy using human preference data as evidence.

---

## Where It Breaks / What It Is Not

**Problem 1: Where does the starting belief come from?**

Bayesian reasoning requires a prior probability. What if you have no idea? Use base rates, reference classes, or a uniform distribution. Refusing to specify a starting belief is not a solution — it just hides one. Implicit beliefs of 50/50 or 100/0 are usually worse than any explicit estimate.

**Problem 2: Knowing the rule doesn't make you follow it.**

Studies consistently show that people underweight base rates (as in the medical example), overweight recent vivid evidence, anchor too hard on their prior, or over-update on a single dramatic event. Knowing Bayes' theorem does not automatically make you a Bayesian reasoner. It takes deliberate practice — which is why Day 63 is dedicated to calibration training.

**Problem 3: Dependent evidence gets double-counted.**

Sequential updating assumes each piece of evidence is independent of the others, given the hypothesis. The three-witness example assumed each witness testified independently. If witnesses talked to each other beforehand, their testimonies are correlated — and treating them as independent would over-update. In AI systems, this appears as dataset contamination — training on correlated data as if it were independent.

**Problem 4: The formula can't consider hypotheses you haven't thought of.**

You can only update on hypotheses you've actually listed. If the true explanation is outside your set, Bayesian updating assigns it probability zero and no evidence can bring it in. This is the weak spot of all formal probabilistic reasoning — it requires the judgment and creativity that no theorem can automate.

---

## Try It Yourself

**Exercise 1 — Medical test variant (5 min)**

Same test setup as above (99% sensitivity, 1% false positive rate), but now for a disease that affects 1 in 10 people (10% prevalence rather than 0.1%).

What is P(disease | positive test)?

<details>
<summary>Worked answer</summary>

P(H) = 0.10, P(E|H) = 0.99, P(E|¬H) = 0.01

```
P(H|E) = (0.99 × 0.10)
          ─────────────────────────────────────
          (0.99 × 0.10) + (0.01 × 0.90)

        = 0.099 / (0.099 + 0.009)
        = 0.099 / 0.108
        ≈ 0.917
```

**~91.7%** — drastically different from the 9% in the rare disease case. The starting belief (prevalence) dominates. Same test, different base rate, vastly different answer.

Lesson: The word "accurate" about a test is meaningless without specifying the population it's used in.

</details>

---

**Exercise 2 — The AI classifier (10 min)**

You have built a model that flags fraudulent transactions. Performance on your test set:
- True positive rate (sensitivity): 95%
- False positive rate: 2%

Fraud rate in your system: 0.5% of transactions.

a) A transaction is flagged. What is P(actually fraud | flagged)?
b) You want P(fraud | flagged) > 80%. What does the fraud rate need to be, holding sensitivity and false positive rate constant?

<details>
<summary>Worked answer</summary>

**a)**
```
P(fraud | flagged) = (0.95 × 0.005)
                     ─────────────────────────────────
                     (0.95 × 0.005) + (0.02 × 0.995)

                   = 0.00475 / (0.00475 + 0.01990)
                   = 0.00475 / 0.02465
                   ≈ 0.193 = 19.3%
```

Only 19.3% of flagged transactions are actually fraudulent — 80.7% are false alarms. This is why fraud detection systems require human review: the rate of true positives among all flags is low when fraud is rare.

**b)** We want: (0.95 × p) / [(0.95 × p) + (0.02 × (1−p))] = 0.80

Let p = fraud rate:
```
0.95p = 0.80 × [0.95p + 0.02(1−p)]
0.95p = 0.76p + 0.016 − 0.016p
0.95p − 0.76p + 0.016p = 0.016
0.206p = 0.016
p ≈ 0.0777 = 7.77%
```

You need a fraud rate of nearly 8% for the classifier to have 80% precision. In most systems, fraud rates are far below this — which is why high-precision fraud detection almost always requires additional signals beyond a single classifier.

</details>

---

**Exercise 3 — Update a product belief (10 min)**

You believe there is a 30% probability that Feature X will become your product's most-used feature within 6 months (your starting belief).

You run a small beta with 50 users. 35 of them use Feature X more than any other feature in their first week (E = "35/50 beta users prefer Feature X").

- P(E | "Feature X wins") = 0.75 (if it's going to win, beta users would show this)
- P(E | "Feature X doesn't win") = 0.40 (beta users sometimes prefer new shiny things that don't stick)

Update your belief using Bayes' theorem. What is P(Feature X wins | beta results)?

<details>
<summary>Worked answer</summary>

```
P(wins | E) = (0.75 × 0.30)
               ─────────────────────────────
               (0.75 × 0.30) + (0.40 × 0.70)

            = 0.225 / (0.225 + 0.28)
            = 0.225 / 0.505
            ≈ 0.446 = 44.6%
```

From 30% to 44.6% — a meaningful update but far from certainty. The beta results are encouraging but the likelihood ratio is only 0.75/0.40 = 1.875 — not strongly diagnostic. Run a larger test or wait for 30-day retention data before making a major resource commitment.

</details>

---

## Connect It Back

Yesterday you learned that probability estimates are often overconfident and that reference class forecasting gives you a better *starting point*. Today you learned the formal machinery for *moving* from that starting point: Bayesian updating. Your starting belief (from yesterday's calibration) feeds into Bayes' theorem. The evidence (new data) moves you. Your updated belief becomes tomorrow's starting point.

This is the core loop of all rational learning — whether in a human forecaster, a recommendation system, or an LLM fine-tuned on human feedback. Everything in Module 4 (AI products) is, at some level of abstraction, Bayesian updating. Day 5 is the most important quantitative tool in the course.

Tomorrow (Day 6) we take a hard turn into the ways this rational loop breaks down. We have now established what *should* happen when we make decisions and update beliefs. Days 6–8 map the systematic, predictable ways that it *doesn't*.

**The question you should be able to answer now:**
*A test has 99% sensitivity and 1% false positive rate. Why does a positive result on a very rare disease only yield a low updated probability of actually having the disease?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
McElreath, Richard. *Statistical Rethinking: A Bayesian Course with Examples in R and Stan*. 2nd ed., CRC Press, 2020. Chapter 2, Sections 2.1–2.3 ("The Garden of Forking Data"). The best intuitive introduction to Bayesian reasoning in any textbook. The marble-in-a-bag example is worth 30 minutes of your life.

**If you want the deep version:**

1. Kahneman, *Thinking, Fast and Slow*, Chapters 14–16. — "Tom W's Specialty," "Linda: Less Is More," "Causes Trump Statistics." These three chapters show exactly how humans fail at Bayesian reasoning — essential for Days 6–7.

2. Pearl, Judea & Mackenzie, Dana. *The Book of Why: The New Science of Cause and Effect*. Basic Books, 2018. Chapter 3 ("From Evidence to Causes: Reverend Bayes Meets Mr Holmes"). — A more narrative treatment of Bayesian inference with a focus on causal reasoning. Excellent bridge to AI product thinking.

3. Jaynes, E.T. *Probability Theory: The Logic of Science*. Cambridge University Press, 2003. Chapter 1. — The most thorough philosophical treatment of why Bayesian probability is the *only* consistent way to reason under uncertainty. Heavy reading; Chapter 1 alone is worth the effort for L2 readers who want to understand *why* Bayes is right, not just how to use it.


---

← [Day 4 — Probability & Calibration](day-04-probability-calibration) &nbsp;|&nbsp; [Day 6 — Cognitive Biases I →](day-06-cognitive-biases-i)
