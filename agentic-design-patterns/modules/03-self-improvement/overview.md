# Module 03 — Self-Improvement Patterns

**Days 15–18 · 3 pages + 1 rest day**

---

## What this module earns you

By the end of Day 18 you can place any self-improvement mechanism on a precise spectrum — from within-run critique to parametric weight updates — and choose the right one for a given task and compute budget. Specifically:

- **Self-Refine (Day 15):** The tightest loop — an agent that critiques and revises its own output within a single run, no external feedback, no memory required.
- **STaR (Day 16):** The parametric end of the spectrum — an LLM that bootstraps its own reasoning capability by generating, filtering, and fine-tuning on self-created rationales.
- **ExpeL (Day 17):** The middle path — an agent that mines its own past trajectories for generalizable rules and retrieves them at inference time, without fine-tuning.
- **Rest & Synthesize III (Day 18):** Map all four improvement patterns (Self-Refine, Reflexion, ExpeL, STaR) onto the spectrum; connect each to the memory taxonomy from Day 2.

---

## The through-line

[Module 2](../02-reasoning-patterns/overview.md) ended with [Reflexion (Day 11)](../02-reasoning-patterns/days/day-11-reflexion.md) — the first pattern that improved across episodes using verbal lessons. This module extends that idea across three dimensions:

```
DIMENSION 1: When does improvement happen?
  Within one run          →  Self-Refine
  Across runs (verbal)    →  Reflexion   (Day 11)
  Across runs (mined)     →  ExpeL
  Across runs (weights)   →  STaR

DIMENSION 2: What is the improvement stored as?
  Nowhere (ephemeral)     →  Self-Refine
  Text in context         →  Reflexion
  Retrieved rules         →  ExpeL
  Model weights           →  STaR

DIMENSION 3: What does it cost?
  ~3× a single generation →  Self-Refine
  ~3× per trial           →  Reflexion
  Trajectory collection + inference overhead → ExpeL
  GPU training + data pipeline → STaR
```

By Day 18 you can read this three-dimensional trade-off fluently — and explain why most production agents use Self-Refine or Reflexion, while STaR is reserved for teams with fine-tuning infrastructure.

---

## Days

| Day | Title | Load-bearing idea |
|-----|-------|------------------|
| 15 | [Self-Refine — Iterative Self-Feedback](./days/day-15-self-refine.md) | An agent can improve any output by critiquing and re-drafting it in a synchronous loop |
| 16 | [STaR — Self-Taught Reasoner](./days/day-16-star.md) | An LLM can bootstrap reasoning capability by filtering self-generated rationales |
| 17 | [ExpeL — Experiential Learning from Trajectories](./days/day-17-expel.md) | Mining past trajectories for generalizable rules enables cross-episode improvement without fine-tuning |
| 18 | [Rest & Synthesize III — The Self-Improvement Spectrum](./days/day-18-rest-synthesize-iii.md) | *(consolidation)* |

---

← [Module 02 — Reasoning Patterns](../02-reasoning-patterns/overview.md)
→ [Module 04 — Skills & Tools](../04-skills-and-tools/overview.md)
