# Day 5 — Rest & Synthesize I

> **Today's one idea:** *(No new material. Today you consolidate Days 1–4.)*
> **Reading time:** ~30 min of active recall · **Prereqs:** Days 1–4
> **Primary source for today:** Your own notes from Days 1–4.

---

## What this day is for

Learning is not reading. Learning is the process of integrating new information into durable memory. That integration happens best when you *retrieve and reconstruct* — not when you re-read.

Today you will not read new material. You will rebuild what you know from scratch. This feels harder than reading. It is supposed to. The difficulty is the learning.

If you find yourself reaching for the previous pages to check an answer: good. Notice that impulse. Check the answer. Then close the page and try again from memory.

---

## Synthesis exercises

Work through these in order. Allow yourself roughly 5–7 minutes per exercise.

---

### Exercise 1 — Rebuild the cognitive map

**Without opening Day 2**, draw the four-component cognitive architecture on paper or a blank document. For each component, write:
- Its name
- One-sentence description
- One example of something an agent might use for it
- The OS analogy (from Karpathy's model)

Only look at [Day 2](./day-02-cognitive-architecture-map.md) after you've written your answers.

<details>
<summary>Check your answers</summary>

| Component | Description | Example | OS Analogy |
|-----------|-------------|---------|------------|
| Action space | What the agent is allowed to do | `web_search()`, `run_python()` | I/O peripherals / system calls |
| Memory | What information it can access beyond weights | Context window, vector DB, file system | RAM (context), Disk (external storage) |
| Planning | How it sequences actions toward a goal | CoT, ReAct loop, tree search | CPU scheduler / task queue |
| Learning | How it improves across episodes | Reflexion verbal lessons, STaR fine-tuning | OS update / driver update |

</details>

---

### Exercise 2 — Map a pattern to its component

Pick any two patterns you've encountered in your own work (LangChain agent, AutoGen, Claude tools, etc.) — doesn't matter if you don't have a formal name for them. For each one, identify:

1. Which cognitive architecture component is this pattern primarily addressing?
2. What is the **force** (the tension) that makes the pattern necessary?
3. What would break if you removed the pattern from the system?

Write your answers before checking anything.

---

### Exercise 3 — Annotate a trace from scratch

Below is a raw agent run. The T/A/O labels have been removed. Label each line and identify any "trace smells" from [Day 4](./day-04-reading-agent-trace.md).

```
Line 1:  I should find the current CEO of OpenAI.

Line 2:  web_search(query="OpenAI CEO 2024")

Line 3:  "Sam Altman is the CEO of OpenAI as of 2024."

Line 4:  Sam Altman is the CEO. Now I need to know his educational background.

Line 5:  web_search(query="Sam Altman education background")

Line 6:  "Sam Altman attended Stanford University, studying computer science,
          but left after his freshman year."

Line 7:  Sam Altman attended Stanford but did not complete his degree. 
         He left after one year.

Line 8:  The CEO of OpenAI is Sam Altman. He studied computer science at 
         Stanford University but left after his freshman year without completing 
         his degree.
```

<details>
<summary>Answers</summary>

```
Line 1:  [THOUGHT 1] — reasoning about what to do next
Line 2:  [ACTION 1]  — tool call
Line 3:  [OBSERVATION 1] — tool result
Line 4:  [THOUGHT 2] — integrates observation, identifies next step
Line 5:  [ACTION 2]  — second tool call
Line 6:  [OBSERVATION 2] — tool result
Line 7:  [THOUGHT 3] — integrates observation, recognizes task is complete
Line 8:  [FINAL ANSWER] — synthesizes both retrieved facts
```

**Trace smells?** None here — this is a clean trace. The agent:
- Correctly integrates each observation into the next thought
- Takes exactly as many steps as needed (no repetition, no premature termination)
- Produces a final answer grounded in both observations

This is what a healthy trace looks like. In practice, you'll see Line 7 be a slightly different summary of Line 6 — which is fine. What would be a smell: Line 7 adding a fact that wasn't in Line 6.
</details>

---

### Exercise 4 — Design the action space

You're building an agent for a data analyst: it should be able to answer questions about a company's sales data, generate charts, and send summaries to Slack.

Design its action space:
1. List every tool it needs (use the five-category taxonomy from Day 2/3)
2. For each tool, write a one-sentence description
3. Identify the two highest-risk tools and explain why

Don't look at any previous page — this is pure recall + application.

---

### Exercise 5 — Write the definition

In your own words, write a one-paragraph answer to: *"What is an agentic design pattern, and why does naming one matter?"*

Then read it back as if you were a smart colleague who hadn't read this course. Is it clear? Is it precise? Does it include the three-part structure (context, force, solution)?

---

## What you should be able to do by the end of today

After completing these exercises, you should be able to:

- [ ] Draw the four-component cognitive map from memory
- [ ] Locate any agent component on the LLM-as-OS analogy
- [ ] Label any agent trace as a sequence of T/A/O events
- [ ] Identify at least three "trace smells" by name
- [ ] State what a design pattern is, including its three-part structure
- [ ] Explain why tool use is a "primitive" rather than a feature

If any of these feel shaky, go back to the relevant day and re-read only the section that's unclear. Don't re-read the whole page.

---

## Looking ahead

[Module 2](../../02-reasoning-patterns/overview.md) starts tomorrow with **Chain-of-Thought** — the foundational reasoning pattern. Everything in that module is about the Planning component of the cognitive architecture. After today, you have the vocabulary to understand why each reasoning pattern exists and what force it resolves.

The first pattern you'll study (CoT) doesn't require a tool call at all — it's purely in-context reasoning. The second (Self-Consistency) doesn't even require a prompt change — just a different decoding strategy. Then ReAct, which fuses everything from Module 1 with the reasoning patterns you'll learn in Days 6–7.

---

## Navigation

← **Previous:** [Day 4 — Reading an Agent Trace](./day-04-reading-agent-trace.md)
→ **Next:** [Day 6 — Chain-of-Thought](../../02-reasoning-patterns/days/day-06-chain-of-thought.md)
