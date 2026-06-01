# Bibliography — Annotated Course Shelf

All sources cited in this course, grouped by type. Every entry includes a specific location (chapter / section / timestamp) and a note on which days draw from it.

---

## Foundational Books

### Xu, Alex. *System Design Interview: An Insider's Guide.* Vol. 1.
ByteByteGo, 2020. ISBN 978-1-736049-10-1.

The most widely-used practitioner guide for working through concrete system design problems. Each chapter follows the same requirements → estimation → design arc that Day 1 installs as methodology. Written for developers preparing for system design interviews, but equally valuable as a design reference for production systems. The writing is concise and diagram-heavy — you can read a chapter in 20 minutes and walk away with a complete mental model. Does not go deep on theory; pairs with Kleppmann when you need the "why" behind a decision.

- **Days 1, 17:** Chapter 1 — "Scale from Zero to Millions of Users." The bottom-up view of scaling that complements Day 1's top-down methodology.
- **Day 8:** Chapter 1, "Design a Load Balancer" section.
- **Day 11:** Chapter 4 — "Design a Rate Limiter." The token bucket, leaky bucket, and fixed-window algorithms explained with worked diagrams.
- **Day 17:** Chapter 8 — "Design a URL Shortener." Primary source.
- **Day 18:** Chapter 10 — "Design a Notification System." Primary source.
- **Day 19:** Chapter 11 — "Design a News Feed System." Primary source.
- **Day 6:** Chapter 15 — "Design a Cache System." Excellent companion to the Day 6 building block page.

---

### Xu, Alex & Lam, Sahn. *System Design Interview: An Insider's Guide.* Vol. 2.
ByteByteGo, 2022. ISBN 978-1-736049-13-2.

Volume 2 covers the harder problems omitted from Volume 1: proximity services, distributed message queues, real-time gaming leaderboards, and hotel reservation systems. The design problems in Days 18–19 are calibrated to roughly Volume 1 difficulty; Volume 2 is the next step for learners who want to stretch beyond the capstone.

- **Day 18 (further reading):** Chapter 3 — "Google Maps." Fan-out at a different scale.
- **Day 19 (further reading):** Chapter 5 — "Metrics Monitoring." Observability at feed scale.
- **Capstone (optional):** Chapter 7 — "Hotel Reservation System." A worked example of strong consistency under contention.

---

### Kleppmann, Martin. *Designing Data-Intensive Applications: The Big Ideas Behind Reliable, Scalable, and Maintainable Systems.*
O'Reilly Media, 2017. ISBN 978-1-449-37332-0.

The deepest, most rigorous treatment of why different databases make different trade-offs. Where Xu tells you *what* to choose, Kleppmann tells you *why* the choice is what it is. Every storage and consistency decision in Modules 2 and 4 is grounded here. The book is long and technically dense — the course sends you to specific chapters at specific moments rather than reading it cover to cover. Chapters 1, 2, 3, 5, 7, and 9 are the core; Chapters 10–12 (stream processing) are graduate-level extensions.

- **Day 1 (further reading):** Chapter 1 — "Reliable, Scalable, and Maintainable Applications." The definitive treatment of non-functional requirements for data systems.
- **Day 4:** Chapter 3 — "Storage and Retrieval." Indexes, B-trees, LSM-trees; why they have different read/write profiles.
- **Day 4:** Chapter 5 — "Replication." Leader-follower, multi-leader, leaderless — the theory behind Azure SQL's HA.
- **Day 5:** Chapter 2 — "Data Models and Query Languages." The clearest single-source explanation of document, relational, and graph models.
- **Day 13:** Chapter 9 — "Consistency and Consensus." The theoretical foundation for Cosmos DB's consistency levels; the formal treatment of linearizability vs. eventual consistency.
- **Day 16:** Chapter 1 (pp. 3–23) — the section on maintainability and operability frames observability as a first-class design concern.

---

### Ford, Neal; Richards, Mark; Sadalage, Pramod; Dehghani, Zhamak. *Software Architecture: The Hard Parts: Modern Trade-Off Analyses for Distributed Architectures.*
O'Reilly Media, 2021. ISBN 978-1-492-08689-5.

The only major architecture book whose entire premise is teaching trade-off analysis for distributed systems. Day 2's framework is a distillation of the approach Ford et al. use throughout: surface the competing forces, name the trade-off, make the decision explicit. Especially valuable for the service granularity and data ownership decisions that appear in Days 14 and the capstone.

- **Day 2:** Chapter 2 — "Discerning Coupling in Software Architecture." The vocabulary for naming trade-offs.
- **Day 14:** Chapter 6 — "Pulling Apart Operational Data." The distributed transaction decision in the context of service decomposition.
- **Capstone:** Chapter 3 — "Architectural Modularity." The framing for decomposing a monolith — useful for capstone problems involving legacy systems.

---

### Newman, Sam. *Building Microservices: Designing Fine-Grained Systems.* 2nd ed.
O'Reilly Media, 2021. ISBN 978-1-492-03402-5.

The ground truth on service boundaries, communication patterns, and the operational complexity that service decomposition introduces. Newman writes from direct experience building and advising on microservice-based systems; his advice on when *not* to decompose is as valuable as his advice on how to do it. Maps directly to Azure Kubernetes Service, Azure Service Bus, and Durable Functions design decisions.

- **Day 9:** Chapter 5 — "Communication Styles." REST vs. gRPC vs. events for service-to-service calls.
- **Day 10:** Chapter 4 — "Communication." The distinction between choreography and orchestration; when each is appropriate.
- **Day 14:** Chapter 6 — "Workflow." The Saga pattern, compensating transactions, and the Durable Functions implementation.

---

### Nygard, Michael T. *Release It!: Design and Deploy Production-Ready Software.* 2nd ed.
Pragmatic Bookshelf, 2018. ISBN 978-1-680-50239-8.

Every stability pattern referenced in Day 15 — circuit breaker, bulkhead, timeout, retry — was either named or popularised by Nygard. The book is structured around post-mortems of real production failures and the patterns that would have prevented them. It is the most "why does this matter right now" book on the shelf. The Azure Polly library implements most of these patterns for .NET.

- **Day 15:** Chapter 4 — "Stability Patterns." Circuit breaker, bulkhead, timeout, and retry with backoff — the primary source.
- **Day 15:** Chapter 3 — "Stability Antipatterns." Cascade failures, integration points, and unbounded result sets — the failure modes Day 15 defends against.

---

## Landmark Papers

### DeCandia, Giuseppe et al. "Dynamo: Amazon's Highly Available Key-value Store."
*ACM Symposium on Operating Systems Principles (SOSP)*, 2007. DOI: 10.1145/1294261.1294281.

Dynamo invented consistent hashing, vector clocks, and sloppy quorums. Three ideas that underpin Cosmos DB's multi-region replication model and Azure Cache for Redis's cluster topology. Reading the abstract and Sections 1–4 is sufficient for L1/L2 depth; the full paper is graduate-level.

- **Day 5 (further reading):** Sections 1–4 — the motivation for eventual consistency and the mechanisms Dynamo uses to achieve it.
- **Day 13:** Section 6 — "Versioning of Objects." How vector clocks resolve write conflicts — directly relevant to Cosmos DB multi-write regions.

---

### Chang, Fay et al. "Bigtable: A Distributed Storage System for Structured Data."
*USENIX Symposium on Operating Systems Design and Implementation (OSDI)*, 2006. Available: https://research.google/pubs/pub27898/

Bigtable is the ancestor of HBase, Cassandra, and the Cassandra API in Azure Cosmos DB. Sections 1–3 explain the wide-column data model — rows, column families, and timestamps — intuitively enough to understand why Cosmos DB's Cassandra API exists and when to use it.

- **Day 5 (further reading):** Sections 1–3 — the data model and motivation for wide-column storage.

---

### Ghemawat, Sanjay; Gobioff, Howard; Leung, Shun-Tak. "The Google File System."
*ACM Symposium on Operating Systems Principles (SOSP)*, 2003. DOI: 10.1145/945445.945450.

GFS invented the append-only, chunk-replicated object storage model that Azure Blob Storage and every modern object store descends from. Sections 1–3 provide the intuition; Section 3.1 ("Assumptions") is especially instructive — the design decisions that seem arbitrary make sense once you read the assumptions.

- **Day 7 (further reading):** Sections 1–3 — the motivation for separating large object storage from structured databases.

---

### Gilbert, Seth & Lynch, Nancy. "Brewer's Conjecture and the Feasibility of Consistent, Available, Partition-Tolerant Web Services."
*ACM SIGACT News*, Vol. 33, No. 2, pp. 51–59, 2002. DOI: 10.1145/564585.564601.

The formal proof that a distributed system cannot simultaneously guarantee consistency, availability, and partition tolerance. Section 2 is sufficient for L1/L2 — it states and proves the theorem in three pages. Learners who read this alongside Kleppmann Chapter 9 will understand Cosmos DB's five consistency levels as engineering trade-offs, not arbitrary product choices.

- **Day 13:** Section 2 — the CAP proof. Primary source.

---

### Dean, Jeffrey & Ghemawat, Sanjay. "MapReduce: Simplified Data Processing on Large Clusters."
*USENIX Symposium on Operating Systems Design and Implementation (OSDI)*, 2004. Available: https://research.google/pubs/pub62/

The paper that made parallelism across commodity hardware tractable. Behind Azure HDInsight, Azure Databricks, and the distributed query engine in Cosmos DB. Sections 1–3 give the key insight (split → map → shuffle → reduce) in under 20 minutes of reading.

- **Day 20 (synthesis, further reading):** Sections 1–3 — reframes how you think about data at scale; useful background for designing data-pipeline systems in the capstone.

---

## Video Lectures and Courses

### Malan, David J. "Scalability." CS75 Web Development, Harvard University, 2012.
YouTube search: `CS75 Lecture 9 Scalability Harvard David Malan`. Duration: ~60 min.

A legendary 60-minute lecture that walks through the entire scaling journey — single server to database replication to load balancing to caching — with no assumed background. The single best warm-up for Module 2. Watch it before Day 4 if you want a narrative through-line for the building block days.

- **Recommended before Day 4** as a Module 2 warm-up.
- **Day 8 (further reading):** Timestamp ~25:00–40:00 — load balancing and session affinity explained via HTTP.

---

### Brown, Simon. "The C4 Model for Software Architecture." Conference talks and website, 2018–2023.
Primary resource: https://c4model.com. Also on YouTube: search `Simon Brown C4 model`.

Simon Brown invented C4. The website is the canonical reference: the "Notation" page defines every element precisely, and the "FAQ" page pre-empts the most common questions ("do I have to use all four levels?", "what counts as a Container?"). The Structurizr DSL tool linked from the site lets you write C4 diagrams as code and render them — used in the Day 3 exercise.

- **Day 3:** The "Notation" and "FAQ" sections of c4model.com. Primary source.
- **Day 20:** The "C4 and HLD" section — how C4 maps to document sections.

---

### ByteByteGo YouTube channel. Alex Xu. 2021–present.
https://www.youtube.com/@ByteByteGo

Short (5–15 min) animated explainers for system design concepts and specific system problems. Best watched *after* the corresponding day page, not before — the channel gives you the answer without forcing you through the reasoning. Specific videos called out per day in the "Suggested Readings" sections.

- **Days 17–19:** The system-design walkthrough videos for URL shortener, notification systems, and news feed. Watch after completing the day page.

---

### MIT 6.824 Distributed Systems. MIT OpenCourseWare, 2020 edition.
Lecture videos and materials: https://pdos.csail.mit.edu/6.824/schedule.html

The MIT graduate course that accompanies the Dynamo, GFS, and MapReduce papers. Lectures 3 (GFS), 7 (Raft), and 8 (ZooKeeper) are the most directly relevant to this course. Not required for L1/L2 — flagged as the L3 path for learners who want to go from practitioner to researcher.

- **Day 13 (L3 further reading):** Lecture 7 — Raft consensus. The algorithm behind distributed leader election in Azure's managed services.

---

### Microsoft. "Azure Architecture Center." Microsoft Learn, continuously updated.
https://learn.microsoft.com/en-us/azure/architecture/

The living reference for all Azure-specific building block decisions in this course. The "Reference Architectures" section provides worked Azure system designs. The "Cloud Design Patterns" section maps directly to the building blocks in Modules 2–3. The "Well-Architected Framework" section provides the five-pillar evaluation lens.

- **Days 4–11:** "Cloud Design Patterns" — each building block day links to the relevant pattern(s).
- **Day 12 (synthesis):** "Reference Architectures" — source for the three system briefs used in the synthesis exercise.
- **Day 20 (synthesis):** "Reference Architectures" — annotate one architecture using every lens from the course.

---

## Optional Deeper Dives

- **Fowler, Martin. "Patterns of Enterprise Application Architecture." Addison-Wesley, 2002.** The source for Repository, Unit of Work, and CQRS patterns; Chapter 1's architecture framing remains the clearest in the literature. Relevant to Days 5 and 14.

- **Richardson, Chris. *Microservices Patterns: With Examples in Java.* Manning, 2018.** Deep dive on sagas and CQRS; the patterns are language-agnostic and apply directly to .NET. Chapters 4 and 6 are the most relevant to Days 10 and 14.

- **The Twelve-Factor App. https://12factor.net/** — The free cloud-native application manifesto. Directly relevant to how .NET services on Azure should be structured before being designed at scale. Read it in 30 minutes.

- **High Scalability blog. http://highscalability.com** — Real architecture teardowns of Twitter, YouTube, WhatsApp, Slack, and others. Excellent supplementary reading for Module 5 design days — see how real companies solved the same problems.

- **Brewer, Eric. "CAP Twelve Years Later: How the 'Rules' Have Changed." *IEEE Computer*, Vol. 45, No. 2, pp. 23–29, 2012.** [VERIFY: DOI 10.1109/MC.2012.37] Brewer's own retrospective on the CAP theorem — clarifies the commonly misunderstood "pick two" framing. Pairs with Day 13.
