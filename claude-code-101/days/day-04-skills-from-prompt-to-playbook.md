*[Claude Code 101](../../README.md) · Day 4 of 14*

# Day 4 — Skills: From Prompt to AI Playbook

> **Today's one idea:** A skill is a reusable, parameterized workflow package — not a saved prompt. The shift from prompting to skills is the shift from improvisation to playbook execution.
> **Reading time:** ~60 min · **Prereqs:** [Day 3](day-03-writing-claude-md-and-settings.md)
> **Primary source for today:** Claude Code Official Documentation — "Slash commands" / "Skills" section

---

## The hook

It is Monday morning. Your team's pull request queue has four items waiting. You open Claude Code and type:

> "Review this PR. Check for type safety issues, adherence to our service layer pattern, missing test coverage, and any security concerns. Be specific about line numbers and explain why each issue matters."

Claude produces a decent review. You do the same thing on Tuesday. Same request, slightly different wording. Claude focuses more on style this time, misses a service layer violation, mentions some things that aren't actually issues in your codebase.

By Friday you have noticed: the quality of Claude's code reviews is directly proportional to how well you prompted it on that particular day. Some days you get a thorough review against your actual conventions. Some days you get generic feedback.

The problem is not Claude. The problem is that you are starting from scratch every time — re-encoding the review criteria, the project conventions, and the expected output format with every invocation. You are relying on improvisation where you should have a playbook.

A skill solves this. `/review` carries the context, the rubric, the process, and the output format with it. Every invocation is identical in quality — not because Claude got smarter, but because it got consistent context.

This is the shift: you stop prompting and start deploying pre-designed workflows.

---

## Building the intuition

### Conversations vs. procedures

Most Claude Code usage looks like a conversation. You ask something, Claude responds, you react, Claude adjusts. Conversations are great for exploration — when you don't know exactly what you want.

But for recurring tasks — code review, test generation, bug triage, refactoring — you do know what you want. You want the same high-quality output every time, against your project's specific conventions, with a predictable structure you can act on.

Procedures are better than conversations for recurring tasks. A procedure:
- Starts from a known state (the skill's instructions, not the current conversation)
- Follows a defined process (the steps in the skill)
- Produces a predictable output format (the structure specified in the skill)
- Can be invoked identically by anyone on the team (no individual prompting skill required)

This is what a skill is: a procedure encoded as a file.

### The playbook analogy

A sports team does not improvise every play. They have a playbook — a collection of named, practiced plays with defined roles, sequences, and objectives. When the quarterback calls "Slot Right 38 Toss," the entire team knows exactly what to do without real-time negotiation.

Your Claude Code skills are your playbook. When you run `/review`, Claude knows:
- What to look for (from the skill instructions)
- What conventions apply (from the CLAUDE.md reference embedded in the skill)
- What format to use for output (from the skill's output template)
- How to handle edge cases (from the skill's exception handling instructions)

And critically: the *next developer on your team* gets the same quality review when they run `/review` — even if they have never written a Claude prompt in their life.

### Auto-activation: skills that fire themselves

Explicit skill invocation (`/review`) is the basic form. But skills can also be designed to fire automatically when Claude detects you are doing a particular type of task.

The auto-activation trigger is a description in the skill's frontmatter that Claude pattern-matches against the conversation. When you say "add tests for the payment service," Claude recognizes this matches the trigger for your `/test-gen` skill and activates it without you typing `/test-gen`.

This matters because it removes the "remember to use the skill" tax from the workflow. You just describe what you want in natural language; Claude recognizes the task type and brings the right playbook to bear.

---

## The formal picture

### Skill file anatomy

Skills are Markdown files stored in `.claude/commands/` (project, committed) or `~/.claude/commands/` (global, personal). The file name becomes the slash command name: `review.md` → `/review`.

The structure:

```markdown
---
description: [One sentence: what this skill does and when to use it]
---

[Instructions for Claude — this is the skill body]
```

The `description` field serves double duty:
1. It is the tooltip shown when users type `/` and see the command list
2. It is the text Claude uses for auto-activation matching — write it as a trigger phrase, not a title

### A complete `/review` skill for TaskFlow

```markdown
---
description: Review a Python file or PR diff for TaskFlow conventions, type safety, test coverage, and security issues. Auto-activates when asked to review code or a pull request.
---

# TaskFlow Code Review

You are performing a structured code review for the TaskFlow project.
Review the code provided (or read $ARGUMENTS if a file path is given).

## Review Checklist (check each explicitly)

### Architecture Compliance
- [ ] All business logic is in `app/services/`, not in route handlers
- [ ] Services return `Result[T, Error]` — no `raise` statements in service layer
- [ ] No direct ORM queries outside the service layer
- [ ] Route handlers are thin: validate → call service → return response

### Type Safety
- [ ] All function signatures have type hints (params + return type)
- [ ] No use of `Any` type without a comment explaining why
- [ ] Pydantic models used for all request/response shapes

### Test Coverage
- [ ] New service methods have at least one happy-path and one error-path test
- [ ] Test files mirror source structure (e.g., `services/task_service.py` → `tests/services/test_task_service.py`)
- [ ] Tests use pytest fixtures, not setUp/tearDown

### Security
- [ ] No SQL string interpolation (use parameterized queries / ORM)
- [ ] No hardcoded credentials or API keys
- [ ] Input validation on all route parameters and request bodies

### Code Quality
- [ ] No functions longer than 50 lines (flag, don't fail — but note it)
- [ ] No commented-out code
- [ ] Imports follow stdlib → third-party → local ordering

## Output Format

Produce a structured review in this format:

```
## Code Review: [filename or PR title]

### Summary
[2-3 sentence overall assessment: is this ready to merge, or does it need work?]

### Issues Found

#### BLOCKING (must fix before merge)
- **[File:Line]** [Issue description] — [Why it matters / what rule it violates]

#### SUGGESTIONS (improve but not required)
- **[File:Line]** [Suggestion] — [Rationale]

#### STYLE NOTES (informational)
- [Any style observations that don't rise to blocking or suggestion level]

### What's Done Well
[Briefly note 1-3 things the author did right — makes review feedback more actionable]
```

If no issues are found in a category, write "None found." Do not skip categories.
```

### A complete `/test-gen` skill

```markdown
---
description: Generate a pytest test suite for a Python module in TaskFlow. Auto-activates when asked to write, add, or create tests for a file or module.
---

# TaskFlow Test Generation

Generate a comprehensive pytest test suite for the module at $ARGUMENTS
(or the most recently discussed module if no argument given).

## Process

1. **Read the source file** at the given path
2. **Identify all public functions and methods** — these are the test targets
3. **For each function/method, identify:**
   - Happy path: what is the expected output for valid input?
   - Error paths: what inputs should fail, and how should they fail?
   - Edge cases: empty input, None, boundary values, maximum values
4. **Generate tests** following TaskFlow conventions

## Test Generation Rules

- Use `pytest` — no `unittest.TestCase`
- Use fixtures for database setup (see `tests/conftest.py` for existing fixtures)
- Mock external services and HTTP calls — never make real network requests in tests
- Use descriptive test names: `test_[function]_[scenario]_[expected_result]`
  Example: `test_create_task_with_duplicate_title_returns_error`
- Follow the Result pattern: services return `Result[T, Error]`, assert `.is_ok()` or `.is_err()`
- Group tests by function using a test class: `class TestCreateTask:`

## Output Format

```python
# tests/[mirror path of source file]

import pytest
from [module path] import [imports]
# ... additional imports

class Test[FunctionName]:
    """Tests for [function_name] in [module]."""

    def test_[scenario]_[expected](self, [fixtures]):
        # Arrange
        [setup]

        # Act
        result = [function_call]

        # Assert
        assert result.is_ok()
        assert result.value == [expected]

    def test_[error_scenario]_returns_error(self, [fixtures]):
        # Arrange
        [setup with bad input]

        # Act
        result = [function_call]

        # Assert
        assert result.is_err()
        assert "expected error message" in str(result.error)
```

Generate tests for ALL public methods. After generating, list any methods
where you could not determine the correct test behavior and why.
```

### Parameters: `$ARGUMENTS`

`$ARGUMENTS` is a special variable that receives everything typed after the slash command name.

```
/review app/services/task_service.py
         └──────────────────────────── this becomes $ARGUMENTS
```

You can also define named parameters in the skill description and parse them from `$ARGUMENTS` in your instructions:

```markdown
---
description: Analyze a Python function for complexity and suggest refactoring options.
---

Parse $ARGUMENTS as: [file_path] [function_name]

Read the file at [file_path] and find the function named [function_name].
```

### Skill asset bundling

Skills can reference external files — checklists, templates, scripts — that travel with the skill. Store these in `.claude/commands/assets/`:

```
.claude/
├── commands/
│   ├── review.md           ← The /review skill
│   ├── test-gen.md         ← The /test-gen skill
│   └── assets/
│       ├── review-checklist.md    ← Referenced in review.md
│       └── test-template.py      ← Referenced in test-gen.md
```

In the skill, reference them with:
```markdown
Read the checklist at `.claude/commands/assets/review-checklist.md`
and apply each item to the code under review.
```

This decouples the process (the skill) from the content (the checklist), making it easy to update the checklist without rewriting the skill instructions.

### Sub-agents in skills

A skill can spawn sub-agents for parallel work — particularly useful when a task decomposes into independent parts:

```markdown
---
description: Run a full quality audit: tests, review, and security scan in parallel.
---

# Full Audit

Spawn three parallel sub-agents:

**Sub-agent 1 — Test Coverage:**
Run `python -m pytest tests/ --tb=short -q` and report which modules
have less than 80% coverage.

**Sub-agent 2 — Code Review:**
Run /review on all files changed in the last commit (use `git diff HEAD~1 --name-only`).

**Sub-agent 3 — Security Scan:**
Run `bandit -r app/ -f json` and report any HIGH or MEDIUM severity findings.

Wait for all three to complete. Produce a consolidated report with sections
for each sub-agent's findings.
```

Sub-agents are the bridge to [Day 12](day-12-team-orchestration.md)'s orchestration patterns. For now, notice that each sub-agent gets an isolated context — it does not see what the other sub-agents are doing.

---

## Where it breaks / what it is not

**When skills are overkill.** If you ask for something once and will never need it again, writing a skill is over-engineering. Skills earn their overhead when a workflow recurs at least weekly, involves multiple steps or references multiple conventions, or needs to be shareable with teammates.

**Skills that are just long system prompts.** A skill that says "you are an expert reviewer, review this code very carefully" is not a skill — it is a verbose prompt. A real skill defines process (what steps to follow), rubric (what to check against), and output format (what the result should look like).

**The over-automation trap.** If a bad process gets encoded as a skill, Claude will execute that bad process reliably and at scale. Skills amplify whatever workflow you encode. Before automating, make sure the manual version produces good output.

**Auto-activation misfires.** If the skill description is too broad, Claude will activate it for tasks it shouldn't. If your `/review` description says "helps with code," Claude may fire it for non-review code questions. Write trigger descriptions precisely: "Review a Python file or PR diff for TaskFlow conventions."

---

## Try it yourself

**Exercise 1 — Analyze the `/review` skill (comprehension check)**

Read the `/review` skill above. For each section of the review checklist, identify which specific line in the TaskFlow `CLAUDE.md` from Day 3 it is checking against. If there is no corresponding CLAUDE.md rule, is that a gap in the CLAUDE.md or a gap in the review checklist?

---

**Exercise 2 — Write a `/fix` skill**

Write a skill called `/fix` for TaskFlow that takes a bug description (as `$ARGUMENTS`) and follows this process:
1. Ask Claude to reproduce the bug with a failing test
2. Make the code change that fixes it
3. Verify the test now passes
4. Check that no other tests broke

The skill should refuse to proceed if it cannot produce a reproducing test first.

---

**Exercise 3 — Add auto-activation triggers (stretch)**

Revisit the `/review` and `/test-gen` skills. For each, write three natural-language phrases a developer might type that *should* trigger the skill, and two that should *not* trigger it. Then write a skill description that would correctly activate for the "should" cases and not activate for the "should not" cases. This is the art of trigger specification.

---

## Connect it back

[Day 3](day-03-writing-claude-md-and-settings.md) established what Claude is *told* (CLAUDE.md) and what Claude is *allowed to do* (settings.json). Today you gave Claude repeatable workflows it can execute on demand — the playbook that turns ad-hoc prompting into consistent procedure.

But there is still a gap. Your skills tell Claude what process to follow, and your `CLAUDE.md` tells Claude what rules to honor. Both are advisory — they rely on Claude's judgment to follow them correctly. What happens when Claude's judgment should not be trusted?

Tomorrow: hooks — the layer that removes Claude's judgment from the equation entirely for the things that matter most.

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
- Claude Code Documentation, "Slash commands" — the full reference for skill file syntax, including all frontmatter fields and the `$ARGUMENTS` variable. `[VERIFY at docs.anthropic.com/en/docs/claude-code]`

**If you want the deep version:**
- *"Building Effective Agents"*, Anthropic Engineering Blog — the section "Workflow patterns" covers the same patterns skills implement at the API level. Reading this gives you the engineering theory behind why skills are structured as procedures rather than prompts.
- Anthropic Prompt Engineering Guide, "Chain of thought prompting" — the mechanism behind step-by-step skill instructions. Understanding why explicit process enumeration works helps you write better skill instruction sequences.

---

← [Day 3 — Writing CLAUDE.md That Works + settings.json](day-03-writing-claude-md-and-settings) &nbsp;|&nbsp; [Day 5 — Behavior Gates: Hooks as Deterministic Guardrails →](day-05-behavior-gates-hooks)
