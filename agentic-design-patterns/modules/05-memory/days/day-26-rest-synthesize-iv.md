# Day 26 — Rest & Synthesize IV

> **No new material today.** Five active-recall exercises that span all five modules so far. Close the tabs. Write from memory. Check your answers after.
> **Reading time:** ~40 min (exercises only) · **Prereqs:** Days 1–25

---

## Why this matters

You have covered 25 days of material across five modules. The goal of this session is to consolidate — not to consume more. Research on learning consistently shows that retrieval practice (trying to recall and reconstruct) produces stronger retention than re-reading. If an exercise feels hard, that is the point: the difficulty is the learning.

---

## Exercise 1 — The full memory map (Day 23)

Without looking at any prior material, complete this table from memory. For each pattern, identify the memory store it primarily uses and describe specifically what is stored there (not just "experience" — what data structure, what content).

| Pattern | Primary memory store | What is specifically stored |
|---------|---------------------|----------------------------|
| Chain-of-Thought | | |
| Self-Consistency | | |
| ReAct | | |
| Reflexion | | |
| Self-Refine | | |
| STaR | | |
| ExpeL | | |
| Skill Library | | |
| Scratchpad (Day 24) | | |
| Episodic Memory (Day 25) | | |

After filling it in, ask: which store has the most patterns using it? Which pattern is the *only* one using parametric memory? Why?

<details>
<summary>Reference answers</summary>

| Pattern | Primary memory store | What is specifically stored |
|---------|---------------------|----------------------------|
| Chain-of-Thought | In-context | The intermediate reasoning tokens (the "scratchpad" embedded in the prompt) |
| Self-Consistency | In-context (multi-sample) | N independent reasoning paths + final answers for majority voting |
| ReAct | In-context | The full T/A/O trace — each Thought, Action, and Observation in sequence |
| Reflexion | External episodic | Verbal lessons — one string per failed trial: what went wrong and why |
| Self-Refine | In-context | The sequence: initial output → critique → refined output (all in one context) |
| STaR | Parametric | Fine-tuned model weights encoding correct rationale-then-answer patterns |
| ExpeL | External semantic | Distilled, indexed rules mined from comparing success vs failure trajectories |
| Skill Library | External semantic | Verified, executable Python functions keyed by name and searchable by description |
| Scratchpad (Day 24) | In-context (structured) | Named slots: Established Facts, Open Questions, Working Notes — explicitly managed |
| Episodic Memory (Day 25) | External episodic | Serialized episodes: task + full T/A/O trajectory + outcome + success flag |

STaR is the only pattern updating parametric memory at runtime (weight update via fine-tuning). Everything else updates in-context or external stores. This reflects the cost asymmetry: parametric writes are training-run-scale; everything else is API-call-scale.
</details>

---

## Exercise 2 — Design from requirements (Module 05 synthesis)

A customer support agent has the following requirements:

1. **Handles 500 tickets per day** — must be fast; no human review in the loop for routine tickets.
2. **Improves over time** — after 30 days, it should handle the ticket types it has seen better than on day 1.
3. **Never gives the same wrong answer twice** — if a response is escalated by a customer, the system must prevent that exact failure mode from recurring.
4. **Retains verified solutions for reuse** — when a novel workaround is discovered for a known bug, that workaround should be reusable for all future occurrences.
5. **Keeps full conversation context for long sessions** — a single ticket can span 20+ turns without losing early context.

For each requirement, specify:
- Which memory store handles it
- What the write event is (when does the agent write to this store?)
- What the read event is (when does the agent read from this store?)

Write your answer before looking at the reference.

<details>
<summary>Reference answers</summary>

**1. Speed — In-context (working memory)**
Write: every LLM call naturally writes to context as tokens. No extra infrastructure.
Read: every LLM call reads its context window. Zero overhead.
Note: this doesn't improve over time, but it's the fastest path for each individual ticket.

**2. Improves over time — External episodic**
Write: at the end of each completed ticket, serialize the episode (ticket description, resolution steps, outcome, success flag) and store it.
Read: at the start of each new ticket, retrieve the top-k most similar past episodes and inject them as few-shot context.

**3. No repeat failures — External semantic**
Write: when a ticket is escalated (marked as failed), extract the failure pattern and write a "do not" rule to a semantic failure store with verification.
Read: at the start of each new ticket, search the failure store for patterns similar to this ticket. If a match is found, inject the rule as a constraint.

**4. Verified workarounds — External semantic (Skill Library variant)**
Write: when a novel workaround is discovered and verified, add it to the skill library with a description keyed to the bug type.
Read: at the start of a ticket that matches a known bug, retrieve the relevant skill and inject it as a usable tool.

**5. Long conversation context — In-context + Scratchpad pattern**
Write: at each turn, update the scratchpad slots (established facts about the ticket, open questions, recent exchange). Compress old turns when approaching the token limit.
Read: each LLM call reads the compressed scratchpad. This prevents silent truncation of early context.

The full memory architecture uses all four stores — but not parametric. The volume (500 tickets/day) isn't enough to justify a fine-tuning run, and the agent's domain shifts too fast for parametric memory to stay current.
</details>

---

## Exercise 3 — Scratchpad trace (Day 24)

A scratchpad agent is at step 7. Its current state is:

```
Established Facts (4):
- Customer placed order #7821 on May 10th.
- Order status: shipped May 12th, carrier USPS.
- Estimated delivery window: May 14–16.
- Customer contacted us on May 20th — delivery 4 days overdue.

Open Questions (2):
- Has the package been marked "delivered" by USPS but not received?
- Is there a local delivery exception or hold at the post office?

Working Notes (12):
- Checked tracking: last scan was May 14th at regional sort facility.
- No "delivered" scan exists in the system.
- Called USPS API: no exception flags on TRK-7821.
- Package may be lost in transit — need to initiate trace.
- USPS trace request requires 15-day window from ship date — today is day 8.
- We cannot file a trace yet.
- Checked if the carrier shows a different tracking number — none found.
- Customer has not received any other packages in the last 5 days.
- No reports of widespread USPS delays in their zip code.
- Looked at similar cases: 3 of 12 "no scan after sort facility" cases were delivered without final scan.
- Customer's address is confirmed correct in our system.
- Insurance covers up to $100; order value is $74.
```

Answer these without looking at the code:
1. With `COMPRESSION_THRESHOLD_NOTES = 10` and `COMPRESSION_THRESHOLD_TOKENS = 2000`, which threshold fires in the next step check?
2. After compression with `keep_count = 5`, which notes are retained verbatim?
3. Write the one-sentence fact you would generate to compress the 7 oldest notes.
4. After compression, how many Established Facts does the scratchpad have?

<details>
<summary>Reference answers</summary>

1. **Both thresholds fire** — there are 12 working notes, exceeding `COMPRESSION_THRESHOLD_NOTES = 10`. The token count also likely exceeds 2000 for this much text. In the code, the check is `or`, so either threshold firing triggers compression.

2. **Retained notes (5 most recent):**
   - "Looked at similar cases: 3 of 12 'no scan after sort facility' cases were delivered without final scan."
   - "Customer's address is confirmed correct in our system."
   - "Insurance covers up to $100; order value is $74."
   - (The list has 12 notes; `keep_count = 5` retains notes 8–12 in order)
   - "Customer has not received any other packages in the last 5 days."
   - "No reports of widespread USPS delays in their zip code."

3. **Compressed fact (the 7 oldest):**
   "[compressed] Last USPS scan was May 14 at sort facility; no delivered scan, no exception flags, no alternate tracking number. A trace request requires 15 days from ship date — eligible on May 27, 8 days from now."

4. **Established Facts after compression: 5** — original 4 + 1 new compressed fact.
</details>

---

## Exercise 4 — Retrieval strategy match (Day 25)

Match each scenario to the most appropriate retrieval strategy: **similarity**, **recency**, **success-filtered**, or **contrastive**.

**Scenario A:** An agent resolving IT tickets uses episodic memory. A user reports a "VPN not connecting" error. The agent needs to find the most helpful past resolution approach.

**Scenario B:** A coding assistant has been solving Python debugging tasks all week. A new session begins Monday with a fresh Python debugging task. The developer's codebase changes frequently, so last week's workarounds may not apply this week.

**Scenario C:** An agent has been solving a class of tasks for 3 months but has a 40% failure rate on one specific sub-type. You want to improve performance specifically by showing the agent what it tends to do wrong.

**Scenario D:** A tutoring agent wants to show a student both a worked example and a common incorrect approach, to help the student recognize the mistake.

<details>
<summary>Reference answers</summary>

**A → Similarity (success-filtered).** The agent wants the most relevant past resolution for this specific error type. It should only retrieve successful resolutions — a past failure would show an approach that didn't work. Success-filtered similarity is the right choice.

**B → Recency.** The codebase changes frequently, so last month's workarounds may be stale. Recent episodes reflect the current state of the environment better than older ones, even if older ones are semantically more similar to the task.

**C → Contrastive.** You specifically want the agent to see what it gets wrong — retrieve the past failures on this sub-type alongside the successes. The contrastive signal (here's what worked, here's what failed) is exactly what's needed to break the pattern.

**D → Contrastive.** The tutoring use case explicitly needs one positive (worked example) and one negative (common incorrect approach). Contrastive retrieval is designed for this.

Note the pattern: similarity (success-filtered) for finding helpful examples; recency for evolving environments; contrastive for learning from mistakes or teaching from contrast.
</details>

---

## Exercise 5 — Cross-module synthesis (Days 1–25)

[Day 2](../../01-foundations/days/day-02-cognitive-architecture-map.md) introduced four orthogonal components of a cognitive architecture: **action space**, **memory**, **planning**, and **learning** (self-improvement).

Answer these questions without re-reading Day 2:

1. Map each module (01–05) to the component(s) it primarily covers.
2. Which component has received the most coverage across all days?
3. Which component is the least covered so far?
4. Module 06 (Multi-Agent Patterns) is next. Which component do multi-agent architectures primarily address? Why?
5. What is *not yet covered* that a production agent would need? Name at least two specific capabilities.

<details>
<summary>Reference answers</summary>

**1. Module-to-component mapping:**
- Module 01 (Foundations): Action space (tool use primitive, Day 3), Memory (trace reading, Day 4)
- Module 02 (Reasoning Patterns): Planning (CoT, ReAct, ToT, LATS), Memory (Reflexion's verbal memory)
- Module 03 (Self-Improvement): Learning (Self-Refine, STaR, ExpeL, Reflexion)
- Module 04 (Skills & Tools): Action space (skill design, composition, Toolformer, Skill Library)
- Module 05 (Memory): Memory (all four days explicitly)

**2. Most covered: Planning** — CoT, Self-Consistency, ReAct, ToT, LATS (Days 6–13) give planning the most dedicated days. Memory is second with 4 dedicated days plus appearances in Reasoning Patterns and Self-Improvement.

**3. Least covered: Action space design** — Module 04 covers it, but there are no days dedicated to the *types* of action spaces (text output vs. tool use vs. code execution vs. computer control) or to action space evaluation.

**4. Multi-agent primarily addresses: Action space + Planning.** Multiple agents expand the *effective action space* (each agent can specialize in a subset of tools/skills). They also distribute planning — an orchestrator plans, workers execute — which is a structural change to how decisions are made. Memory is less central: agents often have independent memory stores.

**5. Missing so far (two examples):**
- **Evaluation and benchmarking** — how to measure whether an agent is actually improving, not just behaving differently. The course shows agents generating feedback (Self-Refine), but not how to rigorously evaluate agent quality at scale.
- **Safety and guardrails** — how to prevent an agent from taking harmful actions, how to enforce output constraints, how to detect and handle prompt injection. Module 06 includes Day 32 (Guardrails), but this has not appeared yet.
- Other valid answers: cost optimization, latency management, observability/tracing in production, context window management at scale.
</details>

---

## Check your coverage

After completing these exercises, you have actively recalled:
- The four-store memory taxonomy and which patterns map to which stores
- How to design a memory architecture from a requirements specification
- How the scratchpad compression algorithm works at the state level
- When to use each of the four retrieval strategies
- How the five modules fit into the four-component cognitive architecture framework

**What to do if any exercise was hard:**
Re-read *only* the day that covers that topic. Don't re-read the entire module. Targeted re-reading after a retrieval attempt is the most efficient remediation.

---

## Navigation

← **Previous:** [Day 25 — Episodic Memory & Retrieval](./day-25-episodic-memory-retrieval.md)
→ **Next:** [Module 06 — Multi-Agent Patterns](../../06-multi-agent/overview.md)
