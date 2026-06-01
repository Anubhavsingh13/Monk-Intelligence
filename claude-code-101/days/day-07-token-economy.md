*[Claude Code 101](../../README.md) · Day 7 of 14*

# Day 7 — The Token Economy

> **Today's one idea:** Every token you send is budget you spend; four levers — context hygiene, compaction, prompt caching, and sub-agents — let you spend that budget on things that actually help Claude, not on noise.
> **Reading time:** ~60 min · **Prereqs:** [Day 6](day-06-mcp-servers-wiring-diagram.md)
> **Primary source for today:** *"Building Effective Agents"*, Anthropic Engineering Blog; Claude Code Docs — "Context management" and "Sub-agents"

---

## The hook

You are 45 minutes into a debugging session with Claude. The bug is complex — you have read eight files, run three test iterations, tried two approaches that did not work. The context window is at 78%.

Claude's last suggestion was generic. It repeated something it said 20 minutes ago. It missed an obvious constraint that was established early in the session.

You are not imagining it. Claude's reasoning quality degrades in long, cluttered contexts. Not because Claude gets tired — because the relevant signal (the actual bug, the relevant constraints) is now surrounded by 30,000 tokens of abandoned approaches, redundant file reads, and failed test outputs. The whiteboard is full of crossed-out work, and Claude is trying to read through it.

This is the token economy problem, and it has four solutions. Today you learn all four — not as abstract theory, but as specific, actionable techniques you can apply to any session.

---

## Building the intuition

Think of your context window as a whiteboard with a fixed size. At the start of a session it is clean and focused. As the session progresses, it fills with:

- Messages you exchanged early on that are no longer relevant
- File contents you read but do not need anymore
- Results from approaches you abandoned
- Repeated tool outputs
- Verbose error messages

The four levers each address a different part of this problem:

| Lever | Problem it solves | When to apply |
|-------|------------------|---------------|
| **Context hygiene** | You are putting irrelevant content in the whiteboard to begin with | Before the session starts and during task setup |
| **Compaction** | The whiteboard has filled with outdated content mid-session | When you notice quality degrading, or at natural task boundaries |
| **Prompt caching** | You are re-paying for the same static content every session | When CLAUDE.md and skill content is large and stable |
| **Sub-agents** | One long serial context is doing work that could be parallel and isolated | When a task decomposes into independent parts |

---

## The formal picture

### Lever 1: Context Hygiene — What to put in and what to leave out

Context hygiene is not a feature you toggle — it is a discipline you practice every time you start a session or provide information to Claude.

**Rule 1: Read files on demand, not upfront.**

Bad pattern:
```
"Here are all 12 files in the service layer: [pastes 12 files worth of content]
Now help me add input validation to the task creation endpoint."
```

Good pattern:
```
"Help me add input validation to the task creation endpoint in
app/api/v1/tasks.py. Read the file and any relevant service/model files
as you need them."
```

The difference: in the bad pattern, 12 files enter context before Claude does any reasoning. Most of them will never be relevant to the task. In the good pattern, Claude reads only what it needs, when it needs it.

**Rule 2: Scope your task to the smallest useful unit.**

Bad: "Refactor the entire service layer."
Good: "Refactor `TaskService.create_task` to use the Result pattern — read the existing code first, then propose a plan."

Smaller scope = smaller context = better reasoning on the thing that matters.

**Rule 3: Close and reopen sessions at task boundaries.**

If you finish fixing a bug and now want to work on a new feature, do not continue the same session. The bug-fixing context is now noise for the feature work. Start fresh — your `CLAUDE.md` will re-brief Claude automatically.

**Rule 4: Use specific file paths, not directory reads.**

Bad: `"Read everything in app/services/"` — loads all service files
Good: `"Read app/services/task_service.py"` — loads one file

**Quantifying the impact:** A medium Python file is ~1,000 tokens. Reading 10 unnecessary files = 10,000 tokens of noise that Claude must reason through for every subsequent response in the session. At scale, this is the difference between sharp, specific answers and generic, unfocused ones.

---

### Lever 2: Compaction — Keeping Long Sessions Clean

Even with good hygiene, long sessions fill up. Compaction is the mechanism for clearing the whiteboard while keeping the important facts.

**How it works:**

Claude Code replaces a portion of the conversation history with a structured summary. The summary captures:
- What was decided
- What was tried and abandoned (and why)
- The current state of the task
- Any constraints discovered during the session

The raw conversation (file contents, tool outputs, failed attempts) is discarded. The summary is much smaller — typically 10–20% of the original size.

**Two ways to trigger compaction:**

Manual: type `/compact` at any point in the session. Claude will summarize the conversation up to that point.

Automatic: Claude Code triggers compaction automatically when the context window exceeds ~85–90% capacity.

**When to compact manually:**

- After completing one subtask before starting the next (e.g., after fixing the bug, before starting the test)
- After an approach fails and you are pivoting to a different strategy
- When you notice Claude starting to repeat itself or miss previously established constraints
- After a long file-reading phase before starting the editing phase

**What compaction loses:**

Compaction is lossy. It summarizes; it does not preserve. If Claude's reasoning during the conversation established something important — a specific invariant, a subtle constraint, a decision and its rationale — and that reasoning is compressed away, Claude may not apply it correctly after compaction.

Mitigate this: before compacting, explicitly state the critical facts:

```
Before compacting: the key constraint we established is that task status 
can only change through TaskService.update_status, and we must preserve 
the optimistic locking mechanism in that method. Compact now.
```

---

### Lever 3: Prompt Caching — Reusing Computation

Every time you start a Claude Code session, the content of your `CLAUDE.md` is processed from scratch — tokenized, fed through the model, computed. If your `CLAUDE.md` is 2,000 tokens and you run 50 sessions a day, you are re-computing 100,000 tokens of identical content daily.

Prompt caching eliminates this cost.

**How it works:**

Anthropic caches the KV (key-value attention) computation for prompt prefixes. If the beginning of your prompt is identical across calls, the cached computation is reused. Cache hits cost approximately **10% of the standard input token price** and respond faster.

The cache has a **5-minute TTL**: if the same prefix appears in two calls within 5 minutes, the second call hits the cache.

**What this means for you:**

Your `CLAUDE.md` content and skill instructions are the most cache-friendly content in your workflow. They are static (same content every session) and they always appear at the beginning of the context (loaded at session start before any conversation).

**How to structure for maximum cache hits:**

```
Context window order for maximum cache benefit:
┌─────────────────────────────────────────┐
│  1. System prompt (Claude Code managed) │ ← Always identical
│  2. CLAUDE.md content                   │ ← Static, long-lived
│  3. Skill instructions (if active)      │ ← Static per skill
│  4. Conversation history                │ ← Changes every turn
└─────────────────────────────────────────┘
```

The more stable content at the top, the larger the cacheable prefix, the higher the cache hit rate. This ordering is Claude Code's default — your job is to keep items 2 and 3 stable (don't edit `CLAUDE.md` mid-session) and to keep them as far from item 4 as possible (don't interleave static content with conversation history).

**Practical CLAUDE.md caching optimization:**

Put stable content at the top of your `CLAUDE.md`, variable content at the bottom:
```markdown
# TaskFlow — Claude Code Configuration

## Project Facts (stable)          ← put at top, maximizes cache prefix
## Architecture (stable)
## Conventions (mostly stable)
## Active Sprint Context (changes) ← put at bottom, changes often
```

**Rough cost impact for a team:**
- 2,000 token CLAUDE.md × 50 sessions/day = 100,000 daily cache tokens
- At 90% cache hit rate: pay full price for 10,000 tokens, cache price for 90,000
- For claude-sonnet-4-6: ~$0.003/1K input tokens → full: ~$0.30, cache: ~$0.027
- Monthly saving for a team of 5: roughly $60–120 just on CLAUDE.md tokens

Small per session; meaningful at team scale.

---

### Lever 4: Sub-agents — Parallel Isolated Work

The first three levers optimize a single context window. Sub-agents are a different approach: instead of one long context doing everything, you spawn multiple short focused contexts.

**The core insight:**

Many engineering tasks decompose into independent subtasks. "Run the full quality audit" decomposes into:
- Run tests and report failures
- Run linting and report violations
- Check security with bandit

These three tasks are independent — the test results do not affect the linting results. Running them serially in one context means:
1. The test output (potentially large) sits in context while linting runs
2. The linting output sits in context while security scan runs
3. Each subsequent task reasons through all prior task outputs as noise

Running them as parallel sub-agents means:
1. Each sub-agent has a clean 3-turn context: task description → tool execution → result
2. All three run simultaneously (wall-clock time is the longest, not the sum)
3. No cross-contamination between tasks

**When to use sub-agents:**

| Use sub-agents | Use single session |
|----------------|-------------------|
| Tasks are genuinely independent | Tasks depend on each other's results |
| Tasks are long (many tool calls each) | Tasks are short (2-3 tool calls) |
| You want parallel execution | Sequential order matters |
| Each task needs a focused context | Claude needs to reason across all results simultaneously |

**The pattern in a skill:**

```markdown
Spawn three parallel sub-agents for this quality audit:

**Agent 1:** Run `python -m pytest tests/ -q --tb=short`.
Report: pass/fail, number of failures, which test files failed.
Return a JSON object: {"tests": {"passed": N, "failed": N, "failures": [...]}}

**Agent 2:** Run `flake8 app/ --max-line-length=100 --statistics`.
Report: total violations, top 5 violation types, files with most violations.
Return: {"lint": {"total": N, "by_type": {...}, "worst_files": [...]}}

**Agent 3:** Run `bandit -r app/ -f json -ll`.
Report: HIGH and MEDIUM severity findings only.
Return: {"security": {"high": [...], "medium": [...]}}

Wait for all three. Combine their JSON results and produce a unified
quality report with a section for each, plus an overall recommendation
(green/yellow/red) based on the results.
```

**Token economy of sub-agents:**

Each sub-agent starts with a fresh context. For the three-agent quality audit above:
- Single session: test output + lint output + security output = potentially 20K tokens of accumulated context
- Sub-agents: each runs in ~3K tokens; orchestrator receives summaries
- Net saving: ~70% context reduction, plus wall-clock time is parallel rather than serial

This is the foundation of [Day 12](day-12-team-orchestration.md)'s orchestration patterns — sub-agents are not just a token optimization, they are an architectural pattern.

---

## Putting it together: the optimization checklist

Before starting any significant Claude Code session, run through this:

```
□ HYGIENE: Am I scoping this task to the smallest useful unit?
□ HYGIENE: Have I identified which files are actually needed?
□ HYGIENE: Should I start a new session (not continue yesterday's)?

During the session:
□ COMPACT: Am I at a natural task boundary where I should compact?
□ COMPACT: Is Claude starting to repeat itself or miss constraints?

After configuring CLAUDE.md or skills:
□ CACHING: Is my CLAUDE.md structured with stable content at the top?
□ CACHING: Are my skill instructions written once and not per-session?

Before starting a multi-part task:
□ SUB-AGENTS: Can this decompose into independent parallel subtasks?
□ SUB-AGENTS: Is the total context savings worth the orchestration overhead?
```

---

## Where it breaks / what it is not

**Over-hygiene: making Claude ask too many questions.** If you scope too narrowly, Claude will not have the context it needs and will ask clarifying questions or make wrong assumptions. There is a minimum viable context below which quality drops. The signal: Claude starts asking obvious questions or making assumptions about things it should know.

**Compaction quality loss.** Compaction summaries can miss nuances. Always explicitly state critical constraints before compacting, and verify that Claude still knows the key facts after compaction by asking "What are the constraints we established about X?"

**Cache invalidation surprises.** The 5-minute TTL means that if you are iterating on CLAUDE.md — editing it between sessions — you will get cache misses until the content stabilizes. Do not edit CLAUDE.md mid-session.

**Sub-agent overhead for small tasks.** Spawning a sub-agent has overhead: it starts a new session, runs the initialization, returns a result. For tasks that take 2 tool calls, this overhead exceeds the benefit. Sub-agents pay off when each subtask involves 5+ tool calls.

---

## Try it yourself

**Exercise 1 — Token audit (comprehension check)**

Open a recent Claude Code session transcript (or start a new one on any task). After 10 exchanges, answer:
1. What percentage of the content in context is actually relevant to the current state of the task?
2. Which entries are "dead weight" (completed subtasks, abandoned approaches, large file reads you are done with)?
3. At what point should you have compacted — and what would you have said explicitly before compacting?

---

**Exercise 2 — Structure CLAUDE.md for caching**

Take the TaskFlow `CLAUDE.md` you wrote in Day 3. Reorganize it so:
1. The most stable sections (Project Facts, Architecture, Core Conventions) are at the top
2. The least stable sections (Active Work, Current Sprint, Known Issues) are at the bottom
3. Add a comment marker at the "stability boundary" where content starts changing frequently

Estimate: given your current CLAUDE.md, how many tokens are above the stability boundary (cacheable prefix) vs. below it?

---

**Exercise 3 — Design a sub-agent task decomposition (stretch)**

Take the following request: "Prepare TaskFlow for the v2.1 release: ensure all tests pass, no critical lint violations, no security issues, and the changelog is up to date."

Decompose this into sub-agent tasks. For each sub-agent, specify:
- The exact task (what it does, what tools it uses)
- The output format (what it returns to the orchestrator)
- Whether it is truly independent of the other agents, or if there is a dependency

Identify any subtask that cannot be parallelized and explain why.

---

## Connect it back

[Days 1–6](day-01-how-claude-code-works.md) built the infrastructure — the four layers of how Claude is configured and controlled. Today you learned how to use that infrastructure economically. Every workflow day that follows applies these four levers automatically: tasks are scoped, sessions are fresh, CLAUDE.md is stable, and complex tasks decompose.

Tomorrow the case study begins in earnest. Day 8 applies everything from Days 1–7 to build the complete TaskFlow configuration — CLAUDE.md scaffold, settings, skills, hooks — as a single coherent system rather than individual pieces.

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
- *"Building Effective Agents"*, Anthropic Engineering Blog — the section "Parallelization" (covers sub-agent patterns) and "Long-context management." These are the canonical source for the sub-agent and compaction patterns. anthropic.com/engineering/building-effective-agents `[VERIFY URL]`

**If you want the deep version:**
- Claude Code Documentation, "Context management" — the authoritative reference for `/compact` behavior, auto-compaction thresholds, and what is preserved vs. discarded. Essential reading before Day 12's orchestration patterns.
- Claude Code Documentation, "Prompt caching" (or the Anthropic API docs on prompt caching) — the exact TTL, pricing, and eligibility rules for cache hits. Useful if you want to calculate the ROI of caching for your team's specific usage patterns.

---

← [Day 6b — MCP Server Setup: Installing and Building](day-06b-mcp-server-setup) &nbsp;|&nbsp; [Day 8 — Case Study Kickoff: TaskFlow Scaffold →](day-08-taskflow-scaffold)
