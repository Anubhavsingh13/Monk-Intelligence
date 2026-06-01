*[Spec-Driven Development](../../README.md) · Day 1 of 7*

# Day 1 — The Spec-Agent Contract

> **Today's one idea:** A coding agent is a spec-interpreting machine — "vibe coding" fails not because AI is bad, but because ambiguous input produces ambiguous output, every time.
> **Reading time:** ~35 min · **Prereqs:** None
> **Primary source for today:** "SWE-bench: Can Language Models Resolve Real-World GitHub Issues?" — Jimenez et al., NeurIPS 2024, arXiv:2310.06770, Section 4

---

## The hook

It's 3pm on a Tuesday. You open your coding agent and type:

> *"Add a loading spinner to the dashboard so users know when data is loading."*

The agent gets to work. Two minutes later, it hands you 180 lines of code. You review it. The spinner appears — on every page. It uses a third-party animation library that isn't in your project. It triggers on *all* async requests, including background analytics pings the user never sees.

The agent didn't fail. You asked for a loading spinner that tells users data is loading. It gave you exactly that — plus its own reasonable interpretation of scope, library choice, and trigger condition, because you didn't specify any of those.

This is not an AI problem. It is a spec problem. And it happens every time.

---

## Building the intuition

Think about the last time you handed a ticket to a junior developer. You wrote "add search to the user list." They added search — but used a different styling pattern than the rest of the app, and queried the API on every keystroke instead of debouncing like everything else does. Sound familiar?

The developer filled in the gaps with their best guess. The agent does the same thing, but faster and more confidently.

Here is the mental model that changes everything:

**A coding agent is a function. Your spec is the input. Code is the output.**

```
f(spec) → code
```

This function is extraordinarily powerful. It has read millions of codebases, knows hundreds of frameworks, and can hold a complex implementation in its working memory. But it is still a function. It does not have memory of last week's architecture discussion. It does not know your team's unwritten conventions. It does not know that "small" to your product manager means "one afternoon of work," not "a full redesign."

Every word you write in your spec is a constraint — something the agent cannot randomly vary. Every word you omit is a **degree of freedom** — something the agent will fill with the statistically most likely answer based on its training data.

**Vibe coding** is writing a spec with maximum degrees of freedom. It feels fast because you type three sentences. It is slow because you spend the next hour reviewing an implementation that is technically correct but wrong for your context.

**Spec-driven development** is the practice of closing degrees of freedom deliberately — not by over-specifying the implementation, but by precisely specifying the outcome and the constraints.

The difference is not how much you write. It is what you write.

---

## The formal picture

### The spec-agent contract

When you give a spec to a coding agent, you are entering a contract. The contract has three terms:

**1. The agent has exactly what you give it.**
No institutional knowledge. No access to last quarter's Notion docs. No memory of the conversation you had about this feature two weeks ago. It has your spec, the codebase (if you provide it), and its training data. Nothing else.

**2. Every ambiguity in your spec is resolved by the agent's training data priors.**
If you write "make it faster," the agent will make it faster in the most statistically common way it has seen — which may or may not match what you mean. These priors are not random; they are often reasonable. But "reasonable" is not the same as "correct for your context."

**3. There is no verification unless you specify it.**
The agent does not know it succeeded. It generates code that satisfies the pattern your spec established. Whether that code passes your team's standards, handles your edge cases, or satisfies your users is not in its contract unless you put it there explicitly — as acceptance criteria.

### Three failure modes

Every spec failure is one of three things:

| Failure mode | Example | What the agent does |
|---|---|---|
| **Ambiguity** | "make it look better" | Chooses one of many valid interpretations |
| **Incompleteness** | Missing: "don't add new npm packages" | Does the sensible thing, which may be wrong for your context |
| **Untestability** | "improve performance" | Produces code it cannot verify against your intent |

The SWE-bench benchmark (see primary source) makes this concrete. Researchers gave real GitHub issues to coding agents and measured how often they produced correct patches. Issues with **specific reproduction steps, explicit expected behavior, and test cases** were solved at dramatically higher rates than vague issues. The agent's capability was roughly constant — the spec's quality was the variable.

### Vibe coding vs. spec-driven: where the ambiguity lives

```
Vibe coding:      [your 3-sentence description] → agent fills gaps → code → you debug
Spec-driven:      [your structured spec] → agent fills implementation → code → you verify
```

In vibe coding, the ambiguity is resolved invisibly, inside the agent, during generation. You discover it during code review.

In spec-driven development, the ambiguity is resolved visibly, by you, during spec writing. You discover it before any code is written.

The total work is similar. The distribution is completely different. Spec-driven front-loads 10 minutes of thinking and saves 30 minutes of debugging.

---

## Where it breaks / what it is not

**"But the agent asked me for clarification."**
That's still vibe coding if the clarification happens in conversation rather than in the spec. Conversational clarification is not captured anywhere. Next time you run the same task — on a different session, with a different agent, by a colleague — the clarification is gone. A spec is durable. A conversation is not.

**"I use a system prompt that sets all the context."**
Good. That's a project constitution (Day 3). But a system prompt covers project-level context, not feature-level acceptance criteria. You still need a per-feature spec.

**"My agent is really good — it usually gets it right without specs."**
Sometimes. For simple, well-trodden tasks (add a button, fix a typo) the priors are strong enough that vibe coding works. For anything with business logic, non-standard conventions, or specific constraints, priors fail. Spec-driven development is for the cases that matter, not the trivial ones.

**"Writing a spec takes longer than just doing it."**
For a one-line change: yes. For a feature with three acceptance criteria: no. The crossover point is lower than most people think. After Day 4 you will have a template that takes 10 minutes to fill in.

---

## Try it yourself

### Exercise 1 — Count the degrees of freedom
Find the last feature request, Jira ticket, or GitHub issue you wrote. Read it once. Then answer: how many different implementations could reasonably satisfy this description? (Think: scope, styling, error handling, edge cases, new dependencies.) Write the number down.

### Exercise 2 — Find the ambiguity
Highlight every sentence in that same ticket that has more than one valid interpretation. A sentence is ambiguous if a reasonable developer could read it two different ways and build two different things.

### Exercise 3 — Write one acceptance criterion
Write a single acceptance criterion for the feature. It must be a statement that is either **true** or **false** after the agent finishes — no "it should probably..." or "it looks like...". Use this format: *"Given [starting state], when [action], then [observable outcome]."*

<details>
<summary>Hint for Exercise 3</summary>
Start with the happy path: what is the single most important thing that must be true for this feature to be considered done? Write that as your first criterion. Ignore edge cases for now — those come in Day 2.
</details>

<details>
<summary>Worked example</summary>

**Original ticket:** "Add search to the user list so admins can find users faster."

**Degrees of freedom (Exercise 1):** At least 7: server-side vs. client-side search, which fields to search, debounce behavior, empty state, loading state, styling pattern, whether to update the URL.

**Ambiguity (Exercise 2):** "find users faster" — faster than what? Keyboard shortcut? Instant filter? API query?

**Acceptance criterion (Exercise 3):** "Given the admin is on the user list page and there are users in the system, when the admin types 'alice' into the search box, then only users whose name or email contains 'alice' (case-insensitive) are shown in the list."
</details>

---

## Connect it back

There is no "yesterday" — this is Day 1. But here is the question you should now be able to answer that you couldn't this morning:

> *When a coding agent produces wrong output, what is the most likely root cause?*

The answer is almost always: the spec had unresolved degrees of freedom. The agent didn't fail — it made a reasonable choice that happened to be wrong for your context.

**Tomorrow (Day 2)** you will learn the four-layer structure that closes the right degrees of freedom without over-specifying the implementation. The key question to carry into tomorrow: *what is the minimum structure a spec needs to be executable?*

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
Jimenez et al., "SWE-bench: Can Language Models Resolve Real-World GitHub Issues?", arXiv:2310.06770. Read Section 1 (Introduction) and Section 4 (Analysis). Focus on the failure breakdown — which types of issues agents got wrong and why. The patterns map directly onto the three failure modes above. Available free at arxiv.org/abs/2310.06770.

**If you want the deep version:**

- Chen et al., "Evaluating Large Language Models Trained on Code" (Codex/HumanEval paper), arXiv:2107.03374, 2021. Read Section 2.1 ("HumanEval Benchmark"). Each benchmark problem is a docstring (spec) + hidden tests (acceptance criteria). The pass rate vs. docstring specificity data is the empirical foundation for today's argument. Available at arxiv.org/abs/2107.03374.

- Karpathy, "Intro to Large Language Models," YouTube, November 2023. Timestamps 0:00–18:00. Builds the mental model of *why* LLMs behave this way — they are next-token predictors, not planners. Once you see this, the "spec as function input" framing is obvious. Search "Andrej Karpathy Intro to Large Language Models" on YouTube.

---

[Day 2 — Anatomy of an Executable Spec →](day-02-anatomy-of-executable-spec)

---

[Day 2 — Anatomy of an Executable Spec →](day-02-anatomy-of-executable-spec.md)
