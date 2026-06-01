# Bibliography — Spec-Driven Development with Coding Agents

All sources listed here are cited in at least one day page. Each entry includes: full citation, what it covers, whose intuition it favors, where it is hardest, and which days rely on it most.

---

## Foundational Books

### 1. Shape Up: Stop Running in Circles and Ship Work that Matters
**Ryan Singer. Basecamp, 2019. Free at basecamp.com/shapeup.**

Shape Up is a product development methodology from the team at Basecamp. Its core contribution is the "shaped pitch" — a format for describing a feature that is concrete enough to act on but loose enough to leave implementation decisions to the builder. The key elements (appetite, problem, solution sketch, no-gos) map almost directly onto the feature spec template used in Day 4.

- **What it covers:** How to write a product pitch that reduces uncertainty without over-specifying. Appetite-based scoping. Betting on shaped work rather than grooming a backlog.
- **Whose intuition it favors:** Product thinkers and designers. It is skeptical of detailed task lists and story points.
- **Where it is hardest:** The book is written for human teams, not AI agents. You will need to mentally translate "builder" → "coding agent" when reading Chapters 3–5.
- **Days that rely on it most:** Days 3 and 4. Chapter 3 ("Shaping") and Chapter 5 ("Writing the Pitch") are the must-reads.

### 2. Continuous Delivery: Reliable Software Releases through Build, Test, and Deployment Automation
**Jez Humble and David Farley. Addison-Wesley Professional, 2010. ISBN 978-0321601919.**

The definitive text on deployment pipelines. Humble and Farley describe a commit-to-production workflow where every code change passes through a series of automated gates (build, unit test, acceptance test, staging, production). The pipeline template in Day 6 is a simplified version of their model.

- **What it covers:** Continuous integration, automated testing, deployment pipelines, feature flags, zero-downtime deployments.
- **Whose intuition it favors:** Engineers and DevOps practitioners. It assumes you are writing the pipeline config yourself.
- **Where it is hardest:** Chapters 10–14 (infrastructure and environment management) are deep engineering — skip them for this course.
- **Days that rely on it most:** Day 6. Chapter 5 ("The Commit Stage") and Chapter 8 ("Automated Acceptance Testing") are the relevant sections.

### 3. The Cucumber Book: Behaviour-Driven Development for Testers and Developers
**Matt Wynne and Aslak Hellesøy. The Pragmatic Bookshelf, 2nd edition, 2017. ISBN 978-1680502381.**

BDD is the practice of writing software requirements as structured examples that can be automated as tests. The Given/When/Then format that appears in the acceptance criteria layer of every spec in this course comes directly from BDD. The Cucumber Book is the clearest introduction to this style of writing.

- **What it covers:** Writing Given/When/Then scenarios, connecting them to automated tests, using them as living documentation.
- **Whose intuition it favors:** QA engineers and developers who think test-first.
- **Where it is hardest:** Part II (automating scenarios with Cucumber) is developer-focused and not needed for this course.
- **Days that rely on it most:** Days 2 and 5. Chapter 2 ("First Scenario") and Chapter 3 ("Gherkin") are the relevant sections.

---

## Primary Course Reference

### 4. Spec-Driven Development with Coding Agents
**Paul Everitt (Developer Advocate, JetBrains). DeepLearning.AI short course, 2024–25. Available at: learn.deeplearning.ai/courses/spec-driven-development-with-coding-agents. Approximately 1 hour of video content across 15 lessons.**

This is the single most directly applicable source in the shelf. Everitt introduces the "project constitution" concept, the plan-implement-verify loop as a named workflow, and the idea of agent replaceability (designing your specs so any agent can execute them). The course uses JetBrains AI Assistant as its primary tool, but all techniques apply to Claude Code, Cursor, and others.

- **What it covers:** Vibe coding vs. spec-driven development, project constitutions, feature specs, plan-implement-verify loop, applying the methodology to legacy codebases, packaging custom agent skills.
- **Whose intuition it favors:** Developers who want a practical, tool-agnostic workflow. Not PM-focused, but all concepts transfer directly.
- **Where it is hardest:** The legacy codebase and custom skills lessons (late in the course) assume more engineering context than this course requires at L1.
- **Days that rely on it most:** Days 3, 5, and 6. Lessons 3 (project constitution), 4–5 (plan-implement-verify), and 8 (legacy codebases) are the core.

---

## Landmark Papers

### 5. SWE-bench: Can Language Models Resolve Real-World GitHub Issues?
**Carlos E. Jimenez, John Yang, Alexander Wettig, Shunyu Yao, Kexin Pei, Ofir Press, Karthik Narasimhan. NeurIPS 2024. arXiv:2310.06770.**

SWE-bench is a benchmark that evaluates coding agents by giving them real GitHub issues and asking them to produce patches that pass the corresponding test suites. The benchmark design is a direct window into what "good spec" means: issues with specific reproduction steps, explicit expected behavior, and test cases are solved at dramatically higher rates than vague issues. The failure analysis in Section 4 is the most PM-relevant reading in the entire shelf.

- **What it covers:** Benchmark design, agent performance across issue types, failure analysis.
- **Days that rely on it most:** Day 1. Section 4 ("Analysis") is the required reading.

### 6. Evaluating Large Language Models Trained on Code (the Codex / HumanEval paper)
**Mark Chen, Jerry Tworek, Heewoo Jun, et al. OpenAI Technical Report, 2021. arXiv:2107.03374.**

This paper introduced HumanEval — a benchmark where each problem is a Python function docstring (the spec) and the agent must produce a function that passes hidden test cases. The core finding relevant to this course: pass rate correlates strongly with docstring specificity. A vague docstring produces a vague function. This is the foundational empirical grounding for Day 1's argument.

- **What it covers:** Codex model capability, the HumanEval benchmark, pass@k evaluation methodology.
- **Days that rely on it most:** Day 1 (background). Section 2.1 ("HumanEval Benchmark") is the relevant part.

---

## Video Lectures and Courses

### 7. Intro to Large Language Models
**Andrej Karpathy. YouTube, November 2023. Search: "Andrej Karpathy Intro to Large Language Models" (approximately 1 hour). Direct URL: youtube.com/watch?v=zjkBMFhNj_g**

Karpathy's clearest public explanation of what LLMs actually do: they are next-token predictors trained on large text corpora. Understanding this mechanism makes the "spec as contract" framing in Day 1 click immediately — the agent is not reasoning about your intent, it is completing a pattern. Timestamps 0:00–18:00 are the relevant section for this course.

- **Days that rely on it most:** Optional pre-reading before Day 1.

### 8. Claude Code Documentation
**Anthropic. Living document at docs.anthropic.com/claude-code.**

The official documentation for Claude Code, including the CLAUDE.md format (Anthropic's implementation of the project constitution), memory and context management, tool use, and MCP server integration. The CLAUDE.md best practices section is the closest real-world implementation of the Day 3 constitution template.

- **Days that rely on it most:** Days 4–6. The "CLAUDE.md guide" and "How Claude Code works" sections are directly applicable.

---

## Optional Deeper Dives

### 9. Software 2.0
**Andrej Karpathy. Medium, November 2017. Search: "Karpathy Software 2.0 Medium".**

A short essay arguing that neural networks represent a new kind of programming: instead of writing explicit logic, you specify desired behavior (via training data or prompts) and the network learns the implementation. The philosophical foundation for why specs matter in the age of coding agents. Worth 15 minutes before or after Day 1.

### 10. Anthropic Prompt Engineering Guide
**Anthropic. docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/overview.**

Practical reference for the steering patterns introduced in Day 4. The "be specific and direct," "use examples," and "chain of thought" sections apply directly to spec writing and to the prompts used in the plan-implement-verify loop.

### 11. Shape Up companion essays — "How We Work"
**Ryan Singer, Jason Fried. basecamp.com/how-we-work.**

Short essays on what happens when a shaped pitch breaks down in practice — scope creep, unclear boundaries, features that grow. Good context for understanding why the "no-gos" section of the Day 4 spec template is not optional.
