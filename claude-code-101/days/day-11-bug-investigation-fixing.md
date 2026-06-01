*[Claude Code 101](../../README.md) · Day 11 of 14*

# Day 11 — Bug Investigation + Fixing

> **Today's one idea:** Scoping is the skill — give Claude the error, the stack trace, the reproducing test, and the relevant files; not the whole repo. The quality of Claude's diagnosis is proportional to the quality of your triage.
> **Reading time:** ~60 min · **Prereqs:** [Day 10](day-10-code-review-refactoring.md)
> **Primary source for today:** Claude Code Official Documentation — "Agentic workflows"; *The Pragmatic Programmer* (Thomas & Hunt), Ch. 9 "Pragmatic Projects"

---

## The hook

It is Tuesday morning. A client reports that tasks are silently dropping their assignee when updated. No error shown to the user. No exception in the logs. Just: task is updated, assignee becomes null.

You open Claude Code and type:

> "There's a bug where tasks lose their assignee when updated. Can you fix it?"

Claude reads the entire service layer. It reads the route handlers. It reads the models. It checks the tests. Forty tool calls and eight minutes later, it produces a hypothesis about the Pydantic schema — which is wrong, because the actual bug is in a single line in `TaskService.update_task` where an optional field is being reset to `None` if not explicitly provided in the update payload.

The problem was not Claude's capability. The problem was the scope you gave it: "tasks lose their assignee" with no stack trace, no reproducing test, no indication of which code path is involved. Claude performed a general search across the entire codebase — and found plausible candidates that were not the actual cause.

Contrast with what happens when you scope correctly:

> "Bug: `PATCH /tasks/{id}` sets `assignee_id` to null even when the request body doesn't include `assignee_id`. Reproducing: `curl -X PATCH /tasks/1 -d '{"title": "Updated"}'` → assignee_id becomes null. Stack trace: none. Relevant file: `app/services/task_service.py`, specifically `update_task`. Please read that file only, identify the root cause, write a failing test, then fix it."

Two tool calls. Correct diagnosis. Passing tests. Done in two minutes.

The difference is not intelligence. It is triage.

---

## Building the intuition

### Bug investigation as funnel narrowing

Think of a bug investigation as narrowing a funnel. At the top: the symptom (what the user observed). At the bottom: the root cause (one line of code, one configuration value, one wrong assumption). The investigation is the process of moving from symptom to root cause by eliminating possibilities.

```
Symptom:     "Tasks lose their assignee when updated"
              ↓ (narrow by: which endpoint?)
HTTP layer:  "PATCH /tasks/{id} — confirmed via curl"
              ↓ (narrow by: which code path?)
Code path:   "task_service.update_task called by PATCH handler"
              ↓ (narrow by: which line?)
Root cause:  Line 47: optional fields default to None in update payload
```

Each narrowing step eliminates a large class of candidates. If you give Claude the symptom alone, it must search the entire funnel. If you give Claude the third level down ("task_service.update_task"), it searches a 50-line function, not a 500-file repository.

**Your job as the investigator:** narrow the funnel as far as you can before handing it to Claude. Claude's job: identify the root cause and fix it. This division of labor is efficient because you have domain context Claude doesn't; Claude has pattern-matching capability you don't.

### The four artifacts of a good bug report

Before running `/fix`, you should have (or actively work toward):

1. **The reproducing scenario** — minimum steps to trigger the bug, every time
2. **The stack trace** (if applicable) — the exact error and call path
3. **The failing test** — a pytest test that fails before the fix and passes after
4. **The relevant files** — the 1–3 files most likely to contain the root cause

You will not always have all four before starting. But the `/fix` skill is designed to help you build them: it will write the reproducing test before touching production code, and it will read only the files named in the stack trace before expanding its search.

---

## The formal picture

### The `/fix` skill in full

The Day 8 stub is expanded here with the complete investigation discipline:

```markdown
---
description: Investigate and fix a bug. Auto-activates when asked to fix a bug, investigate an error, or diagnose unexpected behavior. Requires a reproducing test before making any production code change.
---

# TaskFlow Bug Fix Workflow

## What you have been given (parse from $ARGUMENTS):
- Symptom: [what the user observed]
- Error/stack trace: [if provided]
- Reproducing steps: [if provided]
- Suspected file(s): [if provided]

## Step 1: Scope assessment
State in one sentence: which code path does this symptom implicate?
Read ONLY the files in that path. Do not read unrelated services or models.
If the stack trace names specific files, read those files first.

## Step 2: Hypothesis
State the root cause hypothesis in one sentence before reading any code.
Example: "The update_task method resets optional fields to None when they
are absent from the update payload."

## Step 3: Write a failing test FIRST
Before changing any production code, write a pytest test that:
- Reproduces the exact symptom described
- Passes after the fix (the test defines "fixed")
- Is named: test_[method]_[scenario]_[expected_outcome]

Run the test. Confirm it FAILS. If it does not fail, your hypothesis
or test is wrong — stop and reconsider.

## Step 4: Identify the root cause line
Read the suspected file(s). Find the specific line(s) causing the bug.
Quote the problematic code in your response before proposing the fix.

## Step 5: Make the minimal fix
Change only what is necessary to fix the root cause.
Do NOT refactor unrelated code.
Do NOT add features.
Do NOT "clean up while you're in here."

## Step 6: Verify
Run the failing test from Step 3 — it must now PASS.
Run the full test suite — zero regressions allowed.
If any other test fails: stop, report the regression, do not proceed.

## Step 7: Report
Summary format:
- **Root cause:** [one sentence]
- **Fix:** [what changed, why]
- **Files changed:** [list]
- **Tests added:** [list]
- **Regressions:** None / [list if any]
```

### The TaskFlow bug: a worked example

**The report:**
```
Bug: PATCH /tasks/{id} silently sets assignee_id to null when the request
body does not include assignee_id.

Reproducing:
  curl -X PATCH http://localhost:8000/tasks/1 \
       -H "Authorization: Bearer $TOKEN" \
       -d '{"title": "Renamed task"}'
  → Response 200, but task.assignee_id is now null

Stack trace: none (silent data corruption)
Suspected file: app/services/task_service.py → update_task method
```

**Step 1: Scope**
Claude reads only `app/services/task_service.py` and the `TaskUpdate` Pydantic schema in `app/schemas/task.py`. Two files.

**Step 2: Hypothesis**
"The `update_task` method applies all fields from the `TaskUpdate` schema to the `Task` model, including `assignee_id` which defaults to `None` when not provided in the request."

**Step 3: The failing test**
```python
# tests/services/test_task_service.py

async def test_update_task_preserves_assignee_when_not_in_payload(
    service, db: AsyncSession
):
    """
    PATCH /tasks/{id} with only title should not clear assignee_id.
    Regression test for silent assignee data corruption bug.
    """
    # Arrange: create task with an assignee
    project = await create_project(db)
    assignee = await create_user(db, project_id=project.id)
    task = await create_task(db, project_id=project.id, assignee_id=assignee.id)

    # Act: update only the title (no assignee_id in payload)
    from app.schemas.task import TaskUpdate
    update = TaskUpdate(title="Renamed task")  # assignee_id not provided
    result = await service.update_task(task_id=task.id, update=update, db=db)

    # Assert: assignee_id must be preserved
    assert result.is_ok()
    assert result.value.assignee_id == assignee.id  # THIS FAILS before fix
```

Run: `pytest tests/services/test_task_service.py::test_update_task_preserves_assignee_when_not_in_payload -v`
Result: **FAIL** — `AssertionError: assert None == 3`

**Step 4: Root cause**
```python
# app/services/task_service.py — line 47 (the bug)

async def update_task(
    self, task_id: int, update: TaskUpdate, db: AsyncSession
) -> Result[Task, str]:
    task = await db.get(Task, task_id)
    if not task:
        return Result.err(TaskError.NOT_FOUND)

    # BUG: update.dict() includes all fields with their defaults,
    # including assignee_id=None when not provided in the request
    for field, value in update.dict().items():   # ← line 47
        setattr(task, field, value)

    await db.commit()
    return Result.ok(task)
```

**Step 5: Minimal fix**
```python
# Two approaches — use whichever matches your Pydantic version:

# Pydantic v2 (correct for TaskFlow):
for field, value in update.model_dump(exclude_unset=True).items():
    setattr(task, field, value)

# Pydantic v1 (if applicable):
for field, value in update.dict(exclude_unset=True).items():
    setattr(task, field, value)
```

`exclude_unset=True` is the key: it only includes fields that were explicitly set in the request payload, not fields that were left at their default values.

**Step 6: Verify**
```
pytest tests/services/test_task_service.py::test_update_task_preserves_... PASSED
pytest tests/ -q → 47 passed, 0 failed
```

**Step 7: Report**
- **Root cause:** `update.dict()` includes all schema fields including unset ones; `assignee_id` defaults to `None` and overwrites existing values.
- **Fix:** `update.dict()` → `update.model_dump(exclude_unset=True)` in `update_task`, line 47.
- **Files changed:** `app/services/task_service.py`
- **Tests added:** `test_update_task_preserves_assignee_when_not_in_payload`
- **Regressions:** None

### Scoping patterns for common bug types

| Bug type | Scope heuristic | Files to read first |
|----------|----------------|---------------------|
| Silent data corruption | Which service method mutates this data? | Service file + schema |
| HTTP 500 error | Stack trace → deepest frame in your code | File named in trace |
| Wrong response shape | Which schema defines this response? | Schema + route handler |
| Test flakiness | Which external dependency is called? | Test file + mock setup |
| Performance regression | Which query changed recently? | `git log -p --follow` on service file |
| Auth/permission bypass | Which permission check is on this route? | Route handler + auth middleware |

### When the bug is not where you think

Sometimes the hypothesis is wrong. The failing test writes correctly, but the bug is in a different file than expected. The right response:

1. Do not expand the search randomly — you will waste context
2. Re-read the stack trace (or add `logging.debug` to get one)
3. Use `git log -p app/services/task_service.py` to see recent changes — bugs often live in recent commits
4. Compact the session (the dead-end investigation is noise now) and restart with the corrected scope

---

## Where it breaks / what it is not

**"No stack trace available."** Some bugs (silent data corruption, wrong return values) produce no error. Your tool is the reproducing test — write a test that verifies the expected behavior, confirm it fails, then narrow by reading the call path that test exercises.

**Bugs that span multiple systems.** A bug that involves the database, the cache layer, and the API all interacting is not suitable for a single Claude session. Diagnose one layer at a time in separate, focused sessions. Use `git bisect` to narrow which commit introduced the bug before involving Claude.

**"Claude found a different bug while investigating."** This happens — Claude reads a file looking for one thing and notices an unrelated issue. Do not fix both in the same session. Record the secondary finding (a comment, a TODO, a GitHub issue), fix the original bug, close the session, start a new one for the secondary issue. Mixing fixes creates untestable commits.

**The reproducing test requirement is not optional.** The `/fix` skill stops if it cannot write a reproducing test. This is intentional. A fix without a test is a hypothesis; a fix with a passing test is a verified fact. If you push Claude to skip this step ("just fix it, I'll add the test later"), you will eventually merge a "fix" that does not actually fix anything — it just changed code near the bug.

---

## Try it yourself

**Exercise 1 — Scope a bug report (comprehension check)**

Here is an unscoped bug report: "Sometimes when I create a task, the project's task count doesn't update correctly."

Apply the funnel-narrowing technique:
1. What do you need to find out to narrow "sometimes" to "always, given X"?
2. Which files does the symptom implicate? (Use the TaskFlow structure from Day 8.)
3. Write the `/fix` invocation — what exact text would you type, given what you know?

---

**Exercise 2 — Write a failing test before the fix**

Given this bug: `TaskService.create_task` does not check whether `project_id` refers to an active (non-archived) project. Tasks can be created in archived projects.

Write the failing test (Step 3 of the `/fix` process) before writing any fix:
1. What fixture setup is needed?
2. What is the exact assertion that must fail before the fix?
3. What does the test name communicate about the scenario?

Then write the minimal fix.

---

**Exercise 3 — Post-mortem template (stretch)**

A good bug fix includes a post-mortem: why did this bug exist, why did it survive undetected, and what prevents recurrence?

Write a three-section post-mortem for the `assignee_id` bug above:
1. **Root cause:** what technical decision led to this being possible?
2. **Detection gap:** what should have caught this earlier (test, hook, convention)?
3. **Prevention:** what CLAUDE.md rule, skill instruction, or hook would prevent this class of bug in the future?

This is the feedback loop that makes your Claude Code configuration stronger over time.

---

## Connect it back

[Day 10](day-10-code-review-refactoring.md) found structural violations proactively. Today you fixed behavioral bugs reactively. The two workflows are complementary: review prevents future bugs; `/fix` resolves current ones. Both depend on the test suite from [Day 9](day-09-regression-test-pack.md) as the oracle.

Tomorrow you zoom out from individual tasks to coordinated multi-agent work — the orchestration patterns that let you run a full quality audit, build a new feature, and investigate a bug in parallel rather than serially.

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
- *The Pragmatic Programmer*, Ch. 9 "Pragmatic Projects" — the section "Ruthless and Continuous Testing." The authors' argument for regression tests as the primary artifact of a bug fix maps directly onto the `/fix` workflow's "write a failing test first" requirement.

**If you want the deep version:**
- *Software Engineering at Google*, Chapter 13 "Test Doubles" — the section on "Fakes vs. mocks" is directly applicable when writing reproducing tests that involve database state and external services. The TaskFlow `assignee_id` bug required a real DB fixture (not a mock) to reproduce — this chapter explains when that distinction matters.

---

← [Day 10 — Code Review + Refactoring](day-10-code-review-refactoring) &nbsp;|&nbsp; [Day 12 — Team Orchestration →](day-12-team-orchestration)
