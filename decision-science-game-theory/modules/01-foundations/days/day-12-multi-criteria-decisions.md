*[Decision Science & Game Theory](../../../../README.md) · [Module 1 — The Science of Choosing](../1-overview.md) · Day 12 of 65*

# Day 12 — Multi-Criteria Decisions: When More Than One Thing Matters

> **Today's one idea:** When outcomes have multiple attributes — cost, quality, speed, risk — structured weighting converts incommensurable tradeoffs into explicit, auditable choices rather than gut-feel compromises.
> **Reading time:** ~35 min · **Prereqs:** [Day 2](day-02-expected-value.md), [Day 10](day-10-decision-trees.md)
> **Primary source for today:** Keeney, Ralph L. & Raiffa, Howard. *Decisions with Multiple Objectives: Preferences and Value Tradeoffs*. Cambridge University Press, 1993. Chapter 3 ("Foundations").

---

## The Hook

You are evaluating three ML platform vendors. You have five criteria: price, model quality, latency, data privacy compliance, and vendor stability. Vendor A is cheapest but slowest. Vendor B has the best models but poor compliance posture. Vendor C is well-rounded but mediocre on everything.

Someone says: "Let's just go with C, it's the safest bet."

Someone else says: "Compliance is non-negotiable — B is out, so it's A or C."

A third person says: "Our users care most about quality — the latency difference doesn't matter."

Three smart people, three incompatible conclusions — all from the same data, all because the criteria were never made explicit. Nobody said what "compliance non-negotiable" means in terms of how many quality points it's worth. Nobody said what "users care most about quality" means quantitatively.

Multi-criteria decision analysis (MCDA) doesn't resolve disagreements for you. It surfaces what the disagreement is actually about.

---

## Building the Intuition

### Why single-criterion reduction fails

The naive solution: collapse everything into money. Convert quality, speed, and compliance into dollar values and sum.

The problem: most criteria resist clean monetisation. How much is "user trust" worth in dollars? How much is "team morale impact" worth? For many of the most important dimensions of a decision, you cannot defensibly assign a dollar value — but you still need to trade off between them.

The second naive solution: rank criteria, eliminate options that fail the top criterion, then use the second criterion on survivors.

The problem: this lexicographic approach ignores tradeoffs entirely. It treats "non-negotiable" as absolute when almost nothing truly is. A vendor with 99.9% compliance and excellent quality is treated identically to one with 70% compliance — both "fail" the compliance threshold — even if the quantitative difference is enormous.

Multi-attribute utility theory (MAUT) offers the right middle path: make your tradeoffs explicit through weighted scoring, identify the weights you implicitly hold, and check whether they produce sensible tradeoffs before you commit.

---

### The weighted value method

**Step 1: Identify criteria.**

List all attributes that matter for the decision. Be exhaustive — a criterion left off the list is one that will re-emerge as a vague "gut feeling" later.

For the vendor example: {Price, Model Quality, Latency, Compliance, Vendor Stability}

**Step 2: Define scales.**

For each criterion, define what "best" and "worst" look like, and how you will measure performance on that criterion. Use consistent scales — either raw numbers (lower price = better) or normalised 0–100 scores.

| Criterion | Scale | Vendor A | Vendor B | Vendor C |
|-----------|-------|----------|----------|----------|
| Price ($/mo) | Lower is better. Raw: $500–$2,000 | $600 | $1,800 | $1,200 |
| Model Quality | 0–100 benchmark score | 72 | 95 | 81 |
| Latency (ms) | Lower is better. Range: 80–400ms | 350 | 120 | 180 |
| Compliance score | 0–100 (100 = full compliance) | 85 | 55 | 92 |
| Vendor Stability | 0–100 (funding, tenure, SLA) | 60 | 75 | 88 |

**Step 3: Normalise to 0–100.**

Convert each criterion so that 100 = best possible, 0 = worst possible across your option set.

Price: A=$600 (best→100), C=$1,200 (mid→67), B=$1,800 (worst→0)
Latency: B=120ms (best→100), C=180ms (mid→77), A=350ms (worst→0)
Others already on 0–100 scale.

| Criterion | Vendor A | Vendor B | Vendor C |
|-----------|----------|----------|----------|
| Price | 100 | 0 | 67 |
| Model Quality | 0 | 100 | 39 |
| Latency | 0 | 100 | 77 |
| Compliance | 67 | 0 | 100 |
| Stability | 0 | 39 | 100 |

**Step 4: Assign weights.**

This is the critical step — and the one that forces the most useful conversation. Weights should reflect *relative importance* and *range of variation*. A criterion with a small range of variation across options matters less even if it is "important" in principle.

One way to derive weights: use the "swing weight" method. Imagine all vendors at their *worst* on every criterion. Now suppose you can "swing" one criterion from worst to best. Which swing gives you the most value? That criterion gets the highest weight.

Example weights after a team discussion:
| Criterion | Weight |
|-----------|--------|
| Model Quality | 35% |
| Compliance | 30% |
| Latency | 15% |
| Vendor Stability | 12% |
| Price | 8% |

**Step 5: Compute weighted scores.**

```
Score(A) = 35%×0 + 30%×67 + 15%×0 + 12%×0 + 8%×100
         = 0 + 20.1 + 0 + 0 + 8 = 28.1

Score(B) = 35%×100 + 30%×0 + 15%×100 + 12%×39 + 8%×0
         = 35 + 0 + 15 + 4.7 + 0 = 54.7

Score(C) = 35%×39 + 30%×100 + 15%×77 + 12%×100 + 8%×67
         = 13.7 + 30 + 11.6 + 12 + 5.4 = 72.7
```

**Vendor C wins** (72.7), despite being mediocre on everything. Vendor B scores surprisingly well (54.7) given its compliance failure, because quality and latency are weighted heavily.

Now the conversation is precise: if you want to switch from C to B, you need to either (a) increase the weight on quality/latency relative to compliance, or (b) get Vendor B to improve their compliance score. Both are actionable.

---

## The Formal Picture

**Multi-Attribute Utility Theory (MAUT):**

For a set of criteria — each scored from 0 (worst in your option set) to 100 (best) — the overall score is:

```
Total score = (weight of criterion 1 × score on criterion 1)
            + (weight of criterion 2 × score on criterion 2)
            + ...     (all weights must add to 1.0)
```

This is called the **additive value function**. It works when your criteria are reasonably independent — meaning your preference for one criterion doesn't change based on how another criterion is doing. (If they're not independent — for example, you only care about quality *if* compliance is already satisfied — you need a more complex form described in the primary source.)

**When to use the additive form:** When criteria are reasonably independent and the decision is not one you'd describe as having "threshold requirements" on any single criterion.

**When to use a threshold constraint instead:** When a criterion is truly non-negotiable — not "most important" but "we will not proceed if this criterion falls below X." Model this as a hard constraint (eliminate options that fail the threshold) rather than a weight. If Vendor B's compliance score of 55 represents a legal violation, eliminate it first, then score the survivors.

**The swing weight procedure:**

1. Imagine a "worst possible" option where every criterion is at its worst level.
2. For each criterion, ask: if you could improve just this one criterion from worst to best (holding all others at worst), how much would you value that improvement? Score it 0–100.
3. Divide each criterion's score by the total of all scores so the final weights add to 1.

The swing weight procedure anchors weights to the *range of variation that actually exists among your options* — not to abstract importance.

---

### Multi-criteria decisions in AI product development

**Feature prioritisation (RICE + criteria):**

RICE (Reach, Impact, Confidence, Effort) is an MCDA method in disguise. The standard formula RICE = R×I×C/E is a multiplicative aggregation — not an additive one. Its implicit assumption is that criteria are not independent (low confidence multiplies down the whole score).

More rigorous RICE: decompose Impact into multiple sub-criteria (retention impact, revenue impact, strategic alignment), weight each, then aggregate. Eliminates the arbitrary feeling of most scoring exercises.

**Model selection:**

When choosing between ML models, criteria include: accuracy, latency, cost, interpretability, drift resistance, fairness. These are genuinely incommensurable — you cannot collapse them to a single number without making the tradeoff explicit. MAUT forces the conversation about what "best" means for your application.

**Hiring decisions:**

Technical skill, communication, culture contribution, growth trajectory, domain expertise — all matter, all resist direct comparison. Structured scorecards with explicit weights, filled independently by each interviewer before calibration, produce dramatically less biased outcomes than unstructured discussion.

---

## Where It Breaks / What It Is Not

**Limitation 1: Weights feel arbitrary.**
They are — until you use swing weights. Arbitrary weights produce garbage. Swing weights calibrated to the actual range of variation in your option set produce useful guidance. If two team members' swing weights differ dramatically, that is the real disagreement — surface it.

**Limitation 2: Criteria that seem independent often aren't.**
Compliance and vendor stability are correlated — a vendor with poor compliance posture probably has other governance problems. MAUT with the additive form treats them as independent dimensions. For deeply correlated criteria, consider consolidating into a single criterion with sub-components.

**Limitation 3: Normalisation hides dominated options.**
If Vendor A is worst on four criteria and best on one (price), it scores zero on four dimensions regardless of how *much* better it is on price. Don't let normalisation compress away large absolute differences.

**Limitation 4: This is not a substitute for judgement.**
The output (72.7 vs. 54.7 vs. 28.1) is not the decision — it is structured input to the decision. If the model says Vendor C but your team has a strong gut conviction about Vendor B, don't override the gut: interrogate the weights. The model is wrong if the weights are wrong.

---

## Try It Yourself

**Exercise 1 — Career decision (20 min)**

You are choosing between two job offers. Define your own criteria (suggest 4–6), assign weights using the swing weight procedure, score both options, and compute a weighted total.

Then: identify the one weight change that would flip your recommendation. Is that weight change within your genuine uncertainty about your own preferences?

---

**Exercise 2 — Feature prioritisation (10 min, L2)**

You have 4 features in your backlog. Criteria: user impact (0–10), strategic fit (0–10), implementation confidence (0–10, where 10 = high confidence it works), effort (1–5, where 1 = high effort).

| Feature | User Impact | Strategic Fit | Confidence | Effort |
|---------|-------------|---------------|------------|--------|
| A | 8 | 9 | 6 | 3 |
| B | 5 | 7 | 9 | 1 |
| C | 9 | 4 | 8 | 4 |
| D | 6 | 8 | 5 | 2 |

a) Normalise each criterion to 0–100 (across these four options).
b) Assign weights that reflect: user impact matters most (40%), strategic fit second (30%), confidence third (20%), effort last (10%).
c) Compute weighted scores. Which feature ships first?
d) Rerun with effort weighted 30% and user impact 20%. Does the recommendation change?

<details>
<summary>Worked answer</summary>

**a) Normalisation:**
- User Impact: range 5–9. A→75, B→0, C→100, D→25
- Strategic Fit: range 4–9. A→100, B→60, C→0, D→80
- Confidence: range 5–9. A→25, B→100, C→75, D→0
- Effort: range 1–4 (1=worst/most effort, 4=best/least effort). A→67, B→0, C→100, D→33

**b/c) Weighted scores (40/30/20/10):**
```
A: 40%×75 + 30%×100 + 20%×25 + 10%×67 = 30+30+5+6.7 = 71.7
B: 40%×0  + 30%×60  + 20%×100 + 10%×0  = 0+18+20+0  = 38.0
C: 40%×100 + 30%×0 + 20%×75  + 10%×100 = 40+0+15+10 = 65.0
D: 40%×25  + 30%×80 + 20%×0  + 10%×33  = 10+24+0+3.3 = 37.3
```
**Feature A ships first** (71.7).

**d) Rerun (20/30/20/30):**
```
A: 20%×75+30%×100+20%×25+30%×67 = 15+30+5+20.1 = 70.1
B: 20%×0+30%×60+20%×100+30%×0   = 0+18+20+0   = 38.0
C: 20%×100+30%×0+20%×75+30%×100 = 20+0+15+30  = 65.0
D: 20%×25+30%×80+20%×0+30%×33   = 5+24+0+9.9  = 38.9
```
Still **Feature A** (70.1) — the recommendation is robust to this weight change.

</details>

---

## Connect It Back

Days 1–11 built and synthesised the framework for *individual* decisions under uncertainty and bias. Today added the practical tool for when outcomes can't be reduced to a single dimension. Tomorrow (Day 13) is the last page of Module 1: what to do when you cannot assign probabilities at all — deep uncertainty, where the entire probability framework breaks down.

**The question you should be able to answer now:**
*Why is "compliance is non-negotiable" better modelled as a hard constraint than as a very high weight — and when might that choice itself be wrong?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Keeney & Raiffa, *Decisions with Multiple Objectives*, Chapter 3, Sections 3.1–3.4. The foundational treatment of preferential independence and the additive value function. Dense but worth 20 minutes for L2 readers who want to understand when the additive form is valid.

**If you want the deep version:**

1. Keeney, Ralph L. *Value-Focused Thinking: A Path to Creative Decisionmaking*. Harvard University Press, 1992. Chapter 2 ("Creating Alternatives"). — Keeney's argument that most decision problems are poorly framed because the objectives (criteria) weren't defined before the alternatives were generated. A mindset shift for product and career decisions.

2. Saaty, Thomas L. "How to Make a Decision: The Analytic Hierarchy Process." *European Journal of Operational Research*, 48(1):9–26, 1990. — AHP is an alternative MCDA framework using pairwise comparisons rather than direct weights. More robust to inconsistent preferences; used in large procurement decisions.

3. For product teams: Cagan, Marty. *Inspired: How to Create Tech Products Customers Love*. 2nd ed., Wiley, 2017. Chapter 14 ("The Role of Product Management"). — Cagan's product prioritisation framework implicitly uses MAUT; reading with today's formal tools shows what makes RICE and other scoring models work or fail.


---

← [Day 11 — Rest & Synthesise](day-11-rest-synthesise) &nbsp;|&nbsp; [Day 13 — Deep Uncertainty →](day-13-deep-uncertainty)
