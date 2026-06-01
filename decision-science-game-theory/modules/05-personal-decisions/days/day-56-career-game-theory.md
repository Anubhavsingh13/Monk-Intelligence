*[Decision Science & Game Theory](../../../../README.md) · [Module 5 — Strategic Life & Career](../5-overview.md) · Day 56 of 65*

# Day 56 — Your Career as a Repeated Game

**Module 5: Personal Decision-Making**
**Reading time: ~35 minutes**

---

**Today's one idea:** A career is not a sequence of independent job decisions — it is a repeated game with a reputation mechanism, where your strategy in early rounds determines what equilibria are available to you in later rounds, and patience (high δ) is the most underrated competitive advantage.

**Prerequisites:** Day 21 (repeated games/Folk Theorem), Day 25 (bargaining theory), Day 28 (signalling)

**Primary sources:** Frank, Robert H. *Passions Within Reason: The Strategic Role of the Emotions.* W.W. Norton, 1988. Chapters 1–3. Gibbons, Robert & Waldman, Michael. "Careers in Organizations: Theory and Evidence." *Handbook of Labor Economics,* Vol. 3B, 1999.

---

## The Hook

The conventional career advice is backward.

"Network strategically." "Optimize for salary." "Position yourself for the next role."

These are one-shot game strategies applied to a repeated game — and repeated game theory tells us they're often wrong.

In a one-shot game, defection is rational. Take the money. Burn the bridge. Optimise for yourself right now. There is no tomorrow, so there is no punishment for opportunism.

But in a repeated game with reputation effects, the calculus flips completely. The optimal strategy looks more like reciprocity, honesty, and genuine quality — not because these are morally nice, but because they are strategically dominant. This is what Robert Frank argued in *Passions Within Reason*: emotions like loyalty, guilt, and pride exist partly because they solve commitment problems in repeated games. You behave honourably not just because you want to, but because behaving honourably in a repeated game pays.

Your career is the longest repeated game you'll ever play.

The conventional optimisers who squeeze every interaction for short-term gain are playing the wrong game. They're solving a one-shot problem in a multi-decade repeated interaction. The folk theorem (Day 21) tells us exactly why this is a mistake: in a repeated game with a high enough discount factor, cooperation is a sustainable equilibrium. The opportunist's strategy is only dominant in the single-shot case.

This day translates the formal machinery from Modules 2 and 3 into a personal operating system for career decisions.

---

## Building the Intuition

### 1. The Career as a Multi-Round Reputation Game

Your reputation is the history of play that other agents observe and use to determine what strategies they will use against you.

In the repeated games framework from Day 21, the Folk Theorem says any individually rational payoff is sustainable as an equilibrium if the discount factor δ is high enough. For careers:

**δ_career = probability you interact with these people again × how much you care about future outcomes relative to present ones**

Industries are smaller than they appear. The colleague you dismiss rudely at 27 is the hiring manager at your next company at 34. The VC you pitch carelessly becomes a reference check at your Series A. The employer you leave badly becomes a potential client when you start your own firm. The professor whose class you blew off is on the committee reviewing your promotion.

The horizon of career interactions is unknown — which is exactly the condition under which reputation mechanisms are most powerful. When you don't know when the game ends, you can't backward-induct to defection (Day 21: the finite horizon problem). Unknown horizon length forces both parties to behave as if the game continues.

**The strategic implication:** treat every professional interaction as a repeated game with an unknown horizon. Tit-for-tat with forgiveness — reciprocate cooperation, punish defection once, then return to cooperation — outperforms opportunistic strategies in expectation across the career. Not because of niceness. Because of game theory.

The practical version of this: deliver more than you promise. Be specific about what you will do and do it. When you can't, communicate early. These behaviours are not just good manners; they are the strategy that builds the reputation that unlocks better equilibria in later rounds.

### 2. Signalling Theory Applied to Careers

From Day 28: a signal is only credible if it satisfies the single-crossing condition — if it is more costly for low-types to produce the signal than for high-types.

Spence's original model applied to education: going to Harvard is a signal of ability partly because it is harder for low-ability people to get in and graduate. The costly signal is credible because mimicry is expensive.

Now apply this to career signals. Ask yourself for each credential or marker of quality: can a mediocre person fake this cheaply?

- A degree from a prestigious university: somewhat — you can go through the motions without absorbing much. The signal is partially degraded by credential inflation.
- A published paper in a refereed journal: harder to fake. Reviewers filter. Time investment is high. Low-quality work usually gets rejected.
- A working piece of software used by thousands of people: very hard to fake. Users and GitHub history are public. Performance is observable.
- A portfolio of projects with measurable outcomes: hard to fake. Clients either came back or they didn't. Numbers either pan out or they don't.

The single-crossing condition tells you which signals to invest in: the ones where the difficulty of producing them is negatively correlated with your quality. If you're genuinely skilled, the hardest-to-fake signals are the ones worth building, because they carry the most credible information to the market.

**Career implication:** build track record over credentials. Shipped product over claimed expertise. Published analysis over said-so. Not because credentials are worthless, but because track record is a harder-to-fake signal that commands a larger premium from a well-functioning market.

There is a corollary: if everyone in your field is producing the same signal (the same certification, the same MBA, the same buzzword on their CV), that signal has been driven to near-zero informational value by mimicry. The question to ask is not "what signal do successful people have?" but "what signal separates me from low-quality people at minimum cost to me?" That signal is your comparative advantage in the signalling game.

### 3. Bargaining Power in Salary Negotiation

From Day 25: your bargaining power in any negotiation is determined primarily by your BATNA — Best Alternative to Negotiated Agreement. Your BATNA is your walk-away point. It determines the minimum you will accept and therefore bounds how much the other side can extract.

The Rubinstein alternating-offers model (Day 25) tells us something more precise: in a negotiation where both parties alternate offers until agreement, the more patient party extracts more of the surplus. Patience is measured by δ — how much you discount the value of reaching agreement one period later.

In salary negotiations, the employer is almost always less patient than the candidate thinks. They have a budget cycle. A team that needs someone. A manager who is losing sleep over an unfilled role. Their δ is lower than they let on.

The candidate who needs the job urgently is the one with the low δ. Financial pressure, visa deadlines, family obligations — anything that makes you need to close the deal now — reduces your δ and therefore your share of the surplus.

**This is why "never negotiate when you need the job urgently" is not just folk wisdom but a theorem.** It follows directly from the Rubinstein result.

The practical strategy:

1. **Build BATNA before you need it.** Keep your skills sharp enough that you have real alternatives at all times. An outside offer is the most powerful negotiating tool. Even one you would not take transforms the negotiation.

2. **Run parallel processes.** Interview at multiple places simultaneously. This is the portfolio diversification principle from Day 42 applied to job search — it creates real options and real BATNAs.

3. **Information asymmetry works both ways.** The employer has salary band information you don't have. You have your outside offers and bottom line they don't have. From Day 27: the side that reveals less extracts more. Know your number before you talk. Don't disclose your current salary first if you can avoid it. Let them make the first offer.

4. **The desperation signal is fatal.** Signalling that you really need this job destroys your δ estimate in the employer's model and shifts the Rubinstein split toward them. Even if you do need the job, behave with optionality. This is a case where the signalling game and the bargaining game are active simultaneously.

### 4. The Exploration-Exploitation Problem in Career Choices

From Day 42: the multi-armed bandit problem asks how to allocate exploration (trying new options to learn their value) vs. exploitation (committing to the best-known option). The UCB (Upper Confidence Bound) strategy says: try the arm with the highest (expected value + uncertainty bonus). The uncertainty bonus rewards exploring options you don't know much about.

Applied to careers:

**Early career is the exploration phase.** Try different roles, industries, and functions. The information value of an early career move is high even if the expected salary or prestige is lower — you are generating data about what you're good at, what you enjoy, and where your comparative advantage is. The uncertainty bonus is large when you're young.

**Mid-career is the exploitation phase.** Once you've identified the domain where your comparative advantage is greatest (where the ratio of your skill to the effort required is highest relative to alternatives), go deep. Switch costs are higher later, so exploration has a higher opportunity cost.

**The regret framework:** Jeff Bezos's regret-minimisation framework is a practical approximation of UCB. Ask: which choice will I regret not having tried when I'm 80? The choices with the highest regret from inaction are usually the ones with high uncertainty — you don't know how they would have turned out. These are the ones with the highest uncertainty bonus in UCB terms.

Research on career regret consistently finds that people regret inactions more than actions. The career path not tried haunts more than the career path tried and failed. This is empirical validation of the UCB intuition: the cost of underexploring is higher than the cost of a failed exploration.

**The asymmetry of recovery:** most career mistakes are recoverable. A job that doesn't work out, a project that fails, a company that folds — these are costly but rarely fatal. A career of safe choices that never tested your real comparative advantage is a permanent loss. The asymmetry pushes toward more exploration than most people's risk aversion leads them to choose.

### 5. Comparative Advantage and Specialisation

Ricardo's comparative advantage principle (originally about international trade, directly applicable to careers): even if you are absolutely better than a colleague at everything, it still pays both of you to specialise. You should specialise in whatever task gives you the highest relative advantage over the alternative uses of your time.

If you are 50% better than a colleague at analysis and 20% better at presentations, your comparative advantage is in analysis. You should specialise there even though you are absolutely better at both. The colleague should do the presentations.

**Career implication:** the question is not "what am I best at in absolute terms?" but "where is my relative advantage greatest?" Your career returns are highest in the activity where you are most differentiated from the competition per unit of effort. This is often not the thing you are proudest of, or the thing that sounds most impressive. It is the thing where you are unusually good relative to the population of people competing for the same roles.

The competitive market amplifies this: you are not competing on absolute ability against the median person in a field. You are competing on comparative advantage against the people who have also identified that domain as their comparative advantage. Specialisation concentrates your competitive advantage. Generalism disperses it — which is useful early for exploration but costly late when you're trying to command premium returns.

### 6. The Long Game — Commitment and Credibility

From Day 21: grim trigger strategies (cooperate until defection, then defect forever) require commitment to the punishment phase to be credible. Without credible commitment to punish, the cooperation equilibrium unravels.

From Day 24 (Schelling): commitments work strategically because they remove future options. Burning your boats removes the option to retreat. This makes your advance credible. The enemy knows you cannot fall back, so your forward commitment is real.

Career version: certain reputation-based commitments become more valuable as they become verifiable through your history.

- "I always deliver on time." Valuable when you have years of track record.
- "I never take credit for others' work." Valuable when your collaborators verify it.
- "I always respond to referral requests." Valuable when your network sees the pattern.

These are not virtues declared — they are strategies observable through your history of play. The repeated game (Day 21) is what makes them enforceable and therefore credible.

**Schelling-style career commitment:** publicly commit to a strategy to make it credible. Announcing a career pivot publicly makes it harder to quietly retreat — and this costliness is the signal that you're serious. It's a costly signal (Day 28) that communicates genuine intent to the market. This is why public goal-setting has higher follow-through than private goal-setting: the commitment is observable, and defection is costly.

---

## The Formal Picture

**Career discount factor.** How much you value future professional outcomes relative to present ones depends on two things: how likely you are to stay in this industry and community (the higher the probability you'll see the same people again, the more the future matters), and how patient you are psychologically (how much you discount next year's opportunities relative to today's). Multiply those two together and you get your effective career discount factor. For knowledge workers in concentrated industries — finance, technology, academia, law — this factor is typically between 0.85 and 0.95. These are small worlds and long careers. You will see the same people for decades.

**Cooperation threshold (from Day 21).** In a repeated Prisoner's Dilemma, cooperation is sustainable when your discount factor δ exceeds the threshold:

> **δ* = (T − R) / (T − P)**

where T = the temptation payoff (the gain from one-shot opportunism — the overstated accomplishment, the credit-stealing, the burned bridge), R = the reward payoff (the long-run compounded value of reputation-based referrals and opportunities from mutual cooperation), and P = the punishment payoff (mutual reputation damage from an acrimonious professional relationship).

When your career discount factor exceeds δ*, reputation-based cooperation is the dominant strategy. Exercise 1 asks you to estimate these values for your own field and compute your threshold. For most knowledge workers in concentrated industries, this condition is satisfied comfortably. One-shot opportunism is only optimal when your discount factor is very low — when you're genuinely certain you'll never see these people again.

**Rubinstein bargaining with asymmetric patience.** In alternating-offers bargaining (Day 25), your share of the surplus above your BATNA follows from the two sides' patience. The less patient the employer, the more of the surplus you can claim. In the Rubinstein alternating-offers framework, your share is:

> **your share = (1 − δ_employer) / (1 − δ_you × δ_employer)**

Example: the employer needs to fill the role in two weeks (δ_employer ≈ 0.60). You have savings and other options and can wait three months (δ_you ≈ 0.90).

> your share = (1 − 0.60) / (1 − 0.90 × 0.60) = 0.40 / 0.46 ≈ 0.87

You should claim roughly 87% of the surplus above your BATNA. If your BATNA is $120k and the role's maximum is $160k, the surplus is $40k — so you should aim for approximately $155k. Exercise 3 asks you to apply this to your own negotiation.

The calculation is rough — but the direction is exact. Patience commands a premium. Building BATNA before you need it is the highest-return investment in career economics.

---

## Where It Breaks

**Network effects dominate signalling in many fields.** Who you know can matter more than what you signal — particularly in industries where deals are closed through relationships, not credentials. A mediocre candidate from the right network often beats an excellent candidate from outside it. No amount of costly signalling gets you access to a closed network. Breaking into a network-dominated field requires a different strategy — one involving structural position and weak ties (Day 57) rather than pure quality signalling.

**The reputation game requires repeated interaction.** In large, geographically dispersed, or high-turnover industries — construction, retail, some segments of manufacturing — reputation mechanisms are weaker because repeat interaction is rarer. The Folk Theorem's sustaining condition (high δ) doesn't hold when you genuinely won't see these people again. In these contexts, one-shot game strategies become more appropriate. The advice here is industry-specific: know your industry's structure before choosing your strategy.

**Patience requires slack.** High-δ strategies require the financial runway to decline bad offers and wait for good ones. They require the psychological security to avoid urgency signals in negotiation. If you have neither — if you need this job by next month to make rent — you are forced into low-δ play even in a repeated environment. The game theory is correct but the constraint is real. This is why financial security and career optionality are deeply linked: savings don't just protect against bad outcomes, they enable better strategies.

**Early reputation is hard to update.** The reputation you build in your first few years in a field is disproportionately sticky. People who knew you as a junior employee continue to apply their early model even as you develop. This is both a risk (early mistakes compound) and an opportunity (early excellence compounds too). The implication: the reputation investment is most productive very early.

---

## Try It Yourself

**Exercise 1: Estimate your δ**

Estimate your career discount factor. Consider:
- How concentrated is your industry? If you left today, what fraction of the people you deal with professionally would you encounter again in 10 years?
- How much do you weight career outcomes in 10 years relative to career outcomes next year? (If equally: δ ≈ 0.91 per year. If you mostly care about next year: δ might be as low as 0.5.)
- Is your estimated δ above the cooperation threshold for your industry? Estimate T, R, and P for a typical opportunistic move in your field and compute $\delta^* = (T-R)/(T-P)$.

The result tells you whether pure reputation-based cooperation is already a dominant strategy for you, or whether you're in a domain where more nuance is needed.

**Exercise 2: Signal audit**

List the three most important signals of quality in your field. For each signal, ask:
- Can a mediocre person in my field produce this signal cheaply?
- Does this signal satisfy the single-crossing condition — is it harder for low-quality people to produce than high-quality people?
- If the signal doesn't satisfy the condition, what signal would? Is there a track-record-based, observable-output signal that would separate you from mimics?

Then ask: which of these signals do I currently possess, and at what level? This is your signalling portfolio audit.

**Exercise 3: Negotiation BATNA analysis**

Think of your most recent or upcoming salary negotiation. Write down:
- Your BATNA (the best alternative you have if this negotiation fails)
- Your estimate of the employer's BATNA (the cost to them of the role staying unfilled)
- Your rough estimate of $\delta_{\text{you}}$ and $\delta_{\text{employer}}$

Apply the Rubinstein formula: what fraction of the surplus above your BATNA should you claim? What dollar number does this translate to? Is your current negotiation strategy consistent with this number, or are you leaving money on the table due to urgency signalling or insufficient BATNA development?

---

## Connect It Back

Your career is the repeated game you're playing longest. Everything from Modules 2 and 3 transfers directly: reputation as a sustaining mechanism for cooperation equilibria (Day 21), signalling as the tool for communicating quality to markets with information asymmetry (Day 28), bargaining as the structure of every compensation conversation (Day 25), and exploration-exploitation as the framework for early vs. late career strategy (Day 42).

The key shift from those modules to this one: you are no longer analysing an abstract game between anonymous players. You are the player. Your history of play is your reputation. Your outside options are your BATNA. Your discount factor is determined by your financial situation, your psychology, and your industry structure.

**Tomorrow (Day 57):** network strategy — the structural dimension of career game theory. Your position in a social network determines what information flows to you first and what coordination opportunities you can access. Reputation determines what happens when people interact with you; network position determines who interacts with you.

---

## Suggested Readings

- Frank, Robert H. *Passions Within Reason: The Strategic Role of the Emotions.* W.W. Norton, 1988. Chapters 1–3. The argument that emotions are commitment devices in repeated games — one of the most useful frameworks for understanding professional behaviour.
- Gibbons, Robert & Waldman, Michael. "Careers in Organizations: Theory and Evidence." *Handbook of Labor Economics,* Vol. 3B, 1999. Sections 1–3. Formal treatment of human capital, signalling, and tournament theory in career contexts.
- Burt, Ronald. "Structural Holes and Good Ideas." *American Journal of Sociology,* 110(2), 2004. Preview for Day 57: the empirical evidence that network brokerage produces career advantage.


---

← [Day 55 — Rest & Synthesise](day-55-rest-synthesise) &nbsp;|&nbsp; [Day 57 — Network Strategy & Structural Holes →](day-57-network-strategy)
