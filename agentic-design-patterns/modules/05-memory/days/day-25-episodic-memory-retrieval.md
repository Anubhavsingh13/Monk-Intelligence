# Day 25 — Episodic Memory & Retrieval

> **Today's one idea:** An agent that stores past trajectories and retrieves the most relevant ones before tackling a new task starts every task knowing what worked before — turning experience into compounding capability instead of discarded history.
> **Reading time:** ~40 min · **Prereqs:** Day 23 (Memory Taxonomy), Day 11 (Reflexion), Day 9 (ReAct implementation)
> **Primary source for today:** Park et al., "Generative Agents" (arXiv:2304.03442, 2023) — memory stream architecture · Lewis et al., "Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks" (arXiv:2005.11401, 2020)

---

## The hook

A doctor who has treated 1000 patients with a rare condition is better at diagnosis than one who has treated 10 — not because they got smarter, but because they have more *relevant experience* to draw from. The key word is *relevant*: a doctor with 1000 cases in an unrelated specialty is no better on this patient than the one with 10.

Agent episodic memory has the same structure. The value of stored past experience is determined by two things: **what you stored** (the episode quality) and **what you retrieve** (the retrieval quality). A huge episodic store with bad retrieval is noise. A small episodic store with excellent retrieval is a superpower.

This day builds both halves: a store that captures the right information from each task, and a retrieval pipeline that surfaces the most relevant past experience for the current one.

---

## Building the intuition

### What is an episode?

An episode is a structured record of one completed task. At minimum it contains:

```
Episode {
    task:       what the agent was asked to do
    trajectory: the sequence of steps taken (T/A/O trace)
    outcome:    what the final result was
    success:    whether the result was correct / accepted
    metadata:   timestamp, tags, task domain
}
```

The trajectory is the most expensive part — it captures the reasoning path, not just the result. Storing the reasoning path is what makes retrieval useful: it gives the agent a replayable example of *how* to approach a similar task, not just *what the answer was*.

### Four retrieval strategies

Retrieval is not one-size-fits-all. Different tasks call for different strategies:

| Strategy | When to use | How it works |
|----------|-------------|--------------|
| **Similarity** | Finding relevant prior approaches | Embed current task → cosine similarity → top-k |
| **Recency** | Recent episodes reflect current context better | Return most recent k episodes |
| **Success-filtered** | Providing few-shot positive examples | Return top-k *successful* episodes by similarity |
| **Contrastive** | Teaching from both success and failure | Return one similar success + one similar failure |

**Similarity** is the workhorse. It retrieves episodes whose task description is semantically close to the current task, regardless of when they happened. Requires an embedding model and a vector store.

**Recency** is appropriate when the world changes — what worked six months ago may not work today. Useful for domains with shifting constraints (API endpoints, pricing rules, regulations).

**Success-filtered** maximizes the signal-to-noise ratio when you only want to see what worked. Risk: if the agent has only failed at a task type, you get no retrieval and lose the learning.

**Contrastive** is the most information-dense strategy. A success example shows the agent what to do; a failure example shows what to avoid. Reflexion (Day 11) uses this implicitly — verbal memory contains lessons from failures, while the task itself provides the success target.

### The retrieval-augmented agent loop

The integration with the agent loop adds two steps: retrieve before, store after.

```
Standard ReAct loop:
  Task → [Thought → Action → Observation]* → Answer

Episodic-augmented loop:
  Task
    ↓
  Retrieve similar past episodes
    ↓
  Inject as context ("here's how similar tasks went")
    ↓
  [Thought → Action → Observation]*
    ↓
  Answer
    ↓
  Store episode (task, trajectory, outcome, success)
```

The inject step is critical. You are not just showing the agent the answer from a past episode — you are showing it the *reasoning path*. This is few-shot prompting over the agent's own history.

---

## The formal picture

### Implementation

```python
import json
import os
from dataclasses import dataclass, field
from typing import Any
import anthropic

client = anthropic.Anthropic()


def llm(prompt: str, max_tokens: int = 1024) -> str:
    response = client.messages.create(
        model="claude-3-5-sonnet-20241022",
        max_tokens=max_tokens,
        messages=[{"role": "user", "content": prompt}]
    )
    return response.content[0].text.strip()


# ── Episode data structure ─────────────────────────────────────────────────────

@dataclass
class Episode:
    task:       str
    trajectory: list[str]    # T/A/O steps, each as a string
    outcome:    str
    success:    bool
    tags:       list[str] = field(default_factory=list)

    def to_dict(self) -> dict:
        return {
            "task":       self.task,
            "trajectory": self.trajectory,
            "outcome":    self.outcome,
            "success":    self.success,
            "tags":       self.tags,
        }

    @staticmethod
    def from_dict(d: dict) -> "Episode":
        return Episode(**d)


# ── Episodic memory store ──────────────────────────────────────────────────────

class EpisodicMemory:
    """
    File-backed episodic memory store with LLM-based retrieval.
    Production replacement: swap _score_relevance for a vector embedding + cosine similarity.
    """

    def __init__(self, path: str = "episodic_memory.json"):
        self.path = path
        self._episodes: list[Episode] = self._load()

    def _load(self) -> list[Episode]:
        if not os.path.exists(self.path):
            return []
        with open(self.path) as f:
            return [Episode.from_dict(e) for e in json.load(f)]

    def _save(self) -> None:
        with open(self.path, "w") as f:
            json.dump([e.to_dict() for e in self._episodes], f, indent=2)

    def store(self, episode: Episode) -> None:
        """Add a completed episode to memory."""
        self._episodes.append(episode)
        self._save()

    def size(self) -> int:
        return len(self._episodes)

    # ── Retrieval strategies ───────────────────────────────────────────────────

    def retrieve_by_similarity(
        self, task: str, top_k: int = 3, success_only: bool = False
    ) -> list[Episode]:
        """
        Retrieve the top-k most relevant episodes using LLM-based scoring.

        Production note: replace with:
          embedding = embed(task)
          scores = [cosine(embedding, embed(e.task)) for e in self._episodes]
        This gives O(1) scoring per episode vs O(n) LLM calls here.
        """
        pool = [e for e in self._episodes if not success_only or e.success]
        if not pool:
            return []
        if len(pool) <= top_k:
            return pool

        candidates = "\n".join(
            f"Episode {i}: {e.task}" for i, e in enumerate(pool)
        )
        raw = llm(
            f"Current task: {task}\n\n"
            f"Rate each past episode's relevance to the current task on a scale "
            f"of 0.0 to 1.0. Return ONLY comma-separated scores in order, "
            f"no other text. Example: 0.9,0.2,0.7\n\n"
            f"Past episodes:\n{candidates}",
            max_tokens=128,
        )
        try:
            scores = [float(s.strip()) for s in raw.split(",")]
            if len(scores) != len(pool):
                raise ValueError("Score count mismatch")
            ranked = sorted(zip(scores, pool), key=lambda x: x[0], reverse=True)
            return [ep for _, ep in ranked[:top_k]]
        except (ValueError, IndexError):
            # Fallback: return most recent k
            return pool[-top_k:]

    def retrieve_by_recency(self, top_k: int = 3) -> list[Episode]:
        """Return the most recently stored episodes."""
        return self._episodes[-top_k:]

    def retrieve_contrastive(
        self, task: str
    ) -> tuple[Episode | None, Episode | None]:
        """
        Return one relevant success and one relevant failure.
        Useful for few-shot prompting that shows both what to do and what to avoid.
        """
        successes = self.retrieve_by_similarity(task, top_k=1, success_only=True)
        failures  = self.retrieve_by_similarity(
            task, top_k=1, success_only=False
        )
        # Ensure the failure is actually a failure
        failures = [e for e in failures if not e.success]

        return (successes[0] if successes else None,
                failures[0]  if failures  else None)

    # ── Context formatting ─────────────────────────────────────────────────────

    def format_as_context(self, episodes: list[Episode], label: str = "Relevant Past Experience") -> str:
        """Format retrieved episodes as structured few-shot context."""
        if not episodes:
            return ""
        parts = [f"## {label}"]
        for i, ep in enumerate(episodes, 1):
            status = "✓ Success" if ep.success else "✗ Failed"
            trajectory_summary = (
                "\n".join(f"  {step}" for step in ep.trajectory[:4])
                + ("\n  ..." if len(ep.trajectory) > 4 else "")
            )
            parts.append(
                f"### Past Task {i} [{status}]\n"
                f"**Task:** {ep.task}\n"
                f"**Steps taken:**\n{trajectory_summary}\n"
                f"**Outcome:** {ep.outcome}"
            )
        return "\n\n".join(parts)


# ── Agent loop with episodic memory ───────────────────────────────────────────

def run_with_episodic_memory(
    task: str,
    memory: EpisodicMemory,
    retrieval_strategy: str = "similarity",  # "similarity" | "recency" | "contrastive"
    top_k: int = 2,
) -> str:
    """
    Run a simple agent that retrieves relevant past episodes before reasoning,
    then stores the new episode after completing the task.
    """

    # ── Step 1: retrieve past experience ──────────────────────────────────────
    past_context = ""
    if memory.size() > 0:
        if retrieval_strategy == "similarity":
            episodes = memory.retrieve_by_similarity(task, top_k=top_k, success_only=True)
            past_context = memory.format_as_context(episodes)
        elif retrieval_strategy == "recency":
            episodes = memory.retrieve_by_recency(top_k=top_k)
            past_context = memory.format_as_context(episodes, label="Recent Past Experience")
        elif retrieval_strategy == "contrastive":
            success, failure = memory.retrieve_contrastive(task)
            pos = memory.format_as_context([success], "What Worked") if success else ""
            neg = memory.format_as_context([failure], "What Failed") if failure else ""
            past_context = "\n\n".join(filter(None, [pos, neg]))

    # ── Step 2: build context and run ─────────────────────────────────────────
    prompt_parts = []
    if past_context:
        prompt_parts.append(past_context)
    prompt_parts.append(f"## Current Task\n{task}")
    prompt_parts.append(
        "Reason step by step. Show your work. Provide a final answer."
    )
    prompt = "\n\n".join(prompt_parts)

    trajectory: list[str] = []

    # Single-step reasoning for illustration.
    # Replace with a full ReAct loop (Day 9) for multi-step tasks.
    response = llm(prompt)
    trajectory.append(f"Thought: {response[:200]}")

    outcome  = response
    success  = True  # in production: call evaluate_result(task, response)

    # ── Step 3: store the new episode ─────────────────────────────────────────
    memory.store(Episode(
        task=task,
        trajectory=trajectory,
        outcome=outcome[:300],
        success=success,
        tags=[],
    ))

    return outcome


# ── Full demonstration ─────────────────────────────────────────────────────────

if __name__ == "__main__":
    mem = EpisodicMemory("demo_episodic_memory.json")

    # Seed with a past episode (simulates a prior run)
    mem.store(Episode(
        task="Calculate the compound interest on $10,000 at 5% annually for 3 years",
        trajectory=[
            "Thought: Use formula A = P(1 + r)^t",
            "Action: Calculate 10000 * (1.05)^3",
            "Observation: 10000 * 1.157625 = 11576.25",
        ],
        outcome="The final amount is $11,576.25. Interest earned: $1,576.25.",
        success=True,
        tags=["math", "finance"],
    ))

    # Run on a similar but different task — it should retrieve the past episode
    print("=== Running with episodic memory ===\n")
    result = run_with_episodic_memory(
        task="Calculate the compound interest on $5,000 at 4% annually for 5 years",
        memory=mem,
        retrieval_strategy="similarity",
    )
    print("Result:", result)
    print(f"\nMemory now contains {mem.size()} episode(s).")
```

### What retrieval looks like at inference time

When the agent picks up a task like "Calculate compound interest on $5,000 at 4% for 5 years," the retrieved context it receives looks like:

```
## Relevant Past Experience

### Past Task 1 [✓ Success]
**Task:** Calculate the compound interest on $10,000 at 5% annually for 3 years
**Steps taken:**
  Thought: Use formula A = P(1 + r)^t
  Action: Calculate 10000 * (1.05)^3
  Observation: 10000 * 1.157625 = 11576.25
**Outcome:** The final amount is $11,576.25. Interest earned: $1,576.25.

## Current Task
Calculate the compound interest on $5,000 at 4% annually for 5 years
```

The agent now knows which formula to apply and the structure of the calculation — from its own past experience, not from its training data. As the episodic store grows, the agent becomes progressively more efficient on any task type it has seen before.

---

## Where it breaks / what it is not

**Retrieval quality is the ceiling.** A perfect episode store with poor retrieval is worthless. If you retrieve the wrong past episodes, you inject noise that makes the agent *worse* — it may apply a pattern from a different domain. Evaluate retrieval quality (recall@k) independently before trusting it in production.

**Successful episodes can encode bad habits.** If an episode is marked `success=True` but the agent's reasoning included a lucky guess, future retrievals will show that flawed reasoning as a positive example. Garbage in, garbage out. Evaluation of success must be reliable.

**The store grows without bound.** Every task adds an episode. After 10,000 episodes, retrieval latency may increase (with LLM-based scoring) and storage costs appear. Add retention policies: keep only the top-N by usage count, prune episodes older than X days, or cluster near-duplicate episodes.

**Episodic ≠ semantic.** Episodic memory stores *how the agent reasoned* on a past task. Semantic memory stores *verified facts and skills*. A retrieval that returns "we used the compound interest formula last time" is episodic. A skill library that stores `compute_compound_interest(P, r, t)` as a callable function is semantic. Both are valuable; they are not substitutes for each other.

---

## Try it yourself

**Exercise 1 — Check your understanding:**
In the contrastive retrieval strategy, you retrieve one success and one failure. What does the agent's prompt look like when only a failure is available (no successful past episode for this task type)? Should the agent still be shown the failure? What is the risk if it is, and what is the risk if it isn't?

**Exercise 2 — Apply it:**
Add a `cluster_near_duplicates()` method to `EpisodicMemory` that uses the LLM to identify pairs of episodes with very similar task descriptions and merges them (keeping the most recent, dropping the duplicate). Run it after storing 10 similar episodes. How does this affect retrieval quality?

**Exercise 3 — Stretch:**
The current implementation scores retrieval relevance with an LLM — O(n) calls for n episodes. Replace `_score_relevance` with a stub that uses Python's `difflib.SequenceMatcher` for fast string similarity. When does this approximation break down? What types of task similarity does string similarity miss that embedding similarity would catch?

<details>
<summary>Answer for Exercise 1</summary>

When only a failure is available, the agent's prompt contains:

```
## What Failed
### Past Task 1 [✗ Failed]
**Task:** [similar but different task]
**Steps taken:** [the failing approach]
**Outcome:** [what went wrong]
```

**Risk of showing the failure:** The agent may over-anchor on the failing approach, especially if the failure was subtle (e.g., wrong formula) rather than obvious (e.g., divide by zero). It may replicate the failure while "trying to do better."

**Risk of not showing it:** The agent loses the signal about what *not* to do. If the failure encodes a common mistake, showing it with a clear negative label ("This approach failed because...") is valuable.

The pragmatic answer: show the failure, but label it explicitly as a failed approach and include the error. Add a line to `format_as_context` like "Note: this approach failed — avoid repeating it." This gives the agent the contrast without the risk of positive-example contamination.
</details>

---

## Connect it back

[Day 11 (Reflexion)](../../02-reasoning-patterns/days/day-11-reflexion.md) stored verbal lessons in `verbal_memory` — that was episodic memory with a rudimentary retrieval strategy (append to list, inject all of them). Today's pattern is the same concept with proper retrieval: similarity-based, success-filtered, or contrastive.

[Day 22 (Skill Library)](../../04-skills-and-tools/days/day-22-skill-library.md) built an external semantic store. The combination of episodic memory (for past trajectories) + skill library (for verified reusable functions) is the full external memory stack. An agent that uses both improves in two dimensions simultaneously: it learns *how to reason* about tasks (episodic) and *what to call* to execute them (semantic).

[Tomorrow (Day 26)](./day-26-rest-synthesize-iv.md) synthesizes all five modules so far with active recall exercises.

**One question you can now answer that you couldn't this morning:** Reflexion and episodic memory with similarity retrieval both store past experience and use it in future tasks. What is the structural difference between how they retrieve? When would you use one vs. the other?

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
Park et al., "Generative Agents: Interactive Simulacra of Human Behavior" (arXiv:2304.03442, 2023) — Section 3.1 "Memory stream."
The memory stream is an episodic store for simulated social agents. Its retrieval function scores each memory by recency, importance, and relevance — a worked example of combining multiple retrieval strategies. Section 3.2 shows how retrieved memories are reflected upon to generate higher-order insights (connecting back to ExpeL's insight extraction from Day 17).

**If you want the deep version:**
- Lewis et al., "Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks" (arXiv:2005.11401, 2020). The foundational RAG paper. RAG is episodic memory retrieval applied to knowledge documents rather than agent trajectories — the retrieval pipeline is identical. Section 4 on the retrieval-generation interface is directly applicable.

---

## Navigation

← **Previous:** [Day 24 — Scratchpad & Working Memory](./day-24-scratchpad-working-memory.md)
→ **Next:** [Day 26 — Rest & Synthesize IV](./day-26-rest-synthesize-iv.md)
