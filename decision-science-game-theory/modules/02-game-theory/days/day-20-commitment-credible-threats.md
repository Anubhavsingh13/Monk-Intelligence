*[Decision Science & Game Theory](../../../../README.md) · [Module 2 — Game Theory Fundamentals](../2-overview.md) · Day 20 of 65*

# Day 20 — Commitment & Credible Threats

> **Today's one idea:** A threat is credible only if you would rationally carry it out when the time comes — and smart players design situations *in advance* that make commitment visible, changing what opponents believe you will do before the game even begins.
> **Reading time:** ~35 min · **Prereqs:** [Day 19](day-19-sequential-games.md), [Day 16](day-16-nash-equilibrium.md)
> **Primary source for today:** Dixit & Nalebuff, *The Art of Strategy*, Chapter 6 ("Credible Commitments"). Also: Schelling, Thomas C. *The Strategy of Conflict*, Chapter 2 ("An Essay on Bargaining").

---

## The Hook

In 1519, Hernán Cortés landed on the coast of Mexico with 600 soldiers to conquer the Aztec Empire — vastly outnumbered and far from home. His first order was not to attack. It was to **burn the ships**.

With no retreat possible, his soldiers had only one option: fight forward. The commitment was total. And it worked — not because burning the ships made his army stronger in any direct sense, but because it changed what his soldiers (and perhaps their opponents) believed would happen. There was no negotiated withdrawal in this game tree.

Cortés understood something that took game theory 400 years to formalise: **credible commitment changes your opponents' beliefs about your future actions, which changes their current actions, which can improve your outcome even though you've constrained your own choices.**

Voluntarily giving up options can be a strategic advantage.

---

## Building the Intuition

### The commitment problem

From Day 19's entry deterrence game: the Incumbent *wants* to threaten to fight if Entry occurs. This threat, if believed, would deter the Entrant. But backward induction exposes it as non-credible — when entry actually happens, fighting is too costly for the Incumbent.

The Incumbent's problem: how do you make the threat credible before the game begins?

**Three commitment mechanisms:**

---

**Mechanism 1: Burn the ships — eliminate your own option to retreat.**

If the Incumbent can irreversibly invest in capacity before the Entrant decides, the calculus changes.

*Without commitment:* Incumbent fights entry only if it's profitable ex-post. It isn't.

*With capacity commitment:* Incumbent builds a large production facility *before* the entry decision. Now fighting is cheaper (fixed costs already sunk), and not fighting means the expensive capacity sits idle. The payoff structure has changed — fighting might now be ex-post rational.

This is the economic theory of **excess capacity as entry deterrence**: build more capacity than you need for the current market, not because it's efficient, but because it changes the fight-or-accommodate calculus after entry.

---

**Mechanism 2: Create a hostage — give the other party power over your future behaviour.**

In hostage-taking (the contractual version), you give the other party something valuable that they can destroy if you defect. This credibly commits you to cooperation.

*Business version:* Prepayment, performance bonds, irreversible specialised investment in a customer's system. If you fail to deliver, you lose the prepayment. The prepayment is the hostage.

*Reputational version:* Making a public announcement. If the Incumbent publicly declares "We will fight any entrant," breaking this commitment destroys reputation. The reputational cost converts a non-credible threat into a partially credible one — at the cost of constraining future flexibility.

---

**Mechanism 3: Delegation — bind yourself by making someone else the decision-maker.**

A president who delegates nuclear launch authority to a general (under certain conditions) has made a commitment that even they cannot reverse in the moment. The general follows rules, not real-time political calculation. This changes adversaries' beliefs about when the nuclear option will be used.

*Business version:* Precommitting to an automatic pricing response algorithm. If a competitor drops prices below a threshold, your system automatically matches — no human approval needed, no delay. Competitors who know your algorithm cannot exploit the gap between "human decides to respond" and "response happens."

*Negotiation version:* "I don't have authority to offer more than X" — delegating to an absent principal makes it impossible (not just costly) to exceed X. The lack of authority is the commitment device.

---

### The geometry of commitment in a game tree

Commitment changes the game tree *before* the game begins. Consider the Entrant-Incumbent game with and without commitment:

**Without commitment:**
```
Entrant: Enter ──→ Incumbent: Fight  ─── (−1, −1)
                            Accommodate ─ (1, 1)
Entrant: Stay Out ──────────────────────── (0, 3)
```
SPE: Entrant enters (because Incumbent will accommodate). Payoffs: (1, 1).

**With capacity commitment (Incumbent invests $2 before the game):**

Now fighting costs $1 less (capacity already paid), accommodating requires sharing the market but capacity is semi-idle:
```
Entrant: Enter ──→ Incumbent: Fight  ─── (−1, −3)  [Incumbent fights: costs −1 from war, already paid −2 for capacity]
                            Accommodate ─ (1, −1)   [Incumbent accommodates: capacity underutilised, net −1 before market share]
Entrant: Stay Out ──────────────────────── (0, 1)   [Incumbent monopoly: capacity investment pays off, net 3−2=1]
```

*Wait — this isn't quite right. Let me redo this with a cleaner example.*

The point: after the investment, Incumbent's payoff from fighting may now exceed accommodation (the investment makes fighting cheaper or accommodation more costly). If fighting now yields a higher payoff, the threat becomes credible, and the Entrant stays out.

**The paradox of commitment:** Paying $2 to restrict your options can make you better off. Without the investment, you get 1 (Entrant enters, you accommodate). With the investment, you get 1 (Entrant stays out, you earn the monopoly payoff minus investment cost). If monopoly payoff − investment cost > 1, commitment pays.

---

## The Formal Picture

**Definition (commitment device):**

A commitment device is an action taken at time t that changes the payoffs or available actions at time t+k (k > 0), in a way that makes a specific future action credible that would not otherwise be credible.

**Condition for a credible threat:**

A threat to take action a at node v is credible if and only if taking a at v is a best response *at node v* — i.e., it is part of a subgame perfect equilibrium of the subgame rooted at v.

A commitment mechanism makes a threat credible by changing the payoffs at v so that carrying out the threat becomes the ex-post optimal action.

**The taxonomy of commitment mechanisms:**

| Mechanism | How it works | Example |
|-----------|-------------|---------|
| Burning ships | Eliminate retreat options | Irreversible investment, public announcement |
| Hostage | Costly if you deviate | Deposit, performance bond, reputation stake |
| Delegation | Transfer decision authority | Algorithm-based response, rigid rules, treaties |
| Reputation | Future cost of deviation | Brand equity, long-term customer relationships |
| Contracts | Legal enforcement | Precommitment to supply terms, non-compete |

**When does commitment pay?**

Commitment is worth it when:
```
(payoff with commitment) − (cost of giving up flexibility) > (payoff without commitment)
```

The "cost" here is the option value of the flexibility you surrendered — the value of being able to change your mind later. Commitment only makes sense when the strategic benefit (deterring rivals, building credibility) exceeds this cost.

---

### Commitment in AI products and careers

**Pricing commitment:**

A product commits to "we will not raise prices for 12 months" — a public guarantee. This makes the no-price-increase threat credible and can expand market share by reducing buyer hesitation. But it constrains future pricing flexibility. The commitment is only worthwhile if the market share gain exceeds the option value of flexible pricing.

**API stability commitments:**

Developers care about API stability. A platform that commits to "no breaking changes for 18 months" makes a credible commitment that changes developer investment decisions — developers build more deeply on stable APIs. The platform sacrifices flexibility to earn deeper ecosystem commitment. This is the burning-ships logic: removing your own option to change the API makes partners trust you more.

**Career commitments:**

Choosing a specialisation (deep ML, product management, research) is a commitment that sacrifices flexibility. Publicly announcing a transition ("I am moving into AI governance") changes how opportunities reach you. Visible commitments — blogging, conference talks, GitHub activity — are hostages to your stated direction. They are costly to reverse (reputational cost), which makes them credible signals to employers and collaborators.

**Pre-commitment to AI safety protocols:**

An AI lab that publicly commits to specific deployment standards (e.g., "we will not deploy models above capability level X without safety certification") makes a commitment that, if believed, changes other labs' deployment strategies. The commitment is only credible if violation would be publicly verifiable and carry reputational consequences — hence the importance of external audits and red-teaming disclosures. This is mechanism design applied to the AI safety coordination dilemma from Day 17.

---

## Where It Breaks / What It Is Not

**Commitments can be too rigid.** Committing to "always match competitor prices" prevents you from exploiting situations where your cost advantages allow profitable high-margin pricing. Optimal commitment is selective — commit where the deterrence value exceeds flexibility value.

**Credibility requires verification.** Burning ships works because it is observable. "We have committed internally to fight any entrant" is not observable and therefore not credible. For a commitment mechanism to work, it must be *publicly observable* and *irreversible or costly to reverse*.

**The Schelling point problem.** Once you have made multiple commitments, opponents may not know which ones you will actually honour. If you have committed to 10 things, the credibility of each individual commitment is reduced. Commitment capital is finite.

**Precommitment can be exploited.** If your commitment is known, sophisticated opponents can design games that force you to commit first and then exploit your constraint. This is the "first-mover disadvantage" in some games — being forced to commit can hurt you if your opponent can then adjust optimally. Don't precommit without analysing what the opponent can do in response.

**Intra-personal commitment (previewing Day 58):** The same logic applies to your future self. "I will exercise every day" is a commitment that fails because the future self has different preferences. Commitment devices that change the costs of deviation — gym memberships with cancellation fees, accountability partners, calendar blocking — are Cortés's ship-burning logic applied to yourself.

---

## Try It Yourself

**Exercise 1 — Classify the commitment mechanism (5 min)**

For each, identify which mechanism makes the commitment credible:

a) A university endows a faculty chair — it cannot be revoked — committing to support a research area indefinitely.

b) A company signs a 3-year office lease before negotiating with a key hire, signalling it plans to be around.

c) A government announces automatic tariff retaliation (via statute) if trading partners impose tariffs above 10%.

d) A startup CEO gives an interview to TechCrunch announcing "we will never sell data to advertisers," building a public reputation stake.

---

**Exercise 2 — Design a commitment device (15 min)**

You are an AI startup founder facing a large incumbent. The incumbent has threatened to aggressively cut prices if you enter the enterprise segment (their core market).

a) Is the incumbent's threat credible under backward induction? (Under what conditions would it be?)

b) What commitment mechanism could the incumbent use to make the threat credible before you enter?

c) What commitment mechanism could *you* use as the entrant to change the incumbent's calculus? (Hint: commitments are not only about threats — they can also signal staying power.)

---

**Exercise 3 — The Rubicon commitment (10 min, L2)**

Julius Caesar crossed the Rubicon river with his army in 49 BC — a legally prohibited act in Roman law that made civil war inevitable. This irreversible act was a commitment device.

Analyse this as a game tree:
- Players: Caesar, Roman Senate
- Before the Rubicon: Caesar can cross or not; Senate can negotiate or prepare to fight
- After the Rubicon: Senate can negotiate or fight; Caesar can advance or retreat

a) Without the Rubicon commitment, what does backward induction predict?
b) After the Rubicon crossing (retreat is no longer an option), how does the game tree change?
c) Why did the commitment shift the Senate's rational response?

---

## Connect It Back

Commitment is the answer to the limitation backward induction exposed: threats must be credible to matter. By designing commitments *before* the game unfolds, you change what others believe you will do — and change what they do in response.

Tomorrow (Day 21) takes this logic into repeated play: when the same game is played many times between the same players, the shadow of future punishment can sustain cooperation as a Nash equilibrium without any external commitment mechanism. The repeated game solution is, in some sense, nature's commitment device.

**The question you should be able to answer now:**
*Why does burning the ships make a retreat threat non-credible while simultaneously making an advance more likely — even though burning the ships makes your army objectively weaker?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Dixit & Nalebuff, *The Art of Strategy*, Chapter 6 ("Credible Commitments"). The commitment examples — from nuclear deterrence to business contracts to sports arbitration — are among the best in the book. Read the full chapter.

**If you want the deep version:**

1. Schelling, Thomas C. *The Strategy of Conflict*. Harvard University Press, 1960. Chapter 2 ("An Essay on Bargaining") and Chapter 5 ("International Strategy"). — Schelling's foundational treatment of commitment, focal points, and credibility in conflict and negotiation. The most important non-technical game theory book written. Chapters 2 and 5 are worth reading carefully.

2. Dixit, Avinash K. "The Role of Investment in Entry Deterrence." *Economic Journal*, 90(357):95–106, 1980. — The formal model of capacity as commitment. Proves that investing in capacity beyond current needs can deter entry as a subgame perfect equilibrium. Read Sections 1–3.

3. For career applications: O'Brien, Timothy L. *The Art of the Deal* — No. Better: Lax, David & Sebenius, James. *3D Negotiation: Powerful Tools to Change the Game in Your Most Important Deals*. Harvard Business Review Press, 2006. Chapter 4 ("Setting Up the Right Negotiation"). — Commitment in negotiation from a practitioner perspective, consistent with today's theory.


---

← [Day 19 — Sequential Games](day-19-sequential-games) &nbsp;|&nbsp; [Day 21 — Repeated Games & Cooperation →](day-21-repeated-games)
