# Day 21 — Toolformer: Self-Taught Tool Use

> **Today's one idea:** Instead of telling a model when to use tools via prompting, you can let it teach itself — by sampling candidate tool insertions, filtering them by whether the tool call actually helps, and fine-tuning on the successful cases.
> **Reading time:** ~40 min · **Prereqs:** Day 20 (Tool Composition), Day 16 (STaR)
> **Primary source for today:** Schick, Dwivedi-Kumar, Dessì, Raileanu, Lomeli, Zettlemoyer, Cancedda, Scialom — *Toolformer: Language Models Can Teach Themselves to Use Tools* (NeurIPS 2023, arXiv:2302.04761) — Sections 2 and 3.

---

## The hook

Every agent you've built so far uses tools the same way: you decide which tools to define, you write their descriptions, you prompt the model to call them. The model follows your instructions. This works — but it has a ceiling: the model uses tools the way *you* designed them to be used, not necessarily the way that's most effective.

What if the model could figure out, from raw text, when calling a calculator would help? Not because you told it "use the calculator for arithmetic," but because it discovered, through self-supervised learning, that inserting a calculator call in this position reduces the probability of producing a wrong continuation?

This is Toolformer. Schick et al. trained a language model to use five tools — a calculator, a calendar, a search engine, a translation API, and a QA system — without any human annotation of when or how to use them. The model generated candidate tool usages, filtered the ones that actually helped, and fine-tuned on the survivors.

The result: a model that uses tools at decision points humans never explicitly labeled — and uses them correctly.

---

## Building the intuition

### The core insight: tools help when they reduce future uncertainty

A language model is a next-token predictor. A good tool call is one that, after executing, makes it *easier* to predict the correct continuation. 

Concretely: suppose a model is generating the sentence "The population of France is [NUMBER]." It could guess the number from memory. But if it could call `search("France population 2024")` and get back "68.4 million" — then predicting "68.4 million" is trivial. The search call *reduced the perplexity* of the correct continuation.

Toolformer uses this signal as a self-supervised filter:

```
Candidate insertion:
"The population of France is [search(query="France population")]68.4 million."

Before insertion: P(68.4 million | "The population of France is") — uncertain
After  insertion: P(68.4 million | "The population of France is [search result: 68.4M]") — high

Perplexity reduction = large → keep this tool call as a training example
```

No human labeling. The tool's usefulness is defined by whether it helps the model predict what comes next.

### The five-step algorithm

```
Step 1: SAMPLE POSITIONS
  For each text in the corpus:
    Ask the model to suggest positions where a tool call would help:
    "Where in this text should a tool call be inserted?"
    → Generates k candidate (position, tool, args) triples

Step 2: EXECUTE TOOLS
  For each candidate (position, tool, args):
    Run the tool → get result r
    
Step 3: FILTER BY PERPLEXITY REDUCTION
  For each candidate:
    Compute L_without = perplexity of continuation WITHOUT the tool call
    Compute L_with    = perplexity of continuation WITH the tool result in context
    Keep only if (L_without - L_with) > threshold τ
    
Step 4: AUGMENT THE CORPUS
  Insert the surviving (position, tool, args, result) tuples
  into the text as inline API calls:
  "France has a population of [search(France population)→68.4M] 68.4 million people."

Step 5: FINE-TUNE
  Fine-tune the language model on the augmented corpus.
  The model learns: in this context type → call this tool.
```

The key is Step 3: self-supervised filtering via perplexity reduction. Only tool calls that *demonstrably help* make it into the training set.

---

## The formal picture

### The filtering criterion

Let $c_+$ be the continuation text after the tool call position. Let $e(c, z)$ denote the text with tool result $z$ prepended. The filtering criterion is:

$$L(c_+) - L(e(c_+, \text{tool}(a_i))) > \tau$$

where:
- $L(c_+)$ = cross-entropy loss of continuation *without* tool result in context
- $L(e(c_+, \text{tool}(a_i)))$ = cross-entropy loss of continuation *with* tool result in context
- $\tau$ = threshold (Schick et al. use $\tau = 1.0$)

In plain English: only keep the tool call if having the tool result in context reduces the loss of predicting the correct continuation by more than $\tau$ nats.

### The inline API format

Toolformer uses a specific text format for tool calls embedded in text:

```
[tool_name(argument) → result]
```

Examples from the paper:
```
"The world record for the 100m sprint is [calculator(9.58) → 9.58] 9.58 seconds."

"The Apollo 11 mission landed on the moon in [calendar() → July 1969] July 1969."

"El Pais reported that [WikiSearch(Bilbao) → Bilbao is a city in the Basque Country]
 Bilbao is an important industrial city."
```

The model learns to generate these inline calls *at the right positions* — not because it was told when to use them, but because those positions had high perplexity before the call and low perplexity after.

### Simulating the Toolformer data collection pipeline

You can't replicate Toolformer's full training run without a GPU cluster, but you can implement the data collection logic — which is the conceptually important part:

```python
import anthropic
import math
from dataclasses import dataclass
from typing import Optional

client = anthropic.Anthropic()


@dataclass
class ToolCandidate:
    """A candidate tool insertion to be evaluated."""
    text_before:   str   # text before the insertion point
    tool_name:     str
    tool_args:     str
    tool_result:   str
    text_after:    str   # continuation text used for evaluation
    perplexity_without: float = 0.0
    perplexity_with:    float = 0.0
    passes_filter:      bool  = False


def estimate_token_probability(context: str, continuation: str) -> float:
    """
    Estimate the log-probability of continuation given context.
    This is a proxy for perplexity — we use the model's self-reported
    probability when available, or use log-likelihood estimation.

    Note: Real Toolformer uses actual model perplexity via forward pass.
    This is a simplified approximation for illustration.
    """
    # In production: use logprobs from the API if available, or a local model
    # Here we use a heuristic: ask the model to rate how "expected" the continuation is
    response = client.messages.create(
        model="claude-3-5-sonnet-20241022",
        max_tokens=8,
        messages=[{
            "role": "user",
            "content": (
                f"Context: {context}\n\n"
                f"How predictable is this continuation (1=very surprising, 10=very expected): "
                f"'{continuation[:100]}'\n\nRespond with only a number 1-10."
            )
        }]
    )
    try:
        score = float(response.content[0].text.strip())
        # Convert to a pseudo-perplexity: lower = easier to predict = better tool call
        return 10.0 - score  # invert: high predictability → low pseudo-perplexity
    except ValueError:
        return 5.0  # default


def sample_tool_candidates(
    text: str,
    available_tools: list[str],
    n_candidates: int = 3,
) -> list[dict]:
    """
    Step 1: Ask the model to suggest positions where tool calls would help.
    Returns a list of candidate (position, tool, args) suggestions.
    """
    tools_str = ", ".join(available_tools)
    response = client.messages.create(
        model="claude-3-5-sonnet-20241022",
        max_tokens=512,
        messages=[{
            "role": "user",
            "content": (
                f"You have these tools: {tools_str}\n\n"
                f"Text:\n{text}\n\n"
                f"Suggest {n_candidates} positions in this text where inserting a tool call "
                f"would help verify or complete the information. For each, provide:\n"
                f"- position: the text fragment just before the insertion point\n"
                f"- tool: which tool to call\n"
                f"- args: the argument to pass\n\n"
                f"Format each as: BEFORE: <text> | TOOL: <name> | ARGS: <args>"
            )
        }]
    )

    candidates = []
    for line in response.content[0].text.strip().splitlines():
        line = line.strip()
        if "BEFORE:" in line and "TOOL:" in line and "ARGS:" in line:
            try:
                parts    = {p.split(":")[0].strip(): ":".join(p.split(":")[1:]).strip()
                            for p in line.split("|")}
                candidates.append({
                    "before": parts.get("BEFORE", ""),
                    "tool":   parts.get("TOOL", ""),
                    "args":   parts.get("ARGS", "")
                })
            except Exception:
                pass
    return candidates[:n_candidates]


def execute_tool_stub(tool_name: str, args: str) -> str:
    """Step 2: Execute the tool and get its result. Replace with real tools."""
    stubs = {
        "calculator": f"[calc result for '{args}']",
        "search":     f"[search result for '{args}']",
        "calendar":   "[current date: 2026-05-28]",
    }
    return stubs.get(tool_name.lower(), f"[result of {tool_name}({args})]")


def filter_by_perplexity(
    text_before: str,
    tool_name:   str,
    tool_args:   str,
    tool_result: str,
    text_after:  str,
    threshold:   float = 2.0,
) -> ToolCandidate:
    """
    Step 3: Filter candidates by whether the tool result reduces perplexity.
    Keep only if perplexity_without - perplexity_with > threshold.
    """
    # Perplexity WITHOUT tool result
    p_without = estimate_token_probability(text_before, text_after)

    # Perplexity WITH tool result (result prepended to context)
    context_with = f"{text_before} [{tool_name}({tool_args}) → {tool_result}]"
    p_with = estimate_token_probability(context_with, text_after)

    reduction = p_without - p_with

    return ToolCandidate(
        text_before         = text_before,
        tool_name           = tool_name,
        tool_args           = tool_args,
        tool_result         = tool_result,
        text_after          = text_after,
        perplexity_without  = p_without,
        perplexity_with     = p_with,
        passes_filter       = reduction > threshold
    )


def toolformer_data_collection(
    corpus:          list[str],
    available_tools: list[str],
    threshold:       float = 2.0,
) -> list[str]:
    """
    Full Toolformer data collection pipeline:
    sample → execute → filter → augment.
    Returns augmented text strings ready for fine-tuning.
    """
    augmented_examples = []

    for text in corpus:
        # Step 1: Sample candidates
        candidates = sample_tool_candidates(text, available_tools)

        # Steps 2-3: Execute and filter
        for cand in candidates:
            result    = execute_tool_stub(cand["tool"], cand["args"])
            text_after = text[text.find(cand["before"]) + len(cand["before"]):][:200]

            evaluated = filter_by_perplexity(
                text_before = cand["before"],
                tool_name   = cand["tool"],
                tool_args   = cand["args"],
                tool_result = result,
                text_after  = text_after,
                threshold   = threshold
            )

            if evaluated.passes_filter:
                # Step 4: Augment — insert the tool call inline
                inline = f"[{cand['tool']}({cand['args']}) → {result}]"
                augmented = text.replace(
                    cand["before"],
                    cand["before"] + " " + inline,
                    1  # only first occurrence
                )
                augmented_examples.append(augmented)
                print(f"  ✓ Kept: {inline[:80]}")
            else:
                print(f"  ✗ Filtered: {cand['tool']}({cand['args']}) — insufficient reduction")

    return augmented_examples


# ── Example ────────────────────────────────────────────────────────────────────

if __name__ == "__main__":
    corpus = [
        "The Eiffel Tower, completed in 1889, stands 330 metres tall.",
        "To convert 100 USD to EUR at the current rate, you would receive approximately 92 euros.",
        "The Apollo 11 mission successfully landed astronauts on the moon.",
    ]

    print("Running Toolformer data collection...\n")
    examples = toolformer_data_collection(
        corpus          = corpus,
        available_tools = ["calculator", "search", "calendar"],
        threshold       = 1.5
    )

    print(f"\n{len(examples)} training examples generated:")
    for ex in examples:
        print(f"\n  {ex[:150]}...")
```

### Prompt-engineered vs. learned tool use: the key trade-off

| | Prompt-engineered (Days 3, 19) | Toolformer (learned) |
|--|-------------------------------|---------------------|
| Who decides when to use a tool | You (via description) | The model (via training) |
| Generalization to new contexts | Limited to what you described | Generalizes from training distribution |
| Requires training | No — just API calls | Yes — fine-tuning run |
| Works with new tools | Immediately (add to prompt) | Requires new training data |
| Debuggable | Easy — check description | Hard — inspect learned weights |
| Production cost | Low | High (once, amortized over many uses) |

The key question is: *how many times will this tool be used, and how much does description quality matter?* For a tool used thousands of times per day on a narrow task, Toolformer-style learned tool use pays off. For a one-off custom integration, prompt engineering is the practical choice.

---

## Where it breaks / what it is not

**Perplexity reduction is a proxy, not a guarantee.** A tool call can reduce perplexity on the training examples without being the right tool call in general. If your training corpus is narrow, the model learns narrow tool usage patterns — it won't generalize to positions it hasn't seen.

**Toolformer requires a large corpus.** The paper used a pretraining-scale corpus (hundreds of millions of tokens) to generate enough candidate insertions. For a production fine-tuning run, you need tens of thousands of examples per tool at minimum. This is a significant data collection cost.

**The inline format is fragile.** `[tool(args) → result]` is a specific formatting convention that the model must learn. Any deviation (extra spaces, different brackets) breaks the learned pattern. Production systems typically use structured tool-use formats (function calling) instead of inline text injection, which requires adapting the Toolformer approach.

**Toolformer ≠ deciding what tools to build.** It learns *when* to use tools you've already built. The tool design decisions (what tools exist, what they do) are still yours. Toolformer optimizes usage; it doesn't design capabilities.

---

## Try it yourself

**Exercise 1 — Check your understanding:**
Explain the filtering criterion in plain English. Why does "perplexity reduction" tell you whether a tool call is useful? What would it mean for a tool call to have zero perplexity reduction?

**Exercise 2 — Apply it:**
Run the data collection pipeline on 3 sentences of your own choosing. For each sentence, examine the candidates that pass vs. fail the filter. Does the filter make sensible decisions? What threshold value (1.0 vs. 2.0 vs. 3.0) gives you the best signal-to-noise in the surviving candidates?

**Exercise 3 — Stretch:**
Toolformer generates tool calls for *specific positions in text* — it's designed for inline tool use during generation. But modern agents use a different paradigm: the model generates a complete thought, then decides to call a tool. Design a modified Toolformer approach for this "decide after thinking" paradigm. What is the training signal equivalent to perplexity reduction in this case?

<details>
<summary>Hint for Exercise 1</summary>
Perplexity measures how "surprised" the model is by the continuation. High perplexity = the model didn't expect this continuation. Low perplexity = the model easily predicted it. If inserting a tool call drops the perplexity of the continuation, it means the tool's output made the continuation more predictable. What kind of continuation becomes more predictable when you add factual information from a search or calculator?
</details>

<details>
<summary>Worked solution for Exercise 1</summary>
A tool call has *zero perplexity reduction* when the model is equally surprised by the continuation whether or not it has the tool result. This means the tool result didn't help predict what comes next — either because: (1) the model already knew the information from training data (e.g., calling a calculator for "2 + 2"), (2) the continuation doesn't depend on the tool's result (the tool is answering a question the text wasn't asking), or (3) the tool result is wrong or misleading.

In plain English: a useful tool call is one that provides information the model couldn't reliably predict on its own, and that information is actually used in what comes next. The perplexity filter operationalizes this: if the tool didn't help predict the continuation, don't train on it.
</details>

---

## Connect it back

The tools module now has three layers:

```
Day 19: WHAT makes a good skill (design properties + contract)
Day 20: HOW to combine skills (chain, fan-out, aggregate)
Day 21: HOW the model learns to use skills (Toolformer — self-supervised)
```

[Day 22](./day-22-skill-library.md) adds the fourth layer: a persistent library that grows as the agent gains experience — so the action space expands over time rather than staying fixed.

**One question you can now answer that you couldn't this morning:** Your team is building a coding agent that uses 8 tools. They're debating whether to write detailed prompt descriptions for each tool or invest in Toolformer-style training. What question do you ask first to help them decide?

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
Schick et al., *Toolformer* (arXiv:2302.04761) — Section 2 (method, 4 pages).
Section 2.1 describes the sampling step; Section 2.2 the perplexity filtering; Section 2.3 the fine-tuning objective. Figure 1 shows an example augmented text with inline API calls — seeing a real example makes the format concrete.

**If you want the deep version:**
- Schick et al., Section 3 (experiments) — the five tools and their benchmarks. Table 1 shows that a Toolformer-trained 6.7B model outperforms a much larger GPT-3 (175B) on several tool-using tasks — the empirical case for learned vs. prompted tool use.
- Patil et al., *Gorilla: Large Language Model Connected with Massive APIs* (2023, arXiv:2305.15334) — a related approach to teaching tool use at scale. Gorilla focuses on API retrieval (finding the right API from thousands) rather than learning usage patterns. Good follow-on after Toolformer.

---

## Navigation

← **Previous:** [Day 20 — Atomic Tool Design + Composition Patterns](./day-20-tool-design-composition.md)
→ **Next:** [Day 22 — The Skill Library Pattern](./day-22-skill-library.md)
