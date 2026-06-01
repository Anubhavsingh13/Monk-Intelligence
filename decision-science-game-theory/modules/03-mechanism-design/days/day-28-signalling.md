*[Decision Science & Game Theory](../../../../README.md) · [Module 3 — Mechanism Design & Advanced Structures](../3-overview.md) · Day 28 of 65*

# Day 28 — Signalling

> **Today's one idea:** A signal is credible only if it is prohibitively costly for the type that doesn't have the underlying quality to mimic — and this cost requirement explains why many apparently wasteful behaviours (expensive degrees, peacock tails, luxury offices) are rational equilibrium signals.
> **Reading time:** ~35 min · **Prereqs:** [Day 27](../../02-game-theory/days/day-27-information-asymmetry.md), [Day 19](../../02-game-theory/days/day-19-sequential-games.md)
> **Primary source for today:** Spence, A. Michael. "Job Market Signalling." *Quarterly Journal of Economics*, 87(3):355–374, 1973.

---

## The Hook

In 1973, Michael Spence asked a subversive question about education: what if a college degree doesn't make you more productive — but people still rationally pursue it?

His argument: suppose there are two types of workers — high-ability (type H) and low-ability (type L). Firms cannot observe type before hiring. High-ability workers want firms to know who they are so they can earn high wages. But they can't simply say "I'm high-ability" — anyone can claim that.

Here is the key observation: getting a degree is *easier* for high-ability workers. They find the coursework less painful, they're less likely to fail, they spend less time on it. The cost of getting a degree is *lower for H types than for L types*.

This creates a separating equilibrium: H types get degrees to signal their type; L types find it too costly to mimic. The degree communicates ability even if it conveys zero actual skills. Firms pay degree-holders higher wages because degrees credibly separate H from L.

Spence won the Nobel Prize in 2001 (shared with Akerlof and Stiglitz) for this idea. The implications are profound and disturbing: the billions spent on higher education might be partly (not entirely — let's be careful) a massive coordination device for sorting workers into types, not a productivity-enhancement mechanism.

---

## Building the Intuition

### What makes a signal credible

From Day 27: the problem is that types want to be distinguished but can't communicate type directly (cheap talk is not credible — anyone can claim to be high quality). A signal must satisfy one condition:

**The single-crossing condition (informally):**

The cost of acquiring the signal must be sufficiently lower for the "good" type than for the "bad" type so that the good type finds it worthwhile to signal and the bad type finds imitation too expensive.

**A concrete setup — Spence's model:**

- Workers: type H (high ability) or type L (low ability). Fraction q are type H.
- Firms cannot observe type before hiring; they observe education level e.
- Productivity: H workers produce y_H; L workers produce y_L. (y_H > y_L.)
- Cost of education e: c_H(e) for H workers, c_L(e) for L workers.
- **Single-crossing condition: c_H(e) < c_L(e) for all e > 0.** Getting educated is cheaper for H types.

**The separating equilibrium:**

There exists an education level e* such that:
- H types choose e = e* and earn wage y_H.
- L types choose e = 0 and earn wage y_L.

For this to be an equilibrium, two conditions must hold:

1. **L types don't mimic:** The gain from mimicking (y_H − y_L) must be less than the cost increase: c_L(e*) − c_H(e*) > 0.

2. **H types don't deviate down:** The gain from dropping education (saving c_H(e*)) must be less than the wage loss (y_H − y_L): c_H(e*) < y_H − y_L.

Combined: any e* satisfying:

```
c_L(e*) > y_H − y_L > c_H(e*)
```

...supports a separating equilibrium. There is typically a range of e* values that work — the model pins down a set of equilibria, not a unique one. The minimum e* that satisfies the L-type non-mimicry condition is the "least wasteful" separating equilibrium.

---

### The geometry of signalling

The indifference curves for H and L types in the (education, wage) space cross each other — this is why the condition is called "single-crossing":

```
Wage
  │
y_H ┤ ─ ─ ─ ─ ─ ─ ─ ─ ─●  H indifference curve
    │              ╱   ●
    │            ╱   ╱
    │          ╱   ╱
y_L ┤ ─ ─ ─ ●   ╱     L indifference curve
    │        ●╱
    │
    └────────────────────── Education
             0        e*
```

At education level e*, H types are indifferent between (e*, y_H) and (0, y_L) — just willing to signal. L types strictly prefer (0, y_L) to (e*, y_H) — cannot profitably mimic.

The signal works *because* the cost curves are ordered. If cost were the same for both types, any level of education that H finds worth acquiring is equally worth acquiring for L. No separating equilibrium exists; only a pooling equilibrium (both types treated as average).

---

### Pooling vs. separating equilibria

**Separating equilibrium:** Different types choose different signals; firms can infer type from signal. Efficient allocation — but involves wasteful signalling cost.

**Pooling equilibrium:** All types choose the same signal; firms cannot distinguish types. No wasteful signalling — but inefficient allocation (H types are paid their pooled average value, not their true productivity).

Which equilibrium emerges depends on the signal costs and the proportion of each type. If most workers are L type, pooling might be stable (firms assume low ability unless signalled). If most workers are H type, the value of signalling for L is low (average wage is already high).

**Both are Nash equilibria.** The signalling game typically has multiple equilibria. Off-equilibrium-path beliefs determine which equilibrium is "selected." This is where Bayesian equilibrium refinements matter (preview of Day 29).

---

### The signalling taxonomy

Spence's model generalises. A credible signal requires:

| Signal | Who sends it | Cost structure | What it credibly communicates |
|--------|-------------|----------------|-------------------------------|
| Education | Job candidate | Easier for high-ability | Underlying ability |
| Warranty | Product seller | Cheaper if product is reliable (fewer claims) | Product quality |
| Dividends | Public firm | Costlier to pay if cash-strapped | Financial health |
| Venture funding | Startup | Founders with better companies give up less equity for same capital | Startup quality |
| Price | Luxury brand | High price sustainable only for brands with loyal repeat buyers | Quality and brand strength |
| Conference talk | Researcher | Easier to give a good talk on good research | Research quality |
| Public commitment (Day 20) | Any player | Costlier to make credible commitment if you'll need to defect | Intention to follow through |

The unifying principle: **whatever you do to signal must cost more for bad types to do than for good types.** Cheap talk — verbal claims with no differential cost — cannot separate types.

---

### Signalling in AI products and careers

**GitHub presence as a signal:**

Open-source contributions, commit history, and starred repositories are signals in the software labour market. The signal is credible because maintaining a high-quality public portfolio is time-consuming — more time-consuming for less-skilled developers, because they produce lower-quality output per hour. Employers use GitHub profiles as a lower-cost screen than extensive technical interviews.

**Product launch quality as a signal:**

A startup that launches with exceptional polish (smooth onboarding, no critical bugs, thoughtful UX) signals engineering quality to both users and future investors. The signal is credible: shipping a polished product is much easier for a highly capable team than for a mediocre one. The launch is the first move in the signalling game with the market.

**Conference talks and writing:**

Publishing detailed technical writing, giving conference talks, maintaining an active blog — these signal expertise. The cost is lower for genuinely knowledgeable people (writing about what you know well is easier than faking depth). The audience learns to discount generic content and weight detailed, original analysis. This is why "thought leadership" writing that is generic and unoriginal fails as a signal — it's too cheap to produce.

**Pricing as a quality signal:**

A SaaS product that charges premium prices signals quality — provided buyers believe that low-quality products can't sustain premium pricing (because customers churn, reviews turn negative, renewals fail). The single-crossing condition: the cost of maintaining premium pricing (the churn and reputational damage from low quality at high prices) is lower for high-quality products.

**Investor lead-round commitment:**

When a Tier 1 VC leads a funding round, it signals to other investors that the startup passed rigorous due diligence. The signal is credible because leading a bad deal is costly to the VC's reputation. The VC's reputation is the "costly signal" — they have more to lose from endorsing poor companies.

---

## The Formal Picture

**Signalling game — the structure:**

1. The Sender (informed party) privately learns their type — H or L.
2. The Sender chooses a signal to send (e.g., how much education to get).
3. The Receiver observes the signal (not the type directly) and responds (e.g., what wage to offer).
4. Both parties get payoffs that depend on type, signal, and the response.

**Perfect Bayesian Equilibrium (PBE):**

Signalling games are solved using Perfect Bayesian Equilibrium — a refinement of Nash equilibrium that also specifies what beliefs the Receiver holds after seeing each possible signal. Four requirements:

1. **Strategies:** The Sender's strategy says what signal to send for each type. The Receiver's strategy says what action to take after each possible signal.
2. **Beliefs:** After seeing any signal, the Receiver has a belief about which type sent it.
3. **Belief consistency:** On paths that are actually followed in equilibrium, beliefs must be updated correctly using Bayes' rule (Day 5).
4. **Sequential rationality:** Every player's strategy must be a best response given their beliefs at every point in the game.

**Separating equilibrium condition:**

A separating equilibrium exists when different types choose different signals, so the Receiver can perfectly infer type. This requires:
- The H type prefers their own signal over the L type's signal (given the responses each triggers).
- The L type prefers their own signal over the H type's signal.

**Spence's single-crossing condition:**

The cost of education rises faster for L types than for H types as education increases. This means the indifference curves of H and L types in (education, wage) space cross exactly once — ensuring that any education level that separates the types does so cleanly without overlap.

---

## Where It Breaks / What It Is Not

**Signalling can be purely wasteful.** If education is *only* a signal (no actual productivity increase), the entire cost of signalling is a deadweight loss. Society spends resources on costly signalling that, in equilibrium, conveys information that could have been conveyed more cheaply. Governments and firms sometimes try to create cheaper sorting mechanisms (standardised tests, free credentialing) to reduce signalling waste.

**The single-crossing condition must hold.** If costs are the same for both types, or if good types have *higher* signalling costs (counterintuitive but possible), the separating equilibrium doesn't exist. In those cases, only pooling or partial separation is possible.

**Multiple equilibria and off-path beliefs.** Signalling games have many equilibria — separating, pooling, and "semi-separating." The intuitive criterion (Cho & Kreps 1987) is a refinement that eliminates unreasonable equilibria by requiring that off-path signals are attributed to the type that would benefit most from deviating. This reduces the equilibrium set but does not always yield uniqueness.

**Signals can be gamed.** Once the labour market knows that degrees signal ability, lower-ability workers will try harder to get degrees — driving up the required signal level and inflating the signalling cost. This credential inflation is the dynamic version of the lemons spiral: the signal cost escalates as more types try to mimic.

---

## Try It Yourself

**Exercise 1 — Identify the signal and check single-crossing (10 min)**

For each, identify whether single-crossing holds (i.e., whether the proposed signal credibly separates types):

a) A restaurant offers a full refund on any meal, no questions asked. (Types: high-quality restaurant vs. low-quality. Signal: money-back guarantee.)

b) A company displays expensive art in their lobby. (Types: financially healthy vs. financially stressed. Signal: lavish office décor.)

c) A software company publishes detailed technical documentation. (Types: well-engineered product vs. poorly engineered. Signal: documentation quality.)

<details>
<summary>Analysis</summary>

**a) Single-crossing holds.** A high-quality restaurant has few refund claims (the guarantee is rarely exercised), so the *expected cost* of the guarantee is low. A low-quality restaurant faces many refund claims, making the guarantee expensive. The signal cost is lower for high-quality → separating equilibrium possible. This is why money-back guarantees are credible quality signals.

**b) Uncertain.** Lavish offices cost the same for healthy and stressed firms in cash terms. But a stressed firm faces a higher *opportunity cost* (cash that could pay debt) and a higher *reputational risk* (being seen as wasteful). If these asymmetric costs are large enough, single-crossing might hold — healthy firms signal with confidence; stressed firms can't afford to be seen as extravagant. In practice, this signal is often gamed (leveraged buyout firms use lavish offices they can't afford), weakening its credibility.

**c) Single-crossing holds strongly.** Producing detailed, accurate technical documentation is much cheaper for a well-engineered product (the engineers know what the system does; documentation matches reality) than for a poorly engineered one (documentation requires describing consistent behaviour that doesn't exist). Documentation quality is a strong signal in developer-facing products.

</details>

---

**Exercise 2 — Find the separating equilibrium (10 min)**

H workers produce $120K/year. L workers produce $60K/year. Fraction 40% are H.

Cost of education e years for H: c_H(e) = 5,000e.
Cost of education e years for L: c_L(e) = 15,000e.

Firms pay w = $120K if they observe e ≥ e*, and w = $60K if e < e*.

a) Find the range of e* that supports a separating equilibrium.
b) What is the "minimum-waste" separating equilibrium?
c) What is the social cost (total signalling waste) at the minimum-waste equilibrium?

<details>
<summary>Worked answer</summary>

**a)** Separating equilibrium requires:

L types don't mimic:
```
$60K ≥ $120K − c_L(e*) = $120K − 15,000e*
15,000e* ≥ $60K
e* ≥ 4
```

H types prefer to signal:
```
$120K − c_H(e*) ≥ $60K
$120K − 5,000e* ≥ $60K
5,000e* ≤ $60K
e* ≤ 12
```

Separating equilibrium range: **e* ∈ [4, 12]**.

**b)** Minimum-waste separating equilibrium: e* = 4 years (least education that still deters L types from mimicking).

**c)** Social cost at e* = 4:
- 40% are H types, each spending c_H(4) = $20K on education.
- Expected signalling cost = 0.4 × $20K = $8K per worker per year.
- This is pure waste — education confers no actual productivity gain in this model.

</details>

---

**Exercise 3 — Career signalling audit (10 min)**

List three signals you currently send (or could send) in your professional context. For each:

a) What type are you claiming to be? (What does the signal communicate about you?)
b) Does single-crossing hold? (Is this signal genuinely cheaper for you to produce if you actually have the underlying quality?)
c) Is there a cheaper signal that would communicate the same thing? If so, why isn't the cheaper signal used?

---

## Connect It Back

Signalling is the informed party's solution to information asymmetry: take a costly observable action that bad types won't find worth mimicking. The key insight — that rational behaviour can be partly about costly waste as a communication device — is deeply counterintuitive and deeply important.

Tomorrow (Day 29) flips the perspective: **screening** is the *uninformed* party's solution. Instead of waiting for the informed party to signal, the uninformed party designs a menu of contracts that makes different types self-select into different options. This is the mechanism design view of information asymmetry — and it is the gateway to Module 3's main content.

**The question you should be able to answer now:**
*Why must a credible signal be costly — and why does it need to be differentially costly for different types rather than just costly in absolute terms?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Spence, A. Michael. "Job Market Signalling." *Quarterly Journal of Economics*, 87(3):355–374, 1973. Read Sections 1–3 (pages 355–367). Section 1 sets up the model; Section 2 derives the separating and pooling equilibria; Section 3 discusses the efficiency implications. Spence's original exposition is remarkably clear — the diagrams in Section 2 are particularly illuminating.

**If you want the deep version:**

1. Spence, A. Michael. "Signaling in Retrospect and the Informational Structure of Markets." *American Economic Review*, 92(3):434–459, 2002. — Spence's Nobel Prize lecture. Reflects on the signalling model 30 years later, covering extensions to product markets, financial signalling, and the empirical evidence on whether education is productive or signalling. Read the first half.

2. Cho, In-Koo & Kreps, David M. "Signaling Games and Stable Equilibria." *Quarterly Journal of Economics*, 102(2):179–221, 1987. — Introduces the "Intuitive Criterion" equilibrium refinement, which eliminates unreasonable pooling equilibria in signalling games. Technically demanding but the key intuition (Section 2) is accessible. Read Section 1 ("Introduction") and Section 2 ("The Criterion") — about 10 pages.

3. Riley, John G. "Silver Signals: Twenty-Five Years of Screening and Signalling." *Journal of Economic Literature*, 39(2):432–478, 2001. — A comprehensive survey of signalling theory across labour, product, and financial markets. Read Sections 1–3 for a bird's-eye view of what 25 years of theory produced. Useful preparation for Day 29's screening content.


---

← [Day 27 — Information Asymmetry](day-27-information-asymmetry) &nbsp;|&nbsp; [Day 29 — Screening & Adverse Selection →](day-29-screening-adverse-selection)
