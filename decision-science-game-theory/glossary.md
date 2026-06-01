# Glossary

> Alphabetical. Each entry: plain-English definition · formal definition · first introduced on Day N.
> Updated as pages are written — if a term appears before its day, the page will define it in context.

---

### Anchoring
**Plain:** Your first number poisons all subsequent estimates, even when you know the anchor is arbitrary.
**Formal:** A cognitive heuristic in which an initial piece of information (the anchor) disproportionately influences subsequent judgements under adjustment.
**Introduced:** Day 6

### Arrow's Impossibility Theorem
**Plain:** No ranked-choice voting system can simultaneously satisfy fairness, unanimity, independence of irrelevant alternatives, and non-dictatorship.
**Formal:** For three or more alternatives, no social welfare function satisfying unanimity and independence of irrelevant alternatives is non-dictatorial (Arrow, 1951).
**Introduced:** Day 32

### Availability Heuristic
**Plain:** You judge probability by how easily an example comes to mind — vivid, recent events feel more likely than they are.
**Formal:** A cognitive shortcut in which the judged frequency or probability of an event is proportional to the ease with which instances are retrieved from memory.
**Introduced:** Day 6

### Backward Induction
**Plain:** Solve a sequential game by starting at the last possible move and working backward — each player anticipates the rational response at every node.
**Formal:** A solution procedure for extensive-form games: assign payoffs at terminal nodes, then at each predecessor node assign the payoff of the action that maximises the current player's payoff, iterating backward to the root.
**Introduced:** Day 19

### Bandit Problem (Multi-Armed Bandit)
**Plain:** You have K slot machines with unknown payoff distributions — the problem is which to pull, and how often, to maximise total reward.
**Formal:** A sequential decision problem with K actions (arms), each yielding stochastic rewards drawn from an unknown distribution; the goal is a policy π mapping histories to actions that maximises expected cumulative reward.
**Introduced:** Day 43

### Bayesian Updating
**Plain:** When you see new evidence, multiply your prior belief by how much more likely that evidence is given your hypothesis vs. not — then normalise.
**Formal:** P(H|E) = P(E|H) · P(H) / P(E). The posterior probability of hypothesis H given evidence E equals the likelihood times the prior divided by the marginal probability of the evidence.
**Introduced:** Day 5

### Calibration
**Plain:** A forecaster is well-calibrated if, among all the times they say "70% likely," roughly 70% of those events actually occur.
**Formal:** A probability assessor is calibrated if for all stated probabilities p, the empirical frequency of events in the class {events assigned probability p} converges to p.
**Introduced:** Day 4

### Coalition
**Plain:** A subset of players who agree to cooperate and pool their payoffs.
**Formal:** In a cooperative game (N, v), a coalition is any subset S ⊆ N. The characteristic function v(S) specifies the maximum value coalition S can guarantee itself.
**Introduced:** Day 36

### Commitment Device
**Plain:** A mechanism you voluntarily install today to constrain your future choices — removing the option to defect when temptation strikes.
**Formal:** A pre-commitment is a binding contract or constraint that modifies the game tree faced by a future self, rendering certain actions unavailable or costly.
**Introduced:** Day 58

### Correlated Equilibrium
**Plain:** A mediator sends each player a private recommended action; each player's best response is to follow the recommendation, given that others do too.
**Formal:** A probability distribution σ over action profiles is a correlated equilibrium if, for every player i and every pair of actions (aᵢ, aᵢ'), E[uᵢ(aᵢ, a₋ᵢ) | rec = aᵢ] ≥ E[uᵢ(aᵢ', a₋ᵢ) | rec = aᵢ].
**Introduced:** Day 38

### Decision Tree
**Plain:** A branching diagram that maps all choices and chance events in sequence — solved by folding back from the end.
**Formal:** A rooted directed tree where decision nodes (squares) represent agent choices, chance nodes (circles) represent stochastic outcomes with associated probabilities, and terminal nodes carry payoffs. Solved by backward induction.
**Introduced:** Day 10

### Diversification
**Plain:** Holding many assets reduces risk without reducing expected return — because the random wobbles of individual stocks partially cancel each other out.
**Formal:** For a portfolio of n assets with equal weights, variance σ²_p = σ²/n + [(n−1)/n]·ρ·σ², where ρ is the average pairwise correlation. As n → ∞: σ²_p → ρ·σ² (systematic risk floor). The unsystematic component σ²/n → 0 "for free" — the market does not compensate you for bearing it.
**Introduced:** Addendum A (see also Day 62 — Portfolio Thinking)

### Dominant Strategy
**Plain:** A strategy is dominant if it gives you the best outcome no matter what anyone else does — you play it without needing to predict others.
**Formal:** Strategy sᵢ strictly dominates sᵢ' if uᵢ(sᵢ, s₋ᵢ) > uᵢ(sᵢ', s₋ᵢ) for all s₋ᵢ ∈ S₋ᵢ. A weakly dominant strategy replaces strict inequality with ≥.
**Introduced:** Day 15

### Evolutionary Stable Strategy (ESS)
**Plain:** A strategy is evolutionarily stable if, when adopted by a population, no small group of mutants playing a different strategy can invade and spread.
**Formal:** Strategy s* is an ESS if for all s ≠ s*: u(s*, s*) > u(s, s*), or u(s*, s*) = u(s, s*) and u(s*, s) > u(s, s).
**Introduced:** Day 23

### Expected Utility
**Plain:** The weighted average of the utilities of outcomes, where weights are their probabilities. What rational agents maximise.
**Formal:** EU(L) = Σᵢ pᵢ · u(xᵢ) for a lottery L = {(x₁,p₁), ..., (xₙ,pₙ)}, where u is a von Neumann-Morgenstern utility function.
**Introduced:** Day 3

### Expected Value
**Plain:** The probability-weighted average of all possible monetary outcomes of a gamble.
**Formal:** EV(L) = Σᵢ pᵢ · xᵢ for a lottery L over outcomes {xᵢ} with probabilities {pᵢ}.
**Introduced:** Day 2

### Exploration-Exploitation Tradeoff
**Plain:** At every step, should you use your current best option (exploit) or try something new to gather information (explore)? You cannot fully do both.
**Formal:** In a bandit or MDP, the exploration-exploitation tradeoff is the tension between maximising immediate expected reward (exploiting the current greedy action) and reducing uncertainty (exploring sub-optimal actions to improve future policy performance).
**Introduced:** Day 42

### Extensive Form Game
**Plain:** A game represented as a tree — who moves when, what they know, and what happens at every branch.
**Formal:** An extensive form game specifies: a game tree, a player function assigning each non-terminal node to a player, information sets, a probability distribution over chance moves, and a payoff function over terminal nodes.
**Introduced:** Day 19

### Focal Point (Schelling Point)
**Plain:** When coordination is needed but communication is impossible, players gravitate toward options that are salient, prominent, or "obvious" by shared cultural knowledge.
**Formal:** A focal point is a Nash equilibrium selected in a coordination game by precedent, salience, or cultural convention in the absence of explicit communication.
**Introduced:** Day 24

### Framing Effect
**Plain:** Logically identical choices presented differently produce different decisions — "90% survival rate" and "10% mortality rate" feel different despite being the same.
**Formal:** A violation of description invariance in which preferences over options depend on the way they are described, not only on their objective properties.
**Introduced:** Day 6

### Game
**Plain:** Any situation in which your best action depends on what others choose, and they know that, and you know they know that...
**Formal:** A game in strategic form is a tuple (N, {Sᵢ}, {uᵢ}) where N is a set of players, Sᵢ is player i's strategy space, and uᵢ: S → ℝ is player i's payoff function over strategy profiles.
**Introduced:** Day 14

### Information Set
**Plain:** Everything a player knows at a given point in a sequential game — the set of nodes they cannot distinguish between.
**Formal:** An information set for player i is a set of nodes in the game tree such that the player has the same available actions at every node in the set and cannot distinguish which node they are at.
**Introduced:** Day 27

### Incentive Compatibility
**Plain:** A mechanism is incentive-compatible if every participant's best strategy is to tell the truth (or follow the intended rule) — you cannot gain by gaming it.
**Formal:** A mechanism is incentive-compatible (IC) if for every type profile θ, truthful reporting is a Bayesian Nash equilibrium: uᵢ(θᵢ, θᵢ; θ₋ᵢ) ≥ uᵢ(θᵢ', θᵢ; θ₋ᵢ) for all θᵢ' ≠ θᵢ.
**Introduced:** Day 30

### Loss Aversion
**Plain:** Losing $100 feels roughly twice as bad as gaining $100 feels good — losses loom larger than equivalent gains.
**Formal:** In Prospect Theory, the value function v satisfies v(−x) > −v(x) for x > 0; Kahneman & Tversky estimate the loss aversion coefficient λ ≈ 2.25.
**Introduced:** Day 7

### Markov Decision Process (MDP)
**Plain:** A formal model of sequential decision-making: states of the world, actions available, transition probabilities, and rewards. The framework underlying all of modern RL.
**Formal:** An MDP is a tuple (S, A, P, R, γ) where S is the state space, A the action space, P: S × A × S → [0,1] the transition function, R: S × A → ℝ the reward function, and γ ∈ [0,1) the discount factor.
**Introduced:** Day 44

### Mechanism Design
**Plain:** Designing the rules of a game so that when selfish players follow their own interests, they produce an outcome you want. "Engineering incentives."
**Formal:** A mechanism M = (A, g) specifies an action space A for each agent and an outcome function g: A → X. The designer chooses M to implement a desired social choice function f(θ) as a Bayesian Nash equilibrium.
**Introduced:** Day 30

### Mixed Strategy
**Plain:** A deliberate randomisation over your available actions — rational when your opponent would exploit any predictable pure strategy.
**Formal:** A mixed strategy is a probability distribution σᵢ ∈ Δ(Sᵢ) over player i's pure strategy set Sᵢ. Expected payoff: uᵢ(σ) = Σₛ (Πⱼ σⱼ(sⱼ)) · uᵢ(s).
**Introduced:** Day 18

### Nash Equilibrium
**Plain:** A stable state where no player can improve their outcome by changing their strategy, assuming everyone else holds theirs fixed.
**Formal:** A strategy profile s* = (s₁*, ..., sₙ*) is a Nash equilibrium if for every player i: uᵢ(sᵢ*, s₋ᵢ*) ≥ uᵢ(sᵢ, s₋ᵢ*) for all sᵢ ∈ Sᵢ.
**Introduced:** Day 16

### Nudge
**Plain:** A change to the choice environment that predictably alters behaviour without forbidding options or significantly changing incentives.
**Formal:** A nudge is any aspect of choice architecture that alters people's behaviour in a predictable way without forbidding any options or significantly changing their economic incentives (Thaler & Sunstein, 2008).
**Introduced:** Day 53

### Policy (RL)
**Plain:** An agent's decision rule — a mapping from every situation to an action (or a distribution over actions).
**Formal:** A policy π: S → Δ(A) maps states to distributions over actions. A deterministic policy is a special case π: S → A.
**Introduced:** Day 41

### Price of Anarchy
**Plain:** The ratio of the worst-case social welfare under selfish equilibrium play to the optimal social welfare under a central planner — measures how much efficiency selfishness destroys.
**Formal:** PoA = min_{s ∈ NE} W(s) / max_{s} W(s), where W is the social welfare function and the minimum is over all Nash equilibria.
**Introduced:** Day 39

### Prior / Posterior
**Plain:** Prior = what you believe before seeing evidence. Posterior = what you believe after updating on evidence.
**Formal:** In Bayesian inference, the prior P(H) is the probability assigned to hypothesis H before observing data. The posterior P(H|E) = P(E|H)·P(H)/P(E) is the updated probability after observing evidence E.
**Introduced:** Day 5

### Prisoner's Dilemma
**Plain:** A game where two players each benefit from defecting — regardless of what the other does — but mutual defection leaves both worse off than mutual cooperation.
**Formal:** A symmetric 2×2 game with payoffs T > R > P > S (Temptation, Reward, Punishment, Sucker) satisfying 2R > T + S, where (D,D) is the unique Nash equilibrium despite (C,C) Pareto-dominating it.
**Introduced:** Day 17

### Prospect Theory
**Plain:** People evaluate outcomes as gains or losses relative to a reference point, weight losses more heavily than equivalent gains, and distort probabilities nonlinearly.
**Formal:** V(x, p) = Σᵢ π(pᵢ) · v(xᵢ), where v is an S-shaped value function (concave for gains, convex for losses, steeper for losses) and π is a probability weighting function satisfying π(0)=0, π(1)=1, with overweighting of small probabilities (Kahneman & Tversky, 1979).
**Introduced:** Day 8

### Q-function (Action-Value Function)
**Plain:** The expected total future reward of taking action a in state s, then following policy π forever after.
**Formal:** Qπ(s,a) = E[Σₜ γᵗ Rₜ₊₁ | S₀=s, A₀=a, π]. The optimal Q-function Q*(s,a) = maxπ Qπ(s,a) satisfies the Bellman optimality equation.
**Introduced:** Day 45

### Reward Function (RL)
**Plain:** The signal the agent receives after each action — the designer's specification of what "good" means.
**Formal:** R: S × A × S → ℝ. The agent's objective is to find a policy maximising Gₜ = Σₖ γᵏ Rₜ₊ₖ₊₁.
**Introduced:** Day 41

### Risk Aversion
**Plain:** Preferring a certain outcome to a gamble with the same expected value — the reason most people prefer insurance.
**Formal:** An agent is risk-averse if u is concave: u(E[X]) > E[u(X)] for all non-degenerate lotteries X. Degree of risk aversion is measured by the Arrow-Pratt coefficient −u''(x)/u'(x).
**Introduced:** Day 3

### Screening
**Plain:** A designer who can't observe what "type" someone is offers a menu of contracts designed so each type self-selects into the option intended for them.
**Formal:** A screening mechanism is an incentive-compatible, individually rational direct revelation mechanism (q(θ), t(θ)) where the designer induces each type θ to truthfully reveal their type and self-select the socially appropriate allocation.
**Introduced:** Day 29

### Shapley Value
**Plain:** Each player's fair share of the coalition's total value, defined as their average marginal contribution across all possible orderings of players joining the coalition.
**Formal:** φᵢ(v) = Σ_{S⊆N\{i}} [|S|!(|N|−|S|−1)!/|N|!] · [v(S∪{i}) − v(S)]. The unique value satisfying efficiency, symmetry, null-player, and additivity axioms.
**Introduced:** Day 37

### Signalling
**Plain:** A costly action taken to credibly communicate private information — costly enough that only the high-quality type would bother.
**Formal:** In a signalling game, a sender with type θ chooses signal m(θ) to maximise payoff. A separating equilibrium has m(θ) ≠ m(θ') for θ ≠ θ'; a pooling equilibrium has m(θ) = m(θ') for all θ. Signal credibility requires the single-crossing property.
**Introduced:** Day 28

### Stable Matching
**Plain:** A matching between two groups where no unmatched pair would both prefer to be matched to each other over their current partners.
**Formal:** A matching μ is stable if there is no blocking pair (i, j) such that j ≻ᵢ μ(i) and i ≻ⱼ μ(j). The Gale-Shapley deferred acceptance algorithm produces a stable matching in O(n²) time.
**Introduced:** Day 35

### Sunk Cost Fallacy
**Plain:** Continuing to invest in something because of past irrecoverable costs, rather than future expected value — throwing good money after bad.
**Formal:** A violation of consequentialist decision theory in which agents allow sunk costs (past expenditures that cannot be recovered) to influence forward-looking decisions. Rationally, only marginal future costs and benefits are decision-relevant.
**Introduced:** Day 7

### System 1 / System 2
**Plain:** System 1 is fast, automatic, intuitive. System 2 is slow, deliberate, effortful. Most decisions are made by System 1, even when System 2 should be in charge.
**Formal:** Dual-process theory of cognition (Kahneman, 2011, after Stanovich & West, 2000): Type 1 processes are associative, automatic, and parallel; Type 2 processes are rule-based, controlled, and serial.
**Introduced:** Day 1

### Value of Information (VoI)
**Plain:** The maximum amount you should pay to learn something before making a decision — the difference between your best decision with and without that information.
**Formal:** VoI = E[maxₐ u(a, θ) | learn θ] − maxₐ E[u(a, θ)]. The Expected Value of Perfect Information (EVPI) assumes you learn θ exactly; the Expected Value of Sample Information (EVSI) accounts for imperfect signals.
**Introduced:** Day 9

### Alpha (Investment)
**Plain:** The return you earn above and beyond what your level of market risk would predict — genuine skill, not just market exposure.
**Formal:** α = r_i − [r_f + β_i(r_m − r_f)], where r_i is the asset's return, r_f the risk-free rate, β_i the asset's market beta, and r_m the market return. Positive alpha implies outperformance unexplained by systematic risk. After fees and transaction costs, most actively managed funds show α ≤ 0.
**Introduced:** Addendum A

### A/B Test (Randomised Controlled Experiment)
**Plain:** Split your users into two groups, show each a different version, and measure the difference — sounds simple, but the statistical inference is subtler than most practitioners realise.
**Formal:** An A/B test is a randomised experiment where units are i.i.d. assigned to treatment T or control C. Under SUTVA, the average treatment effect τ = E[Y(1) − Y(0)] is estimated by the difference in group means. Inference requires pre-registration of sample size, metric, and stopping rule.
**Introduced:** Day 53

### Adverse Selection (Markets)
**Plain:** When the counterparty in a trade may know something you don't, the act of them being willing to trade is itself bad news — you face the worst possible pool of sellers.
**Formal:** In a double auction, the bid-ask spread partly compensates the market maker for adverse selection risk: the probability that the incoming order is from an informed trader who knows the asset is worth more (buy order) or less (sell order) than the quoted price. Spread = 2λσ_v where λ is the probability of an informed trade and σ_v is the variance of the asset's true value.
**Introduced:** Addendum A (see also Day 27 — Information Asymmetry)

### Barbell Strategy
**Plain:** Put most of your resources in safe, boring assets and a small fraction in high-risk, high-upside bets — nothing in the middle where moderate risk meets moderate return.
**Formal:** A portfolio allocation that concentrates weight on extreme ends of the risk spectrum: (1−ε) in near-zero-variance assets and ε in assets with large positive skew and fat-tailed upside, avoiding the "mediocristan" middle.
**Introduced:** Day 62

### BATNA (Best Alternative to a Negotiated Agreement)
**Plain:** The best outcome you can achieve if the negotiation fails and you walk away — your outside option, which sets your reservation price.
**Formal:** BATNA defines negotiator i's reservation value rᵢ: the minimum (for sellers) or maximum (for buyers) agreement value at which participation is individually rational. An agreement is reached only if the ZOPA {[r_buyer, r_seller]} is non-empty.
**Introduced:** Day 59

### Beta (Market Risk)
**Plain:** A stock's beta measures how much it moves relative to the overall market — beta of 1.5 means the stock rises or falls 50% more than the market on average.
**Formal:** β_i = Cov(r_i, r_m) / Var(r_m). The CAPM predicts E[r_i] = r_f + β_i·(E[r_m]−r_f). Beta captures only systematic (non-diversifiable) risk; idiosyncratic risk is priced out by diversification.
**Introduced:** Addendum A

### Bellman Equation
**Plain:** The value of being in a state equals the immediate reward plus the discounted value of the best next state — a recursive identity that lets you compute long-run value one step at a time.
**Formal:** The Bellman optimality equation for the state-value function: V*(s) = max_a [R(s,a) + γ · Σ_{s'} P(s'|s,a) · V*(s')]. The Q-function version: Q*(s,a) = R(s,a) + γ · Σ_{s'} P(s'|s,a) · max_{a'} Q*(s',a').
**Introduced:** Day 44

### Betweenness Centrality
**Plain:** A measure of how often a node sits on the shortest path between other pairs of nodes — high betweenness means you are a broker through whom information or resources must flow.
**Formal:** C_B(v) = Σ_{s≠v≠t} [σ_{st}(v) / σ_{st}], where σ_{st} is the total number of shortest paths from s to t and σ_{st}(v) is the number of those paths that pass through v.
**Introduced:** Day 57

### Brier Score
**Plain:** A measure of forecasting accuracy for probabilistic predictions — lower is better, zero is perfect.
**Formal:** BS = (1/N) · Σᵢ (fᵢ − oᵢ)², where fᵢ ∈ [0,1] is the stated probability for event i and oᵢ ∈ {0,1} is the outcome. Decomposes as BS = Reliability − Resolution + Uncertainty.
**Introduced:** Day 61

### Choice Architecture
**Plain:** The design of the environment in which people make decisions — the order of options, defaults, labels, and framing that shapes choices without changing what is available.
**Formal:** Choice architecture encompasses: default rules, the arrangement and framing of options, the salience of attributes, and the presence or absence of commitment devices. Behavioural mechanism design is the systematic application of choice architecture to steer agents toward better outcomes.
**Introduced:** Day 52

### Constitutional AI (CAI)
**Plain:** A method for aligning language models using a written set of principles (a "constitution") — the model critiques and revises its own outputs against these principles, reducing reliance on human labellers for harmful-output annotation.
**Formal:** CAI (Bai et al., 2022) involves two phases: (1) supervised learning on self-critiqued revisions guided by the constitution; (2) RLHF where the reward model is trained on AI-generated preference labels filtered by constitutional principles, rather than human labels for harmfulness.
**Introduced:** Day 50

### Dark Pattern
**Plain:** A user-interface design that uses the techniques of choice architecture against the user's interests — exploiting defaults, salience, and cognitive biases to maximise a platform metric at the user's expense.
**Formal:** A dark pattern is an adversarial nudge: a choice-architecture manipulation in which the "nudger" (designer) and the "nudgee" (user) have misaligned objectives, so that the mechanism that steers behaviour maximises the designer's utility at the cost of user welfare.
**Introduced:** Day 52

### Disposition Effect
**Plain:** Investors tend to sell winners too early (to lock in the feeling of a gain) and hold losers too long (to avoid realising a loss) — the opposite of what the tax code and expected-value reasoning recommend.
**Formal:** A behavioural pattern documented by Shefrin & Statman (1985) arising from prospect theory: the S-shaped value function v(x) makes realising a gain feel good and realising a loss feel bad, causing the reference point (purchase price) to anchor ongoing sell/hold decisions even though the purchase price is a sunk cost irrelevant to future expected returns.
**Introduced:** Addendum A

### Discount Factor (δ and γ)
**Plain:** How much you value future payoffs relative to today. In RL, γ makes future rewards worth less. In game theory, δ reflects patience and the probability the game continues.
**Formal:** In RL: Gₜ = Σ_{k=0}^∞ γᵏ R_{t+k+1}, γ ∈ [0,1). In repeated games: δ = (1−p_exit)·ρ where p_exit is the probability the relationship ends each period and ρ is the pure time discount. Higher δ supports cooperation via Folk Theorem.
**Introduced:** Day 21 (game theory), Day 41 (RL)

### Efficient Market Hypothesis (EMH)
**Plain:** In a competitive market, prices quickly incorporate all available information — so you cannot consistently beat the market using publicly known data.
**Formal:** Fama (1970) defines three forms: (1) weak: prices reflect all past price data; (2) semi-strong: prices reflect all public information (P_t = E[P_{t+1}+D_{t+1} | I_t]/(1+r)); (3) strong: prices reflect all information including private. The semi-strong form is most empirically supported for liquid markets, though not perfectly.
**Introduced:** Addendum A

### Efficient Frontier
**Plain:** The set of portfolios that offer the highest expected return for each level of risk — you cannot do better without accepting more risk.
**Formal:** The efficient frontier is the upper boundary of the minimum-variance set in mean-variance space: min_{w} w^T·Σ·w subject to w^T·μ = μ_target and Σᵢwᵢ = 1. Points on the frontier are Pareto-optimal in the (σ, μ) plane.
**Introduced:** Day 62

### Goodhart's Law
**Plain:** When a measure becomes a target, it ceases to be a good measure — agents optimise the metric while undermining the underlying goal.
**Formal:** Originally stated as "any observed statistical regularity will tend to collapse once pressure is placed upon it for control purposes" (Goodhart, 1975). In RL: if a reward function R is a proxy for true utility U, optimising R too aggressively causes divergence from U — formally captured by the overoptimisation scaling law R_true(d) ≈ α√d − βd where d is KL divergence from the initial policy.
**Introduced:** Day 46

### Importance Sampling (Off-Policy Correction)
**Plain:** A technique for using data collected under one policy (the behaviour policy β) to estimate performance under a different policy (the target policy π) — by reweighting each data point by how much more or less likely π is to have produced it.
**Formal:** Off-policy gradient estimator: ∇_θ J ≈ Σ_t [π_θ(aₜ|sₜ) / π_β(aₜ|sₜ)] · rₜ · ∇_θ log π_θ(aₜ|sₜ). To prevent high-variance blowup, the ratio is capped: ρₜ = min(π_θ/π_β, c).
**Introduced:** Day 49

### Inside View / Outside View
**Plain:** Inside view: reasoning from the specific details of your situation. Outside view: asking what the base rate is for similar situations, before engaging with the details of your case.
**Formal:** Kahneman & Lovallo (1993) define the inside view as causal-model reasoning from situation-specific features, and the outside view as reference-class reasoning from the empirical distribution of outcomes in a comparison class. The planning fallacy results from systematic over-reliance on the inside view.
**Introduced:** Day 60

### Kelly Criterion
**Plain:** The fraction of your bankroll to bet on each opportunity that maximises the long-run growth rate of wealth — bet less than this and you grow slower than possible; bet more and you risk ruin.
**Formal:** For a binary bet with probability p of winning and odds b-to-1: f* = (b·p − q) / b, where q = 1−p. Equivalently, f* = (edge) / (odds). The Kelly fraction maximises E[log W] per period, which is equivalent to maximising the geometric growth rate.
**Introduced:** Day 62

### Multi-Agent Reinforcement Learning (MARL)
**Plain:** RL with multiple learning agents interacting simultaneously — each agent's environment is partly determined by the other agents' changing policies, which makes the problem non-stationary and game-theoretic.
**Formal:** In MARL, each agent i has policy πᵢ: S → Δ(Aᵢ). The joint value function Qᵢ(s, a₁,…,aₙ) depends on all agents' actions. Solution concepts: Nash Q-values (Nash equilibrium of the stage game at each state), CTDE (centralised training, decentralised execution), and correlated equilibrium policies.
**Introduced:** Day 48

### Off-Policy Learning
**Plain:** Learning about one policy (the target) from data generated by a different policy (the behaviour) — allows reuse of historical data but requires importance-sampling corrections.
**Formal:** Off-policy methods estimate V^π or Q^π using trajectories generated by π_β ≠ π. Q-learning is off-policy because it bootstraps from max_{a'} Q(s',a') regardless of the behavioural policy. Contrast with on-policy methods (SARSA, policy gradient), which only use data from the current policy.
**Introduced:** Day 44

### Planning Fallacy
**Plain:** The systematic tendency to underestimate the time, cost, and difficulty of future plans — and to overestimate the benefits — because planners reason from the inside view.
**Formal:** A cognitive bias (Kahneman & Tversky, 1979; elaborated in Lovallo & Kahneman, 2003) in which forecasts of a task's completion are lower than the actual completion time, even when the planner has personal experience of similar tasks taking longer. Corrected by reference class forecasting.
**Introduced:** Day 60

### Policy Gradient
**Plain:** Directly optimise the policy by computing the gradient of expected total reward with respect to the policy parameters and ascending it — no need to learn a value function first.
**Formal:** REINFORCE: ∇_θ J(θ) = E_π[Σₜ ∇_θ log π_θ(aₜ|sₜ) · Gₜ]. The gradient is the expected product of the log-probability gradient and the return. PPO-Clip: L^CLIP(θ) = E[min(rₜ(θ)·Âₜ, clip(rₜ(θ), 1−ε, 1+ε)·Âₜ)] where rₜ = π_θ/π_θ_old.
**Introduced:** Day 46

### Pre-mortem
**Plain:** Before starting a project, imagine it has already failed one year from now and write the story of why — a structured technique to surface failure modes before they materialise.
**Formal:** A prospective hindsight exercise (Klein, 2007) that reverses the social dynamics of standard risk assessment by reframing the question from "what could go wrong?" (which invites defensiveness) to "the project failed — why?" (which licenses disconfirmatory thinking and surfaces underweighted failure modes).
**Introduced:** Day 60

### Quasi-hyperbolic Discounting
**Plain:** A model of present bias — you discount the immediate future steeply (β factor), then discount subsequent periods geometrically (δ factor). Captures why people act impatient in the moment but patient in the abstract.
**Formal:** Uₜ = uₜ + β · Σ_{τ>t} δ^(τ−t) · u_τ, with β ∈ (0,1] capturing present bias and δ ∈ (0,1) capturing long-run patience. When β < 1, the agent is time-inconsistent: preferences at time t conflict with preferences at time t−1 about the t-to-future tradeoff.
**Introduced:** Day 58

### Reference Class Forecasting
**Plain:** A four-step method for beating the planning fallacy: (1) identify the reference class of similar projects, (2) look up the base-rate distribution of outcomes, (3) adjust for specific features of your case, (4) sanity-check that adjustment.
**Formal:** Formalised by Flyvbjerg (2006) from Kahneman & Lovallo's inside/outside view distinction. The reference class provides an empirical prior P_base; specific features update this prior: P(success | features) = [P(features | success) / P(features)] · P_base, applied conservatively because most adjustments reflect inside-view optimism.
**Introduced:** Day 60

### Reward Hacking
**Plain:** When an RL agent finds a policy that scores well on the specified reward function but fails to achieve the intended goal — exploiting gaps between the proxy measure and the true objective.
**Formal:** Reward hacking occurs when π* = argmax_π E[Σ R(s,a)] satisfies the reward specification but not the designer's true utility function U. Formally: ∃π such that E_π[Σ R] > E_{π_human}[Σ R] but E_π[Σ U] < E_{π_human}[Σ U]. Closely related to Goodhart's Law and specification gaming.
**Introduced:** Day 46

### RLHF (Reinforcement Learning from Human Feedback)
**Plain:** Fine-tune a language model using human preference ratings rather than hand-coded rewards — humans compare model outputs and their preferences train a reward model, which then guides policy optimisation.
**Formal:** Three-stage pipeline: (1) supervised fine-tuning on demonstrations; (2) reward model R_θ(x,y) trained on human preference data via cross-entropy on pairs: L = −E[(y_w, y_l)][log σ(R_θ(x,y_w) − R_θ(x,y_l))]; (3) policy optimisation via PPO: max_π E[R_θ(x,y)] − β · KL[π||π_ref].
**Introduced:** Day 50

### Sharpe Ratio
**Plain:** The return you earn per unit of risk — how much excess return (above the risk-free rate) you get for each unit of volatility.
**Formal:** SR = (μ_p − r_f) / σ_p, where μ_p is the portfolio expected return, r_f the risk-free rate, and σ_p the portfolio standard deviation. A higher Sharpe ratio means you're being compensated more per unit of risk taken.
**Introduced:** Day 62

### Structural Hole
**Plain:** A gap in a social network between two groups that are not directly connected — a broker who spans the hole controls information flow between them and extracts value from both sides.
**Formal:** A structural hole exists between clusters A and B when there is no direct edge (or only weak edges) between members of A and members of B. A broker v spanning the hole has high betweenness centrality over A×B pairs. Effective network size (Burt, 1992): E_i = Σ_j [1 − Σ_q p_{iq} · m_{jq}] summed over j ≠ i.
**Introduced:** Day 57

### Superforecaster
**Plain:** A forecaster who consistently achieves near-optimal probabilistic calibration across a wide range of real-world questions — identified in Tetlock's Good Judgment Project by tracking predictions over years.
**Formal:** Operationalised via Brier score: superforecasters achieve BS < 0.15 over sustained prediction tournaments. Characterised by: thinking in probabilities (not binaries), actively seeking disconfirming evidence, updating fluidly on new information, and combining inside and outside views.
**Introduced:** Day 61

### SUTVA (Stable Unit Treatment Value Assumption)
**Plain:** Each user's outcome depends only on their own treatment assignment, not on what treatment other users received — the assumption that makes standard A/B test inference valid.
**Formal:** SUTVA requires: (1) no interference between units (Yi(T) does not depend on Tj for i ≠ j); (2) no hidden treatment variations (there is a single version of each treatment level). Violated whenever users interact (social platforms, marketplaces) or share resources (infrastructure experiments).
**Introduced:** Day 53

### Sycophancy (AI)
**Plain:** An AI model's tendency to produce outputs that match what the user seems to want rather than what is accurate — a reward hacking failure where the model optimises for approval rather than truth.
**Formal:** Sycophancy is a misalignment failure mode in RLHF: human raters tend to prefer responses that validate their beliefs, creating a principal-agent problem where the reward model R_θ captures "perceived quality" rather than "actual quality." Formally: E[R_θ(x,y_sycophantic)] > E[R_θ(x,y_accurate)] even when U(y_accurate) > U(y_sycophantic).
**Introduced:** Day 50

### Two-Sided Market (Platform)
**Plain:** A market where a platform intermediates two distinct groups of users (e.g., buyers and sellers, users and advertisers), and each group's value depends on how many users are on the other side.
**Formal:** A two-sided market (Rochet & Tirole, 2003) has two user groups i ∈ {1,2} with cross-side network effects: user group 1's utility increases in n₂ and vice versa. Optimal platform pricing: p_i* = (MC_i − e_{j→i}) / (1 − 1/|η_i|), where e_{j→i} is the cross-side externality and η_i is group i's price elasticity.
**Introduced:** Day 51

### ZOPA (Zone of Possible Agreement)
**Plain:** The range of deal terms that both parties would prefer to no deal — if there is no overlap between what the buyer is willing to pay and what the seller is willing to accept, no agreement is possible.
**Formal:** ZOPA = [r_seller, r_buyer] when r_buyer ≥ r_seller; empty otherwise. Any agreement x ∈ ZOPA is individually rational for both parties. Bargaining theory (Rubinstein, Nash) predicts which point in the ZOPA is selected based on patience parameters and threat points.
**Introduced:** Day 59

### Volatility Drag
**Plain:** Volatile investments grow more slowly than their arithmetic average return suggests — because a 50% loss requires a 100% gain to recover, not a 50% gain.
**Formal:** r_geometric ≈ r_arithmetic − σ²/2. The gap between arithmetic mean and geometric mean equals half the variance. For a strategy with 30% annual volatility, the geometric return is ~4.5% below the arithmetic return. This is why leveraged ETFs reliably underperform their stated multiple over any period beyond one day, and why volatility is a direct cost in compounding portfolios.
**Introduced:** Addendum A

---

*This glossary is a living document. New terms are added as each day's page is written. If you encounter a term used before its introduction day, flag it as an error.*