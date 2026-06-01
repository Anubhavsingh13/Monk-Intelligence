*[Decision Science & Game Theory](../../../../README.md) · [Module 3 — Mechanism Design & Advanced Structures](../3-overview.md) · Day 31 of 65*

# Day 31 — Voting Systems & Arrow's Impossibility Theorem

> **Today's one idea:** No voting system can simultaneously satisfy a short list of obviously reasonable fairness requirements — Arrow's theorem proves this mathematically, and every real voting system violates at least one of them.
> **Reading time:** ~35 min · **Prereqs:** [Day 30](day-30-mechanism-design.md), [Day 14](../../02-game-theory/days/day-14-what-is-a-game.md)
> **Primary source for today:** Arrow, Kenneth J. *Social Choice and Individual Values*, 2nd ed. Wiley, 1963. Chapter 5 ("The Possibility Theorem"). Also: Sen, Amartya K. *Collective Choice and Social Welfare*. Holden-Day, 1970. Chapter 3.

---

## The Hook

In 1951, a 29-year-old economist named Kenneth Arrow proved something that shocked political scientists and philosophers: there is no "perfect" voting system. Not just that we haven't found one yet — that it is *mathematically impossible* for one to exist.

The proof starts from four axioms — requirements so mild and reasonable that any fair voting system should surely satisfy them. You might even find them obvious. Then Arrow proves that no system can satisfy all four simultaneously when there are three or more alternatives.

The result has a strange quality: it is simultaneously a trivial mathematical theorem (once you understand the proof, it seems almost inevitable) and a genuinely unsettling fact about collective decision-making. Every democracy runs a voting system that violates at least one of Arrow's axioms. Your company's decision-making processes do too. And the specific axiom violated determines the specific pathology the system produces.

---

## Building the Intuition

### The setting

A group of n voters must choose among k ≥ 3 alternatives (candidates, policies, features). Each voter has a complete, transitive preference ranking over the alternatives. A **social welfare function** aggregates these individual rankings into a collective ranking.

**Examples of social welfare functions:**
- Majority vote (pairwise comparison)
- Plurality (first-past-the-post)
- Borda count (assign points by rank)
- Approval voting
- Condorcet method

Arrow asks: what properties should a reasonable social welfare function have?

---

### Arrow's four axioms

**Axiom 1 — Unrestricted Domain (U):**
The social welfare function must work for *any* profile of individual preferences. Voters can have any consistent preferences — no restrictions.

*Why reasonable?* A voting system that only works when voters' preferences take a particular form is fragile. A robust system should handle any input.

**Axiom 2 — Pareto Efficiency (P):**
If every voter prefers A to B, then the social ranking must place A above B.

*Why reasonable?* If the entire electorate agrees on something, the system should respect that consensus. A system that ranks B above A when everyone prefers A seems absurd.

**Axiom 3 — Independence of Irrelevant Alternatives (IIA):**
The social ranking of A vs. B depends only on voters' relative preferences between A and B — not on their preferences involving any third alternative C.

*Why reasonable?* Whether we prefer Candidate A to Candidate B shouldn't depend on whether a third candidate C is on the ballot or not. Adding C to the race shouldn't change the A-B comparison.

**Axiom 4 — Non-Dictatorship (ND):**
There is no single voter whose preferences always determine the social ranking, regardless of what others prefer.

*Why reasonable?* A system where one person's vote always wins regardless of others is a dictatorship, not a democracy.

**Arrow's Impossibility Theorem:**

> For three or more alternatives and two or more voters, there is no social welfare function satisfying all four axioms (U, P, IIA, ND).

---

### The Condorcet paradox: intuition for why

Before Arrow's general proof, the Condorcet paradox shows the core tension.

**Three voters, three candidates (A, B, C):**

```
Voter 1: A > B > C
Voter 2: B > C > A
Voter 3: C > A > B
```

**Pairwise majority votes:**
- A vs. B: Voters 1 and 3 prefer A. A wins (2-1).
- B vs. C: Voters 1 and 2 prefer B. B wins (2-1).
- A vs. C: Voters 2 and 3 prefer C. C wins (2-1).

**The paradox:** A beats B, B beats C, but C beats A. Majority preference is **cyclic** — it is not transitive. Even though every individual has consistent preferences, the collective preference has no winner.

This shows that a simple rule ("pick the majority winner") can fail to produce a coherent social ranking. The Condorcet paradox is the tip of Arrow's iceberg.

---

### The proof sketch: how IIA forces dictatorship

The full proof of Arrow's theorem is intricate, but the key idea is worth following.

**Step 1 — Any decisive group can be shrunk to one person.**

Call a group G of voters "decisive over (A, B)" if whenever all members of G prefer A to B, the social ranking puts A above B (regardless of others' preferences). Arrow shows that any decisive group can be split — one subgroup or the other is also decisive. By finite descent, this reaches a decisive group of size 1: a dictator.

**Step 2 — IIA forces the group structure.**

IIA prevents the social ranking of A vs. B from being influenced by preferences over C. This means you can reason about each pair independently. But once pairs are independent and Pareto holds, a chain of logic shows that some individual must be decisive.

**Step 3 — Non-dictatorship fails.**

The process of Step 1 always terminates at a single decisive individual. Since ND says no such individual can exist, no function satisfies all four axioms.

The proof is rigorous but short — Arrow's original proof in *Social Choice and Individual Values* fits in a few pages. This is one of the most elegant impossibility proofs in social science.

---

### Which axiom does each voting system violate?

Since every system must violate at least one axiom, the question becomes: *which one?*

| Voting System | Axiom Violated | How |
|---------------|---------------|-----|
| Plurality (FPTP) | IIA | Adding a third candidate splits the vote; the winner can change |
| Borda count | IIA | A candidate's rank changes when an "irrelevant" candidate is added or removed |
| Majority vote | Universal Domain | Can produce Condorcet cycles (no transitive winner) |
| Approval voting | IIA | Who you approve can affect the outcome for pairs you care about |
| Dictatorship | Non-Dictatorship | By construction |
| Ranked-choice (IRV) | IIA | Eliminating a candidate can change the winner |

**The spoiler effect (IIA violation in plurality):**

2000 US Presidential election: In Florida, Nader received 97,000 votes. Bush won Florida by 537 votes. Without Nader on the ballot, most Nader voters would have preferred Gore. Nader was "irrelevant" (he didn't win) but his presence determined the A-vs-B comparison (Bush vs. Gore). This is IIA violation — the A-B ranking changed because of an irrelevant alternative.

**Borda count manipulation (IIA violation):**

If voters rank A > B > C and you're using Borda counts (3-2-1 points), removing C from the ballot gives more Borda points to B relative to A — because B no longer gets its "second place" score suppressed by C. The A-B ranking can flip when C is removed.

---

### Why the theorem matters for mechanism design

Arrow's theorem is the voting analogue of Gibbard-Satterthwaite (Day 30). Both prove that you cannot simultaneously achieve:
- Full generality (any preferences)
- Some efficiency criterion (Pareto, efficiency)
- Independence (IIA or strategyproofness)
- Non-dictatorship

The mechanism design response: accept that you can't have everything, and choose which axiom to relax based on the application's priorities.

**Relaxing Unrestricted Domain:**

Single-peaked preferences (Black's median voter theorem): if all voters' preferences are single-peaked on a one-dimensional issue (everyone has a "favourite" point and preferences decline on both sides), the median voter's preference is always the Condorcet winner. No paradox, no IIA violation. The restriction to single-peaked preferences makes majority vote well-behaved.

This is why political issues are often deliberately framed as one-dimensional spectra (left-right) even when reality is multi-dimensional — single-dimensionality makes collective choice tractable.

**Relaxing IIA:**

Most practical voting systems violate IIA. The question is how badly. Ranked-choice voting (IRV) has better IIA properties than plurality, but not perfect IIA. The design choice is about the magnitude of the IIA violation, not its elimination.

**Using cardinal utilities:**

Arrow's framework uses ordinal rankings only. If voters report cardinal utilities (not just rankings), more is possible. With money transfers (VCG), efficiency and strategyproofness are achievable — but now you're doing mechanism design, not just voting.

---

### Arrow's theorem in AI and product decisions

**Feature prioritisation as a social choice problem:**

When a product team votes on features, they face Arrow's theorem. If you use a simple ranking vote, you'll hit IIA violations (removing a feature from consideration changes the vote on other features). The classic "how do we prioritise?" problem in product management is a collective choice problem — and the impossibility theorem says no clean solution exists.

**RLHF preference aggregation (preview of Day 52):**

Training an AI model from human feedback requires aggregating the preferences of many raters into a training signal. This is a social choice problem. The RLHF mechanism must choose how to aggregate preferences. If raters' preferences cycle (Condorcet paradox), the aggregation method determines the model's behaviour — and different aggregation methods can produce different aligned models.

**Committee decision-making:**

Any group that decides by ranking alternatives faces Arrow's constraints. Understanding which axiom your committee violates (IIA, via agenda control: who controls which options are on the table controls the outcome) helps you understand power dynamics in organisational decision-making.

**Recommendation system ranking:**

When a platform aggregates user preferences to produce a ranked list of recommendations, it is implementing a social welfare function. Arrow's theorem says it cannot simultaneously satisfy all reasonable properties. The specific violation (IIA: adding a new item to the catalogue changes other items' rankings) manifests as the "cold start problem" and related recommendation instabilities.

---

## The Formal Picture

**Social welfare function:**

A social welfare function takes a collection of individual preference rankings (one ranking per voter, over all the alternatives) and produces a single collective ranking. It is the mathematical version of a voting system.

**Arrow's four axioms (plain version):**

- **(U) Unrestricted Domain:** The rule must work for *any* combination of individual preferences. No restrictions on what voters are allowed to prefer.
- **(P) Pareto Efficiency:** If every voter prefers A to B, the collective ranking must place A above B.
- **(IIA) Independence of Irrelevant Alternatives:** The collective ranking of A vs. B depends only on individual rankings of A vs. B — not on anything involving other alternatives.
- **(ND) Non-Dictatorship:** No single voter's preferences always determine the collective ranking, regardless of others.

**Arrow's Impossibility Theorem:**

With three or more alternatives and two or more voters, no social welfare function can satisfy all four axioms simultaneously.

**Condorcet winner:**

A Condorcet winner is an alternative that beats every other alternative in head-to-head majority voting. It does not always exist (the Condorcet paradox shows preferences can cycle). When it does exist, most reasonable voting systems elect it.

**Black's median voter theorem:**

If preferences are single-peaked on a one-dimensional issue — everyone has a "favourite" point, and preferences fall off on both sides — then the median voter's top choice is always the Condorcet winner. On this restricted domain, majority rule satisfies all four of Arrow's axioms. This is why political issues are often simplified to a one-dimensional left-right spectrum: it makes collective choice tractable.

---

## Where It Breaks / What It Is Not

**Arrow's theorem is about ordinal preferences.** If you allow cardinal utilities (how much better is A than B?), more is achievable. Utilitarianism (maximise the sum of utilities) satisfies modified versions of Arrow's conditions — but requires interpersonal utility comparisons that are philosophically controversial.

**The theorem doesn't say all systems are equally bad.** Some systems violate IIA more severely than others; some are more susceptible to strategic manipulation. The theorem establishes impossibility; it doesn't say all designs are equivalent.

**Arrow's theorem doesn't cover all desiderata.** Sen (1970) showed that even weaker requirements — "minimal liberty" (every individual should be decisive over at least one pair of alternatives) combined with Pareto — lead to impossibility. Sen's Liberal Paradox extends Arrow's insight to individual rights.

**Practical voting systems have been studied extensively.** The "best" system depends on what you care about most: IIA compliance (Condorcet methods), resistance to strategic manipulation (approval voting), proportional representation (STV). The mechanism design approach: state your priorities, accept the violated axiom that matters least to you.

---

## Try It Yourself

**Exercise 1 — Find the Condorcet winner (5 min)**

Five voters rank three product features (A, B, C):

```
Voter 1: A > B > C
Voter 2: A > C > B
Voter 3: B > A > C
Voter 4: C > B > A
Voter 5: C > A > B
```

a) Run all pairwise majority votes. Is there a Condorcet winner?
b) Apply plurality (first choice) voting. What wins?
c) Apply Borda count (3/2/1 points for 1st/2nd/3rd place). What wins?
d) Do all three methods agree?

<details>
<summary>Worked answer</summary>

**a)** Pairwise results:
- A vs. B: Voters 1, 2, 5 prefer A → A wins (3-2).
- A vs. C: Voters 1, 2, 3 prefer A → A wins (3-2).
- B vs. C: Voters 1, 3, 4 prefer B → B wins (3-2).

**A is the Condorcet winner** — beats both B and C pairwise.

**b)** Plurality (first choices): A: voters 1, 2 = 2 votes. B: voter 3 = 1 vote. C: voters 4, 5 = 2 votes. A and C tie. (If tie-breaking favours A, A wins.)

**c)** Borda count:
- A: 3+3+2+1+2 = 11 points.
- B: 2+1+3+2+1 = 9 points.
- C: 1+2+1+3+3 = 10 points.

**A wins with Borda count** (11 points).

**d)** All three agree on A (with tie-breaking in plurality). Note: this is not always the case — in other preference profiles, the three methods can elect different winners.

</details>

---

**Exercise 2 — Demonstrate IIA violation (10 min)**

Three voters rank three candidates (X, Y, Z):

```
Voter 1: X > Y > Z
Voter 2: Y > Z > X
Voter 3: Z > X > Y
```

a) Apply Borda count. Who wins?
b) Now remove candidate Z from the ballot. Apply Borda count to {X, Y} only.
c) Does the removal of Z change the X-Y ranking? This demonstrates IIA violation.

<details>
<summary>Worked answer</summary>

**a)** Borda count (3/2/1):
- X: 3+1+2 = 6. Y: 2+3+1 = 6. Z: 1+2+3 = 6. Three-way tie.

**b)** With only X and Y (2/1 points):
- X: 2+1+2 = 5. Y: 1+2+1 = 4.
X wins 5-4.

**c)** With Z present: X = Y (tie). Without Z: X > Y. The removal of Z (an "irrelevant" alternative that didn't win) changed the X-Y ranking. **IIA is violated by Borda count.**

This is not a pathological edge case — it's the regular behaviour of Borda count, which is why it's sensitive to the set of candidates on the ballot.

</details>

---

**Exercise 3 — Apply to your organisation (10 min)**

Think of a collective decision your team, company, or community has made in the past year using some group decision process.

a) What was the voting/decision mechanism used? (Vote, discussion-to-consensus, ranked preference survey, leadership decree, etc.)
b) Which of Arrow's four axioms does it violate? (Every process violates at least one.)
c) Was the violated axiom exploited strategically? (Agenda control, strategic voting, adding or removing options to change the outcome?)
d) What mechanism would you use instead, and what axiom would that sacrifice?

---

## Connect It Back

Arrow's theorem is the democratic equivalent of the Prisoner's Dilemma: individually rational and reasonable requirements collide collectively. There is no escape through cleverness — the impossibility is mathematical.

The response is not despair but clarity: understand which axiom you're sacrificing, design accordingly, and be aware of the failure modes your chosen system introduces. Mechanism design (Day 30) operates in the same space but adds money transfers, which Arrow's purely ordinal framework excludes — and that is exactly why VCG can achieve efficiency where voting cannot.

Tomorrow (Day 32) brings the **public goods problem** — the multi-player Prisoner's Dilemma where every individual has incentive to free-ride on collective contributions, and the mechanism design challenge of inducing voluntary provision.

**The question you should be able to answer now:**
*Why does IIA violation allow agenda control — and how can the order in which alternatives are considered determine the outcome of an apparently fair voting process?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Arrow, Kenneth J. *Social Choice and Individual Values*, 2nd ed. Wiley, 1963. Chapter 3 ("The Social Welfare Function") and Chapter 5 ("The Possibility Theorem"). — The original proof, stated with unusual clarity for a mathematical result. Chapter 3 states the axioms; Chapter 5 proves impossibility. About 15 pages total. Read slowly — every sentence counts.

**If you want the deep version:**

1. Sen, Amartya K. "The Impossibility of a Paretian Liberal." *Journal of Political Economy*, 78(1):152–157, 1970. — Six pages. Sen's extension of Arrow's impossibility to individual rights: no social welfare function can satisfy both Pareto efficiency and minimal individual liberty. Proves that individual rights and collective welfare are in fundamental tension. Read with today's page — it deepens the impossibility theme.

2. Riker, William H. *Liberalism Against Populism: A Confrontation Between the Theory of Democracy and the Theory of Social Choice*. W. H. Freeman, 1982. Chapter 4 ("Arrow's Theorem"). — The application of Arrow's theorem to democratic theory. Riker argues that Arrow's theorem implies that "the will of the people" is a meaningless concept — collective preferences are artifacts of the voting mechanism, not independent social facts. Controversial but thought-provoking.

3. Brams, Steven J. & Fishburn, Peter C. *Approval Voting*, 2nd ed. Springer, 2007. Chapter 1 ("Introduction: Overview of Approval Voting") and Chapter 9 ("Comparisons with Other Voting Systems"). — The best-developed alternative to Arrow's framework: approval voting allows voters to express support for any subset of candidates, reducing (but not eliminating) IIA violations. Chapters 1 and 9 cover the theory and the empirical evidence on whether approval voting improves election outcomes.


---

← [Day 30 — Mechanism Design](day-30-mechanism-design) &nbsp;|&nbsp; [Day 32 — Public Goods & the Free-Rider Problem →](day-32-public-goods-free-rider)
