# Day 31 — Human-in-the-Loop Patterns

> **One idea:** Agent autonomy is not binary. Three interrupt patterns — pre-action (pause before
> irreversible actions), checkpoint (pause at phase boundaries), and confidence-based (pause when
> certainty falls below threshold) — let you dial the right level of oversight for every task.

| | |
|---|---|
| **Reading time** | ~40 min |
| **Prerequisites** | Day 27 (Orchestrator-Worker), Day 9 (ReAct implementation) |
| **Primary sources** | Anthropic, "Building Effective Agents" (Dec 2024) — "Human-in-the-loop" section · Amershi et al., "Guidelines for Human-AI Interaction" (CHI 2019) |

---

## 1. Hook

A commercial autopilot doesn't put the crew to sleep.

It handles cruise altitude with sub-millimeter precision — better than any human over eight hours.
But the crew is present for takeoff, landing, and every red alert. The system is not trying to
replace the pilots; it is trying to use each party — human and machine — for what they're best at.

Agents need the same model. A fully autonomous agent is a liability: one bad decision compounds
into ten before anyone notices. A fully manual agent defeats the purpose of building one.
The space between those extremes is where most real deployments live, and navigating it requires
explicit, designed interrupt points — moments where the agent pauses and hands control back to
the human.

This module is about building those moments deliberately.

---

## 2. Building the Intuition

### 2.1 The Autonomy Spectrum

Think of agent autonomy as a dial, not a switch.

```
FULLY MANUAL ←───────────────────────────────────────────→ FULLY AUTONOMOUS
     │              │              │               │               │
  Human does   Pre-action     Checkpoint     Confidence-      Agent runs
  everything   interrupt      interrupt       based int.      end-to-end
               (pause before  (pause at       (pause when
               irreversible)  phase end)      unsure)
```

Different tasks belong at different positions on this dial — and the right position can change
within a single task. A research-then-email workflow might be fully autonomous for the research
phase and pre-action-interrupted for the email send. That's the key insight: **the dial is
per-action, not per-agent.**

### 2.2 Three Interrupt Patterns

**Pattern 1 — Pre-action interrupt**

The agent pauses *before* any action that is irreversible or high-stakes, shows the proposed
action to the human, and waits for approval before executing.

- File deletion, email send, database write, payment, API call with side-effects.
- The human sees exactly what will happen, not a summary of it.
- If rejected: the agent is told "action rejected, try a different approach" and continues reasoning.

**Pattern 2 — Checkpoint interrupt**

The agent pauses at the *end of a phase* — planning, research, drafting, publishing — and presents
a summary of what it has done and what it plans to do next.

- Used when you trust the agent's execution quality but want to approve the direction.
- The human approves the phase output, possibly with modifications, before the agent moves on.
- More coarse-grained than pre-action: you're reviewing a plan, not a single tool call.

**Pattern 3 — Confidence-based interrupt**

The agent monitors its own certainty during reasoning and pauses when it falls below a threshold.
The agent signals this with a flag in its tool input or reasoning output.

- Handles ambiguous instructions, contradictory context, out-of-scope requests.
- Requires the model to express calibrated uncertainty — not always reliable, but useful as a
  supplementary signal.
- The human provides clarification and the agent continues.

### 2.3 The Interrupt Budget

Every interrupt consumes human attention and adds latency. This means interrupt conditions must be
designed to fire *rarely* — if the agent interrupts every third action, people start rubber-stamping
approvals, which is worse than no interrupt (false sense of safety).

A useful heuristic for deciding what qualifies:

| Criterion | Examples |
|---|---|
| Irreversibility | Send email, delete file, publish post, charge card |
| High blast radius | Action affects many downstream records |
| Ambiguous instruction | The user's request has two plausible interpretations |
| Out-of-scope | Agent is about to do something the user never asked for |
| Confidence below threshold | Agent flagged its own uncertainty |

Rate-limit your interrupt conditions. If a condition fires more than once every 5–10 tool calls on
average, it is too broad.

### 2.4 Handling the Human Response

When the agent pauses, the human must have three options — and the agent must handle all three
correctly:

| Response | Meaning | Agent action |
|---|---|---|
| **APPROVE** | Proceed exactly as planned | Execute the proposed action |
| **MODIFY** | Proceed, but with this change | Incorporate the modification, then execute |
| **REJECT** | Do not do this | Tell the agent "rejected", let it reason about alternatives |

The MODIFY path is the most dangerous to get wrong. It is not enough for the agent to acknowledge
the modification — it must actually incorporate it into its next reasoning step. This is best
handled by injecting the modification as a `tool_result` that contains both the human instruction
and the outcome, so the model's next turn sees both in context.

---

## 3. The Formal Picture

### Control flow

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        HumanInLoopAgent.run(task)                           │
└───────────────────────────────────┬─────────────────────────────────────────┘
                                    │
                          ┌─────────▼──────────┐
                          │  Build messages[]   │
                          └─────────┬──────────┘
                                    │
                          ┌─────────▼──────────┐
                          │  client.messages    │◄──────────────────────────┐
                          │  .create(...)       │                           │
                          └─────────┬──────────┘                           │
                                    │                                       │
              ┌─────────────────────▼────────────────────────┐             │
              │            stop_reason == "tool_use"?         │             │
              └──────────Yes──────┬──────────────No───────────┘             │
                                  │                    │                    │
                    ┌─────────────▼──────────┐   ┌────▼────────┐           │
                    │  For each tool_use block│   │  Extract    │           │
                    └─────────────┬──────────┘   │  final text │           │
                                  │              └────┬────────┘           │
                    ┌─────────────▼──────────┐        │                    │
                    │ _should_interrupt()?    │        ▼                    │
                    └──────Yes──────No────────┘    RETURN                  │
                           │        │                                       │
              ┌────────────▼─┐  ┌───▼──────────────────┐                  │
              │ Pause + show  │  │ Execute tool directly │                  │
              │ human         │  └───────────────────────┘                  │
              └────────┬──────┘                                             │
                       │                                                    │
        ┌──────────────┼──────────────┐                                    │
        │              │              │                                     │
    APPROVE         MODIFY         REJECT                                   │
        │              │              │                                     │
   Execute         Execute        Return         Append tool_results        │
   as-is          modified        "rejected"     to messages ──────────────┘
                  version         to agent
```

### Full implementation

```python
"""
day31_human_in_the_loop.py

Human-in-the-loop agent with three interrupt patterns:
  - Pre-action  : fires before irreversible tool calls
  - Checkpoint  : fires at the end of configurable phase boundaries
  - Confidence  : fires when the agent signals low certainty

Requires: pip install anthropic
"""

import json
import sys
from dataclasses import dataclass, field
from typing import Callable, Any

import anthropic

client = anthropic.Anthropic()

# ---------------------------------------------------------------------------
# Type alias
# An InterruptCondition takes (tool_name, tool_input) and returns True
# if this action requires human approval before execution.
# ---------------------------------------------------------------------------
InterruptCondition = Callable[[str, dict], bool]


# ---------------------------------------------------------------------------
# Built-in interrupt conditions
# ---------------------------------------------------------------------------

def is_irreversible(tool_name: str, tool_input: dict) -> bool:
    """
    Pause before any tool whose name contains an irreversibility keyword.
    This covers the most common cases without needing per-tool configuration.
    """
    irreversible_keywords = {
        "delete", "send", "publish", "pay", "remove",
        "destroy", "post", "write", "overwrite", "drop",
    }
    return any(kw in tool_name.lower() for kw in irreversible_keywords)


def is_high_cost(tool_name: str, tool_input: dict) -> bool:
    """
    Pause before actions the agent estimates will be expensive.
    Requires the agent's tool schema to include an estimated_cost_usd field.
    """
    return float(tool_input.get("estimated_cost_usd", 0)) > 10.0


def confidence_below_threshold(tool_name: str, tool_input: dict) -> bool:
    """
    Pause when the agent has flagged low confidence in this action.
    Requires the tool schema to include a 'confidence' field (0.0–1.0).
    The agent must be prompted to use this field honestly.
    """
    return float(tool_input.get("confidence", 1.0)) < 0.6


def is_out_of_scope(tool_name: str, tool_input: dict) -> bool:
    """
    Pause when the agent marks an action as outside its original task scope.
    Requires a boolean 'out_of_scope' field in the tool input.
    """
    return bool(tool_input.get("out_of_scope", False))


# ---------------------------------------------------------------------------
# Checkpoint support
# ---------------------------------------------------------------------------

@dataclass
class CheckpointConfig:
    """
    Configures phase-boundary (checkpoint) interrupts.

    phase_tool_map:
        Maps a phase name to a set of tool names that, when completed,
        signal the end of that phase. When the agent finishes the last tool
        of a phase, the checkpoint fires.

    Example:
        CheckpointConfig(
            phase_tool_map={
                "research": {"search_web", "read_url"},
                "draft":    {"write_draft"},
            }
        )
    """
    phase_tool_map: dict = field(default_factory=dict)  # {phase_name: set[tool_name]}
    _completed_in_phase: dict = field(default_factory=dict, init=False, repr=False)

    def record_completion(self, tool_name: str) -> str | None:
        """
        Record that tool_name completed. Return the phase name if this
        completion triggers a checkpoint, else None.
        """
        for phase, tools in self.phase_tool_map.items():
            if tool_name in tools:
                completed = self._completed_in_phase.setdefault(phase, set())
                completed.add(tool_name)
                if completed >= tools:
                    return phase
        return None


# ---------------------------------------------------------------------------
# HumanInLoopAgent
# ---------------------------------------------------------------------------

@dataclass
class InterruptDecision:
    action: str          # "approved" | "modified:<text>" | "rejected"
    tool_name: str
    tool_input: dict


class HumanInLoopAgent:
    """
    An agent wrapper that applies configurable interrupt conditions to tool
    calls before executing them.

    Parameters
    ----------
    tools : list
        Anthropic tool definitions (list of dicts with name/description/input_schema).
    interrupt_conditions : list[InterruptCondition]
        Callables that return True when a human should be consulted.
    checkpoint_config : CheckpointConfig | None
        If provided, adds phase-boundary checkpoint interrupts.
    human_input_fn : callable | None
        (tool_name, action_str, tool_input) → decision_str
        Defaults to a stdin-based prompt. Replace with a UI callback in production.
    tool_executor : callable | None
        (tool_name, tool_input) → result_str
        Defaults to a stub. Replace with real tool dispatch in production.
    system_prompt : str | None
        System-level instruction for the underlying model.
    """

    def __init__(
        self,
        tools: list,
        interrupt_conditions: list,
        checkpoint_config: CheckpointConfig | None = None,
        human_input_fn: Callable[[str, str, dict], str] | None = None,
        tool_executor: Callable[[str, dict], Any] | None = None,
        system_prompt: str | None = None,
    ):
        self.tools = tools
        self.conditions = interrupt_conditions
        self.checkpoint_config = checkpoint_config
        self.human_input_fn = human_input_fn or self._default_human_input
        self.tool_executor = tool_executor or self._stub_executor
        self.system_prompt = system_prompt
        self.interrupt_log: list[InterruptDecision] = []

    # ------------------------------------------------------------------
    # Interrupt condition evaluation
    # ------------------------------------------------------------------

    def _should_interrupt(self, tool_name: str, tool_input: dict) -> bool:
        return any(cond(tool_name, tool_input) for cond in self.conditions)

    def _check_checkpoint(self, tool_name: str) -> str | None:
        if self.checkpoint_config is None:
            return None
        return self.checkpoint_config.record_completion(tool_name)

    # ------------------------------------------------------------------
    # Default I/O handlers (override in production)
    # ------------------------------------------------------------------

    @staticmethod
    def _default_human_input(
        tool_name: str, action_str: str, tool_input: dict
    ) -> str:
        print("\n" + "=" * 60)
        print("[INTERRUPT — Human approval required]")
        print(f"  Tool   : {tool_name}")
        print(f"  Action : {action_str[:200]}")
        print("=" * 60)
        while True:
            raw = input("  (a)pprove / (m)odify / (r)eject: ").strip().lower()
            if raw == "a":
                return "approved"
            elif raw == "m":
                mod = input("  Describe your modification: ").strip()
                return f"modified:{mod}"
            elif raw == "r":
                return "rejected"
            else:
                print("  Please enter 'a', 'm', or 'r'.")

    @staticmethod
    def _default_checkpoint_input(phase_name: str, summary: str) -> str:
        print("\n" + "=" * 60)
        print(f"[CHECKPOINT — End of phase: {phase_name.upper()}]")
        print(f"  Summary: {summary[:300]}")
        print("=" * 60)
        while True:
            raw = input("  (a)pprove to continue / (m)odify / (r)eject: ").strip().lower()
            if raw in {"a", "m", "r"}:
                if raw == "m":
                    mod = input("  Direction change: ").strip()
                    return f"modified:{mod}"
                return "approved" if raw == "a" else "rejected"
            print("  Please enter 'a', 'm', or 'r'.")

    @staticmethod
    def _stub_executor(tool_name: str, tool_input: dict) -> str:
        """
        Stub tool executor. Replace with your real dispatch logic:
            match tool_name:
                case "search_web": return web_search(tool_input["query"])
                case "send_email": return send_email(**tool_input)
                ...
        """
        return f"[STUB] Executed {tool_name} with {json.dumps(tool_input)[:150]}"

    # ------------------------------------------------------------------
    # Decision handling
    # ------------------------------------------------------------------

    def _handle_decision(
        self, decision: str, tool_name: str, tool_input: dict
    ) -> str:
        """Translate a human decision string into a tool result content."""
        if decision == "approved":
            result = self.tool_executor(tool_name, tool_input)
        elif decision.startswith("modified:"):
            modification = decision[9:].strip()
            # The modification is fed back to the agent as context.
            # The stub executes the original tool; real dispatch would
            # incorporate the modification into the call.
            result = (
                f"Human modified this action: {modification}\n"
                f"Execution result: {self.tool_executor(tool_name, tool_input)}"
            )
        else:  # rejected
            result = (
                "This action was rejected by the human reviewer. "
                "Do not attempt the same action again. "
                "Reassess the approach and try an alternative if one exists."
            )
        return result

    # ------------------------------------------------------------------
    # Main run loop
    # ------------------------------------------------------------------

    def run(self, task: str) -> str:
        """
        Execute the task with human-in-the-loop interrupts.

        Returns the agent's final text response.
        """
        messages: list[dict] = [{"role": "user", "content": task}]
        kwargs: dict = dict(
            model="claude-3-5-sonnet-20241022",
            max_tokens=2048,
            tools=self.tools,
            messages=messages,
        )
        if self.system_prompt:
            kwargs["system"] = self.system_prompt

        while True:
            response = client.messages.create(**kwargs)

            if response.stop_reason != "tool_use":
                # Agent produced a final answer — extract and return it.
                return next(
                    (b.text for b in response.content if hasattr(b, "text")), ""
                )

            # ----- Process tool calls ---------------------------------
            tool_results = []
            for block in response.content:
                if block.type != "tool_use":
                    continue

                action_str = json.dumps(block.input)

                # 1. Pre-action interrupt
                if self._should_interrupt(block.name, block.input):
                    decision = self.human_input_fn(block.name, action_str, block.input)
                    self.interrupt_log.append(
                        InterruptDecision(
                            action=decision,
                            tool_name=block.name,
                            tool_input=block.input,
                        )
                    )
                    result = self._handle_decision(decision, block.name, block.input)
                else:
                    result = self.tool_executor(block.name, block.input)

                # 2. Checkpoint interrupt (fires AFTER tool execution)
                phase = self._check_checkpoint(block.name)
                if phase is not None:
                    summary = f"Completed phase '{phase}'. Last result: {str(result)[:200]}"
                    cp_decision = self._default_checkpoint_input(phase, summary)
                    self.interrupt_log.append(
                        InterruptDecision(
                            action=f"checkpoint:{cp_decision}",
                            tool_name=block.name,
                            tool_input=block.input,
                        )
                    )
                    if cp_decision.startswith("modified:"):
                        result = (
                            f"{result}\n"
                            f"[Checkpoint] Human direction change for next phase: "
                            f"{cp_decision[9:]}"
                        )
                    elif cp_decision == "rejected":
                        return f"Task halted at checkpoint '{phase}' by human reviewer."

                tool_results.append({
                    "type": "tool_result",
                    "tool_use_id": block.id,
                    "content": str(result),
                })

            # Append assistant turn + tool results, then loop
            messages.append({"role": "assistant", "content": response.content})
            messages.append({"role": "user", "content": tool_results})
            kwargs["messages"] = messages


# ---------------------------------------------------------------------------
# Demo tools
# ---------------------------------------------------------------------------

DEMO_TOOLS = [
    {
        "name": "search_web",
        "description": "Search the web for factual information. Safe, read-only.",
        "input_schema": {
            "type": "object",
            "properties": {
                "query":      {"type": "string", "description": "Search query."},
                "confidence": {"type": "number", "description": "0.0–1.0 certainty this query answers the task."},
            },
            "required": ["query"],
        },
    },
    {
        "name": "send_email",
        "description": "Send an email. This action is irreversible.",
        "input_schema": {
            "type": "object",
            "properties": {
                "to":      {"type": "string"},
                "subject": {"type": "string"},
                "body":    {"type": "string"},
            },
            "required": ["to", "subject", "body"],
        },
    },
    {
        "name": "delete_file",
        "description": "Permanently delete a file from disk.",
        "input_schema": {
            "type": "object",
            "properties": {"path": {"type": "string"}},
            "required": ["path"],
        },
    },
    {
        "name": "write_draft",
        "description": "Write a draft document to memory (not persisted yet).",
        "input_schema": {
            "type": "object",
            "properties": {
                "title":   {"type": "string"},
                "content": {"type": "string"},
            },
            "required": ["title", "content"],
        },
    },
]


# ---------------------------------------------------------------------------
# Entry point
# ---------------------------------------------------------------------------

if __name__ == "__main__":
    # Pattern 1: Pre-action interrupt on irreversible actions
    print("\n--- Demo: Pre-action + confidence-based interrupts ---\n")

    agent = HumanInLoopAgent(
        tools=DEMO_TOOLS,
        interrupt_conditions=[is_irreversible, confidence_below_threshold],
        system_prompt=(
            "You are a research and email assistant. "
            "When searching, include a 'confidence' field (0.0–1.0) in your tool call "
            "to indicate how confident you are that this search answers the task. "
            "When you are unsure, set confidence below 0.6."
        ),
    )

    result = agent.run(
        "Research the top 3 Python libraries for PDF parsing, then send a brief "
        "summary email to team@example.com with the key findings."
    )

    print("\n=== FINAL RESULT ===")
    print(result)

    print(f"\n=== INTERRUPT LOG ({len(agent.interrupt_log)} interrupts) ===")
    for i, entry in enumerate(agent.interrupt_log, 1):
        print(f"  {i}. [{entry.tool_name}] → {entry.action[:80]}")
```

### Running it

```bash
python day31_human_in_the_loop.py
```

The agent will search the web autonomously (no interrupt), then pause before calling `send_email`.
You will see a prompt like:

```
============================================================
[INTERRUPT — Human approval required]
  Tool   : send_email
  Action : {"to": "team@example.com", "subject": "PDF Parsing Libraries", "body": "..."}
============================================================
  (a)pprove / (m)odify / (r)eject:
```

---

## 4. Where It Breaks / What It Is Not

### Human latency is now on the critical path

Every interrupt adds human response time. If the agent needs three approvals per task and each
takes five minutes, a task that takes 30 seconds to compute takes 15+ minutes end-to-end. Design
interrupt conditions to fire rarely. One interrupt per task is ideal; more than three per task
suggests the task should be broken into smaller, better-defined pieces.

### False sense of safety from rubber-stamping

If interrupts fire too often, humans start approving without reading. An operator who approves
every `send_email` without checking the body provides no protection — and creates a paper trail
that implies oversight where none exists. The goal is not maximum interrupts; it is *meaningful*
interrupts where the human has context and time to make a real decision.

### MODIFY responses require prompt engineering

When a human sends a modification, the agent receives it as a `tool_result`. Nothing guarantees
the agent will incorporate it correctly. You must prompt the model explicitly: *"If a tool result
contains 'Human modified:...', you must follow that instruction in your next action."* Without
this, the model may acknowledge the modification in prose but ignore it in its next tool call.

### Confidence-based interrupts are model-dependent

The `confidence` field approach works only if the model produces well-calibrated self-assessments.
In practice, LLMs tend to be overconfident. Treat confidence-based interrupts as a supplementary
signal — useful when they fire, but do not rely on them as the *only* safety mechanism for
consequential decisions. Pair them with structural conditions (irreversibility, cost).

### Checkpoint interrupts require stable phase boundaries

If the agent skips a tool or reorders its steps, a checkpoint configured for "all of {search_web,
read_url} completed" may never fire. Phase-based checkpoints work best when the agent follows a
predictable workflow. For more exploratory agents, pre-action interrupts are more reliable.

### What it is not

- Not a security boundary. A human-in-the-loop is not a guardrail. A determined adversary who
  can inject content into the agent's context can construct inputs that look approved. See Day 32
  for security-focused design.
- Not a substitute for correct tool permissions. The interrupt is a last-line check, not the
  primary mechanism for preventing unauthorized actions. Proper tool authorization should happen
  at the tool layer, independent of the agent.

---

## 5. Try It Yourself

### Exercise 1 — Add a "high blast radius" interrupt condition

Add a new `InterruptCondition` that fires when the agent's tool input contains a `records_affected`
field with a value greater than 100. Wire it into the agent alongside `is_irreversible`.

<details>
<summary>Answer</summary>

```python
def is_high_blast_radius(tool_name: str, tool_input: dict) -> bool:
    """
    Pause when the action would affect more than 100 records.
    Requires the tool schema to include a 'records_affected' integer field.
    """
    return int(tool_input.get("records_affected", 0)) > 100


# Usage:
agent = HumanInLoopAgent(
    tools=DEMO_TOOLS,
    interrupt_conditions=[is_irreversible, is_high_blast_radius],
)
```

Note: for this to work, the agent must include `records_affected` in its tool calls. You need
a system prompt that instructs the model to estimate and include this field, just as with
`confidence`. Example addition to the system prompt:

```
When calling any tool that modifies data, include a 'records_affected' integer field
estimating how many records this action will affect.
```

</details>

---

### Exercise 2 — Non-interactive interrupt (for testing)

The default `human_input_fn` uses `input()`, which blocks in CI and tests. Write a
`mock_human_input_fn` that:
- Approves any `search_web` call.
- Rejects any `send_email` call.
- Approves everything else.

Then run the agent with this function instead of stdin.

<details>
<summary>Answer</summary>

```python
def mock_human_input_fn(
    tool_name: str, action_str: str, tool_input: dict
) -> str:
    """
    Deterministic interrupt responder for testing:
    - Approves searches
    - Rejects email sends
    - Approves everything else
    """
    if tool_name == "send_email":
        print(f"[MOCK] Rejecting send_email to {tool_input.get('to')}")
        return "rejected"
    print(f"[MOCK] Approving {tool_name}")
    return "approved"


# Usage in tests:
agent = HumanInLoopAgent(
    tools=DEMO_TOOLS,
    interrupt_conditions=[is_irreversible],
    human_input_fn=mock_human_input_fn,
)
result = agent.run("Research PDF libraries and email the results to team@example.com.")
# The agent will research, attempt to send, be rejected, and either
# return without sending or ask the human for an alternative.
assert "team@example.com" not in result or "rejected" in str(agent.interrupt_log)
```

</details>

---

### Exercise 3 — Checkpoint-based two-phase workflow

Set up a checkpoint-interrupted agent that:
1. Runs a research phase (uses `search_web`).
2. Pauses and asks "approve to continue to drafting?".
3. Runs a drafting phase (uses `write_draft`).
4. Pauses and asks "approve to publish?".
5. Sends the email only if both checkpoints are approved.

Use `CheckpointConfig` from the implementation above.

<details>
<summary>Answer</summary>

```python
checkpoint_config = CheckpointConfig(
    phase_tool_map={
        "research": {"search_web"},    # fires after search_web completes
        "drafting": {"write_draft"},   # fires after write_draft completes
    }
)

agent = HumanInLoopAgent(
    tools=DEMO_TOOLS,
    interrupt_conditions=[is_irreversible],  # still catch send_email
    checkpoint_config=checkpoint_config,
)

result = agent.run(
    "Research the top Python PDF libraries, write a draft summary, "
    "then send it to team@example.com."
)

# Expected flow:
# 1. Agent calls search_web → executes → checkpoint "research" fires
#    → human approves → agent continues
# 2. Agent calls write_draft → executes → checkpoint "drafting" fires
#    → human approves → agent continues
# 3. Agent calls send_email → is_irreversible fires → pre-action interrupt
#    → human approves → email sent
print(result)
print(f"Interrupts: {len(agent.interrupt_log)}")
```

The key insight: checkpoints and pre-action interrupts compose independently. The checkpoint
fires based on *which phase just completed*; the pre-action interrupt fires based on *which
tool is about to execute*. They cover different risks and can both be active simultaneously.

</details>

---

## 6. Connect It Back

**Day 9 (ReAct)** introduced the thought → action → observation loop. Human-in-the-loop is a
modification of that loop: certain actions require a human observation rather than a tool
observation. The structure is identical; only the observation source changes.

**Day 27 (Orchestrator-Worker)** showed how to split complex tasks across agents. When the
orchestrator sends a task to a worker, the interrupt conditions should live in the orchestrator,
not the worker — the orchestrator is the one with the full task context and the authority to pause.

**Day 32 (Guardrails)** extends this idea toward production hardening. Human-in-the-loop handles
*ambiguity and oversight*; guardrails handle *safety and invariants*. A production agent typically
needs both layers: guardrails prevent obviously wrong outputs automatically, human interrupts
handle the judgment calls that cannot be automated.

The autonomy spectrum from §2.1 maps directly to a deployment maturity model:

- **New agent, new task type:** start with checkpoint interrupts at every phase boundary.
- **Trusted agent, familiar task:** promote to pre-action interrupts only.
- **High-confidence, high-frequency task:** promote to confidence-based or no interrupts.
- **Any irreversible action, always:** keep a pre-action interrupt regardless of maturity.

---

## 7. Suggested Readings

**Primary**
- Anthropic, "Building Effective Agents" (Dec 2024), § "Human-in-the-loop" — the canonical
  source for interrupt pattern taxonomy in LLM agents.
- Amershi, S. et al. "Guidelines for Human-AI Interaction." CHI 2019. — 18 design guidelines
  for human-AI systems; Guidelines 8–11 are directly relevant to interrupt patterns.

**Secondary**
- Shneiderman, B. "Human-Centered AI." Oxford University Press, 2022. — Broad framing of the
  human-AI partnership; Chapter 7 covers the automation-control tradeoff.
- Parasuraman, R., Sheridan, T., & Wickens, C. "A Model for Types and Levels of Human
  Interaction with Automation." IEEE Transactions on Systems, Man, and Cybernetics, 2000. —
  The original 10-level automation taxonomy that the autonomy spectrum in §2.1 adapts.
- OpenAI, "Practices for Governing Agentic AI Systems" (2024). — Discusses when and how to
  implement human oversight for deployed agents.

**Code**
- `anthropic-cookbook` / `tool-use` examples — shows the tool_result message format in detail.
- LangGraph's `interrupt()` primitive — a different architectural approach to the same problem,
  useful for comparison.

---

## 8. Navigation

| | |
|---|---|
| **Previous** | [Day 30 — Mixture of Agents](./day-30-mixture-of-agents.md) |
| **Next** | [Day 32 — Guardrails & Production Patterns](./day-32-guardrails.md) |
| **Module home** | [Module 06 — Multi-Agent Systems](../overview.md) |
| **Course home** | [Agentic Design Patterns](../../../README.md) |
