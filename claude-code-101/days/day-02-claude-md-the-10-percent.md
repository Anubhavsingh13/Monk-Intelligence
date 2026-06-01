*[Claude Code 101](../../README.md) · Day 2 of 14*

# Day 2 — CLAUDE.md: The 10% That Changes Everything

> **Today's one idea:** CLAUDE.md is a briefing document, not a README — and understanding that distinction is the difference between Layer 1 and all the layers above it.
> **Reading time:** ~60 min · **Prereqs:** [Day 1](day-01-how-claude-code-works.md)
> **Primary source for today:** [Claude Code Official Documentation](https://docs.anthropic.com/en/docs/claude-code) — "CLAUDE.md guide" section

---

## The hook

Here is a real pattern that happens across engineering teams that adopt Claude Code:

**Week 1:** Developer adds a `CLAUDE.md` to their project. Writes some notes about the tech stack, the project structure, maybe the deployment process. Claude now knows the basics. Consistency improves slightly. Developer thinks: "OK, I see what this is for."

**Week 2–12:** Nothing changes. The `CLAUDE.md` grows slowly, accumulates more descriptive notes. Claude still makes the same class of mistakes — it edits files it shouldn't touch, uses deprecated patterns, ignores the team's naming conventions, occasionally suggests approaches that were tried and abandoned months ago for good reasons.

**The realization:** The developer reads their `CLAUDE.md` critically for the first time and notices it reads exactly like a `README.md`. It describes the project. It does not *instruct* Claude. It is a map of the territory, not a set of orders.

This is where 90% of teams stop. The `CLAUDE.md` becomes a better README — and a README is not nothing, but it is not leveraging 90% of what this file can do.

Today you learn what the other 90% looks like, why it matters, and how the file's hierarchical structure enables something that a single README never could: team-shared conventions with individual overrides.

---

## Building the intuition

### The briefing vs. the manual

Imagine you are onboarding a new contractor to your team. You have two documents you could hand them:

**Document A — The Manual:**
> "TaskFlow is a task management API. The backend is built with Python 3.11 and FastAPI. The frontend uses TypeScript and React 18. The database is PostgreSQL. The repository has the following structure: `backend/` contains the API code, `frontend/` contains the React application..."

**Document B — The Briefing:**
> "Here is what you need to know on day one:
>
> 1. **Never run migrations in production directly.** Always use the migration script in `scripts/deploy_migrate.sh`. Three times we have had incidents from people running `alembic upgrade head` directly.
>
> 2. **The `TaskService` class is the only place task state should change.** If you are tempted to update task status from a route handler, stop and go through `TaskService`. This was an architectural decision made after the race condition incident in Q3.
>
> 3. **All API changes require updating `docs/openapi.yaml` first.** Not after. The frontend team consumes this spec for type generation and they deploy on a separate schedule.
>
> 4. We use `result` pattern for service returns, not exceptions. See `app/core/result.py`. Do not add `raise` statements in service layer."

Document A describes the project. Document B instructs the contractor.

Claude Code will read both with equal attention — but only Document B will actually change how it behaves. The key difference: **Document B contains rules Claude can act on, violations it can avoid, and patterns it should follow or reject.** Document A is information. Document B is direction.

A good `CLAUDE.md` is Document B.

### The signal-to-noise problem

Every line in your `CLAUDE.md` is in Claude's context window for the *entire session*. It costs tokens whether or not it is relevant to what Claude is doing right now. This creates an incentive to be ruthless about what you include.

The question to ask for every line you consider adding: **"If Claude didn't know this, would it make a mistake?"**

If the answer is yes — put it in. If the answer is "it's useful context" — leave it out. Useful context that doesn't change behavior is noise.

This is why long `CLAUDE.md` files are often less effective than short ones. The specific, behavioral instructions get diluted by descriptive paragraphs that Claude doesn't need to do its job.

A working rule of thumb: if you can say it with a bullet point and an imperative verb, it belongs. If you need a paragraph to describe the history, it probably doesn't.

### The hierarchy: three layers for three audiences

`CLAUDE.md` isn't a single file — it is a three-level hierarchy, each layer serving a different audience:

```
~/.claude/CLAUDE.md          ← Global: your personal preferences
                                 (applies to ALL projects you work on)
                                 NOT committed to version control
                                 Example: "Always use type hints in Python"

.claude/CLAUDE.md            ← Project: shared team conventions
                                 Committed to the repository
                                 Example: "Never modify the migrations table directly"

.claude/CLAUDE.local.md      ← Local: your personal project overrides
                                 Listed in .gitignore, NOT committed
                                 Example: "My local DB is at localhost:5433"
```

Claude loads all three at session start, in this order: global → project → local. Later layers can override earlier ones.

This structure solves a real problem. A team needs shared conventions (project layer). Each developer has personal preferences that should not be imposed on teammates (global layer). And individuals sometimes need local overrides for their specific environment — database port, local API keys, personal shortcuts — that would be wrong or unsafe to commit (local layer).

Without this hierarchy, teams typically do one of two things: put everything in the project `CLAUDE.md` (which means personal preferences pollute the team contract), or keep everything personal (which means Claude never benefits from shared team knowledge). The three-layer system resolves this tension cleanly.

---

## The formal picture

### What CLAUDE.md actually is under the hood

When a Claude Code session starts, the runtime:

1. Reads `~/.claude/CLAUDE.md` (if it exists)
2. Reads `.claude/CLAUDE.md` (if it exists, searching from the current directory up)
3. Reads `.claude/CLAUDE.local.md` (if it exists)
4. Concatenates all three, in that order
5. Injects the result verbatim into Claude's context as part of the session initialization

"Verbatim" is important. Claude Code does not parse or interpret your `CLAUDE.md` before injecting it. It is Markdown text, and Claude reads it exactly as you wrote it. This means:
- Formatting matters: headers and bullet points are read as structure, helping Claude parse the document
- Contradictions are just confusing: if you say "use type hints" in one section and "don't bother with type hints" in another, Claude will be inconsistent
- Length has a direct cost: every token in `CLAUDE.md` is a token spent before you even type your first message

### What belongs in each layer

**Global `~/.claude/CLAUDE.md` — your personal defaults**

This is where you put things that are true about *you as a developer*, not about any specific project. Examples:

```markdown
# My Coding Defaults

## Style
- Always include type hints in Python
- Prefer explicit over implicit (readability over brevity)
- When I ask for a refactor, show me the before/after diff before making changes

## Communication
- When you are uncertain, say so explicitly — don't guess silently
- Ask clarifying questions before starting large tasks
- Flag potential security issues even if I didn't ask about them

## Workflow
- Before editing multiple files, list what you plan to change and get my confirmation
- After making changes, summarize what you changed and why
```

**Project `.claude/CLAUDE.md` — the team contract**

This is the most important layer. It encodes the knowledge that lives in long-timers' heads and causes friction when it isn't written down. The four sections it must have:

1. **Project facts** — what the system does, the tech stack, the deployment environment. Keep this short.
2. **Architecture decisions** — *why* the system is structured the way it is, especially non-obvious decisions.
3. **Conventions** — naming, patterns, where things go, how things are done.
4. **Forbidden actions** — explicit, specific rules about what Claude must never do. This is the most valuable and most neglected section.

**Local `.claude/CLAUDE.local.md` — your personal project overrides**

Add this to `.gitignore`. Use it for environment-specific values that are true for your machine but wrong for your teammates:

```markdown
# Local overrides (do not commit)

- My local PostgreSQL runs on port 5433 (not 5432)
- I have a test API key in ~/.env.local — use that for testing
- Skip the Docker check when running tests locally; I run the DB natively
```

### The "Forbidden Actions" section — the highest leverage 5 minutes in this course

Most `CLAUDE.md` files describe what Claude should do. The forbidden actions section is about what Claude should *never* do — and it is disproportionately valuable because these are the actions that cause actual incidents.

The template:

```markdown
## FORBIDDEN — Never do these without explicit confirmation

- **Never run `DROP TABLE` or `DELETE FROM` without a WHERE clause.** Show the SQL and wait for confirmation.
- **Never modify files in `migrations/`** — migration files are immutable once committed.
- **Never push to `main` directly.** Always use a feature branch and PR.
- **Never import from `app.internal.*` in route handlers.** That namespace is for internal use only.
- **Never change the API contract (request/response shape) without first updating `docs/openapi.yaml`.**
```

Why does this work? Because Claude is probabilistic — given a task, it generates the most likely continuation. If your conventions are not in context, it will generate the most common pattern it has seen in training, which may not match your project. The forbidden actions section narrows the space of plausible continuations to exclude the dangerous ones.

---

## Where it breaks / what it is not

**CLAUDE.md is not dynamic.** It is loaded once at session start and does not update during the session. If Claude discovers something important mid-session — a bug in a critical module, a pattern it should avoid — that knowledge evaporates when the session ends unless you manually update `CLAUDE.md`. Treat it as static configuration, not a learning system.

**CLAUDE.md cannot hold secrets.** Do not put API keys, passwords, or credentials in any `CLAUDE.md` layer — especially not the project layer, which is committed to version control. Use environment variables (configured in `settings.json`) for secrets. See [Day 3](day-03-writing-claude-md-and-settings.md).

**CLAUDE.md cannot override the system prompt.** There are things Claude will refuse or behave differently on regardless of what you put in `CLAUDE.md` — safety guidelines, fundamental operating instructions. Your `CLAUDE.md` adds to Claude's context; it does not replace it.

**"Claude keeps ignoring my CLAUDE.md."** Usually this means one of three things:
1. The instruction is descriptive, not imperative ("the codebase uses FastAPI" vs. "always use FastAPI dependency injection, never direct service calls")
2. The instruction is too vague ("be careful with the database" vs. "never execute write operations on the `tasks` table outside of `TaskService`")
3. The context window is too full and Claude is not attending to earlier content (see [Day 7](day-07-token-economy.md) for context hygiene)

**"Longer is better — more context helps Claude."** Every additional paragraph in your `CLAUDE.md` dilutes the attention Claude gives to every other paragraph. The optimal `CLAUDE.md` is the minimum content needed to produce correct behavior. Start short, add only when you observe a mistake, and audit periodically to remove stale content.

---

## Try it yourself

**Exercise 1 — The README audit (comprehension check)**

Read your current project's `CLAUDE.md` (or any `CLAUDE.md` you have written). For each line, ask: "If Claude didn't know this, would it make a mistake?" Mark the lines where the answer is yes — those are earning their token cost. Count what fraction of the file is actually instructional vs. descriptive.

If you don't have a project `CLAUDE.md` yet, use this sample and do the audit on it:

```markdown
# MyApp

MyApp is a web application that helps users track their habits.
It uses Django for the backend and Vue.js for the frontend.
The database is MySQL. Authentication uses JWT tokens.

The project was started in 2022 by the platform team.
It runs on AWS EC2 instances behind a load balancer.

Tests are in the `tests/` directory. We use pytest.
The CI pipeline runs on GitHub Actions.
```

How much of this would actually change Claude's behavior?

---

**Exercise 2 — Write your first forbidden actions section**

Think of a project you are currently working on (or have worked on recently). Identify three specific actions Claude could take that would cause real problems — not hypothetical ones, but things that have actually caused incidents or near-misses in your team.

Write them as `CLAUDE.md` forbidden action rules using the imperative format:

```markdown
## FORBIDDEN

- **Never [specific action].** [One-line explanation of why.]
```

Be specific enough that there is no ambiguity about what counts as a violation.

---

**Exercise 3 — Design your global CLAUDE.md (stretch)**

Write a `~/.claude/CLAUDE.md` for yourself. Include three sections:
1. **Style** — how you prefer Claude to write code (type hints, formatting conventions, patterns you like/dislike)
2. **Communication** — how you want Claude to communicate (confirm before large changes, flag uncertainty, etc.)
3. **Workflow** — how you want Claude to approach tasks (show plan before executing, summarize changes after, etc.)

Keep it under 500 words. This file will be in context for every Claude Code session you ever run — budget it accordingly.

---

## Connect it back

[Yesterday](day-01-how-claude-code-works.md) you learned that Claude starts every session blank. Today you learned that `CLAUDE.md` is the mechanism for making the blank slate arrive pre-filled — but only if the file contains instructions, not just descriptions.

Tomorrow's sharp question: you now know *what* to put in `CLAUDE.md`. But knowing what to put in is different from writing it in a way Claude actually acts on. What is the difference between an instruction that changes behavior and one that gets read and ignored?

[Day 3](day-03-writing-claude-md-and-settings.md) answers that, and introduces `settings.json` — the file that makes your instructions enforceable rather than just advisory.

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
- Claude Code Official Documentation, "CLAUDE.md guide" — the authoritative section on file loading order, hierarchy, and what content is most effective. Read it alongside your notes from today's exercises. `[VERIFY URL at docs.anthropic.com/en/docs/claude-code]`

**If you want the deep version:**
- Anthropic Prompt Engineering Guide, "Be specific and direct" section — docs.anthropic.com/en/docs/build-with-claude/prompt-engineering. The same principles that make a good system prompt make a good `CLAUDE.md`. The guide's advice on format, imperative phrasing, and examples applies directly to `CLAUDE.md` authorship.
- *The Pragmatic Programmer* (Thomas & Hunt), Chapter 7 "Before the Project" — specifically the section on communicating expectations. The authors' advice on writing specifications that actually constrain behavior (vs. specifications that describe intent) is the engineering philosophy behind the forbidden actions section.

---

← [Day 1 — How Claude Code Actually Works](day-01-how-claude-code-works) &nbsp;|&nbsp; [Day 3 — Writing CLAUDE.md That Works + settings.json →](day-03-writing-claude-md-and-settings)
