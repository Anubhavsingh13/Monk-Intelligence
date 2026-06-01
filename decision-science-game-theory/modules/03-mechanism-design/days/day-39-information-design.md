*[Decision Science & Game Theory](../../../../README.md) · [Module 3 — Mechanism Design & Advanced Structures](../3-overview.md) · Day 39 of 65*

# Day 39 — Information Design & Bayesian Persuasion

> **Today's one idea:** A sender who controls what information to reveal can strategically design their disclosure policy to shift a receiver's beliefs and actions — and the optimal policy often involves selective, partial revelation rather than full transparency or full concealment.
> **Reading time:** ~35 min · **Prereqs:** [Day 5](../../01-foundations/days/day-05-bayesian-updating.md), [Day 28](day-28-signalling.md), [Day 30](day-30-mechanism-design.md)
> **Primary source for today:** Kamenica, Emir & Gentzkow, Matthew. "Bayesian Persuasion." *American Economic Review*, 101(6):2590–2615, 2011.

---

## The Hook

A pharmaceutical company wants to get their drug approved by a regulator. The drug is effective for some conditions, harmful for others. The company runs clinical trials — and crucially, *decides which results to submit*.

The regulator updates their belief about the drug based on the submitted evidence, then approves or rejects. The company can't lie (the submitted trials are real) — but they can selectively disclose. What is the optimal disclosure policy?

This is the problem of **Bayesian Persuasion**, formalised by Kamenica and Gentzkow in 2011. The sender (company) designs an information structure — a mapping from the true state of the world to a signal — to maximise their payoff. The receiver (regulator) updates beliefs rationally on the signal and acts in their own interest.

The result is counterintuitive: the sender can sometimes do better by revealing some information than by concealing everything. Selective, designed disclosure is a strategic tool. And the optimal design is solved by a geometric argument — the "concavification" of the sender's payoff function.

---

## Building the Intuition

### The basic setup

**Players:** Sender and Receiver.

**State:** A fact about the world — quality of a drug, creditworthiness of a borrower — that the sender observes but the receiver does not. The receiver starts with a prior belief about how likely each state is.

**Information structure (signal):** A rule the sender commits to in advance: "If the state is X, I will send signal Y with this probability." The sender designs this rule before seeing the actual state. Once the state is realised, the matching signal is sent to the receiver.

**Receiver's action:** Upon seeing the signal, the receiver applies Bayes' rule to update their belief, then takes the action that looks best given that updated belief.

**Sender's goal:** Design the signal rule to maximise the sender's own expected payoff, knowing the receiver will act rationally on whatever signal they receive.

---

### Why selective disclosure can help the sender

**Example — Drug approval:**

State ω ∈ {good, bad}. Prior: μ₀(good) = 0.4, μ₀(bad) = 0.6 (drug is more likely bad).

Regulator approves iff posterior belief P(good|signal) ≥ 0.5.

Sender (company) wants approval. Under full disclosure:
- Regulator learns the true state.
- Approves only when ω = good.
- P(approval) = 0.4.

Under full concealment (no signal):
- Regulator keeps prior belief 0.4 < 0.5.
- Rejects always.
- P(approval) = 0.

Under optimal persuasion — can the company do better than 0.4?

**A clever disclosure policy:**

Design signal s ∈ {pass, fail}:
- When ω = good: always send "pass."
- When ω = bad: send "pass" with probability p, "fail" with probability (1−p).

What is the posterior when the receiver sees "pass"?

```
P(good | pass) = P(pass | good) × P(good) / P(pass)
              = 1 × 0.4 / (1 × 0.4 + p × 0.6)
              = 0.4 / (0.4 + 0.6p)
```

For approval, need P(good | pass) ≥ 0.5:
```
0.4 / (0.4 + 0.6p) ≥ 0.5
0.4 ≥ 0.2 + 0.3p
0.2 ≥ 0.3p
p ≤ 2/3
```

Set p = 2/3 (maximum "pass" rate from bad state while still getting approval):

P(approval) = P(pass) = 0.4 + 0.6 × (2/3) = 0.4 + 0.4 = **0.8**.

The company achieves 80% approval probability — double the full-disclosure rate of 40%. They achieve this by mixing in some good news about bad drugs just enough to keep the posterior at exactly the approval threshold.

---

### The concavification argument

Kamenica and Gentzkow found a beautiful geometric characterisation of the optimal information structure.

**Setup:**

Let the state be a binary probability μ = P(ω = ω₁). The sender's value function V(μ) is their expected payoff when the receiver's posterior is μ.

Under the prior μ₀, the sender gets V(μ₀) from no disclosure. Under full disclosure, they get E[V(ω)] — the average of V at the realised state.

**The sender can do better:**

By designing the signal, the sender can achieve any payoff in the **concave hull** of V at μ₀. The optimal information structure achieves the value of the **concavification** of V evaluated at μ₀ — the smallest concave function that everywhere exceeds V.

**Geometric interpretation:**

```
V(μ)
  │           ●  concavification
  │         /   \
  │       /       \    ← V(μ): actual payoff
  │     /    ●      \
  │   /               ●
  │ ●
  └─────────────────── μ
    0      μ₀       1
```

The concavification is achieved by a signal that induces two posteriors — the "good news" and "bad news" posteriors — such that the prior μ₀ is a weighted average of them. The sender splits the state into belief "regions" to maximise their expected payoff.

**Optimal persuasion is:**
1. **Never beneficial if V is already concave** — concavification = V, no improvement possible.
2. **Always beneficial when V is convex** — the sender can gain by generating extreme posteriors.
3. **The full-concavification value** is the maximum any information structure can achieve.

---

### When does persuasion help?

**Conditions for persuasion to help the sender:**

1. **V is not concave at μ₀.** If V is concave everywhere, no information structure does better than no disclosure.
2. **Receiver acts on information.** If the receiver ignores the signal, persuasion has no effect.
3. **Sender can commit to the information structure.** If the receiver doesn't trust that the sender follows the announced policy, they won't update on the signal.

**When does full transparency beat selective disclosure?**

If the sender's interests are aligned with the receiver's (they both want the "good" outcome), full transparency is usually optimal — there's no reason to conceal. Strategic disclosure arises when interests diverge.

---

### Information design in practice

**Stress tests and financial disclosure:**

Bank stress tests (conducted by regulators) are an information design problem from the regulator's perspective. The regulator chooses *how much* to reveal about individual bank results. Revealing too little: investors can't distinguish solvent from insolvent banks (all banks face borrowing pressure). Revealing too much: solvent banks with minor weaknesses face bank runs based on the disclosed shortfall. Optimal disclosure reveals enough to restore confidence without triggering runs — exactly the persuasion framework.

**Product reviews and rating display:**

A platform that displays ratings is an information designer. Showing all reviews vs. showing a curated subset vs. showing only aggregate scores are different information structures. Each induces different buyer beliefs and purchasing decisions. The platform's optimal design depends on whether it wants to maximise short-term sales (show best reviews) or long-term trust (show full distribution).

**AI model documentation (model cards):**

When an AI company publishes a model card, they choose what benchmarks to report, which failure modes to disclose, and how to frame capability claims. This is information design — the documentation is a signal from the company (sender) to developers and regulators (receivers). The optimal disclosure policy depends on the company's objectives (build trust? avoid regulation? win enterprise contracts?) and the receivers' decision thresholds.

**A/B test disclosure:**

When a product team reports A/B test results to leadership, they choose which metrics to highlight (conversion rate, engagement, LTV) and which to downplay (churn impact, edge-case failures). This is informal information design — the presenter shapes the posterior beliefs of decision-makers. Designing reporting templates and result dashboards is, partly, a persuasion problem.

**Recommendation system transparency:**

A recommendation algorithm that shows users why they're being recommended something ("Because you watched X") is engaging in information design. The explanation changes user beliefs about the system's values and builds trust. Platforms choose explanations strategically — revealing enough to feel transparent without exposing the full recommendation logic.

---

## The Formal Picture

**Information structure:**

An information structure is a rule the sender commits to in advance: for each possible true state of the world, it specifies a probability distribution over signals. When the true state is realised, the sender draws a signal from the matching distribution and sends it to the receiver. The receiver never sees the state directly — only the signal.

**Bayes' rule (receiver updates):**

The receiver uses Bayes' rule (Day 5) to update their belief about the state after seeing the signal. The updated belief — the posterior — tells the receiver how likely each state is, given the signal just received. The receiver then takes whichever action looks best given that posterior.

**Sender's expected payoff:**

The sender's payoff depends on what the receiver does, which depends on what signal the receiver sees, which depends on what the true state turned out to be. Averaging over all possible states and signals — weighted by their probabilities — gives the sender's expected payoff from any given disclosure policy.

**Kamenica-Gentzkow Theorem:**

The best payoff the sender can ever achieve — across all possible disclosure policies — equals the "concavification" of the sender's payoff function, evaluated at the prior belief.

The concavification is the ceiling you get by drawing the tightest concave curve that lies above the sender's payoff function everywhere. At the prior belief, this ceiling is the maximum the sender can reach by choosing signals cleverly.

**Corollary:** Persuasion helps the sender if and only if the sender's payoff function is not already concave at the prior belief — that is, if there is any upward-curving region the sender can exploit by splitting the receiver's beliefs strategically.

---

## Where It Breaks / What It Is Not

**Commitment is essential.** The sender must credibly commit to the information structure before the state is realised. If the receiver suspects the sender can deviate from the announced structure, they won't update rationally. In practice, commitment requires verifiable mechanisms (mandatory disclosure rules, independent audits, cryptographic commitments).

**Multiple receivers complicate things.** The single-receiver framework assumes the signal is private. With multiple receivers who may interact (e.g., investors deciding whether to run on a bank), the analysis requires game-theoretic equilibrium among receivers — dramatically more complex.

**Dynamic persuasion.** In multi-stage settings, the receiver updates beliefs over multiple periods and the sender faces a dynamic persuasion problem. The optimal strategy may involve building credibility early to exploit later — much harder than the static case.

**Hard vs. soft information.** Kamenica-Gentzkow assumes the sender cannot lie — only design which *true* information to reveal. If the sender can fabricate signals, cheap talk models apply instead. The distinction between "lying" and "selective disclosure" is important both theoretically and ethically.

**Not all information design is ethical.** Selective disclosure that exploits receiver trust is manipulative, not merely strategic. The persuasion framework is neutral — it applies equally to well-designed stress tests (socially beneficial) and misleading drug trials (socially harmful). The ethics depends on whose welfare the design serves.

---

## Try It Yourself

**Exercise 1 — Optimal persuasion calculation (10 min)**

A seller wants to convince a buyer to purchase a product. State ω ∈ {high quality, low quality} with prior P(high) = 0.3. Buyer purchases iff posterior P(high|signal) ≥ 0.5. Seller's payoff: +1 if purchase, 0 if not.

a) Under full disclosure, what is the seller's expected payoff?
b) Design a signal that maximises P(purchase). What is the maximum probability of a sale?
c) Verify your signal using the Bayes' rule update.

<details>
<summary>Worked answer</summary>

**a)** Full disclosure: buyer learns true quality. Buys only when ω = high (posterior = 1 ≥ 0.5). P(purchase) = P(high) = **0.3**.

**b)** Use the persuasion design: signal s ∈ {buy, don't buy}.

Send "buy" for sure when ω = high. Send "buy" with probability p when ω = low.

Posterior when receiver sees "buy":
```
P(high | buy) = 0.3 / (0.3 + 0.7p)
```

Need ≥ 0.5:
```
0.3 / (0.3 + 0.7p) = 0.5
0.3 = 0.15 + 0.35p
0.15 = 0.35p
p = 3/7
```

P(purchase) = P(buy) = 0.3 + 0.7 × (3/7) = 0.3 + 0.3 = **0.6**.

**c)** At p = 3/7:
```
P(high | buy) = 0.3 / (0.3 + 0.7 × 3/7) = 0.3 / (0.3 + 0.3) = 0.3/0.6 = 0.5 ✓
```

Posterior exactly at the buyer's purchase threshold. Seller achieves 60% purchase rate vs. 30% under full disclosure.

</details>

---

**Exercise 2 — Concavification intuition (10 min)**

Sender's value function V(μ) for μ ∈ [0,1]:

```
V(μ) = 0 for μ < 0.5
V(μ) = 1 for μ ≥ 0.5
```

(Sender gets payoff 1 iff receiver believes the state is good with probability ≥ 1/2.)

Prior μ₀ = 0.3.

a) Without any information design, what is the sender's payoff?
b) Sketch V(μ). Is V concave at μ₀ = 0.3?
c) What is the concavification co(V) at μ₀ = 0.3?
d) What is the optimal information structure, and what is the sender's maximum payoff?

<details>
<summary>Worked answer</summary>

**a)** μ₀ = 0.3 < 0.5 → V(μ₀) = **0**.

**b)** V is a step function: 0 for μ < 0.5, 1 for μ ≥ 0.5. This is convex (step functions are neither convex nor concave in the standard sense — but the relevant notion here is: V(0.3) = 0 < λ × V(0) + (1-λ) × V(1) for appropriate λ, meaning the chord connecting (0, V(0)) to (1, V(1)) lies above V at μ₀ = 0.3). **V is not concave at μ₀.**

**c)** Concavification: The straight line from (0, 0) to (1, 1) evaluates at μ₀ = 0.3 as co(V)(0.3) = **0.3**.

**d)** Optimal information structure: Send two signals. One signal induces posterior μ₁ = 0 (certain bad state); other induces posterior μ₂ = 0.5 (threshold posterior triggering purchase).

From μ₀ = 0.3 = α × 0 + (1-α) × 0.5 → 0.3 = 0.5(1-α) → α = 0.4.

So: signal 1 (posterior 0) with probability 0.4; signal 2 (posterior 0.5) with probability 0.6.

Sender's payoff: (0.4)(0) + (0.6)(1) = **0.6** ≠ 0.3.

Wait — the concavification at 0.3 is 0.3, but optimal payoff is 0.6? Let me recheck: co(V)(0.3) uses the line from V(0)=0 to V(0.5)=1 (the first "jump"), not V(0) to V(1). The concavification at μ₀=0.3 = 0.3/0.5 × 1 = 0.6. **co(V)(0.3) = 0.6.** ✓

Maximum sender payoff = **0.6**.

</details>

---

**Exercise 3 — Design an information policy for your context (15 min)**

Choose a disclosure decision you face or have observed: reporting metrics to stakeholders, publishing model evaluation results, sharing user data insights with partners, presenting A/B test results to leadership.

a) Who is the sender? Who is the receiver? What is the "state" (the private information)?
b) What is the receiver's action threshold (what belief makes them take the action the sender wants)?
c) What is the sender's payoff under full disclosure? Under no disclosure?
d) Design a selective disclosure policy that improves the sender's payoff. Is this ethically acceptable in your context?

---

## Connect It Back

Information design is the "soft" form of mechanism design: instead of designing incentives (transfers, rules), the sender designs beliefs (information). The Kamenica-Gentzkow framework gives a precise, computable answer to when and how much selective disclosure helps — and the concavification geometry shows exactly what the optimal policy looks like.

Tomorrow (Day 40) closes Module 3 with a full Rest & Synthesise session — consolidating Days 28–39 before Module 4 applies everything to AI products.

**The question you should be able to answer now:**
*Why can a sender sometimes achieve a higher payoff by selectively disclosing information than by either fully revealing or fully concealing — and what geometric condition on the sender's payoff function determines whether persuasion helps?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Kamenica, Emir & Gentzkow, Matthew. "Bayesian Persuasion." *American Economic Review*, 101(6):2590–2615, 2011. Read Sections I–III (pages 2590–2603). Section I introduces the model; Section II derives the concavification characterisation; Section III gives applications. The persuasion example and the geometric argument are both in these sections.

**If you want the deep version:**

1. Bergemann, Dirk & Morris, Stephen. "Information Design: A Unified Perspective." *Journal of Economic Literature*, 57(1):44–95, 2019. — A comprehensive survey of information design from two of its leading researchers. Sections 1–3 cover Bayesian persuasion and its extensions to multiple receivers and dynamic settings. The most useful synthesis of the field for someone coming from the Kamenica-Gentzkow paper.

2. Rayo, Luis & Segal, Ilya. "Optimal Information Disclosure." *Journal of Political Economy*, 118(5):949–987, 2010. — An independent development of optimal disclosure, with emphasis on the value of coarse signals. Complementary to Kamenica-Gentzkow; useful for understanding why simple signals (binary pass/fail) are often optimal. Read Sections 1–2.

3. Taneva, Ina A. "Information Design." *American Economic Journal: Microeconomics*, 11(4):151–185, 2019. — A tutorial-style treatment of information design with worked examples and the connection to correlated equilibrium (Day 37). Particularly good on the relationship between Bayesian persuasion and mechanism design.


---

← [Day 38 — Price of Anarchy](day-38-price-of-anarchy) &nbsp;|&nbsp; [Day 40 — Rest & Synthesise →](day-40-rest-synthesise)
