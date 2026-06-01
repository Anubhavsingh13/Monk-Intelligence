# Module 05 — Memory Patterns

**Days 23–26 · 4 pages**

---

## What this module earns you

By the end of Day 26 you can design the memory subsystem for any agent — choosing the right store for each type of information, implementing a scratchpad for deliberate working memory, and building an episodic memory that makes the agent improve with experience. Specifically:

- **Memory Taxonomy (Day 23):** The four memory stores — in-context, external episodic, external semantic, and parametric — and how every pattern in this course maps onto this taxonomy.
- **Scratchpad & Working Memory (Day 24):** How to turn the context window from a passive buffer into a structured workspace with deliberate slots, compression, and selective retention.
- **Episodic Memory & Retrieval (Day 25):** How to store past trajectories and retrieve them by semantic similarity so that an agent improves with experience — the RAG pattern applied to agent history.
- **Rest & Synthesize IV (Day 26):** Active recall across all five modules.

---

## The through-line

[Day 2](../01-foundations/days/day-02-cognitive-architecture-map.md) placed "memory" on the cognitive architecture map as one of four orthogonal components. Every pattern in Modules 02–04 *uses* memory implicitly:

- Chain-of-Thought uses in-context working memory (the reasoning trace)
- Reflexion uses external episodic memory (verbal lessons across trials)
- ExpeL uses external semantic memory (an indexed insight pool)
- STaR updates parametric memory (the weights)
- Skill Library is external semantic memory (verified executable skills)

This module makes the memory dimension explicit and designable. Instead of memory "happening" as a side effect of running a pattern, you choose the right store, design the read/write protocol, and manage capacity deliberately.

---

## Days

| Day | Title | Load-bearing idea |
|-----|-------|------------------|
| 23 | [Memory Taxonomy](./days/day-23-memory-taxonomy.md) | Four stores with different cost, capacity, and decay trade-offs |
| 24 | [Scratchpad & Working Memory](./days/day-24-scratchpad-working-memory.md) | The context window as a deliberate, structured workspace |
| 25 | [Episodic Memory & Retrieval](./days/day-25-episodic-memory-retrieval.md) | Past trajectories retrieved by similarity make agents improve over time |
| 26 | [Rest & Synthesize IV](./days/day-26-rest-synthesize-iv.md) | Active recall across all five modules |

---

← [Module 04 — Skills & Tools](../04-skills-and-tools/overview.md)
→ [Module 06 — Multi-Agent Patterns](../06-multi-agent/overview.md)
