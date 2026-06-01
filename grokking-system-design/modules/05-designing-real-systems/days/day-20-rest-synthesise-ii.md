*[Grokking System Design](../../../README.md) · Module 5 — Designing Real Systems · Day 20*

# Day 20 — Rest & Synthesise II

> **Today's one idea:** Every system design in this course — no matter the domain — solves one of five recurring problems; recognising which problem you're facing is the skill that makes a system design interview look easy.
> **Reading time:** ~30 min (no new material — reflect, extract patterns, build your personal checklist)
> **Prereqs:** All 19 prior days

---

## How to Use This Day

This is your second consolidation day. Day 12 synthesised Modules 1–3 by rebuilding the URL shortener. Today you zoom out further: across all five system designs you've seen (URL shortener, notification system, social media feed, search autocomplete, and the underlying building blocks), what patterns repeat?

This is the cognitive work that makes the difference between a practitioner who has read a lot and one who can think fast in a design session. You are not memorising architectures. You are extracting **reusable decision heuristics** that transfer to systems you've never seen before.

---

## The Five Recurring Problems

Every non-trivial system design question is, at its core, one (or a combination) of these five problems. Once you recognise which one you're facing, the architecture largely follows.

---

### Problem 1 — The Read-Heavy Lookup

**Signature:** The read:write ratio is high (10:1 or more). The dominant operation is "given this key, return this value." Writes are infrequent or can be eventually consistent.

**The recurring solution stack:**
1. Write to the primary store (Cosmos DB or SQL).
2. Cache the hot path in Redis (Cache-Aside, TTL matched to acceptable staleness).
3. Put a CDN in front of static or semi-static responses.
4. Read replicas to distribute read load.

**Examples in this course:**
- URL shortener redirect (Day 12): shortCode → longUrl. Redis + Cosmos DB.
- Product catalogue lookup (Days 4–6): productId → product details. Redis + SQL/Cosmos DB.
- Autocomplete suggestion (Day 19): prefix → top-5 terms. CDN + Redis trie.

**The primary decision:** How stale can the cached value be? That answer determines TTL and whether you need write-through or Cache-Aside.

---

### Problem 2 — The Fan-Out Write

**Signature:** One write event must update N entities. N can range from 10 (order → inventory, email, warehouse) to 50 million (celebrity tweet → all followers).

**The recurring solution stack:**
1. Accept the write quickly (return 202, put on queue).
2. Fan out asynchronously via Service Bus topic (one message, N subscriptions).
3. If N is large (celebrity), use the hybrid approach: fan-out on write for small N, fan-out on read for large N.
4. Idempotent consumers with DLQ for durability.

**Examples in this course:**
- Order creation (Day 14): one order → inventory + payment + email + warehouse.
- Notification send (Day 17): one trigger → push + email + SMS workers.
- Social feed post (Day 18): one post → N followers' feed caches.

**The primary decision:** What is N? If N < ~10,000 and all recipients are equivalent: pure fan-out on write. If N can be millions or recipients have different priorities: hybrid or fan-out on read.

---

### Problem 3 — The Tiered Storage Problem

**Signature:** Data has a lifecycle. Fresh data is accessed frequently; old data rarely. Storing everything at "hot" tier costs too much. Serving everything from cold tier is too slow.

**The recurring solution stack:**
1. Hot tier: in-memory cache (Redis) for actively accessed items.
2. Warm tier: Cosmos DB or Azure SQL for recent/active data.
3. Cold tier: Blob Storage (Cool or Cold tier) for old data, rarely read.
4. Archive tier: Blob Archive or Azure Data Explorer for compliance/analytics data.
5. Lifecycle management policies automate tier transitions.

**Examples in this course:**
- Notification log (Day 17): recent notifications in Cosmos DB; older audit records in Azure Data Explorer.
- Social feed (Day 18): today's posts in Redis; last 30 days in Cosmos DB; older posts in Blob Storage.
- Blob Storage tiers (Day 7): Hot → Cool → Cold → Archive based on last access time.

**The primary decision:** What is the access pattern vs age? Plot access frequency over time — the shape of that curve determines tier boundaries.

---

### Problem 4 — The Pre-Computation Pipeline

**Signature:** The query is too expensive to compute at request time. The answer changes slowly (minutes to hours). The number of distinct queries is bounded (e.g., 26³ prefixes, or a daily top-1000 list).

**The recurring solution stack:**
1. Accept writes into a cheap append store (Event Hubs, Blob Storage).
2. Offline pipeline (Azure Function, Stream Analytics) aggregates and computes results on a schedule.
3. Write pre-computed results to a fast read store (Redis, CDN files).
4. Serve pre-computed results at request time — O(1) read.

**Examples in this course:**
- Autocomplete suggestions (Day 19): hourly pipeline mines query logs → builds ranked trie → serves from CDN/Redis.
- Social feed ranking (Day 18): ML pipeline scores posts for each user offline; feed reads serve pre-ranked results.
- Capacity estimation (Day 1): you pre-compute QPS and storage requirements before designing — not at "system question time."

**The primary decision:** How frequently does the pre-computed result need to refresh? That answer determines batch cadence (hourly, daily) vs near-real-time stream processing.

---

### Problem 5 — The Distributed Coordination Problem

**Signature:** Multiple independent services must agree on a fact or complete a multi-step process atomically. There is no single transaction spanning all services.

**The recurring solution stack:**
1. Saga pattern (orchestration or choreography) for multi-step processes.
2. Outbox Pattern for guaranteed event publishing.
3. Idempotency keys for safe retries.
4. Eventual consistency accepted; compensating transactions handle failures.

**Examples in this course:**
- Order processing (Day 14): inventory + payment + order record + email.
- Notification deduplication (Day 17): guarantee one delivery despite multiple queue deliveries.
- URL creation (Day 12): unique code generation without a global lock.

**The primary decision:** Which step is un-compensatable? Design that step to be last in the saga, or accept its failure mode explicitly.

---

## The Pattern-Matching Cheat Sheet

When you encounter a new system design question, ask these questions in order:

```
1. What is the read:write ratio?
   High (10:1+) → Problem 1 (Read-Heavy Lookup)
   Roughly equal → Problem 5 (Coordination) or base CRUD

2. Does one event affect many recipients?
   Yes → Problem 2 (Fan-Out Write)
   How many? < 10K → fan-out on write; > 10K → hybrid or fan-out on read

3. Does data have a lifecycle (hot → cold)?
   Yes → Problem 3 (Tiered Storage)
   Where is the access cliff? → tier boundary

4. Is the query too expensive for real-time?
   Yes → Problem 4 (Pre-Computation Pipeline)
   How fresh? → batch cadence vs stream processing

5. Does a business transaction span multiple services?
   Yes → Problem 5 (Distributed Coordination)
   Which step is un-compensatable? → design that as last
```

Most real systems combine 2–3 of these. The social feed is Problem 1 (read-heavy) + Problem 2 (fan-out) + Problem 3 (tiered storage). Autocomplete is Problem 1 (read-heavy) + Problem 4 (pre-computation). The skill is recognising which combination applies — and applying the right stack for each.

---

## Self-Assessment Checklist

Before the Capstone tomorrow, confirm you can reconstruct these from memory:

**System designs (close your notes, draw from scratch):**
- [ ] URL shortener — end-to-end. Which Cosmos DB consistency level? Why?
- [ ] Notification system — why per-channel queues? What is in the DLQ?
- [ ] Social feed — when does a user land in the "celebrity" bucket? What changes architecturally?
- [ ] Autocomplete — what is stored at each trie node? When does the CDN serve vs the API?

**Building blocks (state the Decision Guide for each from memory):**
- [ ] When do you shard SQL vs migrate to NoSQL?
- [ ] Cache-Aside in 3 steps.
- [ ] Which Azure LB service for global multi-region routing?
- [ ] Service Bus vs Event Hubs — one sentence each.
- [ ] Circuit breaker states and transition conditions.
- [ ] Which CAP profile does Cosmos DB Session consistency have?
- [ ] Saga: what makes a compensating transaction different from a rollback?

**Design methodology:**
- [ ] The four steps of Day 1's methodology — in order.
- [ ] What does an ADR contain? (6 fields.)
- [ ] The four C4 levels and what each shows.

---

## The Design Session Protocol

When you are in a real architecture review or design interview, use this protocol. It maps directly to Day 1's four-step methodology but is paced for a live conversation:

```
Minutes 0–5:   Requirements
  → Ask: "What are the top 3 features?" "What is the peak load?" 
    "What does failure look like from the user's perspective?"
  → Write functional requirements and 3 quality attribute priorities on the board.

Minutes 5–10:  Capacity estimation (back-of-envelope only)
  → QPS, storage size, bandwidth.
  → State which number will dominate the design.

Minutes 10–15: Identify the problem type
  → "This is primarily a Problem 2 (fan-out) system."
  → Propose the high-level architecture (components only, no details yet).
  → Ask: "Does this match your mental model, or should I adjust?"

Minutes 15–25: One deep-dive
  → "The interesting decision is [X]. Let me walk through the options."
  → Options considered → trade-off matrix → decision.
  → Draw the relevant part of the C4 Container diagram.

Minutes 25–30: Failure modes and scale limits
  → "At 10× load, this breaks here. We would address it by..."
  → Mention 2–3 resilience concerns from your pre-production checklist.
```

---

## Suggested Reading for Today

**No required reading.** Use the time for active reconstruction:

1. **15 minutes:** Without opening any page, draw the full C4 Container diagram for the notification system (Day 17). Include every container, every connection, and the Azure service name for each.

2. **10 minutes:** Write the hybrid fan-out decision in ADR format [(Day 2)](../../01-design-methodology/days/day-02-trade-off-analysis.md) for the social media feed. Status: Accepted. Context, Options, Decision, Consequences.

3. **5 minutes:** Re-read Day 16's pre-production resilience checklist. Pick the three items your current Azure project is most likely missing and write them down.

**If you want optional reading:**
Ford et al., *Software Architecture: The Hard Parts* (O'Reilly, 2021) — Chapter 2, "Discerning Coupling in Software Architecture." Ford's coupling taxonomy maps well to the five recurring problems above: read-heavy = data coupling, fan-out = event coupling, coordination = workflow coupling. Recognising coupling types is the same skill as recognising problem types.

---

← [Day 19 — Search Autocomplete](day-19-search-autocomplete.md) &nbsp;|&nbsp; [Day 21 — Capstone →](day-21-capstone.md)
