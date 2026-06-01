# Claude Code 101

> **The promise:** In 14 days you will stop prompting Claude Code and start deploying it — configuring the context it receives, the guardrails it operates within, the workflows it executes, and the agents it coordinates.

---

## Who this course is for

You have used Claude Code. You have typed prompts, gotten code back, and found it impressive but inconsistent. You have probably written a `CLAUDE.md` file, added some project context, and thought "that's the configuration done." This course is built for the moment you realize that is only Layer 1 of 4.

This is an **L1/L2 course**: you will gain the practitioner's fluency to deploy Claude Code confidently on real engineering work, and the builder's depth to implement and debug the configuration layers yourself. You do not need to understand Claude's internals or machine learning. You do need to be comfortable with Python or TypeScript, understand basic git workflows, and be willing to treat your AI tool like software — something you configure, test, and iterate on.

If you want to read papers on LLM architecture or contribute to Claude Code's source code, this is not that course. If you want Claude Code to make your engineering team measurably faster and more consistent **by next month**, you are in the right place.

---

## The arc at a glance

On Day 1 you learn that Claude Code has no memory by default — every session is a blank slate and every byte of context is budget you spend. By Day 7, you have the full configuration toolkit to make that blank slate arrive pre-loaded with everything Claude needs. The first half of the course (Days 1–7) is about building that infrastructure: `CLAUDE.md`, settings, skills, hooks, MCP servers, and token discipline. The second half (Days 8–13) deploys it on real engineering work — test generation, code review, refactoring, bug fixing, and new features — all against the same **TaskFlow** codebase, so each new skill has immediate, concrete evidence it works.

The single story this course tells: **the gap between a developer who uses Claude Code and one who deploys it is not intelligence — it's configuration.** A well-configured Claude session costs less, produces more consistent output, and requires less hand-holding than an unconfigured one. By Day 14, the capstone asks you to prove that claim by building the configuration from scratch for a project you have never seen before.

---

## How to use this course

- **Rhythm:** One page per day, approximately one hour of focused reading. Do not skip ahead; each day is a prerequisite for the next.
- **Exercises:** Non-optional at L2. At L1, do at minimum the first exercise on each page — the comprehension check. Skip the stretch exercises only if you are short on time, not because they look difficult.
- **Bibliography:** `bibliography.md` is the annotated Course Shelf. When a page marks something "Required if you have 15 extra minutes," treat it as part of the day.
- **Rest and synthesize (Day 6):** This is not a day off. It is a day where you draw the wiring diagram of everything you have built, audit your configuration, and find the gaps. Budget the full hour.
- **If life gets in the way:** Miss a day, pick up where you left off. Do not cram two pages into one sitting — the exercises need the thinking time that sleep provides.

---

## The learning path

| Day | Title | One Idea | Primary Source |
|-----|-------|----------|----------------|
| 0 | [Installation + Project Setup](days/day-00-installation-project-setup.md) | Set up once, correctly — for new and existing projects — and every future session starts loaded | Claude Code Docs |
| 1 | [How Claude Code Actually Works](days/day-01-how-claude-code-works.md) | Claude is a context window with tools — stateless by default | Claude Code Docs |
| 2 | [CLAUDE.md — The 10% That Changes Everything](days/day-02-claude-md-the-10-percent.md) | CLAUDE.md is a briefing, not a README | Claude Code Docs |
| 3 | [Writing CLAUDE.md That Works + settings.json](days/day-03-writing-claude-md-and-settings.md) | Specificity beats length; settings.json is the trust boundary | Claude Code Docs + Prompt Engineering Guide |
| 4 | [Skills — From Prompt to AI Playbook](days/day-04-skills-from-prompt-to-playbook.md) | A skill is a reusable workflow package, not a saved prompt | Claude Code Docs |
| 5 | [Behavior Gates — Hooks as Deterministic Guardrails](days/day-05-behavior-gates-hooks.md) | Hooks inject determinism into a probabilistic system | Claude Code Docs |
| 6 | [MCP Servers + Full Wiring Diagram](days/day-06-mcp-servers-wiring-diagram.md) | MCP expands Claude's reach; the wiring diagram connects all four layers | MCP Specification |
| 6b | [MCP Server Setup — Installing and Building](days/day-06b-mcp-server-setup.md) | Installing an existing server takes five minutes; building a custom one takes an afternoon — the wiring is identical either way | MCP SDK Docs |
| 7 | [The Token Economy](days/day-07-token-economy.md) | Four levers — hygiene, compaction, caching, sub-agents — each cuts cost differently | Building Effective Agents |
| 8 | [Case Study Kickoff — TaskFlow Scaffold](days/day-08-taskflow-scaffold.md) | A complete CLAUDE.md is a contract between you, your team, and Claude | Pragmatic Programmer |
| 9 | [Regression Test Pack Creation](days/day-09-regression-test-pack.md) | `/test-gen` makes Claude infer boundary conditions, not just echo templates | Software Eng. at Google |
| 10 | [Code Review + Refactoring](days/day-10-code-review-refactoring.md) | `/review` checks against your CLAUDE.md conventions, not generic style rules | Refactoring (Fowler) |
| 11 | [Bug Investigation + Fixing](days/day-11-bug-investigation-fixing.md) | Scoping is the skill: give Claude the error, the trace, the test, and the files — not the repo | Claude Code Docs |
| 12 | [Team Orchestration — Orchestrator, Map-Reduce, Isolated Context](days/day-12-team-orchestration.md) | Isolated sub-agent contexts prevent cross-contamination and unlock parallelism | Building Effective Agents |
| 13 | [CI/CD Integration + Team Collaboration](days/day-13-cicd-team-collaboration.md) | Hooks fire on git events; CLAUDE.md lives in the repo — Claude becomes a team member | Accelerate |
| 14 | [Capstone — Design a Workflow from Scratch](days/day-14-capstone.md) | Produce a complete CLAUDE.md, three skills, one hook, and a token audit for a new project | All |

---

## The Course Shelf (top 5)

1. **Claude Code Official Documentation** — docs.anthropic.com/en/docs/claude-code — authoritative reference for every configuration option discussed in this course.
2. **"Building Effective Agents"** — Anthropic Engineering Blog, December 2024 — foundational thinking on agentic loops and multi-agent orchestration; spine for Days 7 and 12.
3. **"Prompt Engineering Guide"** — Anthropic docs — how to write instructions Claude reliably follows; essential for CLAUDE.md authorship and skill design.
4. **"Software Engineering at Google"** — Winters, Manshreck & Wright, O'Reilly 2020 — authoritative on code review practice and team-scale conventions; spine for Days 9–10 and 13.
5. **"Refactoring" (2nd Ed.)** — Martin Fowler, Addison-Wesley 2018 — the named catalog of refactoring patterns Claude can be instructed to apply; spine for Day 10.

Full annotated shelf with chapter-level guidance: [bibliography.md](bibliography.md)

---

## The capstone

On Day 14, you receive a one-page brief describing a software project you have not seen before. Your deliverable: a complete `CLAUDE.md` scaffold (project facts, architecture, conventions, forbidden actions, workflow patterns), three skills (`/test-gen`, `/review`, `/fix`), one hook of your choice, and a written token-optimization audit explaining your context hygiene decisions. "Done" means another developer can clone your configuration, run Claude Code against the project, and get consistent, safe, useful output on their first session — without asking you how any of it works.

---

## Glossary

Alphabetical definitions with plain-English and formal forms: [glossary.md](glossary.md)

---

## Meta

| | |
|---|---|
| **Depth level** | L1 (Practitioner) + L2 (Builder) |
| **Estimated total hours** | 15 hours reading + exercises; 2–4 hours for the capstone |
| **Case study stack** | Python 3.11 (FastAPI) + TypeScript/React — see [Day 8](days/day-08-taskflow-scaffold.md) for full project definition |
| **Last updated** | April 2026 |
| **Feedback** | Open an issue at the repository where this course lives |
