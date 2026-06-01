# Monk Intelligence Wiki

> *A library of structured, concept-first courses for building durable expertise in AI, decision-making, and the engineering workflows that surround both.*

---

## What this is

Each course in this library is built on the same principle: **understanding compounds, information decays**. The goal is not to teach the current version of a tool — it is to build the mental models that make every future version obvious.

Courses are designed to be read in order, one page per day, 30–45 minutes of focused work. There are no videos, no quizzes, and no certificates. There are first principles, worked examples, and exercises designed so that understanding either clicks or visibly doesn't — and you know which.

---

## Course Catalogue

### 🤖 Agentic Learning Path

*The conceptual foundation for everything else in this library.*

A technology-agnostic, concept-first progression through the building blocks of modern AI systems — from how LLMs work to multi-agent architectures, RAG, evaluation, and production deployment. Designed to be durable: the ideas here remain true as tools change.

**Format:** 5 modules · Reference depth  
**Best for:** Anyone building or directing AI systems who wants the *why* before the *how*

**→ [Start here](<Agentic_learning path/README.md>)**

---

### 🧩 Agentic Design Patterns

*33 days from "I've built agents" to "I can name, implement, and stress-test every pattern in the field."*

A vocabulary-first, builder-depth course covering every major agentic design pattern — from Chain-of-Thought and ReAct through Tree of Thoughts, self-improvement loops (Self-Refine, STaR, ExpeL), skill libraries, four memory stores, and the full multi-agent stack (Orchestrator-Worker, Critic-Actor, Peer Debate, Mixture of Agents, Guardrails). Exercises are non-optional: the pattern doesn't become yours until you've debugged it at least once.

**Format:** 33 days · 7 modules · ~22 hours  
**Depth:** L2 — Builder  
**Best for:** Engineers who have called an LLM API and built something with LangChain, AutoGen, or a similar framework, and want a precise shared vocabulary — the kind where "this system uses ReAct with an ExpeL-style skill library and a Critic-Actor multi-agent layer" carries exact meaning

**→ [Start Day 1](agentic-design-patterns/README.md)**

#### The arc

**Days 1–5 (Foundations):** Shared vocabulary — four cognitive-architecture components, the pattern-lens, reading agent execution traces.

**Days 6–14 (Reasoning Patterns):** Chain-of-Thought → Self-Consistency → ReAct (implemented from scratch in 80 lines of Python) → Reflexion → Tree of Thoughts → LATS → Fast/Slow reasoning mode selection.

**Days 15–26 (Self-Improvement + Memory):** Self-Refine → STaR → ExpeL → Skill design → Atomic tool composition → Toolformer → Skill Library → Memory taxonomy → Working memory → Episodic memory.

**Days 27–32 (Multi-Agent):** Orchestrator-Worker → Critic-Actor → Peer Debate → Mixture of Agents → Human-in-the-Loop → Guardrails and production patterns.

**Day 33 (Capstone):** Design, implement, and annotate a working agent system using at least three named patterns from different modules, one self-improvement loop, and a written pattern rationale.

#### Modules

| Module | Days | Theme |
| -------- | ------ | ------- |
| [01 Foundations](agentic-design-patterns/modules/01-foundations/overview.md) | 1–5 | Vocabulary, cognitive map, tool use, trace reading |
| [02 Reasoning Patterns](agentic-design-patterns/modules/02-reasoning-patterns/overview.md) | 6–14 | CoT, Self-Consistency, ReAct, Reflexion, ToT, LATS, Fast/Slow |
| [03 Self-Improvement](agentic-design-patterns/modules/03-self-improvement/overview.md) | 15–18 | Self-Refine, STaR, ExpeL, the improvement spectrum |
| [04 Skills & Tools](agentic-design-patterns/modules/04-skills-and-tools/overview.md) | 19–22 | Skill design, atomic tools, Toolformer, Skill Library |
| [05 Memory](agentic-design-patterns/modules/05-memory/overview.md) | 23–26 | Taxonomy, working memory, episodic memory |
| [06 Multi-Agent](agentic-design-patterns/modules/06-multi-agent/overview.md) | 27–32 | Orchestrator-Worker, Critic-Actor, Debate, MoA, HITL, Guardrails |
| [07 Capstone](agentic-design-patterns/modules/07-capstone/overview.md) | 33 | Integration project |

#### Full learning path

| Day | Title | One Idea | Module |
| ----- | ------- | ---------- | -------- |
| 1 | [What Is an Agentic Design Pattern?](agentic-design-patterns/modules/01-foundations/days/day-01-what-is-a-design-pattern.md) | Design patterns are a vocabulary of reusable solutions | Foundations |
| 2 | [The Cognitive Architecture Map](agentic-design-patterns/modules/01-foundations/days/day-02-cognitive-architecture-map.md) | Every agent decomposes into action, memory, planning, learning | Foundations |
| 3 | [Tool Use as First-Class Primitive](agentic-design-patterns/modules/01-foundations/days/day-03-tool-use-primitive.md) | An agent's power is its action space | Foundations |
| 4 | [Reading an Agent Trace](agentic-design-patterns/modules/01-foundations/days/day-04-reading-agent-trace.md) | The trace is ground truth — everything else is theory | Foundations |
| 5 | [Rest & Synthesize I](agentic-design-patterns/modules/01-foundations/days/day-05-rest-synthesize-i.md) | *(consolidation)* | Foundations |
| 6 | [Chain-of-Thought](agentic-design-patterns/modules/02-reasoning-patterns/days/day-06-chain-of-thought.md) | Making reasoning explicit and textual improves correctness | Reasoning |
| 7 | [Self-Consistency](agentic-design-patterns/modules/02-reasoning-patterns/days/day-07-self-consistency.md) | Sampling multiple paths and voting is more reliable than greedy decoding | Reasoning |
| 8 | [ReAct — Interleaving Thought and Action](agentic-design-patterns/modules/02-reasoning-patterns/days/day-08-react.md) | Interleaving reasoning traces with tool calls creates tighter feedback | Reasoning |
| 9 | [Implementing ReAct from Scratch](agentic-design-patterns/modules/02-reasoning-patterns/days/day-09-react-implementation.md) | The pattern is an 80-line loop: think → act → observe → repeat | Reasoning |
| 10 | [Rest & Synthesize II](agentic-design-patterns/modules/02-reasoning-patterns/days/day-10-rest-synthesize-ii.md) | *(consolidation)* | Reasoning |
| 11 | [Reflexion — Verbal Reinforcement](agentic-design-patterns/modules/02-reasoning-patterns/days/day-11-reflexion.md) | An agent that critiques its own trajectory can self-improve without retraining | Reasoning |
| 12 | [Tree of Thoughts](agentic-design-patterns/modules/02-reasoning-patterns/days/day-12-tree-of-thoughts.md) | Deliberate search over partial reasoning states beats single-path greedy | Reasoning |
| 13 | [LATS — Language Agent Tree Search](agentic-design-patterns/modules/02-reasoning-patterns/days/day-13-lats.md) | Tree search + Monte Carlo rollouts unifies reasoning, acting, and planning | Reasoning |
| 14 | [Thinking Fast and Slow](agentic-design-patterns/modules/02-reasoning-patterns/days/day-14-thinking-fast-slow.md) | The first architectural choice is which reasoning mode to engage | Reasoning |
| 15 | [Self-Refine — Iterative Self-Feedback](agentic-design-patterns/modules/03-self-improvement/days/day-15-self-refine.md) | An agent can improve any output by critiquing and re-drafting it in a loop | Self-Improvement |
| 16 | [STaR — Self-Taught Reasoner](agentic-design-patterns/modules/03-self-improvement/days/day-16-star.md) | An LLM can bootstrap reasoning capability by filtering self-generated rationales | Self-Improvement |
| 17 | [ExpeL — Experiential Learning from Trajectories](agentic-design-patterns/modules/03-self-improvement/days/day-17-expel.md) | Mining past trajectories for generalizable rules enables cross-episode learning | Self-Improvement |
| 18 | [Rest & Synthesize III](agentic-design-patterns/modules/03-self-improvement/days/day-18-rest-synthesize-iii.md) | *(consolidation)* | Self-Improvement |
| 19 | [The Skill Design Pattern](agentic-design-patterns/modules/04-skills-and-tools/days/day-19-skill-design-pattern.md) | A skill is a composable capability with a defined input/output contract | Skills & Tools |
| 20 | [Atomic Tool Design + Composition Patterns](agentic-design-patterns/modules/04-skills-and-tools/days/day-20-tool-design-composition.md) | One job per tool; chain, fan-out, and aggregate to compose complex behaviour | Skills & Tools |
| 21 | [Toolformer — Self-Taught Tool Use](agentic-design-patterns/modules/04-skills-and-tools/days/day-21-toolformer.md) | An LLM can learn when and how to invoke tools from self-generated examples | Skills & Tools |
| 22 | [The Skill Library Pattern](agentic-design-patterns/modules/04-skills-and-tools/days/day-22-skill-library.md) | A persistent, growing library of verified skills enables compound capability | Skills & Tools |
| 23 | [Memory Taxonomy](agentic-design-patterns/modules/05-memory/days/day-23-memory-taxonomy.md) | Agents have four orthogonal memory stores with distinct properties | Memory |
| 24 | [Scratchpad and Working Memory Patterns](agentic-design-patterns/modules/05-memory/days/day-24-scratchpad-working-memory.md) | What to keep, compress, or offload in the context window is an architectural decision | Memory |
| 25 | [Episodic Memory and Retrieval Patterns](agentic-design-patterns/modules/05-memory/days/day-25-episodic-memory-retrieval.md) | Storing and retrieving past trajectories lets agents learn without fine-tuning | Memory |
| 26 | [Rest & Synthesize IV](agentic-design-patterns/modules/05-memory/days/day-26-rest-synthesize-iv.md) | *(consolidation)* | Memory |
| 27 | [Orchestrator-Worker Pattern](agentic-design-patterns/modules/06-multi-agent/days/day-27-orchestrator-worker.md) | A central orchestrator decomposes tasks and dispatches to specialist workers | Multi-Agent |
| 28 | [Critic-Actor Pattern](agentic-design-patterns/modules/06-multi-agent/days/day-28-critic-actor.md) | Decoupling generation from evaluation improves quality through adversarial feedback | Multi-Agent |
| 29 | [Peer Debate and Society of Mind](agentic-design-patterns/modules/06-multi-agent/days/day-29-peer-debate.md) | Multiple agents with distinct personas arguing toward consensus surface novel perspectives | Multi-Agent |
| 30 | [Mixture of Agents](agentic-design-patterns/modules/06-multi-agent/days/day-30-mixture-of-agents.md) | Routing tasks to specialists and aggregating their outputs is a parallelizable architecture | Multi-Agent |
| 31 | [Human-in-the-Loop Patterns](agentic-design-patterns/modules/06-multi-agent/days/day-31-human-in-the-loop.md) | Deciding when to pause for human input is itself a design pattern | Multi-Agent |
| 32 | [Guardrails and Production Patterns](agentic-design-patterns/modules/06-multi-agent/days/day-32-guardrails.md) | Validation, filtering, retry, and observability are patterns — not afterthoughts | Multi-Agent |
| 33 | [Capstone](agentic-design-patterns/modules/07-capstone/days/day-33-capstone.md) | Design and implement a multi-pattern agent system with a self-improvement loop | Capstone |

#### Key resources

| Source | Why it matters |
| -------- | --------------- |
| Sumers et al. (2024) — *Cognitive Architectures for Language Agents* | The unified taxonomy the entire course is built around |
| Yao et al. (2023) — *ReAct* | The foundational reasoning+acting pattern paper |
| Anthropic — *Building Effective Agents* (2024) | The most opinionated practical guide to production patterns |
| Karpathy — *Intro to Large Language Models* (2023) | The LLM-as-OS framing; best intuition for cognitive architecture |
| Chip Huyen — *AI Engineering* (2025) | The engineering and production spine of the course |

[Full annotated bibliography](agentic-design-patterns/bibliography.md) · [Glossary](agentic-design-patterns/glossary.md) · [Learning path with sequencing rationale](agentic-design-patterns/learning-path.md)

---

### 💻 Claude Code Academy

*From first run to production-grade workflows — a practitioner's field guide.*

Ten focused lessons covering the full Claude Code workflow: navigating codebases, writing effective prompts, debugging, testing, refactoring, CLAUDE.md configuration, context management, and two capstone projects. Each lesson is a self-contained reference page you will return to after the first read.

**Format:** 10 lessons · 4 phases  
**Best for:** Engineers and tech leads already using (or about to use) Claude Code on real projects

**→ [Enter the academy](<Claude Code/README.md>)**

---

### 📖 Claude Code 101

*14 days from "I have Claude Code installed" to "I have Claude Code deployed."*

A structured course that builds the four configuration layers of Claude Code from scratch: project memory, behavior gates, workflows, and orchestration. The second half of the course deploys each layer on a real shared codebase (TaskFlow) so every concept has immediate, verifiable evidence.

**Format:** 14 days · 5 modules  
**Best for:** Engineers who have used Claude Code casually and want to use it professionally

**→ [Start Day 0](claude-code-101/README.md)**

---

### 🏗️ Grokking System Design

*21 days from "I can build things" to "I can design and justify them."*

A practitioner's course for .NET developers working in the Azure ecosystem who can implement features but have never been taught to design systems. Covers the four-step design methodology, trade-off analysis, C4 diagrams, storage and compute building blocks (relational, NoSQL, caching, blob, messaging), distributed systems constraints (CAP, sagas, observability, resilience), three full worked system designs, and a self-directed capstone from a one-line brief.

**Format:** 21 days · 5 modules  
**Best for:** .NET/Azure engineers who want to produce defensible High-Level and Low-Level Designs with explicit trade-off reasoning — and pass a system design interview along the way

**→ [Start Day 1](grokking-system-design/README.md)**

---

### 🎯 Decision Science & Game Theory

*65 days from intuition to rigorous strategic reasoning.*

A graduate-level course compressed for practitioners — covering decision theory, Bayesian reasoning, cognitive biases, game theory, mechanism design, reinforcement learning, and personal decision-making. Designed for product managers, engineers, and operators who make consequential decisions under uncertainty and want to do it better.

**Format:** 65 days · 5 modules  
**Best for:** Anyone who makes decisions for a living and wants a formal framework to audit their intuitions against

**→ [Start Day 1](decision-science-game-theory/README.md)**

---

### 📋 Spec-Driven Development

*7 days from "vibe coding" to a repeatable, tool-agnostic delivery workflow.*

A short, dense course on writing specs that AI coding agents can execute reliably. Covers the spec-agent contract, the four-layer anatomy of an executable spec, the project constitution, feature spec templates, the plan-implement-verify loop, and a full CI/deploy pipeline. Everything is tool-agnostic: the workflow works with Claude Code, Cursor, or any future agent.

**Format:** 7 days · Single arc  
**Best for:** Product managers and technical leads who direct AI coding agents and want predictable output

**→ [Start Day 1](spec-driven-development/README.md)**

---

### 🕸️ Knowledge Graphs: From Concept to Production

*15 days from "what is a triple" to a live KG an AI agent queries as structured memory.*

A concept-to-builder course for practitioners with an AI/LLM background who want knowledge graphs in their production toolkit. Covers triples, property graphs, ontologies, Cypher querying, entity extraction, ingestion pipelines, GraphRAG, and KG embeddings — culminating in a working domain-specific knowledge graph wired into a real agent.

**Format:** 15 days · Single arc (L1 + L2)  
**Best for:** Engineers and AI practitioners who already work with RAG or agents and want structured, queryable, multi-hop memory on top of their existing stack

**→ [Start Day 1](knowledge-graphs/README.md)**

---

### 🍵 Japanese Philosophy for Better Living

*21 days from scattered fragments to a coherent, lived philosophy.*

A concept-first journey through twenty-one ideas that form a distinctly Japanese relationship with reality: wabi-sabi, ikigai, ma, kaizen, kintsugi, yūgen, and more. Three modules — Perception, Practice, Integration — build from seeing differently, to acting differently, to sustaining what you've built when life pushes back. No background in philosophy or Japanese culture required; curiosity and thirty minutes a day is enough.

**Format:** 21 days · 3 modules  
**Best for:** Anyone who has encountered these ideas in fragments — on a podcast, in a design article, in a business book — and suspects there is something deeper and more coherent underneath

**→ [Start Day 1](japanese-philosophy/README.md)**

---

## How to navigate

Each course has its own **README** at the folder root — start there for the full day-by-day table of contents. Every lesson page has a breadcrumb at the top linking back to its course and module. Open a course folder, read the README, and begin at Day 1.

If you are new to this library, the recommended sequence is:

1. **[Agentic Learning Path](<Agentic_learning path/README.md>)** — build the conceptual foundation
2. **[Claude Code 101](claude-code-101/README.md)** or **[Claude Code Academy](<Claude Code/README.md>)** — get operational with the primary tool
3. **[Spec-Driven Development](spec-driven-development/README.md)** — make your prompts and specs reliable
4. **[Knowledge Graphs](knowledge-graphs/README.md)** — add structured, multi-hop memory to your agent stack
5. **[Decision Science & Game Theory](decision-science-game-theory/README.md)** — sharpen the reasoning behind every product and engineering decision

**Parallel track (standalone):** **[Grokking System Design](grokking-system-design/README.md)** — for .NET/Azure engineers who design the production systems that AI runs on; pairs with any step above

---

*Built and maintained with Claude Code.*
