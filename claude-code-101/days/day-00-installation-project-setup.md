*[Claude Code 101](../../README.md) · Day 0 of 14*

# Day 0 — Installation + Project Setup

> **Today's one idea:** The configuration you build before your first Claude Code session determines the quality of every session that follows — for new projects and existing ones alike. Set it up once, correctly, and every future session starts loaded.
> **Reading time:** ~45 min · **Prereqs:** None — this is Day 0
> **Primary source for today:** Claude Code Official Documentation — "Getting started" and "Configuration" sections — docs.anthropic.com/en/docs/claude-code

---

## The hook

Two developers join the same project. Both have Claude Code installed. Both type their first prompt.

Developer A's session: Claude asks what language the project uses. Then what testing framework. Then what the folder structure is. Then whether it should use async or sync. Fifteen minutes later, after a lot of back-and-forth, it produces something approximately right.

Developer B's session: Claude reads `.claude/CLAUDE.md`, sees the FastAPI service layer rules, the Pydantic v2 conventions, the async SQLAlchemy patterns, and the three things it must never do. Its first output is production-ready.

The difference is not Claude's capability. It is two hours of setup work that Developer B did before their first session. This page is those two hours.

---

## Part 1 — Installation

### Prerequisites

Before installing Claude Code, verify:

```bash
# Node.js 18 or higher required
node --version    # should output v18.x.x or higher

# npm 8 or higher
npm --version     # should output 8.x.x or higher
```

If Node.js is not installed, download it from [nodejs.org](https://nodejs.org) or use a version manager:

```bash
# Using nvm (recommended — lets you switch Node versions per project)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
nvm install 20
nvm use 20
```

### Install the CLI

```bash
npm install -g @anthropic-ai/claude-code
```

Verify:

```bash
claude --version
# Should output: claude-code/x.x.x ...
```

### Authenticate

**Option A — Environment variable (recommended for teams and CI):**

```bash
# Add to ~/.bashrc, ~/.zshrc, or your shell profile
export ANTHROPIC_API_KEY="sk-ant-..."

# Reload the profile
source ~/.bashrc   # or source ~/.zshrc
```

Get your API key from [console.anthropic.com](https://console.anthropic.com) → API Keys.

**Option B — Interactive login:**

```bash
claude login
# Opens a browser window for OAuth authentication
# Credentials stored in ~/.claude/.credentials.json
```

**Option C — `.env` file (per-project, but never commit this):**

```bash
# In your project root
echo 'ANTHROPIC_API_KEY=sk-ant-...' >> .env
echo '.env' >> .gitignore   # CRITICAL — do this immediately
```

### Verify the installation works

```bash
claude --print "Say hello in one sentence."
# Should return a short response. If it does, CLI + auth are working.
```

If you see `401 Unauthorized`, your API key is not set or is invalid. If you see `command not found`, the npm global bin directory is not in your `PATH`:

```bash
# Find where npm installs global packages
npm config get prefix
# Add <prefix>/bin to your PATH in ~/.bashrc or ~/.zshrc
```

---

## Part 2 — Global setup (once, on your machine)

The global `~/.claude/` directory holds configuration that applies to *every* project on your machine. Set it up once; it travels with you.

```
~/.claude/
├── CLAUDE.md           ← Personal cross-project preferences
├── settings.json       ← Personal tool permissions
└── commands/           ← Personal skills (available in every project)
```

### Global CLAUDE.md — your personal briefing

This file loads in every session, in every project. Keep it short (< 50 lines) and personal. It is not the place for project-specific rules.

```bash
# Create it
mkdir -p ~/.claude
touch ~/.claude/CLAUDE.md
```

A good global CLAUDE.md template:

```markdown
# Global Developer Preferences

## My style
- I prefer explicit over implicit in all code
- Type hints everywhere in Python; strict TypeScript
- Tests before production code, always
- Commit messages: imperative mood, < 72 chars

## My workflow habits
- When I say "clean up" I mean refactor only — do not change behavior
- When I say "fix the bug" I mean: write a failing test first, then fix
- Always tell me which files you changed at the end of a task

## Things I always want
- Explain your reasoning briefly before making changes
- Flag anything that looks like a security concern, even if not asked
- If a task would take more than ~10 tool calls, ask me to break it down first

## My machine context
- Default Python: 3.12 (via pyenv)
- Default Node: 20 (via nvm)
- Editor: VS Code
```

What belongs in global CLAUDE.md vs. project CLAUDE.md:

| Global | Project |
|--------|---------|
| Personal preferences (how I like to work) | Project architecture and conventions |
| Machine-specific paths or tools | Stack-specific rules |
| Cross-project habits | Forbidden actions for this codebase |
| Things true of all your projects | The CLAUDE.md that gets committed to the repo |

### Global settings.json — personal tool permissions

```bash
touch ~/.claude/settings.json
```

A reasonable starting point:

```json
{
  "permissions": {
    "allow": [
      "Read(**)",
      "Glob(**)",
      "Grep(**)",
      "Bash(git status)",
      "Bash(git log*)",
      "Bash(git diff*)",
      "Bash(git branch*)"
    ],
    "deny": [
      "Bash(rm -rf*)",
      "Bash(git push --force*)",
      "Bash(sudo rm*)"
    ]
  }
}
```

The global settings file is additive — project settings layer on top. A `deny` in either file blocks the tool. An `allow` in either file approves it.

---

## Part 3 — Setting up a new project from scratch

This is the sequence for a project that does not yet have any Claude Code configuration.

### Step 1: Navigate to the project root

```bash
cd /path/to/your/project
```

Claude Code uses the current directory as the project root. Always run it from there.

### Step 2: Run `claude init`

```bash
claude init
```

This command:
- Analyzes your existing files (package.json, pyproject.toml, tsconfig.json, etc.)
- Generates a starter `.claude/CLAUDE.md` with inferred project facts
- Creates the `.claude/` directory structure

The generated file is a **starting point**, not a finished product. It will get the technology stack approximately right but will miss your architecture decisions, conventions, and forbidden actions.

You should see output similar to:
```
✓ Created .claude/
✓ Generated .claude/CLAUDE.md
  → Detected: Python 3.12, FastAPI, SQLAlchemy
  → Added 12 inferred conventions
  → Review and edit before your first session
```

### Step 3: Edit CLAUDE.md

Open `.claude/CLAUDE.md` and expand it with what `claude init` could not infer:

```bash
# Open in your editor
code .claude/CLAUDE.md    # VS Code
vim .claude/CLAUDE.md     # vim
```

Follow the template from [Day 3](day-03-writing-claude-md-and-settings.md). At minimum, add:

**Architecture section** — how your code is structured (service layers, module boundaries, what calls what):
```markdown
## Architecture
- app/services/ — all business logic. Route handlers call services, never the DB directly.
- app/api/v1/ — thin route handlers only. No ORM queries here.
- app/schemas/ — Pydantic v2 models for all request/response bodies.
- Result[T, str] return type for all service methods. No bare raise.
- Alembic migrations in alembic/versions/ — never edit after merge.
```

**FORBIDDEN section** — the things Claude must never do, derived from your past incidents or architectural commitments:
```markdown
## FORBIDDEN — Never do these
- Never query the database directly from a route handler
- Never use .dict() — use .model_dump() (Pydantic v2)
- Never commit secrets, API keys, or tokens in any file
- Never edit an existing Alembic migration file
- Never use synchronous SQLAlchemy in async context
```

**Stable content at the top, volatile content at the bottom.** Project facts and architecture change rarely — put them first. Active work changes every sprint — put it last. This ordering maximizes prompt cache hit rate (explained in [Day 7](day-07-token-economy.md)).

### Step 4: Create settings.json

```bash
touch .claude/settings.json
```

A project-specific starting template:

```json
{
  "permissions": {
    "allow": [
      "Bash(python -m pytest*)",
      "Bash(npm test*)",
      "Bash(npm run*)",
      "Bash(pip install*)",
      "Bash(alembic upgrade head)",
      "Bash(alembic revision*)"
    ],
    "deny": [
      "Bash(alembic downgrade*)",
      "Bash(DROP TABLE*)",
      "Bash(git push --force*)"
    ]
  }
}
```

Calibrate `allow` to your actual dev workflow — what commands do you type every day? Those are candidates to pre-approve so Claude is not asking for permission on routine operations.

### Step 5: Create the skills directory

```bash
mkdir -p .claude/commands
```

You will populate this with skills as you build them (Days 4, 9–11). For now, create one minimal skill to verify the pattern works:

```bash
cat > .claude/commands/hello.md << 'EOF'
---
description: Verify Claude Code is configured correctly for this project.
---

Read .claude/CLAUDE.md and summarize:
1. What project is this?
2. What is the primary technology stack?
3. Name one FORBIDDEN action from the file.

Then confirm: "Claude Code is configured for [project name]."
EOF
```

### Step 6: Create the hooks directory

```bash
mkdir -p .claude/hooks
```

A minimal first hook — a secret scanner that fires before Claude runs any Bash command:

```bash
cat > .claude/hooks/secret-scan.sh << 'EOF'
#!/usr/bin/env bash
# PreToolUse: Scan Bash commands for accidental secret exposure

COMMAND=$(cat)

# Patterns that look like secrets being echoed or written to files
SECRET_PATTERNS=(
  'sk-ant-'
  'ANTHROPIC_API_KEY'
  'AWS_SECRET'
  'GITHUB_TOKEN'
  'DATABASE_URL.*password'
)

for PATTERN in "${SECRET_PATTERNS[@]}"; do
  if echo "$COMMAND" | grep -qiE "$PATTERN"; then
    echo "BLOCKED: Command contains a pattern that looks like a secret." >&2
    echo "Pattern matched: $PATTERN" >&2
    echo "If this is a false positive, run the command in your terminal directly." >&2
    exit 2
  fi
done

exit 0
EOF

chmod +x .claude/hooks/secret-scan.sh
```

Wire it in `settings.json`:

```json
{
  "permissions": {
    "allow": ["..."],
    "deny": ["..."]
  },
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "cat | .claude/hooks/secret-scan.sh"
          }
        ]
      }
    ]
  }
}
```

### Step 7: Add to .gitignore

```bash
cat >> .gitignore << 'EOF'

# Claude Code — personal overrides (not for team sharing)
.claude/CLAUDE.local.md
EOF
```

Everything else in `.claude/` should be committed — `CLAUDE.md`, `settings.json`, all skills, all hooks. The `.local.md` file is for personal overrides that should not affect the team (your local DB port, personal style preferences, machine-specific paths).

### Step 8: Make hooks executable

```bash
chmod +x .claude/hooks/*.sh
```

If you forget this, hooks will fail silently — the command exists but cannot be executed.

### Step 9: Verify the full setup

Run each layer in order to confirm it loaded:

```bash
# Verify CLAUDE.md loads
claude --print "What project are we working on? Answer in two sentences."
# Should name your project and mention its stack from CLAUDE.md

# Verify skills work
claude --print "/hello"
# Should run your hello.md skill and confirm the project name

# Verify settings.json permissions (attempt something in the deny list)
# Claude should ask for confirmation or block it depending on your deny rules

# Verify hook fires
claude --print "Run: echo sk-ant-testkey"
# Should be blocked by the secret scanner
```

If any step fails, check:
- Is `.claude/` in the project root (not a subdirectory)?
- Are hooks executable (`ls -la .claude/hooks/`)?
- Does `settings.json` have valid JSON (no trailing commas)?

### Final structure for a new project

```
your-project/
├── .claude/
│   ├── CLAUDE.md              ✓ Committed — team briefing
│   ├── settings.json          ✓ Committed — permissions and hooks
│   ├── CLAUDE.local.md        ✗ Gitignored — personal overrides
│   ├── commands/
│   │   └── hello.md           ✓ Committed — your first skill
│   └── hooks/
│       └── secret-scan.sh     ✓ Committed + executable
└── .gitignore                 ✓ CLAUDE.local.md added
```

---

## Part 4 — Setting up an existing project

An existing project has code, a team, and conventions that have already been decided. Your job is to capture those decisions in Claude Code configuration — not invent new ones.

### Step 1: Check if configuration already exists

```bash
ls .claude/ 2>/dev/null && echo "Config exists" || echo "No config yet"
```

If `.claude/` exists, someone already started the setup. Read what is there before making changes. If `CLAUDE.md` already exists, treat it like code — do not overwrite it; open a PR to change it.

If `.claude/` does not exist, proceed with the steps below.

### Step 2: Gather project knowledge before touching any file

Before running `claude init`, spend 15–20 minutes collecting the raw material CLAUDE.md needs. You cannot write a good briefing for a project you do not understand.

**Gather:**

```bash
# What tech stack is this?
cat package.json pyproject.toml go.mod Cargo.toml 2>/dev/null | head -40

# What is the directory structure?
find . -type d -not -path '*/node_modules/*' -not -path '*/.git/*' | head -30

# What are the recent conventions (commit message style, PR conventions)?
git log --oneline -20

# Are there any existing style or architecture documents?
find . -name 'CONTRIBUTING.md' -o -name 'ARCHITECTURE.md' -o -name 'ADR*' | head -10
cat CONTRIBUTING.md 2>/dev/null

# What does the CI pipeline check? (reveals enforced conventions)
cat .github/workflows/*.yml 2>/dev/null | head -80
```

Interview a senior developer if possible. The questions to ask:
1. "What is the most important architectural rule in this codebase — the one new developers always get wrong?"
2. "What action, if Claude took it, would cause the most damage?"
3. "What are the three things you always explain in code review?"

These answers become your FORBIDDEN section and your Architecture section.

### Step 3: Run `claude init`

```bash
claude init
```

Review the generated `.claude/CLAUDE.md`. It will:
- **Get right:** language, framework, rough directory structure
- **Get wrong or miss:** service layer rules, test patterns, forbidden actions, post-incident conventions, anything from tribal knowledge

Correct and expand it using what you gathered in Step 2.

### Step 4: Audit against existing patterns

Open the three most recently changed files in the codebase. For each, ask:

- Would a developer following my CLAUDE.md produce something similar?
- Are there patterns in these files that CLAUDE.md does not mention?
- Are there patterns I said were FORBIDDEN that these files violate? (If so, either the rule is wrong or there is technical debt to document.)

```bash
# Find the 5 most recently changed source files (excluding tests and migrations)
git diff --name-only HEAD~10 HEAD \
  | grep -v test | grep -v migration | grep -v alembic \
  | head -5
```

Open each file. Write CLAUDE.md rules that would reproduce the patterns you see.

### Step 5: Check what CI already enforces

The CI pipeline reveals the minimum acceptable standard the team has already decided on. Anything CI enforces is a CLAUDE.md rule for free — you just need to document it.

```bash
cat .github/workflows/ci.yml
```

Common CI checks to document in CLAUDE.md:
- Linter rules (flake8 config, eslint config)
- Type checker (mypy strict mode, TypeScript strict)
- Test runner commands and coverage thresholds
- Migration checks
- Secret scanning

### Step 6: Create settings.json

Follow the same template as new projects, but calibrate to the existing workflow:

```bash
# Find what commands the team runs regularly
git log --oneline -50 | head -20
cat Makefile 2>/dev/null  # Often documents the canonical dev commands
```

The Makefile or npm scripts are a reliable source of what to pre-approve in `settings.json` — they encode the commands the team already agrees are safe to run.

### Step 7: Add hooks for your highest-risk actions

Review recent production incidents (the post-mortem docs, the incident Slack channel, the blame on recently fixed bugs). Every incident is a candidate for a hook.

Start with one hook. The best first hook for an existing project is the one that guards the action most likely to cause the incident that just happened.

### Step 8: Onboard the team

Add a `## Claude Code Setup` section to your `CONTRIBUTING.md`:

```markdown
## Claude Code Setup

1. Install: `npm install -g @anthropic-ai/claude-code`
2. Set API key: `export ANTHROPIC_API_KEY=your_key` (add to ~/.bashrc)
3. Make hooks executable: `chmod +x .claude/hooks/*.sh`
4. Verify: `claude --print "What project are we working on?"`
   → Should describe this project from CLAUDE.md

Personal overrides (optional):
   `cp .claude/CLAUDE.local.md.example .claude/CLAUDE.local.md`
   Edit for your local database port, personal preferences, etc.

That's it. CLAUDE.md, settings.json, skills, and hooks are all in the repository.
```

Create a `.claude/CLAUDE.local.md.example` as a template:

```markdown
# Personal Overrides — do not commit this file

## My local setup
- Local database port: 5432  (change if yours differs)
- Local Redis: localhost:6379

## My preferences (override team defaults)
- I prefer verbose output when debugging
- Always show the full stack trace, even for minor errors
```

---

## The bootstrap checklist

Use this as a reference for any new setup:

### New project
- [ ] Node.js 18+ and npm installed
- [ ] `npm install -g @anthropic-ai/claude-code`
- [ ] API key set in environment variable
- [ ] `claude --version` works
- [ ] Global `~/.claude/CLAUDE.md` written (personal preferences)
- [ ] `cd` to project root
- [ ] `claude init` run
- [ ] CLAUDE.md expanded (Architecture + Conventions + FORBIDDEN)
- [ ] `.claude/settings.json` created with project-specific permissions
- [ ] `.claude/commands/` directory created; at least one skill present
- [ ] `.claude/hooks/` directory created; at least one hook present
- [ ] `chmod +x .claude/hooks/*.sh` run
- [ ] `CLAUDE.local.md` added to `.gitignore`
- [ ] All four verification commands pass
- [ ] `.claude/` committed (except CLAUDE.local.md)

### Existing project
- [ ] Check if `.claude/` already exists
- [ ] Read existing config before making changes
- [ ] Gather stack, conventions, and incidents (15–20 min)
- [ ] Interview a senior developer for the three code review rules
- [ ] `claude init` run (or manual creation if init would overwrite existing)
- [ ] CLAUDE.md audited against 5 recent code files
- [ ] CI pipeline checks documented in CLAUDE.md
- [ ] `settings.json` calibrated to existing Makefile / npm scripts
- [ ] First hook targeting highest-risk action
- [ ] `chmod +x .claude/hooks/*.sh`
- [ ] `CONTRIBUTING.md` updated with Claude Code setup section
- [ ] `.claude/CLAUDE.local.md.example` created
- [ ] Verification commands pass
- [ ] PR opened — treat CLAUDE.md like code

---

## Where it breaks

**`claude init` in a large monorepo generates a confused CLAUDE.md.** The file analyzer works well for single-purpose projects. For monorepos with 10 services, it will try to describe everything and describe nothing well. Run `claude init` from the subdirectory of the service you are configuring, not the monorepo root. Use one `.claude/` per service if services have genuinely different conventions.

**Hooks fail silently when not executable.** If you add a hook script but forget `chmod +x`, the hook runs but produces an error Claude Code interprets as a non-blocking failure (exit code other than 0 or 2). The hook does nothing. Always verify: `ls -la .claude/hooks/` and check the `x` bit.

**CLAUDE.md grows stale on existing projects.** The biggest risk in an existing project setup is writing CLAUDE.md to match what the project was when you set it up, then letting it drift as the project evolves. Add a recurring calendar reminder (quarterly, or after each major architectural change) to audit CLAUDE.md against the current codebase. Treat a stale CLAUDE.md as a bug.

**Committing the API key.** This happens. Set up the secret-scan hook from Step 6 before any other hook. If it has already happened, rotate the key immediately at console.anthropic.com, then add the hook.

---

## Connect it forward

Day 0 is the only day in this course that is purely procedural. From Day 1 onward, every page builds the conceptual model — what Claude Code actually is, why the configuration layers exist, and how to use each one to its maximum effect.

The setup you just did creates the foundation. Day 1 explains why the foundation works the way it does — and knowing the *why* is what lets you debug, extend, and adapt the setup when you encounter a project that doesn't fit the template.

**Before moving to Day 1, run this final check:**

```bash
claude --print "Describe this project's most important architectural rule in one sentence."
```

If the answer matches what you wrote in CLAUDE.md, the foundation is working. If it does not, check that CLAUDE.md is in `.claude/CLAUDE.md` (not the project root), and that you are running `claude` from the project root.

## Suggested readings

**Required if you have 15 extra minutes:**
- Claude Code Official Documentation, "Quickstart" section — docs.anthropic.com/en/docs/claude-code/quickstart — the official walkthrough. Reading it after this page, you will notice where it covers the happy path and where this page fills in the gaps (global config, hook verification, existing project audit).

**If you want the deep version:**
- Claude Code Official Documentation, "Configuration" — the complete reference for `settings.json` — every permission pattern, every hook event type, every env var. Keep it open as you configure your first real project.
- Claude Code Official Documentation, "CLAUDE.md reference" — what fields are supported, what the load order is across the three-layer hierarchy (global → project → local), and what `claude init` actually generates. Essential for understanding why your CLAUDE.md did or did not load as expected.

---

[Day 1 — How Claude Code Actually Works →](day-01-how-claude-code-works)