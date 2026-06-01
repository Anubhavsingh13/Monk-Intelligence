# Day 24 — Scratchpad & Working Memory

> **Today's one idea:** The context window is not a passive buffer — it is the agent's only fast-access workspace, and it should be structured deliberately. The scratchpad pattern organizes working memory into slots, compresses old content when capacity approaches, and retains only what the next step actually needs.
> **Reading time:** ~40 min · **Prereqs:** Day 23 (Memory Taxonomy), Day 8 (ReAct)
> **Primary source for today:** Nye et al., "Show Your Work: Scratchpads for Intermediate Computation with Language Models" (arXiv:2112.00114, 2021) · Anthropic, *Building Effective Agents* (Dec 2024) — "Augmented LLM."

---

## The hook

When a human solves a hard multi-step problem, they write intermediate results on paper. That paper is not the answer — it is the workspace. The final answer is small; the scratchpad can be ten times larger. When the scratchpad gets full, the human crosses out old notes and writes summaries. They don't try to keep everything — they keep what the *next step* needs.

An LLM's context window is that paper. Most agents let it fill up randomly: tool results, reasoning traces, system prompt, user messages — all in a single flat buffer. When it overflows, something gets silently truncated. What gets lost is whoever happens to be at the oldest position.

The scratchpad pattern makes this workspace explicit. You decide what goes in, how it is organized, and what gets compressed when capacity approaches. The agent still uses the same context window — but now it is a designed workspace instead of an unmanaged pile.

---

## Building the intuition

### Three sub-patterns

**Sub-pattern 1 — Tagged slots.** Divide the context window into named sections using XML-like tags or Markdown headers. The agent knows what each section is for. This prevents the most common failure: tool results overwriting reasoning traces, or conclusions mixing with open questions.

```
## Established Facts
- The user's order was placed on 2024-01-15.
- The shipping address is confirmed correct.

## Open Questions
- Why did the package not arrive by the estimated date?

## Working Notes
- Need to check carrier tracking system.
- Check if the order was flagged for manual review.
```

**Sub-pattern 2 — Compression.** When working notes grow past a threshold, summarize the oldest entries into a single "Established Facts" entry and drop the originals. The summary captures the conclusion; the detailed path that led there is discarded. This keeps total token count bounded.

**Sub-pattern 3 — Selective retention.** Not all content has equal value for the next step. A resolved question can be dropped. A working note that turned out to be wrong can be dropped. The agent explicitly marks entries as resolved or irrelevant before each step. Only what is needed for the next step occupies working memory.

### Why this matters: the silent truncation failure

Without a scratchpad, a long-running agent accumulates content passively until it hits the context limit. At that point the API truncates from the oldest end. What gets cut is:
- The system prompt (your instructions to the agent)
- Early tool results (observations the agent was reasoning about)
- Initial facts the agent established

The agent continues, but now it is missing its own prior conclusions. It may re-derive them, contradict them, or simply produce incoherent output with no error signal. The scratchpad pattern prevents this by controlling *what* is kept.

---

## The formal picture

### The `ScratchpadAgent`

```python
import anthropic
from dataclasses import dataclass, field

client = anthropic.Anthropic()

COMPRESSION_THRESHOLD_NOTES = 10  # compress when working notes exceed this count
COMPRESSION_THRESHOLD_TOKENS = 2_000  # compress when scratchpad exceeds this token estimate


# ── Scratchpad data structure ──────────────────────────────────────────────────

@dataclass
class Scratchpad:
    established_facts: list[str] = field(default_factory=list)
    open_questions:    list[str] = field(default_factory=list)
    working_notes:     list[str] = field(default_factory=list)
    conclusion:        str       = ""

    def token_estimate(self) -> int:
        """Rough token estimate: 4 characters ≈ 1 token."""
        return len(self.to_context()) // 4

    def to_context(self) -> str:
        """Render the scratchpad as structured context for an LLM call."""
        parts = []
        if self.established_facts:
            parts.append(
                "## Established Facts\n"
                + "\n".join(f"- {f}" for f in self.established_facts)
            )
        if self.open_questions:
            parts.append(
                "## Open Questions\n"
                + "\n".join(f"? {q}" for q in self.open_questions)
            )
        if self.working_notes:
            parts.append(
                "## Working Notes\n"
                + "\n".join(f"- {n}" for n in self.working_notes)
            )
        return "\n\n".join(parts) if parts else "(scratchpad empty)"

    def compress(self, llm_fn) -> None:
        """
        Compress old working notes into a single Established Fact.
        Keeps the most recent COMPRESSION_THRESHOLD_NOTES // 2 notes untouched.
        """
        keep_count = COMPRESSION_THRESHOLD_NOTES // 2
        old_notes  = self.working_notes[:-keep_count]
        fresh_notes = self.working_notes[-keep_count:]

        if not old_notes:
            return

        summary = llm_fn(
            f"Compress these working notes into 1–2 key facts, "
            f"discarding intermediate reasoning. Return only the facts, "
            f"one per line:\n\n" + "\n".join(old_notes),
            max_tokens=256,
        )
        for line in summary.splitlines():
            line = line.strip().lstrip("- •").strip()
            if line:
                self.established_facts.append(f"[compressed] {line}")

        self.working_notes = fresh_notes


# ── LLM helper ────────────────────────────────────────────────────────────────

def llm(prompt: str, max_tokens: int = 1024) -> str:
    response = client.messages.create(
        model="claude-3-5-sonnet-20241022",
        max_tokens=max_tokens,
        messages=[{"role": "user", "content": prompt}]
    )
    return response.content[0].text.strip()


def llm_with_system(system: str, user: str, max_tokens: int = 1024) -> str:
    response = client.messages.create(
        model="claude-3-5-sonnet-20241022",
        max_tokens=max_tokens,
        system=system,
        messages=[{"role": "user", "content": user}]
    )
    return response.content[0].text.strip()


# ── Parsing the agent's structured output ─────────────────────────────────────

def apply_scratchpad_update(scratch: Scratchpad, response: str) -> str | None:
    """
    Parse the agent's response and update the scratchpad.
    Returns the final answer if ANSWER: is present, else None.
    """
    for line in response.splitlines():
        line = line.strip()
        if line.startswith("ADD_FACT:"):
            fact = line[9:].strip()
            if fact and fact not in scratch.established_facts:
                scratch.established_facts.append(fact)
        elif line.startswith("ADD_NOTE:"):
            note = line[9:].strip()
            if note:
                scratch.working_notes.append(note)
        elif line.startswith("ADD_QUESTION:"):
            question = line[13:].strip()
            if question and question not in scratch.open_questions:
                scratch.open_questions.append(question)
        elif line.startswith("RESOLVE:"):
            resolved = line[8:].strip()
            scratch.open_questions = [
                q for q in scratch.open_questions if q != resolved
            ]
        elif line.startswith("ANSWER:"):
            return line[7:].strip()
    return None


# ── The scratchpad agent loop ──────────────────────────────────────────────────

SYSTEM_PROMPT = """\
You are a methodical reasoning agent with a structured scratchpad.
At each step, read the scratchpad, reason, then update it using these commands:

ADD_FACT: <fact>          — a verified, stable conclusion
ADD_NOTE: <note>          — a working hypothesis or intermediate result
ADD_QUESTION: <question>  — something you need to investigate next
RESOLVE: <question>       — mark an open question as answered
ANSWER: <final answer>    — emit this only when you have a complete answer

Rules:
- Use ADD_FACT sparingly — only for things you are certain of.
- RESOLVE a question before emitting ANSWER.
- Emit ANSWER only once, as the last line.
"""


def run_scratchpad_agent(task: str, max_steps: int = 8) -> dict:
    """
    Run a reasoning agent with a structured, compressible scratchpad.
    Returns: {"answer": str, "scratchpad": Scratchpad, "steps": int}
    """
    scratch = Scratchpad()
    scratch.open_questions.append(task)  # seed the first question

    for step in range(1, max_steps + 1):
        # Compress if scratchpad is growing large
        if (len(scratch.working_notes) > COMPRESSION_THRESHOLD_NOTES
                or scratch.token_estimate() > COMPRESSION_THRESHOLD_TOKENS):
            scratch.compress(llm)
            print(f"  [step {step}] Compressed scratchpad "
                  f"({scratch.token_estimate()} tokens after compression)")

        # Build the context
        context = scratch.to_context()
        user_msg = f"Task: {task}\n\n{context}\n\n---\nStep {step}: Reason and update the scratchpad."

        response = llm_with_system(SYSTEM_PROMPT, user_msg)

        print(f"\n[Step {step}]")
        print(response)

        # Apply updates and check for final answer
        answer = apply_scratchpad_update(scratch, response)
        if answer:
            scratch.conclusion = answer
            return {"answer": answer, "scratchpad": scratch, "steps": step}

    # No ANSWER emitted within max_steps
    conclusion = scratch.conclusion or "Max steps reached without a final answer."
    return {"answer": conclusion, "scratchpad": scratch, "steps": max_steps}


# ── Example ────────────────────────────────────────────────────────────────────

if __name__ == "__main__":
    result = run_scratchpad_agent(
        task=(
            "A company has revenue of $10M, costs of $7M, and a tax rate of 25%. "
            "What is their net profit, and what percentage of revenue does it represent?"
        )
    )

    print("\n=== FINAL ANSWER ===")
    print(result["answer"])

    print("\n=== FINAL SCRATCHPAD STATE ===")
    print(result["scratchpad"].to_context())
    print(f"\nCompleted in {result['steps']} step(s).")
```

### Compression in action

Here is what the scratchpad looks like before and after a compression at step 6 of a long task:

**Before compression (12 working notes):**
```
## Working Notes
- Checked the order database: order #4421 exists
- Status field shows "pending_shipment" — not yet dispatched
- Carrier API call returned 404 for tracking number TRK-9981
- TRK-9981 may have been generated before physical pickup
- Checked pickup schedule: next pickup window is tomorrow 9am
- Customer placed order 3 days ago — within normal window
- Email notification was sent on day 1 with estimated delivery
- ... (5 more notes)
```

**After compression (2 established facts + 5 retained notes):**
```
## Established Facts
- Order #4421 exists in the database with status "pending_shipment."
- [compressed] Tracking number TRK-9981 is pre-generated; physical pickup 
  is scheduled for tomorrow at 9am. Order is within normal delivery window.

## Working Notes
- Email notification was sent on day 1 with estimated delivery
- Customer account shows no prior complaints
- Escalation threshold: 5 business days; we are at day 3
- Need to check if a delivery estimate was shown at checkout
- Should draft a proactive status email to the customer
```

The compression kept what the next step needs (where we are in the resolution) and dropped what it doesn't (the discovery path that led there).

---

## Where it breaks / what it is not

**The compression step itself can lose information.** The LLM summarizing old notes can drop a nuance that matters. Mitigate by keeping more notes before compression (raise `COMPRESSION_THRESHOLD_NOTES`) or by compressing into multiple facts instead of one. The tension between compression fidelity and capacity is real.

**Structured output requires prompt discipline.** The `ANSWER:` / `ADD_FACT:` protocol only works if the model follows it consistently. With weaker models, you'll see malformed lines (`ADD FACT:` without the colon, or free-form text that bypasses the structure entirely). Add validation and a retry loop for production.

**Working memory is not long-term memory.** The scratchpad solves the within-session capacity problem. It doesn't solve the cross-session problem. Once the session ends, the scratchpad is gone. If you need facts to persist, promote them to external semantic or episodic store before the session closes.

**Compression is lossy.** This is intentional — you cannot keep everything at zero cost. But "intentionally lossy" is very different from "accidentally lossy." The scratchpad pattern gives you control over what is discarded. Random truncation does not.

---

## Try it yourself

**Exercise 1 — Check your understanding:**
Given a scratchpad with 15 working notes, 3 established facts, and 2 open questions, `token_estimate()` returns 3200. The two thresholds are `COMPRESSION_THRESHOLD_NOTES = 10` and `COMPRESSION_THRESHOLD_TOKENS = 2000`. Which threshold fires first? What does the scratchpad look like after compression assuming the compression summary produces two facts?

**Exercise 2 — Apply it:**
Run `run_scratchpad_agent` on a multi-step arithmetic problem (e.g., compute compound interest over 10 years). Inspect the scratchpad state at each step. At what step does it first write an `ADD_FACT:` vs an `ADD_NOTE:`? What does that reveal about the model's confidence at each stage?

**Exercise 3 — Stretch:**
The current `apply_scratchpad_update` parses one command per line. A more expressive protocol would allow the agent to emit a JSON block like `{"add_fact": "...", "resolve": "...", "answer": "..."}` in a single structured turn. Redesign `apply_scratchpad_update` to parse this JSON format. What are the tradeoffs vs. the line-by-line protocol?

<details>
<summary>Answer for Exercise 1</summary>

Both thresholds are checked with `or`, so the one that *fires first in the agent loop* is whichever becomes true first. In the scenario given, `COMPRESSION_THRESHOLD_TOKENS = 2000` fires first because `token_estimate() = 3200 > 2000` is already true — even if `len(working_notes) = 15 > 10` is also true, they fire at the same check in the same step.

After compression with `keep_count = 5` (half of threshold 10):
- Old notes (10 oldest) → summarized into 2 new established facts → added to established_facts (now 5 total)
- Fresh notes (5 most recent) → retained
- Result: 5 established facts, 2 open questions, 5 working notes

Token estimate after compression will depend on how long the compressed facts are, but the scratchpad is substantially smaller.
</details>

---

## Connect it back

[Day 23](./day-23-memory-taxonomy.md) established that in-context memory is the cheapest to write but the most constrained by capacity. This day shows how to manage that constraint deliberately.

[Tomorrow (Day 25)](./day-25-episodic-memory-retrieval.md) moves to the next store: external episodic memory. Instead of managing working memory within a session, Day 25 builds the mechanism for an agent to carry experience *across* sessions.

**One question you can now answer that you couldn't this morning:** A ReAct agent running for 30 steps hits the context limit at step 28 and starts producing incoherent output. How would you retrofit the scratchpad pattern onto the ReAct loop to prevent this? What would the compression trigger be?

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
Nye et al., "Show Your Work: Scratchpads for Intermediate Computation with Language Models" (arXiv:2112.00114, 2021).
The original scratchpad paper. Section 2 shows how intermediate computation tokens improve accuracy on algorithmic tasks. The formal treatment is about arithmetic, but the principle — explicit intermediate workspace → better final output — generalizes to every multi-step reasoning setting.

**If you want the deep version:**
- Anthropic, *Building Effective Agents* (Dec 2024) — "The augmented LLM" section, specifically the memory subsection. Anthropic's production framing of working memory management and when to use in-context vs. external stores.

---

## Navigation

← **Previous:** [Day 23 — Memory Taxonomy](./day-23-memory-taxonomy.md)
→ **Next:** [Day 25 — Episodic Memory & Retrieval](./day-25-episodic-memory-retrieval.md)
