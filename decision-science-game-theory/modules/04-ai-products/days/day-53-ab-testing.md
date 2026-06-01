*[Decision Science & Game Theory](../../../../README.md) · [Module 4 — Decision Science in AI Products](../4-overview.md) · Day 53 of 65*

# Day 53 — A/B Testing as Sequential Decision-Making

**Module 4 · AI Products & Decision Systems**
**Reading time:** ~35 minutes
**Prerequisites:** [Day 42 — Exploration-Exploitation & Bandits](./day-42-exploration-exploitation.md) · [Day 52 — Nudge Design & Choice Architecture](./day-52-nudge-design.md) · [Day 9 — Value of Information & Decision Under Uncertainty](../../01-foundations/days/day-09-value-of-information.md)

---

## Today's One Idea

A/B testing is not just a statistical procedure — it is an online learning problem in disguise. Every experiment is a bandit pull, stopping rules are exploration-exploitation trade-offs, and the decision of *when to stop collecting data* is itself a decision under uncertainty.

---

## The Hook

Picture the morning ritual at a mid-sized tech company. A product manager opens the experiment dashboard. The A/B test for the new checkout button has been running for four days. The treatment group's conversion rate is sitting at 5.3% versus 4.8% for control. The p-value is 0.04. It's below 0.05. She flags it as significant and ships the feature.

This scene plays out thousands of times per day across the industry. And it is, from a statistical standpoint, largely broken.

The standard A/B test procedure is: split users randomly between control (A) and treatment (B), collect data for a predetermined period, run a t-test, declare significance at p < 0.05. This is the dominant experimental paradigm in tech. It is also riddled with structural problems that are not minor statistical quibbles — they are fundamental mismatches between a static statistical framework and the sequential decision-making reality of online experimentation.

**The peeking problem.** Most teams check results early and stop when p < 0.05. Johari et al. (2017) showed this inflates Type I error from the nominal 5% to approximately 26% under realistic conditions. One in four "significant" results found this way is noise.

**The winner's curse.** When you stop at the moment a test crosses significance, the measured effect size is biased upward. The true effect is almost always smaller than what you measured at stopping time. You shipped a feature expecting a 10% lift; you got 4%.

**The fixed-sample fallacy.** The statistical validity of a t-test depends on the sample size being determined before the experiment begins. But in practice, experiments run continuously — users arrive in a stream, and the decision to stop is made in real time, not in advance.

These problems have a common root: the standard frequentist A/B test assumes a world where you commit to a sample size, collect that sample, and then decide once. Production experimentation is nothing like this. It is a sequential process — data arrives one observation at a time, decisions are made at any point, and the experimenter is always watching. What's needed is a framework built for that sequential reality.

That framework already exists. It lives at the intersection of statistics and the bandit problem you met on [Day 42](./day-42-exploration-exploitation.md).

---

## Building the Intuition

### 1. A/B Testing as a Bandit Problem

Cast the A/B test in the bandit vocabulary from [Day 42](./day-42-exploration-exploitation.md). You have two arms: arm A (control) and arm B (treatment). Each time you assign a user to a variant, you are pulling one of the arms. The reward is the user's behaviour — a conversion, a click, a purchase.

Now consider what the standard A/B test actually does in bandit terms. For the first T time steps, it pulls each arm exactly 50% of the time, regardless of what the rewards look like. Then, after T steps, it pulls the better arm 100% of the time forever.

This is the *explore-then-exploit* strategy, and on [Day 42](./day-42-exploration-exploitation.md) you saw why it is suboptimal. The problem is that 50/50 exploration continues even when one arm is obviously better. If arm B is clearly superior after 500 observations, you are still routing half of your traffic to the inferior arm for the rest of the experiment period. Every unnecessary assignment to the inferior arm is regret — real users getting a worse experience.

The bandit solution is adaptive allocation. Under **Thompson Sampling** ([Day 42](./day-42-exploration-exploitation.md)), you maintain a probability distribution over each arm's true conversion rate, sample from those distributions, and route the next user to whichever arm's sample is higher. As evidence accumulates that arm B is better, more and more traffic goes to B. Exploration is not wasted on arms that are clearly inferior.

**Multi-armed bandit experiments** of exactly this type are used by Google Ads, Bing, and Netflix to reduce regret during the experiment period itself. The experiment serves a dual purpose: it gathers information *and* it optimises outcomes in real time.

The key insight is this: the standard A/B test treats the experiment period as purely the cost you pay for information. The bandit perspective treats the experiment as a decision-making problem in its own right — one where good decisions can be made even before you have conclusive evidence.

### 2. The Peeking Problem — Sequential Hypothesis Testing

Why is peeking so damaging? Let's build the intuition carefully.

In standard frequentist hypothesis testing, the p-value is defined as: the probability of observing data at least as extreme as what you saw, *assuming the null hypothesis is true and the sample size was fixed in advance*. The phrase "fixed in advance" is not a footnote. It is load-bearing.

Under the null hypothesis (A and B have identical conversion rates), the p-value is uniformly distributed on [0, 1]. This means that if you run an experiment and look at the p-value at exactly one predetermined point, there is a 5% chance it will be below 0.05 — and this is exactly your Type I error rate.

Now suppose instead you check the p-value every day for 30 days, stopping as soon as p < 0.05. What happens? At each check, there is some probability that you'll observe a spuriously low p-value by chance. Across 30 independent checks, the probability of *at least one* false positive accumulates. For 20 peeks, the Type I error rate reaches approximately 64%. For continuous checking, it approaches 100% as the number of observations grows.

This is not a subtle statistical point. If your team checks an experiment dashboard daily, your actual false positive rate is far higher than you think — regardless of what the dashboard's p-value readout says.

The underlying issue is that **the p-value is not a valid measure of evidence at arbitrary stopping times**. It is only valid at a single pre-committed stopping point.

The correct remedy is not to forbid peeking. It is to use a testing procedure that remains valid at any stopping time.

### 3. Always-Valid Inference — The mSPRT

Johari et al. (2017) developed the **mixture Sequential Probability Ratio Test (mSPRT)** precisely to address this problem.

The key idea is to design a test statistic that satisfies a stronger guarantee than the standard p-value: it controls Type I error regardless of when you stop, not just at a fixed sample size. These are called *anytime-valid p-values* or *always-valid* statistics.

The mSPRT works by computing a **likelihood ratio** that compares how likely the data is under the null hypothesis versus a mixture of alternative hypotheses. By mixing over alternatives, the statistic maintains calibration across the full path of the sequential experiment — at every moment, not just the endpoint.

Practically: the experiment can be stopped as soon as the mSPRT statistic crosses a pre-set threshold, and the Type I error is controlled at α across all possible stopping times. This is not a Bayesian result — it is a frequentist guarantee with a sequential stopping rule. The experimenter can peek freely.

This is the right framework for continuous experimentation, which is the reality of how all production A/B tests run. Platforms like Airbnb, Netflix, and LinkedIn have moved to variants of always-valid inference precisely for this reason.

### 4. Bayesian A/B Testing

The Bayesian approach to A/B testing reframes the question entirely. Instead of asking "is the effect statistically significant?", it asks "what is the *posterior distribution* of the effect size given the data?"

The mechanics connect directly to Thompson Sampling from [Day 42](./day-42-exploration-exploitation.md).

Start with a prior belief about each arm's conversion rate: Beta(α₀, β₀). The Beta distribution is the natural choice because it is defined on [0, 1] and is conjugate to the Bernoulli likelihood — meaning the posterior is also a Beta distribution, computable in closed form.

After observing n_A conversions in N_A trials for arm A, the posterior is:

```
p_A | data  ~  Beta(α₀ + n_A,  β₀ + N_A − n_A)
```

The decision rule: stop when P(p_B > p_A | data) exceeds some threshold, say 0.95. You can compute this probability by simulating from the two posteriors (or analytically approximating it).

This is Thompson Sampling applied to hypothesis testing. The moment the posterior probability of B being better crosses 0.95, you ship. You don't need to pre-specify a sample size, and you can interpret the result at any point.

**Advantages over frequentist testing:**
- Naturally incorporates prior knowledge (if you have historical conversion rates, use them as the prior)
- Doesn't require sample size specification in advance
- Produces a rich output: the full posterior distribution, not just a binary reject/fail-to-reject
- Penalises small samples naturally — if you have 10 observations, the posterior is wide and uncertain

**Disadvantages:**
- Sensitive to prior choice, especially with small samples. A Beta(1, 1) flat prior is reasonable; a misspecified prior can dominate the posterior in underpowered experiments
- Does not control frequentist error rates directly. In regulated settings (clinical trials, financial decisions with legal obligations), frequentist Type I error control may be required regardless of philosophical preference
- "Probability of being best" is not the same as effect size — a treatment can have P(p_B > p_A) = 0.95 with an expected lift of 0.1%, which may not be worth shipping

### 5. Multiple Testing and the Exploration-Exploitation Connection

The peeking problem gets worse when you run many experiments simultaneously. If you run 100 A/B tests at α = 0.05 each, you expect approximately 5 false positives even if all null hypotheses are true. This is the **multiple comparisons problem**.

The naive correction is **Bonferroni**: divide α by the number of tests. Test each experiment at α/100 = 0.0005. This is extremely conservative — it reduces power dramatically and misses real effects.

A better approach is the **Benjamini-Hochberg procedure**, which controls the **False Discovery Rate (FDR)**: the expected fraction of significant results that are false positives. Rather than trying to prevent any false positive (FWER control, Bonferroni's goal), FDR control accepts that some discoveries will be false and targets the *rate* at which this happens.

Concretely: if you run 100 tests and BH tells you 10 are significant with FDR ≤ 0.05, you accept that roughly 0.5 of those 10 "significant" results are false positives. For exploratory product decisions, this is usually the right trade-off.

Now for the bandit connection. Running many simultaneous A/B tests is, structurally, a multi-armed bandit problem — one arm per variant, across all experiments. Adaptive allocation using UCB or Thompson Sampling is statistically superior: more traffic to currently-promising variants, less waste on confirmed losers. Platforms running at scale (Spotify, Netflix, Google) run hundreds of experiments simultaneously and have built bandit-based systems to manage the allocation across this full portfolio.

### 6. Long-Run vs. Short-Run Effects — The Novelty Effect

There is a deeper problem with A/B tests that purely statistical fixes cannot address.

Users are not stationary. When you introduce a new feature, users may click it because it is novel — it catches their attention, they're curious, they explore it because it's different. This **novelty effect** inflates the measured lift during the experiment period. After the feature is fully deployed and novelty fades, the long-run effect may be much smaller, zero, or even negative.

The standard A/B test measures the **short-run treatment effect during the experiment period**. But the decision to ship is a commitment to the long-run effect. These are not the same.

In the reinforcement learning framing you built over Days 41–46, this is a **non-stationarity problem**. The reward function during the experiment period is not the same as the reward function in steady state. The agent (the product) is being evaluated in a non-stationary environment during a period when users' behaviour is shaped by novelty, not by genuine preference.

Practical mitigations:
- **Run experiments longer.** Weeks, not days. Let novelty decay before making the ship decision.
- **Holdout groups.** Keep a small fraction of users permanently on the old experience. Compare holdout users (who have never seen the new feature) to long-term treatment users after six months. This estimates the long-run steady-state effect.
- **Cohort analysis.** Segment users by how long they've been exposed to the treatment. Look for a convergence in treatment effect as exposure time grows.

The deeper framing: the experiment period is exploration; the deployment decision is exploitation. Stopping the experiment too early means acting on a biased estimate of the long-run value of your chosen arm. The bandit framework is explicit about this cost — it shows up as regret.

---

## The Formal Picture

**Standard t-test — what it assumes.** The t-test computes how many standard errors apart the two conversion rates are. If that gap is large enough (roughly two standard errors for α = 0.05), you declare significance. The catch is that "standard error" is calculated using the sample sizes — and the test is only valid if those sample sizes were locked in before you saw any data. The moment you let results influence when you stop, the standard error calculation is wrong and the p-value is unreliable.

**Peeking error inflation — the numbers.** If you run one peek at α = 0.05, your false positive risk is exactly 5%. If you peek every day for a month and stop at the first crossing, the false positive risk compounds:

- 20 independent peeks: roughly 64% chance of at least one false positive under the null
- 30 independent peeks: roughly 79%

Sequential p-values are correlated (not truly independent), so the real inflation is lower than this simple calculation suggests. Johari et al. measured it empirically under realistic conditions and found approximately 26% — still more than five times the nominal 5%.

**mSPRT always-valid guarantee (Johari et al. 2017).** The mixture Sequential Probability Ratio Test computes a running likelihood ratio that tracks how much more consistent the accumulating data is with the alternative hypothesis than with the null. The key guarantee: no matter when you stop — whether after 10 observations or 10 million — the probability of a false positive stays at or below your chosen level α. You can peek freely. This is fundamentally different from the standard t-test, where validity requires deciding the stopping time in advance.

**Bayesian update — the Beta rule.** Start with a prior belief about arm A's conversion rate, expressed as Beta(α₀, β₀). After observing s conversions in n trials, the posterior is:

> **p_A | data  ~  Beta(α₀ + s,  β₀ + n − s)**

This is the formula for Exercise 2. With a uniform starting belief Beta(1, 1) and 50 conversions in 1,000 trials, the posterior becomes Beta(51, 951), giving a mean of 51/1,002 ≈ 5.1% and a 95% credible interval computed from the Beta distribution's quantiles.

**Thompson Sampling — how allocation shifts.** At each step, draw one sample from each arm's current posterior. Route the next user to whichever arm drew the higher value. Early in the experiment, posteriors are wide and uncertain — both arms get roughly equal traffic. As evidence accumulates that one arm is better, its posterior shifts higher and draws higher values more reliably, so it receives more traffic. Regret grows at most logarithmically with total users — compare that to fixed 50/50 exploration, where regret grows linearly with the true gap between arms multiplied by how long exploration continues.

**Benjamini-Hochberg FDR control.** Sort all m test p-values from smallest to largest. Working from the smallest upward, find the last test where the p-value is no larger than (its rank / m) times your target false discovery rate. Declare every test up to that point significant. This controls the expected fraction of your significant results that are actually false positives — a much weaker and more practical guarantee than trying to prevent any false positive at all.

---

## Where It Breaks

**Network effects and SUTVA violation.** The entire statistical framework for A/B tests rests on SUTVA — the Stable Unit Treatment Value Assumption — which requires that one user's treatment assignment does not affect another user's outcome. On social networks, two-sided markets ([Day 51](./day-51-platform-mechanism-design.md)), and recommendation systems ([Day 49](./day-49-recommendation-systems.md)), this is systematically violated. If user X is assigned to treatment B (a new news feed algorithm), and user Y is still on control A, user Y's experience is still affected because X's behaviour (what X posts, likes, and shares) has changed. The SUTVA violation means your measured effect is the difference between "treatment for some users, control for others" and "control for all users" — not the quantity you actually want, which is "treatment for all users" vs. "control for all users".

The partial solution is **cluster randomisation**: assign entire social clusters, households, or geographic regions to the same variant, not individual users. This reduces but does not eliminate the interference. For two-sided markets, even cluster randomisation may be insufficient — supply-side effects propagate globally.

**Metric selection as mechanism design.** Which metric is the primary outcome? Click-through rate? Time on site? 30-day retention? Revenue per user? The choice of metric is a mechanism design decision in the sense of [Day 30](../../03-mechanism-design/days/day-30-mechanism-design.md): whichever metric you optimise, you will get more of — but you will tend to get it in ways you didn't intend. Optimise for CTR and you get sensational headlines. Optimise for time on site and you get outrage and autoplay. This is Goodhart's Law ([Day 46](./day-46-reward-design.md)): the metric ceases to be a good measure of what you want once it becomes the target of optimisation. A/B testing amplifies this problem because it provides a precise, rapid feedback loop for Goodhart dynamics.

**Users as learning agents.** The novelty effect is a surface manifestation of a deeper problem: users are themselves learning agents ([Day 48](./day-48-multi-agent-rl.md)). They update their behaviour based on experience with the new feature. The equilibrium user behaviour during the experiment period — when users are still adapting — is not the same as the long-run equilibrium user behaviour after full adaptation. Standard A/B tests measure the former and implicitly assume it predicts the latter.

---

## Try It Yourself

**Exercise 1 — Peeking calculation.**
You run an A/B test and check results once per day for 30 days, using α = 0.05 at each check. Using the Bonferroni approximation, calculate the overall Type I error rate. Now: what per-day α would you need to use in order to achieve an overall Type I error rate of exactly 5% across 30 checks? If you wanted to use this level, what does that imply about the statistical power of your daily check? Is this a practical solution?

**Exercise 2 — Bayesian update.**
Control arm A had 50 conversions out of 1,000 visitors. Treatment arm B had 65 conversions out of 1,000 visitors. Use a Beta(1, 1) uniform prior for both arms.

- Write down the posterior distributions for p_A and p_B.
- Compute the posterior mean and approximate 95% credible interval for each.
- Estimate P(p_B > p_A | data) by simulation (draw 100,000 samples from each posterior and compute the fraction where B's sample exceeds A's).
- At P(p_B > p_A) = 0.95, should you declare B the winner? What additional information would you want before shipping?

**Exercise 3 — Design a network-aware experiment.**
You are running an A/B test for a new recommendation algorithm on a social platform with 10 million users. Identify two specific SUTVA violations that would arise from individual-level randomisation. Propose a cluster randomisation scheme that mitigates them. What metric would you use as the primary outcome, and how would you detect and measure the novelty effect? Estimate how long you'd need to run the experiment before novelty decay is sufficiently complete.

---

## Connect It Back

A/B testing is the empirical feedback loop that connects everything we've built in Module 4 to the real world. Without valid experiments, mechanism design is theory without evidence, recommendation systems are unverifiable, and reward functions are untested hypotheses.

The sequential testing framework unifies the two main threads of this module: **statistical causal inference** (we want to know if B actually caused the improvement) and **online learning** (we want to make good decisions in real time, not just at the end of an experiment). The bandit framing from [Day 42](./day-42-exploration-exploitation.md) is not merely an analogy — it is the correct generative model for what production A/B testing actually is.

Tomorrow's [Day 54](./day-54-ai-safety-coordination.md) scales this up to the largest possible stakes: AI safety as a coordination problem, where the experiments being run involve the development of transformative technology and the stopping rule is a civilisational decision.

---

## Suggested Readings

- Johari, Ramesh, Pete Koomen, Leonid Pekelis, and David Walsh. "Peeking at A/B Tests: Why It Matters, and What to Do About It." *KDD 2017*. — The primary reference for always-valid inference; readable and directly practical.
- Kohavi, Ron, Diane Tang, and Ya Xu. *Trustworthy Online Controlled Experiments: A Practical Guide to A/B Testing*. Cambridge University Press, 2020. Chapters 1–3. — The canonical industry reference; covers peeking, metric selection, network effects, and more.
- Russo, Daniel J., Benjamin Van Roy, Abbas Kazerouni, Ian Osband, and Zheng Wen. "A Tutorial on Thompson Sampling." *Foundations and Trends in Machine Learning* 11(1), 2018. — Connects the bandit framework to Bayesian updating; chapter 4 is directly relevant.
- Benjamini, Yoav, and Yosef Hochberg. "Controlling the False Discovery Rate: A Practical and Powerful Approach to Multiple Testing." *Journal of the Royal Statistical Society, Series B* 57(1), 1995. — The original FDR paper; surprisingly accessible.

---

*Next: [Day 54 — AI Safety as a Coordination Problem](./day-54-ai-safety-coordination.md)*
*Previous: [Day 52 — Nudge Design & Choice Architecture](./day-52-nudge-design.md)*


---

← [Day 52 — Nudge Design & Behavioural Mechanism Design](day-52-nudge-design) &nbsp;|&nbsp; [Day 54 — AI Safety as a Coordination Problem →](day-54-ai-safety-coordination)
