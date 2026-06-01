*[Claude Code 101](../../README.md) · Day 9 of 14*

# Day 9 — Regression Test Pack Creation

> **Today's one idea:** The `/test-gen` skill makes Claude infer boundary conditions from code structure — not just echo template tests — because it has the project conventions and the source code in context simultaneously.
> **Reading time:** ~60 min · **Prereqs:** [Day 8](day-08-taskflow-scaffold.md)
> **Primary source for today:** *Software Engineering at Google* (Winters et al.), Ch. 11 "Testing Overview"

---

## The hook

TaskFlow has 40% test coverage. The other 60% is not untested code — it is *unknown* code. You do not know if it works correctly, only that it has not been explicitly verified.

Your team is about to start a major refactoring of the service layer for v2.1. The rule of safe refactoring is: you must be able to verify that the refactored code behaves identically to the original. Without test coverage, you cannot verify this. The 60% gap is a liability that compounds with every change.

The traditional fix: assign a developer to write tests for two weeks. They write tests that mirror the code they are reading — tests that verify implementation rather than behavior, tests that will break when the refactoring they are supposed to enable arrives. They also have no idea which code paths are most critical, so they cover the happy paths first and leave the edge cases for later.

The Claude Code approach: run `/test-gen app/services/task_service.py`. Claude reads the source, infers the boundary conditions from the method signatures, the docstrings, the error returns, and the data models, and generates a test suite that covers the cases a reviewer would flag in a code review: invalid input, missing data, race conditions, permission violations.

Today you learn how the `/test-gen` skill works under the hood, what makes generated tests good vs. useless, and how to run a systematic test pack generation campaign against TaskFlow's entire service layer.

---

## Building the intuition

### What makes a good test

A test is a hypothesis: "given this input, the system produces this output." A good test hypothesis is:
1. **Falsifiable** — it could plausibly fail if the code were wrong
2. **Independent** — it does not depend on another test's side effects
3. **Specific** — it tests one behavior, not a workflow
4. **Named** — the name describes the scenario and expected outcome without reading the body

A bad test is one that:
- Only verifies that the code ran without crashing ("it returned something")
- Mirrors the implementation so closely that it would pass even if the implementation were changed to be wrong in a subtle way
- Tests multiple behaviors in one method (hard to diagnose when it fails)

When you ask Claude cold — "write tests for task_service.py" — you get tests that mirror the implementation. Claude reads the code and writes assertions that match what the code currently does.

When you use `/test-gen` with the TaskFlow scaffold, something different happens: Claude has the `CLAUDE.md` conventions in context (Result pattern, async SQLAlchemy, fixture requirements), and the skill instructions enumerate the categories of tests to generate. Claude is filling a structured template against the code, not just reflecting the code back.

### The boundary condition technique

For any function, the most valuable tests are at the boundaries — the inputs that are one step inside and one step outside the valid range. For `create_task`:

```python
async def create_task(
    self,
    title: str,
    project_id: int,
    assignee_id: Optional[int],
    db: AsyncSession
) -> Result[Task, TaskError]:
```

The boundaries are:
- `title`: empty string, one character, max length (if there is one), None (but type hints say str)
- `project_id`: valid ID, ID that does not exist in DB, zero, negative
- `assignee_id`: valid user ID, user ID not in DB, None (it is Optional)
- `db`: valid session, session that raises on commit (infrastructure failure)
- Combined: project exists but assignee does not, title is duplicate within project

A cold Claude will write: "assert `create_task('My Task', 1, None, db).is_ok()`."
A skilled Claude (with the right context) will write all the boundary tests above.

The `/test-gen` skill encodes the boundary technique as an explicit instruction, so Claude applies it systematically to every method.

---

## The formal picture

### The enhanced `/test-gen` skill

Here is the full production-grade version of the skill (expanding on the stub from Day 8):

```markdown
---
description: Generate a comprehensive pytest test suite for a Python module in TaskFlow. Auto-activates when asked to write, add, or create tests for a file or function.
---

# TaskFlow Test Generation

Read the source file at $ARGUMENTS.

## Step 1: Inventory
List every public function and method with its signature. For each, identify:
- Return type (is it a Result type?)
- Required parameters and their types
- Optional parameters and their defaults
- Any docstring describing expected behavior

## Step 2: Boundary analysis
For each parameter of each method, identify:
- **Happy path:** valid input producing successful output
- **Type boundaries:** None when not Optional, wrong type
- **Value boundaries:** empty string, zero, negative int, max-length string
- **Existence boundaries:** ID that does not exist in DB, duplicate unique values
- **State boundaries:** operation on deleted/archived entity, concurrent modification
- **Permission boundaries:** unauthorized user, wrong project context

## Step 3: Check existing fixtures
Read `tests/conftest.py` to understand available fixtures before creating new ones.
List the fixtures you will use. Only create new fixtures if absolutely necessary.

## Step 4: Generate test file
Follow TaskFlow conventions:
- `async def test_*` with `pytest.mark.asyncio` or use `anyio` backend
- Use `AsyncSession` fixtures from `conftest.py`
- Group by method: `class TestCreateTask:`, `class TestUpdateStatus:`, etc.
- Name tests: `test_[method]_[scenario]_[expected_outcome]`
- Assert Result: `.is_ok()`, `.is_err()`, `.value`, `.error`
- Mock external I/O: email, Redis, third-party APIs
- Never assert on implementation details — only on observable outputs

## Step 5: Coverage check
After generating, list:
- Methods with complete boundary coverage (happy + error + edge)
- Methods where you needed to make assumptions — list them explicitly
- Any behavior you could not infer from the source (requires domain knowledge)

## Output format:
```python
# tests/services/test_[module_name].py
# Generated by /test-gen — review all assumptions before committing

import pytest
import pytest_asyncio
from unittest.mock import AsyncMock, patch
from sqlalchemy.ext.asyncio import AsyncSession

from app.services.[module] import [ServiceClass]
from app.core.result import Result
from app.models.[model] import [Model]

# ── Fixtures ──────────────────────────────────────────────────────────────────

@pytest_asyncio.fixture
async def service(db: AsyncSession):
    return [ServiceClass](db)

# ── Tests ─────────────────────────────────────────────────────────────────────

class Test[MethodName]:
    """Tests for [method_name]."""

    async def test_[happy_path_scenario](self, service, db):
        # Arrange
        ...
        # Act
        result = await service.[method](...)
        # Assert
        assert result.is_ok()
        assert result.value.[field] == expected

    async def test_[error_scenario]_returns_error(self, service, db):
        # Arrange — set up invalid state
        ...
        # Act
        result = await service.[method](...)
        # Assert
        assert result.is_err()
        assert result.error == [ExpectedError]
```
```

### The TaskFlow `task_service.py` module (abbreviated)

Here is the relevant source to understand what `/test-gen` would generate against:

```python
# app/services/task_service.py

from typing import Optional
from sqlalchemy.ext.asyncio import AsyncSession
from sqlalchemy import select
from app.core.result import Result
from app.models.task import Task, TaskStatus
from app.models.user import User
from app.models.project import Project

class TaskError:
    NOT_FOUND = "TASK_NOT_FOUND"
    DUPLICATE_TITLE = "DUPLICATE_TITLE_IN_PROJECT"
    INVALID_STATUS_TRANSITION = "INVALID_STATUS_TRANSITION"
    ASSIGNEE_NOT_IN_PROJECT = "ASSIGNEE_NOT_IN_PROJECT"
    UNAUTHORIZED = "UNAUTHORIZED"

class TaskService:

    async def create_task(
        self,
        title: str,
        project_id: int,
        assignee_id: Optional[int],
        created_by_id: int,
        db: AsyncSession
    ) -> Result[Task, str]:
        # Validate project exists
        project = await db.get(Project, project_id)
        if not project:
            return Result.err(TaskError.NOT_FOUND)

        # Check for duplicate title within project
        existing = await db.execute(
            select(Task).where(Task.project_id == project_id, Task.title == title)
        )
        if existing.scalar_one_or_none():
            return Result.err(TaskError.DUPLICATE_TITLE)

        # Validate assignee is in project if provided
        if assignee_id:
            # ... membership check
            pass

        task = Task(
            title=title,
            project_id=project_id,
            assignee_id=assignee_id,
            created_by_id=created_by_id,
            status=TaskStatus.OPEN
        )
        db.add(task)
        await db.commit()
        await db.refresh(task)
        return Result.ok(task)

    async def update_status(
        self,
        task_id: int,
        new_status: TaskStatus,
        updated_by_id: int,
        db: AsyncSession
    ) -> Result[Task, str]:
        # Valid transitions enforced here
        VALID_TRANSITIONS = {
            TaskStatus.OPEN: [TaskStatus.IN_PROGRESS, TaskStatus.CANCELLED],
            TaskStatus.IN_PROGRESS: [TaskStatus.DONE, TaskStatus.OPEN],
            TaskStatus.DONE: [],  # terminal state
            TaskStatus.CANCELLED: [],  # terminal state
        }
        # ...
```

### The generated test suite (excerpt)

Running `/test-gen app/services/task_service.py` with the TaskFlow scaffold produces:

```python
# tests/services/test_task_service.py
# Generated by /test-gen — review all assumptions before committing

import pytest
import pytest_asyncio
from sqlalchemy.ext.asyncio import AsyncSession

from app.services.task_service import TaskService, TaskError
from app.models.task import Task, TaskStatus
from tests.conftest import create_project, create_user  # assumed fixtures

@pytest_asyncio.fixture
async def service():
    return TaskService()

class TestCreateTask:
    """Tests for TaskService.create_task."""

    async def test_creates_task_with_valid_inputs(
        self, service, db: AsyncSession
    ):
        project = await create_project(db)
        user = await create_user(db, project_id=project.id)

        result = await service.create_task(
            title="Write unit tests",
            project_id=project.id,
            assignee_id=None,
            created_by_id=user.id,
            db=db
        )

        assert result.is_ok()
        assert result.value.title == "Write unit tests"
        assert result.value.status == TaskStatus.OPEN

    async def test_returns_error_when_project_not_found(
        self, service, db: AsyncSession
    ):
        user = await create_user(db)
        result = await service.create_task(
            title="Any Task",
            project_id=99999,  # non-existent
            assignee_id=None,
            created_by_id=user.id,
            db=db
        )

        assert result.is_err()
        assert result.error == TaskError.NOT_FOUND

    async def test_returns_error_for_duplicate_title_in_same_project(
        self, service, db: AsyncSession
    ):
        project = await create_project(db)
        user = await create_user(db, project_id=project.id)
        # Create first task
        await service.create_task("Duplicate", project.id, None, user.id, db)

        # Try to create with same title
        result = await service.create_task("Duplicate", project.id, None, user.id, db)

        assert result.is_err()
        assert result.error == TaskError.DUPLICATE_TITLE

    async def test_allows_duplicate_title_in_different_projects(
        self, service, db: AsyncSession
    ):
        project_a = await create_project(db)
        project_b = await create_project(db)
        user = await create_user(db)
        await service.create_task("Same Title", project_a.id, None, user.id, db)

        result = await service.create_task("Same Title", project_b.id, None, user.id, db)

        assert result.is_ok()  # Different project — title is not duplicate

    async def test_returns_error_when_assignee_not_in_project(
        self, service, db: AsyncSession
    ):
        project = await create_project(db)
        creator = await create_user(db, project_id=project.id)
        outsider = await create_user(db)  # not in project

        result = await service.create_task(
            title="New Task", project_id=project.id,
            assignee_id=outsider.id, created_by_id=creator.id, db=db
        )

        assert result.is_err()
        assert result.error == TaskError.ASSIGNEE_NOT_IN_PROJECT


class TestUpdateStatus:
    """Tests for TaskService.update_status."""

    async def test_transitions_open_to_in_progress(
        self, service, db: AsyncSession
    ):
        task = await create_task(db, status=TaskStatus.OPEN)
        user = await create_user(db)

        result = await service.update_status(task.id, TaskStatus.IN_PROGRESS, user.id, db)

        assert result.is_ok()
        assert result.value.status == TaskStatus.IN_PROGRESS

    async def test_rejects_invalid_transition_done_to_open(
        self, service, db: AsyncSession
    ):
        task = await create_task(db, status=TaskStatus.DONE)
        user = await create_user(db)

        result = await service.update_status(task.id, TaskStatus.OPEN, user.id, db)

        assert result.is_err()
        assert result.error == TaskError.INVALID_STATUS_TRANSITION

    async def test_rejects_all_transitions_from_cancelled(
        self, service, db: AsyncSession
    ):
        task = await create_task(db, status=TaskStatus.CANCELLED)
        user = await create_user(db)

        for target_status in [TaskStatus.OPEN, TaskStatus.IN_PROGRESS, TaskStatus.DONE]:
            result = await service.update_status(task.id, target_status, user.id, db)
            assert result.is_err(), f"Expected error transitioning CANCELLED → {target_status}"
```

### Running a test pack generation campaign

For a systematic coverage campaign across TaskFlow's entire service layer:

```bash
# Run /test-gen on each service module sequentially
/test-gen app/services/task_service.py
/test-gen app/services/user_service.py
/test-gen app/services/project_service.py
```

Or use the sub-agent pattern for parallel generation (Day 7 applied):

```markdown
Spawn three parallel sub-agents, one per service file:

Agent 1: /test-gen app/services/task_service.py
Agent 2: /test-gen app/services/user_service.py
Agent 3: /test-gen app/services/project_service.py

Each agent should return the complete test file content.
After all three complete, report any assumptions each agent flagged.
```

---

## Where it breaks / what it is not

**Generated tests need human review.** Claude generates tests based on inferred behavior from source code. If the source has a bug, Claude will write tests that assert the buggy behavior. Always review generated tests with the question: "Is this what the code *should* do, or just what it *does* do?"

**"Assumption" flags are important.** The `/test-gen` skill explicitly lists assumptions it made. Read these carefully — they are the tests most likely to be wrong, because they required domain knowledge Claude does not have.

**Generated tests against private implementation details.** Watch for tests that assert on internal state (e.g., the exact SQL query executed) rather than observable outputs. These tests will break during refactoring — which defeats the purpose. Replace them with tests that assert on the service's return value only.

**100% coverage is not the goal.** Coverage measures which lines were executed, not whether the right assertions were made. A test that calls every method with `assert True` gives 100% coverage and zero confidence. Use coverage as a floor (find what's not covered at all), not as a target.

---

## Try it yourself

**Exercise 1 — Analyze the generated test suite (comprehension check)**

Read the generated tests above for `TestCreateTask`. For each test:
1. What boundary condition does it cover?
2. Is it asserting on observable output (Result value/error) or implementation details?
3. Is there any case from the boundary analysis in "Building the intuition" that is NOT covered by the generated suite?

---

**Exercise 2 — Extend the test suite for `update_status`**

The `TestUpdateStatus` class above covers three scenarios. Write three more tests:
1. `test_update_status_returns_error_for_nonexistent_task`
2. `test_update_status_returns_error_when_user_unauthorized`
3. `test_concurrent_status_updates_preserve_consistency` (hint: this requires two db sessions)

---

**Exercise 3 — Run a coverage gap analysis (stretch)**

Take a real service file from your own project. Run `/test-gen` on it (or simulate the process manually using the boundary analysis technique). After reviewing the output:
1. Which methods had obvious boundaries that Claude caught?
2. Which methods required domain knowledge (business rules) that Claude could not infer?
3. Write a one-paragraph addition to the `CLAUDE.md` "Architecture" section that would give Claude enough context to generate better tests for the domain-knowledge-dependent methods.

---

## Connect it back

[Day 8](day-08-taskflow-scaffold.md) built the scaffold. Today you saw the first major workflow payoff: systematic test generation that respects your project's conventions and covers boundary conditions that ad-hoc prompting misses.

Tomorrow you use the test suite you just generated as the foundation for safe code review and refactoring — the tests are the net that makes it safe to change the code.

Sharp question to carry into Day 10: the `/review` skill checks for service layer violations. You run it on `task_service.py` and it finds three issues. Two are minor style notes. One is a genuine architectural violation from three months ago. What should Claude do about each — and how does the `/fix` skill factor in?

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
- *Software Engineering at Google*, Chapter 11 "Testing Overview" — the section "The Beyoncé Rule" (if you liked it enough to put it in prod, put it in tests) and "Test size" classification. Understanding what constitutes a unit test vs. integration test vs. system test maps directly onto the fixture choices in the generated test suite above.

**If you want the deep version:**
- *Software Engineering at Google*, Chapter 12 "Unit Testing" — the section on "Writing clear tests" covers the exact bad patterns to watch for in generated tests: implementation-coupled tests, tests that don't test behavior. Read alongside your generated test output.
- *Software Engineering at Google*, Chapter 14 "Larger Testing" — when the service-layer unit tests are not enough and you need integration tests. Relevant to Day 13's CI/CD integration.

---

← [Day 8 — Case Study Kickoff: TaskFlow Scaffold](day-08-taskflow-scaffold) &nbsp;|&nbsp; [Day 10 — Code Review + Refactoring →](day-10-code-review-refactoring)
