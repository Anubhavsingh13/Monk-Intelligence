*[Decision Science & Game Theory](../../../../README.md) · [Module 1 — The Science of Choosing](../1-overview.md) · Day 9 of 65*

# Day 9 — The Value of Information

> **Today's one idea:** The value of learning something new equals the improvement it creates in your best decision — and this quantity is precisely computable, telling you exactly when to gather more data and when to act now.
> **Reading time:** ~35 min · **Prereqs:** [Day 2](day-02-expected-value.md), [Day 5](day-05-bayesian-updating.md)
> **Primary source for today:** Howard, Ronald A. & Abbas, Ali E. *Foundations of Decision Analysis*. Pearson, 2015. Chapter 7 ("The Value of Information").

---

## The Hook

A junior PM asks: "Should we run a user study before we build this feature?"

A senior PM asks: "What decision would this user study change, and by how much?"

The junior is asking about process. The senior is asking about *value* — and that is the only question that matters.

User studies, A/B tests, market research, and technical spikes all cost time, money, and opportunity. The only way to decide whether to gather information is to estimate what it is worth.

The surprising thing: this is computable. Not precisely — but precisely enough to make better decisions about when to investigate and when to ship.

---

## Building the Intuition

### A toy problem

You are deciding whether to launch Feature X. Based on what you know right now:
- 40% chance the feature succeeds (generates $500K in revenue)
- 60% chance the feature fails (costs $100K in engineering with no revenue gain)

**Option A — Launch now:**
```
EV(launch) = (0.40 × $500K) + (0.60 × −$100K)
           = $200K − $60K
           = +$140K
```

**Option B — Don't launch:**
```
EV(don't launch) = $0
```

Launch now. EV = $140K > $0.

Now a researcher offers you a perfect oracle: before you launch, the oracle tells you whether the feature will succeed or fail. What is this oracle worth?

If the oracle says "success" (40% of the time):
→ You launch. Gain $500K.

If the oracle says "failure" (60% of the time):
→ You don't launch. Gain $0 (avoid the $100K loss).

```
EV(with perfect information) = (0.40 × $500K) + (0.60 × $0)
                              = $200K
```

**Value of Perfect Information (EVPI)** — the most any information could ever be worth for this decision:
```
EVPI = EV(with perfect info) − EV(best decision without info)
     = $200K − $140K
     = $60K
```

The oracle is worth at most $60,000. If a user study costs $10K and gives you something close to perfect information, run it. If it costs $80K, don't.

**This is the fundamental insight:** EVPI is a ceiling. No research project should cost more than EVPI. If it does, act now.

---

### Why EVPI is never zero (or is it?)

Without information, you must commit to one action before knowing the outcome.

With perfect information, you can tailor your action to each scenario — launch if success, skip if failure. You always choose the best action given the situation.

EVPI measures the improvement from being able to choose optimally in each scenario. It is the *cost of having to commit early*.

```
State: Success (p=0.4)          State: Failure (p=0.6)

Without info: launch             Without info: launch
  Payoff: +$500K                   Payoff: −$100K

With info: launch                With info: don't launch
  Payoff: +$500K                   Payoff: $0

Improvement from info:
  Success state: $0                Failure state: +$100K

EVPI = 0.4×$0 + 0.6×$100K = $60K  ✓
```

EVPI is zero only if your best action is the same regardless of what you learn. When information cannot change your decision, it has no value — even if it costs nothing to get.

---

### Imperfect information (EVSI)

Real research doesn't give you an oracle. A user study might reveal "users seem to like it" or "users seem confused." These signals are correlated with success or failure but not perfectly.

**Expected Value of Sample Information (EVSI)** — the value of a real, imperfect signal:
```
EVSI = EV(best strategy with the signal) − EV(best decision without signal)
```

EVSI is always ≤ EVPI. A noisy signal can't do better than a perfect one.

To compute EVSI you need:
- How likely each signal is, given each outcome (how diagnostic is the test?)
- Bayesian updating to revise your belief after each signal (Day 5)
- The best action given each updated belief

**Example — a diagnostic user study:**

The study costs $15K. Its properties:
- If the feature will succeed: 80% chance the study says "positive," 20% "negative"
- If the feature will fail: 30% chance the study says "positive," 70% "negative"

**Step 1: How likely is each signal?**
```
P(positive signal) = P(pos|success)×P(success) + P(pos|failure)×P(failure)
                   = 0.80×0.40 + 0.30×0.60
                   = 0.32 + 0.18 = 0.50

P(negative signal) = 0.20×0.40 + 0.70×0.60
                   = 0.08 + 0.42 = 0.50
```

**Step 2: Update your belief after each signal (Bayes' theorem)**
```
P(success | positive) = (0.80×0.40) / 0.50 = 0.64
P(success | negative) = (0.20×0.40) / 0.50 = 0.16
```

**Step 3: Best action under each updated belief**

*If study says positive (you now think 64% chance of success):*
```
EV(launch | positive) = (0.64×$500K) + (0.36×−$100K) = $320K − $36K = $284K
EV(don't launch)      = $0
→ Launch. Value = $284K
```

*If study says negative (you now think only 16% chance of success):*
```
EV(launch | negative) = (0.16×$500K) + (0.84×−$100K) = $80K − $84K = −$4K
EV(don't launch)      = $0
→ Don't launch. Value = $0
```

**Step 4: Compute EVSI**
```
EV(with study) = P(positive)×$284K + P(negative)×$0
               = 0.50×$284K + 0.50×$0
               = $142K

EVSI = EV(with study) − EV(best without study)
     = $142K − $140K
     = $2K
```

The study is worth only $2,000 in expectation. It costs $15,000. **Don't run it.** The study's diagnostic power isn't strong enough to change your decision often enough to be worth the cost.

---

## The Formal Picture

**EVPI (Expected Value of Perfect Information):**

In plain terms:

```
EVPI = (what you'd earn if you always knew the right answer before deciding)
     − (what you earn choosing the best action under your current uncertainty)
```

The first number: imagine you could always pick the perfect action for each possible state of the world. What would you earn on average? The second number: your best expected value without any new information — your current plan.

EVPI ≥ 0 always. More information never hurts in theory — you can always choose to ignore it.

**Key properties:**
- Information that doesn't change your optimal action has EVSI = 0, regardless of cost
- Information is most valuable when you are near-indifferent between actions, the decision is high-stakes, and the signal is highly diagnostic
- Information is worthless when your optimal action is obvious regardless of the state, the signal is noise, or the decision is already made

---

### When is information most valuable?

Three things drive high EVPI and EVSI:

**1. Decision sensitivity:** If new information would change what you do, it has value. If you'd act the same regardless, it doesn't.

**2. Stakes:** A 5% belief update matters far more on a $10M decision than a $10K one.

**3. Signal quality:** The more a test separates success from failure, the more of EVPI it captures.

A useful check: *"What would have to be true for me to make a different decision?"* If the answer is plausible, information is valuable. If it requires extraordinary evidence, act now.

---

## Where It Breaks / What It Is Not

**Limitation 1: EVPI assumes you know your starting probabilities.**
The calculation requires P(success) = 0.40. If that starting belief is way off, so is your EVPI. Calibration (Day 4) comes first.

**Limitation 2: Waiting for information has a cost.**
Delay means competitors can move. The real calculation is EVSI minus the cost of waiting — which requires estimating what you lose by not acting sooner.

**Limitation 3: Gathering information is itself a signal.**
Running a public user study about Feature X tells competitors you're building it. Sometimes "research" is a strategic action with real consequences. Module 2 formalises this.

**Limitation 4: EVPI is not the value of certainty.**
Perfect information means knowing the outcome before you choose — not controlling the outcome. Don't confuse them.

---

## Try It Yourself

**Exercise 1 — Compute EVPI (10 min)**

You are deciding whether to enter a new market. Estimates:
- 30% chance the market is attractive: entry earns $2M profit
- 70% chance the market is unattractive: entry costs $500K with no return

a) What is the EV of entering?
b) What is the EV of not entering?
c) What is the EVPI?
d) A market research firm offers a study for $200K. Should you commission it, assuming perfect information?

<details>
<summary>Worked answer</summary>

**a)** EV(enter) = (0.30 × $2M) + (0.70 × −$0.5M) = $600K − $350K = **$250K**

**b)** EV(don't enter) = $0

**c)** With perfect info:
- Market attractive (30%): enter, earn $2M
- Market unattractive (70%): don't enter, earn $0

EV(perfect info) = (0.30 × $2M) + (0.70 × $0) = $600K

EVPI = $600K − $250K = **$350K**

**d)** Yes — $200K < $350K, so even a perfect study would be worth buying. Commission it.

</details>

---

**Exercise 2 — EVSI with a noisy signal (15 min)**

Same market entry problem. A consultant offers a report for $50K with these characteristics:
- If market is attractive: 75% chance report says "enter," 25% says "wait"
- If market is unattractive: 20% chance report says "enter," 80% says "wait"

a) What is EVSI?
b) Should you commission the $50K report?

<details>
<summary>Worked answer</summary>

**Signal probabilities:**
```
P("enter" signal) = 0.75×0.30 + 0.20×0.70 = 0.225 + 0.14 = 0.365
P("wait" signal)  = 0.25×0.30 + 0.80×0.70 = 0.075 + 0.56 = 0.635
```

**Posteriors:**
```
P(attractive | "enter") = (0.75×0.30)/0.365 = 0.225/0.365 ≈ 0.616
P(attractive | "wait")  = (0.25×0.30)/0.635 = 0.075/0.635 ≈ 0.118
```

**Optimal action under each signal:**

*If "enter" signal (P(attractive)=0.616):*
```
EV(enter) = 0.616×$2M + 0.384×(−$0.5M) = $1,232K − $192K = $1,040K
EV(don't) = $0 → Enter. Value = $1,040K
```

*If "wait" signal (P(attractive)=0.118):*
```
EV(enter) = 0.118×$2M + 0.882×(−$0.5M) = $236K − $441K = −$205K
EV(don't) = $0 → Don't enter. Value = $0
```

**EVSI:**
```
EV(with signal) = 0.365×$1,040K + 0.635×$0 = $379.6K
EVSI = $379.6K − $250K = $129.6K
```

**Decision:** $129.6K > $50K — **commission the report.**

Note: The report captures $129.6K of the $350K EVPI. It is imperfect (doesn't capture the full value) but still worth its cost.

</details>

---

**Exercise 3 — When not to research (5 min)**

Your team wants to run a $40K user study before deciding whether to add dark mode to your app.

Without research: you would add dark mode (EV positive, low downside).

Roughly: What would EVPI need to be for the $40K study to be worth it, and is that plausible for a dark mode decision?

*(No precise calculation needed — reason qualitatively using the "what would change my decision?" test.)*

<details>
<summary>Discussion</summary>

EVPI must be > $40K for any research to be worth it. For dark mode:
- The decision is low-risk (implementation cost is modest, reversal is easy)
- The outcome is relatively certain (dark mode is almost universally wanted and technically proven)
- The study cannot give you information that would make you *not* add dark mode

If your action is the same regardless of what the study reveals, EVSI = 0. This is a case where you should ship, not study. Save the $40K.

The VoI framework is particularly valuable for surfacing exactly this: many "should we do more research?" conversations are really "we're not comfortable deciding" conversations dressed up as epistemics.

</details>

---

## Connect It Back

Yesterday you learned that Prospect Theory describes how humans actually make decisions — systematically different from EU. Today you got one of the most practical tools in the course: a principled way to decide whether gathering more information is worth the cost. Together, these two form a complete picture for the decision before the decision: should I decide now, or learn more first?

Tomorrow (Day 10) you get the primary structural tool for *organising* a multi-stage decision: the decision tree. Decision trees are the operational form of everything you've built in Days 2, 5, and 9 — they make EV, Bayesian updating, and VoI calculations concrete and manageable for real decisions.

**The question you should be able to answer now:**
*If your optimal action is the same regardless of what a study reveals, what is its EVSI — and what does that tell you about whether to run it?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Howard & Abbas, *Foundations of Decision Analysis*, Chapter 7, Sections 7.1–7.4. The clearest textbook treatment of EVPI and EVSI with worked examples. Requires some comfort with probability notation but builds slowly.

**If you want the deep version:**

1. Raiffa, Howard. *Decision Analysis: Introductory Lectures on Choices Under Uncertainty*. McGraw-Hill, 1968. Chapter 4 ("The Value of Perfect Information"). — The original decision-analytic treatment of VoI. Dense but elegant; the numerical examples are still the clearest worked cases available.

2. Clemen, Robert T. & Reilly, Terence. *Making Hard Decisions with DecisionTools*. 3rd ed., Cengage, 2014. Chapter 12 ("Value of Information"). — Modern textbook with Excel-compatible examples and a thorough treatment of EVSI for continuous distributions.

3. For AI applications: Chapter 16 of Sutton & Barto, *Reinforcement Learning*, discusses the exploration bonus as an implicit value-of-information calculation — the agent trades immediate reward for information that improves future decisions. Preview for Day 42.


---

← [Day 8 — Prospect Theory](day-08-prospect-theory) &nbsp;|&nbsp; [Day 10 — Decision Trees →](day-10-decision-trees)
