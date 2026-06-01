# Day 28 — Critic-Actor Pattern

> **Today's one idea:** A separate critic agent evaluates the actor's output before it is accepted, using three verdicts — ACCEPT, REVISE, REJECT — to gate quality without requiring human review.
> **Reading time:** ~40 min · **Prereqs:** [Day 15 (Self-Refine)](../../03-self-improvement/days/day-15-self-refine.md), [Day 27 (Orchestrator-Worker)](./day-27-orchestrator-worker.md)
> **Primary sources for today:** Bai et al., *Constitutional AI: Harmlessness from AI Feedback* (arXiv:2212.08073, 2022) · Shinn et al., *Reflexion* (arXiv:2303.11366, 2023) — contrast with Reflexion's self-evaluation.

---

## The hook

Every professional writer has an editor. Not a proofreader — an editor. The editor doesn't write a word of the piece. They read what the writer produced, form an independent judgment about what's working and what isn't, and deliver specific, actionable feedback. "The third paragraph buries the lede." "You've defined this term twice." "The argument falls apart in section four because you assume X, but you haven't established X."

The key word is *independent*. The editor hasn't been living with the draft for three weeks. They haven't made the decisions that led to the structure. They have no ego investment in the choices the writer made. This independence is the entire source of their value — it's precisely why the writer can't just re-read their own draft and get the same quality of feedback. The writer knows what they meant to say. The editor only knows what's actually on the page.

The best published work is the product of that adversarial collaboration. Not of a single person's unchecked first draft — no matter how talented they are.

Self-Refine (Day 15) asks the same agent to critique its own output. It works. But it has a structural ceiling: the agent that generated the output is also the agent evaluating it, with the same context, the same priors, the same blind spots. The Critic-Actor pattern breaks this by separating the roles into two distinct agents. One produces. One judges. They never share a context window.

---

## Building the intuition

### Self-Refine vs Critic-Actor: the same loop, a different agent

[Day 15](../../03-self-improvement/days/day-15-self-refine.md) built a loop that runs within a single agent: generate → critique → refine → repeat. The critique and the refinement both happen in the same LLM instance, reading the same conversation history. This gives the loop coherence (the critic "remembers" what the generator was trying to do) but limits objectivity (the critic shares the generator's blind spots).

Critic-Actor runs the same loop structure but with a crucial architectural change: the critic is a separate LLM call, starting from a clean context. It receives the original task and the actor's output — nothing else. It has never seen the actor's intermediate reasoning, its discarded drafts, or the constraints it was working under. It evaluates the output as a finished product against stated criteria, not as a work-in-progress against internal intentions.

The practical effect: the critic catches different errors. A Self-Refine critic tends to catch errors that are visible in the context (logical inconsistencies the generator can see when prompted to look). A Critic-Actor critic catches errors that require fresh eyes — the kind of thing you only notice when you come to a document without knowing what the author was trying to do.

### The three verdicts: why ternary beats binary

A binary pass/fail evaluator creates a blunt instrument. "Fail" covers everything from "one word is slightly imprecise" to "this is fundamentally wrong and none of it can be salvaged." When the actor receives a fail verdict, it has no information about whether to make a small targeted edit or start from scratch. It will probably do something in between — making moderate edits that fix neither the small things nor the large ones.

Three verdicts make the feedback structurally actionable:

**ACCEPT** — the output meets all criteria. No changes needed. The loop terminates. This is the success state.

**REVISE** — the output has specific, fixable issues. The actor receives the critique and produces an improved version of the same output. It preserves the parts that were correct. This is the most common verdict in practice, and the most valuable: targeted revision almost always outperforms starting over when the foundation is sound.

**REJECT** — the output has a fundamental problem that targeted revision cannot fix. Wrong structure, wrong approach, wrong interpretation of the task. The actor starts from scratch in the next round, producing a new output without reference to the rejected one. This is expensive (you've paid for a failed attempt) but sometimes necessary — polishing a broken foundation produces polished rubble.

The ternary verdict answers the question every editor faces: "Is this salvageable, or does it need to go back to the drawing board?" Making that decision explicit — in a structured field that the actor's code can read — is what allows the loop to behave differently in each case.

### Critique quality is the bottleneck

The verdict is only as useful as the reasoning behind it. "REVISE — this could be better" is not a usable critique. The actor has no idea what to change. It will change something, probably not the right thing, and the next critique will likely reach the same verdict for the same reason.

Specific, actionable critique has three properties:

1. **It names the exact location of the problem.** "Paragraph 3" not "the middle section." "The definition in line 2" not "the explanation."

2. **It says why it is a problem in terms of the stated criteria.** "This claim is technically accurate but uses jargon that the target audience (software engineers without ML background) won't know" is usable. "This is unclear" is not.

3. **It suggests what a fix looks like, without writing it.** "Replace the formal definition with an analogy" is a suggestion that leaves the writing to the actor. "The attention mechanism computes a weighted sum of values where the weights are determined by query-key similarity" (just rewriting the thing) bypasses the actor entirely and defeats the purpose of the loop.

The `CriticAgent` in today's code receives an explicit `criteria` list and is instructed to evaluate against those criteria specifically. Vague criteria produce vague critique. "Under 200 words" is a testable criterion. "Clear and engaging" is not, unless you define what counts as clear and engaging for this specific audience.

### When to use Critic-Actor vs Self-Refine

This is the practical decision you'll face repeatedly. The heuristic:

**Use Self-Refine when:** you're iterating within a session and the task is creative or generative (writing, code drafts, plans). The in-context coherence of Self-Refine is an asset — the critique understands the generator's intent and can produce targeted improvements quickly. Self-Refine is cheaper (one model, one context) and faster.

**Use Critic-Actor when:** you need a quality gate before output is delivered to a user or downstream system. The independence of the critic is the asset here — you want the critique to be uninformed by the actor's intentions. Use it when the cost of a bad output reaching the user is high enough to justify the extra LLM call per round.

The two patterns compose naturally: you can use Self-Refine inside the actor (the actor refines its draft internally before submitting to the critic) and Critic-Actor as the final gate. This is the editorial model: the writer revises their own draft before sending it to the editor. The editor still catches what the writer's self-revision missed.

---

## The formal picture

### The Critic-Actor loop

```
Task ──→ Actor ──────────────────────────────────→ output
              ↑                                        │
              │                                        ▼
              │                                    Critic
              │                                        │
              │                            ┌───────────┼───────────┐
              │                            ▼           ▼           ▼
              │                          ACCEPT      REVISE      REJECT
              │                            │           │           │
              │                            ▼           │           │
              │                        return      Actor with  Actor with
              └────────────────────────────────── feedback ─── no context
                                                 (targeted)    (start over)
```

The ACCEPT path terminates immediately. The REVISE path feeds the critique back to the actor — the actor's `produce` method receives the `CritiqueResult` and uses `specific_issues` and `suggestions` to generate a targeted revision. The REJECT path resets: the actor's `produce` method is called with `critique=None`, exactly as in the first round, and the actor starts fresh without seeing the rejected output.

### Data structures

```
Verdict (enum)
├── ACCEPT   — terminate, return current output
├── REVISE   — actor revises with feedback
└── REJECT   — actor restarts from scratch

CritiqueResult
├── verdict: Verdict
├── reasoning: str         (one sentence summary — logged for audit)
├── specific_issues: list  (each issue is a targeted, locatable problem)
└── suggestions: list      (each suggestion is actionable but not prescriptive)

ActorAgent
└── system: str            (system prompt defines actor's domain expertise)

CriticAgent
└── criteria: list[str]    (explicit evaluation checklist; drives critique specificity)
```

### A complete working implementation

```python
import json
import anthropic
from dataclasses import dataclass, field
from enum import Enum

client = anthropic.Anthropic()


def llm(prompt: str, system: str = "", max_tokens: int = 1024) -> str:
    kwargs = {
        "model": "claude-3-5-sonnet-20241022",
        "max_tokens": max_tokens,
        "messages": [{"role": "user", "content": prompt}],
    }
    if system:
        kwargs["system"] = system
    return client.messages.create(**kwargs).content[0].text.strip()


class Verdict(Enum):
    ACCEPT = "ACCEPT"
    REVISE = "REVISE"
    REJECT = "REJECT"


@dataclass
class CritiqueResult:
    verdict: Verdict
    reasoning: str
    specific_issues: list = field(default_factory=list)   # list[str]
    suggestions: list = field(default_factory=list)        # list[str]


class ActorAgent:
    def __init__(self, system_prompt: str = ""):
        self.system = system_prompt or "You are a careful, thorough assistant."

    def produce(self, task: str, critique: object = None) -> str:  # critique: CritiqueResult | None
        if critique is None:
            return llm(task, system=self.system, max_tokens=1024)

        issues = "\n".join(f"- {i}" for i in critique.specific_issues)
        suggestions = "\n".join(f"- {s}" for s in critique.suggestions)
        return llm(
            f"Task: {task}\n\n"
            f"Your previous attempt had these issues:\n{issues}\n\n"
            f"Suggestions:\n{suggestions}\n\n"
            f"Produce an improved version that addresses all issues.",
            system=self.system,
            max_tokens=1024,
        )


class CriticAgent:
    def __init__(self, criteria: list):  # list[str]
        self.criteria = criteria

    def evaluate(self, task: str, output: str) -> CritiqueResult:
        criteria_text = "\n".join(f"- {c}" for c in self.criteria)
        raw = llm(
            f"Task: {task}\n\n"
            f"Output to evaluate:\n{output}\n\n"
            f"Evaluation criteria:\n{criteria_text}\n\n"
            f"Return JSON:\n"
            f'{{"verdict": "ACCEPT" | "REVISE" | "REJECT",\n'
            f' "reasoning": "one sentence",\n'
            f' "specific_issues": ["issue1", "issue2"],\n'
            f' "suggestions": ["suggestion1"]}}\n\n'
            f"ACCEPT: meets all criteria. "
            f"REVISE: fixable with specific changes. "
            f"REJECT: fundamentally wrong, start over.",
            max_tokens=512,
        )
        start, end = raw.find("{"), raw.rfind("}") + 1
        data = json.loads(raw[start:end])
        return CritiqueResult(
            verdict=Verdict(data["verdict"]),
            reasoning=data.get("reasoning", ""),
            specific_issues=data.get("specific_issues", []),
            suggestions=data.get("suggestions", []),
        )


def critic_actor_loop(
    task: str,
    actor: ActorAgent,
    critic: CriticAgent,
    max_rounds: int = 3,
) -> dict:
    critique = None
    output = ""
    for round_num in range(1, max_rounds + 1):
        output = actor.produce(task, critique)
        critique = critic.evaluate(task, output)
        print(f"[Round {round_num}] {critique.verdict.value} — {critique.reasoning}")

        if critique.verdict == Verdict.ACCEPT:
            return {"output": output, "rounds": round_num, "verdict": "ACCEPT"}
        if critique.verdict == Verdict.REJECT and round_num < max_rounds:
            critique = None  # full reset: actor starts from scratch next round

    return {
        "output": output,
        "rounds": max_rounds,
        "verdict": critique.verdict.value if critique else "TIMEOUT",
    }


if __name__ == "__main__":
    actor = ActorAgent(system_prompt="You write clear, accurate technical explanations.")
    critic = CriticAgent(criteria=[
        "Technically accurate — no false claims",
        "Concrete examples for every abstract concept",
        "Under 200 words",
        "No jargon without definition",
    ])
    result = critic_actor_loop(
        task=(
            "Explain what a transformer attention head does, "
            "for a software engineer who has not studied ML."
        ),
        actor=actor,
        critic=critic,
        max_rounds=3,
    )
    print("\n=== ACCEPTED OUTPUT ===")
    print(result["output"])
    print(f"\nCompleted in {result['rounds']} round(s). Final verdict: {result['verdict']}")
```

### Tracing a full run

Here is a representative trace for the attention head explanation task. Reading a trace is the fastest way to build intuition for where the loop helps and where it stalls.

**Round 1 — Actor produces:**

```
A transformer attention head is a mechanism that lets a model decide which parts of the
input sequence to focus on when producing each output token. It computes three vectors
for each token — query (Q), key (K), and value (V) — using learned weight matrices.
The attention score between two tokens is the dot product of their Q and K vectors,
scaled by √d_k to prevent gradient saturation, then passed through softmax to produce
a probability distribution. The final output is a weighted sum of the V vectors,
weighted by these attention scores.
```

**Round 1 — Critic evaluates:** `REVISE — technically accurate but uses "dot product", "softmax", "gradient saturation", and "√d_k" without definition; no concrete example.`

**Round 2 — Actor revises (receives issues + suggestions):**

```
An attention head is like a spotlight that decides which words in a sentence to pay
attention to when processing each word. When processing the word "it" in "The cat sat
because it was tired," the head figures out that "it" refers to "cat" (not "sat" or
"tired") by computing similarity scores between "it" and every other word.
Under the hood, each word gets three numerical fingerprints (query, key, value). The
attention score between two words is how similar their query and key fingerprints are.
High similarity → the head pays more attention to that word when producing the output.
The output for each word is then a blend of all other words' value fingerprints,
weighted by those scores — so "it" ends up carrying information from "cat."
One attention head captures one type of relationship (e.g., pronoun references). A
transformer has many heads running in parallel, each catching different relationships.
```

**Round 2 — Critic evaluates:** `ACCEPT — technically accurate, concrete pronoun example, all terms defined, 160 words.`

The loop ran for two rounds. The first output was technically correct but violated two criteria (jargon without definition, no examples). The revision fixed both. Note what the critic did not do: it did not rewrite the explanation. It identified what was missing. The actor did the writing.

### Wiring in an audit log

In production, you want a record of every verdict and critique. A minimal audit log alongside the loop:

```python
def critic_actor_loop_with_audit(
    task: str,
    actor: ActorAgent,
    critic: CriticAgent,
    max_rounds: int = 3,
) -> dict:
    audit = []
    critique = None
    output = ""

    for round_num in range(1, max_rounds + 1):
        output = actor.produce(task, critique)
        critique = critic.evaluate(task, output)

        audit.append({
            "round": round_num,
            "verdict": critique.verdict.value,
            "reasoning": critique.reasoning,
            "issues_count": len(critique.specific_issues),
            "output_length": len(output.split()),
        })

        print(f"[Round {round_num}] {critique.verdict.value} — {critique.reasoning}")

        if critique.verdict == Verdict.ACCEPT:
            return {"output": output, "rounds": round_num, "verdict": "ACCEPT", "audit": audit}
        if critique.verdict == Verdict.REJECT and round_num < max_rounds:
            critique = None

    return {
        "output": output,
        "rounds": max_rounds,
        "verdict": critique.verdict.value if critique else "TIMEOUT",
        "audit": audit,
    }
```

The `audit` list gives you the data to answer post-hoc questions: how many rounds did tasks require on average? Which criteria caused the most REVISEs? Are REJECTs correlated with task type or task length? This data drives criteria calibration.

---

## Where it breaks / what it is not

**The critic can be inconsistent across rounds.** LLM outputs are non-deterministic. The same output, evaluated twice, may receive ACCEPT in one call and REVISE in the next. In practice, this means the loop can escape a stuck state by luck (a lenient critique in round 3 accepts something round 1 would have rejected) or get trapped in one (a severe critique keeps REVISing something that is actually fine). The fix for production: lower temperature on the critic, or use structured evaluation with explicit thresholds rather than free-text JSON.

**REJECT in round N means round N+1 is pure waste if the actor makes the same mistake.** When the actor receives `critique=None` on a restart, it has no information about why the previous attempt failed. If the first attempt failed because the actor consistently misunderstands the task, it will make the same mistake again from scratch. A better REJECT handler passes a brief high-level note to the next attempt ("previous attempt was rejected because of fundamental structural issue X — avoid approach Y") without exposing the full rejected output.

**The criteria are static — they don't adapt to what the actor struggles with.** If the actor consistently fails criterion 3 but passes all others, the critic spends effort re-evaluating criteria 1, 2, and 4 every round even though they're always satisfied. Adaptive criteria — generated fresh based on what failed last round — would make the critic more efficient. The current implementation doesn't do this.

**Critic-Actor is not Self-Refine.** This distinction matters enough to state twice. Self-Refine (Day 15) is a single agent critiquing its own output within the same context. The critic in Self-Refine has access to the generator's reasoning trace — it knows what the generator was trying to do. The Critic-Actor critic has none of that. It evaluates the output as a finished product. This is not a limitation — it is the point. The independence is the value. But it means the two patterns have different failure modes and different performance profiles. Don't conflate them when choosing which to use.

---

## Try it yourself

**Exercise 1 — Check your understanding:**

Trace through `critic_actor_loop` for this scenario: round 1 verdict is REVISE, round 2 verdict is REJECT, round 3 verdict is REVISE. What does the actor receive as `critique` in each round? When does `critique = None` get set, and why? Does the loop terminate, and if so, with what final state?

<details>
<summary>Hint for Exercise 1</summary>

Walk through the loop body for each round_num. Pay attention to the two `if` branches at the bottom: one for ACCEPT, one for REJECT. What does the REJECT branch do? What does the REVISE branch do — or more precisely, what does it *not* do? And when `max_rounds` is exhausted without an ACCEPT, what does the function return?
</details>

<details>
<summary>Worked solution for Exercise 1</summary>

**Round 1 (round_num = 1):**
- `critique` starts as `None`
- `actor.produce(task, None)` — first attempt, no feedback
- Critic returns REVISE
- Neither `if` branch triggers (REVISE is not ACCEPT, and the REJECT branch requires `verdict == Verdict.REJECT`)
- `critique` is now the REVISE `CritiqueResult`

**Round 2 (round_num = 2):**
- `actor.produce(task, critique)` — revision, actor receives the round 1 critique
- Critic returns REJECT
- The REJECT branch fires: `critique = None`
- Loop continues (round_num < max_rounds = 3)

**Round 3 (round_num = 3):**
- `critique` is `None` (reset by round 2's REJECT)
- `actor.produce(task, None)` — fresh start, no feedback, exactly like round 1
- Critic returns REVISE
- Neither `if` branch triggers — but now the `for` loop exhausts (round_num = max_rounds = 3)

The function falls through to the final `return`:
```python
{"output": output, "rounds": 3, "verdict": "REVISE", "audit": ...}
```

The loop terminates with a REVISE verdict (not ACCEPT, not REJECT). This is a "max rounds exceeded" state — the final output is the actor's round 3 attempt, which the critic wanted revised but never got the chance. The caller receives a non-accepted output and must decide whether to deliver it anyway or escalate.

The key insight: the REJECT → `critique = None` reset is what causes round 3 to start fresh. Without that reset, round 3 would have received the round 2 REJECT critique — which may be different from what the round 1 critique covered — and the actor would be trying to address two rounds' worth of feedback simultaneously.
</details>

---

**Exercise 2 — Apply it:**

Run the code with the provided attention head explanation task. Then run it again with a harder task: "Explain what gradient descent does, for a small business owner who has never written code."

For the harder task, observe:
- Does the actor's first attempt use jargon?
- Does the critic catch it and produce specific issues?
- Does the actor's revision actually fix the jargon, or does it introduce different jargon?

Then modify the criteria list to add a fifth criterion: "Uses an analogy from business or daily life." Does adding this criterion change the actor's first-round output, the critic's verdict, or both?

<details>
<summary>Hint for Exercise 2</summary>

The criteria list is passed to `CriticAgent.__init__`. To add a fifth criterion, modify the list literal. The criterion change affects the critic's evaluation prompt — the actor never sees the criteria directly. This means the criterion change will affect what the critic evaluates, which may or may not change the actor's first draft (it won't, since the actor doesn't see the criteria). It will change what the critic reports as issues, which will then influence the actor's revision.
</details>

<details>
<summary>Worked solution for Exercise 2</summary>

The five-criteria critic for the gradient descent task typically produces a round 1 verdict of REVISE with issues like:

```
specific_issues: [
  "Uses 'parameters' and 'weights' without defining them for a non-technical audience",
  "No business or daily-life analogy provided (criterion 5)",
  "Abstract description of 'minimizing loss' without concrete meaning"
]
suggestions: [
  "Replace 'parameters' with 'the settings the model adjusts'",
  "Add an analogy: tuning a pricing model by trial and error, adjusting prices up or down until profit is maximized",
  "Explain 'loss' as 'how wrong the prediction was'"
]
```

The actor's revision incorporates the pricing analogy and replaces technical terms. A typical round 2 output for a non-coder audience:

```
Gradient descent is how a machine learning model learns from its mistakes — similar to
how you'd tune your pricing strategy. Imagine you set a product price and track weekly
profit. If profit goes up when you raise the price, raise it again next week. If it
goes down, lower it. Repeat, adjusting in smaller increments as you get close to the
best price.

The model does the same thing with its internal settings. It makes predictions, measures
how wrong they are, and adjusts each setting in the direction that makes the predictions
more accurate. After thousands of adjustments, it converges on settings that produce
good predictions.

The math that tells it which direction to adjust is called the gradient — think of it
as a compass that always points toward "less wrong."
```

Adding criterion 5 did not change the actor's first draft (the actor doesn't see the criteria). It changed what the critic reported as missing, which the actor then fixed. This is the correct behavior — criteria belong to the critic, not the actor.
</details>

---

**Exercise 3 — Stretch:**

The current REJECT handling resets `critique = None`, giving the actor no information about why the previous attempt failed. Implement an improved REJECT handler: on REJECT, pass a short, high-level summary of the fundamental flaw to the actor's next attempt — but do not pass the specific issues or suggestions from the rejected attempt (to avoid anchoring the actor to the wrong approach).

Your implementation should add a `reject_reason` field to `CritiqueResult` and modify `ActorAgent.produce` to use it when `critique.verdict == Verdict.REJECT`.

<details>
<summary>Hint for Exercise 3</summary>

Add `reject_reason: str = ""` to `CritiqueResult`. Populate it from the critic's JSON response (add a `"reject_reason"` field to the critic prompt). In `ActorAgent.produce`, add a check: if `critique is not None` and `critique.verdict == Verdict.REJECT`, use a different prompt template that mentions the reject reason but gives no specific issues or suggestions.
</details>

<details>
<summary>Worked solution for Exercise 3</summary>

Modified `CritiqueResult`:

```python
@dataclass
class CritiqueResult:
    verdict: Verdict
    reasoning: str
    specific_issues: list = field(default_factory=list)
    suggestions: list = field(default_factory=list)
    reject_reason: str = ""  # high-level flaw summary; populated only on REJECT
```

Modified critic prompt (add one line to the JSON schema in `evaluate`):

```python
f"Return JSON:\n"
f'{{"verdict": "ACCEPT" | "REVISE" | "REJECT",\n'
f' "reasoning": "one sentence",\n'
f' "specific_issues": ["issue1"],\n'
f' "suggestions": ["suggestion1"],\n'
f' "reject_reason": "fundamental flaw in one sentence; empty string unless REJECT"}}\n\n'
```

And the parse:
```python
return CritiqueResult(
    verdict=Verdict(data["verdict"]),
    reasoning=data.get("reasoning", ""),
    specific_issues=data.get("specific_issues", []),
    suggestions=data.get("suggestions", []),
    reject_reason=data.get("reject_reason", ""),
)
```

Modified `ActorAgent.produce`:

```python
def produce(self, task: str, critique: object = None) -> str:
    if critique is None:
        return llm(task, system=self.system, max_tokens=1024)

    if critique.verdict == Verdict.REJECT:
        return llm(
            f"Task: {task}\n\n"
            f"Your previous attempt was rejected for this fundamental reason:\n"
            f"{critique.reject_reason}\n\n"
            f"Start fresh with a completely different approach that avoids this problem.",
            system=self.system,
            max_tokens=1024,
        )

    issues = "\n".join(f"- {i}" for i in critique.specific_issues)
    suggestions = "\n".join(f"- {s}" for s in critique.suggestions)
    return llm(
        f"Task: {task}\n\n"
        f"Your previous attempt had these issues:\n{issues}\n\n"
        f"Suggestions:\n{suggestions}\n\n"
        f"Produce an improved version that addresses all issues.",
        system=self.system,
        max_tokens=1024,
    )
```

And in `critic_actor_loop`, remove the `critique = None` reset on REJECT — the actor now handles REJECT itself via the verdict check:

```python
if critique.verdict == Verdict.ACCEPT:
    return {"output": output, "rounds": round_num, "verdict": "ACCEPT"}
# No special handling for REJECT needed here — ActorAgent.produce handles it
```

The result: on REJECT, the actor receives a single sentence explaining the fundamental problem ("the explanation assumes the reader knows what a neural network is, which violates the stated audience constraint") and is explicitly directed to start fresh. It doesn't receive the specific issues from the failed attempt, which would anchor it to the wrong approach.
</details>

---

## Connect it back

[Day 15 (Self-Refine)](../../03-self-improvement/days/day-15-self-refine.md) introduced the generate → critique → refine loop. Today's Critic-Actor pattern runs the same loop but breaks it across two separate agents. The structural difference is small; the behavioral difference is significant. When you choose between them, the decision axis is: do you want coherence (same agent, shared context) or independence (separate agent, fresh context)?

[Day 27 (Orchestrator-Worker)](./day-27-orchestrator-worker.md) delegated execution to specialists. Today's pattern delegates evaluation to a specialist. The Critic-Actor critic is a worker whose specialty is judgment rather than production. You could implement Critic-Actor entirely within an Orchestrator-Worker system: the orchestrator dispatches "Actor" and "Critic" as named workers, routes output between them, and decides when to synthesize. The patterns compose.

Tomorrow: Peer Debate. Where Critic-Actor uses one critic and one actor, Peer Debate uses multiple agents arguing opposing positions over several rounds. The critic in Critic-Actor is evaluating; the debaters in Peer Debate are advocating. Different pressure, different failure modes, and better suited to contested factual questions where no single answer is obviously right.

**One question you can now answer that you couldn't this morning:** A colleague proposes using Self-Refine for a customer-facing content generation pipeline. You suggest Critic-Actor instead. What is the single most important reason?

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
Bai et al., *Constitutional AI: Harmlessness from AI Feedback* (arXiv:2212.08073, 2022) — Section 3 ("RLHF from AI Feedback"). The key idea: a separate AI critic evaluates outputs against a fixed list of principles and produces preference judgments. Today's `CriticAgent.criteria` list is a direct analogue of the CAI "constitution." Reading Section 3 shows the industrial-scale version of the pattern you just implemented.

**If you want the deep version:**
Shinn et al., *Reflexion* (arXiv:2303.11366, 2023) — Section 3, specifically the "self-reflection" component. Reflexion uses self-evaluation (the same agent reflects on its own trajectory). Comparing Section 3 of Reflexion against today's `CriticAgent` code makes the self-evaluation vs external-evaluation distinction concrete. The architectural choice appears as a minor implementation detail in the paper but has large practical consequences — which the exercises above should now make clear.

---

## Navigation

← **Previous:** [Day 27 — Orchestrator-Worker](./day-27-orchestrator-worker.md)
→ **Next:** [Day 29 — Peer Debate](./day-29-peer-debate.md)
