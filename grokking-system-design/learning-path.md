# Learning Path

> **21 days · 5 modules · one building block at a time**

---

## The Design Philosophy

Each day has exactly one load-bearing idea. Every building block day includes a **Decision Guide** — a structured "choose this when / don't choose this when" section that applies the Day 2 trade-off framework in context. Every Module 5 design day is structured as: Requirements → Options Considered → Trade-off Matrix → Decision + Justification → HLD → LLD excerpt.

---

## Module 1 — The Design Methodology (Days 1–3)

The first three days install the instruments you will use for the rest of the course. No building blocks yet — just the methodology, the decision framework, and the visual grammar.

| Day | Title | Load-Bearing Idea | Prereqs |
|-----|-------|-------------------|---------|
| [1](modules/01-design-methodology/days/day-01-how-to-approach-system-design.md) | How to Approach Any System Design Problem | Every system design starts with the same four steps: clarify functional requirements, define non-functional requirements, estimate capacity, sketch the data flow — in that order, because skipping any step produces a design that solves the wrong problem at the wrong scale. | None |
| [2](modules/01-design-methodology/days/day-02-trade-off-analysis.md) | Trade-Off Analysis: The Framework for Every Design Decision | Every design choice is a bet that certain quality attributes matter more than others for *this specific system* — a repeatable framework (identify options, score against quality attributes, surface the dominant trade-off, decide and record) prevents both analysis paralysis and thoughtless defaults. | Day 1 |
| [3](modules/01-design-methodology/days/day-03-c4-model-vocabulary.md) | Drawing Systems: C4 and the System Design Vocabulary | Four zoom levels — Context → Container → Component → Code — give you exactly the right level of detail for any audience; the grammar is the same whether you are presenting to a junior developer, a product manager, or a CTO. | Day 1 |

---

## Module 2 — The Storage Building Blocks (Days 4–7)

Storage decisions are the most consequential in any system design. Get these wrong and no amount of clever compute or networking fixes them. Each day maps the building block to its Azure service(s) and includes a Decision Guide.

| Day | Title | Load-Bearing Idea | Prereqs |
|-----|-------|-------------------|---------|
| [4](modules/02-storage-building-blocks/days/day-04-relational-databases.md) | Relational Databases: Indexing, Replication, and the Sharding Decision | A relational database buys ACID transactions and flexible queries; the moment you shard it you sacrifice joins and cross-shard transactions — so sharding is a last resort, chosen only when the trade-off analysis shows no other path to the required scale. | Days 1, 2 |
| [5](modules/02-storage-building-blocks/days/day-05-nosql.md) | NoSQL: Document, Key-Value, Wide-Column, and Graph — When Each Wins | NoSQL databases exist because each model optimises a specific access pattern that relational databases handle poorly at scale — applying the trade-off framework to your access pattern, not your team's familiarity, is the only defensible selection method. | Days 2, 4 |
| [6](modules/02-storage-building-blocks/days/day-06-caching.md) | Caching: What to Cache, Where to Cache, and When Not To | A cache is a bet that the same data will be requested again before it changes — the architecture of that bet (cache-aside vs. write-through vs. write-behind; local vs. distributed) determines whether the cache solves the problem or creates a harder one. | Days 2, 4, 5 |
| [7](modules/02-storage-building-blocks/days/day-07-blob-cdn-search.md) | Blob Storage, CDN, and Search | Blob storage externalises large objects from your database; a CDN pushes content to the edge; a search index inverts the query model — three distinct building blocks each solving a different read problem. | Days 2, 4, 5 |

---

## Module 3 — The Compute and Communication Building Blocks (Days 8–12)

How data moves between components is as important as where it is stored. Days 8–11 complete the building block library; Day 12 is the first active synthesis session.

| Day | Title | Load-Bearing Idea | Prereqs |
|-----|-------|-------------------|---------|
| [8](modules/03-compute-communication-building-blocks/days/day-08-load-balancing.md) | Load Balancing and Reverse Proxies: Traffic as a First-Class Concern | A load balancer is not just a traffic splitter — it is the point where routing intelligence, health checking, TLS termination, and geographic distribution are decided; choosing the wrong Azure layer (L4 vs. L7 vs. global) locks in hidden constraints. | Days 1, 2, 3 |
| [9](modules/03-compute-communication-building-blocks/days/day-09-api-design.md) | API Design: REST, gRPC, and WebSockets — Choosing the Right Contract | REST, gRPC, and WebSockets are not style preferences — each optimises a different communication pattern, and the trade-off framework applied to latency, coupling, and client type produces a defensible contract choice. | Days 2, 8 |
| [10](modules/03-compute-communication-building-blocks/days/day-10-async-messaging.md) | Asynchronous Messaging and Event Streaming | A message queue delivers a command to exactly one consumer reliably; an event stream retains a replayable ordered log for many consumers — choosing wrong means rebuilding the messaging layer under production load. | Days 2, 8, 9 |
| [11](modules/03-compute-communication-building-blocks/days/day-11-rate-limiting.md) | Rate Limiting, Throttling, and API Gateways | Rate limiting is the contract between your system and its callers that bounds blast radius; the API gateway is the right enforcement point — choosing algorithm and placement requires the same trade-off framework as any other decision. | Days 2, 8, 9, 10 |
| [12](modules/03-compute-communication-building-blocks/days/day-12-rest-synthesize-i.md) | Rest & Synthesize I — Building Block Selection in Practice | For three different system briefs, apply the trade-off framework to select and justify every storage, compute, and communication building block — and map each to its Azure service. | Days 1–11 |

---

## Module 4 — Distributed Systems Reality (Days 13–16)

Distributed systems have physics. This module adds the constraints — CAP, distributed transactions, failure modes, observability — that determine which building block combinations work under pressure and which collapse silently.

| Day | Title | Load-Bearing Idea | Prereqs |
|-----|-------|-------------------|---------|
| [13](modules/04-distributed-systems-reality/days/day-13-cap-theorem.md) | CAP Theorem and Consistency Models: Choosing Your Guarantee | When a network partition occurs you must choose between consistency and availability — and Cosmos DB exposes this as five named consistency levels, each with a different latency and correctness profile. | Days 2, 4, 5 |
| [14](modules/04-distributed-systems-reality/days/day-14-distributed-transactions.md) | Distributed Transactions and the Saga Pattern | Across service boundaries ACID transactions are impossible at scale; the trade-off between two-phase commit and the Saga pattern is one of the most consequential choices in a distributed .NET Azure system. | Days 2, 10, 13 |
| [15](modules/04-distributed-systems-reality/days/day-15-resilience.md) | Resilience: Designing for Failure, Not Against It | Reliable systems are not systems that never fail — they are systems with bounded failure radius and automatic recovery; circuit breaker, bulkhead, retry, and timeout are the four tools, each for a different failure mode. | Days 2, 8, 10, 14 |
| [16](modules/04-distributed-systems-reality/days/day-16-observability.md) | Observability at Scale: Choosing What to Measure | Observability is a structural property of the system designed in at build time; logs, metrics, and traces answer different questions and carry different costs — the selection must be made deliberately. | Days 2, 15 |

---

## Module 5 — Designing Real Systems (Days 17–21)

Every design day follows the same structure: requirements → options considered → trade-off matrix → decision + justification → HLD → LLD excerpt for the hardest subsystem.

| Day | Title | Load-Bearing Idea | Prereqs |
|-----|-------|-------------------|---------|
| [17](modules/05-designing-real-systems/days/day-17-url-shortener.md) | Designing a URL Shortener — Decision-by-Decision Walkthrough | The URL shortener is "hello world" for system design — small enough to complete in one day, rich enough to exercise the full methodology on three consecutive decisions. | Days 1–16 |
| [18](modules/05-designing-real-systems/days/day-18-notification-system.md) | Designing a Notification System — Choosing Your Fan-Out Strategy | Fan-out-on-write vs. fan-out-on-read is a first-class trade-off with no universally correct answer — the right choice depends on the read/write ratio and follower distribution of the specific system. | Days 10, 15, 17 |
| [19](modules/05-designing-real-systems/days/day-19-social-media-feed.md) | Designing a Social Media Feed — The Read-Heavy Options Matrix | A social media feed requires explicit decisions on five independent axes — and the course makes you generate at least two options per axis before selecting, forcing the framework to operate at full resolution. | Days 6, 13, 18 |
| [20](modules/05-designing-real-systems/days/day-20-rest-synthesize-ii.md) | Rest & Synthesize II — HLD/LLD Templates and the Decision Record | A well-formed HLD, LLD, and Architecture Decision Record — written using the Day 2 framework — is what turns a diagram into an auditable design. | Days 17–19 |
| [21](modules/05-designing-real-systems/days/day-21-capstone.md) | Capstone: Design a Complete System from a One-Line Brief | Produce an HLD, component-level LLD, and one ADR per consequential decision — each ADR must show at least two rejected options and the dominant trade-off that drove the selection. | Days 1–20 |
