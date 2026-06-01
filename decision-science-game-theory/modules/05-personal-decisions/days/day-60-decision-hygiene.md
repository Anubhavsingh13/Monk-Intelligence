*[Decision Science & Game Theory](../../../../README.md) · [Module 5 — Strategic Life & Career](../5-overview.md) · Day 60 of 65*

# Day 60 — Decision Hygiene: Pre-Mortems, Reference Classes, and Outside View

**Today's one idea:** The single most reliable improvement to decision quality is not more analysis — it is deliberately seeking the outside view (base rates, reference classes) before engaging with the details of your specific situation, because the planning fallacy and optimism bias almost universally cause us to underweight the base rate and overweight our unique narrative.

**Prerequisites:** Day 9 — Decision Under Uncertainty · [Day 56 — Career Game](./day-56-career-game-theory.md) · [Day 58 — Commitment Devices](./day-58-commitment-devices.md)

**Primary sources:** Kahneman, Daniel. *Thinking, Fast and Slow.* Farrar, Straus and Giroux, 2011. Chapters 19–24. Also: Lovallo, Dan & Kahneman, Daniel. "Delusion and Deception in Large Infrastructure Projects: Two Models for Explaining and Preventing Executive Disaster." *California Management Review,* 44(2), 2002.

**Estimated reading time:** ~35 minutes

---

## The Hook

In 2002, Daniel Kahneman consulted with a team writing a curriculum guide for the Israeli Ministry of Education. The project was already underway. He asked the team to estimate how long it would take to complete. The median estimate: 2 years.

He then turned to the most experienced person in the room — the curriculum expert with the most knowledge of similar projects. "How long," Kahneman asked, "do projects like this typically take?" The expert paused for a long moment and said: "I can't think of any that were completed in less than 7 years. Several took more than 10. And none that I know of were abandoned before completion."

The team knew this. They had all worked on curriculum projects before. They had all seen the pattern. And yet, when asked to estimate their own project, they produced 2 years — because they were reasoning from the inside: our team, our plan, our constraints. They had ignored the outside view entirely.

The guide was completed in 8 years.

This is not a story about incompetence. It is a story about a systematic bias that affects every planning decision you make — and it has a systematic fix.

---

## Building the Intuition

### 1. Inside View vs. Outside View (Kahneman & Lovallo 1993)

Kahneman and Lovallo introduced this distinction in a 1993 paper and it remains one of the most practically useful ideas in all of decision science.

**Inside view:** You reason from the specific details of your situation. You build a mental causal model: here are the steps, here are the resources, here are the potential obstacles, here is the timeline. You are working from the inside of your project outward to a prediction.

**Outside view:** You step back and ask a different question entirely: what is the distribution of outcomes for similar projects or decisions? What fraction succeed? What is the median time to completion? What does the histogram of cost overruns look like? You deliberately ignore the details of your specific case at first.

The inside view is seductive because it feels rigorous. You're being thorough — you've thought about the steps, the risks, the resources. But this thoroughness is an illusion. The causal model you're building is almost certainly missing dependencies, underestimating cooperation required from others, and defaulting to the best-plausible-case scenario rather than the median scenario.

**The planning fallacy** is the technical name for what happens when you use only the inside view: you systematically underestimate time, cost, and difficulty. This is not random error — it is a directional bias toward optimism. The inside-view plan is almost always better than what actually happens.

**The fix is mechanical:** always start with the reference class. Before you reason from the details of your situation, ask "what is the base rate for projects like this?" Then and only then adjust for the specific features of your case.

### 2. Reference Class Forecasting (Flyvbjerg 2006)

Bent Flyvbjerg, a Danish planning scholar, formalised this into a four-step method after studying decades of infrastructure project overruns. The method is directly applicable to personal decisions.

**Step 1: Identify the reference class.**
What category of decision or project is this? Be broad enough that the class contains enough historical cases to have statistical power. If you're starting a consumer software company, the reference class is "consumer software startups" — not "consumer software startups in my exact niche with my team profile" (too narrow, N too small) and not "all new businesses" (too broad, heterogeneous).

The choice of reference class is the most contestable step, and that's intentional — it forces you to be explicit about which prior experiences you're drawing on.

**Step 2: Look up the base rate.**
What fraction of projects in this class succeed (by whatever definition of success you're using)? What is the median timeline? The cost distribution? The P90 outcome?

Some base rates to have in your head:
- Startups: 80–90% fail within 10 years (definition-dependent, but consistently above 70%)
- Drug development: ~10% of drugs entering Phase 1 trials reach market
- Software projects over budget: median cost overrun ~45%, P90 overrun is 200%+
- Personal fitness goals: ~80% of gym members stop attending within 5 months of joining
- Book manuscripts submitted to agents: <1% are published

**Step 3: Adjust for the specific case.**
Does your project have genuine features that put it in the better or worse tail of the distribution? You may have an exceptional team, unique technology, or strong early signals that genuinely differentiate your situation from the median.

Apply these adjustments multiplicatively and conservatively. The adjustment for "my team is better than average" should be modest unless you have strong evidence.

**Step 4: Sanity-check your adjustment.**
If your final estimate is more than ~1 standard deviation better than the reference class mean, be suspicious. This is probably inside-view reasoning creeping back in through the adjustment step. The research on selection biases is stark: people who enter challenging endeavours consistently believe they are in the top quartile of their reference class. They can't all be right.

### 3. Pre-Mortem (Klein 2007)

Gary Klein, a cognitive psychologist, developed the pre-mortem as an antidote to the standard planning process.

**Standard process:** you build a plan, then ask the group "does this look reasonable?" The social dynamics of this question are terrible — people who have invested effort in the plan are motivated to defend it; raising objections feels like attacking colleagues; confirmation bias runs hot.

**Pre-mortem:** instead of asking "will this work?", you give the group a hypothetical: "It is one year from now. The project has failed. Not struggled — failed. Write the story of how it failed."

The reframing does something powerful: it licenses disconfirmatory thinking. You're no longer defending the plan, you're explaining a failure that has already occurred. The social obligation reverses — now you're *supposed* to surface problems.

Pre-mortems consistently surface failure modes that standard planning misses:
- **Underestimated dependencies.** "We assumed Legal would approve in two weeks, but they have a backlog and it typically takes eight."
- **Optimistic cooperation assumptions.** "We assumed the partner team would prioritise our integration, but they have their own roadmap."
- **External shocks.** "A competitor launched a similar product, or a regulation changed, or a key person left."
- **Goodhart failures.** This is the Day 46 connection: the metric you're optimising diverged from the goal you actually care about. "We hit our user acquisition targets but acquired the wrong users and retention was terrible."

After completing the pre-mortem, don't just record the failure modes — prioritise mitigations. Ask: which failure modes are (a) most likely, and (b) most under-mitigated in the current plan? These are your planning blind spots.

### 4. Debiasing Overconfidence

**Calibration** is the formal term for the alignment between your stated confidence and your actual accuracy. You are perfectly calibrated if, among all the predictions you make with 70% confidence, 70% come true — and the same holds at every confidence level.

Most people are overconfident: their 90% confidence intervals contain the true answer only 50–70% of the time. They are not lying about their uncertainty — they genuinely feel 90% confident. The confidence is miscalibrated.

**Superforecasters** (Tetlock & Gardner 2015 — covered in depth tomorrow in [Day 61](./day-61-calibration-practice.md)) are people who achieve near-optimal calibration through deliberate practice. The key distinction is not intelligence — it is a set of practices that override the natural drift toward overconfidence:

- They track their predictions and receive feedback (you can't improve calibration without a feedback loop).
- They think in probabilities, not binary outcomes. Not "will this deal close?" but "P(deal closes in Q3) = 65%."
- They distinguish between inside-view reasoning and base rate reasoning, and consciously weight both.
- They update on evidence rather than defending priors.

You can start this practice today. Keep a running document (the decision journal below) where you record predictions with stated confidence levels. Reviewing it every six months will reveal your systematic miscalibrations.

**Practical calibration exercise.** Answer the five trivia questions at the bottom of this page with 90% confidence intervals. Your intervals should contain the true answer 90% of the time. If 4 or 5 out of 5 answers fall within your intervals, you're reasonably calibrated. If 2 or fewer do, you're significantly overconfident. Most people score 2–3 out of 5 the first time they try this.

### 5. Decision Journals and Process Quality

Here is a subtle but important point: **you cannot evaluate decision quality by outcomes alone.**

A good decision can produce a bad outcome (you made the right call given available information, but you were unlucky). A bad decision can produce a good outcome (you made a poor call, but got lucky). If you only track outcomes, you'll reinforce the decision-making process that recently produced good outcomes — which may be the process that happened to be lucky, not the process that is genuinely good in expectation.

Annie Duke, a professional poker player and decision theorist, calls this "resulting" — judging decisions by their outcomes rather than by the quality of the reasoning at the time. A poker player who makes a mathematically correct call and loses will be tempted to question the call. A player who makes a mathematically incorrect call and wins will be reinforced. This is how bad decision habits form.

**The decision journal** breaks this trap. For every significant decision, record:
1. The decision itself (what you chose, what you declined)
2. The information available to you at the time of the decision
3. Your reasoning and the alternatives you considered
4. Your uncertainty level (stated as a probability or confidence range)
5. The reference class base rate you identified

When you review it later, you evaluate the process on steps 2–5 — not just whether the outcome was good. Over time, this builds genuine process feedback: you'll see where you consistently used inside-view reasoning when the outside view was available, where you failed to surface alternatives, where your stated confidence was systematically too high.

Date-stamp every entry and resist the urge to revise your stated reasoning after you learn the outcome. Memory is reconstructive and self-serving — you will unconsciously remember your reasoning as being more consistent with the outcome than it actually was.

### 6. When More Information Hurts

There is a common failure mode in careful decision-makers: collecting ever more information to avoid making a decision, under the theory that more information always helps.

It doesn't. More information can harm decision quality in two ways:

**Analysis paralysis.** Each new piece of information feeds another round of inside-view narrative reasoning. You're building an increasingly elaborate causal story about your specific situation — which is exactly the cognitive process that most reliably leads you away from the reference class base rate.

**Diminishing EVSI.** The expected value of sample information (from Day 9) decreases as the decision becomes more constrained or as prior information already captures most of what's knowable. At some point, the next piece of information simply won't change your decision — and gathering it is pure cost.

Jeff Bezos's "70% rule": make most decisions when you have ~70% of the information you wish you had. Waiting for 90% is too slow; deciding at 50% is too uncertain. The exact number is context-dependent, but the principle is correct: there is an optimal information threshold, and most careful decision-makers stop well short of it.

The EVSI framework from Day 9 gives you the formal tool to estimate when to stop gathering information: if the expected value of one more piece of information is less than the cost of obtaining it (including delay cost), stop and decide. For reversible decisions, the cost of deciding early is low — you can update later. For irreversible decisions, the EVSI of additional information is higher, and you should gather more before committing.

---

## Formal Picture

**Reference class adjustment (Bayesian framing).** Start with the base rate for your reference class. When you identify a specific feature of your situation that you believe places you in the better tail, you are implicitly doing Bayesian updating: asking how much more likely people with your features are to succeed than the average member of the reference class. In practice, you rarely have precise estimates of these likelihood ratios. The heuristic: multiply your base rate by a conservative adjustment factor for each genuinely differentiating feature. If the base rate is 20% and you identify two features you believe each add one-third extra probability weight, your adjusted estimate might be 20% × 1.3 × 1.2 ≈ 31%. That is a long way from "my project is different so I'll definitely succeed." The adjustment should be modest unless you have strong external evidence that your specific features predict success in this reference class.

**Calibration score — Brier score.** Your Brier score is the average squared difference between your probabilistic forecast and what actually happened (1 for an event that occurred, 0 for one that didn't). Lower is better. A perfect forecaster scores 0. A forecaster who always says "50-50" scores 0.25. A well-calibrated forecaster typically achieves below 0.15. A forecaster who always says "certain" and is wrong half the time scores 1.0. You can track your Brier score on your prediction log: for each resolved prediction, square the difference between your stated probability and the outcome, then average over all resolved predictions. Improvement over time is measurable.

**EVSI (Day 9 connection).** The expected value of gathering one more piece of information is the difference between your expected decision quality with that information versus without it. Gather more when this value exceeds the cost of obtaining it. The practical rule: if your current base rate and inside-view analysis already push your probability estimate well above or well below your action threshold, additional information has low expected value — it won't change what you do. If you're right on the fence, additional information may tip you one way or the other, so its expected value is higher. The cost of information includes not just money but time and the delay cost of acting later.

---

## Where It Breaks

**Reference class is hard to define.** "What is my reference class?" is itself a judgment that can be gamed. A startup founder can choose "all startups" (80%+ failure rate) or "startups in my sector with my team profile" (maybe 40% failure rate). The choice of reference class is contestable, and you can unconsciously choose a flattering class. The discipline: start with a broad class, then narrow it only with explicit justification that the narrow class is genuinely different. If you can't articulate why the narrow class has a different failure mechanism, use the broad one.

**Base rates don't account for selection.** If you've spent six months studying decision science, you're systematically different from the median person starting a company or making a career decision. Your reference class may be genuinely better-calibrated-than-average. But note: selection bias also means that the reference class is populated by people who all thought they were the exception. Selection doesn't explain away the base rate; it explains some of the overrepresentation of confident people in the failing-to-beat-the-base-rate category.

**Decision journals require honesty.** The self-serving memory bias is strong. People genuinely believe, six months later, that their reasoning at the time was more aligned with the eventual outcome than it was. The only antidote is date-stamped, specific records written before you know the outcome. Vague records ("I thought this might be risky") are useless because they can be post-hoc reconstructed in either direction.

**The outside view can be demoralising.** Telling yourself "80% of startups fail" before launching one is technically correct and practically difficult. This is real. The outside view is not a reason not to try — it is a tool for honest planning. Understanding the base rate helps you: (a) plan for the likely scenario, not just the hoped-for scenario; (b) identify what would have to be true for you to be in the success tail; (c) decide whether you're willing to accept the base-rate odds before committing.

---

## Try It Yourself

**Exercise 1 — Reference class analysis.**
You're considering starting a side project: a newsletter, a product, a consulting practice. Answer:
- What is the reference class? (Be explicit. Don't choose a reference class that's suspiciously flattering.)
- What is the base rate for success? Define "success" specifically before looking up the base rate.
- What specific, verifiable features of your situation place you in the better or worse tail?
- What is your adjusted probability of success, and what would have to be true for that adjustment to be correct?

**Exercise 2 — Pre-mortem.**
Choose one current project or plan — a career decision, a major purchase, a commitment you've recently made. Write a 200-word pre-mortem: it is 12 months from now, and the project has failed. What happened? After writing the failure story, identify:
- The top three failure modes you surfaced
- Which one is most likely
- Which one is most under-mitigated in your current plan

Spend 20 minutes on mitigations for that third failure mode.

**Exercise 3 — Calibration check.**
Answer these five questions with 90% confidence intervals. A 90% CI means you are 90% confident the true answer falls within your range. Resist the urge to make your interval extremely wide — a 90% CI of "1 to 1 trillion" for the year of the first commercial airplane flight is not useful.

(a) Year in which the first commercial passenger airline operated regular scheduled service
(b) Population of Brazil (2024 estimate)
(c) Year Einstein published the special theory of relativity
(d) Number of countries in Africa (as recognised by the African Union)
(e) Nominal GDP of India in USD (most recent available year)

After answering, check the true values. Score: how many of your 90% intervals contained the true answer? Target is 4 to 5 out of 5. If you score 2 or fewer, you are significantly overconfident and the calibration training in [Day 61](./day-61-calibration-practice.md) is particularly high-leverage for you.

---

## Connect It Back

Decision hygiene is the maintenance of the cognitive engine that processes all the other tools you've built. Over the past 59 days, you've learned game theory, mechanism design, reinforcement learning, and behavioural economics. These tools are powerful — and they're deployed by a human reasoner who is subject to the planning fallacy, inside-view bias, overconfidence, and outcome-based learning.

Decision hygiene doesn't add new content — it ensures the existing content is applied without systematic distortion. The reference class check, the pre-mortem, the decision journal, and the calibration practice are maintenance procedures for a cognitive system that drifts toward optimism and inside-view narrative under normal operating conditions.

Tomorrow — [Day 61: Calibration Practice](./day-61-calibration-practice.md) — you move from understanding calibration conceptually to training it deliberately: Fermi estimation, prediction logs, the Brier score in practice, and the specific habits of superforecasters. That day is the practical companion to today's theoretical foundation.

---

## Suggested Readings

- Kahneman, Daniel. *Thinking, Fast and Slow.* Farrar, Straus and Giroux, 2011 — Chapters 19–24. Chapters 19–21 cover the planning fallacy and inside/outside view directly. Chapters 22–24 cover overconfidence and expert judgment.
- Tetlock, Philip E. & Gardner, Dan. *Superforecasting: The Art and Science of Prediction.* Crown Publishers, 2015 — Chapters 1–4. This introduces the Good Judgment Project and the empirical evidence that calibration is trainable.
- Klein, Gary. "Performing a Project Premortem." *Harvard Business Review,* September 2007. Short (3 pages) and directly actionable. Recommended before your next significant planning meeting.


---

← [Day 59 — Negotiation: Applied Bargaining Theory](day-59-negotiation) &nbsp;|&nbsp; [Day 61 — Calibration Practice →](day-61-calibration-practice)
