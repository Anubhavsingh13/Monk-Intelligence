# Glossary — Claude Code 101

> Alphabetical. Each entry: plain-English definition · formal definition · day of first use.
> Updated as each new term is introduced.

---

## A

**Agent (Sub-agent)**
*Plain English:* A separate Claude session that receives a focused task from an orchestrator, does its work in isolation, and returns a result.
*Formal:* A Claude Code process invoked programmatically by another Claude process (the orchestrator), with its own independent context window and tool permissions.
*Introduced:* Day 7

**Auto-activation**
*Plain English:* A skill that fires automatically when Claude detects you're doing the kind of task it covers — without you typing `/command`.
*Formal:* A skill trigger mechanism in which pattern-matching on the task description (or explicit tool-use events) causes Claude to load and execute the skill's instructions without an explicit slash-command invocation.
*Introduced:* Day 4

---

## B

**Behavior Gate**
*Plain English:* A checkpoint that intercepts Claude's actions and either lets them through, modifies them, or blocks them — deterministically, regardless of what Claude "wants" to do.
*Formal:* A hook-based mechanism using `PreToolUse` or `PostToolUse` events to apply shell-executable rules to Claude's tool invocations before or after execution.
*Introduced:* Day 5

---

## C

**CLAUDE.md**
*Plain English:* A Markdown file that Claude reads at the start of every session — your project's standing briefing document.
*Formal:* A configuration file loaded verbatim into Claude Code's context at session initialization. Supports a three-level hierarchy: `~/.claude/CLAUDE.md` (global), `.claude/CLAUDE.md` (project), `.claude/CLAUDE.local.md` (personal, untracked).
*Introduced:* Day 2

**Compaction**
*Plain English:* Summarizing a long conversation to free up context space while keeping the important facts.
*Formal:* The process by which Claude Code replaces a portion of the conversation history with a compressed summary, reducing token consumption while preserving task-relevant state. Triggered manually via `/compact` or automatically when context usage exceeds a threshold.
*Introduced:* Day 7

**Context window**
*Plain English:* The "working memory" Claude has during a session — everything it can see at once, measured in tokens.
*Formal:* The fixed-size buffer of tokens (input + output combined) that a Claude model can process in a single forward pass. All reasoning, tool calls, and responses occur within this window.
*Introduced:* Day 1

---

## H

**Hook**
*Plain English:* A shell command that Claude Code runs automatically at specific moments in its workflow — before a tool call, after a tool call, or when stopping.
*Formal:* A configured shell script invoked by the Claude Code runtime at defined event points (`PreToolUse`, `PostToolUse`, `Stop`). Exit code `0` = pass; exit code `2` = block with message; other non-zero = non-blocking error.
*Introduced:* Day 5

---

## I

**Isolated context**
*Plain English:* Each sub-agent gets its own blank-slate conversation — it cannot see what other sub-agents are doing or have done.
*Formal:* The property of sub-agent invocations whereby each spawned Claude process has an independent context window with no shared state with sibling agents; communication occurs only through the orchestrator's explicit message passing.
*Introduced:* Day 12

---

## M

**Map-Reduce (agent pattern)**
*Plain English:* Split a big task into independent parallel pieces (map), run each piece in its own sub-agent, then combine the results (reduce).
*Formal:* An orchestration pattern in which an orchestrator agent decomposes a task into N independent subtasks, spawns N sub-agents with isolated contexts, awaits their results, and synthesizes a unified output.
*Introduced:* Day 12

**MCP Server (Model Context Protocol Server)**
*Plain English:* A plugin that gives Claude access to a new external system — a database, an API, a browser — as if it were a built-in tool.
*Formal:* A process implementing the Model Context Protocol specification that exposes tools, resources, and/or prompts to Claude Code via a standardized JSON-RPC interface. Claude invokes MCP-provided tools identically to its built-in tools.
*Introduced:* Day 6

---

## O

**Orchestrator**
*Plain English:* The "manager" Claude agent that breaks a big task into pieces, delegates to sub-agents, and assembles the results.
*Formal:* A Claude Code process whose primary role is task decomposition, sub-agent invocation, and result synthesis — it typically performs no direct file editing itself.
*Introduced:* Day 12

---

## P

**Permission model**
*Plain English:* The rules that control what tools Claude is allowed to use freely, what it must ask permission for, and what is always forbidden.
*Formal:* The `permissions` block in `settings.json`, which defines `allow` and `deny` lists for tool invocations. Operates as an allowlist-first system: tools not explicitly allowed require user confirmation.
*Introduced:* Day 3

**PostToolUse hook**
*Plain English:* A hook that fires after Claude finishes using a tool — typically used for linting, logging, or quality checks.
*Formal:* A hook event triggered after a tool's execution completes, receiving the tool name, input parameters, and output in its event payload. Cannot undo the tool's action; used for reactive checks and side effects.
*Introduced:* Day 5

**PreToolUse hook**
*Plain English:* A hook that fires before Claude uses a tool — can inspect what Claude is about to do and block it if necessary.
*Formal:* A hook event triggered before a tool executes, receiving the tool name and input parameters. Exit code `2` blocks the tool call and returns the hook's stdout as an error message to Claude.
*Introduced:* Day 5

**Prompt caching**
*Plain English:* Reusing the computation for the parts of your prompt that don't change between calls — your `CLAUDE.md` contents, for example — so you pay full price once and a fraction thereafter.
*Formal:* An Anthropic API feature that caches the KV (key-value) computation for a prompt prefix for a 5-minute TTL. Cache hits cost ~10% of the standard input token price. Requires structuring prompts so stable content appears at the top.
*Introduced:* Day 7

---

## S

**Session**
*Plain English:* One continuous Claude Code conversation, from when you start it to when you close it.
*Formal:* A bounded interaction with the Claude Code runtime, initialized with a fresh context window (populated by `CLAUDE.md` and system prompts) and terminated by explicit exit or timeout. No state persists between sessions unless written to disk.
*Introduced:* Day 1

**settings.json**
*Plain English:* The configuration file that controls what Claude is and isn't allowed to do, and sets environment-level behaviors.
*Formal:* A JSON file at `.claude/settings.json` (project) or `~/.claude/settings.json` (global) that configures Claude Code's permission model, allowed tools, hook definitions, and environment variables.
*Introduced:* Day 3

**Skill (Slash Command)**
*Plain English:* A reusable workflow package stored as a Markdown file — invoke it with `/command-name` or let it auto-activate.
*Formal:* A Markdown file in `.claude/commands/` (project) or `~/.claude/commands/` (global) that defines a named, parameterized set of instructions Claude executes as a unit. Skills can reference external scripts, spawn sub-agents, and accept `$ARGUMENTS`.
*Introduced:* Day 4

**Stateless**
*Plain English:* Claude has no memory between sessions — every conversation starts from scratch unless you explicitly provide context.
*Formal:* The property of Claude Code sessions whereby no conversational state, variable values, or intermediate results persist after session termination. Persistence requires explicit mechanisms: `CLAUDE.md` for instructions, files on disk for artifacts, memory systems for facts.
*Introduced:* Day 1

---

## T

**Token**
*Plain English:* The basic unit of text that Claude processes — roughly ¾ of a word in English.
*Formal:* A subword unit produced by a byte-pair encoding (BPE) tokenizer. Approximately 1 token ≈ 4 characters ≈ 0.75 words in English. Input tokens and output tokens are priced separately; context window size is measured in tokens.
*Introduced:* Day 1

**Tool call**
*Plain English:* Claude asking the system to perform an action — read a file, run a command, edit code — and waiting for the result before continuing.
*Formal:* A structured request from the Claude model to the Claude Code runtime to execute a named capability (e.g., `Read`, `Edit`, `Bash`) with specified parameters. The result is returned to the model as a new context entry.
*Introduced:* Day 1

---

*New terms are added as they first appear. If a term is used in a day page before this entry exists, that is a bug — please report it.*
