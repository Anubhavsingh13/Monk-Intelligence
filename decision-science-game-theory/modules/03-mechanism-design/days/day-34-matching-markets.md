*[Decision Science & Game Theory](../../../../README.md) · [Module 3 — Mechanism Design & Advanced Structures](../3-overview.md) · Day 34 of 65*

# Day 34 — Matching Markets

> **Today's one idea:** When prices alone can't clear markets — because relationships matter, not just quantities — a matching algorithm must produce stable assignments, and the Gale-Shapley deferred acceptance algorithm achieves this while being strategyproof for one side.
> **Reading time:** ~35 min · **Prereqs:** [Day 26](../../02-game-theory/days/day-26-auctions.md), [Day 30](day-30-mechanism-design.md), [Day 24](../../02-game-theory/days/day-24-coordination-focal-points.md)
> **Primary source for today:** Gale, David & Shapley, Lloyd S. "College Admissions and the Stability of Marriage." *American Mathematical Monthly*, 69(1):9–15, 1962. Also: Roth, Alvin E. "The Evolution of the Labor Market for Medical Interns and Residents: A Case Study in Game Theory." *Journal of Political Economy*, 92(6):991–1016, 1984.

---

## The Hook

In 1945, the American medical system faced a crisis. Hospitals desperately wanted the best graduating medical students; students wanted positions at the best hospitals. Hospitals began offering positions earlier and earlier — sometimes 18 months before graduation — to "lock in" desirable students before competitors could approach them. Students, pressured to decide, accepted offers they hadn't fully considered. Both sides were miserable.

By 1952, hospitals had tried to agree not to extend offers before a certain date. But this failed — individually rational hospitals jumped the queue anyway. A classic coordination problem (Day 24), failing repeatedly.

In 1952, the National Resident Matching Program (NRMP) was introduced: hospitals and students submit ranked preference lists, and a centralised algorithm produces a matching. Within years, nearly all hospitals and students participated voluntarily. The program is still running today, matching ~45,000 medical residents annually.

In 2012, Alvin Roth and Lloyd Shapley won the Nobel Prize in Economics for the theory of stable matchings and the design of matching markets. The NRMP is one of their central examples.

---

## Building the Intuition

### Why prices alone don't work

In a standard market, prices clear supply and demand. If there are too many buyers for a good, the price rises until demand equals supply. Efficient allocation follows from prices.

Matching markets are different in two ways:

1. **Discreteness:** You can't hire 0.7 of a doctor. Each position is filled by exactly one resident; each resident fills exactly one position. The allocation is a one-to-one matching, not a continuous quantity.

2. **Relationship quality matters:** It's not just whether you get a position — it's *which* position. A resident at Massachusetts General Hospital and a resident at a rural hospital may receive the same salary, but the match quality (research opportunities, training quality, location) is radically different. Both sides have preferences over *whom* they're matched with.

When both sides have preferences over partners, a price signal alone cannot coordinate — you need a mechanism that accounts for the preference structure on both sides.

---

### Stability: the right solution concept

**What should a good matching achieve?**

Efficiency (Day 30): match each doctor to the hospital where they add most value? But value to whom — the hospital, the patient, the doctor? Preferences conflict.

**Stability** is the right concept for matching markets:

A matching is **stable** if there is no "blocking pair" — a doctor d and a hospital h who are not currently matched to each other but *both prefer each other* to their current match.

**Why stability?**

If a blocking pair exists, d and h would mutually benefit from renegotiating — they would defect from the central matching. A stable matching is self-enforcing: no pair of participants has incentive to circumvent it. An unstable matching would unravel as blocking pairs defect.

**Example:**

Two doctors (D1, D2), two hospitals (H1, H2):

Preferences:
- D1: H1 > H2
- D2: H1 > H2
- H1: D1 > D2
- H2: D1 > D2

Consider matching: {D1-H2, D2-H1}.

Check stability: Does D1-H1 form a blocking pair? D1 prefers H1 to H2 (yes). H1 prefers D1 to D2 (yes). **Blocking pair exists — matching is unstable.**

The stable matching is: {D1-H1, D2-H2}. Verify: D2 and H1 might prefer each other, but D2 prefers H1 (yes) and H1 prefers D1 over D2 (so H1 doesn't prefer D2). No blocking pair. Stable.

---

### Gale-Shapley Deferred Acceptance Algorithm

Gale and Shapley (1962) proved that a stable matching always exists and provided an algorithm to find one.

**The Doctor-Proposing Algorithm:**

```
Round 1:
  Each doctor proposes to their top-choice hospital.
  Each hospital tentatively holds its most preferred among applicants,
  rejects the rest.

Round 2:
  Rejected doctors propose to their second-choice hospital.
  Each hospital compares new applicants + held applicant,
  tentatively holds its most preferred, rejects the rest.

Repeat until no rejections. All tentative holds become final.
```

**Worked example:**

Three doctors (D1, D2, D3), three hospitals (H1, H2, H3):

Preferences:
```
D1: H1 > H2 > H3      H1: D3 > D1 > D2
D2: H1 > H3 > H2      H2: D1 > D2 > D3
D3: H1 > H2 > H3      H3: D2 > D1 > D3
```

**Round 1:** D1, D2, D3 all propose to H1.
H1 holds D3 (prefers D3 > D1 > D2), rejects D1 and D2.

**Round 2:** D1 proposes to H2. D2 proposes to H3.
H2 holds D1 (only applicant). H3 holds D2 (only applicant).

**No rejections. Final matching: {D1-H2, D2-H3, D3-H1}.**

**Check stability:**
- D1-H1 blocking pair? D1 prefers H1. H1 prefers D3 (currently holding D3) over D1. H1 doesn't prefer D1. Not a blocking pair. ✓
- D2-H1 blocking pair? D2 prefers H1. H1 prefers D3 over D2. Not a blocking pair. ✓
- D3-H2 blocking pair? D3 prefers H1 (currently at H1) over H2. D3 doesn't prefer H2. Not a blocking pair. ✓
- All other pairs: verify similarly. **Matching is stable.**

---

### Key properties of Gale-Shapley

**Theorem (Gale-Shapley 1962):**

1. **A stable matching always exists** (for any preference profiles).
2. **The doctor-proposing algorithm produces a stable matching.**
3. **The doctor-optimal stable matching:** The algorithm produces the matching that is *best for all doctors simultaneously* among all stable matchings.
4. **The hospital-pessimal stable matching:** The same matching is *worst for all hospitals* among all stable matchings.

**This asymmetry is fundamental:** The proposing side gets their best stable matching; the receiving side gets their worst. Power in a matching market goes to the proposers.

**Strategy-proofness (Roth 1982):**

The doctor-proposing algorithm is **strategyproof for doctors**: each doctor's dominant strategy is to submit their true preference ranking. No doctor can improve their outcome by misrepresenting preferences.

However, hospitals *can* manipulate: by submitting a false preference ranking, a hospital might get a better match than by reporting truthfully. No stable matching mechanism is strategyproof for *both* sides simultaneously — the Gibbard-Satterthwaite impossibility (Day 30) applies.

---

### Real-world matching markets

**Medical residency (NRMP):**

The NRMP runs a doctor-proposing (technically, a "resident-proposing" adapted version) Gale-Shapley algorithm annually. When NRMP switched to Gale-Shapley in 1998 (from an earlier hospital-proposing version), doctors' match quality improved noticeably. Roth's analysis of the pre-1998 system showed that the hospital-proposing version had been strategically manipulable — doctors submitted false preferences. The switch to doctor-proposing made truth-telling dominant for the weaker side.

**School choice (Boston mechanism vs. Gale-Shapley):**

Many US cities use "school choice" systems where families rank preferred schools. The classic "Boston mechanism" (immediate acceptance) is not stable and encourages strategic misrepresentation — families are rewarded for gaming the system rather than listing true preferences. Boston, New York, and Chicago have switched to Gale-Shapley (student-proposing deferred acceptance), which is strategyproof for students.

**Kidney exchange (Roth, Sönmez, Ünver 2004):**

Some patients need kidney transplants but have willing donors whose kidneys are incompatible with them. A kidney exchange pairs two (or more) incompatible donor-patient pairs who are cross-compatible. The matching problem: which exchanges to execute. Gale-Shapley variants have been used to design kidney exchange markets, saving thousands of lives annually.

**Job markets and dating apps:**

The academic job market (economics, law) uses a matching market structure. Dating apps are fundamentally two-sided matching markets — but most use non-stable algorithms (swipe-based, undirected). Hinge's "most compatible" feature is a step toward algorithmic stable matching.

---

## The Formal Picture

**Matching market (two-sided, one-to-one):**

There are two groups — doctors and hospitals (or students and schools, etc.). Each agent has a strict ranking of everyone on the other side, plus the option of remaining unmatched.

A **matching** assigns each doctor to at most one hospital and each hospital to at most one doctor, with the constraint that if doctor d is matched to hospital h, then h is also matched to d (the assignment is mutual).

**Stability — the key requirement:**

A matching is stable if:
1. **Individual rationality:** No agent would rather be unmatched than accept their current assignment.
2. **No blocking pair:** There is no doctor-hospital pair (d, h) who are *not* matched to each other but *both* prefer each other to their current assignments. If such a pair existed, they'd walk away from the central matching.

**Gale-Shapley Theorem:**

For any combination of preferences, the doctor-proposing algorithm terminates in at most n² rounds and produces the unique doctor-optimal stable matching — the best stable outcome for doctors, simultaneously, across all possible stable matchings.

**Many-to-one matching:**

Each hospital can take multiple doctors (capacity cⱼ). The algorithm generalises: each round, hospitals hold their top-cⱼ applicants and reject the rest. All main results still hold: stable matchings exist, the algorithm finds the doctor-optimal one, and truth-telling is a dominant strategy for doctors.

---

### Matching in AI and platform design

**Talent marketplaces:**

Platforms like LinkedIn, Toptal, and Upwork are matching markets. The mechanism design challenge: make the platform strategyproof for the weaker side (typically workers/freelancers). If freelancers must game the system to get good matches, they invest effort in presentation over substance — adverse selection returns.

**Content recommendation as matching:**

Recommendation systems can be modelled as one-to-many matching: each user is matched to a set of content items. Stability means no user-content pair both prefers each other over current assignments. Most recommendation systems don't explicitly compute stable matchings — but instability manifests as churn (users who "should" prefer other content but don't see it).

**AI model evaluation committees:**

When assigning models to evaluators in RLHF pipelines, a matching mechanism determines which rater sees which model output. Stability matters: if raters have expertise preferences (a safety researcher should evaluate safety-relevant outputs), an unstable matching wastes expert attention on irrelevant tasks.

**Cloud computing task allocation:**

Assigning computational jobs to compute clusters is a many-to-many matching problem. Jobs have preferences over cluster specifications (GPU type, memory); clusters have capacity constraints. Stable matching algorithms can improve utilisation over greedy allocation.

---

## Where It Breaks / What It Is Not

**Stable matchings are not necessarily efficient.** A stable matching prevents blocking pairs but doesn't maximise total welfare. The doctor-optimal stable matching is best for doctors but worst for hospitals — and "best for doctors" doesn't mean total surplus is maximised.

**Preferences must be submitted honestly (for one side).** The strategyproofness result for doctors depends on them submitting true rankings. If doctors collude or have incorrect beliefs about the algorithm, their submitted rankings may not reflect true preferences, and the algorithm's optimality breaks down.

**The algorithm doesn't handle continuous outcomes.** Real matching markets have salary negotiation, geographic preferences, part-time options, and other continuous dimensions that the binary stable matching model abstracts away. Roth's extensions handle some of these (couples matching, for example), but the clean theory deteriorates with complex real-world constraints.

**Market thickness and congestion.** Matching markets need to be thick (many participants on both sides) to generate good matches. Thin markets produce poor outcomes even with a good algorithm. Market design for thick markets — timing of offers, centralised vs. decentralised mechanisms — is a separate design challenge. This is why the NRMP works: it centralises offers at one moment, creating a thick market.

---

## Try It Yourself

**Exercise 1 — Run Gale-Shapley (15 min)**

Four students (S1–S4) apply to four universities (U1–U4):

Preferences:
```
S1: U1 > U2 > U3 > U4       U1: S2 > S1 > S4 > S3
S2: U1 > U3 > U4 > U2       U2: S1 > S3 > S2 > S4
S3: U2 > U1 > U4 > U3       U3: S3 > S4 > S1 > S2
S4: U3 > U1 > U2 > U4       U4: S4 > S2 > S1 > S3
```

a) Run student-proposing Gale-Shapley. Show each round's proposals, holds, and rejections.
b) Verify the final matching is stable (check that no blocking pair exists).
c) Now run university-proposing Gale-Shapley. What matching does it produce?
d) Are the two matchings the same? Which students are better off under student-proposing?

<details>
<summary>Worked answer (student-proposing)</summary>

**Round 1:** S1→U1, S2→U1, S3→U2, S4→U3.
- U1 holds S2 (prefers S2>S1), rejects S1.
- U2 holds S3 (only applicant).
- U3 holds S4 (only applicant).

**Round 2:** S1→U2.
- U2 holds S1 (prefers S1>S3), rejects S3.

**Round 3:** S3→U1.
- U1 compares S2 and S3. Prefers S2>S3, rejects S3.

**Round 4:** S3→U4.
- U4 holds S3 (only applicant).

**No more rejections. Final matching:** {S1-U2, S2-U1, S3-U4, S4-U3}.

**Stability check (key pairs):**
- S1-U1: S1 prefers U1>U2 (yes). U1 prefers S2>S1 (no — doesn't prefer S1 over current match S2). Not a blocking pair. ✓
- S3-U1: S3 prefers U1>U4 (yes). U1 prefers S2>S3 (no). Not a blocking pair. ✓

**Matching is stable.** (Other pairs also check out.)

</details>

---

**Exercise 2 — Design a hiring matching market (15 min)**

Your company hires 20 engineers per year. You currently use a free-form application process where hiring managers reach out to candidates in an uncoordinated way.

a) Identify the instabilities in your current process. (What blocking pairs are forming? How does uncoordinated outreach create premature offers?)

b) Design a centralised matching mechanism. What preference information do you need from candidates? From hiring managers? What algorithm would you run?

c) What are the incentive compatibility challenges — would candidates honestly report their team preferences? What mechanism design features could make truth-telling dominant?

---

## Connect It Back

Matching markets reveal that market design goes beyond price-setting: when preferences over partners matter, a stable algorithm is needed to prevent the unravelling of coordination. Gale-Shapley gives us an existence result, an algorithm, and a strategyproofness guarantee — the same toolkit as mechanism design, applied to discrete allocation.

Tomorrow (Day 35) is a **rest and synthesise** day — consolidation of everything in Module 3 before the final four days covering cooperative game theory, correlated equilibrium, the price of anarchy, and information design.

**The question you should be able to answer now:**
*Why is the doctor-proposing algorithm strategyproof for doctors but not hospitals — and what does this asymmetry reveal about who holds power in a matching market?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Gale, David & Shapley, Lloyd S. "College Admissions and the Stability of Marriage." *American Mathematical Monthly*, 69(1):9–15, 1962. Read the full seven pages. The marriage matching problem (Section 1), the algorithm (Section 2), and the college admissions generalisation (Section 3) are all here. This is one of the most readable and elegant papers in applied mathematics — a genuine pleasure.

**If you want the deep version:**

1. Roth, Alvin E. "The Evolution of the Labor Market for Medical Interns and Residents: A Case Study in Game Theory." *Journal of Political Economy*, 92(6):991–1016, 1984. — Roth's analysis of the NRMP: why the pre-1952 decentralised market failed, why the 1952 centralized algorithm succeeded, and why the specific algorithm (hospital-proposing, later switched to resident-proposing) mattered for incentive compatibility. The canonical example of market design theory validated by historical data.

2. Roth, Alvin E. *Who Gets What — and Why: The New Economics of Matchmaking and Market Design*. Houghton Mifflin Harcourt, 2015. Chapters 1–3. — Roth's accessible account of matching market design: the residency match, school choice, and kidney exchange. Chapter 3 ("Congestion and Unravelling") is particularly relevant for platform designers facing timing problems. Essential reading if you want the practical implications without the math.

3. Sönmez, Tayfun & Ünver, M. Utku. "Matching, Allocation, and Exchange of Discrete Resources." In *Handbook of Social Economics*, Benhabib, Bisin & Jackson (eds.). Elsevier, 2011. Volume 1A, Chapter 17. — The comprehensive survey of matching theory, covering two-sided matching, house allocation, school choice, and kidney exchange under a unified framework. Read Sections 17.1–17.3 for the core theory; Section 17.5 for the applications.


---

← [Day 33 — Network Effects & Two-Sided Markets](day-33-network-effects-platforms) &nbsp;|&nbsp; [Day 35 — Rest & Synthesise →](day-35-rest-synthesise)
