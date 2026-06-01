# Bibliography — Claude Code 101

> Full annotated Course Shelf. Every source cited in a day page links back here.
> Sources marked `[VERIFY]` should be confirmed against current URLs at time of reading — documentation sites update frequently.

---

## Foundational Books

### 1. The Pragmatic Programmer (20th Anniversary Edition)
**David Thomas & Andrew Hunt** | Pragmatic Bookshelf | 2019 | ISBN 978-0-13-595705-9

The philosophical backbone of this course's approach to configuration and automation. Thomas and Hunt's core principles — "Don't Repeat Yourself," "Automate Your Environment," and "Use the Power of Command Shells" — map directly onto the case for investing in `CLAUDE.md`, skills, and hooks. Not about Claude Code specifically, but provides the professional engineering discipline that makes every configuration decision principled rather than arbitrary.

*Relied on most heavily in:* Days 3 (conventions), 8 (TaskFlow scaffold), 13 (team collaboration).
*Specific chapter:* Chapter 3 ("The Basic Tools") and Chapter 7 ("Before the Project") — read alongside Day 8.

---

### 2. Software Engineering at Google: Lessons Learned from Programming Over Time
**Titus Winters, Tom Manshreck, Hyrum Wright** | O'Reilly Media | 2020 | ISBN 978-1-492-08279-8
Free online at: abseil.io/resources/swe-book

The definitive reference for code review practices, testing philosophy, and team-scale engineering conventions. Chapter 9 ("Code Review") directly informs the `/review` skill design in Day 10. Chapter 11 ("Testing Overview") grounds the test generation workflow in Day 9. Chapter 16 ("Version Control and Branch Management") connects to the CI/CD patterns in Day 13.

*Relied on most heavily in:* Days 9, 10, 13.
*Specific chapters:* Ch. 9 for Day 10, Ch. 11 for Day 9, Ch. 24 ("Continuous Integration") for Day 13.

---

### 3. Refactoring: Improving the Design of Existing Code (2nd Edition)
**Martin Fowler** | Addison-Wesley Professional | 2018 | ISBN 978-0-13-468599-1

The canonical catalog of named refactoring patterns. Day 10's refactoring workflow gains precision from Fowler's taxonomy — telling Claude to apply "Extract Function" or "Replace Conditional with Polymorphism" produces better results than "clean this up." The introduction (Chapter 1) is a worked example of the entire refactoring process and is worth reading in full before Day 10.

*Relied on most heavily in:* Day 10.
*Specific sections:* Ch. 1 worked example (read before Day 10); the pattern catalog as a reference during Day 10 exercises.

---

### 4. Accelerate: The Science of Lean Software and DevOps
**Nicole Forsgren, Jez Humble, Gene Kim** | IT Revolution Press | 2018 | ISBN 978-1-942788-33-1

Data-driven evidence for the practices in Day 13. Forsgren et al. establish that deployment frequency, lead time, change failure rate, and recovery time are the four key metrics of high-performing engineering teams — and that automation (hooks, CI/CD pipelines, shared configuration) is a leading predictor of performance on these metrics. Use this to justify the Day 13 investment to skeptical teammates.

*Relied on most heavily in:* Day 13.
*Specific sections:* Ch. 2 ("Measuring Performance") and Ch. 4 ("Technical Practices").

---

## Primary / Official Sources

### 5. Claude Code Official Documentation
**Anthropic** | Continuously updated | docs.anthropic.com/en/docs/claude-code `[VERIFY URL]`

The authoritative reference for every configuration file, permission model, CLI flag, and tool discussed in this course. When this course describes behavior, the documentation is the ground truth. Check the "What's New" section regularly — Claude Code ships frequently.

*Relied on most heavily in:* All days. Specific sections:
- "CLAUDE.md guide" — Days 2, 3, 8
- "Settings" — Day 3, 7
- "Slash commands / Skills" — Days 4, 9–12
- "Hooks" — Days 5, 13
- "Sub-agents" — Days 7, 12
- "MCP integration" — Day 6

---

### 6. "Building Effective Agents"
**Anthropic Engineering** | Anthropic Blog | December 2024
URL: anthropic.com/engineering/building-effective-agents `[VERIFY URL]`

The clearest published statement of Anthropic's thinking on agentic loops, tool use patterns, and multi-agent orchestration. Introduces the orchestrator/subagent distinction, the importance of minimal tool surfaces, and the "augmented LLM" building block. Essential reading before Day 12. The section on "Orchestrator and subagents" maps directly onto Day 12's patterns.

*Relied on most heavily in:* Days 7, 12.
*Specific sections:* "Building blocks of agentic systems" for Day 7; "Orchestrator and subagents" for Day 12.

---

### 7. Prompt Engineering Guide
**Anthropic** | Continuously updated | docs.anthropic.com/en/docs/build-with-claude/prompt-engineering `[VERIFY URL]`

The canonical reference for writing instructions Claude reliably follows. The sections on "Be specific and direct," "Use structured formats," and "Chain of thought" are directly applicable to `CLAUDE.md` authorship (Day 3) and skill design (Day 4). Read the section on "System prompt construction" before Day 3.

*Relied on most heavily in:* Days 3, 4.

---

### 8. Model Context Protocol (MCP) Specification
**Anthropic** | 2024 | modelcontextprotocol.io `[VERIFY URL]`

The open specification for the protocol that MCP servers implement. Day 6 is built around the concepts in this specification: tools, resources, and prompts as the three primitives MCP exposes. The "Architecture" section explains client-server separation; the "Tools" section explains how Claude invokes MCP-provided tools identically to built-in tools.

*Relied on most heavily in:* Day 6.
*Specific sections:* "Architecture overview" and "Tools specification."

---

## Video Lectures / Courses

### 9. Prompt Engineering for Developers (Short Course)
**Andrew Ng & Isa Fulford** | DeepLearning.AI | 2023
URL: learn.deeplearning.ai/courses/chatgpt-prompt-eng `[VERIFY current URL]`

Free, ~1.5 hours, immediately applicable. The best structured introduction to writing instructions that produce consistent outputs — directly applicable to `CLAUDE.md` authorship and skill design. Covers prompt structure, delimiters, chain-of-thought, and iterative refinement. Recommended as pre-reading before Day 3.

*Role in course:* Practical supplement for Days 3 and 4; watch before writing your first real skill.

---

### 10. Claude Code: Official Walkthroughs
**Anthropic** | Anthropic YouTube channel (@anthropic-ai) | 2024–2025
Search: "Claude Code" on youtube.com/@anthropic-ai `[Verify specific video titles at time of reading — titles vary by release]`

Anthropic periodically publishes walkthrough videos demonstrating Claude Code features. The most useful for this course cover hooks, MCP server setup, and multi-agent workflows. Watch the most recent versions, as Claude Code ships frequently and older videos may show deprecated patterns.

*Role in course:* Practical supplement for Days 5, 6, 12. Watch after the day's page, not before.

---

## Optional Deeper Dives

### 11. The Art of Unix Programming
**Eric S. Raymond** | Addison-Wesley | 2003 | Free online: catb.org/esr/writings/taoup/

The philosophical source for hooks, pipelines, and the "do one thing well" principle that makes hooks composable. Chapter 1 ("Philosophy") and Chapter 7 ("Multiprogramming") are relevant to Day 5's hook design and Day 13's CI/CD patterns.

---

### 12. "Attention Is All You Need"
**Vaswani et al.** | NeurIPS 2017 | arXiv:1706.03762

Not required for L1/L2, but if you want to understand *why* the context window is finite and why token position matters (relevant to Day 7's prompt caching discussion), this is the foundational paper. Read the abstract and Figure 1 only — the rest requires ML background.

---

### 13. "Chain-of-Thought Prompting Elicits Reasoning in Large Language Models"
**Wei et al.** | NeurIPS 2022 | arXiv:2201.11903

The paper behind the chain-of-thought technique used in complex skill prompts (Day 4). Understanding why "think step by step" works helps you write skill instructions that actually invoke it reliably.

---

*Last updated: April 2026. If a URL is broken, search the title directly — documentation moves but content persists.*
