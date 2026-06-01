*[Claude Code 101](../../README.md) · Day 10 of 14*

# Day 10 — Code Review + Refactoring

> **Today's one idea:** `/review` checks code against your CLAUDE.md conventions — not generic style — which means its findings are immediately actionable; safe refactoring means tests first, then the smallest possible change, then verification.
> **Reading time:** ~60 min · **Prereqs:** [Day 9](day-09-regression-test-pack.md)
> **Primary source for today:** *Refactoring* (Fowler, 2nd Ed.), Ch. 1; *Software Engineering at Google* (Winters et al.), Ch. 9 "Code Review"

---

## The hook

There is a route handler in TaskFlow's codebase that has been there for eight months. Nobody remembers who wrote it. It works — the feature is live, clients use it. But it violates three architectural rules that the team established after the Q3 2024 incidents.

```python
# app/api/v1/tasks.py  (the problematic handler)

@router.put("/{task_id}/status")
async def update_task_status(
    task_id: int,
    new_status: str,
    db: AsyncSession = Depends(get_db),
    current_user: User = Depends(get_current_user)
):
    # Direct ORM query in route handler — violates service layer rule
    task = await db.get(Task, task_id)
    if not task:
        raise HTTPException(status_code=404, detail="Task not found")

    # Bypasses TaskService.update_status — violates the architectural rule
    # that caused the Q3 race condition
    task.status = new_status
    await db.commit()

    return {"id": task.id, "status": task.status}
```

You have three options:

**Option A:** Leave it. It works. Nobody complains.

**Option B:** Run `/review` and get a structured report that identifies the violations, rates their severity, and tells you exactly which CLAUDE.md rules they break — then run `/fix` to apply the minimal corrective change.

**Option C:** Ask Claude cold: "refactor the task status endpoint." Claude produces a large rewrite that changes the handler, the test, and several files you didn't intend to touch — and now you have a merge conflict to resolve.

Option B is the workflow today. The key insight: `/review` produces actionable, CLAUDE.md-anchored findings because it has the conventions in context. And safe refactoring means making the smallest possible change to move from wrong to right, with the existing tests verifying that nothing else broke.

---

## Building the intuition

### What "actionable" means in a code review

A code review comment is actionable when:
1. It identifies a specific location (file and line)
2. It states what rule is violated (not just "this is bad")
3. It explains what the correct pattern is
4. The reviewer (or Claude) can implement the fix without asking follow-up questions

Generic review feedback fails these tests:
- "This is not following best practices" → which best practice? whose?
- "This could be more readable" → more readable to whom, and how?
- "Consider refactoring this" → into what?

The `/review` skill produces structured output because it checks against *named rules from CLAUDE.md*, not generic heuristics. When Claude flags "direct ORM query in route handler," it can immediately cite the CLAUDE.md rule: *"No ORM queries in route handlers — all business logic lives in app/services/."* And because the rule is in CLAUDE.md, the fix is clear: move the logic to the service layer.

### Safe refactoring: the discipline that makes Claude Code trustworthy

Refactoring has a precise meaning in Fowler's sense: changing the *structure* of code without changing its *observable behavior*. The tests are the oracle for "same behavior."

The pattern for safe refactoring with Claude:

```
1. Ensure tests exist (if not → /test-gen first)
2. Run baseline: all tests pass
3. State the refactoring goal precisely (one thing at a time)
4. Claude makes the minimal change
5. Run tests: all must still pass
6. If tests fail: revert, diagnose, try again with smaller change
```

The failure mode — "change-and-pray" — happens when you skip step 1 or step 5, or when the change in step 4 is too large to understand when tests break. Claude Code is particularly prone to the "large change" failure because it can edit many files quickly. The discipline is yours to enforce; the `/fix` skill can help by explicitly requiring a failing test before touching production code.

### The code smell catalog for TaskFlow

Before running `/review`, it helps to know what categories of issues exist. For TaskFlow, the architectural smells that matter are:

| Smell | Where to look | CLAUDE.md rule |
|-------|--------------|----------------|
| Logic in route handler | `app/api/v1/*.py` | Service layer pattern |
| `raise` in service layer | `app/services/*.py` | Result type over exceptions |
| Synchronous DB call | Any `*.py` | Async SQLAlchemy |
| ORM query outside service | `app/api/`, tests | Service layer pattern |
| Missing type hints | Any `*.py` | Conventions: Python |
| Status change outside `TaskService` | Any `*.py` | Architecture decisions |
| `openapi.yaml` not updated | `docs/`, `app/api/` | API contract first |

---

## The formal picture

### Running `/review` on the problematic handler

Invoke: `/review app/api/v1/tasks.py`

With the TaskFlow scaffold loaded, Claude produces:

```
## Code Review: app/api/v1/tasks.py

### Summary
The update_task_status endpoint contains two architectural violations that
directly contradict CLAUDE.md rules established after the Q3 2024 race condition
incident. These are blocking issues for the upcoming v2.1 service layer refactor.
The file is otherwise well-structured.

### BLOCKING (must fix before merge)

- **tasks.py:14** Direct ORM query (`db.get(Task, task_id)`) in route handler.
  Rule violated: "No ORM queries in route handlers — all business logic lives
  in app/services/." This bypasses `TaskService` entirely.

- **tasks.py:19** Direct status assignment (`task.status = new_status`) bypasses
  `TaskService.update_status`. Rule violated: "Never change task status outside
  TaskService.update_status." This is the exact pattern that caused the Q3 2024
  race condition.

- **tasks.py:19** `new_status` is typed as `str` but `TaskStatus` is an enum.
  No validation that `new_status` is a valid `TaskStatus` value.
  Rule violated: "Pydantic v2 schemas in app/schemas/ for all request/response bodies."

### SUGGESTIONS (non-blocking improvements)

- **tasks.py:24** Response returns raw dict instead of a Pydantic schema.
  Consistent with other handlers would use a response_model. Not blocking,
  but inconsistent with the rest of the API layer.

### STYLE NOTES
- Import order is correct.
- Function name is clear and follows conventions.

### What's Done Well
- Dependency injection is used correctly for db and current_user.
- Endpoint path follows RESTful conventions.
- HTTPException usage is appropriate (though this should move to the service).
```

### Applying the fix with `/fix`

After the review, you run: `/fix The update_task_status handler has three blocking issues from /review. Fix them using the service layer pattern.`

The `/fix` skill requires a failing test before changing production code. Since the violations are architectural (the code works, just wrongly structured), you need a test that captures the *behavioral* requirement:

```python
# /fix writes this test first:
async def test_update_status_endpoint_uses_service_layer(
    client: TestClient, db: AsyncSession
):
    """Verify the endpoint delegates to TaskService, not direct ORM."""
    task = await create_task(db, status=TaskStatus.OPEN)

    with patch('app.api.v1.tasks.TaskService.update_status') as mock_service:
        mock_service.return_value = Result.ok(task)
        response = client.put(f"/tasks/{task.id}/status",
                             json={"status": "in_progress"})

    # If the service was called, the architectural rule is satisfied
    mock_service.assert_called_once()
    assert response.status_code == 200
```

Then `/fix` makes the minimal corrective change:

```python
# app/api/v1/tasks.py — corrected

from app.services.task_service import TaskService
from app.schemas.task import TaskStatusUpdate, TaskResponse
from app.models.task import TaskStatus

@router.put("/{task_id}/status", response_model=TaskResponse)
async def update_task_status(
    task_id: int,
    body: TaskStatusUpdate,  # Pydantic schema with TaskStatus validation
    db: AsyncSession = Depends(get_db),
    current_user: User = Depends(get_current_user)
):
    service = TaskService()
    result = await service.update_status(
        task_id=task_id,
        new_status=body.status,
        updated_by_id=current_user.id,
        db=db
    )
    if result.is_err():
        raise HTTPException(
            status_code=404 if result.error == TaskError.NOT_FOUND else 400,
            detail={"detail": result.error, "code": result.error}
        )
    return result.value
```

The test passes. The full test suite runs. The architectural violation is resolved.

### The Fowler refactoring catalog, applied to TaskFlow

Fowler names 60+ refactorings. For TaskFlow's common issues, these are the most applicable:

| Fowler refactoring | When to apply to TaskFlow |
|-------------------|--------------------------|
| **Extract Function** | Route handler logic that belongs in service layer |
| **Move Function** | Method that operates on data it doesn't own |
| **Replace Exception with Precheck** | Converting `try/raise` to Result pattern |
| **Introduce Parameter Object** | Multiple parameters that travel together → Pydantic schema |
| **Replace Conditional with Polymorphism** | Long `if/elif` status transition chains |
| **Extract Class** | Service method with too many responsibilities |

When using Claude for refactoring, name the Fowler pattern explicitly in your request. "Extract the database logic into a separate function" is less clear than "Apply Extract Function to move the ORM query from `update_task_status` into `TaskService.get_task_by_id`." Named patterns give Claude a precise target.

### The complete review → refactor → verify workflow

```
Step 1: Ensure test coverage
  /test-gen app/api/v1/tasks.py
  → Run: python -m pytest tests/api/test_tasks.py -v
  → All tests pass (baseline)

Step 2: Review
  /review app/api/v1/tasks.py
  → Read the structured report
  → Triage: BLOCKING vs. SUGGESTIONS vs. STYLE NOTES

Step 3: Fix blocking issues (one at a time)
  /fix [blocking issue description]
  → /fix checks for reproducing test (writes one if needed)
  → /fix makes minimal change
  → /fix runs tests: must pass

Step 4: Verify no regressions
  python -m pytest tests/ -v --tb=short
  → All tests pass

Step 5: Update CLAUDE.md if the review found a gap
  → If the violation was not covered by CLAUDE.md, add it
  → If the violation was in CLAUDE.md but Claude missed it, sharpen the rule
```

---

## Where it breaks / what it is not

**`/review` is only as good as your `CLAUDE.md`.** If your `CLAUDE.md` doesn't mention the service layer pattern, `/review` won't flag violations of it. The quality of review output is a direct measure of `CLAUDE.md` quality. Use each review cycle as a feedback loop: if Claude missed something that a human reviewer would catch, add it to `CLAUDE.md`.

**Refactoring and bug fixing in the same commit.** The discipline is: refactor first (no behavior change), then fix bugs (behavior change) in separate commits. If you mix them, you cannot distinguish "tests failing because of the refactoring" from "tests failing because of the fix." Claude will mix them if you don't enforce the separation explicitly.

**The "working correctly" fallacy.** "It works in production" is not evidence that a violation is harmless. The Q3 race condition worked in production — until load increased and two requests hit the endpoint simultaneously. Architectural violations are latent failures, not current ones.

**Over-automating review.** `/review` is a first pass, not a final decision. It will produce false positives (flagging things that are correct in context) and false negatives (missing issues that require domain knowledge). Use it to catch the systematic, rule-based violations; keep human judgment for architectural tradeoffs.

---

## Try it yourself

**Exercise 1 — Trace the review output (comprehension check)**

For each BLOCKING issue in the `/review` output above:
1. Which specific line in the TaskFlow `CLAUDE.md` (from Day 8) does it reference?
2. Is this violation also guarded by a hook, or only by CLAUDE.md?
3. If you had removed the relevant CLAUDE.md rule, would Claude still have flagged this?

---

**Exercise 2 — Refactor using named patterns**

Here is a service method that needs refactoring:

```python
async def process_task_action(
    self, task_id: int, action: str, user_id: int,
    assignee_id: Optional[int], due_date: Optional[str],
    priority: Optional[str], db: AsyncSession
) -> Result[Task, str]:
    task = await db.get(Task, task_id)
    if not task:
        return Result.err(TaskError.NOT_FOUND)
    if action == "assign":
        if not assignee_id:
            return Result.err("MISSING_ASSIGNEE")
        task.assignee_id = assignee_id
    elif action == "set_due_date":
        task.due_date = datetime.fromisoformat(due_date)
    elif action == "set_priority":
        task.priority = priority
    elif action == "complete":
        task.status = TaskStatus.DONE
        task.completed_at = datetime.utcnow()
    await db.commit()
    return Result.ok(task)
```

Identify: which Fowler refactorings apply here? Write the refactoring plan as a sequence of named steps (e.g., "Step 1: Extract Function for the assign action"). Then apply them in order using Claude or manually.

---

**Exercise 3 — Review your own code (stretch)**

Take a real file from a project you work on. Run `/review` (or simulate it using the review checklist). For each finding:
1. Is it covered by your project's CLAUDE.md?
2. If not, should it be? Write the CLAUDE.md rule.
3. Is it a refactoring (structure change, behavior preserved) or a bug fix (behavior change)?

---

## Connect it back

[Day 9](day-09-regression-test-pack.md) generated the safety net. Today you used it — the tests made the refactoring verifiable. The sequence matters: tests first, review second, fix third.

Tomorrow the workflow is bug investigation — a fundamentally different mode from review. Review starts with code and finds problems. Bug investigation starts with a problem (error, symptom, failing test) and works backward to find code. The discipline of scoping — giving Claude exactly what it needs and nothing more — becomes critical.

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
- *Refactoring* (Fowler), Chapter 1 — the worked example of the entire refactoring process: understand the code, write tests, apply named refactorings in sequence, verify at each step. This is the canonical walkthrough of the same discipline this page teaches.

**If you want the deep version:**
- *Software Engineering at Google*, Chapter 9 "Code Review" — particularly "What to look for in a code review" and "The author's perspective." The Google practices map directly onto the `/review` skill design: structured checklists, separation of BLOCKING from SUGGESTIONS, keeping review comments specific and actionable.
- *Refactoring* (Fowler), Chapter 3 "Bad Smells in Code" — Fowler's catalog of the 22 code smells that suggest refactoring is needed. Cross-reference against your project's CLAUDE.md conventions — every convention violation is a named smell.

---

← [Day 9 — Regression Test Pack Creation](day-09-regression-test-pack) &nbsp;|&nbsp; [Day 11 — Bug Investigation + Fixing →](day-11-bug-investigation-fixing)
