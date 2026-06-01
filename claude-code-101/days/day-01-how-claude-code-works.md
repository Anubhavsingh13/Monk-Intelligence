*[Claude Code 101](../../README.md) · Day 1 of 14*

# Day 1 — How Claude Code Actually Works

> **Today's one idea:** Claude Code is a context window with tools attached — it is stateless by default, and every session starts blank.
> **Reading time:** ~60 min · **Prereqs:** None
> **Primary source for today:** [Claude Code Official Documentation](https://docs.anthropic.com/en/docs/claude-code) — "Overview" section

---

## The hook

Imagine you hire a brilliant contractor. Exceptional code, fast turnaround, great instincts. There's just one thing: every morning they show up with no memory of yesterday. They don't know your project, your codebase, your conventions, or even that they've worked with you before. You have to brief them from scratch at the start of every single day.

Now imagine you hand them a short briefing document each morning — five pages covering the architecture, the conventions, the landmines to avoid — and suddenly the quality is consistent and you barely have to repeat yourself.

That is Claude Code. The brilliant-but-amnesiac contractor is not a metaphor — it is a precise description of how the system works. And the briefing document? That is what the rest of this course is about.

Before you can write good briefing documents, you need to understand exactly *why* the contractor forgets, *what* they can actually perceive, and *how* they interact with the world. That is today's job.

---

## Building the intuition

### The whiteboard that gets erased

Picture a whiteboard. At the start of a session, it is blank. As you and Claude exchange messages, both sides of the conversation get written on the whiteboard — your messages, Claude's responses, the results of tool calls. Claude "sees" everything on the whiteboard when generating each response.

The whiteboard has a fixed size (the *context window*). When the session ends, the whiteboard is erased completely. There is no drawer, no photograph, no backup. The next session starts with a clean whiteboard.

This explains a lot of behavior that confuses new users:
- Why Claude doesn't remember the refactoring you did last week (that was a different whiteboard).
- Why Claude is consistent *within* a long session but inconsistent *across* sessions (it's reading the same whiteboard within, different ones across).
- Why adding "remember this for next time" achieves nothing (there is no "next time" — there is only a new blank whiteboard).

### Claude's hands: tools

Claude cannot do anything by itself. It cannot read a file, run a command, or edit code directly. Everything it does to the world happens through **tools** — discrete capabilities the runtime grants it.

Think of tools as hands with very specific shapes. Each tool is good for exactly one kind of action:

| Tool | What it does |
|------|-------------|
| `Read` | Reads a file at a path |
| `Edit` | Makes a targeted string replacement in a file |
| `Write` | Creates or overwrites a file |
| `Bash` | Runs a shell command and returns output |
| `Glob` | Finds files matching a pattern |
| `Grep` | Searches file contents |
| `TodoWrite` | Manages a task list |
| `Agent` | Spawns a sub-agent |

When Claude wants to read `app/main.py`, it doesn't just "look at it" — it makes a formal `Read` tool call, the runtime reads the file, and the content appears on the whiteboard. Then Claude can reason about it.

This matters because it means:
1. Claude can only "see" what it has explicitly asked to see. A 500-file repo is invisible until Claude reads the files.
2. Every tool call costs tokens — the result gets added to the whiteboard.
3. You can control exactly which tools Claude has access to (more on this in [Day 3's settings.json](day-03-writing-claude-md-and-settings.md)).

### The REPL loop

Claude Code operates in a loop that will feel familiar if you have ever used a REPL (Read-Eval-Print Loop):

```
1. READ:   Claude receives your message + everything on the whiteboard
2. REASON: Claude generates a response (which may include tool calls)
3. EXECUTE: The runtime runs tool calls, appends results to the whiteboard
4. LOOP:   Back to step 1, with the whiteboard now containing the tool results
```

This loop continues until Claude produces a final text response with no more tool calls, or until you interrupt it.

Here is what a simple session looks like on the whiteboard:

```
[USER]:  What's in app/main.py?

[CLAUDE]: I'll read that file for you.
          <tool call: Read("app/main.py")>

[TOOL RESULT]: from fastapi import FastAPI
               app = FastAPI()
               ...

[CLAUDE]: The file contains a FastAPI application setup. The main
          module imports FastAPI, creates an app instance, and...
```

Every arrow in that sequence is tokens on the whiteboard.

### What "stateless" actually means in practice

Stateless doesn't mean Claude is dumb. It means Claude's knowledge of *your project* resets each session. Its general knowledge — how to write Python, what FastAPI is, best practices for REST APIs — that is baked into the model. Stateless only means: everything *specific to your work* must be provided in context, every session.

This is the fundamental tension this course resolves. You have valuable, project-specific knowledge. Claude has general capability. The configuration files — `CLAUDE.md`, settings, skills, hooks — are the mechanisms for injecting your project knowledge into Claude's context reliably, every session, without you having to repeat it manually.

---

## The formal picture

### Session lifecycle

A Claude Code session has five phases:

```
┌─────────────────────────────────────────────────────────┐
│  1. INITIALIZATION                                       │
│     • Runtime starts                                     │
│     • CLAUDE.md files loaded → written to context       │
│     • System prompts injected                           │
│     • Tool permissions applied from settings.json       │
├─────────────────────────────────────────────────────────┤
│  2. INTERACTION                                          │
│     • User message received                             │
│     • Claude reasons, emits text + optional tool calls  │
│     • Tool calls executed, results appended to context  │
│     • Repeat until no pending tool calls               │
├─────────────────────────────────────────────────────────┤
│  3. TOOL EXECUTION                                       │
│     • Each tool call: validated → hooked → executed     │
│     • PreToolUse hooks run before execution             │
│     • PostToolUse hooks run after                       │
│     • Result appended to whiteboard                     │
├─────────────────────────────────────────────────────────┤
│  4. CONTEXT MANAGEMENT                                   │
│     • Whiteboard fills as session progresses            │
│     • Auto-compaction triggers near limit               │
│     • Manual /compact available                         │
├─────────────────────────────────────────────────────────┤
│  5. TERMINATION                                          │
│     • Session ends (exit, timeout, or /quit)            │
│     • Whiteboard erased                                 │
│     • No state persists unless written to disk          │
└─────────────────────────────────────────────────────────┘
```

### What lives in the context window

At any point in a session, the context window contains (in rough order):

1. **System prompt** — injected by the Claude Code runtime; sets Claude's identity and baseline instructions.
2. **CLAUDE.md content** — your project briefing, loaded verbatim at startup.
3. **Conversation history** — all user messages, Claude responses, and tool results from this session.
4. **In-flight tool call** — the current tool invocation and its result (briefly, before it becomes history).

You do not control the system prompt. You control items 2 and 3. This course is largely about maximizing the value of item 2.

### The token economy (preview)

Tokens are the unit of measurement for everything in the context window. As of 2025, Claude's context window is 200,000 tokens — large, but not infinite.

A rough sense of scale:
- 1 token ≈ ¾ of an English word
- 200,000 tokens ≈ a short novel
- A typical CLAUDE.md: 500–2,000 tokens
- A medium Python file: 500–1,500 tokens
- A long conversation with many tool calls: 20,000–80,000 tokens

You will learn to budget this deliberately in [Day 7](day-07-token-economy.md). For now, the important insight is: every byte of context is budget you spend. If you spend it on things Claude doesn't need, you have less room for things it does.

### What persists between sessions (exactly three things)

1. **Files on disk** — anything Claude writes or edits persists because it is on the filesystem, not in the context.
2. **CLAUDE.md** — loaded fresh at every session start; the closest thing to persistent memory Claude has about your project.
3. **The memory system** — if configured, Claude can write facts to memory files that are loaded into future sessions (see [Day 2](day-02-claude-md-the-10-percent.md)).

Nothing else persists. Not the conversation. Not what Claude "decided" to do. Not the task list. Not the intermediate reasoning.

---

## Where it breaks / what it is not

**"But it remembered my name!"**
The session was still open. Within a session, the whiteboard accumulates — Claude absolutely remembers everything from earlier in the *same* session. The amnesia is between sessions, not within them.

**"CLAUDE.md makes it stateful."**
Not quite. CLAUDE.md makes the *blank slate arrive pre-filled*. It is static content loaded at startup, not dynamic memory that updates as Claude works. If Claude learns something important mid-session, that knowledge evaporates when the session ends unless you update `CLAUDE.md` manually (or have an automated mechanism to do so).

**"It has access to the whole codebase."**
Only what it reads. Claude does not scan your repository at startup. It sees only what it explicitly requests via `Read`, `Glob`, or `Grep` tool calls. A common failure mode: asking Claude about a file it has never read. It will reason from its general knowledge and produce plausible-sounding but wrong answers.

**"More context is always better."**
False. Filling the context with irrelevant files and long conversations degrades Claude's reasoning quality and increases cost. The whiteboard analogy is apt: a cluttered whiteboard is harder to read from than a focused one.

**"The context window is 200K, so I never need to worry about it."**
200K tokens runs out faster than you expect when you have long sessions with many file reads. You will encounter the limit — [Day 7](day-07-token-economy.md) teaches you to manage it before it manages you.

---

## Try it yourself

**Exercise 1 — The blank slate (comprehension check)**

Open a new Claude Code session in any directory. Type exactly:

```
What project are we working on?
```

Claude will not know. It will either say so directly, or make an inference from the directory name. This is the blank slate in action. Notice that it has no information you haven't provided.

Now type:

```
We are working on a task management API called TaskFlow.
It is a Python FastAPI backend with a TypeScript React frontend.
```

Then close and reopen the session. Ask again. Claude will not know again. You just experienced the session boundary.

---

**Exercise 2 — Watch the REPL loop**

In a Claude Code session, ask:

```
How many Python files are in the current directory tree?
```

Watch what Claude does. It will call `Glob` or `Bash` to find the files, the results will appear in the tool output, and then Claude will count them. You just watched the Read-Execute-Reason loop in action. Each step is a separate entry on the whiteboard.

---

**Exercise 3 — Map what's in context (stretch)**

Ask Claude:

```
What do you currently know about this project? Be explicit about
what you have actually read vs. what you are inferring from general
knowledge.
```

This is a useful diagnostic habit. Claude will tell you exactly what is on the whiteboard regarding your project — usually: nothing, unless you told it something this session. Save this question. You will use it in Day 2 to compare what Claude knows before and after a `CLAUDE.md`.

---

## Connect it back

There is no "yesterday" for this exercise — today is Day 1. But here is the sharp question to carry forward:

*If Claude starts every session blank, and you cannot be there to brief it every morning, what mechanism ensures it arrives pre-informed and ready to work on your specific project?*

Tomorrow's page answers that question. Today you learned *why* the question matters.

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
- Claude Code Official Documentation, "Overview" section — docs.anthropic.com/en/docs/claude-code/overview `[VERIFY URL]`. Read the overview and the "How Claude Code works" subsection. It will confirm and extend the session lifecycle picture above. Pay attention to the tool list — this is the definitive set of what Claude can do.

**If you want the deep version:**
- Claude Code Official Documentation, "Core concepts" — the full section on context management gives you the authoritative description of what enters the context window and in what order. Directly relevant to [Day 7](day-07-token-economy.md)'s optimization work.
- *"Building Effective Agents"*, Anthropic Engineering Blog — the section "The augmented LLM" (first few paragraphs) describes the tool-use architecture in the terms used by the engineers who built it. Worth reading to understand why the REPL loop is designed the way it is. See [bibliography.md](../bibliography.md) for the full citation.

---

← [Day 0 — Installation + Project Setup](day-00-installation-project-setup.md) &nbsp;|&nbsp; [Day 2 — CLAUDE.md: The 10% That Changes Everything →](day-02-claude-md-the-10-percent.md)
