*[Claude Code 101](../../README.md) · Day 14 of 14*

# Day 14 — Capstone: Build It from Scratch

> **Today's one idea:** Everything you have learned is only useful if you can apply it cold — a new project, a blank `.claude/` directory, and no scaffolding from this course. That is exactly the situation you are in now.
> **Reading time:** ~60 min (active work, not passive reading) · **Prereqs:** Days 1–13
> **Primary source for today:** *Your own judgment.* Every canonical source you need is in `bibliography.md`. Use them.

---

## The brief

You have been given access to a new project: **Fieldnotes**, a Python FastAPI service for scientific field researchers to log observations, attach photos, and sync to a team dashboard.

Here is everything you know about it:

```
Project: Fieldnotes API
Stack:   Python 3.12 · FastAPI · SQLAlchemy 2.0 async · PostgreSQL
Auth:    JWT via python-jose · bcrypt password hashing
Storage: Local filesystem (dev) · S3-compatible (prod)
Tests:   pytest + pytest-asyncio · no coverage threshold set yet
State:   Prototype complete · no CLAUDE.md · no .claude/ directory
Team:    3 developers · 1 has used Claude Code before · 2 are new

Core models:
  Researcher   (id, email, hashed_password, team_id, role)
  Observation  (id, researcher_id, project_id, body, lat, lon, recorded_at)
  Attachment   (id, observation_id, filename, s3_key, mime_type)
  Project      (id, team_id, name, is_archived)

Service layer conventions (from code review notes):
  - All business logic in app/services/
  - Route handlers in app/api/v1/ — thin, no ORM queries
  - Return type: Result[T, str] for all service methods
  - Pydantic v2 schemas in app/schemas/
  - Alembic migrations — never edit a migration after merge

Known production incidents:
  - Observation body was being truncated silently on update (fixed)
  - Attachment upload allowed any MIME type — exploit attempted (fixed)
  - JWT secret was hardcoded in a settings file that was committed (rotated)
```

You do not have the codebase. You have the brief above, everything you learned in Days 1–13, and a blank `.claude/` directory.

Your deliverable: a complete, commit-ready Claude Code configuration for Fieldnotes.

---

## What you are building

### Deliverable 1 — `.claude/CLAUDE.md`

A production-quality CLAUDE.md that another developer can pick up cold and immediately produce consistent, safe Claude Code output from.

**Required sections** (in this order, stable content first):

1. **Project Facts** — name, stack, purpose, team size, current phase
2. **Architecture** — directory structure, service layer rule, Result type, async patterns
3. **Conventions** — Python style (type hints, async, naming), Pydantic v2 idioms, test patterns
4. **FORBIDDEN** — at minimum: three rules derived from the production incidents above
5. **Active Work / Session Primer** — what the team is working on (you decide; make it plausible)

**Evaluation criteria:**
- Would a senior developer reading this for the first time understand the project without asking questions?
- Are the FORBIDDEN rules specific enough to catch violations, or vague enough to be ignored?
- Is the stable content (facts, architecture, conventions) at the top, and the volatile content (active work) at the bottom? (Token optimization: [Day 7](day-07-token-economy.md))
- Could Claude generate a service method that follows your conventions, or would it have to guess?

---

### Deliverable 2 — Three skills

#### `/review` for Fieldnotes

Adapt the TaskFlow `/review` skill (Day 8) to Fieldnotes conventions. Your checklist must include:

- Service layer compliance (no ORM in route handlers)
- Result type usage (no bare `raise` in services)
- Attachment security (MIME type validation present)
- Auth context (JWT user present where required)
- Schema completeness (Pydantic v2, not v1 `.dict()`)

Output format: same three-tier structure (BLOCKING / SUGGESTIONS / STYLE NOTES) — keep it, it works.

#### `/test-gen` for Fieldnotes

Adapt `/test-gen` (Day 9). The boundary analysis for Fieldnotes has one dimension TaskFlow did not: **geospatial inputs** (`lat`, `lon`). Your skill must instruct Claude to test:

- Valid lat/lon ranges (−90 to 90, −180 to 180)
- Boundary values (exactly 90.0, −180.0, 90.0001)
- None when the field is Optional

Everything else from the TaskFlow version applies.

#### `/fix` for Fieldnotes

The TaskFlow `/fix` skill (Day 11) is largely portable. Adapt it for Fieldnotes with one addition: if the bug involves an attachment or file operation, the failing test must use a temporary file fixture (not a real S3 call or real file path). Add this as an explicit step in your skill instructions.

---

### Deliverable 3 — One hook

Choose **exactly one** of the following. Implement it fully (working shell script + `settings.json` wiring).

**Option A — MIME type guard (PreToolUse)**
Block any Bash command that invokes `curl` or `httpx` with a `content-type` containing a non-allowlisted MIME type. Allowlist: `image/jpeg`, `image/png`, `application/json`. Block with exit code `2`.

**Option B — Migration safety gate (PostToolUse)**
After any file edit in `alembic/versions/`, check if the edited file already exists in `git log` (i.e., it is a previously merged migration being modified). If yes, block completion with exit code `2` and print a warning. If the file is new (not in git history), pass.

**Option C — JWT secret leak scan (PreToolUse)**
Before any Bash command that writes a file (`tee`, `cat >`, `echo >`, `> filename`), scan the command for patterns that look like JWT secrets (base64 strings matching HS256/RS256 patterns, or strings containing `SECRET_KEY`). Block with exit code `2` if found.

Your hook must include:
- The shell script (complete, tested logic — not pseudocode)
- The `settings.json` entry wiring it to the correct event
- A one-paragraph explanation of why you chose this hook over the others for this specific project

---

### Deliverable 4 — Token optimization audit

Write a short document (200–400 words) that answers these questions for your Fieldnotes configuration:

1. **Context hygiene decisions:** Which sections of your CLAUDE.md did you put at the top, and why? What would happen to cache hit rate if Active Work were at the top instead of the bottom?

2. **Session boundary decisions:** For a workflow where a developer runs `/review` on 5 files, then `/fix` on the worst finding, then `/test-gen` on the fixed file — where should they compact, and why? What context is valuable to carry forward, and what is noise?

3. **Sub-agent opportunity:** The team wants to run a full sprint review: `/review` on 8 changed Python files + a security scan + a test coverage check, all before merging a release branch. Which orchestration pattern applies? How many sub-agents? What does the orchestrator receive from each?

4. **One thing you would not automate:** Given everything you know about Fieldnotes, what is one check you explicitly chose *not* to put in a hook or CI workflow, and why? (There is no single correct answer — the reasoning matters.)

---

## How to work through this

This is a design exercise, not a transcription exercise. The goal is not to copy-paste from Days 8–13. It is to make judgment calls under constraints you have not seen before.

**Suggested order:**

1. **Read the brief carefully** (10 min). Make notes on what is ambiguous. For anything ambiguous, decide and document your assumption — do not leave it undefined.

2. **Draft CLAUDE.md first** (15 min). The CLAUDE.md is the foundation. The skills, hook, and audit all depend on what conventions you commit to here. A vague CLAUDE.md produces a weak skill and an untestable hook.

3. **Write the skills** (15 min). Start from the TaskFlow versions (Days 8–11 have the full templates). Adapt rather than rewrite. The differences should be small and deliberate.

4. **Implement the hook** (10 min). Pick your option before writing a line of shell. Write the detection logic first, the exit codes second, the `settings.json` wiring third.

5. **Write the audit** (10 min). This is the section that reveals whether you have internalized the token economy (Day 7) or just memorized it. Write it last, after the decisions are made — then explain them honestly.

---

## What "done" looks like

Another developer can:

1. Clone Fieldnotes
2. Copy your `.claude/` directory into the project root
3. Run `claude --print "What project are we working on?"` → Get a coherent answer from CLAUDE.md
4. Run `/review app/services/observation_service.py` → Get structured output with BLOCKING / SUGGESTIONS / STYLE NOTES
5. Run `/test-gen app/services/observation_service.py` → Get a test file with boundary tests including lat/lon validation
6. Run `/fix` on a described bug → Get a failing test written before any production code changes
7. Accidentally run a command the hook guards against → Get blocked with a clear message

They do this **without asking you how any of it works**.

---

## Reflection: the four-layer audit

After completing your deliverables, run this audit against your own work:

| Layer | What you built | Question to answer |
|-------|---------------|---------------------|
| **Layer 1 — Project Memory** | `CLAUDE.md` | Can Claude generate Fieldnotes-specific code without the codebase, using only this file? |
| **Layer 2 — Behavior Gates** | Hook + `settings.json` | What does the hook block that CLAUDE.md cannot block? Could you replace your hook with a CLAUDE.md rule? If yes, your hook might not be necessary. |
| **Layer 3 — Specialized Workflows** | Three skills | Does each skill encode a workflow that takes more than 5 steps to prompt correctly? If a skill could be replaced with a single sentence, it is not a skill — it is a habit. |
| **Layer 4 — Team Orchestration** | Audit Q3 (sprint review) | Did you identify the correct orchestration pattern, or did you default to sequential because parallel feels complex? |

The four-layer model is not a checklist to complete. It is a way of asking: *at each layer, what can go wrong, and what is the right enforcement mechanism for this layer?* CLAUDE.md instructs. Hooks enforce. Skills encode. Orchestration scales.

---

## Connect it all back

[Day 1](day-01-how-claude-code-works.md) opened with a single fact: Claude starts every session blank. That is still true on Day 14. The difference is that "blank" no longer means "unconfigured." Blank means: no prior session context. But your CLAUDE.md loads. Your skills are available. Your hooks are armed. Your orchestration patterns are designed.

The blank slate is the feature. It means every session starts clean — no accumulated noise from yesterday's debugging session, no stale assumptions carried forward from last week's refactor. What persists is what you deliberately encoded: the project's accumulated knowledge, its guardrails, its workflows, its conventions.

That is what you have built over 14 days.

---

## Suggested readings for capstone day

**Before you start:**
- Skim your own `CLAUDE.md` from Day 8 (the TaskFlow version). Notice what sections map cleanly to Fieldnotes and where you will need to make new decisions. The delta is the interesting part.

**If you want to check your work:**
- *Software Engineering at Google*, Chapter 24 "Continuous Integration" — re-read the section on "what belongs in CI vs. pre-commit hooks vs. local dev." Use it as a lens on your hook choice: did you pick the right enforcement layer?
- Claude Code Documentation, "CLAUDE.md reference" — verify your CLAUDE.md structure against the official spec. Correct any discrepancies.
- *Accelerate* (Forsgren, Humble & Kim), Chapter 4 "Technical Practices" — the data on what separates high-performing teams from low-performing ones. After building this configuration, which practices did you enable? Which remain manual?

**The question to carry forward:**
You can now configure Claude Code for a project you have never seen before, in under an hour, with enough quality that another developer can be productive on day one. What would you do differently for a project ten times larger — 50 developers, 500K lines, 12 services?

That question is the beginning of the next course.

---

← [Day 13 — CI/CD Integration + Team Collaboration](day-13-cicd-team-collaboration) &nbsp;|&nbsp; [↑ Back to Course Home](README)
