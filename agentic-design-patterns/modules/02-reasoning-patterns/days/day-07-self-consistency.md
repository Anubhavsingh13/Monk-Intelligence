# Day 7 — Self-Consistency

> **Today's one idea:** Sampling multiple independent reasoning paths and taking the majority vote is significantly more reliable than any single greedy chain — and requires no additional training.
> **Reading time:** ~35 min · **Prereqs:** Day 6 (Chain-of-Thought)
> **Primary source for today:** Wang et al., *Self-Consistency Improves Chain of Thought Reasoning in Language Models* (ICLR 2023, arXiv:2203.11171) — Sections 2 and 3.

---

## The hook

You're a judge, and five expert witnesses have each independently investigated the same incident and submitted reports. Three of them conclude: "the defendant was in Building A at 9pm." One concludes Building B. One is uncertain.

You don't average their conclusions. You don't take the first report. You take the plurality: Building A, with 3/5 confidence.

This is Self-Consistency — and it's remarkable because you haven't asked any witness to do additional research. You haven't given them new evidence. You've simply made the decision more robust by getting *more independent opinions* and aggregating them.

Wang et al. asked: what if we did this with reasoning chains? Instead of generating one CoT and trusting it, generate five — or twenty — and vote on the final answer. The result: a 17.9 percentage-point improvement on grade school math. No new model. No new training. Just more samples and a vote.

---

## Building the intuition

### Why one chain can be unlucky

[Yesterday](./day-06-chain-of-thought.md) we saw that CoT improves accuracy by providing better conditioning context for the final answer. But it doesn't eliminate errors. A single CoT chain can fail in at least three ways:

1. **Arithmetic slip:** "7 × 8 = 54" instead of 56 — a mistake in one step that propagates forward.
2. **Semantic drift:** the model re-interprets a key phrase mid-chain in a way that shifts the problem.
3. **Unlucky sampling:** at temperature > 0, the model samples from a distribution. A low-probability wrong path can be sampled.

For any of these, the failure is *specific to that particular sample*. A different sample, starting from the same question, would likely not make the same error at the same step.

**The key insight:** correct reasoning paths tend to converge on the same answer. Incorrect reasoning paths tend to diverge — they produce different wrong answers for different reasons. If you sample enough paths, the correct answer should be the plurality.

```
Correct paths  → {answer: 42, answer: 42, answer: 42}  (converge)
Incorrect paths → {answer: 41, answer: 43, answer: 38}  (diverge)

Majority vote → 42  ✓
```

This is not a guarantee — it's a probabilistic argument. If the model has a systematic bias (all incorrect paths go to 41), majority voting won't help. But for random errors, it works extremely well.

### The temperature dial

Self-Consistency requires generating diverse paths — if all paths are identical, there's nothing to vote on. This means you need temperature > 0. Wang et al. used temperature = 0.5–0.7 for their experiments.

Think of temperature as a dial on the "creativity" of the model's next-token sampling:
- **Temperature = 0:** Always pick the highest-probability token. Deterministic, but only one path possible.
- **Temperature = 0.5:** Some randomness. Mostly high-probability paths, occasional detours.
- **Temperature = 1.0:** Sample proportionally to probabilities. Much more diverse, including low-probability paths.

For Self-Consistency, you want enough temperature to generate meaningfully different paths, but not so much that the paths become incoherent.

---

## The formal picture

### The algorithm

```
Self-Consistency(question q, samples k, temperature T):
  chains = []
  for i in 1..k:
    chain_i = generate_cot(q, temperature=T)
    answer_i = extract_answer(chain_i)
    chains.append((chain_i, answer_i))
  
  return majority_vote([answer for _, answer in chains])
```

That's it. The entire pattern is: sample k times, extract answers, take the plurality.

**The formal version** (from Wang et al.):

Let $z_i$ be the $i$-th sampled reasoning chain and $a_i$ be the final answer extracted from $z_i$. Self-Consistency returns:

$$\hat{a} = \underset{a}{\operatorname{argmax}} \sum_{i=1}^{k} \mathbf{1}[a_i = a]$$

In words: the answer that appears most often across the $k$ samples.

### Diminishing returns

Wang et al. studied how accuracy scales with $k$:

```
k = 1  (standard CoT)    → baseline accuracy
k = 5                    → +8–12% improvement
k = 20                   → +15–18% improvement
k = 40                   → +17–20% improvement (near-plateau)
k = 100                  → marginal additional gain
```

The improvement is dramatic from 1→5 and then flattens significantly after 20–40. In practice, k=10–20 is a reasonable production choice for most tasks.

### Implementing Self-Consistency

```python
import anthropic
import re
from collections import Counter

client = anthropic.Anthropic()

def generate_cot_sample(question: str, temperature: float = 0.7) -> str:
    """Generate one CoT reasoning chain."""
    response = client.messages.create(
        model="claude-3-5-sonnet-20241022",
        max_tokens=1024,
        temperature=temperature,
        messages=[{
            "role": "user",
            "content": (
                f"{question}\n\n"
                "Let's think step by step. "
                "At the end, state your final answer as:\n"
                "FINAL ANSWER: [your answer here]"
            )
        }]
    )
    return response.content[0].text

def extract_answer(response: str) -> str:
    """Pull out the final answer from a CoT response."""
    # Look for the structured FINAL ANSWER: marker
    match = re.search(r"FINAL ANSWER:\s*(.+?)(?:\n|$)", response, re.IGNORECASE)
    if match:
        return match.group(1).strip()
    # Fallback: take the last line
    lines = [l.strip() for l in response.strip().splitlines() if l.strip()]
    return lines[-1] if lines else ""

def self_consistency(question: str, k: int = 10) -> tuple[str, dict]:
    """
    Run Self-Consistency with k samples.
    Returns: (best_answer, vote_counts)
    """
    answers = []

    for i in range(k):
        chain = generate_cot_sample(question)
        answer = extract_answer(chain)
        answers.append(answer)
        print(f"  Sample {i+1:2d}: {answer}")

    vote_counts = Counter(answers)
    best_answer = vote_counts.most_common(1)[0][0]

    return best_answer, dict(vote_counts)


# Example usage
problem = (
    "A train leaves Station A at 9:00am traveling at 60 mph. "
    "Another train leaves Station B at 10:00am traveling toward Station A at 80 mph. "
    "The stations are 280 miles apart. At what time do the trains meet?"
)

print(f"Question: {problem}\n")
print("Sampling reasoning chains...\n")
answer, votes = self_consistency(problem, k=10)

print(f"\nVote distribution: {votes}")
print(f"Self-Consistency answer: {answer}")
```

**What to notice in the output:**
- Most samples will agree on the same answer (the correct one)
- One or two samples may produce different answers — these are the unlucky paths
- The `vote_counts` dictionary shows you the confidence: 9/10 on the right answer is very different from 6/10

---

## Where it breaks / what it is not

**Self-Consistency multiplies cost by k.** 10 samples = 10× the token cost and latency. In production, this is significant. The pattern is most justified for high-stakes reasoning tasks where a wrong answer is expensive — financial calculations, medical information synthesis, legal document analysis. For casual conversational queries, it's overkill.

**Majority voting fails for open-ended tasks.** How do you take the majority vote over 10 different essays? Or 10 different code files? Self-Consistency as described here only works when the final answer is a discrete, comparable value (a number, a category, a yes/no). For open-ended outputs, you need a different aggregation strategy — like asking the model to synthesize the best elements of k outputs (this is Mixture of Agents, Day 30).

**Systematic bias defeats the vote.** If the model always makes the same mistake on a certain type of problem (e.g., always miscounts items in lists), sampling more won't help — all 10 samples will be wrong in the same way. Self-Consistency only helps with random, sample-specific errors. This is a fundamental limitation that connects to why parametric learning (STaR, Day 16) is sometimes necessary.

**Answer extraction is a hidden failure mode.** The `extract_answer` function above is simple, but in practice, models don't always format their answers consistently. Two samples might both compute "the trains meet at 11:00am" but format it as "11:00 AM," "11am," and "11:00 a.m." — the vote counter treats these as different answers. Normalizing answers before voting is essential for reliable Self-Consistency.

---

## Try it yourself

**Exercise 1 — Check your understanding:**
Why does Self-Consistency work better for random errors than for systematic errors? Answer by thinking about what "majority vote" actually achieves.

**Exercise 2 — Apply it:**
Run the code above on a math problem you find tricky. Try k=3, k=7, and k=15. Plot (or just note) the vote distribution for each k. At what k does the answer stabilize?

**Exercise 3 — Stretch:**
Self-Consistency requires a discrete answer to vote on. Design an aggregation strategy for a task with open-ended output — e.g., writing a one-paragraph summary of a document. The strategy should use k=5 samples. What does the "vote" look like? How do you pick a winner? (There's no single right answer — think about the trade-offs.)

<details>
<summary>Hint for Exercise 1</summary>
Imagine you flip a coin 10 times. If it's a fair coin (random error), you expect roughly 5 heads and 5 tails — but you can still identify heads as the plurality if the error is asymmetric. Now imagine the coin is weighted to always land heads (systematic error). All 10 flips are the same — voting is irrelevant.
</details>

<details>
<summary>Worked solution for Exercise 1</summary>
Self-Consistency aggregates answers by majority vote. When errors are random — specific to a particular sampled path — different paths make different mistakes in different places, and their answers diverge. The correct answer, being the target of a convergent reasoning process, tends to be reached by more paths than any single wrong answer. Majority vote then selects it. 

When errors are systematic — all paths make the same mistake because the model has a persistent misunderstanding — every sample reaches the same wrong answer. The majority vote unanimously selects the wrong answer. Self-Consistency makes this case worse than standard CoT: not only does it not help, it gives you false confidence (10/10 samples agree! must be right) in a wrong answer.
</details>

---

## Connect it back

[Yesterday's Chain-of-Thought](./day-06-chain-of-thought.md) gave us the tool to make reasoning visible. Today's Self-Consistency asks: *what if we don't trust a single visible reasoning chain?* The answer is to sample many and vote.

This sets up two future patterns:
- **Tree of Thoughts (Day 12):** instead of sampling paths independently and voting at the end, explore them *interactively* and *prune bad branches early*. More efficient than Self-Consistency for hard tasks.
- **LATS (Day 13):** use Self-Consistency-style sampling within a tree search, combined with environment feedback as the "vote" signal.

Tomorrow: we add tool calls to the reasoning chain. ReAct = CoT + actions, in an interleaved loop. You already know both ingredients — tomorrow you see them combined.

**One question you can now answer that you couldn't this morning:** A client asks: "Can we make our reasoning agent more reliable without retraining it?" What do you say, and what question do you ask back before recommending Self-Consistency?

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
Wang et al., *Self-Consistency* (arXiv:2203.11171) — Section 2 (method, 2 pages) and Table 1 in Section 3.
Section 2 is the complete algorithm description; Table 1 shows the accuracy improvements across benchmarks — look at the GSM8K row specifically to see the 17.9% gain mentioned in the hook.

**If you want the deep version:**
- Wang et al., Section 5 (analysis) — investigates *why* Self-Consistency works, including experiments on what happens when you vary k and temperature. ~3 pages, very concrete.
- Yao et al., *Tree of Thoughts* (arXiv:2305.10601) — Section 1 (introduction) — frames Self-Consistency as a "generate-then-vote" strategy and motivates why interactive search (ToT) is more efficient. Reading the intro now will make Day 12 easier.

---

## Navigation

← **Previous:** [Day 6 — Chain-of-Thought](./day-06-chain-of-thought.md)
→ **Next:** [Day 8 — ReAct: Interleaving Thought and Action](./day-08-react.md)
