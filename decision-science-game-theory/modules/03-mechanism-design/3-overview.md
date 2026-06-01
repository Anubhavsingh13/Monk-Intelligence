*[Decision Science & Game Theory](../../README.md) · Module 3 of 5*

# Module 3 — Mechanism Design & Advanced Structures
*Days 28–40 · Engineering incentives, auctions, and fair division*

---

## What This Module Earns You

By the end of Day 40 you will have made the transition from *playing* games to *designing* them. You will be able to:

- Design incentive-compatible mechanisms — rules that make telling the truth or following instructions individually rational.
- Analyse signalling games: when signals are credible, when they are cheap talk, and how to design verifiable outputs.
- Construct screening menus that separate customer types without direct observation.
- Apply the Revelation Principle and design mechanisms that implement desired social choices.
- Evaluate voting systems for their fairness properties and failure modes.
- Reason about network effects, platform economics, and the strategic consequences of switching costs.
- Compute Shapley values for fair credit attribution — directly applicable to ML explainability (SHAP).
- Quantify the efficiency loss from selfish behaviour using the Price of Anarchy.

---

## The Story of This Module

Module 2 asked: "Given the rules, how should I play?" Module 3 asks the inverse: "Given that players will play strategically, what rules should I design to get the outcome I want?" This is the question every AI product designer implicitly answers — often badly — every time they ship a new feature, pricing tier, or ranking algorithm.

The module opens with the two most important tools for dealing with hidden information: signalling (the informed player acts to reveal type) and screening (the uninformed designer offers menus to separate types). It then introduces mechanism design proper — the general theory of incentive-compatible rule design — before widening to cooperative structures (matching, coalitions, fair division) and computational complexity (Price of Anarchy).

The capstone rest day (Day 40) asks you to perform a full game-theoretic audit of a real AI product. This is not an abstract exercise. If you cannot identify the players, information structure, and equilibrium in a product you know, the theory has not yet been internalised.

---

## Days at a Glance

| Day | Title | One Idea |
|-----|-------|----------|
| 28 | Signalling | Costly signals screen types |
| 29 | Screening | Menus that self-sort |
| 30 | Mechanism Design | Inverse game theory |
| **31** | **Rest & Synthesise** | **Redesign a feature from scratch** |
| 32 | Voting Systems | Arrow's impossibility |
| 33 | Public Goods & Externalities | Coordination at scale |
| 34 | Network Effects & Platforms | Winner-take-most dynamics |
| 35 | Matching Markets | Stable pairings |
| 36 | Cooperative Game Theory | Coalition formation |
| 37 | The Shapley Value | Fair marginal credit |
| 38 | Correlated Equilibrium | Beyond Nash |
| 39 | Algorithmic Game Theory | Price of Anarchy |
| **40** | **Rest & Synthesise** | **Audit a real AI product** |

---

## Primary Sources
- Tadelis. *Game Theory: An Introduction*. Princeton, 2013. — Chapters on incomplete information and mechanism design.
- Myerson, R.B. "Optimal Auction Design." *Mathematics of Operations Research*, 1981. — Primary for Day 30.
- Roughgarden, T. *Twenty Lectures on Algorithmic Game Theory*. Cambridge, 2016. — Primary for Day 39.

*(Full annotations in [bibliography.md](../../bibliography.md))*

← **Previous module:** [Module 2 — Game Theory Fundamentals](../02-game-theory/2-overview.md)
→ **Next module:** [Module 4 — Decision Science in AI Products](../04-ai-products/4-overview.md)
