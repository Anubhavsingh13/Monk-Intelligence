*[Decision Science & Game Theory](../../../../README.md) · [Module 2 — Game Theory Fundamentals](../2-overview.md) · Day 26 of 65*

# Day 26 — Auctions & the Revenue Equivalence Theorem

> **Today's one idea:** Four standard auction formats look completely different, but under symmetric bidder assumptions they all produce the same expected revenue for the seller — and understanding *why* reveals the deep logic of how private information determines outcomes in competitive settings.
> **Reading time:** ~40 min · **Prereqs:** [Day 15](day-15-dominant-strategies.md), [Day 16](day-16-nash-equilibrium.md), [Day 5](../../01-foundations/days/day-05-bayesian-updating.md)
> **Primary source for today:** Vickrey, William. "Counterspeculation, Auctions, and Competitive Sealed Tenders." *Journal of Finance*, 16(1):8–37, 1961. Also: Krishna, Vijay. *Auction Theory*, 2nd ed., Academic Press, 2010. Chapter 2.

---

## The Hook

In 1961, William Vickrey — an economist at Columbia — published a paper about auctions that at the time attracted almost no attention. He described a peculiar auction format: sealed-bid, where the highest bidder wins but pays the **second-highest bid**, not their own.

This seems like a bad idea from the seller's perspective. Why would you charge less than the winner's actual bid?

Vickrey's insight: because in this format, every bidder's dominant strategy is to bid their true value. No gaming, no lowballing, no second-guessing. The mechanism extracts honest information from participants, and the outcome is efficient (the person who values it most gets it). Thirty years later, this mechanism was redesigned into the Google and Yahoo ad auctions that now allocate billions of dollars of advertising daily. In 1996, Vickrey won the Nobel Prize in Economics.

Today you understand why the Vickrey auction works, how it relates to other standard auction formats, and the Revenue Equivalence Theorem — one of the most surprising results in all of economics.

---

## Building the Intuition

### The four standard auction formats

**1. English auction (open ascending-price):**
Start at a low price. Bidders call out higher bids. Last remaining bidder wins, pays their bid. *You observe your rivals' bids in real time.*

**2. Dutch auction (open descending-price):**
Start at a high price. Price drops continuously. First bidder to call "mine!" wins, pays the current price. *Speed and nerve — you decide to bid before seeing others act.*

**3. First-price sealed-bid (FPSB):**
Each bidder submits a sealed bid. Highest bid wins, pays their own bid. *You commit to a number without knowing others' bids.*

**4. Second-price sealed-bid (Vickrey):**
Each bidder submits a sealed bid. Highest bid wins, pays the second-highest bid. *Same commitment, different payment rule.*

These four look very different. Intuition says they'd generate different revenues for the seller. Vickrey's mathematics — later systematised by Myerson (1981) — says intuition is wrong.

---

### Why the Vickrey auction is brilliant: dominant strategy truthfulness

**Setup:** Your private value for an item is v. You don't know others' values. What should you bid?

**If you bid b > v (overbid):**
- If you win by overbidding: you might pay more than your value. You win an auction you "shouldn't" have won — negative gain.
- If you would have won anyway with b = v: no change.
- Overbidding can only hurt you.

**If you bid b < v (underbid):**
- You might lose when you would have won at true value.
- If you win: you still pay the second-highest bid — your bid didn't change the payment.
- Underbidding can only cause you to lose a profitable auction.

**Bidding b = v dominates both:**

| Your bid | Second-highest bid (call it b₂) | Outcome |
|----------|----------------------------------|---------|
| v        | b₂ < v: You win, pay b₂ < v. Profit: v − b₂ > 0 ✓ |
| v        | b₂ > v: You lose. Profit: 0 (no loss) ✓ |
| b < v    | b < b₂ < v: You lose when you should win. Profit: 0 instead of v − b₂ > 0 ✗ |
| b > v    | v < b₂ < b: You win and pay b₂ > v. Loss ✗ |

Bidding your true value is a **weakly dominant strategy** — it's optimal regardless of what others bid. You never have a reason to deviate. This is mechanism design's holy grail: **incentive compatibility**. The mechanism makes honesty the best policy.

---

### Why the first-price auction is harder: strategic shading

In a first-price auction, you pay your own bid. Should you bid your true value v?

No. If you win and pay v, you have zero profit. You should **shade your bid below v** — bid b < v — to capture some surplus if you win.

But how much to shade? This depends on:
1. How many other bidders are there?
2. What do you believe about their values?

**The symmetric equilibrium (uniform distribution example):**

Suppose n bidders each draw values uniformly from [0, 1], independently. The symmetric Nash equilibrium bidding strategy for a bidder with value v is:

```
b*(v) = v × (n−1)/n
```

With 2 bidders: bid b*(v) = v/2. With 10 bidders: bid b*(v) = 9v/10. With more competition, you shade less (because the risk of losing is higher).

This is a Nash equilibrium: given that all others bid (n−1)/n × their value, your best response is to do the same. No one has a profitable deviation.

---

### The Revenue Equivalence Theorem

Here is the striking result. Under four assumptions:
1. Bidders are risk-neutral.
2. Values are drawn independently from the same distribution (symmetric).
3. The bidder with the highest value wins.
4. The bidder with the lowest possible value gets zero expected surplus.

**The Revenue Equivalence Theorem (Vickrey 1961, Riley & Samuelson 1981, Myerson 1981):**

> All auction formats satisfying these four conditions yield the **same expected revenue** to the seller.

The English auction, Dutch auction, first-price sealed-bid, and Vickrey auction — despite looking completely different — all extract the same expected payment from bidders in equilibrium.

**Why?** Because the equilibrium bidding strategies adjust to offset the different payment rules. In the Vickrey auction, you bid truthfully but pay less (second-highest bid). In the first-price auction, you shade your bid but pay your own bid. The shading and the discounting cancel out in expectation — total expected revenue is identical.

**The example:**

Two bidders, values uniform on [0, 1].

*Vickrey auction:* Both bid truthfully. Whoever has the higher value wins and pays the other's value. Expected payment by the winner: E[second-highest value | two draws from U[0,1]] = 1/3.

*First-price auction:* Both bid v/2 (equilibrium with n=2). Winner pays their bid. Expected payment = E[(v/2) | v is the highest of two draws] = E[max(v₁, v₂)/2] = E[max]/2 = (2/3)/2 = 1/3.

Same expected revenue: 1/3. The theorem holds precisely.

---

### When does Revenue Equivalence break down?

The theorem's assumptions are its limits. Violate any one and revenues diverge:

**Risk aversion:** Risk-averse bidders bid higher in first-price auctions (to reduce the risk of losing) than the risk-neutral equilibrium predicts. First-price auctions generate *more* revenue than Vickrey when bidders are risk-averse.

**Correlated values:** If bidders' values are positively correlated (all see a common signal — e.g., professional appraisers valuing an estate), the English auction generates more revenue. In the English auction, you can infer the common signal from others' bids and thus bid more aggressively when others are bidding high. This is the **linkage principle** (Milgrom & Weber 1982): formats that reveal more information generate more revenue when values are correlated.

**Asymmetric bidders:** When one bidder is "stronger" (higher expected value), first-price and Vickrey auctions generate different revenues. The Vickrey format may favour the stronger bidder excessively.

**Budget constraints:** If some bidders are cash-constrained, their equilibrium bids are distorted. Revenue depends on who has budget constraints.

---

### Google's ad auction: Vickrey in the real world

The largest application of auction theory in history is online advertising. Google's AdWords (now Google Ads) is a generalised second-price (GSP) auction:

- Advertisers bid for keywords.
- The top bidder gets the top ad slot; the second-highest bidder gets the second slot; etc.
- Each bidder pays the bid of the next-lower bidder (not their own bid).

This is a multi-unit Vickrey generalisation. Like Vickrey, it has an equilibrium where bidding (close to) your true value is approximately optimal. Unlike the pure Vickrey auction, the GSP is not exactly incentive-compatible in the multi-slot case — the dominant strategy result breaks down — but in practice bidding close to value is near-optimal.

The mechanism design choice (second-price vs. first-price) had enormous consequences: switching to second-price encouraged honest bidding, which gave Google more accurate information about advertiser valuations, improving allocation efficiency and ultimately revenue. The annual revenue from this auction exceeds $150 billion.

---

## The Formal Picture

**Auction setup:**

- There are n bidders, each with a private value for the item — a value only they know.
- If you win, your payoff = your value − the price you pay. If you lose, your payoff = 0.
- The seller's goal: maximise expected revenue.

**Definition (incentive compatibility):**

A mechanism is incentive-compatible if every bidder's best strategy is to report their true value honestly. The Vickrey auction is incentive-compatible.

**Definition (efficiency):**

An auction is efficient if the item always goes to the bidder who values it most.

**Revenue Equivalence Theorem (plain version):**

For any two auction formats where: (a) the highest-value bidder wins, and (b) the lowest-value bidder gets zero expected profit — the seller earns exactly the same expected revenue from both formats.

**Optimal auction (Myerson 1981):**

When the seller can design any rules, the revenue-maximising mechanism is a second-price auction with a **reserve price** r*. Any bidder whose value is below r* is excluded from winning. The seller deliberately turns away some low-value buyers to earn more from high-value ones. This trades away some efficiency in exchange for higher revenue.

---

### Auctions in AI and technology

**Ad auctions and real-time bidding:**

Every time you load a webpage, a sealed-bid auction (typically first-price since ~2019, when Google switched) runs in ~50ms. The auction allocates the ad slot to the highest bidder. Understanding bidding strategy — how much to shade, how to estimate your value for each impression — is a direct application of auction theory. DSPs (Demand Side Platforms) implement automated bidding strategies derived from these Nash equilibria.

**Cloud resource allocation:**

AWS spot instances use a continuous auction where you name a maximum price; the instance runs as long as the spot price stays below it. This is a variant of a Dutch auction over time. Optimal bidding strategy: set your bid equal to your true value of the compute (the classic Vickrey result) — overbidding doesn't help, underbidding risks premature termination.

**Token sales and NFT auctions:**

Crypto token sales (ICOs, NFT drops) use various auction formats — English auctions, Dutch auctions, first-price sealed-bid, bonding curves. Revenue equivalence helps reason about which format favours which type of participants (risk-averse collectors → first-price generates more; correlated values among collectors → English auction generates more).

**Procurement auctions (reverse auctions):**

Corporations and governments run reverse auctions where suppliers bid to provide services (lowest price wins contract). The same Vickrey logic applies: in a second-price reverse auction, the dominant strategy is to bid your true cost. Many government procurement systems now use reverse auctions to improve competition and reduce procurement costs.

---

## Where It Breaks / What It Is Not

**Collusion:** If bidders can coordinate (bid rings), revenue equivalence breaks catastrophically. Colluding bidders suppress competition; the seller captures far less than any equilibrium prediction. Detecting collusion is a major challenge in public procurement. This connects to Day 21's repeated game — bidders who interact repeatedly can sustain collusion via Grim Trigger.

**The winner's curse:** In common-value auctions (an oil field with uncertain value — all bidders would get the same value if they knew it), the winner is systematically the most optimistic bidder. This "curse" means rational bidders must shade their bids downward to correct for selection bias — if you win, you were probably the most optimistic, and probably wrong. Standard Vickrey/Revenue Equivalence logic does not apply.

**Shill bidding:** In Vickrey auctions, sellers can benefit from inserting fake "shill" bids to push the second-highest bid up. This is illegal in most settings but hard to detect in online auctions. It breaks the dominant-strategy property from the bidder's perspective.

**Budget-constrained bidders:** Large common-value auctions (spectrum auctions, government bonds) often involve bidders with hard budget constraints. The equilibrium strategies change substantially; revenue equivalence fails. Spectrum auction theory required new mechanisms (simultaneous ascending auctions, package auctions) to handle this.

---

## Try It Yourself

**Exercise 1 — Dominant strategy proof (5 min)**

Prove that bidding your true value is weakly dominant in a second-price sealed-bid auction. You should cover all four cases: (i) bid = v, win; (ii) bid = v, lose; (iii) bid b < v, lose when you would have won; (iv) bid b > v, win when you should have lost. (This recaps the intuition section formally.)

<details>
<summary>Answer sketch</summary>

Let m = max bid among all other bidders. Your value is v. You bid b.

- If b = v and m < v: You win, pay m. Payoff = v − m > 0. ✓
- If b = v and m > v: You lose. Payoff = 0. ✓ (No gain from any bid, since paying m > v would give negative payoff.)
- If b < v and m ∈ (b, v): You lose. Payoff = 0. But with b = v you'd win and get v − m > 0. Underbidding loses a profitable auction. ✗
- If b > v and m ∈ (v, b): You win, pay m > v. Payoff = v − m < 0. Overbidding causes a loss. ✗

In every case, deviation from b = v either makes no difference or hurts. Bidding v weakly dominates all other bids. ∎

</details>

---

**Exercise 2 — Revenue equivalence in action (10 min)**

Three bidders have values drawn uniformly from [0, 1]. In the symmetric equilibrium of the first-price auction with n=3, the equilibrium bid function is b*(v) = v × (n−1)/n = 2v/3.

a) What is the expected bid of the winner? (Use the fact that the expected value of the maximum of 3 uniform [0,1] draws is 3/4.)
b) What is the expected revenue in the first-price auction?
c) In the Vickrey auction, the winner pays the second-highest bid. The expected second-highest of 3 draws from U[0,1] is 1/2. What is the expected revenue in the Vickrey auction?
d) Does Revenue Equivalence hold?

<details>
<summary>Worked answer</summary>

**a)** Winner's value ≈ 3/4 (expected maximum of 3 draws). Equilibrium bid: b*(3/4) = 2/3 × 3/4 = 1/2.

**b)** Expected revenue in first-price = expected winning bid = 1/2.

**c)** Expected revenue in Vickrey = expected second-highest bid = 1/2.

**d)** Yes — both produce expected revenue of 1/2. Revenue Equivalence holds. ✓

</details>

---

**Exercise 3 — Auction design for a real problem (15 min)**

You are building a cloud GPU rental marketplace. Buyers (ML researchers, startups) want GPU time; sellers (data centres) want to earn revenue. You must choose an auction format.

a) What is each buyer's "value" in this context? Is it private information or common knowledge?

b) Are buyer values likely to be independent (each researcher has their own deadline/urgency) or correlated (all buyers know the same GPU shortage is coming)?

c) Given your answer to (b), which auction format — English, Dutch, first-price sealed-bid, or Vickrey — would generate more revenue, and why does Revenue Equivalence not apply here?

d) What practical constraint makes the Vickrey format difficult to implement at scale? (Hint: think about what the format requires the platform to verify.)

---

## Connect It Back

Auctions are a self-contained world where game theory and mechanism design meet: the seller designs the rules, bidders respond strategically, and Revenue Equivalence shows that many apparently different rules produce the same outcome. The key to breaking equivalence is breaking one of its assumptions — risk aversion, correlated values, asymmetry, or budget constraints.

Tomorrow (Day 27) enters **information asymmetry** directly — the setting where one party knows something the other doesn't, and strategic revelation (or concealment) becomes the central game. This is the gateway to Module 3's mechanism design, signalling, and screening.

**The question you should be able to answer now:**
*Why is bidding your true value a dominant strategy in the Vickrey auction but NOT in the first-price auction — and what is the equilibrium bid in the first-price auction, and why does it produce the same expected revenue?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Vickrey, William. "Counterspeculation, Auctions, and Competitive Sealed Tenders." *Journal of Finance*, 16(1):8–37, 1961. Read pages 8–19 (Sections I–III). The second-price auction and the dominant strategy result are in Section II. The writing is dense but the key insight takes fewer than three pages to establish.

**If you want the deep version:**

1. Krishna, Vijay. *Auction Theory*, 2nd ed. Academic Press, 2010. Chapters 1 ("Symmetric Independent Private Values") and 2 ("Revenue Equivalence"). — The standard graduate textbook on auction theory. Chapter 1 derives equilibrium bidding strategies for all four formats; Chapter 2 proves Revenue Equivalence with clean notation. Read Sections 1.1–1.3 and 2.1–2.2.

2. Myerson, Roger B. "Optimal Auction Design." *Mathematics of Operations Research*, 6(1):58–73, 1981. — The paper that won Myerson the Nobel Prize (2007). Derives the revenue-maximising mechanism using virtual values and the revelation principle. Sections 1–3 are accessible after today's page. The reserve price formula and its intuition are the key takeaway.

3. Edelman, Benjamin, Ostrovsky, Michael & Schwarz, Michael. "Internet Advertising and the Generalized Second-Price Auction: Selling Billions of Dollars Worth of Keywords." *American Economic Review*, 97(1):242–259, 2007. — The paper that analysed Google's GSP auction. Shows that GSP is not exactly incentive-compatible in the multi-slot case, derives the equilibrium bidding strategies, and estimates the revenue implications. Essential reading if you work in digital advertising or ad tech.


---

← [Day 25 — Bargaining Theory](day-25-bargaining-theory) &nbsp;|&nbsp; [Day 27 — Information Asymmetry →](day-27-information-asymmetry)
