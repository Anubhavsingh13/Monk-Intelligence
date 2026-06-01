# Learning Path — Agentic Design Patterns

Full 33-day path with rationale for every sequencing decision.

---

## The design principle

Each day has exactly one load-bearing idea. Prerequisites are strict: a page is only placed after everything it depends on has been earned. Rest & Synthesize days are mandatory — they are when consolidation happens.

---

## Full path

| Day | Title | Load-Bearing Idea | Why It Comes Now | Prereqs |
|-----|-------|-------------------|-----------------|---------|
| 1 | [What Is an Agentic Design Pattern?](./modules/01-foundations/days/day-01-what-is-a-design-pattern.md) | Design patterns are a vocabulary of reusable solutions | Establishes pattern-lens before any specific pattern | None |
| 2 | [The Cognitive Architecture Map](./modules/01-foundations/days/day-02-cognitive-architecture-map.md) | Every agent decomposes into action, memory, planning, learning | Stable coordinate system for all future patterns | Day 1 |
| 3 | [Tool Use as First-Class Primitive](./modules/01-foundations/days/day-03-tool-use-primitive.md) | An agent's power is its action space | Tools underpin every reasoning and multi-agent pattern | Day 2 |
| 4 | [Reading an Agent Trace](./modules/01-foundations/days/day-04-reading-agent-trace.md) | The trace is ground truth | Grounds abstract concepts in observable behavior | Day 3 |
| 5 | [Rest & Synthesize I](./modules/01-foundations/days/day-05-rest-synthesize-i.md) | *(consolidation)* | Fuse Days 1–4 into one durable mental model | Days 1–4 |
| 6 | [Chain-of-Thought](./modules/02-reasoning-patterns/days/day-06-chain-of-thought.md) | Making reasoning explicit improves correctness | CoT is the primitive for ReAct, ToT, every reasoning pattern | Day 4 |
| 7 | [Self-Consistency](./modules/02-reasoning-patterns/days/day-07-self-consistency.md) | Sampling multiple paths and voting is more reliable than greedy | Extends CoT; motivates tree-based search in Days 12–13 | Day 6 |
| 8 | [ReAct — Interleaving Thought and Action](./modules/02-reasoning-patterns/days/day-08-react.md) | Interleaving reasoning traces with tool calls creates tighter feedback | The foundational pattern — almost every agent uses it | Days 6, 3 |
| 9 | [Implementing ReAct from Scratch](./modules/02-reasoning-patterns/days/day-09-react-implementation.md) | The pattern is an 80-line loop: think → act → observe → repeat | Solidifies understanding; reveals exactly where the pattern breaks | Day 8 |
| 10 | [Rest & Synthesize II](./modules/02-reasoning-patterns/days/day-10-rest-synthesize-ii.md) | *(consolidation)* | Rebuild a ReAct trace from memory; label each step | Days 6–9 |
| 11 | [Reflexion — Verbal Reinforcement](./modules/02-reasoning-patterns/days/day-11-reflexion.md) | An agent that critiques its own trajectory can self-improve without retraining | Natural extension of ReAct; introduces the feedback-loop meta-pattern | Day 9 |
| 12 | [Tree of Thoughts](./modules/02-reasoning-patterns/days/day-12-tree-of-thoughts.md) | Deliberate search over partial reasoning states beats single-path greedy | Generalizes CoT and Self-Consistency into explicit search | Days 7, 11 |
| 13 | [LATS — Language Agent Tree Search](./modules/02-reasoning-patterns/days/day-13-lats.md) | Tree search + MCTS rollouts unifies reasoning, acting, planning | Shows how ToT scales to interactive environments | Day 12 |
| 14 | [Thinking Fast and Slow](./modules/02-reasoning-patterns/days/day-14-thinking-fast-slow.md) | The first architectural choice is which reasoning mode to engage | Synthesizes Days 6–13 into a decision framework | Days 6–13 |
| 15 | [Self-Refine — Iterative Self-Feedback](./modules/03-self-improvement/days/day-15-self-refine.md) | An agent can improve any output by critiquing and re-drafting in a loop | Simplest self-improvement pattern — within-run, no memory | Day 11 |
| 16 | [STaR — Self-Taught Reasoner](./modules/03-self-improvement/days/day-16-star.md) | An LLM can bootstrap reasoning by filtering self-generated rationales | Extends improvement from prompt-time to weight-time | Days 11, 6 |
| 17 | [ExpeL — Experiential Learning from Trajectories](./modules/03-self-improvement/days/day-17-expel.md) | Mining past trajectories for generalizable rules enables cross-episode learning | Bridges Reflexion and STaR — episodic-to-knowledge pattern | Days 11, 15 |
| 18 | [Rest & Synthesize III — Self-Improvement Spectrum](./modules/03-self-improvement/days/day-18-rest-synthesize-iii.md) | *(consolidation)* | Map all 4 improvement patterns onto the spectrum | Days 11, 15–17 |
| 19 | [The Skill Design Pattern](./modules/04-skills-and-tools/days/day-19-skill-design-pattern.md) | A skill is a composable capability with a defined input/output contract | Shifts focus from reasoning to capability design | Days 3, 9 |
| 20 | [Atomic Tool Design + Composition Patterns](./modules/04-skills-and-tools/days/day-20-tool-design-composition.md) | One job per tool; chain, fan-out, aggregate for complex behavior | Practical design rules + three composition structures | Day 19 |
| 21 | [Toolformer — Self-Taught Tool Use](./modules/04-skills-and-tools/days/day-21-toolformer.md) | An LLM can learn when to invoke tools from self-generated examples | Contrasts prompt-engineered vs. learned tool use | Days 20, 16 |
| 22 | [The Skill Library Pattern](./modules/04-skills-and-tools/days/day-22-skill-library.md) | A persistent, growing library of verified skills enables compound capability | Combines skill design + ExpeL-style memory | Days 19, 17 |
| 23 | [Memory Taxonomy](./modules/05-memory/days/day-23-memory-taxonomy.md) | Agents have four orthogonal memory stores with distinct properties | Vocabulary for Days 24–25; connects to ExpeL and Skill Library | Day 22 |
| 24 | [Scratchpad and Working Memory Patterns](./modules/05-memory/days/day-24-working-memory.md) | What to keep, compress, or offload in the context window is architectural | Most immediately actionable memory pattern | Day 23 |
| 25 | [Episodic Memory and Retrieval Patterns](./modules/05-memory/days/day-25-episodic-memory.md) | Storing and retrieving past trajectories enables learning without fine-tuning | Bridges memory taxonomy to RAG-style retrieval | Day 24 |
| 26 | [Rest & Synthesize IV](./modules/05-memory/days/day-26-rest-synthesize-iv.md) | *(consolidation)* | Connect the full self-improvement + memory arc | Days 15–25 |
| 27 | [Orchestrator-Worker Pattern](./modules/06-multi-agent/days/day-27-orchestrator-worker.md) | A central orchestrator decomposes tasks and dispatches to specialists | First multi-agent pattern; most common production topology | Days 9, 19 |
| 28 | [Critic-Actor Pattern](./modules/06-multi-agent/days/day-28-critic-actor.md) | Decoupling generation from evaluation improves quality through adversarial feedback | Extends Self-Refine/Reflexion to cross-agent critique | Days 15, 27 |
| 29 | [Peer Debate and Society of Mind](./modules/06-multi-agent/days/day-29-peer-debate.md) | Multiple agents with distinct personas arguing toward consensus surface novel perspectives | Extends multi-agent to horizontal coordination | Day 27 |
| 30 | [Mixture of Agents](./modules/06-multi-agent/days/day-30-mixture-of-agents.md) | Routing tasks to specialists and aggregating outputs is a parallelizable architecture | Combines specialization + aggregation at scale | Days 27, 29 |
| 31 | [Human-in-the-Loop Patterns](./modules/06-multi-agent/days/day-31-human-in-the-loop.md) | Deciding when to pause for human input is itself a design pattern | Connects multi-agent patterns to deployment reality | Day 30 |
| 32 | [Guardrails and Production Patterns](./modules/06-multi-agent/days/day-32-guardrails-production.md) | Validation, filtering, retry, and observability are patterns — not afterthoughts | Closes the implementation arc | Days 27–31 |
| 33 | [Capstone](./modules/07-capstone/days/day-33-capstone.md) | Design, implement, and annotate a multi-pattern agent system with a self-improvement loop | Forces integration of the entire course | Days 1–32 |

---

## Module summary

| Module | Days | Theme |
|--------|------|-------|
| 01 Foundations | 1–5 | Vocabulary, cognitive map, tool use, trace reading |
| 02 Reasoning Patterns | 6–14 | CoT, Self-Consistency, ReAct, Reflexion, ToT, LATS, Fast/Slow |
| 03 Self-Improvement | 15–18 | Self-Refine, STaR, ExpeL, the improvement spectrum |
| 04 Skills & Tools | 19–22 | Skill design, atomic tools, Toolformer, Skill Library |
| 05 Memory | 23–26 | Taxonomy, working memory, episodic memory |
| 06 Multi-Agent | 27–32 | Orchestrator-Worker, Critic-Actor, Debate, MoA, HITL, Guardrails |
| 07 Capstone | 33 | Integration project |

---

← [README](./README.md) · [bibliography.md](./bibliography.md) · [glossary.md](./glossary.md)
