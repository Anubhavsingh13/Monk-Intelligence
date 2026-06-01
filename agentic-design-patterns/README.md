# Agentic Design Patterns

> **The promise:** By the end of this course, you can look at any agent system, name every design pattern it uses, implement each pattern from scratch in Python, and reason precisely about where and why each one will break.

---

## Who this course is for

This course targets **L2 — Builder** depth. You already know what an agent is, have called an LLM API, and have probably built something with LangChain, AutoGen, or a similar framework. What you lack is a **shared vocabulary** — a way to say "this system uses ReAct with an ExpeL-style skill library and a Critic-Actor multi-agent layer" and have that sentence carry precise meaning.

If you have never built an agent before, start with Chip Huyen's *AI Engineering* (Ch. 7–9) first, then return here. If you want researcher-level depth (reading papers, contributing to the field), supplement each day with the full papers cited — this course gives you the scaffolding to read them productively.

---

## The arc at a glance

On Day 1 you arrive with working knowledge of agents but no shared vocabulary. By Day 5 you have a stable cognitive map — four orthogonal components, a pattern-lens, and the ability to read any agent execution trace and locate where decisions are being made. By Day 10 you can implement ReAct from scratch in 80 lines of Python, explain why self-consistency beats greedy decoding, and articulate what Reflexion adds on top. Every reasoning pattern you will ever encounter is a variation on the structures built in this first arc.

The middle arc (Days 11–25) layers in richer patterns: deliberate tree-search reasoning, the self-improvement spectrum (within-run refinement through parametric self-bootstrapping), the skill design vocabulary that separates brittle one-off agents from composable systems, and the four memory stores that determine what an agent can remember and learn. The final arc (Days 26–32) scales to multi-agent architectures — orchestration, critique, debate, mixture — and closes with production concerns that are often treated as afterthoughts but are themselves patterns. The capstone forces you to choose, justify, and implement patterns from across the course in a single working system.

---

## How to use this course

**Rhythm.** One page per day, 30–45 minutes of focused reading. Do not stack days — the spacing is intentional.

**Exercises.** Non-optional for L2. Write the code. The pattern does not become yours until you have debugged it at least once.

**Rest & Synthesize days.** Do not skip them. These are the days the material consolidates. No new reading — just rebuild from memory, draw diagrams, explain it to yourself.

**bibliography.md vs. keep moving.** If the day's idea clicks, keep moving. If something feels shaky, check `bibliography.md` for the primary source — every paper is pointed at a specific section. Don't read whole books mid-course unless the curiosity is burning.

**If life gets in the way.** Missing one day is fine — resume where you left off. Missing a week: re-read the last Rest & Synthesize day before continuing.

---

## The learning path

| Day | Title | One Idea | Module |
|-----|-------|----------|--------|
| 1 | [What Is an Agentic Design Pattern?](./modules/01-foundations/days/day-01-what-is-a-design-pattern.md) | Design patterns are a vocabulary of reusable solutions | Foundations |
| 2 | [The Cognitive Architecture Map](./modules/01-foundations/days/day-02-cognitive-architecture-map.md) | Every agent decomposes into action, memory, planning, learning | Foundations |
| 3 | [Tool Use as First-Class Primitive](./modules/01-foundations/days/day-03-tool-use-primitive.md) | An agent's power is its action space | Foundations |
| 4 | [Reading an Agent Trace](./modules/01-foundations/days/day-04-reading-agent-trace.md) | The trace is ground truth — everything else is theory | Foundations |
| 5 | [Rest & Synthesize I](./modules/01-foundations/days/day-05-rest-synthesize-i.md) | *(consolidation)* | Foundations |
| 6 | [Chain-of-Thought](./modules/02-reasoning-patterns/days/day-06-chain-of-thought.md) | Making reasoning explicit and textual improves correctness | Reasoning |
| 7 | [Self-Consistency](./modules/02-reasoning-patterns/days/day-07-self-consistency.md) | Sampling multiple paths and voting is more reliable than greedy decoding | Reasoning |
| 8 | [ReAct — Interleaving Thought and Action](./modules/02-reasoning-patterns/days/day-08-react.md) | Interleaving reasoning traces with tool calls creates tighter feedback | Reasoning |
| 9 | [Implementing ReAct from Scratch](./modules/02-reasoning-patterns/days/day-09-react-implementation.md) | The pattern is an 80-line loop: think → act → observe → repeat | Reasoning |
| 10 | [Rest & Synthesize II](./modules/02-reasoning-patterns/days/day-10-rest-synthesize-ii.md) | *(consolidation)* | Reasoning |
| 11 | [Reflexion — Verbal Reinforcement](./modules/02-reasoning-patterns/days/day-11-reflexion.md) | An agent that critiques its own trajectory can self-improve without retraining | Reasoning |
| 12 | [Tree of Thoughts](./modules/02-reasoning-patterns/days/day-12-tree-of-thoughts.md) | Deliberate search over partial reasoning states beats single-path greedy | Reasoning |
| 13 | [LATS — Language Agent Tree Search](./modules/02-reasoning-patterns/days/day-13-lats.md) | Tree search + Monte Carlo rollouts unifies reasoning, acting, and planning | Reasoning |
| 14 | [Thinking Fast and Slow — Choosing Your Reasoning Mode](./modules/02-reasoning-patterns/days/day-14-thinking-fast-slow.md) | The first architectural choice is which reasoning mode to engage | Reasoning |
| 15 | [Self-Refine — Iterative Self-Feedback](./modules/03-self-improvement/days/day-15-self-refine.md) | An agent can improve any output by critiquing and re-drafting it in a loop | Self-Improvement |
| 16 | [STaR — Self-Taught Reasoner](./modules/03-self-improvement/days/day-16-star.md) | An LLM can bootstrap reasoning capability by filtering self-generated rationales | Self-Improvement |
| 17 | [ExpeL — Experiential Learning from Trajectories](./modules/03-self-improvement/days/day-17-expel.md) | Mining past trajectories for generalizable rules enables cross-episode learning | Self-Improvement |
| 18 | [Rest & Synthesize III — The Self-Improvement Spectrum](./modules/03-self-improvement/days/day-18-rest-synthesize-iii.md) | *(consolidation)* | Self-Improvement |
| 19 | [The Skill Design Pattern](./modules/04-skills-and-tools/days/day-19-skill-design-pattern.md) | A skill is a composable capability with a defined input/output contract | Skills & Tools |
| 20 | [Atomic Tool Design + Composition Patterns](./modules/04-skills-and-tools/days/day-20-tool-design-composition.md) | One job per tool; chain, fan-out, and aggregate to compose complex behaviour | Skills & Tools |
| 21 | [Toolformer — Self-Taught Tool Use](./modules/04-skills-and-tools/days/day-21-toolformer.md) | An LLM can learn when and how to invoke tools from self-generated examples | Skills & Tools |
| 22 | [The Skill Library Pattern](./modules/04-skills-and-tools/days/day-22-skill-library.md) | A persistent, growing library of verified skills enables compound capability | Skills & Tools |
| 23 | [Memory Taxonomy](./modules/05-memory/days/day-23-memory-taxonomy.md) | Agents have four orthogonal memory stores with distinct properties | Memory |
| 24 | [Scratchpad and Working Memory Patterns](./modules/05-memory/days/day-24-working-memory.md) | What to keep, compress, or offload in the context window is an architectural decision | Memory |
| 25 | [Episodic Memory and Retrieval Patterns](./modules/05-memory/days/day-25-episodic-memory.md) | Storing and retrieving past trajectories lets agents learn without fine-tuning | Memory |
| 26 | [Rest & Synthesize IV](./modules/05-memory/days/day-26-rest-synthesize-iv.md) | *(consolidation)* | Memory |
| 27 | [Orchestrator-Worker Pattern](./modules/06-multi-agent/days/day-27-orchestrator-worker.md) | A central orchestrator decomposes tasks and dispatches to specialist workers | Multi-Agent |
| 28 | [Critic-Actor Pattern](./modules/06-multi-agent/days/day-28-critic-actor.md) | Decoupling generation from evaluation improves quality through adversarial feedback | Multi-Agent |
| 29 | [Peer Debate and Society of Mind](./modules/06-multi-agent/days/day-29-peer-debate.md) | Multiple agents with distinct personas arguing toward consensus surface novel perspectives | Multi-Agent |
| 30 | [Mixture of Agents](./modules/06-multi-agent/days/day-30-mixture-of-agents.md) | Routing tasks to specialists and aggregating their outputs is a parallelizable architecture | Multi-Agent |
| 31 | [Human-in-the-Loop Patterns](./modules/06-multi-agent/days/day-31-human-in-the-loop.md) | Deciding when to pause for human input is itself a design pattern | Multi-Agent |
| 32 | [Guardrails and Production Patterns](./modules/06-multi-agent/days/day-32-guardrails-production.md) | Validation, filtering, retry, and observability are patterns — not afterthoughts | Multi-Agent |
| 33 | [Capstone](./modules/07-capstone/days/day-33-capstone.md) | Design and implement a multi-pattern agent system with a self-improvement loop | Capstone |

---

## The Course Shelf

Top 5 must-have sources. Full annotated list in [`bibliography.md`](./bibliography.md).

| Source | Why it matters |
|--------|---------------|
| Sumers et al. (2024) — *Cognitive Architectures for Language Agents* | The unified taxonomy the entire course is built around |
| Yao et al. (2023) — *ReAct* | The foundational reasoning+acting pattern paper |
| Anthropic — *Building Effective Agents* (2024) | The most opinionated practical guide to production patterns |
| Karpathy — *Intro to Large Language Models* (2023) | The LLM-as-OS framing; best intuition for cognitive architecture |
| Chip Huyen — *AI Engineering* (2025) | The engineering and production spine of the course |

---

## The capstone

On Day 33 you will design, implement, and annotate a working agent system that: (1) uses at least three named patterns from different modules, (2) includes at least one self-improvement loop, and (3) includes a written pattern rationale — a short document explaining *why* you chose each pattern, what alternatives you rejected, and where your system will break. "Done" means another engineer could read your rationale, disagree with a specific pattern choice, and point precisely at which trade-off they would make differently.

---

## Glossary

[`glossary.md`](./glossary.md) — alphabetical, with day of first use.

---

## Meta

- **Depth level:** L2 — Builder
- **Estimated total hours:** 33 days × 40 min ≈ 22 hours of reading + exercise time
- **Last updated:** 2026-05-28
- **Feedback:** Annotate any page with a comment if something doesn't click — the confusion is data.
