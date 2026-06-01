*[Decision Science & Game Theory](../../../../README.md) · [Module 4 — Decision Science in AI Products](../4-overview.md) · Day 51 of 65*

# Day 51 — Platform Mechanism Design

**Today's one idea:** A platform is a mechanism designer operating a two- or multi-sided market — every algorithmic choice (ranking, pricing, matching, access) is a mechanism that shapes what equilibrium the market reaches, and the gap between the platform's equilibrium and the social optimum is exactly the Price of Anarchy from Day 38.

**Prerequisites:** Day 33 (network effects), Day 30 (mechanism design), Day 38 (price of anarchy), Day 34 (voting and collective choice)

**Primary sources:** Parker, Geoffrey G., Van Alstyne, Marshall W. & Choudary, Sangeet P. "Platform Revolution." W. W. Norton, 2016. Chapters 5–7. Rochet, Jean-Charles & Tirole, Jean. "Platform Competition in Two-Sided Markets." Journal of the European Economic Association, 1(4):990–1029, 2003.

**Reading time:** ~35 minutes

---

## The Hook

Uber doesn't own cars. Airbnb doesn't own apartments. YouTube doesn't make videos. Amazon Marketplace doesn't manufacture most of what it sells. Yet these platforms extract enormous value from markets they don't directly participate in — and they shape those markets in profound ways through decisions that are almost entirely algorithmic.

The mechanism is the product.

Every algorithmic choice a platform makes — how to rank search results, how to price during demand spikes, which content to recommend, how to display and aggregate reviews, whether to show total price or base price — is a mechanism. It determines which equilibrium the market reaches. It determines who wins and who loses, which sellers get visibility, which content gets amplified, which transactions happen at what price.

Platform executives often describe these choices in the language of engineering: "We improved the algorithm." But from the perspective of the weeks you've spent studying mechanism design, these are design choices about incentives, information revelation, and equilibria. The platform designer is in the same position as a social planner designing an allocation mechanism — except that the platform's objective (maximise revenue, engagement, or growth) may diverge significantly from social welfare.

This is the gap that matters. Understanding how platforms design their mechanisms, and where those mechanisms produce outcomes that are privately optimal but socially suboptimal, is one of the most practically important applications of everything you've studied in Module 3.

---

## Building the Intuition

### 1. The Platform as Mechanism Designer

Start with the contrast between a traditional firm and a platform.

A traditional firm is a linear value chain: it buys inputs, transforms them, and sells outputs. The firm directly participates in production. Its mechanism design problems are internal: how to structure employee incentives (Day 27's principal-agent problem), how to price its products, how to design its supply chain.

A platform facilitates interactions between two or more user groups without participating directly in most of those interactions. Airbnb doesn't stay in its own listings. Amazon Marketplace doesn't buy and sell its own third-party products (its first-party business is a separate operation). The value comes from the interactions between user groups, and the platform extracts a share of that value through transaction fees, data, or attention.

This makes the platform's mechanism design problem external: how do you design rules that produce good outcomes when the agents responding to those rules are not your employees but independent participants — sellers, buyers, drivers, riders, creators, viewers — each with their own objectives?

Day 30 defined a mechanism as a rule set that, when agents respond strategically, produces a desired outcome. This is exactly the platform design problem. The "algorithm" is the mechanism. When Airbnb redesigns its search ranking, it is not tweaking software — it is changing the mechanism that determines which hosts get bookings, which guests see which options, and what total market outcomes emerge.

### 2. Two-Sided Pricing: The Rochet-Tirole Framework

The most fundamental mechanism decision a platform makes is who to charge and how much. This seems like a pricing decision, but it is a mechanism design decision — the pricing structure determines which user groups participate and what equilibrium the market reaches.

Rochet and Tirole (2003) formalised this for two-sided markets. The key insight: in a two-sided market, the platform creates value only if both sides participate. If one side won't come, the other side gets no value. This creates a chicken-and-egg coordination problem that the platform must solve through its pricing mechanism.

The Rochet-Tirole optimal pricing rule says: set prices to internalise the cross-side externalities. Side i generates value for side j every time a member of side i joins or transacts. The platform should price side i below its marginal cost (or even subsidise it) when side i creates large positive externalities for side j, because the profit from capturing those externalities on side j exceeds the loss from subsidising side i.

In practice, platforms subsidise the side that creates more value for the other side, and monetise the side that benefits most from the subsidised side's participation. Three canonical examples:

**Credit cards (Visa, Mastercard):** Cardholders are subsidised with cashback rewards, airline miles, purchase protection, and zero annual fees. Merchants pay interchange fees of 1.5–3% on every transaction. Why? Each cardholder who joins creates value for every merchant by representing a potential buyer. Each merchant who joins creates value for every cardholder by expanding acceptance. But merchants have much lower price elasticity for card acceptance — they cannot easily refuse major cards without losing customers. So the equilibrium has cardholders subsidised and merchants charged.

**Google Search:** Users get the search product free. Advertisers pay for placement. Each user who searches creates value for advertisers by representing a potential customer. The cross-side externality from advertisers to users is present but negative in one sense — users generally don't want ads. Google manages this by ensuring ads are at least somewhat relevant (more on this in the ranking section).

**App stores (Apple App Store, Google Play):** Users get the store free and often get apps free. Developers pay a 30% revenue share. Each developer who builds an app creates value for users; the large user base creates value for developers. The platform extracts its share from the developer side because developers have a lower outside option once they've built platform-specific apps.

The key insight for mechanism design: **the pricing structure IS the mechanism**. Who pays determines who has power. When merchants pay and cardholders are subsidised, the platform is structurally aligned with cardholders' interests, not merchants'. This determines whose complaints get resolved, which regulations the platform lobbies for, and how the mechanism evolves over time.

### 3. Ranking as Mechanism

Every ranking algorithm is a mechanism that determines which sellers, creators, or content providers get exposure. Exposure translates into transactions, revenue, and survival. The platform's ranking mechanism is therefore one of the most consequential allocation decisions in the economy — affecting millions of small businesses, creators, and consumers simultaneously.

The platform's stated goal for ranking is usually "quality" or "relevance." But quality is multidimensional and hard to measure. The platform's actual optimisation target is typically a proxy — click-through rate, conversion rate, review score, session length — that correlates with but is not identical to quality. The Goodhart dynamic (Day 50) applies directly: as soon as sellers understand the ranking algorithm, they optimise for the proxy rather than the underlying quality.

This creates a strategic game between the platform and its sellers. Sellers invest in gaming the ranking mechanism (SEO, keyword stuffing, fake reviews, A+ content optimisation). The platform must make gaming costly and quality-seeking rewarding — this is mechanism design under strategic response. The arms race between sellers' gaming strategies and the platform's mechanism updates is a repeated game (Day 21) that neither side can fully win.

**Auction-based ranking** is one solution. Rather than trying to algorithmically assess quality (which sellers will game), let sellers bid for ranking — and combine bids with quality scores to allocate positions. This is the approach used by Google AdWords and Amazon Sponsored Products.

The mechanism works as follows. Each advertiser submits a bid b_i representing their maximum willingness to pay per click. The platform computes a quality score q_i for each advertiser's ad (measuring relevance, landing page quality, historical CTR). The ranking score is:

```
Score_i = q_i × b_i
```

Positions are allocated in descending order of Score. Payment is determined by a Generalised Second Price (GSP) rule (Day 26): each advertiser pays the minimum amount needed to maintain their current position, not their actual bid. This makes truthful bidding approximately optimal for advertisers with linear value, limits the value of overbidding, and ensures that high-quality ads can outrank high-bid but low-quality ads.

The connection to Day 26's VCG mechanism: pure VCG would be strategyproof but computationally expensive and vulnerable to collusion in the repeated game. GSP is a practical approximation that achieves many of the same properties without the VCG's strategic complexity.

Why multiply by quality score rather than just using bid? Because without the quality adjustment, platforms would allocate positions to whoever bids highest regardless of quality — which degrades user experience, reduces CTR on the winning ads, and ultimately undermines the platform's long-run advertising revenue. The quality score aligns the platform's auction design with user experience and platform profitability simultaneously.

### 4. Review Systems as Information Design

Reviews are information the platform provides to buyers about seller quality. How the platform aggregates and displays this information is an information design problem (Day 39). The platform controls what buyers learn — not by fabricating information, but by choosing which information to reveal, how to aggregate it, and how to present it.

The Kamenica-Gentzkow framework (Day 39): the sender (platform) designs an information revelation rule that, when the receiver (buyer) updates beliefs optimally, produces the sender's preferred behaviour. What does the platform want buyers to do? Transact. Platforms make money on transactions, so they want buyers to be matched to sellers confidently enough to transact, but accurately enough to avoid post-transaction dissatisfaction that leads to disputes, returns, and reputation damage.

This creates a non-trivial information design problem. Show too little positive information and buyers don't transact (lost revenue). Show too much positive information and buyers transact with low-quality sellers, get disappointed, and stop using the platform (long-run reputation damage). The optimal review revelation strategy balances transaction volume against transaction quality.

Practically, this plays out in decisions like: show the overall star average or the full distribution? Show all reviews or only verified purchases? How to handle review manipulation attempts? Each choice has different effects on buyer beliefs and transaction behaviour.

The strategic manipulation problem is the mechanism design challenge. Sellers have incentives to game reviews: purchase fake positive reviews, report competitors' reviews as fake, solicit reviews selectively from satisfied customers. The platform must design the review system so that honest review solicitation is at least weakly dominant over manipulation. This is the revelation principle (Day 30) applied to the review mechanism: design the system so that truth-telling (genuine reviews, genuine quality) is the equilibrium behaviour.

Current review mechanisms largely fail this test. Fake review markets are substantial and persistent. The platforms' attempts to detect and remove fake reviews are themselves gamed. The underlying problem is that the mechanism allows sellers to capture private gains from fake reviews (higher ranking, more sales) without fully bearing the social cost (degraded buyer information, reduced platform trust). This is an externality problem (Day 32) embedded in the review mechanism.

### 5. Congestion, Surge Pricing, and the Price of Anarchy

Platforms face congestion problems at multiple levels. Ride-sharing platforms face matching congestion: too many riders requesting rides at the same time in the same location creates queues that reduce rider satisfaction and driver efficiency. Food delivery platforms face kitchen congestion. Accommodation platforms face seasonal congestion. The platform must design mechanisms to manage these congestion externalities.

Uber's surge pricing is an elegant mechanism in Pigouvian terms (Day 38). When demand for rides exceeds supply of drivers in a given time-and-place, surge pricing imposes a higher price on riders. This serves two functions simultaneously: it suppresses marginal demand (riders who value the trip below the surge price don't request) and it attracts supply (drivers in nearby areas are drawn to high-surge zones). The market clears without queues.

The Price of Anarchy framing (Day 38): in the absence of surge pricing, each driver makes routing decisions to maximise their own expected earnings, without coordinating with other drivers. This produces a Nash equilibrium in which drivers cluster in the same high-demand areas (all chasing the same passengers), creating locally efficient but globally suboptimal supply distribution. Surge pricing is a mechanism that moves the market from the individual-equilibrium outcome (PoA > 1) toward the socially optimal supply distribution.

But surge pricing is imperfect as a mechanism. It's Pigouvian in structure but not in intent — the platform keeps the surplus, rather than redistributing it to drivers or using it to fund alternative transportation. And it can create severe equity effects: during disasters or extreme weather, surge pricing raises prices precisely when demand is most inelastic and for whom paying is most difficult.

The correlated equilibrium connection (Day 37): a platform that coordinates driver dispatch centrally (rather than leaving it to individual driver decisions) can implement a correlated equilibrium that achieves better total welfare than the Nash equilibrium of decentralised driver decision-making. This is what platforms' backend dispatch algorithms attempt — turning a strategic game among independent drivers into a coordination mechanism.

A second congestion effect operates at the market level. Network effects (Day 33) create pressure toward market tipping: as one platform gains more users on both sides, it becomes more valuable to new users on both sides, attracting more users, and so on. Markets with strong network effects tend toward monopoly or duopoly, not because of deliberate exclusion, but because the equilibrium of the strategic entry game produces tipping.

The Price of Anarchy of market tipping: users choosing platforms based on their current network size (a reasonable individual strategy) produces a tipped monopoly equilibrium that may not be socially optimal, even if it is stable. An earlier equilibrium with two competing platforms might have produced better outcomes for users (more innovation, lower prices, more choice) but is unstable because each user has an individual incentive to join the larger platform.

### 6. Platform Governance and Arrow's Impossibility

Platforms don't just run markets — they govern them. Content moderation, account suspension, dispute resolution, appeals processes: these are governance mechanisms that determine what's permissible, who has rights, and how conflicts are resolved. And they face exactly the impossibility constraints from Day 34.

Arrow's impossibility theorem (Day 34) says no voting rule simultaneously satisfies all desirable properties: unanimity, independence of irrelevant alternatives, and non-dictatorship. Platform content moderation faces an analogous impossibility:

- **Free expression** (no constraint on speech) is incompatible with **safety** (preventing harassment, disinformation, incitement).
- **Consistency** (apply the same rules to everyone) is incompatible with **context-sensitivity** (the same statement has different implications from different speakers in different situations).
- **Non-discrimination** (no differential treatment based on viewpoint) is incompatible with both safety and the platform's own editorial judgment.

Every content moderation policy makes tradeoffs among these goals. Every tradeoff disadvantages some users. And there is no tradeoff-free policy that satisfies all of these simultaneously — Arrow's theorem in governance form.

Practical platforms address this through a hierarchical mechanism: algorithmic enforcement for clear cases (spam, CSAM, obvious copyright violation — cases where the signal is strong and error costs are asymmetric), community moderation for edge cases (flagging, community standards enforcement), and human review for contested cases (appeals, high-profile decisions). Each level is a different mechanism with different strategic incentives for participants.

The strategic game: creators learn what content patterns trigger algorithmic demotion and avoid them. Adversarial actors learn what content stays just inside permissible bounds. The platform updates its detection mechanism; actors adapt. This is the Goodhart dynamic applied to content moderation — the mechanism's signal (policy-violating content patterns) becomes the target of adversarial optimisation.

---

## The Formal Picture

**Platform profit decomposition.** A two-sided platform earns revenue by charging each side above its cost of serving that side, multiplied by how many users from that side participate. Crucially, the demand from each side depends on how many users are on the other side — this is the cross-side network effect. Because the two sides are interlinked, the platform cannot price them independently. Raising the price on side A reduces side A's participation, which reduces side B's value from the platform, which reduces side B's participation, which reduces side A's value — a cascade. The platform must choose both prices simultaneously, trading off the direct revenue from each side against the indirect effect on the other.

**Rochet-Tirole optimal pricing.** The profit-maximising price for each side follows a modified version of a classic pricing rule. For a normal product, the optimal markup above cost is inversely proportional to price sensitivity — the less sensitive buyers are to price, the more you can charge above cost. For a two-sided platform, the rule has one addition: the effective cost of serving a given side is reduced by the cross-side value that side generates. If each developer who joins creates $3 of value per user already on the platform, then attracting one more developer is worth more to the platform than just the fee that developer pays — it also unlocks $3 per user in indirect value. That indirect value is subtracted from the "effective cost" before applying the markup rule, which pushes the optimal developer price down — sometimes below zero, meaning a subsidy. The side that generates the larger cross-side externality for the other side gets the lower (or negative) price; the side that benefits most from the subsidised side pays more.

Numerical intuition: if each developer adds $3 of value per user, then the platform's effective gain from attracting one more developer is the developer fee plus $3 multiplied by the number of users. This makes attracting developers more valuable, pushing optimal developer prices down.

**Ad auction rank-by-value.** In the Generalised Second Price mechanism, each advertiser submits a bid and the platform assigns a quality score. The ranking score is bid multiplied by quality (as described above in the intuition section). Each winner pays the minimum amount needed to hold their position above the next competitor — not their own bid. Specifically: the winner of position k pays just enough that their quality score times their effective per-click payment equals the quality score times the bid of the advertiser in position k+1. Because payment is based on the competitor below you, not on your own bid, there is no advantage to overbidding. A high-quality advertiser can outrank a high-bid, low-quality competitor at a lower per-click cost — which benefits both advertisers (efficiency) and users (more relevant ads).

---

## Where It Breaks

**Platform objectives diverge from social welfare.** The mechanism that maximises platform profit is not, in general, the mechanism that maximises total surplus. Amazon's ranking mechanism optimises for Amazon's revenue (which includes both transaction fees and sponsored product placement fees), not for buyer welfare or seller welfare or their combination. Google's search ranking optimises for ad revenue, not for information quality as a public good. The gap between platform-optimal and socially-optimal mechanisms is the Price of Anarchy of platform capitalism — a structural feature, not a design failure that can be corrected by better algorithm design.

**Lock-in as mechanism weaponisation.** Once a platform achieves dominance through network effects (Day 33), it can change its mechanism in ways that disadvantage competitors and users without those users switching to alternatives. Users are locked in by switching costs (social graphs, content history, supplier relationships). This allows the platform to gradually shift the mechanism in its favour — increasing fees, degrading organic reach to push sellers toward paid placement, changing data sharing rules — without losing market share. The mechanism becomes a barrier to entry rather than a fair set of rules for market operation.

**Multi-homing collapses platform pricing power.** When both sides of the market can use multiple platforms simultaneously (multi-homing), the platform loses its ability to set prices above marginal cost — because sellers and buyers can route transactions through whichever platform charges less for that transaction. Platform mechanism design increasingly attempts to prevent multi-homing through lock-in features: exclusive content (streaming platforms), platform-specific payment systems (Apple Pay in App Store), proprietary review histories (Amazon seller metrics that don't transfer to other platforms), and social features that are only valuable within the platform. These are mechanisms to maintain pricing power, not mechanisms to improve market efficiency.

---

## Try It Yourself

**Exercise 1 — Design and Information Theory (25 min).** Airbnb is deciding whether to show total price (including all cleaning fees and service fees) or base nightly price in search results. Frame this as a Kamenica-Gentzkow information design problem (Day 39):

- Who is the sender? Who is the receiver?
- What is the state space (what information exists that the platform could reveal)?
- What are the receiver's actions (what does the buyer decide)?
- What is the sender's objective function?
- When total price is shown, what does the buyer learn? When base price is shown, what does the buyer infer (and how does this affect the equilibrium if rational buyers expect hidden fees)?
- Based on Bayesian persuasion theory, what does the platform's choice reveal about which choice generates more transactions? Who benefits and who is harmed?
- Regulators in several countries have mandated total price display. What is the mechanism design rationale for this regulation?

**Exercise 2 — Rochet-Tirole Calculation (20 min).** A platform connects developers (side D) and users (side U). Parameters:

- MC = 0 for both sides (digital platform, zero marginal cost)
- Developer price elasticity: η_D = −2
- User price elasticity: η_U = −5
- Cross-side externality: each developer adds $3 of value per user (e_{D→U} = $3); each user adds $0.50 of value per developer (e_{U→D} = $0.50)

Using the Rochet-Tirole condition, compute optimal prices p_D* and p_U* (Hint: work from the condition that (p_i − e_{j→i}) / p_i = 1/|η_i|, recognising that "effective cost" incorporates the cross-side externality and MC = 0).

Which side is subsidised? Which side is charged? Does this match the real-world pattern you observe for app stores? What does the pricing structure imply about whose interests the platform is structurally aligned with?

**Exercise 3 — Mechanism Critique (30 min).** Analyse Amazon's product review system as a mechanism. Structure your analysis around the following:

1. What is the mechanism's goal? State it in terms of the revelation principle — what preference information is the mechanism trying to elicit, and from whom?
2. Identify three strategic behaviours the mechanism inadvertently incentivises (at least one from sellers, one from buyers, and one from competitors).
3. For each strategic behaviour, identify the mechanism feature that makes it profitable.
4. Propose one specific mechanism modification that reduces the most damaging gaming behaviour without eliminating honest reviews. Be specific about: what changes (the rule, the incentive, the information structure), who is affected, and what new strategic behaviour your modification might inadvertently incentivise.

---

## Connect It Back

Platform mechanism design is applied mechanism design at market scale. Every concept from Module 3 appears in concrete form:

- **VCG and GSP auctions (Day 26):** ad ranking mechanisms
- **Two-sided markets (Day 33):** Rochet-Tirole optimal pricing
- **Information design (Day 39):** review systems, price display, recommendation algorithms
- **Price of Anarchy (Day 38):** surge pricing, market tipping
- **Collective choice impossibility (Day 34):** content moderation governance
- **Principal-agent moral hazard (Day 27):** platform executives optimising for measurable proxies rather than true social value

The unifying theme: platforms are mechanism designers operating under the constraint that their private incentives don't perfectly align with social welfare. The Price of Anarchy of platform capitalism — the gap between platform-optimal mechanisms and socially-optimal mechanisms — is a structural feature that requires either regulatory intervention or competitive pressure to correct.

Tomorrow (Day 52): nudge design — a lighter-touch mechanism that changes behaviour by changing the choice architecture rather than the incentives. Where platform mechanism design uses hard constraints (rankings, prices, access), nudge design uses soft architecture (defaults, framing, salience). Both are in the toolkit of the AI product designer.

---

## Suggested Readings

- **Primary:** Parker, Van Alstyne & Choudary "Platform Revolution" Chapters 5–7. Chapter 5 (the value unit) and Chapter 6 (disruption) are most relevant to mechanism design.
- **Primary:** Rochet & Tirole "Platform Competition in Two-Sided Markets" (2003). The first few pages and the pricing intuition are accessible; the full model requires calculus and patience.
- **Pricing depth:** Evans & Schmalensee "Matchmakers: The New Economics of Multisided Platforms" (2016). Chapters 4–5 for the pricing intuition without the full Rochet-Tirole formalism.
- **Ad auctions:** Varian, Hal. "Position Auctions." International Journal of Industrial Organization, 25(6):1163–1178, 2007. The canonical treatment of GSP — accessible and short.
- **Dark side:** Khan, Lina. "Amazon's Antitrust Paradox." Yale Law Journal, 126(3), 2017. A mechanism design critique of Amazon's dual role as marketplace operator and marketplace participant.


---

← [Day 50 — RLHF & Constitutional AI](day-50-rlhf-alignment) &nbsp;|&nbsp; [Day 52 — Nudge Design & Behavioural Mechanism Design →](day-52-nudge-design)
