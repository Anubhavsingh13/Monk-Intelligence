*[Claude Code 101](../../README.md) · Day 3 of 14*

# Day 3 — Writing CLAUDE.md That Works + settings.json

> **Today's one idea:** Specificity beats length in CLAUDE.md; settings.json is the trust boundary that makes your instructions enforceable rather than merely advisory.
> **Reading time:** ~60 min · **Prereqs:** [Day 1](day-01-how-claude-code-works.md), [Day 2](day-02-claude-md-the-10-percent.md)
> **Primary source for today:** Claude Code Docs — "CLAUDE.md guide" + "Settings reference"; Anthropic Prompt Engineering Guide — "Be specific and direct"

---

## The hook

Two teams. Same project. Both have a `CLAUDE.md`. Neither has `settings.json`.

**Team A's CLAUDE.md (excerpt):**
```markdown
## Database
We use PostgreSQL. Be careful with database operations.
Don't break the database. Migrations should be done carefully.
```

**Team B's CLAUDE.md (excerpt):**
```markdown
## Database — Rules (non-negotiable)

- **Never execute `DROP TABLE`, `TRUNCATE`, or `DELETE` without a WHERE clause.**
  Show the SQL statement and wait for explicit "confirmed" before running.
- **Never run `alembic upgrade head` directly.** Use `scripts/deploy_migrate.sh`.
  Reason: direct migration ran in wrong environment twice in 2024, caused downtime.
- **Migration files in `alembic/versions/` are immutable once committed.**
  Never edit them. Create a new migration instead.
```

Same intent. Wildly different behavior. Claude can act on Team B's rules. Team A's rules are aspirations.

But here is the problem both teams share: `CLAUDE.md` rules are *advisory*. Claude reads them and tries to follow them. A clever prompt from the user, a long session that pushes the rules to the edge of context, or Claude simply reasoning itself into a justification — and the advisory rule gets broken.

`settings.json` closes that gap. Some rules should not be advisory. They should be enforced at the runtime level, before Claude's reasoning even runs.

Today you build both: a `CLAUDE.md` that actually instructs, and a `settings.json` that enforces.

---

## Building the intuition

### What makes an instruction Claude acts on

Compare these three ways of saying the same thing:

| Phrasing | What Claude does |
|----------|-----------------|
| "The project uses snake_case" | Treats as context; may follow, may not |
| "Use snake_case for all identifiers" | Treats as a soft preference; follows when writing new code, may miss when editing old code |
| "**All new identifiers must use snake_case.** If you see camelCase in existing code, note it but do not change it unless asked." | Has a clear rule, an explicit scope, and handles the ambiguous case (existing code) |

The third version is specific, scoped, and handles the edge case explicitly. This is the pattern.

**The anatomy of an effective CLAUDE.md instruction:**

```
[What to do / not do] + [Specific scope] + [What to do with ambiguous cases] + [Why, if non-obvious]
```

You do not always need all four parts. But the more consequential the rule, the more parts it earns.

### settings.json: the trust dial

`settings.json` operates at a different level from `CLAUDE.md`. Where `CLAUDE.md` tells Claude *what to do*, `settings.json` tells the *runtime* what Claude is *allowed* to do.

Think of it as the difference between:
- Telling a contractor "don't use power tools on the ceiling" (advisory)
- Giving them a toolbox that physically does not contain ceiling-rated power tools (enforced)

The settings file has two halves:

**Half 1: Permissions** — which tools require confirmation, which are always allowed, which are always blocked.

**Half 2: Environment** — environment variables, hook definitions, and other runtime behavior.

Together, they define the security perimeter for any Claude Code session in that project.

### The permission model

By default, Claude Code asks for user confirmation before most tool calls that modify state (file writes, bash commands). This is safe but slow. `settings.json` lets you tune the dial:

```
ALWAYS ALLOW ←————————————————————→ ALWAYS BLOCK
     |                                    |
(skip confirmation)              (runtime refuses)
     |                                    |
 Read-only tools              rm -rf, production deploys
 Bash in test dirs            Direct DB writes
```

The default for anything not in your settings is "ask for confirmation." You move tools left (auto-allow) when you trust them and the friction is not worth it. You move tools right (block) for things that should never happen in a Claude session regardless of what the user asks.

---

## The formal picture

### The complete CLAUDE.md template

Here is the full template, with annotations. Use it as a starting point — delete sections you don't need.

```markdown
# [Project Name] — Claude Code Configuration

## Project Facts
<!-- 3-5 sentences max. What this system does and its critical constraints. -->
TaskFlow is a task management API serving ~50 enterprise clients.
Backend: Python 3.11 + FastAPI. Frontend: TypeScript + React 18.
Database: PostgreSQL 15. Auth: JWT via `python-jose`.
Uptime SLA: 99.9%. Data loss is a P0 incident.

## Architecture
<!-- Non-obvious decisions and the reasons behind them. Not a description — a rationale. -->
- **Service layer pattern:** All business logic lives in `app/services/`.
  Route handlers in `app/api/` are thin — they validate input, call a service, return a response.
  Reason: we had race conditions when handlers modified state directly in 2024.
- **Result type over exceptions:** Services return `Result[T, Error]` (see `app/core/result.py`),
  not raised exceptions. Exceptions are for truly unexpected failures only.
- **No direct ORM queries in routes.** Always go through the service layer.

## Conventions
<!-- Specific, testable rules. If it can't be verified by inspection, it's too vague. -->

### Python
- Type hints on all function signatures (params + return type)
- `snake_case` for everything (files, functions, variables, modules)
- Max line length: 100 characters
- Imports: stdlib → third-party → local, separated by blank lines

### API
- Route handlers live in `app/api/v1/` — never in `app/api/` directly
- All new endpoints require a corresponding entry in `docs/openapi.yaml` BEFORE implementation
- HTTP status codes: follow RFC 7231 strictly (201 for creates, 204 for deletes, etc.)

### Testing
- Every new service method requires at least one happy-path and one error-path test
- Test files mirror source structure: `app/services/task_service.py` → `tests/services/test_task_service.py`
- Use `pytest` fixtures for database setup; never use `unittest.TestCase`

### Git
- Branch naming: `feat/`, `fix/`, `refactor/`, `test/` prefixes
- Commit messages: imperative mood ("Add user auth" not "Added user auth")
- PRs require at least one passing CI run before merge

## FORBIDDEN — Never do these
<!-- These are hard stops. Claude should refuse or ask for explicit confirmation. -->

- **Never run `DROP TABLE`, `TRUNCATE`, or `DELETE FROM [table]` without a WHERE clause.**
  Show the SQL and wait for explicit "confirmed" before executing.

- **Never run `alembic upgrade head` directly.** Use `scripts/deploy_migrate.sh` only.

- **Never edit files in `alembic/versions/`.** Migration files are immutable post-commit.
  Create a new migration instead.

- **Never commit secrets, API keys, or credentials.** Use `.env.local` (gitignored).

- **Never push directly to `main` or `production` branches.**

- **Never change `app/core/result.py` without a design discussion.**
  It is a load-bearing abstraction used across 40+ service files.

## Workflow Patterns
<!-- How Claude should approach common tasks on this project. -->

### Before making multi-file changes
1. List the files you plan to modify and what you will change in each
2. Wait for confirmation before starting
3. Make changes one file at a time, not all at once

### When you are uncertain
- Say so explicitly: "I'm not certain whether X or Y is correct here."
- Do not silently pick the more common pattern from training data if it conflicts with this project's conventions.

### Running tests
```bash
cd backend && python -m pytest tests/ -v
```
Run tests after any code change. If tests fail, fix them before proceeding.
```

### The settings.json schema

Location: `.claude/settings.json` (project) or `~/.claude/settings.json` (global).

```json
{
  "permissions": {
    "allow": [
      "Read",
      "Glob",
      "Grep",
      "Bash(git status)",
      "Bash(git log *)",
      "Bash(git diff *)",
      "Bash(python -m pytest *)",
      "Bash(npm test *)"
    ],
    "deny": [
      "Bash(rm -rf *)",
      "Bash(git push --force *)",
      "Bash(alembic upgrade *)",
      "Bash(psql * DROP *)"
    ]
  },
  "env": {
    "PYTHONPATH": "${workspaceFolder}/backend",
    "DATABASE_URL": "${env:DATABASE_URL}"
  },
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "cd backend && python -m flake8 ${file} --max-line-length=100 2>&1 | head -20"
          }
        ]
      }
    ]
  }
}
```

**Key fields:**

`permissions.allow` — a list of tool invocation patterns that are auto-approved (no confirmation prompt). Supports glob-style matching: `"Bash(git *)"` allows any bash command starting with `git`.

`permissions.deny` — a list of patterns that are always blocked. The runtime rejects these before Claude even sees the confirmation flow. This is the enforcement layer.

`env` — environment variables injected into every Claude Code session. Use `${env:VAR}` to forward from the shell environment (never hardcode secrets in this file).

`hooks` — shell commands that run at specific tool events. This is preview content — full treatment in [Day 5](day-05-behavior-gates-hooks.md).

### Allow vs. deny: how conflicts are resolved

If a tool call matches both an `allow` and a `deny` pattern, **deny wins**. The deny list is the hard floor; the allow list is a convenience layer above it.

If a tool call matches neither, it falls back to the default behavior: Claude Code asks the user for confirmation.

```
Tool call arrives
      │
      ▼
Matches deny list? ──YES──► Blocked (runtime error, no Claude confirmation)
      │
      NO
      ▼
Matches allow list? ──YES──► Auto-approved (no confirmation prompt)
      │
      NO
      ▼
Default: ask user for confirmation
```

---

## Where it breaks / what it is not

**Putting secrets in settings.json.** The file is often committed to version control. Use `${env:SECRET_NAME}` to forward from your shell environment, and set real values in `.env.local` (gitignored). Never hardcode API keys, database passwords, or tokens.

**Deny patterns that are too broad.** `"Bash(*)"` denies all bash commands — including things like `python -m pytest`. Think about what you are actually blocking, not just what you want to block.

**Allow patterns that are too broad.** `"Bash(*)"` in the allow list skips confirmation for every bash command, including destructive ones. The allow list is where you should be most conservative.

**CLAUDE.md contradicting settings.json.** If `CLAUDE.md` says "always use alembic directly" but `settings.json` denies `Bash(alembic *)`, Claude will be confused. Keep them consistent.

**The "deny overrides human intent" problem.** If a user explicitly asks Claude to do something on the deny list, Claude will refuse — even if the user is aware of the risk. This is a feature, not a bug, but it can frustrate users who need to do a one-off operation. The right solution: remove the deny rule for that session, do the operation, restore the rule. Not: remove the deny rule permanently.

---

## Try it yourself

**Exercise 1 — Specificity rewrite (comprehension check)**

Take each of these vague CLAUDE.md rules and rewrite them to be specific, scoped, and edge-case-aware:

1. "Be careful with the database."
2. "Follow the project's naming conventions."
3. "Don't break things."

There is no single correct answer, but a good rewrite will pass the test: could another developer read it and know exactly what Claude should do in any specific situation?

---

**Exercise 2 — Write CLAUDE.md for TaskFlow**

Using the template above as a guide, write the Project Facts, Architecture, and Conventions sections for TaskFlow. Use what you know:
- Python 3.11 FastAPI backend
- TypeScript/React frontend
- Service layer pattern
- PostgreSQL database
- The result pattern for service returns

Focus on making each item in Conventions specific and testable. Avoid paragraphs — use bullet points with imperative verbs.

---

**Exercise 3 — Write a settings.json for TaskFlow (L2 exercise)**

Write a `.claude/settings.json` for TaskFlow that:
1. Auto-approves: all read-only operations (`Read`, `Glob`, `Grep`, `Bash(git *)`, `Bash(python -m pytest *)`)
2. Denies: `rm -rf`, `git push --force`, `alembic upgrade head`, any `psql` command containing `DROP`
3. Sets `PYTHONPATH` to `./backend`

Then answer: what happens if someone asks Claude to run `git push --force origin main`?

---

## Connect it back

[Day 2](day-02-claude-md-the-10-percent.md) gave you the understanding that `CLAUDE.md` should instruct, not describe. Today you have the template and the pattern to actually write it. You also have `settings.json` — the runtime-level enforcement that turns advisory rules into hard constraints.

Tomorrow you step up a level: skills. You have given Claude standing instructions (CLAUDE.md) and constraints (settings.json). Tomorrow you give Claude reusable workflows it can execute on demand — and you see why that distinction changes everything about how you work.

Sharp question to carry forward: your `CLAUDE.md` now tells Claude *how to work on this project*. But you still have to type the same request every time you want a code review, a test suite generated, or a bug investigated. What would it look like if you didn't have to?

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
- Anthropic Prompt Engineering Guide, "Be specific and direct" section — docs.anthropic.com/en/docs/build-with-claude/prompt-engineering. The principles in this guide are the theory behind everything you practiced today. Particularly: "Use structured formats," "Provide examples," and "Specify the output format."

**If you want the deep version:**
- Claude Code Documentation, "Settings reference" — the complete JSON schema for `settings.json`, including all permission pattern syntax and hook configuration options. Read before Day 5.
- *The Pragmatic Programmer*, Chapter 3 "The Basic Tools" — Thomas & Hunt's argument for investing in your tools and environment is directly applicable to the time you spend on `CLAUDE.md` and settings. The core idea: time spent sharpening the tool is not overhead, it is the work.

---

← [Day 2 — CLAUDE.md: The 10% That Changes Everything](day-02-claude-md-the-10-percent) &nbsp;|&nbsp; [Day 4 — Skills: From Prompt to AI Playbook →](day-04-skills-from-prompt-to-playbook)
