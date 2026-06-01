*[Claude Code 101](../../README.md) · Day 5 of 14*

# Day 5 — Behavior Gates: Hooks as Deterministic Guardrails

> **Today's one idea:** Hooks inject determinism into a probabilistic system — they are the layer of the stack that Claude cannot reason its way around.
> **Reading time:** ~60 min · **Prereqs:** [Day 3](day-03-writing-claude-md-and-settings.md), [Day 4](day-04-skills-from-prompt-to-playbook.md)
> **Primary source for today:** Claude Code Official Documentation — "Hooks" reference section

---

## The hook

You have a brilliant junior developer on your team. Technically sharp, fast learner, good instincts. You trust them with most tasks. But you have also noticed: when they are in flow state and moving fast, they occasionally do things you wish they hadn't. Not maliciously — they just convinced themselves the shortcut was fine, or forgot the rule in the moment.

You've handled this two ways:
1. You told them the rules (analogous to `CLAUDE.md`).
2. You restricted their access so they physically cannot do certain things (analogous to `settings.json` deny rules).

But there is a third class of problem that neither solution fully addresses: the things that should be *checked*, not just permitted or blocked. Auto-format on every file touch. Scan for secrets before every commit. Log every time Claude runs a database write. These are not about blocking — they are about adding consistent, automatic quality checks to a workflow that might otherwise skip them.

This is what hooks do. They are the CI checks of your Claude Code workflow — deterministic, automatic, unbypassable, running at the right moments regardless of what Claude or the user did to trigger them.

The critical difference from `CLAUDE.md` rules: when a hook says "no," Claude does not get to reason about whether the rule applies in this specific case. The shell command runs, the exit code is read, and the decision is made at the runtime level, before Claude's probabilistic judgment enters the picture.

---

## Building the intuition

### Probabilistic vs. deterministic safety

Every rule in `CLAUDE.md` is subject to Claude's judgment. Claude reads the rule, considers the current context, and decides whether it applies. For most rules, this is fine — you want Claude to apply intelligence to your instructions.

But some rules should not be subject to judgment. For these, you want the equivalent of a physical constraint: if the rule says "lint every Python file you touch," that should happen whether Claude thinks linting is necessary in this context or not.

Consider the difference:

| Mechanism | Claude's role | Reliability |
|-----------|--------------|-------------|
| `CLAUDE.md` rule: "lint Python files after editing" | Reads the rule, decides whether to apply it | Probabilistic — follows it most of the time |
| `settings.json` deny: `"Edit(*.py)"` | Runtime blocks the tool entirely | Deterministic — but this blocks too much |
| `PostToolUse` hook: run flake8 after any Edit to a .py file | Runtime calls the hook regardless of Claude's intent | Deterministic — runs every time |

The hook is the right tool here: it does not block the edit, but it guarantees the check runs after every edit.

### The three checkpoints

Hooks fire at three moments in the Claude Code workflow:

```
User message
     │
     ▼
Claude reasons, decides to call a tool
     │
     ├── PreToolUse ←────────── CHECKPOINT 1
     │   (Can block tool call)
     │
     ▼
Tool executes
     │
     ├── PostToolUse ←─────────── CHECKPOINT 2
     │   (Cannot undo, but can react)
     │
     ▼
Claude produces final response, session would end
     │
     └── Stop ←──────────────── CHECKPOINT 3
         (Can block session from stopping, force more work)
```

Each checkpoint serves a different purpose:

**PreToolUse:** Inspect and optionally block before something happens. Best for safety checks — "is this action safe to proceed with?"

**PostToolUse:** React after something happened. Cannot undo, but can trigger follow-up actions — linting, logging, notifications, quality checks.

**Stop:** Intercept before Claude signals completion. Used for "don't let Claude mark this done until certain conditions are met."

### The airport security analogy

Airport security does not rely on passengers to self-report prohibited items. It runs every passenger through a deterministic check — X-ray, metal detector — regardless of whether the passenger seems trustworthy or has flown a hundred times without incident.

Your hooks are that check. They do not replace your trust in Claude. They add a layer that runs regardless of Claude's judgment because some things are too important to leave to probabilistic compliance.

The practical result: your team can give Claude significant autonomy (broad permissions, powerful skills) precisely because the hooks provide a deterministic safety net. Autonomy and safety scale together, not in opposition.

---

## The formal picture

### Hook configuration in settings.json

Hooks are configured in the `hooks` block of `.claude/settings.json`:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "/path/to/scripts/check-dangerous-bash.sh"
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
            "command": "scripts/post-edit.sh"
          }
        ]
      }
    ],
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "scripts/pre-stop.sh"
          }
        ]
      }
    ]
  }
}
```

**`matcher`** — a regex pattern matched against the tool name. `"Edit|Write"` fires for both `Edit` and `Write` tools. `"Bash"` fires for all bash invocations. Omit `matcher` in `Stop` (it has no tool to match).

**`type`** — always `"command"` for shell-based hooks.

**`command`** — the shell command to run. Receives the hook event as a JSON payload on stdin.

### The hook event payload

Every hook receives a JSON payload on stdin describing the event. This is what makes hooks powerful — they can inspect the actual tool call, not just the fact that a tool was called.

**PreToolUse payload:**
```json
{
  "tool_name": "Edit",
  "tool_input": {
    "file_path": "/path/to/app/services/task_service.py",
    "old_string": "def create_task(title):",
    "new_string": "def create_task(title: str) -> Result:"
  }
}
```

**PostToolUse payload:**
```json
{
  "tool_name": "Edit",
  "tool_input": { ... },
  "tool_output": "Successfully edited file"
}
```

**Stop payload:**
```json
{
  "stop_reason": "end_turn",
  "transcript": [ ... ]
}
```

### Exit codes: how hooks communicate decisions

The hook's exit code is the signal the runtime reads:

| Exit code | Meaning | Effect |
|-----------|---------|--------|
| `0` | Pass — proceed normally | Tool executes (PreToolUse) / session continues (Stop) |
| `2` | Block — reject with message | Tool is blocked; hook's stdout is shown to Claude as error message |
| Any other non-zero | Non-blocking error | Logged, but tool proceeds / session continues |

Exit code `2` is the blocking signal. When a PreToolUse hook exits with `2`, the tool call is cancelled and Claude receives the hook's stdout as an explanation. Claude then must decide what to do — ask the user, try a different approach, or abandon the task.

### Four production-grade hooks for TaskFlow

**Hook 1: PostToolUse — Auto-lint Python files after every edit**

```bash
#!/bin/bash
# scripts/post-edit-lint.sh
# Runs flake8 on any Python file that was just edited

# Parse the tool input from stdin
TOOL_INPUT=$(cat)
FILE_PATH=$(echo "$TOOL_INPUT" | python3 -c "
import json, sys
data = json.load(sys.stdin)
path = data.get('tool_input', {}).get('file_path', '')
print(path)
")

# Only lint Python files
if [[ "$FILE_PATH" == *.py ]]; then
  RESULT=$(flake8 "$FILE_PATH" --max-line-length=100 2>&1)
  if [ -n "$RESULT" ]; then
    echo "Linting issues found in $FILE_PATH:"
    echo "$RESULT"
    # Exit 0 — this is a non-blocking notification, not a block
    # Claude will see this output and decide whether to fix it
    exit 0
  fi
fi

exit 0
```

Register in settings.json:
```json
"PostToolUse": [
  {
    "matcher": "Edit|Write",
    "hooks": [{ "type": "command", "command": "scripts/post-edit-lint.sh" }]
  }
]
```

**Hook 2: PreToolUse — Block dangerous Bash patterns**

```bash
#!/bin/bash
# scripts/pre-bash-guard.sh
# Blocks bash commands that match dangerous patterns

TOOL_INPUT=$(cat)
COMMAND=$(echo "$TOOL_INPUT" | python3 -c "
import json, sys
data = json.load(sys.stdin)
cmd = data.get('tool_input', {}).get('command', '')
print(cmd)
")

# Pattern list — each is a regex that should be blocked
DANGEROUS_PATTERNS=(
  "rm -rf"
  "rm -f /"
  "git push --force"
  "alembic upgrade head"
  "DROP TABLE"
  "TRUNCATE TABLE"
  "DELETE FROM .* WHERE 1=1"
  "DELETE FROM [a-z_]+ *$"   # DELETE without WHERE clause
)

for PATTERN in "${DANGEROUS_PATTERNS[@]}"; do
  if echo "$COMMAND" | grep -qiE "$PATTERN"; then
    echo "BLOCKED: Command matches dangerous pattern: '$PATTERN'"
    echo "Command was: $COMMAND"
    echo "If this operation is intentional, run it manually outside Claude Code."
    exit 2  # Exit 2 = block
  fi
done

exit 0  # Pattern not matched — allow
```

**Hook 3: PostToolUse — Secret scanner (run after every file write)**

```bash
#!/bin/bash
# scripts/secret-scan.sh
# Scans written files for common secret patterns

TOOL_INPUT=$(cat)
FILE_PATH=$(echo "$TOOL_INPUT" | python3 -c "
import json, sys
data = json.load(sys.stdin)
path = data.get('tool_input', {}).get('file_path', '')
print(path)
")

if [ -z "$FILE_PATH" ] || [ ! -f "$FILE_PATH" ]; then
  exit 0
fi

# Common secret patterns
SECRET_PATTERNS=(
  "sk-[a-zA-Z0-9]{48}"           # OpenAI key
  "AKIA[0-9A-Z]{16}"             # AWS access key
  "AIza[0-9A-Za-z\\-_]{35}"      # Google API key
  "ghp_[a-zA-Z0-9]{36}"          # GitHub personal token
  "password\s*=\s*['\"][^'\"]{8}" # Hardcoded password
  "api_key\s*=\s*['\"][^'\"]+"   # Generic API key
)

for PATTERN in "${SECRET_PATTERNS[@]}"; do
  if grep -qE "$PATTERN" "$FILE_PATH" 2>/dev/null; then
    echo "SECURITY: Potential secret detected in $FILE_PATH"
    echo "Pattern matched: $PATTERN"
    echo "Review the file and move secrets to .env.local before proceeding."
    exit 2  # Block — this is serious enough to stop
  fi
done

exit 0
```

**Hook 4: Stop — Require tests to pass before Claude signals completion**

```bash
#!/bin/bash
# scripts/pre-stop-tests.sh
# Runs the test suite before allowing Claude to complete a coding task

STOP_INPUT=$(cat)
STOP_REASON=$(echo "$STOP_INPUT" | python3 -c "
import json, sys
data = json.load(sys.stdin)
print(data.get('stop_reason', ''))
")

# Only run tests on end_turn (not on errors or user interrupts)
if [ "$STOP_REASON" != "end_turn" ]; then
  exit 0
fi

# Run the test suite
TEST_RESULT=$(cd backend && python -m pytest tests/ -q --tb=short 2>&1)
EXIT_CODE=$?

if [ $EXIT_CODE -ne 0 ]; then
  echo "Tests failed — Claude Code cannot complete this task until tests pass."
  echo ""
  echo "$TEST_RESULT" | tail -20
  exit 2  # Block the Stop — Claude must fix the tests first
fi

echo "All tests passing. Task complete."
exit 0
```

### Wiring it all together: the complete hooks block

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          { "type": "command", "command": "scripts/pre-bash-guard.sh" }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          { "type": "command", "command": "scripts/post-edit-lint.sh" },
          { "type": "command", "command": "scripts/secret-scan.sh" }
        ]
      }
    ],
    "Stop": [
      {
        "hooks": [
          { "type": "command", "command": "scripts/pre-stop-tests.sh" }
        ]
      }
    ]
  }
}
```

Multiple hooks on the same event run in sequence. The first exit code `2` stops the chain.

---

## Where it breaks / what it is not

**Hooks that are too slow.** Every PostToolUse hook adds latency to every tool call it matches. A `flake8` run takes ~0.5 seconds. If Claude edits 20 files in a session, that is 10 seconds of overhead. For fast tools this is fine; for slow tools (large test suites, network calls) consider using the `Stop` hook instead of `PostToolUse`.

**Hooks that silently fail.** A hook that exits with a non-zero code (but not `2`) produces a log entry and continues. If your guard script has a bug that causes it to exit with `1` instead of `2`, it will appear to run but never block anything. Always test your hooks with `echo '{"tool_input": {"command": "rm -rf /"}}' | bash scripts/pre-bash-guard.sh && echo "Exit: $?"`.

**The false security of overly broad patterns.** A block on `"rm"` will also block `"npm run"` (which contains no `rm`), no — but a block on `"rm"` using a simple `grep` will match `"format"` because `format` contains `rm`. Test your regex patterns carefully.

**Hooks vs. permissions — when to use each:**

| Use `settings.json` deny | Use `PreToolUse` hook |
|--------------------------|----------------------|
| You want to block completely — no exceptions | You want to block with informative messaging |
| The tool should never be used in this project | You want to log what was attempted |
| Simple pattern matching is sufficient | You need to inspect the full command content |
| Speed matters (deny is faster than hook) | You want Claude to receive guidance, not just a block |

**Hooks cannot undo actions.** A `PostToolUse` hook fires after the tool has run. If Claude writes a file and the hook detects a problem, the file is already written. The hook can block Claude from proceeding (by exiting `2`), but it cannot roll back the write. For truly dangerous irreversible operations, use `PreToolUse`, not `PostToolUse`.

---

## Try it yourself

**Exercise 1 — Trace the flow (comprehension check)**

Claude is editing `app/services/task_service.py`. Describe exactly what happens at each step if:
1. The edit introduces a flake8 violation
2. The edit accidentally includes a hardcoded API key
3. Both are true simultaneously

Use the hooks configured in the "Wiring it all together" section above. Which hooks fire, in which order, with which exit codes?

---

**Exercise 2 — Write a PreToolUse guard for migration safety (L2 exercise)**

Write a `PreToolUse` hook that:
1. Fires on Bash tool calls
2. Detects any command that contains `alembic upgrade`
3. Blocks it with a message directing the user to `scripts/deploy_migrate.sh`
4. Allows `alembic revision` commands through (creating new migrations is fine — running them directly is not)

Test your regex with these inputs:
- `alembic upgrade head` → should block
- `alembic upgrade +1` → should block
- `alembic revision --autogenerate -m "add user table"` → should allow
- `echo "alembic upgrade"` → should this block? (Decide and justify your choice)

---

**Exercise 3 — Design a quality gate (stretch)**

Design a `Stop` hook for TaskFlow that runs before Claude signals task completion and checks:
1. The test suite passes (`python -m pytest tests/ -q`)
2. No new flake8 violations were introduced (compare against the current `main` branch)
3. The OpenAPI spec was updated if any route handler files were modified (hint: use `git diff --name-only` in the hook)

Write the shell script and explain how it handles the case where one check passes and two fail — what does Claude see, and what is it expected to do?

---

## Connect it back

[Days 2–4](day-02-claude-md-the-10-percent.md) built the advisory layers: what Claude is briefed, what it is told, what procedures it follows. Today you built the enforced layer — the one that runs regardless of Claude's reasoning.

The combination is the point: `CLAUDE.md` + skills handle the 95% case where Claude's judgment is correct and sufficient. Hooks handle the 5% where deterministic enforcement matters more than flexibility.

Tomorrow you add the final extension mechanism: MCP servers — giving Claude access to systems it was not born knowing about. Then the full wiring diagram closes, and you will see how all four layers connect into a coherent whole.

Sharp question to carry into Day 6: you now have four mechanisms for controlling Claude's behavior (CLAUDE.md, settings.json permissions, skills, hooks). How do you decide which mechanism to use for a given requirement?

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
- Claude Code Documentation, "Hooks" reference — the complete hook event schema, all payload fields, and the full list of supported hook types. Pay particular attention to the `matcher` syntax and how multiple hooks on the same event are ordered. `[VERIFY at docs.anthropic.com/en/docs/claude-code]`

**If you want the deep version:**
- *The Art of Unix Programming*, Chapter 7 "Multiprogramming" (Eric S. Raymond, free at catb.org/esr/writings/taoup/) — the philosophy behind small, composable, event-driven programs. Hooks are this philosophy applied to AI workflows. Understanding why Unix pipes work the way they do helps you design hooks that compose well.
- Claude Code Documentation, "Settings reference" — the full `settings.json` schema. Read the permissions and hooks sections together to understand the complete security model. Hooks and deny rules are complementary, not redundant.

---

← [Day 4 — Skills: From Prompt to AI Playbook](day-04-skills-from-prompt-to-playbook) &nbsp;|&nbsp; [Day 6 — MCP Servers + Full Wiring Diagram →](day-06-mcp-servers-wiring-diagram)
