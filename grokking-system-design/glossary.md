# Glossary

Alphabetical list of key terms. Each entry includes a plain-English definition, a formal definition, and the day it is first introduced. Updated as new terms appear in each day's page.

---

## A

### Architecture Decision Record (ADR)
**Plain English:** A short document that captures a design decision, the options that were considered, the trade-off that tipped the choice, and the consequences — so that future developers understand *why* the system looks the way it does.
**Formal:** A structured record containing: title, status (proposed/accepted/deprecated), context (forces at play), options considered, decision, and consequences.
**Introduced:** Day 2

### Availability
**Plain English:** The fraction of time a system is operational and able to serve requests.
**Formal:** Availability = uptime / (uptime + downtime), typically expressed as a percentage (e.g., 99.9% = 8.76 hours of downtime per year).
**Introduced:** Day 1

---

## B

### BASE (Basically Available, Soft state, Eventually consistent)
**Plain English:** A consistency model for distributed systems that prioritises availability over strict consistency — the opposite of ACID. State may be temporarily inconsistent; all replicas converge over time.
**Formal:** An acronym describing the consistency model of AP-leaning distributed systems: Basically Available (system remains available under failures), Soft state (state may be transitionally inconsistent), Eventually consistent (all replicas converge to the same state given no new writes).
**Introduced:** Day 14

### B-Tree Index
**Plain English:** The data structure most relational databases use to speed up lookups — like the index at the back of a book, organised as a sorted tree so the database can binary-search rather than read every row.
**Formal:** A self-balancing tree where each node holds multiple keys and pointers to child nodes. Lookup cost is O(log N) for N rows. Writes must update the tree, adding write overhead but making reads fast.
**Introduced:** Day 4

### Bandwidth
**Plain English:** The amount of data your system sends or receives per second.
**Formal:** Measured in bytes/second (or KB/s, MB/s, GB/s). Calculated as: requests_per_second × average_response_size_bytes.
**Introduced:** Day 1

### Building Block
**Plain English:** A reusable component — load balancer, database, cache, message broker — that appears in many different system designs in different arrangements.
**Formal:** An architectural primitive with a defined interface, a set of quality attribute trade-offs, and one or more Azure service implementations.
**Introduced:** Day 3

### At-Least-Once Delivery
**Plain English:** A message broker guarantee that every message will be delivered to a consumer at least once — possibly more than once if a failure occurs before ACK. Consumers must be idempotent.
**Formal:** A delivery semantic in which the broker retains a message until it receives an acknowledgement (ACK) from the consumer. If the consumer crashes before ACKing, the broker redelivers. Contrast: at-most-once (may lose messages), exactly-once (expensive, requires distributed transactions).
**Introduced:** Day 10

### Backpressure
**Plain English:** When a downstream component (queue, database) is overwhelmed, it signals the upstream producer to slow down — the system self-regulates rather than collapsing under load.
**Formal:** A flow control mechanism where a consumer's capacity limit propagates upstream to reduce the producer's emission rate. In message brokers: a full queue rejects new messages, forcing producers to retry with backoff.
**Introduced:** Day 10

### BM25 (Best Match 25)
**Plain English:** The default relevance scoring algorithm used by most search engines — short documents get a boost, rare words carry more weight, and repeated terms have diminishing returns.
**Formal:** A probabilistic ranking function that scores a document d for query q as: Σ IDF(t) · (tf(t,d) · (k₁+1)) / (tf(t,d) + k₁ · (1 - b + b · |d|/avgdl)), where IDF is inverse document frequency, tf is term frequency, |d| is document length, and k₁, b are tuning parameters.
**Introduced:** Day 7

### Blue/Green Deployment
**Plain English:** Running two identical production environments (blue and green), deploying new code to the inactive one, and swapping traffic atomically — enabling instant rollback if the new version breaks.
**Formal:** A deployment strategy where two environments are maintained. The live environment (blue) serves production traffic; the new version is deployed to the idle environment (green) and validated. Traffic is switched via DNS, load balancer weight, or deployment slot swap. Rollback = swap back.
**Introduced:** Day 16

### Blob Storage
**Plain English:** A flat key-value store for large binary objects (images, video, documents, backups) — no schema, no queries by content, just "put bytes here / get bytes by key."
**Formal:** An object storage service where objects are identified by a hierarchical key (account → container → blob name). Objects are addressed by URL; retrieval is HTTP GET. Azure Blob Storage supports Hot, Cool, Cold, and Archive access tiers with different cost/latency profiles.
**Introduced:** Day 7

---

## C

### Chaos Engineering
**Plain English:** The practice of deliberately injecting failures (killed processes, added latency, network partitions) into a system to verify that resilience mechanisms work before a real incident exposes that they don't.
**Formal:** A discipline for experimenting on a system in production to build confidence in the system's ability to withstand turbulent conditions. Process: define steady state → hypothesis → inject fault → observe → improve. Azure implementation: Azure Chaos Studio. Pioneered by Netflix (Chaos Monkey, 2011).
**Introduced:** Day 16

### Circuit Breaker Pattern
**Plain English:** When calls to a downstream service start failing consistently, stop making those calls for a period of time — fail fast with a known error rather than waiting and tying up threads. After a cooldown, probe once to see if the service has recovered.
**Formal:** A resilience pattern with three states: CLOSED (normal — all calls pass through), OPEN (failure threshold exceeded — calls fail immediately without network attempt), HALF-OPEN (after break duration — one probe call decides whether to close or re-open). Implemented in .NET via Polly's `AddCircuitBreaker`.
**Introduced:** Day 11

### C4 Model
**Plain English:** A four-level zoom system for drawing software architecture diagrams — like Google Maps zoom levels but for systems.
**Formal:** A hierarchical diagramming approach with four levels of abstraction: System Context (Level 1), Container (Level 2), Component (Level 3), and Code (Level 4). Created by Simon Brown.
**Introduced:** Day 3

### Capacity Estimation
**Plain English:** Back-of-the-envelope math to figure out how much storage, bandwidth, and compute a system needs at the scale it must operate at.
**Formal:** A quantitative analysis producing: QPS (queries per second), storage requirements (bytes), and bandwidth requirements (bytes/second), used to rule out solution classes before detailed design.
**Introduced:** Day 1

### Component (C4)
**Plain English:** A named, well-defined piece of functionality inside a Container — for example, a `UrlShortenerService` class or a `CacheService` module inside an API Container.
**Formal:** C4 Level 3. A grouping of related functionality encapsulated behind a well-defined interface within a Container. Corresponds roughly to a namespace, module, or class cluster.
**Introduced:** Day 3

### Bulkhead Pattern
**Plain English:** Isolate different downstream dependencies into separate resource pools (thread pools, connection pools) so that one slow or failing dependency can only exhaust its own pool — not the shared pool used by everything else.
**Formal:** A resilience pattern that partitions resources into isolated pools per dependency, preventing a failure or slowdown in one pool from cascading to others. Named after watertight compartments in ship hulls. Implemented via separate `IHttpClientFactory` named clients in .NET.
**Introduced:** Day 11

### Canary Deployment
**Plain English:** Routing a small fraction of traffic (e.g., 5%) to a new version while the majority stays on the current version — letting you observe the new version's behaviour under real load before committing to a full rollout.
**Formal:** A progressive delivery strategy where a new version receives a configurable traffic percentage. Metrics are compared between canary and stable populations. Rollout proceeds or halts based on automated or manual gates. Azure Front Door's weighted routing implements this natively.
**Introduced:** Day 16

### CAP Theorem
**Plain English:** A distributed data store cannot simultaneously guarantee Consistency (every read returns the latest write), Availability (every request gets a response), and Partition Tolerance (operation continues despite network splits). Since network partitions are unavoidable, the real choice is CP vs AP.
**Formal:** Gilbert and Lynch (2002) formalisation of Brewer's conjecture: it is impossible for a distributed system to simultaneously provide all three of: Consistency (linearisability — every read reflects the most recent write), Availability (every request receives a non-error response), and Partition Tolerance (operation despite arbitrary message loss between nodes). Partition Tolerance is non-negotiable; the trade-off is C vs A during a partition.
**Introduced:** Day 13

### Cache Stampede (Thundering Herd)
**Plain English:** When a cached value expires and hundreds of concurrent requests all miss and simultaneously query the database, potentially overwhelming it.
**Formal:** A race condition where N concurrent cache misses on the same key generate N concurrent backend reads. Mitigations: TTL jitter (randomised expiry), mutex locks on the cache miss path, background refresh (pre-emptive re-population before expiry).
**Introduced:** Day 6

### Cache-Aside Pattern
**Plain English:** The application code checks the cache first; on a miss it reads from the database and populates the cache itself. The cache is invisible to the write path until a later read.
**Formal:** A lazy-loading caching strategy: (1) check cache; (2) on miss, read from source; (3) write result to cache; (4) return result. Writes invalidate (or update) the cache entry. Also called "lazy loading."
**Introduced:** Day 6

### CDN (Content Delivery Network)
**Plain English:** A globally distributed set of edge servers that cache copies of your content close to users, so a user in Singapore doesn't have to fetch a file from a server in East US.
**Formal:** A network of geographically distributed proxy servers (Points of Presence, PoPs) that cache content at the edge. Reduces latency by serving cached responses from the nearest PoP. Cache behaviour is controlled by HTTP `Cache-Control` headers.
**Introduced:** Day 7

### Compensating Transaction
**Plain English:** A forward transaction that undoes the business effect of a previously completed step in a Saga — not a database rollback, but a new operation that logically reverses the earlier one (e.g., a refund reverses a charge).
**Formal:** In the Saga pattern, a transaction C_i that semantically undoes the effect of transaction T_i after T_i has already committed. Compensating transactions must be idempotent (safe to replay) and eventually succeeding (they cannot permanently fail — use retry queues if necessary).
**Introduced:** Day 14

### Clustered Index
**Plain English:** The physical ordering of rows in a table matches the index — the index IS the table, sorted by the indexed column. There can only be one per table.
**Formal:** An index in which the leaf pages of the B-tree contain the actual row data, sorted by the index key. In SQL Server / Azure SQL, the primary key creates a clustered index by default. Lookup is a single tree traversal; range scans are sequential reads.
**Introduced:** Day 4

### Consistency
**Plain English:** Whether all users of a system see the same data at the same time — or whether some users might temporarily see older values.
**Formal:** In the CAP theorem context, consistency means every read receives the most recent write or an error. More precisely defined by a consistency model (linearizability, sequential consistency, eventual consistency, etc.).
**Introduced:** Day 1

### Container (C4)
**Plain English:** A separately deployable unit of software — a web app, an API service, a database, a message queue, a mobile app.
**Formal:** C4 Level 2. A separately runnable/deployable unit that executes code or stores data. Containers communicate with each other via well-defined interfaces (HTTP, AMQP, SQL, etc.). Not to be confused with Docker containers.
**Introduced:** Day 3

### Context Diagram (C4 Level 1)
**Plain English:** The highest-level diagram — your system as a single box surrounded by the people who use it and the external systems it talks to.
**Formal:** C4 Level 1. Shows a software system in scope and its relationships with users (Persons) and other software systems (Software Systems). No internal structure is visible.
**Introduced:** Day 3

---

## D

### Degradation Contract
**Plain English:** A documented decision table stating what each service does when each of its dependencies fails — the explicit product and engineering agreement about which failures show errors and which show degraded-but-functional experiences.
**Formal:** A per-dependency specification mapping failure modes to fallback responses, UI indicators, and acceptable degradation duration. Implemented via circuit breaker fallbacks, stale-cache fallbacks, and feature flags. Absence of a degradation contract means every failure propagates to a 500 error.
**Introduced:** Day 16

### Dead-Letter Queue (DLQ)
**Plain English:** A separate holding area in a message broker where messages go after they fail processing too many times, so they aren't silently dropped — instead they wait for manual inspection and retry.
**Formal:** A sub-queue in Azure Service Bus (and most message brokers) where messages are moved after exceeding the maximum delivery count or TTL. DLQ messages are not reprocessed automatically; they require operator intervention. Monitor DLQ depth as a critical operational signal.
**Introduced:** Day 10

### Data Flow Sketch
**Plain English:** A first-draft boxes-and-arrows diagram showing how data moves through your system at the highest level — not the final design, just the starting point.
**Formal:** An informal architectural diagram showing the primary data paths between the major components of a system, used to validate that the high-level structure satisfies the functional and non-functional requirements.
**Introduced:** Day 1

### Document Model (NoSQL)
**Plain English:** Stores data as self-contained JSON (or JSON-like) documents. A single read returns all of an entity's data — no JOINs needed.
**Formal:** A data model where the unit of storage is a semi-structured document (typically JSON/BSON). Documents within a collection can have different shapes. Queries target fields within documents. Azure implementation: Cosmos DB Core SQL API or MongoDB API.
**Introduced:** Day 5

### Durability
**Plain English:** Whether data survives failures — crashes, power outages, hardware failures.
**Formal:** The property that committed data persists even in the presence of system failures. Typically quantified as Recovery Point Objective (RPO: how much data can be lost) and Recovery Time Objective (RTO: how long recovery takes).
**Introduced:** Day 1

### Distributed Trace
**Plain English:** A record of the causal chain of events for one request across all services it touched — showing which service, function, and external call was responsible for each millisecond of the total latency.
**Formal:** A collection of spans sharing a trace ID, where each span records a named unit of work (service call, DB query, external HTTP call) with start time, duration, status, and tags. Implemented via W3C Trace Context headers propagated across service boundaries. Azure implementation: Application Insights + OpenTelemetry.
**Introduced:** Day 15

---

## E

### Error Budget
**Plain English:** The allowable failure budget derived from an SLO — if your SLO is 99.9% availability, your error budget is 0.1% of requests that can fail per month before you've violated the SLO.
**Formal:** Error budget = 1 − SLO target. Expressed as a fraction of requests or as downtime duration. Used to balance reliability investment against feature velocity: if the error budget is exhausted, deployments slow; if healthy, development accelerates.
**Introduced:** Day 16

### Exponential Backoff with Jitter
**Plain English:** When retrying a failed request, wait longer before each retry (exponential backoff), and add randomness to the wait time (jitter) so that many clients don't all retry at exactly the same moment.
**Formal:** Retry delay formula: `delay_n = base_delay × 2^n × random(0.75, 1.25)`. Exponential backoff reduces retry throughput; jitter spreads retries over time, preventing a synchronized retry spike (the "thundering herd" at the retry layer).
**Introduced:** Day 11

### Eviction Policy (Cache)
**Plain English:** The rule a cache follows when it is full and must decide which entries to remove to make room for new ones.
**Formal:** An algorithm for selecting cache entries to evict. Common policies: LRU (Least Recently Used — evict the entry not accessed for the longest time), LFU (Least Frequently Used — evict the entry with the fewest total accesses), TTL (Time To Live — evict entries whose age exceeds a configured threshold regardless of access pattern).
**Introduced:** Day 6

---

## F

### Functional Requirement
**Plain English:** What the system must *do* — the features, operations, and user-facing behaviours.
**Formal:** A requirement that specifies a function of the system: what it must do given a set of inputs, what outputs it must produce, and how it must behave. Examples: "users can shorten a URL," "the system must redirect to the original URL within 100ms."
**Introduced:** Day 1

### Fan-Out on Read
**Plain English:** Assembling a user's feed at request time by querying all the accounts they follow — no pre-computation, no write amplification, but potentially slow reads for users who follow many people.
**Formal:** A feed generation strategy where the read path dynamically queries the follow graph and merges posts from all followed accounts. Write cost: O(1). Read cost: O(following_count). Used for celebrity accounts (high follower count makes fan-out on write prohibitive).
**Introduced:** Day 18

### Fan-Out on Write
**Plain English:** Pushing a new post into each follower's pre-assembled feed cache at write time — making reads instant (O(1) list fetch) but creating N write operations for every post.
**Formal:** A feed generation strategy where post creation triggers N cache writes (one per follower). Read cost: O(1). Write cost: O(followers). Used for regular users with bounded follower counts. Impractical for celebrity accounts with millions of followers.
**Introduced:** Day 18

### Four Golden Signals
**Plain English:** The four metrics Google SRE identified as the minimum necessary to monitor any service: Latency (how slow?), Traffic (how much?), Errors (how broken?), Saturation (how full?).
**Formal:** A monitoring framework from Google's SRE book. Latency: request duration distribution (p50/p95/p99). Traffic: requests per second or bytes per second. Errors: rate of failed requests (5xx, exceptions). Saturation: utilisation of the limiting resource (CPU, memory, queue depth, connection pool).
**Introduced:** Day 15

---

## G

### gRPC (Google Remote Procedure Call)
**Plain English:** A framework for calling a function on a remote service as if it were a local function call — using binary encoding (Protocol Buffers) over HTTP/2 for maximum speed, with a `.proto` file as the typed API contract.
**Formal:** An open-source RPC framework from Google. Uses Protocol Buffers for serialisation (binary, schema-defined), HTTP/2 for transport (multiplexed streams, header compression). Supports unary, server-streaming, client-streaming, and bidirectional-streaming call patterns. Not natively supported in browsers without gRPC-Web proxy.
**Introduced:** Day 9

### Graph Model (NoSQL)
**Plain English:** Stores data as nodes (entities) and edges (relationships). Optimised for "who is connected to whom" traversals that would require many self-JOINs in SQL.
**Formal:** A data model consisting of vertices (nodes) and edges (relationships), each with properties. Graph traversals follow edges efficiently without explicit JOINs. Azure implementation: Cosmos DB Gremlin API (Apache TinkerPop).
**Introduced:** Day 5

---

## H

### Health Check Endpoint
**Plain English:** A dedicated URL on your service (typically `/health`) that a load balancer polls to determine if an instance is alive and able to serve traffic. Returns 200 if healthy, 503 if not.
**Formal:** An HTTP endpoint that performs liveness and/or readiness checks and returns a structured health status. In ASP.NET Core: configured via `AddHealthChecks()` with checks for critical dependencies (SQL, Redis). Readiness checks (can I serve requests?) are used for load balancer routing; liveness checks (is the process alive?) are used for container orchestrator restarts.
**Introduced:** Day 8

### Hot Partition (Hotspot)
**Plain English:** When all or most requests land on one shard in a sharded database — because the shard key was chosen poorly — overloading that node while others sit idle.
**Formal:** A load distribution failure in a horizontally partitioned system where one partition receives disproportionately high request volume. Caused by shard keys with low cardinality or sequential values (e.g., date-based keys in time-series workloads). Mitigated by hash-based sharding or composite shard keys.
**Introduced:** Day 4

---

## I

### Idempotent Consumer
**Plain English:** A message consumer that produces the same result whether it processes a message once or multiple times — essential because message brokers guarantee at-least-once delivery, not exactly-once.
**Formal:** A consumer implementation that checks whether a given message ID has already been processed (using a deduplication store such as Redis or a DB table) before executing its business logic. If already processed, it ACKs the message and skips without re-executing.
**Introduced:** Day 10

### Inverted Index
**Plain English:** A search engine's core data structure — instead of mapping documents to words, it maps every word to the list of documents containing it, enabling fast keyword lookup.
**Formal:** A data structure that maps tokens (terms) to the ordered list of document identifiers (postings list) in which each token appears. Lookup cost: O(1) per token (hash map) + merge of postings lists. The basis of full-text search engines including Lucene and Azure AI Search.
**Introduced:** Day 7

---

## K

### KQL (Kusto Query Language)
**Plain English:** The query language used in Azure Monitor Logs, Application Insights, and Azure Data Explorer — SQL-like but designed for log and time-series analytics.
**Formal:** A read-only query language for Azure Monitor Logs. Pipe-based: `table | operator | operator`. Key operators: `where`, `summarize`, `project`, `join`, `render`. Used for ad-hoc investigation, alert rule definitions, and dashboard queries.
**Introduced:** Day 15

### Key-Value Model (NoSQL)
**Plain English:** The simplest possible data model — a dictionary where you look things up by key and get back a blob of data. Extremely fast, but you can only query by exact key.
**Formal:** A data model where each entry is a key → value pair. Operations: GET(key), SET(key, value), DELETE(key). No secondary indexes; queries by value are not supported natively. Azure implementations: Azure Cache for Redis (in-memory), Azure Table Storage (durable).
**Introduced:** Day 5

---

## L

### Layer 4 / Layer 7 Load Balancing
**Plain English:** A Layer 4 load balancer routes by IP and port (fast, no HTTP awareness). A Layer 7 load balancer reads the HTTP request (URL path, headers, cookies) and can make content-based routing decisions.
**Formal:** OSI model layers. Layer 4 (Transport): routes based on IP/TCP/UDP headers — no payload inspection. Layer 7 (Application): inspects HTTP/HTTPS content for routing decisions, TLS termination, WAF enforcement. Azure: Load Balancer = L4; Application Gateway and Front Door = L7.
**Introduced:** Day 8

### Leader-Follower Replication
**Plain English:** One database node (the leader) accepts all writes; one or more replica nodes (followers) receive a copy of every write and serve read traffic. If the leader fails, a follower can be promoted.
**Formal:** A replication topology where one primary node handles writes and replicates the write-ahead log to one or more secondary nodes. Secondaries serve reads and can be promoted to primary on leader failure. In Azure SQL, implemented via Always On Availability Groups. Synchronous replication guarantees no data loss; asynchronous replication allows lag.
**Introduced:** Day 4

---

## M

### Linearisability
**Plain English:** The strongest consistency guarantee in distributed systems — every read returns the most recent write, as if all operations happened on a single machine in a single total order.
**Formal:** A correctness condition for concurrent systems where every operation appears to take effect atomically at some point between its invocation and response, and the order of operations is consistent with real time. Equivalent to "CAP's C." Requires quorum reads/writes; adds latency proportional to round-trip to the farthest replica.
**Introduced:** Day 13

### Message Broker
**Plain English:** A middleman service that accepts messages from producers and delivers them to consumers — decoupling the two in time, space, and failure domain.
**Formal:** An infrastructure component that implements a messaging channel with persistence, routing, and delivery guarantees. Producers publish; consumers subscribe or poll. Azure implementations: Azure Service Bus (queue/topic model), Azure Event Hubs (log model), Azure Event Grid (push/webhook model).
**Introduced:** Day 10

---

## N

### Non-Functional Requirement (NFR)
**Plain English:** How well the system must do what it does — the "-ilities": availability, reliability, scalability, security, latency, etc.
**Formal:** A requirement that constrains how the system fulfils its functional requirements. Quantifiable examples: "99.9% availability," "p99 latency < 200ms," "support 10,000 concurrent users." Also called quality attributes or system qualities.
**Introduced:** Day 1

---

## Q

### Quality Attribute
**Plain English:** A dimension along which a system can be evaluated — scalability, availability, consistency, latency, cost, security, maintainability.
**Formal:** A measurable or testable property of a system used to indicate how well the system satisfies the needs of its stakeholders. Synonymous with non-functional requirement in the system design context.
**Introduced:** Day 2

### Observability
**Plain English:** The property of a system that lets you understand its internal state from its external outputs — metrics, traces, and logs — without having to redeploy or attach a debugger.
**Formal:** A system property derived from control theory: a system is observable if its internal state can be inferred from its outputs. In software: observable systems emit structured telemetry (metrics, distributed traces, logs) sufficient to answer arbitrary questions about their behaviour without prior knowledge of what questions will be asked.
**Introduced:** Day 15

### Outbox Pattern
**Plain English:** Save a message to a database table in the same transaction as the business data, then have a background process publish it to the message broker — guaranteeing that a message is always published if and only if the DB write succeeded.
**Formal:** A reliability pattern that atomically writes both the business entity and a pending outbox record to the database. A separate relay process reads uncommitted outbox records and publishes them to the message broker, then marks them as processed. Guarantees at-least-once message publishing without distributed transactions.
**Introduced:** Day 10

### Partition Key (NoSQL)
**Plain English:** The field whose value determines which shard or partition a document lives on. Choosing it well distributes load evenly; choosing it poorly creates hotspots.
**Formal:** A field used to compute the partition assignment of a document in a horizontally partitioned NoSQL store. Documents with the same partition key value are co-located. In Azure Cosmos DB, the partition key is immutable after document creation.
**Introduced:** Day 5

### QPS (Queries Per Second)
**Plain English:** How many requests your system must handle every second.
**Formal:** Throughput measured as the number of requests processed per second. Calculated as: daily_requests / 86,400. Used to size compute, load balancers, and database connection pools.
**Introduced:** Day 1

---

## R

### PACELC Theorem
**Plain English:** An extension of CAP: even when there is no partition, you still trade off Latency against Consistency. Every distributed data store has a dial between "fast but possibly stale" and "slow but definitely current."
**Formal:** If a Partition occurs: choose between Availability and Consistency (CAP). Else (normal operation): choose between Latency and Consistency. Stated as: PAC | ELC. Cosmos DB's five consistency levels are a PACELC dial: Strong = EC (high C, high L); Eventual = EL (low L, low C).
**Introduced:** Day 13

### Rate Limiting
**Plain English:** Capping how many requests a client (or all clients combined) can make in a given time window — to protect your service from overload, enforce fair use, and support API monetisation tiers.
**Formal:** A traffic control mechanism that measures request rate per identity (IP, user ID, subscription key) and rejects requests exceeding a configured threshold, returning HTTP 429 Too Many Requests with a `Retry-After` header. Common algorithms: token bucket, sliding window, fixed window.
**Introduced:** Day 11

### REST (Representational State Transfer)
**Plain English:** An API style that organises endpoints around resources (nouns) acted on with HTTP verbs (GET, POST, PUT, DELETE) — the most widely used API style for public and partner-facing interfaces.
**Formal:** An architectural style defined by Fielding (2000) with six constraints: client-server, stateless, cacheable, uniform interface, layered system, code-on-demand (optional). In practice: resource-based URLs, HTTP verbs map to CRUD operations, JSON responses, HTTP status codes carry semantics.
**Introduced:** Day 9

### Read Replica
**Plain English:** A copy of a database that receives writes from the primary and serves read traffic — offloading reads so the primary can focus on writes.
**Formal:** A secondary database node that replicates data from the primary (leader) and serves SELECT queries. Read replicas increase read throughput and can act as standby for failover. Writes must go to the primary; replicas may lag behind by milliseconds to seconds depending on replication mode.
**Introduced:** Day 4

### Read/Write Ratio
**Plain English:** For every write operation, how many read operations happen — tells you whether your system is read-heavy or write-heavy, which shapes every storage and caching decision.
**Formal:** The ratio of read operations to write operations in a workload over a given time period. A 10:1 ratio means 10 reads for every write. Read-heavy workloads favour caching and read replicas; write-heavy workloads favour write-optimised storage engines.
**Introduced:** Day 1

---

## S

### Saga Pattern
**Plain English:** A way to implement a multi-step business process that spans multiple services — each step commits locally, and if a later step fails, previously committed steps are undone by running compensating transactions in reverse.
**Formal:** A distributed transaction pattern where a long-lived transaction is decomposed into a sequence of local transactions T₁, T₂, ..., Tₙ, each with a compensating transaction C_i. If Tₖ fails, execute Cₖ₋₁, Cₖ₋₂, ..., C₁ in reverse. Two coordination styles: choreography (event-driven, decentralised) and orchestration (central coordinator manages step sequence).
**Introduced:** Day 14

### SAS Token (Shared Access Signature)
**Plain English:** A cryptographically signed URL that grants time-limited, scoped permission to perform a specific operation on an Azure Blob Storage object — without exposing your storage account key.
**Formal:** A URI component appended to a storage resource URL that grants delegated access. Parameters: permissions (read/write/delete/list), start time, expiry time, resource scope (blob, container, or account). Signed with the storage account key or a user delegation key (AAD-based).
**Introduced:** Day 7

### Scalability
**Plain English:** How well the system handles growing load — more users, more data, more requests — without degrading.
**Formal:** The ability of a system to maintain acceptable performance as load increases, typically achieved through horizontal scaling (adding more nodes) or vertical scaling (upgrading node capacity).
**Introduced:** Day 1

### SLO (Service Level Objective)
**Plain English:** A target for how reliable a service should be — for example, "99.9% of requests complete in under 500ms." The SLO is the internal goal that the team commits to; it drives alerting thresholds and error budget calculations.
**Formal:** A quantified reliability target for a specific metric over a rolling time window. Distinguished from SLA (Service Level Agreement — a contractual commitment with penalties) and SLI (Service Level Indicator — the raw measurement). Example: SLI = p99 latency; SLO = p99 latency < 500ms over 30 days.
**Introduced:** Day 15

### Sharding (Horizontal Partitioning)
**Plain English:** Splitting a database table across multiple machines by row — each machine holds a subset of the data. Needed when a single machine can no longer hold or serve the full dataset.
**Formal:** A horizontal scaling technique where rows are distributed across N nodes (shards) based on a shard key. A routing layer maps requests to the correct shard. JOINs across shards require scatter-gather (fan-out to all shards + merge), which is expensive. Distinguished from vertical partitioning (splitting by column).
**Introduced:** Day 4

### Software System (C4)
**Plain English:** In C4, a "system" is the thing you are designing — your software, treated as a black box at Level 1, then opened up at Level 2.
**Formal:** C4 Level 1 element. The highest-level abstraction — a system that delivers value to its users. May be decomposed into Containers at Level 2.
**Introduced:** Day 3

---

## T

### Storage Tier (Blob)
**Plain English:** Azure Blob Storage's four cost/access trade-off levels: Hot (frequent access, higher storage cost), Cool (infrequent access), Cold (rare access), Archive (near-zero storage cost, hours to retrieve).
**Formal:** A storage access classification that determines per-GB storage price, per-operation price, and retrieval latency. Lifecycle management policies can transition blobs between tiers automatically based on last-modified or last-access time.
**Introduced:** Day 7

### Trie (Prefix Tree)
**Plain English:** A tree data structure where each node represents one character and the path from the root to a node spells out a prefix — used for autocomplete because prefix lookups are O(prefix_length) regardless of how many words share that prefix.
**Formal:** An ordered tree where each node stores one character and a (optional) set of top-K suggestions for the prefix represented by the path to that node. Lookup: traverse the tree character by character. Space optimisation: store top-K at each node during offline construction to avoid subtree traversal at query time.
**Introduced:** Day 19

### Token Bucket Algorithm
**Plain English:** A rate limiting algorithm that allows short bursts of requests (up to bucket capacity) while enforcing a long-term rate limit (the refill rate). Like a bucket that drains with each request and slowly refills.
**Formal:** Each requester has a virtual bucket of capacity T tokens. Tokens are added at rate R/second up to capacity T. Each request consumes one token; if the bucket is empty, the request is rejected. Burst capacity = T; sustained throughput = R req/s.
**Introduced:** Day 11

### Throughput
**Plain English:** How much work the system can do per unit of time — requests per second, transactions per second, bytes per second.
**Formal:** The rate at which a system processes requests or data, measured over a sustained period. Distinct from latency: a system can have high throughput and high latency (batch processing) or low throughput and low latency (interactive queries).
**Introduced:** Day 1

### Trade-Off Analysis
**Plain English:** The structured process of identifying competing design options, scoring them against the system's quality attribute priorities, and making a justified selection.
**Formal:** A decision-making process that (1) extracts quality attribute priorities from non-functional requirements, (2) generates at least two concrete options, (3) scores each option on each attribute, (4) identifies the dominant trade-off, and (5) records the decision as an ADR.
**Introduced:** Day 2

### Trade-Off (Dominant)
**Plain English:** The single most important dimension where your chosen option loses to the runner-up — the cost you are consciously accepting by making this decision.
**Formal:** The quality attribute(s) on which the selected option scores lower than an alternative, explicitly acknowledged in the ADR as the price paid for winning on higher-priority attributes.
**Introduced:** Day 2

---

## W

### Wide-Column Model (NoSQL)
**Plain English:** Organises data into rows with a row key, but each row can have millions of columns and different rows can have completely different columns. Optimised for time-series and append-heavy workloads.
**Formal:** A data model descended from Google Bigtable. Data is organised into tables with a mandatory partition key and optional clustering column(s). Each row can have sparse, variable columns. Reads and writes are efficient when scoped to a single partition key. Azure implementation: Cosmos DB Cassandra API (Apache Cassandra-compatible CQL).
**Introduced:** Day 5

### Two-Phase Commit (2PC)
**Plain English:** A classical distributed transaction protocol where a coordinator asks all participants to "prepare" (lock resources), then sends either a "commit" or "abort." Falls apart when the coordinator crashes between prepare and commit — leaving participants holding locks indefinitely.
**Formal:** A distributed consensus protocol. Phase 1 (prepare): coordinator sends PREPARE to all participants; each replies READY or ABORT. Phase 2 (commit): if all READY, coordinator sends COMMIT; if any ABORT, sends ABORT. The coordinator is a SPOF; in-doubt transactions occur if the coordinator fails after phase 1. Not supported across Azure service boundaries.
**Introduced:** Day 14

### Write-Behind Pattern (Cache)
**Plain English:** The application writes to the cache immediately and returns; the cache asynchronously flushes the update to the database in the background. Fastest writes, but risk of data loss if the cache node fails before flushing.
**Formal:** A caching write strategy where (1) the write goes to the cache synchronously, (2) the client receives acknowledgement, and (3) the cache propagates the write to the primary store asynchronously. Provides lowest write latency but weakest durability guarantee.
**Introduced:** Day 6

### Write-Through Pattern (Cache)
**Plain English:** Every write goes to both the cache and the database synchronously before returning. The cache is always consistent with the database, but writes are slower.
**Formal:** A caching write strategy where every write updates both the cache and the source-of-truth store in a single synchronous operation before returning. Eliminates stale cache entries at the cost of higher write latency. Works best when reads substantially outnumber writes.
**Introduced:** Day 6

### Weighted Scoring Matrix
**Plain English:** A table where rows are your options, columns are quality attributes, and each cell is a score — with column weights applied to make higher-priority attributes count more.
**Formal:** A multi-criteria decision analysis tool. Score_i = Σ(weight_j × rating_ij) for each option i across all quality attributes j. The option with the highest weighted score is preferred, subject to any hard constraints.
**Introduced:** Day 2
