# Day 18 — Rest & Synthesize III: The Self-Improvement Spectrum

> **Today's one idea:** *(No new material. Today you map all four self-improvement patterns onto one spectrum and lock in the decision framework for choosing between them.)*
> **Reading time:** ~30 min of active recall · **Prereqs:** Days 11, 15, 16, 17

---

## The spectrum

Before the exercises, here is the structure you're consolidating. Everything in this module fits on two axes: **when** improvement happens, and **where** the improvement is stored.

```mermaid
quadrantChart
    title Self-Improvement Patterns — When vs. Where
    x-axis Within Run --> Cross-Run (parametric)
    y-axis Ephemeral --> Permanent Weights
    Self-Refine: [0.10, 0.08]
    Reflexion: [0.38, 0.33]
    ExpeL: [0.68, 0.55]
    STaR: [0.75, 0.90]
```

Each position on this chart implies a different cost, persistence, and infrastructure requirement:

| Pattern | When | Where stored | Cost | Inference overhead | Infrastructure |
|---------|------|-------------|------|-------------------|----------------|
| Self-Refine | Within run | Nowhere | 3–5× generation | None | Just the API |
| Reflexion | Across runs | Context string | 2–3× per trial | O(n) tokens | Just the API |
| ExpeL | Across runs | Retrieved rules | High (trajectory collection) | O(k) tokens | Embedding store |
| STaR | Across runs | Model weights | Very high (training) | Zero | GPU + data pipeline |

---

## Synthesis exercises

---

### Exercise 1 — Rebuild from memory

Without opening any previous page, fill in this table:

| Pattern | Load-bearing idea (one sentence) | Requires correct-answer labels? | Requires GPU? | Persists after session ends? |
|---------|----------------------------------|--------------------------------|---------------|------------------------------|
| Self-Refine | | | | |
| Reflexion | | | | |
| ExpeL | | | | |
| STaR | | | | |

<details>
<summary>Check your answers</summary>

| Pattern | Load-bearing idea | Labels? | GPU? | Persists? |
|---------|------------------|---------|------|-----------|
| Self-Refine | Generate → critique → refine within one run, no memory | No | No | No |
| Reflexion | Write a verbal lesson from a failed trajectory; prepend to next trial | Only a success/failure signal | No | Only if you save verbal_memory to disk |
| ExpeL | Mine past trajectories for generalizable rules; retrieve top-k for new tasks | Success/failure signal + trajectory capture | No | Yes (if insight store is persisted) |
| STaR | Generate rationales, filter correct ones, rationalize the rest, fine-tune; repeat | Yes — correct answers required | Yes | Yes (weights updated) |

</details>

---

### Exercise 2 — Scenario matching

For each scenario, name the best self-improvement pattern and write one sentence explaining why.

**(a)** Your agent writes marketing copy. You want it to get better after each piece it writes. You have no labels (no "correct" copy), just a human quality rating (1–5) after each piece.

**(b)** Your coding agent passes 60% of unit tests on first attempt. You have a test suite (ground truth). You have a GPU cluster. You want to push to 90%.

**(c)** Your agent answers customer support tickets. It's handling 200 tickets per day. You want each ticket response to benefit from lessons learned in the current session without any infrastructure changes.

**(d)** Your research agent has run 500 tasks over the past month. You want to distill what it's learned without retraining it.

<details>
<summary>Worked solutions</summary>

**(a)** **Self-Refine.** Human rating is a quality signal but not a labeled answer — you can't train with it easily. Self-Refine doesn't need labels; it runs the generate-critique-refine loop within each piece production, using the human rating as a stopping signal (run refine cycles until rating ≥ 4).

**(b)** **STaR.** You have correct-answer labels (test suite pass/fail), a GPU, and a clear accuracy gap to close. This is exactly STaR's sweet spot: generate rationales → filter by test pass → rationalize the failures → fine-tune → repeat.

**(c)** **Reflexion.** 200 tickets/day is high volume, but within a session. Reflexion's verbal lessons accumulate in context across the day's tickets, benefiting later tickets in the same session. Zero infrastructure change — just prepend the growing lessons list to each new ticket's context.

**(d)** **ExpeL.** 500 historical trajectories is exactly the right scale for insight extraction. Mine them for patterns, build an insight store, retrieve top-k insights for new tasks. No retraining needed, and the improvement is durable (the insight store persists).
</details>

---

### Exercise 3 — Dependency graph

Draw the dependency graph for the four self-improvement patterns, using the Day 2 memory taxonomy as the coordinate system. For each pattern, mark which memory component it primarily uses:

- In-context (working) memory
- External episodic memory
- External semantic memory
- Parametric memory

<details>
<summary>Worked solution</summary>

```
Self-Refine  →  In-context (working) memory
               The critique and refinement happen entirely within one
               context window. Nothing stored externally.

Reflexion    →  External episodic memory (lightweight)
               The verbal lessons are stored as strings (external to
               any single context window) and retrieved by prepending
               them to the next trial's context. They are episodic:
               each lesson is tied to a specific past run.

ExpeL        →  External semantic memory
               The insight store contains generalizable rules indexed
               by semantic similarity — not tied to specific past runs.
               Retrieval is based on meaning, not episode identity.
               This is semantic memory (knowledge that applies generally).

STaR         →  Parametric memory
               The learned reasoning patterns are encoded in the model's
               weights. This is the most durable form of memory — it
               doesn't consume context tokens and persists indefinitely.
```
</details>

---

### Exercise 4 — The composition question

Can two self-improvement patterns be combined? Describe one combination that makes practical sense and one that doesn't. For the one that makes sense, sketch the architecture in a few sentences.

<details>
<summary>Worked solution</summary>

**Makes sense: Self-Refine inside Reflexion.**
Each Reflexion trial uses Self-Refine to produce the best possible answer before submitting for evaluation. The Reflexion loop handles cross-trial learning (verbal lessons from failures). The Self-Refine loop handles within-trial quality. The architecture: outer loop = Reflexion (trials + lesson accumulation); inner loop = Self-Refine (generate-critique-refine for each trial). This is directly deployable.

**Doesn't make sense: ExpeL inside STaR.**
STaR fine-tunes the model's weights using self-generated rationales. ExpeL retrieves external insights for inference. Combining them would mean the fine-tuned model also retrieves insights — but the fine-tuned weights already encode what the insights would say, making retrieval redundant. Worse, the fine-tuning might cause the model to over-rely on insights that were accurate for old trajectories but are outdated after capability improvement. The two patterns serve the same function via different mechanisms and don't need to be layered.
</details>

---

### Exercise 5 — Design review

A colleague proposes this agent architecture: "For every task, we run Self-Refine (5 iterations), then Reflexion (3 trials), then ExpeL (retrieve top-5 insights)." Critique this design. What's right about it? What's wrong? What would you change?

<details>
<summary>Worked solution</summary>

**What's right:** Self-Refine + Reflexion is a valid composition (within-trial polish + cross-trial learning). Including ExpeL for context from historical runs is also sensible.

**What's wrong:**
1. **Order is backward for ExpeL.** ExpeL insights should be retrieved *before* running the task, not after. You want the insights to condition the trial, not annotate a completed run.
2. **5 Self-Refine iterations is aggressive.** For most tasks, 2–3 iterations produce the majority of the improvement; iterations 4–5 often oscillate or regress. Start with 2, add more only if evaluation shows improvement.
3. **3 Reflexion trials × 5 Self-Refine iterations = 15+ LLM calls per task.** This is expensive. Is the task high-stakes enough to justify it? The design doesn't ask this question.
4. **Missing: evaluation signal for Reflexion.** Reflexion requires knowing whether a trial succeeded or failed. What is the evaluator? The design doesn't specify this.

**What you'd change:**
1. Retrieve ExpeL insights before any trial begins.
2. Reduce Self-Refine to 2 iterations by default; add adaptive stopping.
3. Add a clear evaluator (task-specific success criterion).
4. Apply this full stack only to tasks where cost is justified; use direct generation for simple tasks.
</details>

---

## What you should be able to do by the end of today

- [ ] Fill in the 4-pattern comparison table from memory
- [ ] Place each pattern on the when/where spectrum
- [ ] Map each pattern to its primary memory type (Day 2 taxonomy)
- [ ] Choose the right pattern for any of the four scenarios above
- [ ] Identify one valid and one invalid pattern combination, with reasoning

---

## Looking ahead

Module 4 starts tomorrow. The self-improvement patterns you've learned are about *how the agent gets better*. The skills and tools patterns are about *what it can do*. These are orthogonal — a better reasoning agent with poorly-designed tools still hits a ceiling. A well-designed skill library with no self-improvement still starts from scratch every run.

Day 19 introduces the Skill Design Pattern — what distinguishes a brittle tool from a composable skill.

---

## Navigation

← **Previous:** [Day 17 — ExpeL: Experiential Learning from Trajectories](./day-17-expel.md)
→ **Next:** [Day 19 — The Skill Design Pattern](../../04-skills-and-tools/days/day-19-skill-design-pattern.md)
