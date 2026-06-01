# Bibliography — Agentic Design Patterns

Full annotated Course Shelf. Every citation follows the rule: only sources confirmed to exist, pointed at a specific location.

---

## Foundational Books

### AI Engineering
**Chip Huyen.** O'Reilly Media, 2025. ISBN 978-1098166298.

Covers the full engineering lifecycle of LLM-based systems — from evaluation and fine-tuning through agentic pipelines and production deployment. The strongest single-volume treatment of tool design, memory architecture, and the operational concerns (latency, cost, observability) that the research papers skip. Chapters 7–9 are the spine for Days 19–20 and 32. Chapter 6 (on evaluation) underpins the Critic-Actor pattern on Day 28.

**Course reliance:** Primary reference for Days 19, 20, 28, 32.

---

### Hands-On Large Language Models
**Jay Alammar & Maarten Grootendorst.** O'Reilly Media, 2024. ISBN 978-1098150969.

The most visual treatment of how LLMs work at the embedding and attention level. Chapters 8–10 cover retrieval, tool use, and agents with concrete code examples. Best used as an alternative intuition source when the research papers are too terse. Chapter 9 (semantic search and retrieval) is the best companion to Day 25 (Episodic Memory).

**Course reliance:** Alternative reference for Days 3, 6, 25.

---

### Thinking, Fast and Slow
**Daniel Kahneman.** Farrar, Straus and Giroux, 2011. ISBN 978-0374533557.

The source concept for Day 14. Kahneman's System 1 (fast, intuitive, automatic) and System 2 (slow, deliberate, effortful) thinking maps directly onto the architectural choice between direct LLM generation and multi-step reasoning chains. Part I ("Two Systems") is the only required reading — roughly 40 pages.

**Course reliance:** Conceptual spine for Day 14.

---

## Landmark Papers

### Chain-of-Thought Prompting Elicits Reasoning in Large Language Models
**Jason Wei, Xuezhi Wang, Dale Schuurmans, Maarten Bosma, Brian Ichter, Fei Xia, Ed Chi, Quoc Le, Denny Zhou.**
NeurIPS 2022. arXiv:2201.11903.

The paper that established explicit reasoning traces as a first-class technique. Shows that adding reasoning steps to prompts dramatically improves performance on multi-step arithmetic, symbolic, and commonsense reasoning tasks — with no additional training. Section 2 (method) and Section 3 (experiments) are the core; the ablation in Section 4 shows what makes CoT work.

**Course reliance:** Primary source for Day 6.

---

### Self-Consistency Improves Chain of Thought Reasoning in Language Models
**Xuezhi Wang, Jason Wei, Dale Schuurmans, Quoc Le, Ed Chi, Sharan Narang, Aakanksha Chowdhery, Denny Zhou.**
ICLR 2023. arXiv:2203.11171.

Introduces the idea of sampling multiple reasoning paths and taking the majority vote, without any additional training. Improves CoT by 17.9% on GSM8K. Sections 2–3 are essential; Section 5 (analysis) explains why it works and when it fails.

**Course reliance:** Primary source for Day 7.

---

### ReAct: Synergizing Reasoning and Acting in Language Models
**Shunyu Yao, Jeffrey Zhao, Dian Yu, Nan Du, Izhak Shafran, Karthik Narasimhan, Yuan Cao.**
ICLR 2023. arXiv:2210.03629.

The paper that formalized the interleaved think/act/observe loop. Evaluated on HotpotQA (multi-step QA), FEVER (fact verification), ALFWorld, and WebShop. Section 2 (method) is the complete pattern definition in ~2 pages. Section 4 (analysis) shows failure modes — essential reading for Day 9.

**Course reliance:** Primary source for Days 8 and 9.

---

### Reflexion: Language Agents with Verbal Reinforcement Learning
**Noah Shinn, Federico Cassano, Ashwin Gopinath, Karthik Narasimhan, Shunyu Yao.**
NeurIPS 2023. arXiv:2303.11366.

Introduces verbal self-critique as a feedback mechanism: after a failed episode, the agent writes a verbal "lesson" which is prepended to the next episode's context. No gradient updates required. Section 3 defines the three memory components (short-term, long-term, episodic); Section 4 shows results on coding, QA, and decision-making tasks.

**Course reliance:** Primary source for Day 11.

---

### Tree of Thoughts: Deliberate Problem Solving with Large Language Models
**Shunyu Yao, Dian Yu, Jeffrey Zhao, Izhak Shafran, Thomas L. Griffiths, Yuan Cao, Karthik Narasimhan.**
NeurIPS 2023. arXiv:2305.10601.

Formalizes branching deliberation over partial reasoning steps. Defines the four components of ToT: thought decomposition, thought generation, state evaluation, and search algorithm (BFS or DFS). Section 2 is the full method; Section 3.1 (Game of 24) is the clearest worked example.

**Course reliance:** Primary source for Day 12.

---

### Language Agent Tree Search Unifies Reasoning Acting and Planning in Language Models
**Andy Zhou, Kai Yan, Michal Shlapentokh-Rothman, Haohan Wang, Yu-Xiong Wang.**
2023. arXiv:2310.04406. `[VERIFY arXiv ID]`

Combines MCTS-style tree search with environment feedback and LLM-generated rollouts. The most complete single-paper treatment of reasoning + acting + planning in one unified framework. Section 3 (the LATS algorithm) and Section 4 (experiments on HotpotQA and programming tasks) are the core.

**Course reliance:** Primary source for Day 13.

---

### Toolformer: Language Models Can Teach Themselves to Use Tools
**Timo Schick, Jane Dwivedi-Kumar, Roberto Dessì, Roberta Raileanu, Maria Lomeli, Luke Zettlemoyer, Nicola Cancedda, Thomas Scialom.**
NeurIPS 2023. arXiv:2302.04761.

Shows that an LLM can learn when and how to call tools by: (1) generating candidate API call insertions, (2) filtering by whether the call actually reduces perplexity on continuation tokens, and (3) fine-tuning on the filtered examples. The key insight is that the model learns the *decision* to use a tool, not just the mechanics. Section 2 (method) is the complete pattern; Section 3 (experiments) shows it generalizes across calculator, calendar, QA, and translation APIs.

**Course reliance:** Primary source for Day 21.

---

### Voyager: An Open-Ended Embodied Agent with Large Language Models
**Guanzhi Wang, Yuqi Zhu, Xuan Liu, Ruihan Yang, Chen Tang, Yue Li, Zheyuan Zhang, Junxian He, Song-Chun Zhu, Anima Anandkumar.**
2023. arXiv:2305.16291.

Introduces the **Skill Library** by name: an agent that writes verified code skills, stores them in a growing library, and retrieves them for novel tasks. Three components: automatic curriculum, skill library, iterative prompting. Section 3.3 (skill library) is the direct source for Day 22.

**Course reliance:** Primary source for Day 22.

---

### Cognitive Architectures for Language Agents
**Theodore R. Sumers, Shunyu Yao, Karthik Narasimhan, Thomas L. Griffiths.**
TMLR 2024. arXiv:2309.02427.

The field's most systematic taxonomy paper. Maps action space, memory types, and learning mechanisms into a unified framework. Section 2 (the taxonomy) is the direct source for Day 2's cognitive architecture map. Section 4 (learning) is the backbone of the self-improvement module. The best single paper to read before starting the course.

**Course reliance:** Primary source for Day 2; backbone for Days 15–18.

---

### AutoGen: Enabling Next-Gen LLM Applications via Multi-Agent Conversation
**Qingyun Wu, Gagan Bansal, Jieyu Zhang, Yiran Wu, Beibin Li, Erkang Zhu, Li Jiang, Xiaoyun Zhang, Shaokun Zhang, Jiale Liu, Ahmed Hassan Awadallah, Ryen W. White, Doug Burger, Chi Wang.**
2023. arXiv:2308.08155.

The most-cited multi-agent systems paper. Introduces conversable agents, the GroupChat orchestration primitive, and the AssistantAgent/UserProxyAgent split (which is a concrete Critic-Actor implementation). Section 2 (design) and Section 3 (case studies) are the core; the coding agent case study (Section 3.1) is the best concrete example of orchestrator-worker + critic-actor in one system.

**Course reliance:** Primary source for Days 27 and 28.

---

### Self-Refine: Iterative Refinement with Self-Feedback
**Aman Madaan, Niket Tandon, Prakhar Gupta, Skyler Hallinan, Luyu Gao, Sarah Wiegreffe, Uri Alon, Nouha Dziri, Shrimai Prabhumoye, Yiming Yang, Shashank Gupta, Bodhisattwa Prasad Majumder, Katherine Hermann, Sean Welleck, Amir Yazdanbakhsh, Peter Clark.**
NeurIPS 2023. arXiv:2303.17651.

Introduces the tightest form of self-improvement: generate → critique → refine, in a synchronous loop without external feedback or fine-tuning. Works across code generation, math, sentiment, and dialogue. Section 2 (the Self-Refine algorithm) is ~1 page and completely defines the pattern; Figure 1 is the clearest diagram of the feedback loop in the literature.

**Course reliance:** Primary source for Day 15.

---

### STaR: Self-Taught Reasoner Bootstrapping Reasoning With Reasoning
**Eric Zelikman, Yuhuai Wu, Jesse Mu, Noah D. Goodman.**
NeurIPS 2022. arXiv:2203.14465.

The foundational paper on parametric self-improvement. An LLM generates rationales for problems it gets right, uses a "rationalization" trick for problems it gets wrong (condition on the correct answer to generate a rationale), then fine-tunes on the filtered set. Repeat. Section 2 (method) is 2 pages; Section 3 (experiments) shows it eventually matches larger models on arithmetic benchmarks.

**Course reliance:** Primary source for Day 16.

---

### ExpeL: LLM Agents Are Experiential Learners
**Andrew Zhao, Daniel Huang, Quentin Xu, Matthieu Lin, Yong-Jin Liu, Gao Huang.**
2024. arXiv:2308.10144. `[VERIFY arXiv ID]`

Introduces cross-episode experiential learning: the agent mines its own successful and failed trajectories to extract generalizable rules (as natural language), stores them in an experience pool, and retrieves relevant rules at inference time. No fine-tuning required. Section 3 (method) defines the three components: experience collection, insight extraction, and retrieval-augmented task execution.

**Course reliance:** Primary source for Day 17.

---

## Blog Posts & Guides

### Building Effective Agents
**Anthropic.** Blog post, December 2024.
URL: https://www.anthropic.com/research/building-effective-agents

The most opinionated and practical guide to agent architecture written by practitioners running agents at production scale. Introduces the augmented LLM building block, defines five agentic patterns (prompt chaining, routing, parallelization, orchestrator-workers, evaluator-optimizer), and gives explicit guidance on when *not* to use agents. Essential reading alongside Days 19, 20, and 27.

**Course reliance:** Primary reference for Days 19, 20, 27.

---

### Software 2.0
**Andrej Karpathy.** Medium, 2017.
URL: https://karpathy.medium.com/software-2-0-a64152b37c35

The philosophical grounding for why LLM-based agents are architected differently from classical software. The core argument: neural networks are not written, they are trained — and this changes everything about how we compose, debug, and extend them. Read it in 10 minutes; it reframes Day 1's pattern-language discussion.

**Course reliance:** Companion for Day 1.

---

## Video Lectures

### Intro to Large Language Models
**Andrej Karpathy.** YouTube, November 2023.
Search: "Andrej Karpathy Intro to Large Language Models" (1hr 7min)

The best existing explanation of how LLM internals constrain agent behavior. Karpathy introduces the LLM-as-OS analogy (context = RAM, tools = I/O peripherals, external storage = disk) which is the cognitive architecture map for Day 2. Timestamp 45:00–57:00 covers agents, tool use, and multi-agent systems directly.

**Course reliance:** Primary source for Day 2; companion for Day 3.

---

### What's Next for AI Agentic Workflows
**Andrew Ng.** TED AI Conference, 2024.
URL: https://www.ted.com/talks/andrew_ng_what_s_next_for_ai_agentic_workflows (15 min)

The clearest 15-minute treatment of the four agentic pattern families: reflection, tool use, planning, and multi-agent. Ng's framing of "iterative" vs. "one-shot" generation is a good intuition pump for Day 14 (Thinking Fast and Slow). Watch it on Day 1 or Day 2 as an orientation.

**Course reliance:** Orientation for Days 1–2; companion for Day 27.

---

## Optional Deeper Dives

- **Generative Agents: Interactive Simulacra of Human Behavior** — Park et al., 2023. arXiv:2304.03442. Extended worked example of episodic memory + reflection in a multi-agent social simulation. Best read after Day 25.

- **LangGraph documentation** — https://langchain-ai.github.io/langgraph/ — Production implementation of state-machine agent orchestration. Best consulted alongside Days 27 and 32.

- **Microsoft Semantic Kernel documentation** — https://learn.microsoft.com/en-us/semantic-kernel/ — Reference implementation of the Skill/Plugin design pattern. Useful alongside Days 19–20.

- **HuggingGPT / JARVIS** — Shen et al., 2023. arXiv:2303.17580. Orchestrator pattern where a central LLM plans tasks and routes to specialist models. Good concrete example of the orchestrator-worker pattern for Day 27.
