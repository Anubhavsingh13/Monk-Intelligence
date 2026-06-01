# Module 06 — Multi-Agent Patterns

**Days 27–32 · 6 pages**

---

## What this module earns you

By the end of Day 32 you can design a multi-agent system for any task that exceeds what a single agent can reliably accomplish — structuring collaboration, managing quality, involving humans at the right moments, and wrapping everything in production-grade guardrails. Specifically:

- **Orchestrator-Worker (Day 27):** One agent decomposes and delegates; specialized worker agents execute; the orchestrator synthesizes. The pattern that makes parallelism and specialization possible.
- **Critic-Actor (Day 28):** A separate critic agent evaluates the actor's output before it is accepted. The internal quality gate that eliminates the need for human review on routine outputs.
- **Peer Debate (Day 29):** Multiple agents propose, rebut, and update positions over several rounds; a judge synthesizes. Adversarial pressure improves factual accuracy on contested questions.
- **Mixture of Agents (Day 30):** Independent proposer agents generate answers; aggregator layers synthesize them into progressively better outputs. Even weaker models as proposers improve the final answer.
- **Human-in-the-Loop (Day 31):** Three interrupt patterns — pre-action, checkpoint, and confidence-based — for involving a human at the right moments without over-interrupting.
- **Guardrails & Production Patterns (Day 32):** Input rails, output rails, and action rails that constrain agent behavior at the boundary, plus circuit breakers and audit logging.

---

## The through-line

Every pattern in Modules 02–05 involves a single agent reasoning, using tools, improving, and remembering. This module asks: *what can a group of agents do that a single agent cannot?*

The answer is threefold:

1. **Specialization.** Different agents can be optimized for different sub-tasks — one for search, one for synthesis, one for critique. No single agent has to be a generalist at everything.
2. **Independent verification.** A separate critic has no stake in the actor's prior output. It can evaluate more objectively than an agent reviewing its own work.
3. **Parallel execution.** Fan-out (Day 20) applied at the agent level — multiple agents working on independent sub-tasks simultaneously rather than sequentially.

The same principles that make tool composition (Day 20) powerful apply here: clear interfaces between components, single responsibilities per agent, explicit failure signaling.

---

## Days

| Day | Title | Load-bearing idea |
|-----|-------|------------------|
| 27 | [Orchestrator-Worker](./days/day-27-orchestrator-worker.md) | One planner + N specialized executors; decompose, delegate, synthesize |
| 28 | [Critic-Actor](./days/day-28-critic-actor.md) | A separate critic evaluates before output is accepted; 3 verdicts: accept / revise / reject |
| 29 | [Peer Debate](./days/day-29-peer-debate.md) | N agents propose and rebut over rounds; adversarial pressure improves factual accuracy |
| 30 | [Mixture of Agents](./days/day-30-mixture-of-agents.md) | Proposers generate; aggregator layers synthesize; each layer improves quality |
| 31 | [Human-in-the-Loop](./days/day-31-human-in-the-loop.md) | Pre-action, checkpoint, and confidence-based interrupts for human oversight |
| 32 | [Guardrails & Production Patterns](./days/day-32-guardrails.md) | Input, output, and action rails; circuit breakers; audit logging |

---

← [Module 05 — Memory Patterns](../05-memory/overview.md)
→ [Module 07 — Capstone](../07-capstone/overview.md)
