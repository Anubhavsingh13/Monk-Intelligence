# Module 07 — Capstone

**Day 33 · 1 page**

---

## What this module earns you

One complete, annotated agent system that integrates patterns from all six prior modules — designed, implemented, and reflected upon. By the end of Day 33 you can:

- Draw a complete cognitive architecture diagram for a multi-pattern agent and label each component with the pattern that implements it
- Wire together ReAct, episodic memory, a skill library, a critic-actor quality gate, human-in-the-loop interrupts, and guardrails into a single coherent system
- Reason about which patterns to combine vs. keep separate for a given production use case
- Identify the failure modes of the *combined* system — not just individual patterns in isolation

---

## The through-line

The course began with one question: *how do you build an action space that grows with the agent's capability?*

The answer unfolded over six modules:

1. **Foundations (Days 1–5):** Agents are cognitive architectures with four orthogonal components — action space, memory, planning, learning.
2. **Reasoning Patterns (Days 6–14):** CoT, ReAct, ToT, and LATS are planning strategies — they determine *how the agent reasons* before acting.
3. **Self-Improvement (Days 15–18):** Self-Refine, Reflexion, STaR, ExpeL are learning strategies — they determine *how the agent improves* from experience.
4. **Skills & Tools (Days 19–22):** Skill design, composition, Toolformer, and Skill Library determine *what the agent can do* and how its action space grows.
5. **Memory (Days 23–26):** Four memory stores — in-context, episodic, semantic, parametric — determine *what the agent remembers* and for how long.
6. **Multi-Agent (Days 27–32):** Orchestrator-Worker, Critic-Actor, Peer Debate, MoA, HITL, Guardrails determine *how agents collaborate* and how systems stay safe.

The capstone assembles one piece from each module into a single agent architecture, and asks you to reason about why each piece is there.

---

## Day

| Day | Title | What you build |
|-----|-------|---------------|
| 33 | [Capstone — Research Assistant Agent](./days/day-33-capstone.md) | A multi-pattern agent integrating ReAct + Skill Library + Episodic Memory + Critic-Actor + Human-in-the-Loop + Guardrails |

---

← [Module 06 — Multi-Agent Patterns](../06-multi-agent/overview.md)
→ [Course Home](../../README.md)
