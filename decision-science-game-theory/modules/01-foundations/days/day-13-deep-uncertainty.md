*[Decision Science & Game Theory](../../../../README.md) · [Module 1 — The Science of Choosing](../1-overview.md) · Day 13 of 65*

# Day 13 — Decisions Under Deep Uncertainty

> **Today's one idea:** When you cannot assign probabilities, expected value breaks down — and maximin, minimax regret, and robustness offer principled alternatives that protect against the worst without abandoning the search for the best.
> **Reading time:** ~35 min · **Prereqs:** [Day 2](day-02-expected-value.md), [Day 10](day-10-decision-trees.md), [Day 12](day-12-multi-criteria-decisions.md)
> **Primary source for today:** Walker, Warren E. et al. "Defining Uncertainty: A Conceptual Basis for Uncertainty Management in Model-Based Decision Support." *Integrated Assessment*, 4(1):5–17, 2003.

---

## The Hook

In January 2020, no epidemiologist could reliably assign a probability to "this virus causes a global pandemic." In 2008, no bank risk model could confidently assign a probability to "US housing prices fall 30% nationwide simultaneously." In 2022, few energy strategists could assign a probability to "Russia cuts off gas to Europe within 6 months."

These are not failures of intelligence or data. They are cases of *genuine deep uncertainty* — situations where the probability distribution itself is unknown, or where the space of possible outcomes is not fully enumerable. Frank Knight called this "uncertainty" (as opposed to "risk," where probabilities are known). Nassim Taleb calls these outcomes "Black Swans."

Expected value requires probabilities. When probabilities are not just unknown but unknowable, you need different tools. This page covers three.

---

## Building the Intuition

### Risk vs. uncertainty: the Knight distinction

Frank Knight (1921) drew a distinction that becomes more important as AI systems confront genuinely novel domains:

**Risk:** The future state is unknown, but the probability distribution is known or estimable. A fair die, a coin flip, a well-characterised customer churn rate. Expected value is the right tool.

**Uncertainty (deep uncertainty):** The probability distribution itself is unknown — or the space of possible outcomes is not fully specified. A pandemic novel pathogen, an AI system's behaviour in a distribution shift, the long-run implications of AGI. Expected value requires probabilities that don't exist.

The distinction matters because applying expected value to deep uncertainty gives you *the illusion of precision* — a number that looks like EV but is actually extremely sensitive to fabricated probability estimates.

**Three markers of deep uncertainty:**
1. Experts disagree not on probabilities but on the *structure of the problem*
2. The outcome space contains possibilities not yet imagined
3. History provides no relevant reference class

When any of these apply, the tools below are more appropriate than expected value.

---

### Tool 1: Maximin — protect the floor

**The idea:** Choose the action whose *worst-case outcome* is best. Maximise the minimum payoff.

**When to use it:** When the downside of a catastrophic outcome is so severe that no amount of upside elsewhere justifies risking it. Survival situations. Irreversible decisions. Critical infrastructure. Existential risks.

**Example:**

You are deploying an AI system in a medical context. Three deployment configurations:

| Configuration | Best case | Worst case |
|---------------|-----------|------------|
| A: Full auto | +$5M revenue | −$10M liability + reputational damage |
| B: Human-in-loop | +$3M revenue | −$500K (human error overhead) |
| C: Advisory only | +$1M revenue | −$50K (low adoption) |

Maximin says: choose C. Its worst case (−$50K) is best among the worst cases. You sacrifice upside to protect the floor.

**Maximin is conservative by design.** It focuses entirely on the worst case and ignores expected performance. It is appropriate when:
- Worst-case outcomes are catastrophic and irreversible
- You cannot afford to be wrong
- The stakes are existential or reputational

It is inappropriate when:
- Worst-case outcomes are merely unpleasant (not catastrophic)
- You have many decisions to make (maximin applied repeatedly produces systematically over-conservative choices)
- Upside is substantial and the probability of worst-case is genuinely low

---

### Tool 2: Minimax regret — minimise your anticipated regret

**The idea:** For each scenario, compute the *regret* of each action — how much worse it is than the best possible action in that scenario. Then choose the action that minimises maximum regret.

**The regret matrix** is built from a payoff matrix by subtracting the best action's payoff in each scenario.

**Example:**

You are deciding whether to build AI infrastructure in-house, use a cloud provider, or take a hybrid approach. Three possible regulatory scenarios:

**Payoff matrix (NPV, $M):**

| | Scenario 1: Light regulation | Scenario 2: Heavy regulation | Scenario 3: Fragmentation |
|--|--|--|--|
| In-house | $8M | $2M | $5M |
| Cloud | $10M | $1M | $3M |
| Hybrid | $7M | $5M | $6M |

**Regret matrix** (best in each scenario − your payoff):

| | Scenario 1 | Scenario 2 | Scenario 3 | Max regret |
|--|--|--|--|--|
| In-house | $10M−$8M = **$2M** | $5M−$2M = **$3M** | $6M−$5M = **$1M** | **$3M** |
| Cloud | $10M−$10M = **$0M** | $5M−$1M = **$4M** | $6M−$3M = **$3M** | **$4M** |
| Hybrid | $10M−$7M = **$3M** | $5M−$5M = **$0M** | $6M−$6M = **$0M** | **$3M** |

Minimax regret: choose **In-house or Hybrid** (both have max regret = $3M, versus Cloud's $4M).

**Why minimax regret is better than maximin for many business decisions:**
Maximin ignores all the good scenarios. Minimax regret considers all scenarios and asks: "How bad would I feel about this choice after the fact?" It operationalises a psychologically real criterion — people are strongly motivated to avoid the experience of regret.

**The Savage justification:** Leonard Savage (1951) argued that minimax regret is the rational criterion when probabilities are unknown — it is the "least exploitable" decision rule in the sense that no adversary (or scenario) can make you feel foolish about your choice by more than your maximum regret.

---

### Tool 3: Robustness — find options that work well across many scenarios

**The idea:** Instead of optimising for one scenario or protecting against the worst, find actions that achieve *good-enough* performance across the broadest range of scenarios. A robust option may not win in any single scenario but is never disastrously wrong.

**When to use it:** Long-horizon decisions (10+ years), platform architecture, career strategy, policy design. Situations where you know your scenario analysis is incomplete — the real world will produce scenarios you haven't listed.

**The robustness criterion in practice:**

1. Generate a set of plausible scenarios (not exhaustive — you know it isn't)
2. For each option, count how many scenarios in which it performs "acceptably" (above a threshold)
3. Choose the option that is acceptable in the most scenarios

**Example — career strategy:**

You are deciding between three career paths: specialist (deep ML engineering), generalist (product/technical PM), or founder path.

Scenarios (not exhaustive — just representative):
- AI becomes fully commoditised (everyone uses APIs, no need for ML engineers)
- AI regulation tightens dramatically (compliance/ethics roles boom)
- AI boom continues (demand for ML specialists soars)
- Economic recession (capital-efficient, revenue-generating roles survive)
- General-purpose AI arrives (most knowledge-worker roles disrupted)

| | Commoditisation | Regulation | AI boom | Recession | AGI disruption |
|--|--|--|--|--|--|
| Specialist | ✗ | ~ | ✓ | ✗ | ✗ |
| Generalist | ✓ | ✓ | ~ | ✓ | ~ |
| Founder | ~ | ✗ | ✓ | ✗ | ~ |

Robustness count (✓ = clearly good, ~ = acceptable):
- Specialist: 1 clearly good, 1 acceptable = 2/5
- Generalist: 3 clearly good, 2 acceptable = 5/5
- Founder: 2 clearly good, 2 acceptable = 4/5

The generalist path is most robust. Not because it's best in any individual scenario — the specialist beats it in the AI boom — but because it performs acceptably in the most scenarios, including ones not listed.

---

## The Formal Picture

**Maximin — in plain terms:**

Look at each action and identify its worst possible outcome across all scenarios. Then choose the action whose worst case is the *least bad*. You are maximising the minimum payoff.

**Minimax regret — in plain terms:**

For each action and each scenario, calculate regret:
```
Regret = (best payoff anyone could have got in that scenario) − (your actual payoff)
```
Find the largest regret each action could produce. Then choose the action with the *smallest* maximum regret.

**Robustness — in plain terms:**

Set a minimum acceptable threshold for each scenario (what counts as "good enough"). For each action, count how many scenarios it clears that threshold. Choose the action that clears the threshold in the most scenarios.

**Relationship to EV:** When you know the probabilities, expected value beats all three. These tools are for when you genuinely don't — when the probability distribution itself is uncertain or the scenario set is incomplete.

---

### Deep uncertainty in AI systems

**Model deployment under distribution shift:**
When you deploy a model, you face deep uncertainty about what real-world data distributions will look like six months from now. You cannot assign probabilities to "the distribution shifts toward adversarial inputs" or "a new user segment emerges." The robustness criterion — finding architectures and monitoring setups that perform acceptably across many plausible shifts — is more appropriate than expected value optimisation on a single test set.

**Long-horizon AI strategy:**
Companies building AI roadmaps for 5+ years face Knight uncertainty, not risk. The scenario analysis approach — build under light regulation, heavy regulation, commoditisation — and the robustness criterion are the right tools. Any product strategy document that presents a single probability-weighted forecast for the AI landscape in 2030 is using the wrong tool.

**Reward design under uncertainty (preview of Day 47):**
When designing reward functions for RL agents, you face deep uncertainty about how the agent will behave in edge cases you haven't enumerated. Maximin thinking — "what is the worst thing this reward function could cause, and is that acceptable?" — is the right frame before the robustness testing that deployment requires.

---

## Where It Breaks / What It Is Not

**Minimax regret requires a complete scenario set.**
If you've missed a scenario, your regret matrix is wrong. The robustness criterion handles this better by explicitly acknowledging incompleteness.

**Maximin produces systematically conservative outcomes.**
Applied to all decisions, it is irrational — you would never accept any risk at all. Reserve it for decisions where catastrophic downside is truly irreversible.

**"Deep uncertainty" is sometimes used as an excuse not to reason.**
Claiming "we can't assign probabilities" is often wrong — there is usually a reference class. Before invoking deep uncertainty tools, ask: is there genuinely no probability estimate available, or am I just uncomfortable committing to a number? If it's the latter, Day 4 (calibration) is the right tool, not this page.

**Scenario analysis is not forecasting.**
You are not predicting which scenario will occur — you are asking "which action works best across many scenarios?" Do not assign probabilities to scenarios in a robustness analysis and compute expected value — that would convert it back into an EV problem, defeating the purpose.

---

## Try It Yourself

**Exercise 1 — Regret matrix (10 min)**

You are choosing between three AI model architectures for a product that will launch in 18 months. Three regulatory scenarios are plausible (you cannot assign probabilities):

**NPV ($M) across scenarios:**

| Architecture | Open ecosystem | National AI regs | Full interop mandates |
|---|---|---|---|
| Proprietary closed | 12 | 3 | −2 |
| Open source first | 7 | 8 | 10 |
| Hybrid modular | 9 | 7 | 6 |

a) Compute the regret matrix.
b) Apply minimax regret. Which architecture do you choose?
c) Apply maximin. Does the answer change?

<details>
<summary>Worked answer</summary>

**a) Regret matrix** (best per scenario − your payoff):

Best per scenario: Open=12, Reg=8, Interop=10

| | Open | Reg | Interop | Max regret |
|---|---|---|---|---|
| Proprietary | 0 | 5 | 12 | **12** |
| Open source | 5 | 0 | 0 | **5** |
| Hybrid | 3 | 1 | 4 | **4** |

**b) Minimax regret:** Choose **Hybrid modular** (max regret = $4M).

**c) Maximin:** Worst cases — Proprietary: −$2M; Open source: $7M; Hybrid: $6M. Choose **Open source** (worst case = $7M is best among worst cases).

The two criteria disagree. Maximin is more conservative — it sees the −$2M catastrophe for Proprietary as disqualifying. Minimax regret cares more about opportunity cost. This disagreement is itself informative: it tells you the key question is how much weight you put on avoiding catastrophe vs. avoiding missed opportunity.

</details>

---

**Exercise 2 — Robustness for a product strategy (15 min)**

Your team is deciding on an AI product strategy for the next 3 years. Three options: build a verticalized AI tool for a specific industry, build a horizontal API platform, or build an AI agent framework for developers.

Generate 5 plausible scenarios for the AI product landscape in 3 years. For each option, rate performance in each scenario as ✓ (strong), ~ (adequate), or ✗ (poor). Compute robustness scores. Which strategy is most robust?

*(There is no single right answer — the value is in the scenario generation and honest scoring.)*

---

**Exercise 3 — When to switch criteria (5 min)**

For each situation, decide whether to use EV, maximin, minimax regret, or robustness — and justify:

a) Deciding which of three marketing campaigns to run next quarter, with historical data on similar campaigns.

b) Deciding whether to use nuclear power as part of a national energy strategy over 50 years.

c) Deciding which AI vendor to integrate into a product where switching costs are high, regulation is evolving, and you have 3 plausible regulatory scenarios.

d) Deciding whether to buy earthquake insurance for your home.

<details>
<summary>Discussion</summary>

a) **EV** — historical data provides reference class; this is risk, not uncertainty.

b) **Robustness** — 50-year horizon, incomplete scenario set, irreversibility makes maximin worth applying to the catastrophic failure scenarios.

c) **Minimax regret** — three explicit scenarios, high switching cost means regret matters, cannot assign probabilities to regulatory outcomes.

d) **EV + Prospect Theory** — rare event with known probability (actuarial data exists); but loss aversion may make insuring at above-EV premium rational (Day 8).

</details>

---

## Connect It Back

Today completed Module 1. You now have the full individual decision-making toolkit:

| Tool | When to use |
|------|-------------|
| Expected value | Known probabilities, many similar decisions |
| Expected utility | One-time high-stakes decisions with risk preferences |
| Bayesian updating | When new evidence arrives |
| Decision trees | Multi-stage sequential decisions |
| Multi-attribute utility | Multiple incommensurable criteria |
| Maximin | Catastrophic downside, irreversibility |
| Minimax regret | Unknown probabilities, known scenario set |
| Robustness | Long horizon, incomplete scenario set |

Module 2 begins tomorrow with a fundamental shift: there is now a second rational agent in the room, and their choices depend on yours, and yours depend on theirs. The entire individual decision-making framework you've built is *preserved* — it describes each player's internal reasoning. What changes is that the space of possible outcomes now depends on the *interaction* of multiple such agents.

**The question you should be able to answer now:**
*Why is expected value not just "hard to apply" under deep uncertainty — but fundamentally the wrong tool?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Walker et al., "Defining Uncertainty: A Conceptual Basis for Uncertainty Management in Model-Based Decision Support." *Integrated Assessment*, 4(1):5–17, 2003. Free online via ResearchGate. The clearest taxonomy of uncertainty levels and the appropriate decision tools at each level.

**If you want the deep version:**

1. Knight, Frank H. *Risk, Uncertainty and Profit*. Houghton Mifflin, 1921. Part III, Chapter 7 ("The Meaning of Risk and Uncertainty"). — The original Knight distinction. Historical; accessible. Read Chapter 7 only (~20 pages).

2. Lempert, Robert J., Popper, Steven W. & Bankes, Steven C. *Shaping the Next One Hundred Years: New Methods for Quantitative, Long-Term Policy Analysis*. RAND Corporation, 2003. Free PDF from rand.org. — The definitive treatment of "robust decision making" (RDM) for deep uncertainty in long-horizon policy. Directly applicable to AI strategy.

3. Taleb, Nassim Nicholas. *The Black Swan: The Impact of the Highly Improbable*. Random House, 2007. Chapters 1–5. — Taleb's account of why our probability models systematically miss tail events. Accessible and persuasive; the first five chapters make the case for why deep uncertainty tools matter without requiring the full book.


---

← [Day 12 — Multi-Criteria Decisions](day-12-multi-criteria-decisions) &nbsp;|&nbsp; [Day 14 — What Is a Game? →](day-14-what-is-a-game)
