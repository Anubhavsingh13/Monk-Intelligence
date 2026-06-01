# Day 19 — The Skill Design Pattern

> **Today's one idea:** A skill is a composable, reusable capability with a defined input/output contract — and the gap between a raw tool and a well-designed skill is the gap between an agent that sometimes works and one that reliably does.
> **Reading time:** ~40 min · **Prereqs:** Day 3 (tool use primitive), Day 9 (ReAct implementation)
> **Primary source for today:** Anthropic, *Building Effective Agents* (Dec 2024) — "The augmented LLM" and "Workflows" sections · Huyen, *AI Engineering* (O'Reilly, 2025) — Chapter 7.

---

## The hook

Two teams build customer service agents. Both have access to the same LLM and the same backend systems.

**Team A** gives their agent these tools:
- `handle_customer(customer_id, issue)` — a monolith that queries the DB, checks the issue type, sends an email, updates the ticket system, and returns "done"

**Team B** gives their agent:
- `get_customer_profile(customer_id)` → profile dict
- `get_open_tickets(customer_id)` → list of ticket objects
- `send_email(to, subject, body)` → success/failure
- `update_ticket(ticket_id, status, notes)` → updated ticket
- `escalate_to_human(ticket_id, reason)` → escalation record

After three weeks, Team A's agent handles simple cases but falls apart on anything unusual. Team B's agent handles edge cases gracefully: it can decide to check the profile *before* emailing, escalate when the ticket is high-priority, and update the ticket with a specific note. Team A's agent can't make any of these decisions because `handle_customer()` makes them all for it.

The difference isn't LLM quality. It's **skill design**.

---

## Building the intuition

### Tool vs. skill: what's the difference?

[Day 3](../../01-foundations/days/day-03-tool-use-primitive.md) introduced tools as the fundamental primitive. A **skill** is a tool designed to be *composable, reusable, and self-describing* — it follows a design contract that makes it safe and predictable for an agent to use.

The distinction is not in the implementation but in the intent and interface:

```
RAW TOOL                           WELL-DESIGNED SKILL
───────────────────────────────────────────────────────
Does multiple things               Does exactly one thing
Implicit failure modes             Explicit failure modes with error types
Name describes implementation      Name describes purpose
Input: whatever it needs           Input: defined schema with types
Output: varies by case             Output: consistent structure
No usage guidance                  Description tells agent when to use it
```

Think of the difference between a raw API endpoint (implementation-first) and a well-designed library function (user-first). The skill is the library function version of a tool.

### The four design properties of a skill

**Property 1 — Single responsibility.** A skill does one thing. "Send an email" is a skill. "Handle the customer's email request by checking their account, composing a reply, and sending it" is a workflow, not a skill. Workflows are composed from skills; they're not skills themselves.

*Why it matters:* An agent choosing between skills can only make good decisions if it knows what each one does. A monolithic skill that does three things forces the agent to use it for all three — or not at all.

**Property 2 — Explicit failure modes.** A skill always tells the agent what happened. Success returns a value. Failure returns a typed error — not an exception, not silence, not a generic "error occurred."

```python
# Bad — silent failure
def get_customer(customer_id: str) -> dict:
    result = db.query(f"SELECT * FROM customers WHERE id={customer_id}")
    return result  # returns None if not found — agent can't distinguish "no result" from "error"

# Good — explicit failure mode
def get_customer(customer_id: str) -> dict:
    result = db.query(f"SELECT * FROM customers WHERE id=?", [customer_id])
    if result is None:
        return {"error": "not_found", "message": f"No customer found with id={customer_id}"}
    return {"success": True, "data": result}
```

**Property 3 — Self-describing name and description.** The model only sees the name and description. "db_lookup" tells the model nothing. "get_customer_by_id: retrieve the full profile for a customer given their ID" tells it everything it needs to decide when to use this skill.

**Property 4 — Minimal required state.** A skill that requires knowing internal system state (e.g., "this only works after you call `init_session()` first") is fragile. Skills should be stateless or carry their own state. If a skill requires preconditions, document them explicitly in the description.

---

## The formal picture

### The skill interface contract

Every skill should be specifiable as:

```
Skill(
    name:        str,          # verb-object, e.g. "get_customer_profile"
    description: str,          # when to use it; what it returns on success and failure
    input_schema: JSONSchema,  # typed, documented fields; required vs optional
    output_spec:  str,         # what success looks like; what failure looks like
    examples:    list[dict],   # optional: 1-2 input/output pairs for clarity
)
```

Compare a bad and a good skill definition for the same underlying operation:

```python
# Bad skill
{
    "name": "process",
    "description": "Process things",
    "input_schema": {
        "type": "object",
        "properties": {"data": {"type": "string"}}
    }
}

# Good skill
{
    "name": "summarize_document",
    "description": (
        "Produce a 3–5 sentence summary of a document. "
        "Use when the user needs a quick overview of a long text. "
        "Input: the full document text (up to 10,000 words). "
        "Returns: a summary string. "
        "If the document is too short to summarize meaningfully (< 100 words), "
        "returns the original text unchanged with a note."
    ),
    "input_schema": {
        "type": "object",
        "properties": {
            "document": {
                "type": "string",
                "description": "The full text of the document to summarize."
            },
            "focus": {
                "type": "string",
                "description": "Optional: a specific aspect to focus on (e.g. 'financial results'). "
                               "Omit for a general summary."
            }
        },
        "required": ["document"]
    }
}
```

The good version: the agent knows *when* to use this skill, *what inputs to provide*, *what to expect back*, and *what happens at the edges*.

### Skills vs. workflows vs. agents

These three concepts form a hierarchy:

```
Skill       →  A single, well-specified capability.
              "send_email(to, subject, body)"

Workflow    →  A fixed sequence of skills for a known task type.
              "draft_and_send_report = [generate_report, format_as_pdf, send_email]"

Agent       →  A dynamic composer of skills for open-ended tasks.
              "Given any task, decide which skills to use and in what order."
```

Workflows are appropriate when the task structure is known and fixed — they're cheaper, more reliable, and easier to debug. Agents are appropriate when the task requires dynamic, situation-dependent skill selection. Most production systems use both: agents for the routing layer, workflows for the execution layer.

### Skill design in code

```python
from typing import Any
import anthropic

client = anthropic.Anthropic()

# ── Skill registry ─────────────────────────────────────────────────────────────
# A registry gives the agent access to skills, and gives you a single place
# to add, remove, or update skills without touching the agent loop.

class SkillRegistry:
    def __init__(self):
        self._skills: dict[str, dict] = {}
        self._executors: dict[str, callable] = {}

    def register(self, definition: dict, executor: callable) -> None:
        """Register a skill with its definition (shown to LLM) and executor (run by code)."""
        name = definition["name"]
        self._skills[name] = definition
        self._executors[name] = executor

    def tool_definitions(self) -> list[dict]:
        """Return all skill definitions in the format the API expects."""
        return list(self._skills.values())

    def execute(self, name: str, inputs: dict) -> Any:
        """Execute a skill by name. Returns error dict if skill not found."""
        if name not in self._executors:
            return {"error": "skill_not_found", "message": f"No skill named '{name}'"}
        try:
            return self._executors[name](**inputs)
        except TypeError as e:
            return {"error": "invalid_inputs", "message": str(e)}
        except Exception as e:
            return {"error": "execution_error", "message": str(e)}


# ── Example skills ─────────────────────────────────────────────────────────────

registry = SkillRegistry()

# Skill 1: get_word_count
registry.register(
    definition={
        "name": "get_word_count",
        "description": (
            "Count the number of words in a given text. "
            "Use when you need an exact word count. "
            "Returns: {'word_count': int}."
        ),
        "input_schema": {
            "type": "object",
            "properties": {
                "text": {"type": "string", "description": "The text to count words in."}
            },
            "required": ["text"]
        }
    },
    executor=lambda text: {"word_count": len(text.split())}
)

# Skill 2: truncate_to_word_limit
registry.register(
    definition={
        "name": "truncate_to_word_limit",
        "description": (
            "Truncate text to a maximum number of words, ending at a sentence boundary. "
            "Use when text must fit within a word limit. "
            "Returns: {'truncated_text': str, 'original_words': int, 'result_words': int}."
        ),
        "input_schema": {
            "type": "object",
            "properties": {
                "text":      {"type": "string"},
                "max_words": {"type": "integer", "description": "Maximum words allowed."}
            },
            "required": ["text", "max_words"]
        }
    },
    executor=lambda text, max_words: {
        "truncated_text": " ".join(text.split()[:max_words]),
        "original_words": len(text.split()),
        "result_words":   min(len(text.split()), max_words)
    }
)


# ── Agent using the registry ───────────────────────────────────────────────────

def run_with_registry(task: str) -> str:
    messages = [{"role": "user", "content": task}]

    while True:
        response = client.messages.create(
            model="claude-3-5-sonnet-20241022",
            max_tokens=1024,
            tools=registry.tool_definitions(),
            messages=messages
        )
        if response.stop_reason == "tool_use":
            tool_results = []
            for block in response.content:
                if block.type == "tool_use":
                    result = registry.execute(block.name, block.input)
                    tool_results.append({
                        "type": "tool_result",
                        "tool_use_id": block.id,
                        "content": str(result)
                    })
            messages.append({"role": "assistant", "content": response.content})
            messages.append({"role": "user",       "content": tool_results})
        else:
            return next(
                (b.text for b in response.content if hasattr(b, "text")), ""
            )


if __name__ == "__main__":
    result = run_with_registry(
        "I have a document that needs to be under 50 words. "
        "Here it is: 'The quick brown fox jumps over the lazy dog. "
        "It was a beautiful morning in the forest. The birds were singing.' "
        "How many words is it, and what does it look like truncated to 50 words?"
    )
    print(result)
```

---

## Where it breaks / what it is not

**Over-decomposition.** Splitting skills too finely (e.g., separating `get_customer_first_name` and `get_customer_last_name` from `get_customer_profile`) forces the agent to call multiple skills for what should be one operation. The right atomic unit is the *minimum complete unit of meaningful work* — not the minimum unit of computation.

**The description drift problem.** As a system evolves, skill implementations change but descriptions don't get updated. The agent's mental model of what `send_email` does diverges from what it actually does. Treat descriptions as documentation you're accountable for — if the implementation changes, the description must change.

**Skills don't compensate for missing capabilities.** A beautifully designed skill library that doesn't include a database write skill can't handle any task requiring persistent state. Skill design is necessary but not sufficient — you still need to audit whether the skill library covers the task domain.

---

## Try it yourself

**Exercise 1 — Check your understanding:**
Take the monolithic `handle_customer(customer_id, issue)` tool from the hook. Decompose it into 3–5 atomic skills following the four design properties. Write the name and a one-sentence description for each.

**Exercise 2 — Apply it:**
Add a third skill to the registry above: `check_reading_level(text: str) -> dict`. The skill should return the estimated reading level (e.g., "Grade 8", "College") of the text. Write a proper description, schema, and executor (can be a stub or a real implementation using a readability formula like Flesch-Kincaid).

**Exercise 3 — Stretch:**
The `SkillRegistry` above lets you add skills at build time. Design a mechanism to add skills at *run time* — the agent discovers that it needs a capability it doesn't have, and the system dynamically creates and registers a new skill. What are the security implications of this? When is it safe vs. dangerous?

<details>
<summary>Hint for Exercise 1</summary>
Start by listing every distinct action inside `handle_customer()`. Each distinct action with its own inputs and outputs is a candidate for a skill. Then check: does each candidate have single responsibility? Explicit failure modes?
</details>

<details>
<summary>Worked solution for Exercise 1</summary>

```
get_customer_profile(customer_id: str)
  → Returns full customer record or {"error": "not_found", ...}

get_open_tickets(customer_id: str)
  → Returns list of open ticket objects or empty list

classify_issue(issue_description: str)
  → Returns {"category": "billing|technical|general", "priority": "low|medium|high"}

send_customer_email(customer_id: str, subject: str, body: str)
  → Returns {"success": True, "message_id": str} or {"error": "delivery_failed", ...}

update_ticket_status(ticket_id: str, status: str, notes: str)
  → Returns updated ticket or {"error": "ticket_not_found", ...}

escalate_to_human(ticket_id: str, reason: str)
  → Returns {"escalated": True, "assigned_to": str} or {"error": ...}
```

Key decisions: `classify_issue` is a skill (it does one thing: classify). The decision of whether to escalate is left to the agent (it uses `classify_issue` + its own reasoning). No skill called `decide_to_escalate` — that's agent logic, not a skill.
</details>

---

## Connect it back

[Day 3](../../01-foundations/days/day-03-tool-use-primitive.md) showed you the mechanics of tool calling. Today elevated that to a *design discipline*. The four properties — single responsibility, explicit failure, self-describing, minimal state — are your design checklist for every tool you build.

[Tomorrow (Day 20)](./day-20-tool-design-composition.md) goes one level up: given well-designed atomic skills, how do you combine them into complex behaviors? The three composition patterns — chain, fan-out, aggregate — are the structural vocabulary for that.

**One question you can now answer that you couldn't this morning:** An agent that uses `handle_customer()` works 80% of the time but fails on edge cases. An agent that uses the five atomic skills handles edge cases well. Explain precisely why — in terms of the agent's decision-making process.

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
Anthropic, *Building Effective Agents* (Dec 2024) — "The augmented LLM" section and the "Workflows" section.
The "augmented LLM" defines the minimal building block (LLM + tools + memory). The "Workflows" section distinguishes the five workflow patterns (prompt chaining, routing, parallelization, orchestrator-workers, evaluator-optimizer) and discusses when to use each — directly informing tomorrow's composition patterns.

**If you want the deep version:**
- Huyen, *AI Engineering* (O'Reilly, 2025) — Chapter 7 (tool use). The engineering perspective: tool latency profiling, tool selection benchmarks, how many tools you can give an agent before performance degrades. The empirical data behind today's design rules.
- Microsoft Semantic Kernel documentation — https://learn.microsoft.com/en-us/semantic-kernel/ — The production implementation of the Skill/Plugin pattern with versioning, dependency injection, and telemetry built in. The Plugin interface there is a reference implementation of today's `SkillRegistry`.

---

## Navigation

← **Previous:** [Day 18 — Rest & Synthesize III](../../03-self-improvement/days/day-18-rest-synthesize-iii.md)
→ **Next:** [Day 20 — Atomic Tool Design + Composition Patterns](./day-20-tool-design-composition.md)
