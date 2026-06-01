# Learning Path — Claude Code 101

> Full day-by-day rationale for how the course is sequenced and why each page earns the next.

---

## The four-layer architecture this course builds

Every page is in service of one of four layers:

| Layer | What It Is | Days |
|-------|-----------|------|
| **Layer 1: Project Memory** | `CLAUDE.md` — tribal knowledge, architecture decisions, what NOT to do | 2, 3, 8 |
| **Layer 2: Behavior Gates** | Hooks — deterministic guardrails around AI autonomy | 5, 13 |
| **Layer 3: Specialized Workflows** | Skills — reusable workflow packages that build an AI playbook | 4, 9, 10, 11, 12 |
| **Layer 4: Team Orchestration** | Agents + sub-agents — orchestrator, map-reduce, isolated context | 7, 12, 13 |

Days 1, 6, 7, and 14 are cross-cutting: they build the mental model, wire up the layers, and synthesize everything.

---

## Module 0 — Setup (Day 0)

### Day 0: [Installation + Project Setup](days/day-00-installation-project-setup.md)
**Why it comes before everything else:** You cannot experiment with any concept in this course without a working Claude Code installation and at least a minimal project configuration. Day 0 is purely procedural — it covers installation, authentication, global config, and the full bootstrap sequence for both new and existing projects. It introduces no theory; that starts on Day 1. Do Day 0 with your project open in your editor so every step is applied immediately. It is the only day you should complete before reading the next one.

---

## Module 1 — Mental Model (Days 1–2)

### Day 1: [How Claude Code Actually Works](days/day-01-how-claude-code-works.md)
**Why it comes first:** You cannot optimize a system you do not have a model of. Before touching any configuration file, you must understand that Claude Code is stateless by default — a context window with tools attached. Every optimization in this course is an answer to one question: *what should be in that context window, and what should not?*

### Day 2: [CLAUDE.md — The 10% That Changes Everything](days/day-02-claude-md-the-10-percent.md)
**Why it comes second:** The single most important insight in the course — that `CLAUDE.md` is a briefing document, not a README, and that most people stop at Layer 1 — arrives here. This page motivates the remaining 13 days.

---

## Module 2 — Configuration Mastery (Days 3–6)

### Day 3: [Writing CLAUDE.md That Works + settings.json](days/day-03-writing-claude-md-and-settings.md)
**Why it comes here:** Knowing what `CLAUDE.md` is (Day 2) and knowing how to write one that actually changes behavior are different skills. This page closes that gap and introduces `settings.json` as the paired trust-boundary file.

### Day 4: [Skills — From Prompt to AI Playbook](days/day-04-skills-from-prompt-to-playbook.md)
**Why it comes here:** After establishing what Claude knows (CLAUDE.md) and what Claude is allowed to do (settings.json), we extend Claude's capabilities with reusable, parameterized workflow packages. Skills are the highest-leverage tool in this course — every workflow day (9–13) depends on them.

### Day 5: [Behavior Gates — Hooks as Deterministic Guardrails](days/day-05-behavior-gates-hooks.md)
**Why it comes here:** Skills give Claude more power; hooks ensure that power has hard limits. This ordering is intentional — you give Claude capability before you add the safety layer, so you understand exactly what you're constraining and why.

### Day 6: [MCP Servers + Full Wiring Diagram](days/day-06-mcp-servers-wiring-diagram.md)
**Why it comes here:** MCP servers are the final extension point — they give Claude access to external systems (databases, APIs, browsers). The second half of this day synthesizes everything into a single wiring diagram: what flows from session start to tool execution, and where each of the four layers intercepts that flow.

### Day 6b: [MCP Server Setup — Installing and Building](days/day-06b-mcp-server-setup.md)
**Why it comes here:** Day 6 explains *what* MCP servers are and *where* they fit. Day 6b is the operational complement — how to actually install a community server, debug a failed connection, and build a custom server in TypeScript or Python when no existing server does what you need. It is a supplement: read it the same day as Day 6, or the day after if Day 6 was already a full hour. It has no new conceptual content — only practical execution.

---

## Module 3 — Token Optimization (Day 7)

### Day 7: [The Token Economy](days/day-07-token-economy.md)
**Why it comes here:** After building the configuration infrastructure, you have enough context to understand *why* optimization matters and *how* each lever (hygiene, compaction, caching, sub-agents) applies to the specific configuration patterns you've built. This day has four concepts — it is dense by design, because they are deeply connected.

---

## Module 4 — Software Engineering Workflows (Days 8–13)

### Day 8: [Case Study Kickoff — TaskFlow Scaffold](days/day-08-taskflow-scaffold.md)
**Why it comes here:** The first application day. Everything from Days 1–7 is applied to a real (simulated) project. The TaskFlow `CLAUDE.md` scaffold built here is the foundation every subsequent workflow day builds on.

### Day 9: [Regression Test Pack Creation](days/day-09-regression-test-pack.md)
**Why it comes here:** Test generation is the easiest workflow to verify — the output is runnable code with a pass/fail signal. It is the ideal first workflow to demonstrate the ROI of the `/test-gen` skill over ad-hoc prompts.

### Day 10: [Code Review + Refactoring](days/day-10-code-review-refactoring.md)
**Why it comes here:** Review and refactoring are paired because review identifies what refactoring must fix. Both depend on CLAUDE.md conventions (Day 3) and the `/review` skill (Day 4).

### Day 11: [Bug Investigation + Fixing](days/day-11-bug-investigation-fixing.md)
**Why it comes here:** Bug workflows require the discipline established in Days 9–10: you need a reproducing test (Day 9) and a clean codebase (Day 10) to fix bugs reliably. Triage and scoping are the new skills this day adds.

### Day 12: [Team Orchestration — Orchestrator, Map-Reduce, Isolated Context](days/day-12-team-orchestration.md)
**Why it comes here:** By Day 12, you have enough workflow complexity (tests, review, refactoring, bugs, features) that the value of multi-agent parallelism is obvious — you've felt the pain of long serial sessions. This day introduces the architectural patterns that solve it.

### Day 13: [CI/CD Integration + Team Collaboration](days/day-13-cicd-team-collaboration.md)
**Why it comes last in the workflow arc:** CI/CD integration requires all four layers working together. It is the organizational layer that makes individual capability into team infrastructure.

---

## Module 5 — Mastery (Day 14)

### Day 14: [Capstone — Design a Workflow from Scratch](days/day-14-capstone.md)
**Why it comes last:** The capstone has no new concepts. It forces synthesis by removing the scaffolding — a new project, a blank page, and 13 days of tools. The deliverable is a complete, runnable configuration.

---

## Prerequisite map

```
Day 0 (install + bootstrap)
  └── Day 1
        └── Day 2
              └── Day 3
                    ├── Day 4 ──────────────────────────────┐
                    │     └── Day 9 (skills applied)        │
                    ├── Day 5 ──────────────────────────────┤
                    │     └── Day 13 (hooks in CI/CD)       │
                    └── Day 6                               │
                          └── Day 7                         │
                                └── Day 8 ─────────────────┼──── Day 14
                                      └── Day 10            │
                                      └── Day 11            │
                                      └── Day 12 ───────────┘
                                      └── Day 13
```
