*[Decision Science & Game Theory](../../../../README.md) · [Module 1 — The Science of Choosing](../1-overview.md) · Day 11 of 65*

# Day 11 — Rest & Synthesise: The Architecture of Individual Choice

> **Today's one idea:** There is no new material today. Your task is to re-derive the core framework from scratch — without notes — and apply it to one real decision you have faced or are facing.
> **Reading time:** ~40 min active work · **Prereqs:** Days 1–10
> **Primary source for today:** Your own notes and memory.

---

## What Rest & Synthesise Days Are For

You have spent 10 days building a framework piece by piece. The danger at this stage is that you know *each piece* but cannot yet connect them into a coherent whole. You can explain what System 1 is, what Bayesian updating does, and what Prospect Theory predicts — but can you use all three simultaneously on a real problem?

This day exists to find out. And more importantly, to close any gaps before Module 2 (game theory) builds on top of what you've built here.

**Do not re-read previous pages before completing the exercises.** The point is to test retrieval from memory, not to verify recall with the book open. If you get stuck, note exactly where — that is the gap to close.

---

## Part 1 — Reconstruct the Core (15 min)

Without looking at previous pages, complete the following from memory. Use a blank sheet of paper or a fresh document.

**A. Dual-process theory**

1. Draw the dual-process diagram. Label what each system does, when each is active, and the failure conditions.

2. In one sentence: why does *knowing about* System 1 not protect you from it?

**B. The rational model**

3. Write the formula for Expected Value. Give a one-sentence interpretation.

4. Write the formula for Expected Utility. Explain in one sentence what makes it *different* from EV — not just formally, but economically.

5. Describe the shape of the utility function for a risk-averse agent. Why does that shape imply risk aversion?

**C. Probability and updating**

6. Write Bayes' theorem. Name each term (prior, likelihood, posterior, marginal likelihood). Give the medical test example from memory: prior = 0.1%, sensitivity = 99%, false positive = 1%. What is P(disease | positive)?

7. Define calibration in one sentence. What is the difference between being *miscalibrated* and being *wrong* about a specific prediction?

**D. Biases**

8. Name the six biases from Days 6–7. For each, give: (a) the mechanism in one sentence, (b) one real-world example.

**E. Prospect Theory**

9. Draw the Prospect Theory value function. Label the three key properties (reference dependence, diminishing sensitivity, loss aversion) on the diagram.

10. State the four-fold pattern from memory. For each of the four cells, give one real-world example from product design or career decisions.

**F. Decision tools**

11. Define EVPI. Write the formula. When is EVPI = 0?

12. Describe the rollback algorithm for a decision tree in pseudocode or plain English.

---

*Compare your answers to the relevant pages after completing the above. Note specific gaps — these are the pages to skim again before Module 2.*

---

## Part 2 — Apply the Framework (20 min)

Choose **one real decision** you have faced in the last 6 months or are currently facing. It should have:
- At least one significant uncertainty
- At least two plausible options
- Material consequences (career, product, financial, or relationship)

Work through the following five-step analysis **in writing**. Do not skip to the answer — the process is the point.

---

**Step 1: Identify the decision and structure it.**

Write out:
- What are the available options? (Be exhaustive — including "do nothing" and "gather more information first.")
- What are the key uncertainties? (List the things you don't know that would affect the outcome.)
- What is the time horizon? (When will you know the outcome?)

---

**Step 2: Assign probabilities.**

For each key uncertainty:
- Give a point estimate probability.
- Give a 90% confidence interval on that probability.
- Identify your reference class: what base rate are you starting from?
- Name any bias (availability, anchoring, sunk cost) that might be distorting your estimate.

---

**Step 3: Compute EV (or EU if appropriate).**

Build a simple decision tree (it can be hand-drawn or text-based). Roll back. State the EV of each option.

If the stakes are high and you know your risk preferences: apply a concave utility function (e.g., √x or ln(x)) and compute EU instead.

---

**Step 4: Apply the behavioural lens.**

Now apply Prospect Theory:
- What is the reference point for this decision?
- Are you evaluating options as gains or losses from that reference?
- Which biases from Days 6–7 are most active in how you've been thinking about this decision?
- Does the loss-aversion lens change your evaluation relative to Step 3?

---

**Step 5: Decide and commit.**

Write one sentence: *"Given the EV analysis and the behavioural audit, my decision is X."*

Then write one sentence: *"The assumption that, if wrong, would most change this decision is Y."*

Then write one sentence: *"If I observe Y turning out to be wrong, I will revisit this decision by [specific date or trigger]."*

---

## Part 3 — The Module 1 Self-Test (5 min)

Answer these questions from memory. These are the questions a thoughtful colleague should be able to ask you after Day 10, and you should be able to answer without hesitation.

1. What is the Allais Paradox, and which axiom of expected utility theory does it violate?

2. A test has 95% sensitivity and 5% false positive rate. The disease affects 2% of the population. Someone tests positive. What is P(disease | positive)?

3. You estimate there's a 60% chance a feature will succeed. A $50K study would be diagnostically strong (LR ≈ 10). The decision is worth $200K if you get it right. Should you run the study? (Rough reasoning, not full EVSI calculation.)

4. Draw the four-fold pattern table from Prospect Theory. Which cell explains why people buy lottery tickets? Which cell explains why they buy insurance for rare catastrophes?

5. You are about to shut down a project that has consumed $2M of engineering investment. A colleague says, "We've already spent $2M — we can't quit now." Name the bias, explain why it is logically invalid, and state the correct question to ask.

<details>
<summary>Check answers</summary>

**1.** The Allais Paradox: choosing certainty of $1M over a gamble, then choosing a gamble over a near-certain $1M, violates the *independence axiom* of EU theory. The preference for certainty is inconsistent with how independence requires probabilities to be treated.

**2.** P(pos|disease) = 0.95; P(pos|healthy) = 0.05; P(disease) = 0.02.
```
P(disease|positive) = (0.95×0.02) / [(0.95×0.02) + (0.05×0.98)]
                    = 0.019 / (0.019 + 0.049)
                    = 0.019 / 0.068 ≈ 0.279 ≈ 28%
```

**3.** EVPI ≤ $200K × (something based on current distribution). With a strong study (LR ≈ 10), EVSI approaches EVPI. If the decision is worth $200K under optimal conditions and the study costs $50K, the study is likely worth running — provided the $200K payoff is the improvement from getting it right vs. wrong, not the total payoff. (Full EVSI calculation needed for precision, but the magnitude suggests yes.)

**4.**
| | High probability | Low probability |
|--|--|--|
| **Gains** | Risk-averse (certain $900 > 90% chance of $1K) | Risk-seeking (lottery) |
| **Losses** | Risk-seeking (gamble to avoid certain loss) | Risk-averse (insurance for rare disaster) |

Lottery tickets: Gains, Low probability. Insurance for catastrophe: Losses, Low probability.

**5.** Sunk cost fallacy. The $2M is gone regardless of the next decision. The correct question: *"If we were starting today with no prior investment, would we choose to build this product?"* Only future costs and future benefits are decision-relevant.

</details>

---

## Part 4 — Calibration Check (5 min)

Go back to the confidence interval exercise from Day 4 (Exercise 1). Look up the answers you haven't checked yet.

Count: how many of your 90% confidence intervals contained the true answer?

If ≥ 8 out of 10: well-calibrated. Proceed to Module 2 with confidence.
If 5–7 out of 10: mild overconfidence. Before Day 12, read Tetlock & Gardner, *Superforecasting*, Chapter 5 ("Supersmart?"). Focus on the "outside view" technique.
If < 5 out of 10: significant overconfidence. Do the calibration exercise at metaculus.com for 10 more questions before proceeding.

---

## What You've Earned

By completing today, you have demonstrated — not just learned — the following capabilities:

- You can structure an ambiguous decision with multiple uncertainties
- You can assign calibrated probabilities with explicit reference classes
- You can compute EV/EU and solve a decision tree
- You can identify which cognitive biases are active and adjust for them
- You can state the assumption your decision is most sensitive to, and set a trigger to revisit

This is the complete individual decision-making toolkit. Everything in Module 2 onward assumes you can do all of this fluently. If any step in Part 2 was laboured, revisit the relevant day before continuing.

---

## Tomorrow

Day 12 adds one practical extension: what to do when decisions have multiple criteria that can't be reduced to a single number — quality vs. speed vs. cost, or career growth vs. compensation vs. location. The tool is structured multi-attribute decision analysis.

Then Day 13 completes the module with deep uncertainty — what to do when you cannot even assign probabilities, let alone expected values.

---

## No Required Readings Today

Rest & Synthesise days have no required readings. If you finished quickly and want to read, the best choice is to re-read one of the primary sources you found most challenging:

- If Bayesian updating felt shaky: McElreath, *Statistical Rethinking*, Chapter 2 (the marble bag example).
- If Prospect Theory felt abstract: Kahneman & Tversky (1979), Sections I–III.
- If decision trees felt mechanical: Clemen & Reilly, Chapter 4 (sensitivity analysis).

Otherwise: rest. The consolidation happens offline.


---

← [Day 10 — Decision Trees](day-10-decision-trees) &nbsp;|&nbsp; [Day 12 — Multi-Criteria Decisions →](day-12-multi-criteria-decisions)
