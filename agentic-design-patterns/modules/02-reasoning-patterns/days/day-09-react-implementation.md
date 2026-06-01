# Day 9 — Implementing ReAct from Scratch

> **Today's one idea:** The ReAct pattern reduces to a single while-loop: generate a thought, check if done, call a tool, feed the observation back — and the implementation reveals exactly where the pattern is fragile.
> **Reading time:** ~45 min (code-heavy) · **Prereqs:** Day 8 (ReAct concept), Day 3 (tool use mechanics)
> **Primary source for today:** Yao et al., *ReAct* (ICLR 2023, arXiv:2210.03629) — Appendix A (implementation details).

---

## The hook

Every design pattern has a moment of "oh, that's all it is." For the Factory pattern, it's when you see it's just a function that returns different object types based on input. For the Observer pattern, it's when you see it's just a list of callbacks.

ReAct's moment: it's a while-loop with an if-statement. The entire pattern — everything from Day 8 — compiles down to:

```python
while not done:
    thought = model.generate(context)
    if model.wants_tool(thought):
        observation = tool.execute(thought.action)
        context.append(thought, observation)
    else:
        done = True
        answer = thought.text
```

Everything else is engineering: how you format the context, how you extract the tool call, how you handle errors, how you enforce the step limit. Today you build the real version of this loop and run it on a live question.

---

## Building the intuition

### What makes a ReAct implementation non-trivial

The loop above is conceptually complete, but four things make the real implementation harder:

1. **Context format.** The model needs to see all prior thoughts and observations in a structured way. Modern APIs (Anthropic's tool-use protocol) handle this for you — but you need to understand the message format.

2. **Termination detection.** The model signals "I'm done" by not requesting a tool (stop_reason = "end_turn"). But you also need a hard step limit to prevent infinite loops.

3. **Multi-tool responses.** The model may request multiple tool calls in a single turn. You need to execute all of them before continuing.

4. **Error propagation.** When a tool fails, the error must be fed back as an observation — not silently swallowed. Silent failure causes hallucinated continuation.

Today's implementation handles all four.

---

## The full implementation

```python
"""
react_agent.py — A complete ReAct implementation in ~80 lines of logic.

Run: python react_agent.py
Requires: pip install anthropic
"""

import json
import anthropic

client = anthropic.Anthropic()

# ── 1. Tool definitions ────────────────────────────────────────────────────────
# This is the contract the model sees. Description quality directly affects
# how well the model decides when and how to use each tool.

TOOLS = [
    {
        "name": "web_search",
        "description": (
            "Search the web for factual information. Use this when you need "
            "current facts, recent events, or information you are not certain about. "
            "Prefer specific queries over general ones."
        ),
        "input_schema": {
            "type": "object",
            "properties": {
                "query": {
                    "type": "string",
                    "description": "A specific search query. E.g.: 'France population 2024'"
                }
            },
            "required": ["query"]
        }
    },
    {
        "name": "calculator",
        "description": (
            "Evaluate a Python arithmetic expression and return the numeric result. "
            "Always use this for arithmetic — never compute mentally. "
            "E.g.: '84_000_000 / 27' or '2026 - 1889'"
        ),
        "input_schema": {
            "type": "object",
            "properties": {
                "expression": {
                    "type": "string",
                    "description": "A valid Python arithmetic expression using +, -, *, /, ** and parentheses."
                }
            },
            "required": ["expression"]
        }
    }
]

SYSTEM_PROMPT = (
    "You are a precise, grounded assistant. "
    "For each question, think step by step about what information you need, "
    "use tools to retrieve or compute it, and only answer when you have verified facts. "
    "Never guess at numbers — always use the calculator tool for arithmetic."
)

# ── 2. Tool executor ───────────────────────────────────────────────────────────
# Replace the web_search stub with a real search API (Brave, Serper, Tavily, etc.)

def execute_tool(name: str, inputs: dict) -> str:
    """Execute a tool call and return the result as a string."""
    if name == "calculator":
        try:
            # Safe for arithmetic — restrict to numeric ops only
            allowed = set("0123456789+-*/.() _e")
            if not all(c in allowed for c in inputs["expression"].replace("**", "^^")):
                return "Error: expression contains disallowed characters."
            result = eval(inputs["expression"])
            return str(result)
        except Exception as e:
            return f"Error evaluating expression: {e}"

    elif name == "web_search":
        # ── STUB: replace with real search in production ──
        # Example integrations:
        #   Tavily: pip install tavily-python
        #   Brave:  requests.get("https://api.search.brave.com/res/v1/web/search", ...)
        #   Serper: requests.post("https://google.serper.dev/search", ...)
        return (
            f"[Search stub: '{inputs['query']}' — "
            "replace execute_tool() with a real search API to get live results.]"
        )

    return f"Error: unknown tool '{name}'"

# ── 3. The ReAct loop ──────────────────────────────────────────────────────────

def react_agent(
    question: str,
    max_steps: int = 10,
    verbose: bool = True
) -> str:
    """
    A minimal, complete ReAct agent.

    Args:
        question:  The user's question.
        max_steps: Hard cap on think-act-observe cycles (prevents infinite loops).
        verbose:   Print each thought, action, and observation as it happens.

    Returns:
        The agent's final answer as a string.
    """
    messages = [{"role": "user", "content": question}]

    for step in range(1, max_steps + 1):
        if verbose:
            print(f"\n── Step {step} ──────────────────────────")

        # ── Generate: model produces the next thought (and possibly a tool call) ──
        response = client.messages.create(
            model="claude-3-5-sonnet-20241022",
            max_tokens=1024,
            system=SYSTEM_PROMPT,
            tools=TOOLS,
            messages=messages
        )

        # ── Branch A: model wants to call tools ──
        if response.stop_reason == "tool_use":
            tool_results = []

            for block in response.content:
                if block.type == "text" and block.text.strip():
                    if verbose:
                        print(f"THOUGHT: {block.text.strip()}")

                elif block.type == "tool_use":
                    if verbose:
                        print(f"ACTION:  {block.name}({json.dumps(block.input, ensure_ascii=False)})")

                    # Execute and collect result
                    observation = execute_tool(block.name, block.input)

                    if verbose:
                        print(f"OBS:     {observation}")

                    tool_results.append({
                        "type": "tool_result",
                        "tool_use_id": block.id,
                        "content": observation
                    })

            # Append the assistant's turn and all tool results to the conversation
            messages.append({"role": "assistant", "content": response.content})
            messages.append({"role": "user",       "content": tool_results})

        # ── Branch B: model is done (no tool call) ──
        else:
            final_answer = next(
                (block.text for block in response.content if hasattr(block, "text")),
                "No answer generated."
            )
            if verbose:
                print(f"\nANSWER: {final_answer}")
            return final_answer

    # Hard limit reached
    warning = f"[Warning: reached {max_steps}-step limit without a final answer.]"
    if verbose:
        print(warning)
    return warning


# ── 4. Run it ──────────────────────────────────────────────────────────────────

if __name__ == "__main__":
    questions = [
        # Single-hop (tests basic tool use)
        "What is 17% of 4820?",

        # Multi-hop (tests chained reasoning)
        "What is the population of France divided by the number of EU member states?",

        # Adaptive (tests that the model uses its observation to decide next step)
        "What is the square root of the number of days in a non-leap year?",
    ]

    for q in questions:
        print(f"\n{'='*60}")
        print(f"QUESTION: {q}")
        print('='*60)
        react_agent(q, verbose=True)
```

### Running it and what to look for

When you run `python react_agent.py`, watch for these things:

**1. Does the thought match the action?**
```
THOUGHT: I need to calculate 17% of 4820. Let me use the calculator.
ACTION:  calculator({"expression": "0.17 * 4820"})
```
Thought → Action alignment is the sign of a healthy trace. Misalignment ("I should search for this" → `calculator(...)`) is a prompt or tool description problem.

**2. Does the observation feed the next thought?**
```
OBS:     819.4
THOUGHT: 17% of 4820 is 819.4. I now have my answer.
ANSWER:  17% of 4820 is 819.4.
```
The observation directly caused the termination. Good.

**3. Does it terminate correctly?**
The model should stop as soon as it has the answer, not keep searching "to be sure." If it keeps going after having the answer, your system prompt's termination instruction is too weak.

---

## Dissecting the implementation

### Why `max_steps` matters

Without `max_steps`, a malfunctioning agent loops forever. The most common infinite loop: the tool returns an error or empty result → the model decides to retry with the same query → same error → retry → ...

A step limit is not a workaround — it's a required architectural element of any production ReAct agent. The right limit depends on task complexity: simple Q&A needs 3–5 steps; complex research tasks may need 15–20.

### Why we append both sides to `messages`

After a tool call, we add *two* entries to `messages`:
```python
messages.append({"role": "assistant", "content": response.content})  # the thought + tool call
messages.append({"role": "user",       "content": tool_results})      # the observations
```

This is required by the Anthropic API's tool-use protocol. The model's turn (thought + tool call) must be followed by a user turn (tool results) before the model can generate the next thought. This is what creates the alternating T/A/O structure in the context.

### Why the tool executor returns strings

The model receives tool results as strings — not Python objects. This means:
- `calculator("3 + 4")` should return `"7"`, not `7`
- An error should return `"Error: ..."`, not raise an exception

If you raise an exception inside `execute_tool`, the error propagates outside the loop and crashes the agent. Instead, catch all exceptions and return them as error strings. The model can then reason about what went wrong and try a different approach.

---

## Where it breaks / what it is not

**The stub search.** Today's implementation has a search stub that returns a placeholder. For the code to be fully useful, replace `execute_tool`'s web_search branch with a real API. Tavily (`pip install tavily-python`) is the simplest integration for a research agent.

**The calculator uses `eval`.** This is safe for pure arithmetic but dangerous if the expression contains function calls or imports. In production, use a proper math parser (`sympy`, `numexpr`, or a sandboxed executor). The guard in the code above (character allowlist) handles the common cases but is not bulletproof.

**Context length.** Each step adds tokens. After 15–20 steps on a verbose task, the context may approach the model's limit. The implementation doesn't handle context compression — that's Day 24 (Working Memory Patterns).

---

## Try it yourself

**Exercise 1 — Check your understanding:**
Trace through the code manually for a single-step question: "What is 2 + 2?" Walk through every line of `react_agent()` and explain what happens at each step, what the `messages` list contains, and why the loop exits after one iteration.

**Exercise 2 — Apply it:**
Run the code. Then break it deliberately: change the calculator tool description to "Use this only for addition." Ask "What is 17% of 4820?" Does the model call the calculator? Does it try to compute mentally? What does the trace tell you about how description quality affects behavior?

**Exercise 3 — Stretch:**
Add a third tool: `read_file(path: str) -> str` that reads a local text file. Write the tool definition, add the executor, and test it with a question like "Summarize the contents of /tmp/notes.txt." What happens when the file doesn't exist? How should your tool handle that, and how should the agent respond?

<details>
<summary>Hint for Exercise 2</summary>
The model uses tool descriptions to decide which tool fits the task. If the calculator is described as "addition only," the model may: (1) still use it anyway (ignores description), (2) refuse to use it and try to compute in its head, or (3) use search to find the answer. Each behavior reveals something about how strongly the model follows tool constraints vs. task-completing instinct.
</details>

<details>
<summary>Worked solution for Exercise 3</summary>

```python
# Add to TOOLS:
{
    "name": "read_file",
    "description": (
        "Read the complete contents of a local text file at the given absolute path. "
        "Returns the file contents as a UTF-8 string. "
        "If the file does not exist or cannot be read, returns an error message "
        "starting with 'Error:'."
    ),
    "input_schema": {
        "type": "object",
        "properties": {
            "path": {
                "type": "string",
                "description": "Absolute path to the file, e.g. '/tmp/notes.txt'"
            }
        },
        "required": ["path"]
    }
}

# Add to execute_tool():
elif name == "read_file":
    try:
        with open(inputs["path"], "r", encoding="utf-8") as f:
            content = f.read()
        return content if content.strip() else "Error: file is empty."
    except FileNotFoundError:
        return f"Error: file not found at path '{inputs['path']}'"
    except Exception as e:
        return f"Error reading file: {e}"
```

When the file doesn't exist: `execute_tool` returns `"Error: file not found at '/tmp/notes.txt'"`. This becomes the observation. The model should then think something like "The file doesn't exist. I should let the user know." and answer accordingly. If it doesn't — if it tries to summarize a file that it just learned doesn't exist — that's observation poisoning from Day 4.
</details>

---

## Connect it back

[Day 8](./day-08-react.md) explained the pattern. Today you implemented it. The implementation is the test: you now know not just what ReAct is, but what can go wrong and at which line of code. The step limit, the error propagation, the message format — these are not implementation details, they are the pattern's failure modes made tangible.

[Day 10](./day-10-rest-synthesize-ii.md) is Rest & Synthesize: consolidate Days 6–9 before the next wave. Then Day 11 extends ReAct with a memory for failure: Reflexion.

**One question you can now answer that you couldn't this morning:** Where exactly in the ReAct loop does "hallucinated continuation" happen — and which line of code in today's implementation prevents it?

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
Yao et al., *ReAct* (arXiv:2210.03629) — Appendix A (implementation details, 1 page).
Their implementation decisions align closely with today's code; seeing the original choices confirms you've built the canonical version.

**If you want the deep version:**
- LangChain ReAct source code — GitHub: `langchain-ai/langchain` → `langchain/agents/react/` — Seeing a production implementation of the same pattern (with retries, output parsing, and tracing) shows what production adds on top of today's minimal version.
- Anthropic tool-use documentation — https://docs.anthropic.com/en/docs/tool-use — The complete API reference for the message format used in today's implementation. Worth bookmarking.

---

## Navigation

← **Previous:** [Day 8 — ReAct: Interleaving Thought and Action](./day-08-react.md)
→ **Next:** [Day 10 — Rest & Synthesize II](./day-10-rest-synthesize-ii.md)
