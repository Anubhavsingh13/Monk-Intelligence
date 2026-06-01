*[Decision Science & Game Theory](../../../../README.md) · [Module 5 — Strategic Life & Career](../5-overview.md) · Day 64 of 65*

# Day 64 — Integration: One Framework for Strategic Decisions

> **Today's one idea:** Every significant decision in business and life shares the same underlying structure — an agent with preferences acting under uncertainty in a strategic environment with other agents, constrained by information asymmetry and mechanisms — and the course has given you the vocabulary to decompose any decision into these components and reason about each one.
> **Reading time:** ~35 min · **Prereqs:** All prior days. This is the penultimate day.
> **Primary source:** Dixit, Avinash K. & Nalebuff, Barry J. *Thinking Strategically: The Competitive Edge in Business, Politics, and Everyday Life.* W.W. Norton, 1991. Chapters 1–2.

---

## The Hook

A product manager at a platform company faces one decision: should we introduce a subscription pricing tier?

This decision looks simple. It is not.

It is simultaneously a **mechanism design problem** — what pricing rules will produce the consumer behaviour and developer ecosystem we want? A **game theory problem** — how will competitors respond when we announce the tier? A **signalling problem** — what does a premium tier communicate about the product's quality and trajectory to users who don't subscribe? A **two-sided market problem** — how does the new tier change the balance between consumers and developers on the platform? A **reinforcement learning problem** — how do we learn the optimal price point over time without destroying consumer trust through public experimentation? And a **decision under uncertainty** — demand elasticity is unknown, and the distribution of consumer willingness-to-pay is estimated, not observed.

The frameworks don't contradict each other. They illuminate different facets of the same decision. The product manager who knows only pricing theory will miss the competitive response. The game theorist who ignores mechanism design will set the wrong rules. The economist who ignores the learning problem will set a price and never update it.

Today's framework is the procedural answer to this complexity: how do you decompose any significant decision so that the right tools illuminate the right facets, in the right order?

---

## Building the Intuition

### 1. The 5-Step Decision Decomposition Framework

This is the heart of Day 64. It is a practical checklist — not a theory to be proven, but a procedure to be applied. Its purpose is to make sure you do not skip a facet of a decision simply because it is less visible than the facets you are already comfortable analysing.

Work through the steps in order. Steps 1 through 4 are about the decision's structure. Step 5 is about you.

---

**Step 1 — Structure: What type of decision is this?**

- Is this **one-shot or repeated**? If the interaction will recur — with the same counterpart, in the same market, among the same peers — then reputation, Folk Theorem dynamics, and the discount factor δ are active. Cooperation can be sustained; defection will be remembered. If it is genuinely one-shot, Nash equilibrium logic dominates and reputation is irrelevant.

- Is this **strategic or parametric**? A parametric decision is made against a fixed environment: how much to bid in a sealed auction with unknown but fixed demand. A strategic decision involves other agents who respond to what you do: pricing against a competitor who monitors and matches. The distinction matters because strategic decisions require modelling other agents' beliefs and best responses; parametric decisions require only probability distributions over outcomes.

- Is this **risk or uncertainty**? Risk means you can assign probabilities (known distribution). Uncertainty means you cannot (Knightian uncertainty — unknown distribution). Expected utility theory applies to risk. Robustness reasoning — what decision is best across the widest range of scenarios? — applies to uncertainty.

- What is the **time horizon and reversibility**? Short horizon, reversible: optimise and update. Long horizon, irreversible: option value matters enormously. Under irreversibility, the value of waiting for information — the real options premium — can dominate the immediate expected payoff calculus.

---

**Step 2 — Agents: Who are the players?**

- **Who are the agents?** List them explicitly. In a job negotiation: you, the hiring manager, HR (distinct principal-agent from the firm), other candidates. In a startup: founders, early employees, investors, customers, regulators, competitors. Missed agents are the most common source of strategic blindspots.

- **What are each agent's objectives, information, and constraints?** Objectives are often not what agents say they are. The HR manager's objective is not "hire the best candidate" — it is "fill the role within budget, minimise hiring manager complaints, and satisfy internal equity constraints." Map revealed preferences, not stated preferences.

- **Who has private information, and what is the asymmetry structure?** (Days 27–29.) Does the seller know the product quality better than the buyer? Does the employee know their outside offer better than the employer? Does the co-founder know their true risk tolerance better than their partner? Private information changes optimal strategies on both sides. The informed party wants to credibly reveal when it helps them and conceal when it does not. The uninformed party designs screening mechanisms to elicit revelation.

- **Are there principal-agent relationships?** (Day 27.) When one party acts on behalf of another — employee for employer, fund manager for investor, AI system for human overseer — the agent's incentives may diverge from the principal's. Identify every principal-agent relationship in the decision, then ask: is the agent's incentive structure aligned? What would misalignment look like?

- **Are there coalitions or cooperative dynamics?** (Day 36 — Shapley value.) If multiple agents could form a coalition to capture more value jointly, Shapley logic applies: each agent's bargaining power is proportional to their marginal contribution to every possible coalition. Identify whether coalitions are possible and who would benefit from them forming.

---

**Step 3 — Mechanism: What rules govern the interaction?**

- **Who set the rules? Can you change them?** (Day 30.) A mechanism is the rules of the game — the order of moves, what information is revealed when, what commitments are binding. Often the most powerful strategic move is not to play the game as given, but to propose a different game. If you can set the auction format, the contract terms, the platform governance rules — you have mechanism design power. Most people don't ask this question. Ask it.

- **Revelation principle: what would agents honestly reveal if it served their interest?** Any Bayesian implementable outcome can be achieved by a direct mechanism where agents truthfully reveal their types. Practically: if you want to know what a customer truly values, design an incentive structure where honest revelation is dominant. If you want to know an employee's true outside option, create a process where revealing it helps them. Mechanisms that require agents to lie in equilibrium are fragile.

- **What equilibrium do current rules produce? Is it efficient?** (Days 30, 38.) An efficient equilibrium maximises total surplus. Many real mechanisms — first-price auctions, linear contracts, quota-based promotion systems — are systematically inefficient. Identify the inefficiency, then ask: who captures the deadweight loss? Who would need to agree to change the mechanism? Is there an implementation problem (the equilibrium exists in theory but is not reached because coordination fails)?

- **Is there a Goodhart failure?** (Day 46.) Is the measured reward tracking the true objective? Whenever a metric is used as a target, agents will optimise for the metric at the expense of the underlying goal. In product decisions: engagement metrics optimised against genuine user value. In hiring: structured interviews optimised against cultural fit. In AI systems: reward functions optimised against human preferences. The Goodhart check is: if agents optimise hard against this metric, does the true objective improve or degrade?

---

**Step 4 — Dynamics: How does it evolve?**

- **Is there learning? What is the exploration-exploitation trade-off?** (Day 42.) If the environment is stationary and you will make this type of decision many times, a learning strategy is optimal. The bandit framework applies: balance extracting value from your best current option against exploring alternatives that might be better. UCB and Thompson Sampling provide principled policies. The question to ask: are we learning from this decision, and are we structured to update correctly?

- **What is the reward function, and is it well-specified?** (Day 46.) In repeated decisions — product iterations, pricing updates, hiring processes — the reward function is what you optimise over time. Misspecified reward functions compound. A company that optimises for quarterly revenue year after year will make systematically different long-run choices than one that optimises for net present value of customer lifetime value. State the reward function explicitly. Check it against the true objective. Update it when the environment changes.

- **Multi-agent dynamics: convergence or race?** (Day 48.) When multiple agents are learning simultaneously in the same environment, individual learning algorithms may not converge to equilibrium — they may cycle, spiral, or enter an arms race. The question is: what is the qualitative dynamics of the multi-agent system, and is there a stable attractor? If competitors are running gradient descent on their pricing algorithms simultaneously, no individual firm controls the market outcome. The system's dynamics dominate individual strategy.

- **Is there a coordination problem? What is the focal point?** (Day 24.) Many decisions require coordinated action across agents with no communication — which side of the road to drive on, which platform to build for, which technology standard to adopt. Schelling focal points — salient solutions that agents can coordinate on without negotiation — determine outcomes. If you are trying to coordinate, make your preferred equilibrium the focal one: be visible, be early, be the reference point that others coordinate around.

---

**Step 5 — Personal position: What is your specific situation?**

The first four steps are about the structure of the decision. Step 5 is about you as a specific agent occupying a specific position in that structure.

- **What is your BATNA? What are your outside options?** (Days 25, 59.) Your bargaining power is determined by your disagreement point. Before any negotiation, investment, or commitment — state your BATNA explicitly. Could you improve it before the interaction begins? The return on BATNA improvement almost always exceeds the return on tactical preparation.

- **What signals are you sending? Are they credible?** (Day 28.) Signals are informative only when they are costly enough that low-quality types would not mimic them. A claim without a credible signal is cheap talk. Before any important communication — a pitch, a negotiation, a public commitment — ask: is this message credible to a sceptical receiver? What would make it more credible?

- **What is your network position? Who can you leverage?** (Day 57.) Your position in the network determines your information advantage, your access to resources, and your brokerage opportunities. In any significant decision, ask: who else in my network has navigated a similar decision? Am I currently a structural hole broker in the relevant domain, or am I embedded in a single cluster?

- **What is the risk profile and your portfolio context?** (Day 62.) Every significant decision changes your portfolio — your exposure to different sources of risk. Before committing, ask: how correlated is this decision's risk with my existing exposures? Does it hedge or double down? What is the Kelly-optimal fraction of resources to commit, given my estimate of edge and the variance of the outcome?

---

### The Framework as a Reference Card

| Step | Core Question | Key Tools from This Course |
|------|--------------|---------------------------|
| **1 — Structure** | What type of decision is this? | MDPs (D10), Repeated games (D21), Risk vs. uncertainty (D9), Option value |
| **2 — Agents** | Who plays, and what do they want? | Principal-agent (D27), Adverse selection (D29), Bayesian Nash equilibrium (D20), Shapley value (D36) |
| **3 — Mechanism** | What rules govern the interaction? | Mechanism design (D30), Revelation principle (D30), VCG (D38), Goodhart's Law (D46) |
| **4 — Dynamics** | How does it evolve? | Multi-armed bandits (D42), Policy gradient (D45), MARL (D48), Focal points (D24) |
| **5 — Personal** | What is my specific position? | BATNA / Nash bargaining (D25, D59), Signalling (D28), Network brokerage (D57), Kelly Criterion (D62) |

---

### 2. Three Extended Case Studies

#### Case A: "Should I take this job offer?"

**Step 1 — Structure.** This is a repeated game: you will work with these people for years, and how you perform in the first weeks sets the reputation that persists. The interaction is strategic — the firm is also choosing among candidates. It involves risk (you have some information about the role) and uncertainty (culture and management quality are hard to observe). The decision is semi-reversible: you can leave, but with costs.

**Step 2 — Agents.** You, the hiring manager (wants role filled, cares about team fit), HR (separate agent: wants process compliance and equity), other candidates (your competitive environment), your future colleagues (who will shape your daily experience but have no role in the hiring decision). Note the principal-agent gap: HR's incentives may not align with the hiring manager's true preferences.

**Step 3 — Mechanism.** The offer mechanism is set by the firm: they post a number, you counter or accept. Can you change the mechanism? Sometimes — asking to discuss total compensation, equity, flexibility, or scope expands the negotiation surface and often reveals integrative gains. Revelation principle: what structure would make the firm honestly reveal its true flexibility? (Signalling your enthusiasm creates an incentive to reveal constraints.) Goodhart check: are the role's success metrics tracking actual value creation, or proxy metrics that will distort your work?

**Step 4 — Dynamics.** There is a learning dynamic: the first six months is an exploration phase, and you will update your estimate of role quality rapidly. Is the firm structured to give you feedback? Is there a policy gradient — does good performance compound into better opportunities, or is the reward function linear? Multi-agent: what are your future colleagues optimising for, and does it conflict with your goals?

**Step 5 — Personal.** What is your BATNA? If you have a competing offer, Rubinstein's model predicts you should receive a better split. If you don't, your negotiating leverage is weak — which means the highest-return move is not to negotiate harder, but to generate a competing offer before accepting. What signals did your interview performance send? Were they credible quality signals? What is your network position at this firm — do you know people there, or are you entering cold? Is the role's risk profile correlated or uncorrelated with your existing human capital?

---

#### Case B: "Should our team build this AI feature?"

**Step 1 — Structure.** One-shot or repeated? Building the feature is one-shot, but competing in the AI feature landscape is repeated — competitors will respond and your team will iterate. Strategic: competitors monitor your product releases. Uncertainty dominates: demand for the feature is largely unknown. Semi-reversible: engineering investment has opportunity costs; user expectations, once set, are hard to walk back.

**Step 2 — Agents.** Your product team, the users who will use the feature, developers on your platform who may build on top of it, competitors who may copy or counter, and your own AI system (an agent with its own objective function and potential misalignment — Day 46). The users' objectives are heterogeneous: some want efficiency, some want control, some don't know what they want until they see it.

**Step 3 — Mechanism.** Platform mechanism design: how does the feature change the rules of engagement between your platform and its users? (Day 51.) Does it shift power toward or away from developers? Goodhart failure is the central risk for AI features: the feature's success metric (engagement, clicks, time-on-task) may diverge sharply from the user's actual objective (accomplishing their goal efficiently, without distraction). Design the reward function before building. Race dynamics check (Day 54): is this a situation where launching before you are ready is competitively necessary, or is coordination with competitors on safety and quality possible and preferable?

**Step 4 — Dynamics.** A/B testing is the feedback loop (Day 53): treat the launch as a learning experiment, not a one-shot deployment. Are you structured to update the feature based on outcome data, or will you ship and move on? Multi-agent dynamics: if every competitor ships an equivalent AI feature simultaneously, no firm captures lasting advantage — the feature becomes table stakes and competition moves elsewhere. What is the stable equilibrium of the competitive dynamics?

**Step 5 — Personal.** What is your team's BATNA if this feature fails to launch? (Competitive disadvantage? Or simply a preserved engineering budget?) What signal does this feature send about your product's quality and direction — to users, to developers, to potential hires? What is the network effect structure: does the feature become more valuable as more users adopt it, creating a compounding dynamic (Day 33)?

---

#### Case C: "Should I start this company?"

**Step 1 — Structure.** Long horizon, largely irreversible (reputational capital is consumed; opportunity costs are real). Strategic: investors, competitors, potential employees, and customers all respond to your actions. Uncertainty dominates: the market does not yet exist in the form you imagine. The time horizon is 5–10 years, meaning patience and repeated-game logic govern founder behaviour throughout.

**Step 2 — Agents.** Co-founders (the cooperative game: Shapley value determines fair equity splits based on marginal contribution to every possible subset — Day 36), early employees (principal-agent: equity incentives should align them with founders, but vesting structures matter), investors (screening problem: early investors have private information about their own value-add — Day 29), customers (whose preferences you are trying to discover, not serve as known), and competitors (who will respond if you succeed). The co-founder relationship is the highest-leverage agent decision: misalignment among founders destroys more companies than market failure.

**Step 3 — Mechanism.** You are designing the mechanism from scratch — the company's governance, its hiring process, its pricing structure. The revelation principle applies in all of them: what structures make customers honestly reveal their preferences? What equity structures make employees honestly reveal their effort? Goodhart failure check: what metrics will you use to measure progress, and how will optimising them distort your team's behaviour? (Revenue is proxied by ARR; ARR is proxied by signed contracts; signed contracts are gamed by discounting and free pilots.) Design reward functions at the level of true objectives, not convenient proxies.

**Step 4 — Dynamics.** The company's early history is pure exploration (Day 42): you are sampling from a distribution of possible product-market fits. The exploration-exploitation trade-off is concrete: when have you learned enough to commit to one direction? Thompson Sampling intuition applies — stay in high-uncertainty, high-upside search longer than intuition suggests, but not indefinitely. Multi-agent: if you are building in a domain where competitors are also searching, the coordination or race dynamics (Day 54) determine whether first-mover advantage is real or whether the market can support multiple entrants.

**Step 5 — Personal.** Kelly Criterion for bet sizing: what fraction of your total human and financial capital should you commit? Kelly says bet proportional to edge over odds. If your edge is uncertain (early stage), bet small in lifestyle terms — preserve optionality. What signal does founding this company send to the market? Is it a credible signal of commitment and quality, or does it consume signalling capital that would otherwise compound? What is your personal BATNA if the company fails in 18 months — is it good enough to make the bet worth taking?

---

### 3. The Meta-Principles

Five things that cut across every domain of the course. These are not conclusions — they are lenses that remain useful long after the specific models are forgotten.

**All strategic environments have equilibria. The question is: which one, and is it the one you want?**
Equilibrium is not a guarantee of efficiency or fairness — it is simply a state where no agent has a unilateral incentive to deviate. Markets equilibrate at prices that may be far from socially optimal. Repeated games can equilibrate at both cooperation and mutual defection. The mechanism designer's job, and the strategist's job, is to move the system toward the equilibrium that serves the relevant objective.

**Information asymmetry is everywhere. The side that reveals less extracts more — but revealing can unlock integrative gains.**
The informed party's instinct is to conceal. This instinct is often correct in zero-sum settings and often wrong in positive-sum settings. When there are integrative gains — when honest information exchange allows both parties to do better — strategic concealment destroys value. The skill is distinguishing zero-sum from positive-sum information contexts and calibrating disclosure accordingly.

**Goodhart's Law is universal. Whatever you measure will be gamed. Design reward functions deliberately.**
This is not cynicism — it is a structural property of any adaptive system. The moment a metric becomes a target, agents optimise against it, and the correlation between the metric and the underlying objective degrades. The implication is not to stop measuring. It is to measure at the level of true objectives, rotate metrics to prevent gaming, and maintain scepticism about any single KPI that drives significant incentives.

**Patience is systematically underrated. High-δ strategies dominate low-δ strategies in most repeated games.**
The Folk Theorem result is not just a theoretical curiosity. It is an empirical regularity: patient players, who genuinely discount the future less steeply, outperform impatient players in almost every domain involving ongoing relationships. Career building is a repeated game. Company building is a repeated game. Scientific reputation is a repeated game. The discount factor is a choice as much as it is a preference — you can cultivate patience, and doing so is one of the highest-return investments available.

**The outside view is almost always more accurate than the inside view. Base rates before narratives.**
This principle survives contact with every domain the course has covered. Project timelines, investment returns, startup success rates, negotiation outcomes — in every case, the reference class predicts better than the inside narrative. Your project is not special enough to deviate dramatically from the base rate. Neither is your deal, your candidate, or your plan. Anchor on the base rate, adjust modestly for specific features, and distrust adjustments that are large and convenient.

---

### 4. Where Theory Ends and Judgment Begins

The frameworks are not algorithms. Here is precisely where each one runs out.

**Game theory gives equilibria, not which equilibrium to select.** In many games — coordination games, repeated games, games with multiple Nash equilibria — the theory identifies all stable states but cannot tell you which one will be reached. Equilibrium selection requires judgment about which focal point is most salient, which history of play is most relevant, and which equilibrium your specific counterpart is likely to coordinate on. This is not a failure of game theory; it is a feature that reflects genuine indeterminacy in strategic settings.

**Mechanism design gives the revelation principle, not the right mechanism for every context.** The revelation principle tells you that truthful mechanisms can implement any achievable outcome — but it does not tell you what outcome to target. The right mechanism depends on your objective, your constraints, and your beliefs about the distribution of agent types. Designing a mechanism requires judgment about what you actually want and what is implementable given political, legal, and informational constraints.

**RL gives the policy gradient, not the right reward function.** Reinforcement learning algorithms are extraordinarily good at optimising a given reward function. They are entirely silent on whether that reward function is the right one. The reward specification problem — what do we actually want the system to optimise? — is not a technical problem. It is a values problem, and it requires judgment about objectives, trade-offs, and what counts as success.

**The frameworks are lenses, not algorithms. Judgment is applying the right lens to the right problem at the right granularity.** A decision that looks like a mechanism design problem from one angle looks like a signalling problem from another. Both descriptions can be correct. The skill is not in knowing all the lenses — it is in knowing which lens reveals the most leverage for the specific decision at hand. That skill is cultivated by practice, by making many decisions, by reviewing outcomes, and by being honest about which frameworks helped and which were post-hoc rationalisation.

**The skill is knowing when to invoke formal machinery vs. when "good enough" intuition suffices.** Most decisions do not require formal models. The 5-step framework is most valuable for high-stakes, non-reversible, multi-agent decisions with significant information asymmetry. For low-stakes, reversible, single-agent decisions, applying the full framework is framework paralysis. Part of mastery is knowing when to use the tools and when to trust calibrated intuition.

---

### 5. The Course in One Paragraph

Module 1 built the probabilistic foundation: decisions under uncertainty, expected utility, Bayesian updating, and the value of information. Before you can reason strategically, you must reason probabilistically — and most people, most of the time, do not. Module 2 added strategic interaction: game theory, equilibria, repeated games, and the formal structure of conflict and cooperation between rational agents. Module 3 added mechanism design — the question of who designs the rules and how, the revelation principle, VCG mechanisms, and the insight that the most powerful strategic position is often the one that sets the game, not just the one that plays it well. Module 4 applied everything to AI systems learning from interaction: reinforcement learning, multi-armed bandits, multi-agent learning, and the alignment problem — what happens when the agent's objective diverges from the principal's. Module 5 turned the lens inward: you are the agent, your career is the game, your decisions are the mechanism, your network is the platform, and your cognitive biases are the misspecified reward function you are trying to correct. The whole arc is: from individual decisions, to strategic decisions, to designing the games others play, to building systems that play games, to playing the game of your own life well. Tomorrow, you apply the full arc.

---

## Formal Picture

The 5-step framework is the formal structure of Day 64. No new equations. The reference card below is designed to survive outside this course — print it, save it, or write it on the first page of your decision journal.

| Step | Core Question | Key Tools |
|------|--------------|-----------|
| **Structure** | What type of decision is this? | MDPs, repeated games, risk vs. uncertainty, option value, reversibility |
| **Agents** | Who plays? What do they want? | Principal-agent, information asymmetry, adverse selection, Shapley value |
| **Mechanism** | What rules govern it? | Mechanism design, revelation principle, VCG, Goodhart's Law check |
| **Dynamics** | How does it evolve? | RL, bandits, MARL convergence, Schelling focal points |
| **Personal** | What is my specific position? | BATNA, credible signalling, network position, portfolio context, Kelly sizing |

---

## Where It Breaks

**Framework paralysis.** The 5-step checklist can lead to over-analysis. Not every step is relevant to every decision. If Step 3 (mechanism) is obviously irrelevant — you are making a purely personal career choice with no mechanism design component — skip it without guilt. The framework is a guard against blind spots, not a mandate to invoke all tools on all decisions.

**False precision.** Formal models require assumptions. Those assumptions are often wrong in ways that matter. A Nash bargaining solution gives you a precise number; that number is precise only given your estimates of disagreement points, which may be quite uncertain. Models sharpen thinking by forcing you to state assumptions explicitly — they do not manufacture precision that the underlying problem does not possess. State your assumptions alongside your conclusions.

**The world is messier.** Real negotiations have emotions, history, and relationship dynamics that Rubinstein's model does not capture. Real platforms have politics and regulatory constraints that mechanism design theory ignores. Real careers have luck — being in the right room at the right time — that no framework predicts. The models tell you what to look for and what levers to pull. They do not tell you what will happen. Treat outputs as structured inputs to judgment, not as predictions.

**The framework doesn't tell you what you want.** Clarifying your own objectives is prior to applying the framework. Before you decompose a decision, you must answer: what am I actually trying to optimise for? What trade-offs am I willing to make? What constraints are non-negotiable? This is "Step 0" — and it is the step that formal frameworks cannot do for you. A precise analysis of the wrong objective is worse than a rough analysis of the right one.

---

## Exercises

These exercises build directly toward tomorrow's capstone. Do not skip them.

**Exercise 1.** Select one significant past decision — a career move, an investment, a major commitment. Apply the 5-step framework retrospectively. What did you analyse well at the time? What did the framework reveal you missed? Where was the gap between your actual decision process and what the framework would have produced?

**Exercise 2.** Apply the framework to one current decision you are facing. Work through all five steps. At which step do you have the least information or the most uncertainty? What would it cost to resolve that uncertainty — in time, money, or opportunity cost? Is it worth resolving before deciding, or should you proceed under uncertainty and update?

**Exercise 3.** Identify one **recurring** decision in your professional work — a decision you make repeatedly, perhaps monthly or quarterly. Examples: prioritising work items, evaluating candidates, responding to competitive moves, setting prices, deciding which experiments to run. Apply the framework to this class of decision and write a one-paragraph **decision policy**: a standing rule that specifies how you will make this decision going forward, what information you require, and what the default action is when information is incomplete. A good decision policy reduces the cognitive load of the recurring case while preserving judgment for genuinely novel instances.

---

## Connect It Back

The integration is complete. You have the vocabulary, the frameworks, the case studies, and the meta-principles. You know where the tools are powerful and where they break. You know what judgment is required that no framework can replace.

Tomorrow is Day 65 — the capstone. A single integrative problem that requires bringing together all five modules: probabilistic reasoning, strategic interaction, mechanism design, reinforcement learning and alignment, and personal decision-making. The problem is deliberately complex and multi-layered. There are no partial-credit shortcuts.

You have built the toolkit over 64 days. Tomorrow you use it.

---

## Suggested Readings

- Dixit, Avinash K. & Nalebuff, Barry J. *Thinking Strategically: The Competitive Edge in Business, Politics, and Everyday Life.* W.W. Norton, 1991. Chapters 1–2. — The accessible integration of game theory into everyday strategic reasoning. The intellectual ancestor of this framework.

- Raiffa, Howard. *The Art and Science of Negotiation.* Harvard University Press, 1982. — Raiffa is one of the founders of decision analysis and negotiation theory. This book integrates individual decision-making and strategic interaction more thoroughly than almost any other single source.

---

*Next: [Day 65 — Capstone: The Full Arc](day-65-capstone.md)*


---

← [Day 63 — Rest & Synthesise](day-63-rest-synthesise) &nbsp;|&nbsp; [Day 65 — Capstone: The Full Game →](day-65-capstone)
