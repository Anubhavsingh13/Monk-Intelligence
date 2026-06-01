*[Decision Science & Game Theory](../../../../README.md) · [Module 2 — Game Theory Fundamentals](../2-overview.md) · Day 27 of 65*

# Day 27 — Information Asymmetry

> **Today's one idea:** When one party in a transaction knows something the other doesn't, markets can fail catastrophically — not from bad intentions, but from the structure of the information problem itself.
> **Reading time:** ~35 min · **Prereqs:** [Day 5](../../01-foundations/days/day-05-bayesian-updating.md), [Day 16](day-16-nash-equilibrium.md), [Day 14](day-14-what-is-a-game.md)
> **Primary source for today:** Akerlof, George A. "The Market for Lemons: Quality Uncertainty and the Market Mechanism." *Quarterly Journal of Economics*, 84(3):488–500, 1970.

---

## The Hook

You are buying a used car. You want a good one — "a peach." The seller knows whether the car is good or bad. You don't.

Sellers of good cars want at least $10,000. Sellers of bad cars ("lemons") would accept $2,000. You can't tell the difference before buying. You might suspect 50% of used cars are lemons. So you offer the expected value: 0.5 × $10,000 + 0.5 × $2,000 = $6,000.

Here's the problem: at $6,000, sellers of good cars ($10,000 minimum) drop out. Only lemon sellers stay. Now you know you're facing a lemon market — you lower your offer to $2,000. The market collapses.

George Akerlof published this argument in 1970. It was rejected by three major journals before the *Quarterly Journal of Economics* accepted it. One reviewer called it "trivial." In 2001, Akerlof won the Nobel Prize in Economics for it. The paper is now the foundation of information economics.

---

## Building the Intuition

### The lemons model in detail

**Setup:**

- Quality of used cars is uniformly distributed on [0, 1] (0 = worst lemon, 1 = best peach).
- Sellers know their car's quality q. Their minimum acceptable price: $q × 10,000 (they value the car at q).
- Buyers cannot observe q before purchase. They offer a price p.
- A seller sells iff p ≥ q × 10,000 → iff q ≤ p/10,000.

**Adverse selection mechanism:**

At price p, only sellers with quality q ≤ p/10,000 sell. The average quality of cars that sell is:

```
E[q | q ≤ p/10,000] = (p/10,000) / 2
```

Buyers value a car at quality q at $q × 12,000 (they value it 20% more than sellers — there are gains from trade).

**Buyer's expected value at price p:**

```
E[buyer value | car offered at p] = 12,000 × E[q | q ≤ p/10,000]
                                   = 12,000 × p/20,000
                                   = 0.6p
```

Since buyer value (0.6p) < price (p) for all p > 0, **no buyer will pay any positive price.** The market collapses entirely — even though there are gains from trade between high-quality sellers and buyers.

**This is the adverse selection death spiral:**

1. Buyers lower offer → high-quality sellers exit → average quality falls → buyers lower offer further → ... → zero.

The market fails not because participants are dishonest, but because the *structure* of the information problem makes trade impossible.

---

### The three canonical information asymmetry problems

Akerlof's lemons model is one of three canonical information problems, each with a different structure:

**1. Adverse Selection (hidden information before contracting):**

One party has private information about their *type* (quality, risk, ability) before a transaction. The uninformed party cannot distinguish good from bad types. Result: bad types crowd out good types (lemons drive out peaches; high-risk customers drive out low-risk ones; unproductive workers drive out productive ones).

*Classic examples:* Health insurance (sick people buy more than healthy, premiums rise, healthy drop out), credit markets (risky borrowers seek loans more than safe ones), hiring (candidates know their own ability better than employers).

**2. Moral Hazard (hidden action after contracting):**

After a transaction is agreed, one party takes actions that affect outcomes but are not observable to the other. The unobservable effort creates the incentive problem.

*Classic examples:* Car insurance (insured drivers take fewer precautions), employment (employees reduce effort when not monitored), debt financing (equity holders take excessive risks when creditors bear the downside).

**3. Signalling (costly revelation):**

An informed party wants to reveal their type. They take an observable action whose cost is different for different types, making it a credible signal. (Day 28 covers this in full.)

*Classic examples:* Education as a signal of ability (Spence 1973), warranties as signals of quality, dividend payments as signals of firm health.

---

### Adverse selection in health insurance

The US health insurance market before the Affordable Care Act was a textbook Akerlof spiral:

- Healthy people have low expected medical costs; they value insurance less.
- Sick people have high expected medical costs; they value insurance more.
- At any given premium, the sick are more likely to buy than the healthy.
- Insurers, knowing this, raise premiums to cover expected costs of their adverse pool.
- Higher premiums push out more healthy people.
- The pool gets sicker; premiums must rise further.
- Eventually, the market serves only the very sick at very high premiums, or collapses.

The fix requires changing the information structure or the participation rules:
- **Mandate:** Force everyone to buy (eliminates adverse selection by eliminating self-selection).
- **Risk pooling:** Employer-provided insurance covers everyone in the company (healthy and sick alike — no self-selection).
- **Screening:** Require health disclosure and price based on risk (works for the insurer; controversial ethically).

Each fix is a different response to the same underlying information asymmetry.

---

### Principal-Agent problems: moral hazard formalised

**The setup:**

An *agent* takes a costly action (effort e) on behalf of a *principal*. The action produces output x = e + noise. The principal observes x but not e. How should the principal structure the contract?

**The tension:**

- If the principal pays the agent a fixed wage: the agent has no incentive to exert effort (e = 0 minimises cost). Pure moral hazard.
- If the principal makes the agent the full residual claimant (sells them the firm): the agent has full incentive (first-best effort). But the agent bears all the risk — which is inefficient if the agent is risk-averse.

**The first-best:**

If effort were observable, the principal pays w(e) = w₀ + c(e) — effort cost plus a flat wage. First-best effort is achieved.

**The second-best (hidden effort):**

Performance-based pay (commission, stock options, bonus) creates incentives but imposes risk on the agent. The optimal contract trades off:
- **Incentive effect:** Higher pay-performance sensitivity → higher effort
- **Risk-sharing:** Higher pay-performance sensitivity → more risk on agent → higher risk premium demanded

The optimal contract is found by maximising the principal's expected output minus the agent's certainty equivalent wage, subject to the agent choosing effort optimally and being willing to participate.

**Second-best result:** Optimal pay-performance sensitivity decreases with the agent's risk aversion and the noisiness of output (more noise → lower powered incentives, because the agent bears more risk for a given incentive). This is why academic researchers have fixed salaries (noisy, hard-to-attribute output) while salespeople work on commission (noisy but attributable output).

---

### Information asymmetry in AI products

**Recommendation systems as principal-agent problems:**

Users (principals) want a recommendation system (agent) to find content they will genuinely enjoy. The agent observes clicks and watch time, but not genuine satisfaction. Moral hazard: the agent optimises for measurable proxies (engagement) rather than true user welfare, since true welfare is unobservable. This is Goodhart's Law applied to recommendation systems.

**Hiring algorithms:**

Firms cannot observe candidates' true ability before hiring. They observe credentials (degrees, past titles) which are partially informative proxies. Algorithmic hiring uses these observable proxies as signals — but proxies can be gamed (candidates with the same ability but better credentials win). The adverse selection problem: candidates who are skilled at credential presentation but not at the job get selected.

**API pricing and information asymmetry:**

A SaaS API charges per call. The buyer knows their usage profile (bursty vs. steady, peak demand) better than the seller. At a uniform per-call price, high-usage customers will switch to alternatives; the seller is left with low-value customers. This is the insurance adverse selection problem applied to SaaS. Solution: tiered pricing (screening) — design a menu of plans that sorts customers into high/low usage tiers by self-selection.

**Venture capital and adverse selection:**

Startups know their own quality better than VCs. High-quality startups will take funding under reasonable terms; distressed startups will accept poor terms because they have no alternative. At any given valuation, the pool of startups willing to accept skews toward lower quality. VCs respond with due diligence, staged financing, and board control rights — all mechanisms to reduce information asymmetry.

---

## The Formal Picture

**Akerlof's lemons model — the general logic:**

- Cars range in quality from worst to best. Sellers know their car's quality; buyers do not.
- Sellers only sell if the price meets their minimum. Higher-quality sellers have higher minimums.
- At any price, only sellers whose minimum is *at or below* the price will offer their car.
- So the average quality of cars available at a given price is always *below* what buyers would need to see to willingly pay that price.
- Market equilibrium requires that buyers expect enough value to pay the going price. In Akerlof's model, this condition fails at every positive price — so the market collapses entirely.

**Principal-Agent problem — the key trade-off:**

The agent (employee, contractor) chooses how hard to work. Their output is affected by both effort and random noise (luck, market conditions). The employer observes the output but not the effort itself.

Under a simple pay-for-performance contract (base wage + commission on results):
- A higher commission rate raises effort — but also puts more risk on the agent.
- The optimal commission rate balances two forces:
  - **Higher commission** when the agent is less risk-averse, or when output is a more reliable signal of effort (less noise).
  - **Lower commission** when the agent is very risk-averse, or when output is noisy (so performance pay exposes them to a lot of uncontrollable risk).

The result: noisy work gets flat salaries (professors, researchers); attributable work gets commissions (sales, piece-rate manufacturing).

---

## Where It Breaks / What It Is Not

**Not all adverse selection is fatal.** Akerlof's result requires that buyers cannot distinguish types at all. In practice, partial signals exist (car history, brand reputation, warranties). Markets can survive with moderate information asymmetry if good types can signal (Day 28) or bad types can be screened out (Day 29).

**Moral hazard requires unobservability, not unverifiability.** If the principal can *observe* but not *verify* the agent's effort (can see it, but can't prove it in court), contracts can still condition on observable effort through relational contracts and reputation. This is the repeated-game solution applied to principal-agent problems.

**Information asymmetry is not always the seller's advantage.** In some markets, the buyer has private information (in insurance: the buyer knows their health; in labour markets: the worker knows their ability). The direction of asymmetry matters for who suffers from adverse selection.

**Data can resolve but also create information asymmetry.** Algorithmic credit scoring reduces lenders' information asymmetry about borrower risk — but creates new asymmetry (the algorithm's predictions are private to the lender). The borrower doesn't know why they were rejected. Information asymmetry is not static; new technologies redistribute it.

---

## Try It Yourself

**Exercise 1 — Identify the asymmetry type (5 min)**

Classify each as adverse selection, moral hazard, or signalling (preview of Day 28):

a) A company offers a "money-back guarantee" on a software product.
b) A borrower takes on more risk with a project because the bank bears the downside if the project fails.
c) A job posting requires a degree from a top university for a role that doesn't use any university-specific knowledge.
d) After buying comprehensive car insurance, a driver parks in a high-crime neighbourhood they'd normally avoid.

<details>
<summary>Answers</summary>

**a) Signalling.** The guarantee is a costly signal of quality — low-quality producers can't afford to offer it because too many customers would claim refunds. It reveals the seller's private information (product quality) credibly.

**b) Moral hazard.** After the contract is signed (loan agreed), the borrower takes a hidden action (risk level) that the bank cannot observe. The asymmetric payoff structure (borrower keeps upside, bank bears downside) creates incentive to take excessive risk.

**c) Adverse selection / signalling.** The degree requirement is an attempt to screen out low-ability candidates before hiring. Whether it's "adverse selection response" (firm screening) or "signalling" (candidates signalling) depends on perspective. The degree is a signal sent by candidates; the firm uses it to screen. Day 28 will distinguish these carefully.

**d) Moral hazard.** The insurance contract is already signed (adverse selection is before contracting; this is after). Post-contract, the driver changes their behaviour (hidden action: parking location) in a way the insurer cannot monitor.

</details>

---

**Exercise 2 — The lemons calculation (10 min)**

Used laptops: quality q is uniform on [0, 1]. Sellers value their laptop at $q × 800. Buyers value a laptop of quality q at $q × 1,100 (20% more than seller).

a) At a price of $500, what quality range of sellers will sell?
b) What is the expected quality of laptops offered at $500?
c) What is the buyer's expected value at $500?
d) Will buyers pay $500? Does the market clear?
e) Find the price p* (if any) at which the market clears.

<details>
<summary>Worked answer</summary>

**a)** Sellers sell iff p ≥ 800q → q ≤ 500/800 = 0.625. Sellers with quality ≤ 0.625 sell.

**b)** E[q | q ≤ 0.625] = 0.625/2 = 0.3125.

**c)** Buyer's expected value = 1,100 × 0.3125 = $343.75.

**d)** Buyer's expected value ($343.75) < price ($500). Buyers won't pay $500. Market doesn't clear at $500.

**e)** For the market to clear at price p: buyer's expected value ≥ p.

Buyer's expected value at price p:
- Sellers with q ≤ p/800 sell.
- E[q | sell] = (p/800)/2 = p/1600.
- Buyer value = 1,100 × p/1600 = 1100p/1600 = 0.6875p.

For market to clear: 0.6875p ≥ p → 0.6875 ≥ 1. **This is never true.**

The market collapses entirely — no positive price can clear. Even though gains from trade exist (buyers value cars 37.5% more than sellers), adverse selection destroys all trade.

</details>

---

**Exercise 3 — Design the fix (15 min)**

You are building a two-sided marketplace for freelance AI engineers. Clients cannot observe engineer quality before hiring; engineers know their own skill level. The top 30% of engineers are "expert" (worth $200/hr to clients); the bottom 70% are "adequate" (worth $80/hr).

a) Without any screening mechanism, what happens to the market if clients offer a single hourly rate? Show the adverse selection logic.

b) Propose three distinct mechanisms the platform could implement to reduce information asymmetry. For each, explain whether it targets adverse selection, moral hazard, or both.

c) One mechanism is "portfolio review" — clients can see engineers' past work. This costs the engineer time to prepare (20 hours) but increases their hire probability. Is this adverse selection, moral hazard, or signalling? Under what condition does it successfully separate expert from adequate engineers?

---

## Connect It Back

Information asymmetry is the root cause of some of the most important market failures in modern economies — health insurance spirals, financial crises, labour market inefficiencies. The Akerlof model is simple but its logic is pervasive: whenever quality is private information and prices must be uniform, selection effects push bad types into the market and drive good types out.

The two canonical responses are **signalling** (Day 28) — the informed party reveals their type through a credible costly action — and **screening** (Day 29) — the uninformed party designs a menu of options that induces self-selection into types. These are the two sides of Module 3's information economics.

**The question you should be able to answer now:**
*Why does the used car market fail in Akerlof's model even though there are gains from trade — and what does this tell you about the relationship between information structure and market efficiency?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Akerlof, George A. "The Market for Lemons." *Quarterly Journal of Economics*, 84(3):488–500, 1970. Read all 13 pages. The car market example (Section I), the formal model (Section II), and the applications to insurance, credit, and labour markets (Section III) are all essential. This is one of the ten most important papers in economics.

**If you want the deep version:**

1. Stiglitz, Joseph E. "Information and the Change in the Paradigm in Economics." Nobel Prize Lecture, December 8, 2001. Available at nobelprize.org. — Stiglitz's Nobel lecture surveys 40 years of information economics: adverse selection, moral hazard, signalling, and the policy implications. Non-technical, sweeping, and insightful. Read the first half (pages 1–20).

2. Holmström, Bengt. "Moral Hazard and Observability." *Bell Journal of Economics*, 10(1):74–91, 1979. — The foundational paper on principal-agent theory. Derives the optimal contract under moral hazard and the informativeness principle: any signal correlated with the agent's effort should be included in the contract. Read Sections 1–3.

3. Tirole, Jean. *The Theory of Industrial Organization*. MIT Press, 1988. Chapter 1 ("Principal-Agent Theory"). — The comprehensive treatment of information economics in industrial organisation. Dense but thorough. Use as a reference for the formal models underlying Days 27–29.


---

← [Day 26 — Auctions](day-26-auctions) &nbsp;|&nbsp; [Day 28 — Signalling →](day-28-signalling)
