# Grokking System Design

> **The promise:** Given any product brief, you can produce a defensible High-Level Design and Low-Level Design for an Azure-native system — with every key decision justified against a concrete trade-off analysis.

---

## Who This Course Is For

This course is for **.NET software developers** working in the **Azure ecosystem** who can already build things — wire up an API, configure a database, deploy a service — but have never been taught to *design* them. You know how to answer "how do I implement this?" but feel uncertain when asked "what should the architecture look like?" or "why did you choose Cosmos DB over Azure SQL?"

The target depth is **L1 (Practitioner) and L2 (Builder)**. You will be able to reason about trade-offs, select the right Azure service for a given problem, and produce HLD and LLD documents that a team can build from. You will not need to read academic papers to use this course, though pointers to landmark papers are included for those who want to go deeper. If you are looking for a graduate-level distributed systems theory course, this is not it — this course is optimised for developers who design and ship real Azure workloads.

---

## The Arc at a Glance

On Day 1, you arrive knowing how to build things in Azure but not how to design them. You can spin up an Azure SQL database or a Redis cache, but if asked "why did you choose those two together?" the honest answer is "that's what the tutorial used." By Day 3, you have a methodology — a repeatable four-step process for decomposing any design problem — and a trade-off framework that turns architectural debates from politics into engineering. You also have C4, a visual grammar that lets you draw any system at exactly the right level of detail for any audience.

The course's spine is a **building block library**: a small, reusable set of components — relational databases, NoSQL stores, caches, blob stores, load balancers, API gateways, message brokers — that every large-scale system combines in different arrangements. Modules 2 and 3 build this library piece by piece, always tethering each component to its Azure incarnation and its quality attribute profile. Module 4 adds the physics: the distributed systems constraints (CAP, distributed transactions, failure modes, observability) that determine which combinations work under pressure and which collapse silently. Module 5 is where the library becomes architecture — three progressively complex design problems that force you to select, combine, and justify building blocks as real HLDs and LLDs. The capstone on Day 21 removes all scaffolding: one sentence brief, one complete design — the first one that is entirely yours.

---

## How to Use This Course

- **Rhythm:** One page per day, 30–45 minutes of focused reading. Do not read ahead — each page assumes you have internalised the previous ones.
- **Exercises:** Non-optional for L2 depth. If you skip the "Try it yourself" sections, you will recognise the concepts but be unable to apply them under pressure. For L1, at minimum do Exercise 1 on each day.
- **Bibliography:** Consult `bibliography.md` when a day's further-reading annotation says "worth 15 extra minutes." Do not pause the daily rhythm to read entire books — the course is designed to send you to specific chapters at specific moments.
- **Rest and Synthesize days** (Days 12 and 20): These are not optional rest days. They are active consolidation sessions with a specific exercise. Treat them as the most important days of the course — the concepts only stick if you retrieve and apply them.
- **If life gets in the way:** Missing one day is fine. Missing three consecutive days means you should re-read the last two days before continuing. The course is cumulative — each day builds on the previous ones' vocabulary.

---

## The Learning Path

| Day | Title | One Idea | Core Source |
|-----|-------|----------|-------------|
| [1](modules/01-design-methodology/days/day-01-how-to-approach-system-design.md) | How to Approach Any System Design Problem | Every design starts with requirements, then estimation, then data flow — in that order | Xu, *System Design Interview* Vol 1, Ch 1 |
| [2](modules/01-design-methodology/days/day-02-trade-off-analysis.md) | Trade-Off Analysis: The Framework for Every Design Decision | Design decisions are bets on quality attribute priorities — make those bets explicit and traceable | Ford et al., *The Hard Parts*, Ch 2 |
| [3](modules/01-design-methodology/days/day-03-c4-model-vocabulary.md) | Drawing Systems: C4 and the System Design Vocabulary | Four zoom levels give you the right level of detail for any audience | Brown, c4model.com |
| [4](modules/02-storage-building-blocks/days/day-04-relational-databases.md) | Relational Databases: Indexing, Replication, and the Sharding Decision | Sharding is a last resort — exhaust every other option first | Kleppmann, *DDIA*, Ch 3, 5 |
| [5](modules/02-storage-building-blocks/days/day-05-nosql.md) | NoSQL: Document, Key-Value, Wide-Column, and Graph | Each NoSQL model optimises a specific access pattern — match model to pattern, not to trend | Kleppmann, *DDIA*, Ch 2 |
| [6](modules/02-storage-building-blocks/days/day-06-caching.md) | Caching: What to Cache, Where to Cache, and When Not To | A cache is a bet that data will be re-requested before it changes — the placement and invalidation logic determines whether that bet pays off | Xu, *System Design Interview* Vol 1, Ch 15 |
| [7](modules/02-storage-building-blocks/days/day-07-blob-cdn-search.md) | Blob Storage, CDN, and Search | Three building blocks for three non-relational read patterns: large objects, geographically distributed static content, and inverted queries | Azure Architecture Center |
| [8](modules/03-compute-communication-building-blocks/days/day-08-load-balancing.md) | Load Balancing and Reverse Proxies | A load balancer is a traffic policy engine, not just a traffic splitter | Xu, *System Design Interview* Vol 1, Ch 1 |
| [9](modules/03-compute-communication-building-blocks/days/day-09-api-design.md) | API Design: REST, gRPC, and WebSockets | REST, gRPC, and WebSockets optimise different communication patterns — match protocol to pattern | Fielding, REST Dissertation, Ch 5 |
| [10](modules/03-compute-communication-building-blocks/days/day-10-async-messaging.md) | Asynchronous Messaging and Event Streaming | A queue delivers a command reliably; a stream retains a replayable log — confusing them breaks the design | Newman, *Building Microservices*, Ch 4 |
| [11](modules/03-compute-communication-building-blocks/days/day-11-rate-limiting.md) | Rate Limiting, Throttling, and API Gateways | Rate limiting is the contract between your system and its callers — the API gateway is the right enforcement point | Xu, *System Design Interview* Vol 1, Ch 4 |
| [12](modules/03-compute-communication-building-blocks/days/day-12-rest-synthesize-i.md) | Rest & Synthesize I — Building Block Selection in Practice | Apply the trade-off framework to three real system briefs and produce justified building block selections | — |
| [13](modules/04-distributed-systems-reality/days/day-13-cap-theorem.md) | CAP Theorem | When a network partition occurs, the system must choose between Consistency and Availability — there is no option that provides both | Gilbert & Lynch, ACM SIGACT News, 2002 |
| [14](modules/04-distributed-systems-reality/days/day-14-distributed-transactions.md) | Distributed Transactions | The Saga pattern replaces ACID across service boundaries with compensating transactions — trading consistency for availability | Newman, *Building Microservices*, Ch 6 |
| [15](modules/04-distributed-systems-reality/days/day-15-observability.md) | Observability | Logs, metrics, and distributed traces answer different questions — and the instrumentation cost must be paid at design time | Kleppmann, *DDIA*, Ch 1; Google SRE Book, Ch 6 |
| [16](modules/04-distributed-systems-reality/days/day-16-resilience-at-scale.md) | Resilience at Scale | Individual resilience patterns protect a single service; resilience at scale means designing the whole system to degrade gracefully | Nygard, *Release It!*, Ch 2, 4 |
| [17](modules/05-designing-real-systems/days/day-17-notification-system.md) | Designing a Notification System | Fan-out at scale requires a tiered queue architecture — one channel-specific queue per delivery provider | Xu, *System Design Interview* Vol 1, Ch 10 |
| [18](modules/05-designing-real-systems/days/day-18-social-media-feed.md) | Designing a Social Media Feed | Fan-out-on-write vs. fan-out-on-read is a first-class trade-off determined by read/write ratio and follower distribution | Xu, *System Design Interview* Vol 1, Ch 11 |
| [19](modules/05-designing-real-systems/days/day-19-search-autocomplete.md) | Designing a Search Autocomplete | Autocomplete is a pre-computation problem — the magic is a background pipeline, not fast lookup at query time | Xu, *System Design Interview* Vol 1, Ch 13 |
| [20](modules/05-designing-real-systems/days/day-20-rest-synthesise-ii.md) | Rest & Synthesise II — HLD/LLD Templates and the Decision Record | An HLD has five sections; an LLD adds contracts and sequences; an ADR captures the why | — |
| [21](modules/05-designing-real-systems/days/day-21-capstone.md) | Capstone: Design a Complete System from a One-Line Brief | Produce an HLD, LLD, and ADRs for a system from scratch — every decision must show options considered and the trade-off accepted | — |

---

## The Course Shelf — Top 5 Must-Reads

| # | Source | Why |
|---|--------|-----|
| 1 | Xu, *System Design Interview* Vol 1 (2020) | The practical spine — each system design problem in Module 5 draws from a chapter here |
| 2 | Kleppmann, *Designing Data-Intensive Applications* (2017) | The theoretical depth — every storage and consistency decision in Module 4 is grounded here |
| 3 | Ford et al., *Software Architecture: The Hard Parts* (2021) | The trade-off methodology — Day 2's framework is distilled from this book |
| 4 | Newman, *Building Microservices*, 2nd ed. (2021) | The distributed services reality — Days 10 and 14 come directly from this |
| 5 | Brown, Simon. C4 Model (c4model.com) | The visual grammar — used on every HLD and LLD in the course |

Full annotated shelf: [bibliography.md](bibliography.md)

---

## The Capstone

On Day 21 you receive a one-sentence product brief — for example, "Design a multi-tenant SaaS platform for processing and reporting on IoT sensor data from Azure-connected devices" — and produce three documents: a complete HLD (context, containers, key data flows, quality attribute decisions, open questions), a component-level LLD for the hardest subsystem, and one Architecture Decision Record per consequential design choice. Each ADR must name at least two rejected options, score them against the system's quality attributes, and state the dominant trade-off that drove the selection. A design is not done until the ADRs exist. This is the bar.

---

## Glossary

[glossary.md](glossary.md) — key terms, plain-English and formal definitions, day of first use.

---

## Meta

- **Depth level:** L1 (Practitioner) and L2 (Builder)
- **Estimated total hours:** ~13 hours reading + ~4 hours exercises = ~17 hours
- **Last updated:** 2026-05-12
- **Feedback / corrections:** Open an issue or submit a pull request against this repository. For factual errors in source citations, please include the corrected reference.
