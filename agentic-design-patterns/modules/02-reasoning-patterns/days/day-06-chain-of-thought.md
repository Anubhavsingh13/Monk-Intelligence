# Day 6 — Chain-of-Thought

> **Today's one idea:** Making the reasoning process explicit — writing it out step by step before the final answer — improves both the correctness of the answer and the debuggability of the agent.
> **Reading time:** ~40 min · **Prereqs:** Day 4 (agent traces), Day 5 (consolidation)
> **Primary source for today:** Wei et al., *Chain-of-Thought Prompting Elicits Reasoning in Large Language Models* (NeurIPS 2022, arXiv:2201.11903) — Sections 2 and 3.

---

## The hook

In 2022, Google Brain researchers were puzzled. GPT-3 and PaLM — the largest language models available — failed embarrassingly on elementary school math problems. Not because they lacked intelligence, but because the *format* of the answer was wrong.

The standard format: *question → answer*. The model had to compress the entire reasoning path into a single token prediction. A 10-step arithmetic problem with that format expects the model to "know" the answer the way you might know that Paris is the capital of France — instantly, from memory.

Wei et al. tried something radical. They added example answers that showed the intermediate steps — the scratch work. Instead of training examples like:

> Q: Roger has 5 tennis balls. He buys 2 more cans with 3 balls each. How many balls does he have?
> A: 11

They used:

> Q: Roger has 5 tennis balls. He buys 2 more cans with 3 balls each. How many balls does he have?
> A: Roger started with 5 balls. 2 cans × 3 balls = 6 balls. 5 + 6 = 11. **The answer is 11.**

No new training. No new model. Just different *formatting* of the examples.

The result: GPT-3's performance on grade school math jumped from 17.9% to 58.1%. PaLM 540B reached 78.7%. Adding the words "Let's think step by step" (zero-shot CoT, Kojima et al. 2022) alone boosted performance dramatically across dozens of benchmarks.

The scratch paper made the model smarter — or more precisely, it made the model *work* smarter.

---

## Building the intuition

### Why scratch paper helps

Think about a calculation you do in your head vs. on paper. Multiply 347 × 89 mentally. Now try it on paper using long multiplication.

You're not smarter on paper. But on paper you can:
1. **Offload intermediate state.** "3 × 8 = 24, write down 4, carry 2" — you don't have to hold everything in working memory.
2. **Check your work.** Each step is visible. You can catch "3 × 8" errors before they compound.
3. **Decompose the problem.** Long multiplication forces you to break the problem into well-defined sub-problems.

CoT does the same thing for LLMs — except the "working memory" is the context window, and the "scratch paper" is the generated text.

Here is the key mechanism: **in an autoregressive model, each token is predicted conditioned on all previous tokens**. When you generate reasoning steps before the final answer, those steps become part of the conditioning context for the final answer token. The model is predicting its answer *given its own reasoning trace* — which is far better conditioning than predicting the answer given only the question.

```
Without CoT:
P(answer | question)              ← one shot, all reasoning compressed

With CoT:
P(step_1 | question)
P(step_2 | question, step_1)
P(step_3 | question, step_1, step_2)
...
P(answer | question, step_1, step_2, ..., step_n)  ← well-conditioned
```

Each step is a small prediction, conditioned on strong context. The model is doing easier things, more often, and each one feeds the next.

### Two flavors: few-shot and zero-shot CoT

**Few-shot CoT** (the original paper): provide 2–8 examples in the prompt that *show* the reasoning format. The model imitates the format for new questions.

```
[System or few-shot examples]
Q: The cafeteria had 23 apples. If they used 20 to make lunch and bought 6 more, how many apples do they have?
A: The cafeteria started with 23 apples. They used 20, so 23 - 20 = 3. They bought 6 more, so 3 + 6 = 9. The answer is 9.

Q: [new question here]
A:
```

**Zero-shot CoT** (Kojima et al., 2022): no examples needed. Just append *"Let's think step by step."* Remarkably, this alone elicits structured reasoning from large models.

```
Q: [question]
A: Let's think step by step.
```

---

## The formal picture

### What CoT changes

Without CoT, the model's output distribution is:

$$P(y \mid x)$$

where $x$ is the question and $y$ is the answer.

With CoT, we introduce a reasoning chain $z$:

$$P(y \mid x) = \sum_z P(y \mid x, z) \cdot P(z \mid x)$$

In practice, you generate one sample of $z$ (the reasoning trace), then $y$ conditioned on $x$ and $z$. Tomorrow's pattern (Self-Consistency) samples *multiple* $z$ and marginalizes — making the above sum more accurate.

### When CoT works and when it doesn't

Wei et al. documented a critical finding: **CoT is an emergent ability of scale**. It only meaningfully improves performance for models above roughly 100 billion parameters (as of 2022; fine-tuning can lower this threshold).

| Model size | CoT effect |
|-----------|-----------|
| < 10B params | Minimal or negative (CoT can hurt small models) |
| ~50–100B params | Small improvement |
| > 100B params | Large, consistent improvement |

This is important architectural knowledge: if you are deploying a small specialized model, CoT may not help. If you're using a frontier model (GPT-4, Claude 3+, Gemini Ultra), CoT almost always helps on reasoning tasks.

CoT helps most on tasks with **verifiable intermediate states**: arithmetic, symbolic reasoning, commonsense reasoning with clear steps. It helps least on tasks where the reasoning is not decomposable into steps: single-fact retrieval, emotion classification, tasks where the answer is immediate.

### Running CoT in code

```python
import anthropic

client = anthropic.Anthropic()

def ask(question: str, use_cot: bool) -> str:
    if use_cot:
        # Zero-shot CoT: append the magic phrase
        prompt = f"{question}\n\nLet's think step by step."
    else:
        prompt = question

    response = client.messages.create(
        model="claude-3-5-sonnet-20241022",
        max_tokens=1024,
        messages=[{"role": "user", "content": prompt}]
    )
    return response.content[0].text


problem = """
A store has 3 shelves. Each shelf has 4 rows. Each row contains 12 items.
The store sells 60 items in the morning and restocks with 25 new items.
How many items does the store have now?
"""

print("=== Without CoT ===")
print(ask(problem, use_cot=False))

print("\n=== With CoT ===")
print(ask(problem, use_cot=True))
```

Run this. The CoT version will show the intermediate steps (3 × 4 × 12 = 144 total, 144 − 60 = 84, 84 + 25 = 109). The non-CoT version may get it right or wrong — but you'll notice you can't check *why* it got the answer it did.

That debuggability is the second major value of CoT, after accuracy. In agent systems, you want the reasoning visible so you can diagnose failures. A trace where every thought is a well-formed reasoning step is a trace you can actually debug.

---

## Where it breaks / what it is not

**CoT can produce plausible-sounding wrong reasoning.** The model generates a confident, step-by-step argument for the wrong answer. This is sometimes called a "reasoning hallucination" — the steps are internally consistent but reach an incorrect conclusion. The scratch paper is not a correctness guarantee; it's a debuggability aid.

**Long CoT can introduce compounding errors.** If step 2 makes a small mistake ("5 × 7 = 36"), every subsequent step is conditioned on that error. In very long chains, small errors cascade. This motivates Self-Consistency (Day 7): sample multiple chains, most of which won't make the same error in the same step.

**CoT doesn't help for tasks without decomposable reasoning.** "What is the capital of France?" — zero-shot CoT might make the model say "Let's think step by step. France is a country in Western Europe. Its capital is... Paris." But it gained nothing from the intermediate step. Applying CoT indiscriminately wastes tokens and latency.

**The chain is not the model's "true" reasoning.** The thoughts are generated text, not a readout of internal computation. The model might produce a wrong intermediate step and then correct it in the next step — or produce correct intermediate steps that don't actually cause the correct final answer. CoT improves outcomes statistically; it doesn't guarantee a correct reasoning process.

---

## Try it yourself

**Exercise 1 — Check your understanding:**
Explain, in one paragraph, why appending "Let's think step by step" to a prompt improves accuracy — without using the phrase "it thinks better." Use the autoregressive conditioning mechanism.

**Exercise 2 — Apply it:**
Run the code above. Now try the same problem with a different model if you have access to one. Does the CoT improvement hold? What happens if you use zero-shot CoT on a simple fact retrieval question ("What is the capital of Germany?")? Does it hurt, help, or make no difference?

**Exercise 3 — Stretch:**
Design a few-shot CoT prompt for classifying customer support tickets (e.g., "billing issue," "technical problem," "feature request"). Write 2 example (question → reasoning → answer) triples. What does the reasoning step look like for a classification task? What's different from arithmetic reasoning?

<details>
<summary>Hint for Exercise 1</summary>
Focus on *what the model is conditioning on* when it predicts the final answer. With CoT, it has the reasoning steps in context. Without CoT, it doesn't. What does better conditioning mean for the quality of the prediction?
</details>

<details>
<summary>Worked solution for Exercise 1</summary>
Language models predict each token conditioned on all previous tokens. Without CoT, the model predicts the answer conditioned only on the question — it must implicitly compress a multi-step reasoning process into the activation state at a single prediction step. With CoT, the model first generates intermediate reasoning steps, each conditioned on the question and all prior steps. When it predicts the final answer, it is conditioned on both the original question and the complete reasoning trace. This richer context gives the model a stronger signal for the answer token, effectively offloading intermediate state from a single compressed activation into the explicit token stream.
</details>

---

## Connect it back

[Day 4](../../01-foundations/days/day-04-reading-agent-trace.md) introduced the Thought as one of three trace elements. Chain-of-Thought is exactly that: an architectural decision to *always generate a Thought before acting or answering*. It is the prerequisite for ReAct (Day 8), which adds tool calls between thoughts, and for Tree of Thoughts (Day 12), which generates multiple competing thoughts and searches among them.

Tomorrow: what if one chain of thought isn't reliable enough? Sample many. Vote.

**One question you can now answer that you couldn't this morning:** Why does "Let's think step by step" improve model accuracy — when no new information was added to the prompt?

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
Wei et al., *Chain-of-Thought Prompting* (arXiv:2201.11903) — Section 2 (method, 2 pages) and Section 3 (experiments, 3 pages).
Section 2 has the clearest formal definition; Section 3.3 (ablation study) shows which elements of the CoT format actually matter — some of the results are surprising.

**If you want the deep version:**
- Kojima et al., *Large Language Models are Zero-Shot Reasoners* (NeurIPS 2022, arXiv:2205.11916) — the zero-shot CoT paper. Introduces "Let's think step by step" as a technique and shows it works across 12 benchmarks without any examples. ~6 pages, readable in 20 minutes.
- Wei et al., *Emergent Abilities of Large Language Models* (TMLR 2022, arXiv:2206.07682) — places CoT in the context of emergent abilities and explains why scale is a prerequisite. Best read after Day 14 (Thinking Fast and Slow) when you have the full reasoning pattern vocabulary.

---

## Navigation

← **Previous:** [Day 5 — Rest & Synthesize I](../../01-foundations/days/day-05-rest-synthesize-i.md)
→ **Next:** [Day 7 — Self-Consistency](./day-07-self-consistency.md)
