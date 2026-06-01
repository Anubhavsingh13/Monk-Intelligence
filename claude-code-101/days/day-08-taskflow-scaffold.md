*[Claude Code 101](../../README.md) · Day 8 of 14*

# Day 8 — Case Study Kickoff: The TaskFlow Scaffold

> **Today's one idea:** A complete project CLAUDE.md is a contract between you, your team, and Claude — every decision in it should be explainable and every rule should be testable.
> **Reading time:** ~60 min · **Prereqs:** [Day 7](day-07-token-economy.md)
> **Primary source for today:** *The Pragmatic Programmer* (Thomas & Hunt), Ch. 7 "Before the Project"; Claude Code Docs — "CLAUDE.md guide"

---

## The hook

You have spent seven days building the theory. Today it lands.

We are going to build the complete Claude Code configuration for **TaskFlow** — the case study project that all remaining workflow days depend on. By the end of today you will have a production-ready scaffold: a full `CLAUDE.md`, a `settings.json`, the `/review`, `/test-gen`, and `/fix` skill stubs, and the four hooks from Day 5. Every piece has been justified by the preceding days; today you assemble them into a coherent whole.

But first: the project definition. Everything in a good `CLAUDE.md` is answerable to one question — *"Is this true about the project?"* You need to know the project before you can write honest documentation for it.

---

## The TaskFlow project definition

TaskFlow is a task management API used by roughly 50 enterprise clients. Here is everything you need to know to write its configuration:

**Repository structure:**
```
taskflow/
├── backend/
│   ├── app/
│   │   ├── main.py              # FastAPI application entry point
│   │   ├── core/
│   │   │   ├── config.py        # Settings (Pydantic BaseSettings)
│   │   │   ├── database.py      # SQLAlchemy engine + session factory
│   │   │   ├── result.py        # Result[T, Error] type
│   │   │   └── security.py      # JWT helpers
│   │   ├── models/
│   │   │   ├── task.py          # Task ORM model (id, title, status, assignee_id, project_id)
│   │   │   ├── user.py          # User ORM model
│   │   │   └── project.py       # Project ORM model
│   │   ├── schemas/             # Pydantic request/response schemas
│   │   ├── api/
│   │   │   └── v1/
│   │   │       ├── tasks.py     # Task route handlers (thin)
│   │   │       ├── users.py     # User route handlers
│   │   │       └── projects.py  # Project route handlers
│   │   └── services/
│   │       ├── task_service.py  # All task business logic
│   │       ├── user_service.py  # All user business logic
│   │       └── project_service.py
│   ├── alembic/
│   │   └── versions/            # Migration files — immutable once committed
│   ├── tests/
│   │   ├── conftest.py          # Fixtures (test DB, test client, factory functions)
│   │   ├── services/            # Service layer tests (most coverage here)
│   │   └── api/                 # Integration tests for route handlers
│   ├── scripts/
│   │   └── deploy_migrate.sh    # The ONLY way to run migrations in staging/prod
│   └── requirements.txt
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   ├── hooks/
│   │   └── api/                 # API client (auto-generated from openapi.yaml)
│   └── package.json
├── docs/
│   └── openapi.yaml             # Source of truth for API contract
├── .claude/
│   ├── CLAUDE.md
│   ├── settings.json
│   └── commands/
│       ├── review.md
│       ├── test-gen.md
│       └── fix.md
└── docker-compose.yml
```

**Tech stack:**
- Python 3.11, FastAPI 0.110, SQLAlchemy 2.0 (async), Alembic, pytest
- TypeScript 5, React 18, Vite, Vitest
- PostgreSQL 15, Redis (session cache)
- GitHub Actions (CI), Docker Compose (local dev)

**Architecture decisions (the non-obvious ones):**
1. Service layer pattern — all business logic in `app/services/`, route handlers are thin
2. Result type — services return `Result[T, Error]` from `app/core/result.py`, not exceptions
3. API contract first — `docs/openapi.yaml` is updated before route handler changes
4. Immutable migrations — `alembic/versions/` files are never edited post-commit
5. Async SQLAlchemy — all DB operations use `async`/`await`; never use synchronous session

**Known landmines:**
- Three production incidents in 2024 from direct `alembic upgrade head` in production
- Race condition in Q3 2024 caused by task status change outside `TaskService`
- Frontend API client breaks silently if `openapi.yaml` is not updated before deployment

---

## Building the intuition: the contract model

Think of `CLAUDE.md` as three nested contracts:

**Contract 1 — Facts:** "Here is what is true about this project." Claude must believe these facts, not infer or contradict them.

**Contract 2 — Conventions:** "Here is how we do things." Claude must follow these patterns, not substitute its own preferences.

**Contract 3 — Forbidden actions:** "Here is what must never happen." Claude must refuse these actions regardless of what the user asks, and the hooks enforce them even if Claude forgets.

Each contract serves a different failure mode:
- Without facts: Claude guesses about the project and gets it wrong
- Without conventions: Claude uses its training defaults, which may conflict with your standards
- Without forbidden actions: Claude does dangerous things because the user asked (or because it seemed reasonable in context)

A `CLAUDE.md` that has all three contracts is one that a new team member could read and understand exactly what kind of system they are working on, how it expects to be modified, and what lines must not be crossed.

---

## The formal picture: the complete TaskFlow scaffold

### `.claude/CLAUDE.md`

```markdown
# TaskFlow — Claude Code Configuration

<!-- STABLE CONTENT (cache-friendly) — do not edit mid-session -->

## Project Facts

TaskFlow is a task management API serving ~50 enterprise clients.
**Uptime SLA: 99.9%. Data loss is a P0 incident.**
Backend: Python 3.11 + FastAPI 0.110 + SQLAlchemy 2.0 (async).
Frontend: TypeScript 5 + React 18 (API client auto-generated from openapi.yaml).
Database: PostgreSQL 15 + Redis (session cache).
Auth: JWT via python-jose; tokens expire in 1 hour.

## Architecture Decisions

**Service layer pattern.**
All business logic lives in `app/services/`. Route handlers in `app/api/v1/`
are thin: validate input → call service → return response. No ORM queries
in route handlers. Reason: race condition incident in Q3 2024 when handlers
modified task state directly.

**Result type over exceptions.**
Services return `Result[T, Error]` from `app/core/result.py`.
Never use `raise` in service layer for expected failures.
Exceptions are for programmer errors and infrastructure failures only.
Use: `return Result.ok(task)` or `return Result.err(TaskError.NOT_FOUND)`.

**Async SQLAlchemy.**
All DB operations use `async def` and `await`. Never use synchronous
Session or .query(). Use `AsyncSession` and `select()` statements.

**API contract first.**
`docs/openapi.yaml` is the source of truth. The frontend API client is
auto-generated from this file. Update `openapi.yaml` BEFORE changing any
route handler's request or response shape.

**Immutable migrations.**
`alembic/versions/` files are never edited after first commit.
Create a new migration for any schema change.

## Conventions

### Python
- Type hints on ALL function signatures (params + return type). No exceptions.
- `snake_case` for all identifiers (files, functions, classes use PascalCase only)
- Max line length: 100 characters
- Imports: stdlib → third-party → local, separated by blank lines
- Use `async def` for all service and route methods
- Pydantic v2 schemas in `app/schemas/` for all request/response bodies

### Testing
- Every service method needs at minimum: one happy-path test, one error-path test
- File structure mirrors source: `app/services/task_service.py`
  → `tests/services/test_task_service.py`
- Use pytest fixtures from `tests/conftest.py` for DB setup
- Never make real HTTP calls or DB connections in unit tests — use fixtures
- Test naming: `test_[method]_[scenario]_[expected_outcome]`
  Example: `test_create_task_duplicate_title_returns_error`
- Assert on `.is_ok()` / `.is_err()` / `.value` / `.error` from the Result type

### API
- Route handlers live in `app/api/v1/` only
- All new endpoints: add to `docs/openapi.yaml` first, implement second
- HTTP status codes follow RFC 7231: 201 for creates, 204 for no-content deletes
- Error responses use `{"detail": "human-readable message", "code": "SNAKE_CASE_CODE"}`

### Git
- Branch names: `feat/`, `fix/`, `refactor/`, `test/`, `docs/` prefixes
- Commits: imperative mood, present tense ("Add task status validation")
- Never commit directly to `main` or `production`

## FORBIDDEN — Hard stops (enforced by hooks)

- **Never run `alembic upgrade` directly.** Use `scripts/deploy_migrate.sh` only.
  Background: three production incidents in 2024 from direct migration execution.

- **Never edit files in `alembic/versions/`.** Create a new migration instead.

- **Never run `DROP TABLE`, `TRUNCATE`, or `DELETE FROM` without a WHERE clause.**
  Show the SQL and wait for explicit confirmation.

- **Never change task status outside `TaskService.update_status`.**
  Background: race condition incident in Q3 2024.

- **Never update `openapi.yaml` after changing route handlers.**
  Update `openapi.yaml` first, then implement the change.

- **Never push directly to `main` or `production` branches.**

- **Never hardcode credentials, API keys, or secrets in any file.**
  Use environment variables; set them in `.env.local` (gitignored).

## Workflow Patterns

### Before multi-file changes
1. List the files you plan to modify and what changes each requires
2. Wait for my confirmation before starting
3. Change one file at a time; run tests after each change

### When uncertain
- State uncertainty explicitly: "I'm not sure whether X or Y is the right approach here."
- Do not silently pick a pattern from training data if it may conflict with our conventions.
- Ask rather than assume for anything touching the service layer architecture.

### Running tests
```bash
cd backend && python -m pytest tests/ -v --tb=short
```

### Running the API locally
```bash
docker-compose up -d db redis
cd backend && uvicorn app.main:app --reload --port 8000
```

<!-- VARIABLE CONTENT (changes frequently, put at bottom) -->

## Active Work

- Current sprint: v2.1 — task assignment notifications feature
- Known failing test: `tests/services/test_notification_service.py::test_email_on_assign`
  (email mock not configured in CI — skip for now with `@pytest.mark.skip`)
- Do not touch `app/services/notification_service.py` — under active development
```

### `.claude/settings.json`

```json
{
  "permissions": {
    "allow": [
      "Read",
      "Glob",
      "Grep",
      "TodoWrite",
      "Bash(git status)",
      "Bash(git log *)",
      "Bash(git diff *)",
      "Bash(git add *)",
      "Bash(git commit *)",
      "Bash(git checkout *)",
      "Bash(git branch *)",
      "Bash(python -m pytest *)",
      "Bash(python -m flake8 *)",
      "Bash(python -m mypy *)",
      "Bash(uvicorn *)",
      "Bash(docker-compose *)",
      "Bash(npm *)",
      "Bash(npx *)"
    ],
    "deny": [
      "Bash(rm -rf *)",
      "Bash(git push --force *)",
      "Bash(alembic upgrade *)",
      "Bash(alembic downgrade *)",
      "Bash(psql * -c *DROP*)",
      "Bash(psql * -c *TRUNCATE*)"
    ]
  },
  "env": {
    "PYTHONPATH": "${workspaceFolder}/backend",
    "DATABASE_URL": "${env:DATABASE_URL}",
    "REDIS_URL": "${env:REDIS_URL}",
    "JWT_SECRET": "${env:JWT_SECRET}"
  },
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": ".claude/hooks/pre-bash-guard.sh"
          }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": ".claude/hooks/post-edit-lint.sh"
          },
          {
            "type": "command",
            "command": ".claude/hooks/secret-scan.sh"
          }
        ]
      }
    ],
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": ".claude/hooks/pre-stop-tests.sh"
          }
        ]
      }
    ]
  }
}
```

### `.claude/commands/review.md`

```markdown
---
description: Review a Python file or diff for TaskFlow architecture, type safety, test coverage, and security. Auto-activates when asked to review code or a pull request.
---

# TaskFlow Code Review

Read the file at $ARGUMENTS (or the most recently discussed code if no path given).
Apply the full review checklist from `.claude/commands/assets/review-checklist.md`.

Produce output in this exact format:

## Code Review: [filename]

### Summary
[2-3 sentences: overall quality, readiness to merge, primary concern]

### BLOCKING (must fix before merge)
- **[File:Line]** [Issue] — [Rule violated from CLAUDE.md]

### SUGGESTIONS (non-blocking improvements)
- **[File:Line]** [Suggestion] — [Rationale]

### STYLE NOTES
[Brief observations that don't rise to blocking level]

### What's Done Well
[1-3 specific things done correctly]

If a section has no findings, write "None found." Do not skip sections.
```

### `.claude/commands/test-gen.md`

```markdown
---
description: Generate a pytest test suite for a Python module. Auto-activates when asked to write, add, or create tests for a module or function.
---

# TaskFlow Test Generation

Read the source file at $ARGUMENTS.

Process:
1. Identify all public functions and methods
2. For each, determine: happy path, error paths, and edge cases
3. Check `tests/conftest.py` for available fixtures before creating new ones
4. Generate tests following TaskFlow conventions

Rules:
- Use pytest; no unittest.TestCase
- Use AsyncSession fixtures for DB operations
- Mock external services (email, third-party APIs)
- Name tests: `test_[method]_[scenario]_[expected]`
- Use Result pattern: assert `.is_ok()` or `.is_err()`
- Group tests in a class: `class Test[MethodName]:`

After generating, list any method where you could not determine
correct behavior and need clarification.
```

### `.claude/commands/fix.md`

```markdown
---
description: Investigate and fix a bug described in $ARGUMENTS. Auto-activates when asked to fix a bug, investigate an error, or resolve a failing test.
---

# TaskFlow Bug Fix Workflow

Bug description: $ARGUMENTS

Process (follow in order — do not skip steps):

1. **Understand the bug.** Read the error message, stack trace, or test failure carefully.
   Identify the file(s) involved before reading any code.

2. **Write a failing test first.** Before changing any production code, write a pytest
   test that reproduces the bug. Run it to confirm it fails.
   If you cannot write a reproducing test, stop and explain why.

3. **Identify the root cause.** Read only the files involved in the stack trace.
   Do not read unrelated files. State the root cause in one sentence.

4. **Fix the code.** Make the minimal change needed to fix the root cause.
   Do not refactor unrelated code in the same commit.

5. **Verify the fix.** Run the test from step 2 — it must now pass.
   Run the full test suite — no regressions allowed.

6. **Report.** Summarize: what was the bug, what was the root cause, what changed.
```

---

## Where it breaks / what it is not

**The scaffold is a starting point, not a finished product.** On Day 1 of a real project, your `CLAUDE.md` will be incomplete. Write what you know; add rules as you observe Claude making mistakes. A `CLAUDE.md` that grows from observed failures is more effective than one written speculatively.

**"Active Work" section will get stale.** The section at the bottom of the `CLAUDE.md` (sprint context, known failing tests) needs maintenance. If it goes stale, it becomes misinformation. Either update it at the start of every sprint, or remove it and manage this context in-session instead.

**Settings.json is not a substitute for infrastructure security.** The deny rules in `settings.json` stop Claude from running dangerous commands. They do not stop a human. Real production safety requires proper IAM, RBAC, and infrastructure controls.

---

## Try it yourself

**Exercise 1 — Read and interrogate the scaffold (comprehension check)**

Read each section of the CLAUDE.md above. For every rule in the FORBIDDEN section, identify:
1. The real incident or failure mode it is protecting against
2. Whether it is also enforced by a hook (belt-and-suspenders) or only advisory
3. One scenario where a developer might legitimately need to do the forbidden thing — and what the correct process is for that scenario

---

**Exercise 2 — Adapt the scaffold for your own project**

Take the TaskFlow scaffold as a template. Replace every TaskFlow-specific element with the equivalent from a project you actually work on. Focus on:
- The three most important architecture decisions (and their reasons)
- The three most dangerous things Claude could do to your codebase
- The two conventions that cause the most friction when violated

---

**Exercise 3 — Write the review-checklist.md asset (L2)**

The `/review` skill references `.claude/commands/assets/review-checklist.md`. Write that file — it should contain the full checklist that Claude applies during a review, organized by category. Each checklist item should reference the specific CLAUDE.md rule it is checking, so the review is explicitly tied to the project's own conventions, not generic style guidance.

---

## Connect it back

[Days 1–7](day-01-how-claude-code-works.md) were theory and technique. Today was application — all seven days' worth assembled into a single coherent project configuration.

Everything from Day 9 onward operates on top of this scaffold. Tomorrow you use the `/test-gen` skill against TaskFlow's `task_service.py` — a real module with real complexity — and see the difference between generating tests with a configured playbook vs. asking Claude cold.

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
- *The Pragmatic Programmer*, Chapter 7 "Before the Project" — Thomas & Hunt's discussion of project conventions, team standards, and why writing them down before coding saves time. The argument maps directly to why the CLAUDE.md scaffold is worth the upfront investment.

**If you want the deep version:**
- *Software Engineering at Google*, Chapter 8 "Style Guides and Rules" — Winters et al. on why style guides are engineering artifacts, not bureaucracy. The section "The goal of a style guide" is the theoretical foundation for the Conventions section of CLAUDE.md.
- Claude Code Documentation, "CLAUDE.md guide" — re-read after writing your own scaffold. The official guide's examples will now read differently; you have enough context to evaluate them critically.

---

← [Day 7 — The Token Economy](day-07-token-economy) &nbsp;|&nbsp; [Day 9 — Regression Test Pack Creation →](day-09-regression-test-pack)
