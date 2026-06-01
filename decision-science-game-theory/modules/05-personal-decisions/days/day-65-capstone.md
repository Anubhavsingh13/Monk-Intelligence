*[Decision Science & Game Theory](../../../../README.md) · [Module 5 — Strategic Life & Career](../5-overview.md) · Day 65 of 65*

# Day 65 — Capstone: The Full Game

> **Today's challenge:** Apply the full arc of the course — probabilistic reasoning, game theory, mechanism design, reinforcement learning, and personal decision-making — to a single rich strategic scenario that is also personally yours.
> **Time:** ~3 hours (or spread across a week) · **Prereqs:** All 64 prior days.
> **This is the course's only open-ended deliverable.** There is no single correct answer — only more and less rigorous, honest, and insightful ones.

---

## The Premise

You are a senior product manager at **Meridian**, a mid-sized AI company that has spent three years building a foundation model. The model is good — not GPT-4-level good, but competitive on a meaningful subset of tasks, particularly structured reasoning, code generation, and document summarisation. Meridian has raised $120M in Series B funding. You have 18 months of runway. The board has given you and the leadership team 60 days to commit to a commercialisation strategy.

Three options are on the table.

**Option A — API Business.** License the model via an API to enterprise customers. Pricing is metered: pay per token, tiered by volume. Enterprise sales cycle, long contracts, integration support required. Meridian becomes a B2B infrastructure company — invisible to end users, indispensable to developers. Revenue is predictable but growth depends on developer adoption. The product is the model; the moat is the model.

**Option B — Platform.** Build an ecosystem where third-party developers build applications on top of Meridian's model. Meridian provides the model, infrastructure, and developer tooling; it takes a 25% revenue share from all applications built on the platform. Think: Stripe's approach to payments, Twilio's approach to communications, App Store's approach to software. This requires significant upfront investment in SDKs, documentation, sandbox environments, and a developer relations function. The moat shifts from model quality to ecosystem lock-in. Network effects kick in — if developers build on Meridian, users follow; if users are on Meridian, developers stay.

**Option C — Consumer Product.** Build a direct-to-consumer AI assistant: a productivity or tutoring product that charges users directly via a freemium or subscription model. High customer acquisition cost ($40–80 per acquired user, based on current comparable CAC data), but potentially strong lifetime value if retention is good. The moat is the user relationship, the data flywheel, and brand recognition. The risk is that you are competing with OpenAI's ChatGPT, Google's Gemini, and Anthropic's Claude directly in the consumer's mental model — a category where network effects and brand power are already entrenched.

The competitive landscape is not friendly. OpenAI, Google, Anthropic, and Mistral are all present and investing aggressively. The market is growing fast — global AI software revenues are projected to exceed $300B by 2027 — but it is also consolidating. Well-capitalised incumbents tend to win commodity API markets over time; platforms require a critical mass of developers that Meridian does not yet have; and consumer AI is one of the most capital-intensive distribution games in recent memory.

You have a further complication. Yesterday, you received a competing offer. A well-funded AI safety startup — 30 employees, $40M raised, working on interpretability and alignment tooling for frontier models — wants you as their Head of Product. The offer includes a meaningful equity stake and work that aligns more directly with your long-term interests in ensuring AI goes well for humanity.

Your decision is therefore double-layered. You must advise Meridian on what to do. And you must decide, for yourself, what you will do — which may or may not be the same as what you recommend.

These two decisions interact. The option Meridian chooses will shape what the PM role looks like. The appeal of the AI safety startup depends partly on how you evaluate the counterfactual of staying. Your recommendation to Meridian's board will be coloured, whether you acknowledge it or not, by your own preferences about what you would enjoy working on. Good analysis requires you to hold both decisions simultaneously, acknowledge their interaction, and still arrive at conclusions that are honest and actionable.

The rest of this document gives you the analytical lenses. Use all of them.

---

## The Five-Module Lens

### Module 1 — Decision Under Uncertainty

The fundamental structure of this problem is a decision under uncertainty: you must choose among three strategic options before you know which competitive equilibrium the AI market will settle into, how fast regulation will arrive, whether Meridian's model quality will keep pace with frontier improvements, or what the actual demand elasticity for each product type turns out to be.

**Identify the key uncertainties.** Not all uncertainties are equal. Rank them by: (a) how much they affect the payoff differences between options, and (b) how resolvable they are within Meridian's 18-month runway. Candidates include: the rate of consolidation in the API market, competitor pricing moves, whether foundation model quality will become a commodity within 12–24 months, the regulatory trajectory in the EU and US, and whether developer ecosystems have winner-take-all dynamics or can support multiple platforms simultaneously. For each uncertainty, estimate a probability distribution — even a rough one. Saying "I don't know" is not a probability distribution; it is a refusal to reason.

**Build a decision tree.** Construct one formally. The root node is the choice among A, B, and C. Each branch should include at least two chance nodes representing the key uncertainties that most differentiate the payoffs of that option. For Option A, the critical branch is whether the API market consolidates to a small number of providers before Meridian reaches profitability — if yes, what is the payoff? If no? For Option B, the critical branch is whether Meridian can achieve the developer adoption threshold required for network effects to kick in — typically estimated at 500–1,000 active developers building production applications. For Option C, what is the realistic distribution of CAC-to-LTV ratios given the competitive intensity? Assign numbers. If your numbers feel uncertain, represent that uncertainty as a range, not as a refusal to estimate.

**Apply EVPI.** Expected Value of Perfect Information tells you the maximum you should pay — in time, money, or delayed decision — to resolve a specific uncertainty before committing. Compute it for the single most consequential uncertainty in your decision tree. What is the difference between the best expected payoff under that uncertainty versus the best expected payoff if you knew the outcome for certain? That difference is the EVPI. If the EVPI is high, it may be worth running a 30-day experiment, buying market research, or hiring an advisor with specific knowledge before committing. If it is low, the information is not worth delaying for. Show the calculation.

**Apply reference-class forecasting.** What is the base rate for each type of company? B2B infrastructure businesses at the $10–100M ARR scale — what fraction reach profitability vs. get acquired vs. shut down within five years? Developer platform businesses that launch with a 25% revenue share — how many successfully achieve network effects, and what is the typical time to critical mass? Consumer AI products launched against entrenched incumbents — what is the historical distribution of outcomes? The inside view (Meridian's specific features and team) will feel compelling. Weight the outside view more than your intuition says you should. Reference classes are your prior; Meridian's specific story is your likelihood update. Use Bayes' rule, not narrative confidence.

---

### Module 2 — Game Theory

**Type of game.** The competitive landscape is a multi-player game, but it is not a single type. Start by characterising it correctly. Is it a race game — where being first to a certain capability or market share confers a permanent advantage, making speed dominant? A coordination game — where the platform that most developers adopt becomes the standard, regardless of technical quality? Or a prisoner's dilemma — where each firm would prefer a world with fewer competitors but individually has incentive to expand aggressively regardless of what others do? The answer differs by option. Option A is closest to a Bertrand competition in a commodity market. Option B has coordination-game characteristics — focal point dynamics determine which platform ecosystem attracts developers. Option C is a race game with winner-take-all tendencies in consumer mindshare.

**Nash equilibrium analysis.** In the simplified version of the market entry game, ask: is there a Nash equilibrium in which each of the four major competitors (OpenAI, Google, Anthropic, Mistral) chooses a segment to specialise in — or does the equilibrium involve all players competing in all segments simultaneously? If every firm simultaneously enters the consumer segment (Option C), what happens? Run the logic: if all four entrants plus Meridian compete for consumer AI users, the aggregate marketing spend required to acquire users increases, CAC rises across the board, lifetime values compress as users become fragmented, and the survivor is the firm with the deepest pockets — which is not Meridian. What does this imply about the Nash equilibrium of the segment-selection game? Is there an equilibrium where smaller players specialise in B2B (Option A or B) and large incumbents dominate consumer, or does the equilibrium involve everyone choosing consumer?

**Repeated game: Option B's developer relationships.** If Meridian chooses Option B, the relationship between Meridian and platform developers is a repeated game. Developers invest in building on Meridian's infrastructure — they write SDKs, build user bases, accumulate switching costs. Meridian, at any future point, could raise the revenue share from 25% to 35% or 40%, or shut down APIs that are inconvenient. This is the platform squeeze problem. The question is: what discount factor δ is required for Meridian to sustain a credible commitment not to extract? What observable commitments — contractual, reputational, technical — can make that commitment credible today? Without a credible commitment, rational developers will not build on Meridian's platform. The folk theorem gives you the formal structure; apply it to this specific context.

**Simplified payoff matrix.** Construct a two-player, two-strategy payoff matrix for Meridian vs. a representative competitor (say, a well-funded European AI startup of similar scale). Let the strategies be {A: enter enterprise API market, C: enter consumer market}. Assign rough payoffs in terms of three-year expected revenue and probability of survival. What is the Nash equilibrium of this matrix? Does it have a dominant strategy for either player? Is there a coordination problem — do both players prefer the same outcome but need to coordinate to reach it? Be explicit about your payoff assumptions and defend them.

---

### Module 3 — Mechanism Design

**Option B revenue-sharing mechanism.** If Meridian chooses Option B, the 25% revenue share is a mechanism. Evaluate it formally. Does it satisfy Individual Rationality — is it individually rational for a developer to build on Meridian's platform given the 25% take rate, or would they prefer to host the model themselves and capture 100% of revenue? The answer depends on what Meridian provides in exchange: infrastructure cost savings, distribution, trust. Does it satisfy Incentive Compatibility — given the 25% take rate, do developers have an incentive to honestly report their revenue to Meridian, or do they have incentive to misreport or route revenue off-platform? What enforcement mechanism prevents gaming? Would a VCG-style mechanism apply to the developer allocation problem — for instance, to allocate compute subsidies among developers building on the platform? VCG has the advantage of incentive compatibility but is vulnerable to collusion among a small number of agents; is collusion a realistic concern among Meridian's developer base?

**Option A optimal metered pricing.** Metered pricing is a two-sided market problem when the API is used by developers who are themselves building products for end users. Apply the Rochet-Tirole framework (Day 33): who are the two sides? What are the cross-side network externalities? If Meridian prices the API too high, developers build fewer applications; fewer applications means fewer end users; fewer end users reduces the value of the ecosystem to developers. The optimal price is not simply cost-plus-margin — it accounts for these externalities. What subsidies or volume discounts would the Rochet-Tirole analysis recommend? Who should Meridian subsidise — the developer side or the enterprise buyer side — and why?

**Option C free/premium screening mechanism.** Option C requires a freemium tier to acquire users and a premium tier to monetise them. This is a screening problem (Day 29): Meridian cannot observe a user's willingness to pay before they reveal it through their behaviour. Design the screening mechanism. What features should the free tier include? What features should be paywalled? The classic result is that the free tier should be made just good enough to attract high-type users (who will eventually convert) and just bad enough that low-type users (who will never pay) do not cost too much to serve. The hard design question is: what is the right degradation? Degrading quality (lower model, slower response) retains both types; degrading quantity (limited queries per day) screens by intensity of use. Which screen is better given Meridian's cost structure?

**Regulatory mechanism design.** Step outside the firm and ask: if you were the regulator governing AI foundation model providers in 2026, what mechanism would you design to govern Meridian across all three options? Option A raises data-portability and vendor lock-in concerns. Option B raises platform power and app-store-style exclusivity concerns. Option C raises consumer manipulation and addictive design concerns. The regulator's mechanism must satisfy IR (firms must find it individually rational to operate in the regulated jurisdiction) and IC (firms must not be able to game the reporting requirements). What information would the regulator elicit, and how? Apply the revelation principle: what mechanism makes honest disclosure dominant?

---

### Module 4 — AI Products

**Reward functions, one per option.** Whichever product Meridian builds, it will use RLHF or a similar human-feedback loop to improve the model's behaviour over time. The reward function is the specification of what "better" means. For Option A (enterprise API), the reward function is probably some combination of task completion rate, factual accuracy, and latency — measurable by enterprise customers through SLA-style feedback. For Option B (platform), the reward function is more diffuse: it must aggregate signals from thousands of different developer-built applications with different objectives. For Option C (consumer product), the reward function is most dangerous: engagement is easy to measure, actual user value is hard. Apply Goodhart's Law (Day 46) specifically. For each option, identify the point at which optimising the reward metric would degrade the true objective. In Option C, the consumer assistant could score highly on engagement by being slightly addictive, by flattering users rather than correcting them, or by lengthening interactions beyond what's useful. What hard constraints — what off-switch conditions — would prevent the policy gradient from finding these degenerate solutions?

**Option B: preventing a race to the bottom.** If hundreds of developers are independently building applications on Meridian's platform, each running their own RL fine-tuning loops, what prevents the multi-agent system from converging to a race-to-the-bottom equilibrium (Days 48 and 54)? Each developer has an individual incentive to optimise their application's engagement metrics, even at the cost of ecosystem health. If all developers do this simultaneously, user trust in the Meridian platform degrades, which is bad for every developer collectively but individually rational for each one. This is a multi-agent prisoner's dilemma with RL agents. What platform governance mechanisms — content policies, API usage constraints, automated monitoring — would Meridian need to impose to prevent this outcome? What are the limits of those mechanisms when developers are themselves sophisticated ML practitioners?

**Option C: sequential decision-making and off-policy challenges.** A consumer AI assistant is a sequential decision-making problem: the model observes the user's query, generates a response, the user responds or disengages, and the loop repeats. The natural framework is an MDP (Day 43) where the state is the user's context and history, the action is the response, and the reward is some function of user satisfaction. The off-policy challenge (Day 44): you collect data from users who are interacting with version N of the model, but you want to train version N+1. The policy that generated the data is different from the policy you are training. What bias does this introduce? How would you correct for it? Would you use importance sampling, doubly robust estimation, or something else? Be specific about what breaks if you ignore the off-policy problem and train naively on logged data.

**A/B testing plan for the first 90 days.** For whichever option you recommend, design a concrete A/B testing programme for the first 90 days post-launch (Day 53). Identify three specific experiments. For each: what is the null hypothesis? What is the treatment? What is the primary metric, and what is the guardrail metric (the metric you will not let degrade, even if the primary metric improves)? What is the required sample size to detect a meaningful effect at 80% power? The guardrail metric is the more important design choice — it encodes your values about what trade-offs are and are not acceptable. State it explicitly.

---

### Module 5 — Personal Decision

**The 5-step framework, applied to you.** You have a competing offer. Apply the Day 64 framework to your own decision — not to Meridian's decision, but to yours. Step through all five steps. Step 1: is this a one-shot or repeated game? (Your career is a repeated game; this decision will be known to people who remember where you went and why.) Step 2: who are the agents, and what are their objectives? (Your future colleagues at either firm, the founders of the AI safety startup, Meridian's board, your professional network who will observe the choice.) Step 3: what mechanism governs the decision? (The offer process, the negotiation structure, the equity terms.) Step 4: what are the dynamics? (The AI safety field is growing; interpretability research is attracting talent; Meridian's competitive position may improve or deteriorate; your own skills will compound differently in each role.) Step 5: what is your specific position? Work through this honestly. Not as a textbook exercise. As an actual decision you are making.

**BATNA analysis.** Your BATNA in negotiating the Meridian PM role is the AI safety offer — and vice versa. Compute both. What is Meridian's BATNA in recruiting you? (There are other candidates, but how replaceable are you, and at what cost?) What is the AI safety startup's BATNA in recruiting you? (They have a specific role in mind; how many other qualified candidates exist?) Apply Day 59's Nash bargaining logic: your share of the surplus depends on the ratio of your BATNA to the total value of the match. Does this analysis suggest you are currently over- or under-negotiating on compensation or role scope? What could you do in the next two weeks to improve your BATNA before committing?

**Reputation signal.** The choice between Meridian and the AI safety startup sends a signal to the market (Day 56). Signals are informative when they are costly — you are giving up one path to take the other, so the choice reveals something about your priorities and risk tolerance. What does joining Meridian signal? (Commercial focus, product ambition, willingness to work in competitive markets.) What does joining the AI safety startup signal? (Long-term thinking, mission alignment, lower immediate compensation tolerance.) To whom are these signals sent? (Your professional network, future employers, the AI research community, your own future self.) Are either of these signals the one you actually want to send, or are you optimising for a perception that does not match your underlying preferences?

**Portfolio thinking (Day 62).** Treat the two options as assets in your human capital portfolio. Meridian is higher expected value in a short-horizon scenario (faster path to senior leadership, stronger salary trajectory if the company succeeds) but correlated with the broader commercial AI market — if that market corrects, Meridian's trajectory corrects with it. The AI safety startup is lower expected salary, but the skills developed (interpretability, alignment, AI governance) are likely to appreciate dramatically if AI safety becomes a dominant industry concern — and they are negatively correlated with most of the downside scenarios for commercial AI. Which choice has higher variance? Which has higher Kelly fraction, given your specific prior on the probability that AI safety becomes a defining field of the next decade? What does the covariance structure of your existing human capital tell you about which option provides better diversification?

---

## The Deliverable

You are asked to produce three things. There is no length limit, but the word targets are calibrated to what is actually necessary.

---

### 1. Strategic Recommendation (500–1,000 words)

**Which option should Meridian choose, and why?**

Your recommendation must:

- State the option clearly in the first paragraph. Do not bury the lede with qualifications.
- Use at least one formal tool from each of Modules 1 through 4 in your argument. Not name-dropped — actually applied. A decision tree result, a Nash equilibrium analysis, a mechanism design property, a reward function specification.
- Acknowledge the strongest objection to your recommendation and respond to it. If you recommend Option A, acknowledge the commodity risk and explain why you think Meridian's position is defensible despite it. If you recommend Option B, acknowledge the cold-start problem and explain how you propose to solve it. If you recommend Option C, acknowledge the CAC-to-LTV risk and show the numbers under which it works.
- State, explicitly, what would change your recommendation. If the API market consolidates faster than your base case, does Option A still dominate? If developer adoption in Month 6 is below a threshold you specify, does Option B become untenable? A recommendation without stated update conditions is not a recommendation — it is a position.

Show your work. Paste in the relevant section of your decision tree. State your probability estimates. Name the reference class you used.

---

### 2. Personal Decision Analysis (300–500 words)

**What should you — as the PM — do?**

Use the Day 64 framework explicitly. Work through all five steps. This section should be honest and specific to your actual situation — your actual risk tolerance, your actual financial position, your actual beliefs about AI's trajectory, your actual feelings about mission-driven vs. commercial work.

Do not write a generic analysis of "a hypothetical PM." Write about yourself. If you are genuinely uncertain, say so and explain what is driving the uncertainty. If the analysis points in one direction but your gut points in another, name that tension and explain it. The gap between the formal analysis and your actual inclination is often the most informative data point.

This section is not graded on reaching the "right" conclusion. It is graded on honesty, specificity, and the quality of the reasoning that got you there.

---

### 3. Goodhart Audit (200–300 words)

**For the option you recommend, identify the three most likely reward specification failures.**

For each failure:
- Name the metric that will be used and why it seems reasonable.
- Describe the specific degenerate behaviour an RL agent (or a team of humans) would exhibit if they optimised that metric hard.
- Propose what you would measure instead — or what hard constraint you would impose to prevent the degenerate solution.

The Goodhart audit is not a list of risks. It is a precise analysis of what breaks at the level of the objective function. The test of a good audit is: if you gave it to an ML engineer, they could implement the constraints you specify. If your constraints are too vague to implement, revise them.

---

## Evaluation Criteria

### What "done well" looks like

**Rigorous.** Formal tools are applied correctly, not merely named. A decision tree is actually drawn with nodes, probabilities, and expected values. A Nash equilibrium claim is supported by a payoff matrix with actual numbers. A mechanism design argument identifies which property (IR, IC, or efficiency) is satisfied or violated and explains why. Rigour is not complexity — a simple model applied correctly is better than a complex model applied gesturally.

**Honest.** Uncertainty is stated as probability distributions, not concealed behind qualitative language. "I estimate a 60% probability that the API market consolidates significantly within 18 months, with 80% confidence intervals from 40% to 80%" is honest. "The market is uncertain" is not. When you don't know, say so and estimate the uncertainty's magnitude. When your estimate could easily be wrong, state the conditions under which it would be wrong.

**Integrated.** The strongest analyses will produce conclusions that no single module could have generated. The game-theoretic analysis of the competitive landscape should constrain the mechanism design choices. The mechanism design analysis of the platform should inform the RL reward function specification. The personal decision analysis should be informed by the strategic recommendation, not siloed from it. If your three deliverables could have been written independently by three different people, they are not integrated enough.

**Actionable.** The recommendation should be specific enough to act on next week. Not "Meridian should consider a platform strategy with appropriate developer incentives." Rather: "Meridian should commit to Option B, beginning with a three-month developer tooling sprint targeting the top 50 developers from the existing API waitlist, with a go/no-go decision at Month 4 contingent on reaching 200 active developers in sandbox." If a senior engineer read your recommendation, they should know what to build first.

**Self-aware.** The personal analysis section should be genuine, not a textbook exercise in which a generic agent applies a generic framework to a generic decision. The tell of a low-quality personal analysis is that it could have been written by anyone in any situation. The tell of a high-quality personal analysis is that it could only have been written by you, about this specific decision, at this specific moment.

---

### What "done poorly" looks like

- Modules are invoked as vocabulary without analysis. ("This is a Prisoner's Dilemma because there are multiple players with incentives." No — prove it. Show the payoff matrix. Demonstrate that mutual defection is the Nash equilibrium.)
- No quantitative estimates. Any analysis that contains only qualitative language — "significant," "likely," "substantial risk" — without a single number has not done the work. Make estimates. State your confidence. Be wrong in ways that are at least clearly stated.
- The personal dimension is ignored or treated as an afterthought. Allocating 400 words to the strategic recommendation and 50 words to the personal analysis is not integration — it is avoidance. The personal decision is structurally as important as the strategic one in this capstone.
- The recommendation is so heavily hedged as to be useless. "Meridian could pursue Option A or Option B, depending on market conditions and team preferences, while also considering Option C in certain scenarios" is not a recommendation. It is a list. Make a call.
- The Goodhart audit names risks rather than specifying constraints. "The reward function might be gamed" is not a Goodhart audit. "If we optimise session length, the policy will learn to generate slightly incomplete responses that prompt follow-up questions — cap session length improvement as a metric and measure task completion rate with a one-question follow-up survey instead" is a Goodhart audit.

---

## A Note on What You've Built

Sixty-four days ago, you started with a question most people never bother to formalise: how should a rational agent make decisions under uncertainty? You built the probabilistic foundation — expected utility, Bayesian updating, calibration, the value of information — not as abstract mathematics but as practical tools for thinking more clearly about situations where the future is not known and cannot be.

You then entered the strategic layer: the world where other agents exist, have their own objectives and information, and respond to what you do. Game theory gave you the vocabulary for this — Nash equilibrium, the Folk Theorem, signalling, screening, mechanism design. You learned that the most powerful position in many games is not to play optimally given the rules, but to design the rules. The revelation principle is not a theorem about auctions — it is a theorem about power: whoever sets the mechanism has a structural advantage that skill cannot easily overcome.

Module 4 added a layer that did not exist in the formal game theory literature fifty years ago: adaptive agents operating at scale. Reinforcement learning gave you a rigorous way to think about learning from interaction. Multi-agent RL gave you the sobering insight that when many learning agents interact simultaneously, the system's behaviour may not converge to anything sensible — that races to the bottom and specification failures are not edge cases but structural tendencies of systems optimised at scale. Goodhart's Law, applied to AI, is not a warning about measurement laziness. It is a warning about the difficulty of encoding what we actually value into objective functions that survive contact with optimisation pressure.

Module 5 turned all of this back on you. The tools work on your own decisions too — and the honest application of them is both more uncomfortable and more useful than any textbook exercise. You have a BATNA. You send signals whether you intend to or not. Your career is a repeated game with a discount factor. Your skill portfolio has a covariance structure. These are not metaphors. They are descriptions of how your professional life actually works, rendered in a vocabulary precise enough to reason about clearly.

Here is the honest limitation of everything you have learned: these tools are lenses, not algorithms. The world is messier, luckier, and more human than any model. Meridian's outcome will depend partly on a competitor's unexpected pivot, a regulatory shock no one anticipated, a key hire who left for personal reasons, a product demo that went viral for the wrong reason. The decision tree you build will not capture these events — not because the framework is wrong, but because the future is genuinely underdetermined in ways that no probability distribution fully represents. The frameworks help you think more clearly about what you can reason about. They do not eliminate the territory beyond the edge of the map.

The frameworks don't tell you what to want. That's still on you.

What you want — really, not just instrumentally — is the thing that no decision tree can specify and no mechanism can elicit. It is prior to the analysis, not downstream from it. The most important work this course has left undone is the work of deciding what you are actually trying to optimise for. Not what you should want according to some normative theory, and not what the people around you expect you to want. What you actually want, stated clearly enough to check whether your decisions are tracking it. That question was implicit in every exercise, every framework, every case study across 65 days. It is the question that outlasts the course.

Now go answer it.

---

*This is the final day of the course. There is no "Next" link. The work continues elsewhere.*


---

← [Day 64 — Integration: One Framework](day-64-integration) &nbsp;|&nbsp; [↑ Back to Course Home](../../../../README.md)
