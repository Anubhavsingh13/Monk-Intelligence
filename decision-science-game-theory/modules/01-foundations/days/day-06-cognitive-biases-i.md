*[Decision Science & Game Theory](../../../../README.md) · [Module 1 — The Science of Choosing](../1-overview.md) · Day 6 of 65*

# Day 6 — Cognitive Biases I: Perception Traps

> **Today's one idea:** Anchoring, availability, and framing systematically distort how we perceive options — before we even begin reasoning — and these distortions are predictable enough to design around.
> **Reading time:** ~35 min · **Prereqs:** [Day 1](day-01-how-decisions-actually-happen.md), [Day 5](day-05-bayesian-updating.md)
> **Primary source for today:** Kahneman, *Thinking, Fast and Slow*, Chapters 11–13 (anchoring, availability) and Chapter 34 (framing).

---

## The Hook

In 1974, Amos Tversky and Daniel Kahneman published a paper called "Judgment Under Uncertainty: Heuristics and Biases." It is one of the most-cited papers in the history of psychology, and it was built on a single uncomfortable insight:

Human judgment is not random noise. It is *systematically wrong in predictable directions*.

The biases they documented are not failures of intelligence. They are byproducts of the same mental machinery that makes us efficient. Mental shortcuts — rules of thumb — work well in familiar situations. They fail, reliably, in the situations that matter most for modern decision-making: probability estimation, evaluating options, predicting outcomes.

This page covers three biases that corrupt the *perception* stage — they distort how the decision arrives in your mind before you start reasoning. Day 7 covers the biases that corrupt the *evaluation* stage.

---

## Building the Intuition

### Bias 1: Anchoring

**The experiment:** Tversky and Kahneman spun a wheel rigged to stop at either 10 or 65. They then asked subjects: "Is the percentage of African countries in the UN higher or lower than this number? What is your best estimate?"

Subjects who saw 10: average estimate = 25%
Subjects who saw 65: average estimate = 45%

The wheel spin was visibly random. Subjects *knew* it was random. And yet it pulled their estimates toward the anchor by about 20 percentage points.

**Why it happens:** When you encounter a number — any number — your fast-thinking brain uses it as a starting point and adjusts from there. The problem is that adjustment is almost always insufficient. You stop adjusting when you reach a value that "seems plausible," but you stop too soon, pulled back toward the anchor.

**Where it appears in real life:**
- *Salary negotiation:* The first offer anchors the entire negotiation. Whoever makes the first offer wins more on average.
- *Pricing:* "Was $299, now $149" anchors the reference price at $299, making $149 feel like a deal regardless of actual value.
- *Estimates:* "Our last sprint took 2 weeks — how long will this one take?" The previous sprint anchors the new estimate.
- *AI product:* If you show users a default value in a form, most will use it. The default is an anchor.

**How to counter it:**
1. *Consider the opposite:* Before finalising an estimate, ask "what would make this dramatically higher or lower?"
2. *Generate your own estimate first:* Don't let an anchor into your mind before forming your view.
3. *Multiple reference points:* Deliberately seek anchors from different directions.

---

### Bias 2: Availability

**The experiment:** Kahneman asked subjects: "Are there more words in English that start with K, or have K as the third letter?"

Most people answer: "Start with K."

The correct answer: K appears as the third letter roughly twice as often as at the start. But it is far easier to think of words starting with K (king, kit, keen) than words with K in third position (awkward, like, make). The ease of retrieval corrupts the probability estimate.

**The mechanism:** Your fast-thinking brain estimates frequency by asking "how easily do examples come to mind?" Vivid, recent, emotional, or frequently-discussed events are easier to retrieve — so they feel more common.

**Where it appears in real life:**
- *Risk perception:* People overestimate the frequency of plane crashes (vivid, media-covered) and underestimate car accidents (normalised). This is why people fear flying more than driving, despite cars being far more dangerous per hour travelled.
- *Post-mortem analysis:* The most recent failure dominates the post-mortem, even if it was atypical.
- *Product decisions:* The loudest customer complaint (vivid, emotional) gets more engineering resources than the silent majority's need (diffuse, unspoken).
- *Hiring:* The candidate you interviewed last is easiest to recall, which biases your comparison.

**How to counter it:**
1. *Track base rates:* Don't rely on memory — look up actual frequencies.
2. *Slow down for high-stakes risk estimates:* Ask "what are all the ways this could go wrong?" not "what comes to mind?"
3. *Diversify your information sources:* If you only read headlines about AI risks, you will miscalibrate on AI risks.

---

### Bias 3: Framing

**The experiment:** Two groups were given identical scenarios but described differently.

*Group A:* "A disease will kill 600 people. Program A will save 200 people. Program B has 1/3 probability of saving all 600, 2/3 probability of saving no one. Which do you choose?"

*Group B:* "A disease will kill 600 people. Program A will result in 400 deaths. Program B has 1/3 probability of no deaths, 2/3 probability of 600 deaths. Which do you choose?"

Group A: 72% chose Program A (the certain option).
Group B: 22% chose Program A (the certain option).

**Mathematically identical choices. Drastically different decisions.**

"Save 200" and "400 deaths" describe the same outcome. But "save" triggers a gains mindset (take the safe option), while "deaths" triggers a losses mindset (risk it to avoid the loss). Prospect Theory (Day 8) gives us the formal model for this asymmetry. The key insight today: the *way an option is described* changes how it is valued.

**Where it appears in real life:**
- *Subscription pricing:* "Pay $120/year" vs. "Pay $10/month" — same amount, different psychology. Monthly feels smaller.
- *Medical consent:* Doctors who present procedures as "90% survival rate" get higher consent than those presenting "10% mortality rate."
- *AI outputs:* "Your data is used to improve our services" vs. "We share your data with our AI training team" — technically identical, very different reactions.
- *Product onboarding:* "Join 10,000 users who trust us" vs. "9 out of 10 users succeed" — same fact, different emphasis.

---

## The Formal Picture

All three biases share a common structure. They are properties of how your fast-thinking brain *represents* the decision — not failures of computation on a correctly-perceived problem.

**Anchoring — what's actually happening:**

Research by Epley and Gilovich (2006) showed that anchoring works through two sub-processes:
1. *Selective accessibility:* The anchor activates information consistent with it, biasing what your slow-thinking brain works with.
2. *Insufficient adjustment:* Even when you explicitly try to move away from the anchor, you stop too soon.

The bias persists even when you are told the anchor is random, when you are incentivised to be accurate, and when you are an expert in the domain. Knowing about anchoring doesn't fix it — structural processes do.

**Availability — what's actually happening:**

The availability shortcut replaces the hard question "how frequent is X?" with the easy question "how easily do examples of X come to mind?" Kahneman calls this *attribute substitution* — your brain quietly swaps a hard question for an easier one.

The problem: ease of retrieval is driven by factors that have nothing to do with actual frequency — recency, emotional impact, media coverage, personal experience. These introduce *systematic bias* rather than random error.

**Framing — what's actually happening:**

Framing effects violate a basic principle of rational choice: descriptions that mean the same thing should produce the same choice. Their systematic violation means that preferences are not defined over *outcomes* but over *descriptions of outcomes*. Prospect Theory (Day 8) formalises this via the idea of a reference point.

---

### A taxonomy of today's biases

| Bias | What it distorts | Mechanism | Key countermeasure |
|------|-----------------|-----------|-------------------|
| Anchoring | Numerical estimates | Insufficient adjustment from first number | Generate estimate before seeing anchor; consider opposite |
| Availability | Frequency/probability | Ease-of-retrieval substituted for actual frequency | Look up base rates; diversify information diet |
| Framing | Option evaluation | Reference point / description shifts risk preference | Reframe deliberately; examine both gain and loss descriptions |

---

## Where It Breaks / What It Is Not

**Misconception 1: "I know about biases, so I'm immune."**

No. This is one of the most consistent findings in the field. Knowledge of a bias reduces its effect slightly in some conditions and not at all in others. A 2015 meta-analysis by Sellier, Scopelliti and Morewedge found that debiasing training had modest effects — and the effects were smallest in real-world high-stakes situations, exactly when you need them most. What works: *processes* (checklists, pre-mortems, structured decision frameworks) rather than willpower.

**Misconception 2: "These biases only affect unsophisticated people."**

Kahneman, Slovic, and Tversky ran many experiments on expert populations: judges, financial analysts, medical doctors. All showed substantial anchoring, availability, and framing effects. Expertise helps within a domain with tight feedback loops. It does not generalise to novel decision structures.

**Misconception 3: "Framing is just spin."**

Framing effects are not manipulation in a simple sense — they are a fundamental property of how humans encode information. A communicator who presents data in a gains frame is not lying; they are choosing a description. The ethical question is whether the chosen frame serves the audience's interests or exploits the bias against those interests. This distinction will be central to Day 53 (nudge design).

**What these biases do not explain:** Why people are *more* risk-seeking in the losses domain than the gains domain. That requires Prospect Theory (Day 8) — we will see framing's deeper mechanism there.

---

## Try It Yourself

**Exercise 1 — Anchoring detection (5 min)**

For each scenario, identify the anchor and predict the direction of bias:

a) You are asked to estimate the year Gandhi was born. Before answering, you read that Einstein was born in 1879.

b) A VC says "we usually fund at $3M valuations" before you present your pitch.

c) Your PM says "the last sprint took 3 weeks" before you estimate the next sprint.

d) A product review site shows you 847 reviews before you read any of them.

<details>
<summary>Discussion</summary>

a) The 1879 anchor will pull Gandhi estimates down. (Gandhi was born in 1869 — close, but the anchor still creates systematic pull.)

b) The $3M anchor will make you less likely to ask for $5M and more likely to accept $3M. Form your own valuation analysis *before* the meeting.

c) The 3-week anchor will pull estimates toward 3 weeks regardless of the actual scope difference.

d) 847 reviews anchors your expectation of a popular, reliable product — before you have read quality signals. Review *count* is being used as a proxy for review *quality*.

</details>

---

**Exercise 2 — Reframe a product decision (10 min)**

Take a decision you are currently facing (or have recently faced) in your work. Write it out in:
1. A gains frame ("If we do X, we gain Y")
2. A losses frame ("If we don't do X, we lose Y")
3. A neutral frame (describe only the outcomes and their probabilities without gain/loss language)

Does your preference change across framings? If so, the neutral frame is your best attempt at a description-invariant representation.

---

**Exercise 3 — Availability audit (10 min, L2)**

You are making a risk assessment for a new AI model deployment. List the top 5 failure modes that come to mind *immediately* (your availability list). Then:

1. For each, ask: Is this high on my list because it is genuinely frequent or severe — or because it is vivid, recent, or media-covered?
2. Look up or estimate actual base rates for each failure mode.
3. After the base-rate check, re-rank your 5 failure modes. What changed?

---

## Connect It Back

Today's three biases corrupt the *perception* stage — they distort how the decision arrives in your mind before you begin to reason. The anchor contaminates your numerical estimate. The availability shortcut distorts your probability assessment. The frame determines whether you approach the decision as a gain or a loss.

Tomorrow (Day 7) covers the biases that operate *during* evaluation — once you have perceived the decision, these forces warp how you weigh it. Loss aversion, sunk cost, and status quo bias are the three largest. Together with today's three, they form the complete picture that Prospect Theory (Day 8) synthesises into one formal model.

**The question you should be able to answer now:**
*Why does knowing about anchoring bias not protect you from it — and what does protect you?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Kahneman, *Thinking, Fast and Slow*, Chapter 11 ("Anchors") and Chapter 13 ("Availability, Emotion, and Risk"). Chapter 11 has the most striking anchoring experiments. Chapter 13 connects availability to risk perception — directly relevant to AI product safety decisions.

**If you want the deep version:**

1. Tversky, Amos & Kahneman, Daniel. "Judgment under Uncertainty: Heuristics and Biases." *Science*, 185(4157):1124–1131, 1974. — The original paper. 7 pages. Read it in full — it is a masterpiece of concise science writing and directly covers all three biases from today.

2. Ariely, Dan. *Predictably Irrational: The Hidden Forces That Shape Our Decisions*. HarperCollins, 2008. Chapter 2 ("The Fallacy of Supply and Demand"). — Ariely's experiments on anchoring in pricing, told as accessible narratives. Good for building intuition for product pricing applications.

3. Thaler, Richard H. & Sunstein, Cass R. *Nudge*, Chapter 1 ("Biases and Blunders"). — Connects all three today's biases to choice architecture design. The conceptual bridge between psychology and product design.


---

← [Day 5 — Bayesian Updating](day-05-bayesian-updating) &nbsp;|&nbsp; [Day 7 — Cognitive Biases II →](day-07-cognitive-biases-ii)
