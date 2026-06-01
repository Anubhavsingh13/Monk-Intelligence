# Module 04 — Skills & Tools

**Days 19–22 · 4 pages**

---

## What this module earns you

By the end of Day 22 you can design a skill library for any agent system — with well-specified atomic tools, clean composition patterns, and a mechanism for the agent to learn new skills from experience. Specifically:

- **The Skill Design Pattern (Day 19):** What distinguishes a brittle tool from a composable skill — and why the design of your tools determines the ceiling of your agent.
- **Atomic Tool Design + Composition (Day 20):** How to apply the Unix philosophy to agent tools, and the three structural compositions (chain, fan-out, aggregate) that build complex behavior from simple primitives.
- **Toolformer (Day 21):** How an LLM can learn *when and how* to use tools from self-generated examples — contrasting prompt-engineered with learned tool use.
- **Skill Library (Day 22):** A persistent, growing library of verified skills that enables an agent to tackle novel tasks by composing past solutions (Voyager pattern).

---

## The through-line

[Module 1 (Day 3)](../01-foundations/days/day-03-tool-use-primitive.md) established that an agent's power is its action space. This module asks the design question: *how do you build an action space that grows with the agent's capability?*

The answer is a progression:
1. Design individual tools correctly (atomic, loud failures, clear contracts)
2. Compose them in structured patterns (chain, fan-out, aggregate)
3. Teach the agent to learn tool use rather than hard-coding every usage pattern
4. Build a library that persists and grows across tasks

An agent with a well-designed skill library eventually solves novel tasks by *composing what it already knows* — like a senior engineer who reaches for the right pattern instinctively.

---

## Days

| Day | Title | Load-bearing idea |
|-----|-------|------------------|
| 19 | [The Skill Design Pattern](./days/day-19-skill-design-pattern.md) | A skill is a composable capability with a defined input/output contract |
| 20 | [Atomic Tool Design + Composition Patterns](./days/day-20-tool-design-composition.md) | One job per tool; chain, fan-out, and aggregate build complex behavior |
| 21 | [Toolformer — Self-Taught Tool Use](./days/day-21-toolformer.md) | An LLM can learn when and how to invoke tools from self-generated examples |
| 22 | [The Skill Library Pattern](./days/day-22-skill-library.md) | A persistent, growing library of verified skills enables compound capability |

---

← [Module 03 — Self-Improvement Patterns](../03-self-improvement/overview.md)
→ [Module 05 — Memory Patterns](../05-memory/overview.md)
