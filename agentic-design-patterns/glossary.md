# Glossary — Agentic Design Patterns

Alphabetical. Each entry: plain-English definition → formal definition → day of first use.

---

**Action space**
*Plain:* The complete set of things an agent is allowed to do.
*Formal:* The set A of discrete actions available to an agent at each decision step; may include storage manipulation, process execution, UI interaction, service calls, and cross-agent calls.
*Introduced:* Day 2

---

**Agent**
*Plain:* A software system that perceives its environment, reasons about it, and takes actions to achieve a goal — in a loop, not just once.
*Formal:* An autonomous system combining a language model with memory, tools, and a control loop that persists across multiple inference calls.
*Introduced:* Day 1

---

**Agent trace**
*Plain:* The full log of everything an agent did during one run: every thought, every tool call, every observation.
*Formal:* A sequence of (thought, action, observation) tuples generated during a single agent episode; the ground truth of agent behavior.
*Introduced:* Day 4

---

**Agentic design pattern**
*Plain:* A named, reusable solution to a problem that keeps coming up in agent system design.
*Formal:* A triple (context, force, solution) describing a recurring architectural problem in agent systems and its canonical resolution; drawn from Christopher Alexander's pattern language, adapted to software by the GoF, and now applied to LLM-based agents.
*Introduced:* Day 1

---

**Chain-of-Thought (CoT)**
*Plain:* Prompting a model to show its work — write out reasoning steps before giving the final answer.
*Formal:* A prompting technique that augments the input with intermediate reasoning steps z between input x and output y, expressed as: P(y|x) → P(y|x, z) where z is generated autoregressively before y.
*Introduced:* Day 6

---

**Cognitive architecture**
*Plain:* The structural blueprint of an agent — which components it has and how they connect.
*Formal:* The specification of an agent's action space, memory types, planning mechanism, and learning capabilities; the framework used to compare and design agent systems.
*Introduced:* Day 2

---

**Context window**
*Plain:* The "working memory" of an LLM — everything the model can see during one forward pass.
*Formal:* The fixed-length token sequence that constitutes the model's input at inference time; functions as in-context (working) memory in agent architectures.
*Introduced:* Day 2

---

**Episode**
*Plain:* One complete run of an agent on a task, from start to finish (or failure).
*Formal:* A trajectory τ = (s₀, a₀, o₀, s₁, a₁, o₁, …, sₙ) representing a complete interaction sequence between an agent and its environment.
*Introduced:* Day 4

---

**ExpeL**
*Plain:* A pattern where an agent mines its own past runs to extract rules, then applies those rules to future tasks — learning without fine-tuning.
*Formal:* Experiential learning pattern that collects successful/failed trajectories, extracts generalizable natural-language insights via LLM analysis, and retrieves relevant insights at inference time.
*Introduced:* Day 17

---

**Force (in a pattern)**
*Plain:* The tension or pressure that makes the problem hard — why you can't just do the obvious thing.
*Formal:* In Christopher Alexander's pattern language: the conflicting constraints that a solution must balance; a pattern's solution is valid only when it resolves these forces.
*Introduced:* Day 1

---

**Function calling / Tool use**
*Plain:* The ability of an LLM to say "run this tool with these arguments" instead of producing only text.
*Formal:* A structured output mechanism where the model produces a (tool_name, arguments) pair that the host application executes, then feeds the result back as an observation.
*Introduced:* Day 3

---

**Grounding**
*Plain:* Connecting an agent's language-level reasoning to real-world state — files, databases, API responses.
*Formal:* The process of resolving abstract linguistic representations to concrete environmental observations through tool execution or retrieval.
*Introduced:* Day 3

---

**KV cache**
*Plain:* The saved computation from previous tokens — speeds up generation by not recomputing attention over the same context.
*Formal:* A cache of key-value pairs from the attention mechanism, persisting computed representations of prior tokens to avoid redundant computation during autoregressive generation.
*Introduced:* Day 2

---

**LATS (Language Agent Tree Search)**
*Plain:* A pattern that searches a tree of possible reasoning/action paths using simulated rollouts to pick the best one.
*Formal:* An agent planning algorithm that applies Monte Carlo Tree Search over a tree of (thought, action) nodes, using LLM-generated rollouts and environment feedback as the value signal.
*Introduced:* Day 13

---

**LLM-as-OS**
*Plain:* Andrej Karpathy's analogy: the LLM is the CPU, the context window is RAM, tools are I/O, and external storage is the disk.
*Formal:* An architectural mental model mapping language model components to operating system primitives to reason about resource constraints, scheduling, and persistence in agent systems.
*Introduced:* Day 2

---

**Memory (agent)**
*Plain:* Any information an agent can access that wasn't in its original training data.
*Formal:* The four-component memory taxonomy (Sumers et al., 2024): in-context (working), external (episodic/semantic), procedural, and parametric — differing in persistence, capacity, and access speed.
*Introduced:* Day 23

---

**Observation**
*Plain:* What the agent gets back after taking an action — the environment's response.
*Formal:* In the ReAct framework: a text string returned by tool execution or the environment, appended to the agent's context as evidence for the next reasoning step.
*Introduced:* Day 4

---

**Orchestrator**
*Plain:* The agent in charge — the one that breaks down the task and assigns sub-tasks to other agents.
*Formal:* In the Orchestrator-Worker pattern: the controlling agent responsible for task decomposition, sub-task dispatch, and result aggregation; does not itself execute leaf-level actions.
*Introduced:* Day 27

---

**Pattern language**
*Plain:* A system of named patterns that reference each other to form a coherent design vocabulary.
*Formal:* Introduced by Christopher Alexander in *A Pattern Language* (1977): a structured vocabulary of interrelated patterns for solving design problems at different scales of abstraction.
*Introduced:* Day 1

---

**ReAct**
*Plain:* A pattern where the agent alternates between writing a reasoning step (thought) and calling a tool (action), using the result (observation) to inform the next thought.
*Formal:* A prompting framework that interleaves chain-of-thought reasoning traces with action execution in an alternating Thought → Action → Observation loop until a terminal condition is reached.
*Introduced:* Day 8

---

**Reflexion**
*Plain:* A pattern where the agent writes a verbal critique of what went wrong in a past run, then uses that critique as context in the next attempt.
*Formal:* A verbal reinforcement learning pattern storing episodic memory as natural-language self-evaluations that are prepended to future episode contexts, without gradient updates.
*Introduced:* Day 11

---

**Self-Consistency**
*Plain:* Sample the same question multiple times with different random seeds, then vote on the most common answer.
*Formal:* A decoding strategy that samples k independent reasoning chains from P(z,y|x), extracts the final answer y from each, and returns argmax_{y} Σ P(y_i = y).
*Introduced:* Day 7

---

**Self-Refine**
*Plain:* A tight loop where the agent generates an answer, critiques it, then revises it — repeatedly, within a single run.
*Formal:* An iterative self-improvement algorithm: generate output o₀, produce critique c₀ = Critique(o₀), produce revision o₁ = Refine(o₀, c₀), repeat until a stopping criterion is met.
*Introduced:* Day 15

---

**Skill**
*Plain:* A reusable, named capability an agent can invoke — more structured than a raw tool.
*Formal:* A composable agent capability with a defined input schema, output schema, and execution contract; may wrap one or more tools and include internal validation logic.
*Introduced:* Day 19

---

**Skill library**
*Plain:* A persistent, growing collection of verified skills an agent has learned, which it can retrieve and reuse on new tasks.
*Formal:* An external memory store (introduced in Voyager, Wang et al., 2023) that stores verified executable skills indexed by description, enabling retrieval-augmented task execution for novel tasks.
*Introduced:* Day 22

---

**STaR (Self-Taught Reasoner)**
*Plain:* A training method where the model generates its own reasoning examples, keeps the ones that led to correct answers, and fine-tunes on them — then repeats.
*Formal:* A bootstrapping algorithm that alternates between rationale generation (with a rationalization fallback for missed examples) and fine-tuning on filtered correct rationale-answer pairs to progressively improve reasoning capability.
*Introduced:* Day 16

---

**System 1 / System 2 thinking (agent context)**
*Plain:* System 1 = direct generation (fast, cheap, intuitive). System 2 = deliberate multi-step reasoning (slow, expensive, thorough).
*Formal:* Adapted from Kahneman (2011): a design choice between single-pass LLM generation (System 1 analog) and iterative, search-based reasoning chains (System 2 analog); the choice determines cost, latency, and accuracy trade-offs.
*Introduced:* Day 14

---

**Thought (in ReAct)**
*Plain:* The agent's internal monologue — a reasoning step written in natural language before taking an action.
*Formal:* In the ReAct framework: a natural-language reasoning trace generated by the LLM that is visible in the context and conditions the next action selection.
*Introduced:* Day 8

---

**Tool**
*Plain:* A function the agent can call to interact with the outside world — search, code execution, API calls, etc.
*Formal:* An external executable with a defined name, description, and input schema that the agent invokes during execution; the result is returned as an observation.
*Introduced:* Day 3

---

**Tree of Thoughts (ToT)**
*Plain:* Instead of one reasoning chain, the agent explores a tree of possible partial thoughts, evaluates each branch, and searches for the best path.
*Formal:* A framework for deliberate problem solving that decomposes reasoning into: (1) thought decomposition, (2) thought generation (breadth/depth), (3) state evaluation via LLM-as-judge, and (4) tree search (BFS or DFS).
*Introduced:* Day 12

---

**Worker**
*Plain:* A specialist agent that executes a specific sub-task assigned by an orchestrator.
*Formal:* In the Orchestrator-Worker pattern: an agent responsible for executing a specific, well-scoped sub-task and returning a structured result to the orchestrator.
*Introduced:* Day 27
