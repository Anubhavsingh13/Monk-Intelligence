# Day 4 — Reading an Agent Trace

> **Today's one idea:** The execution trace is ground truth — every pattern, every failure, and every design decision leaves a visible fingerprint in the trace.
> **Reading time:** ~35 min · **Prereqs:** Day 3
> **Primary source for today:** Yao et al., *ReAct: Synergizing Reasoning and Acting in Language Models* (ICLR 2023, arXiv:2210.03629) — Appendix (example traces).

---

## The hook

Imagine you hire a consultant to solve a complex problem. At the end of the week, they hand you a report: "Problem solved. Trust me." No reasoning shown, no intermediate steps, no record of what they tried and discarded.

You'd be uncomfortable. You can't evaluate the quality of the solution. You can't tell if they got lucky. You can't learn from their approach. And if the solution is wrong, you have no idea where it went wrong.

Now imagine the same consultant sends you a detailed journal: every step, every question they asked, every dead end, every "I called the legal department and got back this answer, so I then decided to...". Suddenly you can evaluate, learn, and debug.

An agent trace is that journal. It is the full record of what the agent did, in order, with all the reasoning visible. Reading a trace well is the core diagnostic skill of agent engineering — more useful than understanding any single pattern, because the trace tells you *which patterns are present, which are absent, and which are breaking*.

---

## Building the intuition

### The three elements of a trace

Every meaningful trace event is one of three things:

```
THOUGHT      →  The agent's internal reasoning, in natural language.
               "I need to find the population of France to answer this."

ACTION       →  A tool call, with name and arguments.
               web_search(query="France population 2024")

OBSERVATION  →  What the tool returned.
               "France's population in 2024 is approximately 68.4 million."
```

A complete trace is a sequence of these three elements, ending in a final answer. The pattern that formalizes this — interleaving thoughts, actions, and observations — is ReAct (Day 8). But you don't need the pattern name to read the structure. The structure is right here.

### Annotating a real trace

Let's walk through a complete agent trace for the question:

> *"Who won the 2022 FIFA World Cup, and what is that country's capital city?"*

This is a multi-hop question: you need to find the answer to Part 1 before you can answer Part 2.

---

```
[THOUGHT 1]
I need to find who won the 2022 FIFA World Cup first.
Let me search for that.
                        ↑ The agent identifies what it needs before acting.
                          This is the reasoning component working.

[ACTION 1]
web_search(query="2022 FIFA World Cup winner")
                        ↑ Precise query. Notice it's not "football world cup
                          2022 winner country" — the agent chose a clean query.

[OBSERVATION 1]
"Argentina won the 2022 FIFA World Cup, defeating France
 4-2 on penalties after a 3-3 draw in the final."
                        ↑ The tool returns raw information. The agent
                          hasn't processed this yet.

[THOUGHT 2]
Argentina won the 2022 World Cup. Now I need to find
the capital city of Argentina.
                        ↑ The agent correctly integrates the observation
                          into its reasoning and identifies the next step.

[ACTION 2]
web_search(query="capital city of Argentina")
                        ↑ Second tool call. Note: the agent could have
                          answered this from training data (Buenos Aires
                          is well-known), but a well-designed agent doesn't
                          mix retrieved and memorized facts without signaling it.

[OBSERVATION 2]
"The capital city of Argentina is Buenos Aires."

[THOUGHT 3]
I have both pieces of information. I can now answer
the original question.
                        ↑ The agent recognizes the task is complete.
                          This "termination condition" is often implicit.

[FINAL ANSWER]
Argentina won the 2022 FIFA World Cup. The capital city
of Argentina is Buenos Aires.
```

---

### What the trace tells you

Now that you've read the trace, here's what you can diagnose:

**1. Is the reasoning grounded?**
Look at Thought 1 → Action 1. Does the action directly follow from the thought? Here, yes — the agent said it would search, then searched. In a broken agent, you often see Action 1 that has nothing to do with Thought 1.

**2. Is the agent integrating observations?**
Look at Observation 1 → Thought 2. The agent correctly extracts the key fact ("Argentina won") and uses it to identify the next step. A hallucinating agent might start Thought 2 with something inconsistent with what Observation 1 said.

**3. Is the agent terminating correctly?**
Thought 3 identifies that the task is complete. Agents that don't have a clear termination condition will keep searching even after they have the answer — a very common failure.

**4. Are tool calls sensible?**
Action 2 could have been answered from training data. The agent chose to search anyway — a safe choice (avoids mixing retrieved and memorized facts) but also a more expensive one. This is a design choice, not a bug. But it's visible in the trace.

---

## The formal picture

### Trace anatomy as a formal structure

Let $\tau$ be a trace. It is a sequence of events:

$$\tau = (e_1, e_2, \ldots, e_n, a_{final})$$

where each $e_i$ is one of:
- $T_i$: a Thought (natural language string)
- $A_i = (tool\_name, args)$: an Action
- $O_i$: an Observation (tool return value)

and they interleave in the pattern: $T_1, A_1, O_1, T_2, A_2, O_2, \ldots, T_n, A_{final}$

**Crucially:** in the ReAct framework, every element of $\tau$ is appended to the LLM's context window as it is generated. The model at step $i$ can see all of $T_1, A_1, O_1, \ldots, T_{i-1}, A_{i-1}, O_{i-1}$ — but nothing beyond that. The trace IS the context.

This means:
- Longer traces → more context used → approaching context limit
- A bad early observation can poison all subsequent thoughts
- Truncated observations (cut off mid-sentence) confuse the reasoning

### Common trace "smells" — pattern failures made visible

| Smell | What you see in the trace | Likely cause |
|-------|--------------------------|-------------|
| Repetitive action loop | $A_i = A_{i+1} = A_{i+2}$ | No progress signal; agent stuck |
| Observation ignored | $O_i$ contradicts $T_{i+1}$ | Context growing too long; early tokens losing attention |
| Premature termination | Final answer before enough info | Termination prompt too aggressive |
| Hallucinated observation | $O_i$ contains information not in tool output | Model generated its own "observation" |
| Action-thought drift | $T_i$ says "search for X"; $A_i$ searches for Y | Instruction following breaking down |

The last one — hallucinated observation — is the most dangerous and hardest to catch without reading traces. The model, when faced with a tool call failure it didn't handle, sometimes generates what the observation "should" have been and continues confidently. You won't see this in the final answer. You'll only see it in the trace.

---

## Where it breaks / what it is not

**Traces are not explanations of reasoning.** The thoughts in a trace are what the model *chose to write*, not a transparent readout of its internal computation. A model can write a coherent thought chain and still be wrong. The thoughts are input to future steps — they are signals, not ground truth.

**Long traces lose coherence.** As the trace grows, early content gets less attention due to the "lost in the middle" problem (Liu et al., 2023): LLMs attend most strongly to the beginning and end of long contexts. By step 15, the original instruction is competing with 14 rounds of content. This is why the memory patterns (Module 5) exist.

**Traces can be spoofed.** If you're evaluating an agent by reading its traces, be aware: a sufficiently capable model can produce traces that look reasonable while the underlying behavior is wrong. Traces are necessary but not sufficient for evaluation. You also need to verify the final answer against the actual environment state.

---

## Try it yourself

**Exercise 1 — Check your understanding:**
Look at the trace above. Identify the exact point where "hallucination" would most easily occur if Observation 1 had returned an empty string instead of a real result.

**Exercise 2 — Apply it:**
Run an agent (LangChain, AutoGen, Claude with tools, or the code from Day 3) on a multi-hop question. Capture the full trace. Annotate each event as T (thought), A (action), or O (observation). Then answer: did the agent terminate correctly? Was any observation ignored?

**Exercise 3 — Stretch:**
Design a two-event trace where the agent takes a *wrong* action but produces a *correct* final answer by coincidence. What does this trace look like? Why is it dangerous?

<details>
<summary>Hint for Exercise 1</summary>
If Observation 1 is empty, the agent has no grounded information about who won. What would Thought 2 contain? Would it be consistent with Observation 1?
</details>

<details>
<summary>Worked solution for Exercise 1</summary>
If Observation 1 returned an empty string:

```
[THOUGHT 2]
Based on my search, it appears Argentina won the 2022
FIFA World Cup. Their capital city is Buenos Aires.
```

The agent would likely hallucinate Observation 1's content based on training data knowledge, producing an answer that happens to be correct but is not grounded. This is extremely dangerous: the hallucination is invisible unless you inspect the trace, and for less well-known facts it would produce confident incorrect answers. The pattern that prevents this: always validate non-empty observations before proceeding, and log empty observations explicitly as failures.
</details>

---

## Connect it back

[Day 3](./day-03-tool-use-primitive.md) gave you the mechanics of tool calls. Today you saw those mechanics in action — in a trace — and learned to read what's there. The three elements (Thought, Action, Observation) will appear in every pattern we study from Day 6 onward. When we say "ReAct uses a think-act-observe loop," you now know exactly what that means: it's the structure you just read.

[Tomorrow](./day-05-rest-synthesize-i.md) is your first Rest & Synthesize day. No new material — but you will rebuild the cognitive map from scratch, and you will annotate a fresh trace. By the end of Day 5, the first four days should feel like one coherent model, not four separate topics.

**One question you can now answer that you couldn't this morning:** How would you diagnose an agent that "keeps looping" without access to its code — only its trace?

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
Yao et al., *ReAct* (arXiv:2210.03629) — Appendix B (example traces, 3–4 pages).
These are the original traces from the ReAct paper — real agent runs on HotpotQA and FEVER. Read two of them annotating T/A/O as you go. You'll recognize exactly the structure from today's page.

**If you want the deep version:**
- LangSmith (LangChain's trace viewer) — https://smith.langchain.com — Free tier available. Run any LangChain agent and examine its trace in the UI. The visual diff between traces is immediately instructive.
- Yao et al., *ReAct* (arXiv:2210.03629) — Section 4 (analysis), particularly Section 4.2 "Error analysis." It catalogs the exact failure modes we called "trace smells" above, with real examples. ~3 pages, very concrete.

---

## Navigation

← **Previous:** [Day 3 — Tool Use as First-Class Primitive](./day-03-tool-use-primitive.md)
→ **Next:** [Day 5 — Rest & Synthesize I](./day-05-rest-synthesize-i.md)
