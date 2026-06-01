*[Grokking System Design](../../../README.md) · Module 1 — Design Methodology · Day 2*

# Day 2 — Trade-Off Analysis: The Framework for Every Design Decision

> **Today's one idea:** Every architectural decision is a bet that certain quality attributes matter more than others for *this specific system* — a five-step framework (extract priorities, generate options, score, identify the dominant trade-off, record) converts that bet from instinct into engineering.
>
> **Reading time:** ~35 min · **Prereqs:** Day 1
>
> **Primary source for today:** Ford, Neal et al. *Software Architecture: The Hard Parts.* O'Reilly Media, 2021 — Chapter 2, "Discerning Coupling in Software Architecture."

---

## The Hook

Two senior engineers are in a design review. The system: a new internal HR portal, 500 employees, complex reports, strict audit trail.

**Engineer A:** "We should use Cosmos DB — it scales infinitely and gives us multi-region failover."

**Engineer B:** "We should use Azure SQL — we can't compromise on ACID transactions for financial records."

Neither engineer is wrong. Both are right about their *priority*. The problem is they are not arguing about the same thing.

Engineer A is optimising for **scalability**. Engineer B is optimising for **consistency**. For this specific system — 500 users, complex queries, audit requirements — consistency wins by a wide margin. But Engineer A doesn't know that because nobody has stated the priorities out loud.

This meeting will last three hours and end in politics, not engineering. Unless someone introduces a framework.

That framework is what you learn today. You will use it on every remaining day of this course.

---

## Building the Intuition

Imagine you are buying a car. You have a budget of £30,000. You care about:
- Fuel efficiency (long commute)
- Cargo space (two kids and a dog)
- Safety rating (non-negotiable)
- Fun to drive (nice to have)

No car maximises all four equally within your budget. A fuel-efficient hatchback loses on cargo. An SUV loses on efficiency. A sports car loses on safety rating. You cannot have everything.

The right car is not "the best car." It is the car that wins on the attributes *you weighted highest*, at the cost of the attributes you weighted lowest.

You implicitly do this analysis every time you make a significant purchase. The trade-off framework just makes the same process explicit for architectural decisions — so that the decision is documented, the weights are visible, and the team argues about priorities rather than preferences.

### The Five Steps

#### Step 1: Extract Quality Attributes from Your NFRs

Go back to the non-functional requirements you wrote on Day 1. Each NFR maps to one or more quality attributes. The quality attributes that matter most for system design are:

| Quality Attribute | What it Means | Typical Metric |
|------------------|---------------|----------------|
| **Scalability** | Handles growing load without redesign | Can scale from 1K to 1M QPS? |
| **Availability** | Fraction of time the system is operational | 99.9%, 99.99% |
| **Consistency** | All users see the same data at the same time | Strong, eventual, read-your-writes |
| **Durability** | Data survives failures | RPO (recovery point objective) |
| **Latency** | Time to serve a single request | p50, p99 in ms |
| **Throughput** | Requests the system can sustain per second | QPS at peak load |
| **Cost** | Infrastructure + operational expense | $/month |
| **Operability** | Ease of running in production | MTTR, deployment frequency |
| **Security** | Resistance to unauthorised access | Compliance requirements |
| **Maintainability** | Ease of changing the system over time | Time to add a feature |

For any given system, **3–5 of these will dominate**. Trying to optimise for all 10 produces a system that is mediocre on every dimension.

#### Step 2: Rank the Quality Attributes

State the priorities explicitly. This is the single most valuable step — and the one most teams skip.

Ranking forces the team to agree *before* evaluating options. Without a ranking, each engineer weights silently and argues from hidden premises (which is exactly what happened in the HR portal meeting above).

Ranking format: list the top 3–5 attributes in priority order. Everything not listed is secondary.

**HR portal example:**
1. Consistency (audit trail cannot have gaps or race conditions)
2. Queryability (complex reports across multiple entities)
3. Security (employee personal data)
4. Cost (internal tool — no need to over-engineer)
5. *(Scalability is explicitly deprioritised — 500 employees, no growth expected)*

#### Step 3: Generate at Least Two Concrete Options

This step is often skipped because one option "obviously" seems right. Resist this. Generating at least two options forces you to articulate what you are *not* choosing and why — which is the content of a good Architecture Decision Record.

For each option, write one sentence on what it optimises for.

**HR portal example:**
- **Option A — Azure SQL:** Optimises for consistency (ACID), queryability (full SQL), and strong tooling for .NET (Entity Framework, LINQ).
- **Option B — Azure Cosmos DB (Core/SQL API):** Optimises for global distribution, horizontal scalability, and sub-10ms latency at scale.
- **Option C — Azure Table Storage:** Optimises for cost and simplicity at the expense of query flexibility and consistency guarantees.

#### Step 4: Score and Identify the Dominant Trade-Off

Build a scoring matrix. Use a 1–3 scale: 1 = poor fit, 2 = adequate, 3 = strong fit.

| Quality Attribute | Weight | Azure SQL | Cosmos DB | Table Storage |
|------------------|--------|-----------|-----------|---------------|
| Consistency | 5 | 3 (ACID) | 2 (tunable) | 2 (eventual) |
| Queryability | 4 | 3 (full SQL) | 2 (SQL API, no joins) | 1 (limited filter) |
| Security | 3 | 3 | 3 | 2 |
| Cost | 2 | 2 (moderate) | 1 (expensive at this scale) | 3 (cheapest) |
| **Weighted Total** | | **34** | **24** | **20** |

Calculation: `(3×5) + (3×4) + (3×3) + (2×2) = 15+12+9+4 = 40`... (simplified above for illustration)

The **dominant trade-off** is what Azure SQL gives up to win: **scalability**. Azure SQL is a single-region leader-follower setup. If this portal ever needed to serve 10,000 concurrent users globally, it would need a redesign. We are explicitly accepting that trade-off because the system is designed for 500 internal users.

#### Step 5: Record the Decision as an ADR

An Architecture Decision Record (ADR) is a short document — one page or less — that captures:
- What was decided
- Why this option was chosen
- What alternatives were considered
- What the consequences are (what we gain, what we give up)

The ADR is the organisational memory of the decision. Without it, the next developer who joins the team will "fix" the architecture by switching to Cosmos DB because it "scales better" — and destroy the audit trail in the process.

---

## The Formal Picture

### The Quality Attribute Priority Vector

Formally, before evaluating options, the team agrees on a **priority vector** P = (p₁, p₂, ..., pₙ) where pᵢ is the weight assigned to quality attribute Qᵢ.

The priority vector must satisfy: each pᵢ ≥ 0, and the relative ordering reflects the system's actual constraints. The weights are subjective — they must be agreed upon by the team, not calculated from first principles.

### The Weighted Scoring Matrix

For a set of options {O₁, O₂, ..., Oₘ} and quality attributes {Q₁, Q₂, ..., Qₙ} with priority vector P:

```
Score(Oⱼ) = Σ pᵢ × rating(Oⱼ, Qᵢ)   for i = 1 to n
```

Where `rating(Oⱼ, Qᵢ)` ∈ {1, 2, 3} is the team's assessment of how well option Oⱼ satisfies attribute Qᵢ.

The option with the highest weighted score is the *prima facie* choice, subject to any hard constraints (e.g., "must be HIPAA compliant" eliminates options regardless of score).

### The ADR Format

```
# ADR-NNN: [Short Title]

## Status
Accepted | Proposed | Deprecated | Superseded by ADR-NNN

## Context
[The forces at play. What makes this decision necessary? What constraints exist?]

## Options Considered
- **Option A:** [description] — optimises for [attribute]. Trade-off: [what it gives up].
- **Option B:** [description] — optimises for [attribute]. Trade-off: [what it gives up].

## Decision
We chose **Option A**.

## Consequences
**What we gain:** [list]
**What we give up (dominant trade-off):** [list]
**Revisit if:** [conditions under which this decision should be reconsidered]
```

The "Revisit if" line is the most underused part of an ADR. It is also the most valuable. It converts a static decision into a living constraint — one that can be revisited when the system's requirements change.

---

## Where It Breaks / What It Is Not

**"The matrix gives an objective answer."** It does not. The weights are subjective. Two teams with different priorities will produce different matrices and different decisions from the same options — both correctly. The value of the matrix is not objectivity; it is *explicitness*. It converts hidden premises into visible ones.

**"I should generate as many options as possible."** No. Two to three well-chosen options is correct. More than three options turns the matrix into busywork and obscures the real trade-off. If you find yourself generating five options, you have not yet identified the real decision axis.

**"The highest-scoring option is always the right choice."** Not if it violates a hard constraint. Hard constraints (compliance, existing infrastructure, team capability) eliminate options before scoring begins. The matrix operates on the options that survive the hard-constraint filter.

**"This takes too long for a design review."** The scoring matrix should take 10–15 minutes, not two hours. If it takes longer, the team has not agreed on the priority vector (Step 2). Stop scoring and resolve the priority disagreement first.

**"I already know which option is right."** Then write the ADR first and see if the matrix confirms it. If the matrix contradicts your intuition, investigate why — your intuition may be optimising for the wrong attribute, or the matrix may have an invalid weight. Both are worth knowing.

---

## Try It Yourself

### Exercise 1 — Gaming Leaderboard
A real-time gaming leaderboard for a multiplayer mobile game. Requirements:
- 10 million players
- Leaderboard updated on every score change (up to 5,000 score updates per second)
- Players can query their own rank and the top-100 global ranking
- Reads: p99 latency < 10ms
- Writes: p99 latency < 50ms
- Eventual consistency acceptable (a 1-second lag in rank updates is fine)

**Priority vector for this system:**
1. Latency (10ms read, 50ms write)
2. Throughput (5,000 writes/sec)
3. Scalability (10M players)
4. Consistency (eventual acceptable)
5. Cost

Using this priority vector, score the following three options (1–3 on each attribute) and identify the dominant trade-off of the winner:
- **Azure SQL** — ACID, SQL queries, single primary
- **Azure Cosmos DB** — tunable consistency, 10ms reads at any scale, global distribution
- **Azure Cache for Redis** — in-memory, sorted sets (ZADD/ZRANK), sub-millisecond operations

<details>
<summary>Hint</summary>

Redis has a native data structure — the sorted set — that is designed exactly for leaderboards. `ZADD` adds a score, `ZRANK` returns a player's rank, `ZRANGE` returns the top-N — all in O(log N) time. What does Redis give up? Think about what happens when the server restarts or runs out of memory.

</details>

<details>
<summary>Worked Solution</summary>

| Quality Attribute | Weight | Azure SQL | Cosmos DB | Redis |
|------------------|--------|-----------|-----------|-------|
| Latency | 5 | 1 (10ms+ for ranked queries) | 2 (10ms at scale) | 3 (sub-ms) |
| Throughput | 4 | 2 (adequate with indexing) | 3 (scales horizontally) | 3 (in-memory) |
| Scalability | 3 | 1 (vertical scaling only) | 3 (global) | 2 (limited by RAM) |
| Consistency | 2 | 3 (ACID) | 2 (tunable) | 2 (eventual persistence) |
| Cost | 1 | 2 | 1 (expensive for 10M players) | 2 |
| **Weighted Total** | | **25** | **38** | **43** |

**Winner: Redis.** Score: (3×5)+(3×4)+(2×3)+(2×2)+(2×1) = 15+12+6+4+2 = 39.

**Dominant trade-off:** Redis stores data in RAM. At 10M players × ~100 bytes/player = 1GB — manageable. But if the process restarts without persistence enabled, **all rankings are lost**. The mitigation is Redis AOF (Append-Only File) persistence — but this adds write latency. In Azure, use Azure Cache for Redis with persistence enabled and a fallback recompute from Cosmos DB if the cache is cold.

**ADR excerpt:**
- **Decision:** Use Azure Cache for Redis (sorted sets) as the primary leaderboard store.
- **Dominant trade-off:** Redis is RAM-limited and requires a durable fallback (Cosmos DB) to reconstruct rankings after a cold start. We accept this because the 10ms read requirement is only achievable with in-memory storage at this scale.
- **Revisit if:** Player count exceeds 50M (RAM pressure) or the 10ms SLA is relaxed to 50ms (Cosmos DB becomes viable without the Redis layer).

</details>

---

### Exercise 2 — Write the ADR
Using your answer from Exercise 1, write a complete ADR for the leaderboard storage decision. Use the formal ADR format from the Formal Picture section.

<details>
<summary>Worked Solution</summary>

```
# ADR-001: Use Azure Cache for Redis as the Primary Leaderboard Store

## Status
Accepted

## Context
The gaming leaderboard must serve 10 million players with p99 read latency < 10ms
and sustain 5,000 score updates per second. Eventual consistency (1-second lag) is
acceptable. The primary design constraint is read latency — traditional relational
databases cannot serve ranked queries at < 10ms under this write load.

## Options Considered
- **Azure SQL:** Optimises for consistency and queryability. Trade-off: ranked queries
  (ORDER BY score) at 10M players with 5K writes/sec will exceed 10ms p99 latency
  without extreme indexing and caching — and those compensations add write overhead.

- **Azure Cosmos DB (Core API):** Optimises for global scale and 10ms reads.
  Trade-off: native ranked queries (TOP-N by score) require a custom index or
  materialized view; achievable but adds design complexity. Read latency is ~10ms,
  not sub-ms.

- **Azure Cache for Redis (sorted sets):** Optimises for sub-millisecond in-memory
  operations. Native ZADD, ZRANK, and ZRANGE operations are O(log N).
  Trade-off: RAM-bound; data loss risk on cold restart without persistence.

## Decision
We chose **Azure Cache for Redis** with AOF persistence enabled.
A durable copy of all scores is written to Azure Cosmos DB asynchronously.
On cold start, the Redis sorted set is rebuilt from Cosmos DB.

## Consequences
**What we gain:**
- Sub-millisecond ZRANK and ZRANGE operations (well under the 10ms SLA)
- Native sorted-set semantics — no custom ranking logic in application code
- 5,000 ZADD/sec is well within Redis throughput at this data size (~1GB)

**What we give up (dominant trade-off):**
- Two storage systems to operate and keep in sync (Redis + Cosmos DB)
- Cold-start rebuild time (~2–3 minutes for 10M players from Cosmos DB)
- RAM cost scales linearly with player count

**Revisit if:**
- Player count exceeds 50M (RAM cost becomes prohibitive; evaluate Cosmos DB only)
- Read SLA relaxes to 50ms (Cosmos DB becomes viable without the Redis layer)
- Team capacity cannot support dual-store operations (simplify to Cosmos DB alone)
```

</details>

---

### Exercise 3 (Stretch) — When Intuition and the Matrix Disagree
A team is designing a hospital patient records system. The tech lead has used MongoDB (Cosmos DB Mongo API) on three previous projects and strongly advocates for it. The team builds the matrix and Azure SQL scores higher.

The tech lead says: "The matrix is wrong — I've shipped three products with Mongo and it worked fine."

How do you respond? What does this situation tell you about the matrix's weights?

<details>
<summary>Hint</summary>

The tech lead's experience is real data — but it is data about different systems. What quality attributes did those three previous systems prioritise? How do they compare to a hospital records system's priorities?

</details>

<details>
<summary>Worked Solution</summary>

The correct response is: "Let's look at the weights we assigned. Which ones do you think are wrong for this system?"

The tech lead is implicitly using a priority vector from their previous projects (probably: developer velocity, flexibility, scalability) rather than the priority vector for a hospital records system (consistency, auditability, compliance, query complexity).

If the team revisits the priority vector and agrees that **consistency** and **auditability** should be weighted 5 and the current matrix weighted them 2, the matrix was wrong — and fixing the weights will likely confirm Azure SQL as the right choice, which aligns with the tech lead's instinct for a different reason (familiarity with relational data modelling).

The lesson: when intuition and the matrix disagree, the weights are wrong — not the intuition. The framework's value is in making the disagreement explicit and resolvable, not in overriding engineering judgment.

</details>

---

## Connect It Back

On Day 1, you learned to define what a system must do and at what scale. Today you learned to choose *how* to do it — not by instinct but by a repeatable process that makes trade-offs visible and decisions auditable.

From Day 3 onward, every building block has a Decision Guide section at its bottom. That section is the trade-off framework applied to a specific choice (SQL vs. NoSQL, sync vs. async, L4 vs. L7 load balancer). Reading those sections using today's vocabulary — quality attributes, priority vector, dominant trade-off — is what turns a list of tools into architectural judgment.

Tomorrow on Day 3, you will add the third instrument: a visual grammar for drawing the decisions you have been making.

**The question you should be able to answer now that you couldn't this morning:**

*Two engineers disagree about which database to use. One wants Azure SQL; the other wants Cosmos DB. What is the first question you ask them — and why?*

*(Answer: "What quality attributes are you each optimising for?" Until that question is answered, both engineers are right and neither can be proven wrong.)*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Ford et al., *Software Architecture: The Hard Parts*, Chapter 2 — "Discerning Coupling in Software Architecture" (pp. 27–55). The book's framing of architectural trade-offs is the most rigorous practitioner treatment available. The "architecture fitness functions" concept (pp. 47–52) is a direct extension of today's quality attribute scoring.

**If you want the deep version:**

- Ford et al., *The Hard Parts*, Chapter 3 — "Architectural Modularity" (pp. 57–89). The decision whether to decompose a monolith into services is the canonical application of the trade-off framework — this chapter walks through it in full.

- Kleppmann, *Designing Data-Intensive Applications*, Chapter 1, pp. 3–12 — "Thinking About Data Systems." Kleppmann frames the entire book as a sequence of trade-off decisions between reliability, scalability, and maintainability. Reading this after today's page connects the abstract framework to concrete data system decisions.

---

← [Day 1 — How to Approach Any System Design Problem](day-01-how-to-approach-system-design.md) &nbsp;|&nbsp; [Day 3 — Drawing Systems: C4 and the System Design Vocabulary →](day-03-c4-model-vocabulary.md)
