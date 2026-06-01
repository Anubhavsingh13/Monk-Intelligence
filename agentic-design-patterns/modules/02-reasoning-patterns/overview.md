# Module 02 — Reasoning Patterns

**Days 6–14 · 8 pages + 1 rest day**

---

## What this module earns you

By the end of this module you can implement every major single-agent reasoning pattern from scratch, explain when each one is worth its cost, and choose the right pattern for a given task. Specifically:

- **Chain-of-Thought (Day 6):** Make any LLM show its reasoning — the prerequisite for every pattern below.
- **Self-Consistency (Day 7):** Improve reliability without changing the model or the prompt — just sample more.
- **ReAct (Days 8–9):** Interleave reasoning and action in a loop — the most widely deployed reasoning pattern. You will implement it in ~80 lines of Python.
- **Reflexion (Day 11):** Let an agent learn from its own failures without gradient updates.
- **Tree of Thoughts (Day 12):** Give an agent the ability to explore and backtrack over reasoning paths.
- **LATS (Day 13):** Combine tree search with environment feedback — the most complete single-pattern treatment of reasoning + acting + planning.
- **Thinking Fast and Slow (Day 14):** Learn the meta-decision: *which reasoning mode should this task use?*

---

## The through-line

Every pattern in this module is an answer to the same question: *how does an agent decide what to do next?*

Chain-of-Thought says: write your reasoning down. Self-Consistency says: write it down several times and vote. ReAct says: interleave the writing with real actions. Reflexion says: remember what went wrong last time. Tree of Thoughts says: explore multiple paths before committing. LATS says: search the tree with rollouts. Thinking Fast and Slow says: choose which of these to use.

By Day 14, you will have a complete decision framework for single-agent reasoning. The multi-agent patterns (Module 6) are then simply this framework applied at scale.

---

## Days

| Day | Title | Load-bearing idea |
|-----|-------|------------------|
| 6 | [Chain-of-Thought](./days/day-06-chain-of-thought.md) | Making reasoning explicit and textual improves correctness |
| 7 | [Self-Consistency](./days/day-07-self-consistency.md) | Sampling multiple paths and voting is more reliable than greedy decoding |
| 8 | [ReAct — Interleaving Thought and Action](./days/day-08-react.md) | Interleaving reasoning traces with tool calls creates tighter feedback |
| 9 | [Implementing ReAct from Scratch](./days/day-09-react-implementation.md) | The pattern is an 80-line loop: think → act → observe → repeat |
| 10 | [Rest & Synthesize II](./days/day-10-rest-synthesize-ii.md) | *(consolidation)* |
| 11 | [Reflexion — Verbal Reinforcement](./days/day-11-reflexion.md) | An agent that critiques its own trajectory can self-improve without retraining |
| 12 | [Tree of Thoughts](./days/day-12-tree-of-thoughts.md) | Deliberate search over partial reasoning states beats single-path greedy |
| 13 | [LATS — Language Agent Tree Search](./days/day-13-lats.md) | Tree search + MCTS rollouts unifies reasoning, acting, and planning |
| 14 | [Thinking Fast and Slow](./days/day-14-thinking-fast-slow.md) | The first architectural choice is which reasoning mode to engage |

---

← [Module 01 — Foundations](../01-foundations/overview.md)
→ [Module 03 — Self-Improvement Patterns](../03-self-improvement/overview.md)
