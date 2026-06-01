# Module 4 — Distributed Systems Reality

**Days 13–16 · 4 pages · ~2.5 hours**

---

## What This Module Earns You

Modules 2 and 3 gave you the building blocks: databases, caches, queues, load balancers, circuit breakers. Module 4 confronts the **fundamental impossibilities** that govern all of them — the hard physical and mathematical limits that no amount of clever engineering can eliminate, only manage.

By the end of this module you will be able to:

- **Day 13 — CAP Theorem:** Explain, using a concrete example, why no distributed data store can simultaneously guarantee Consistency, Availability, and Partition Tolerance — and identify which side of that trade-off every Azure service you've learned sits on.
- **Day 14 — Distributed Transactions:** Articulate why a single atomic transaction is impossible across two independently-failing machines, and apply the Saga pattern and eventual consistency as the practical alternatives used in every large-scale Azure system.
- **Day 15 — Observability:** Describe the three pillars of observability (metrics, traces, logs), explain why distributed systems fail in ways that local debugging cannot diagnose, and configure Application Insights with structured logging and distributed tracing in a .NET service.
- **Day 16 — Resilience at Scale:** Move from individual resilience patterns (circuit breaker, retry) to system-level resilience: chaos engineering, graceful degradation, SLO-based alerting, and the architecture review checklist that governs every production Azure deployment.

---

## The Thread Running Through This Module

Every building block in this course assumes a network between components. Module 4 makes that assumption explicit and asks: *what happens when the network lies?*

The answers are uncomfortable:
- The network will partition. The database will return stale data. The transaction will be in an unknown state. The service will fail in a way you didn't anticipate. These are not edge cases — they are the default at scale.

The practical consequence: **design for failure as the normal case, not the exception.** Every day in this module gives you a tool for reasoning about — and designing around — a specific class of distributed failure.

---

## Navigation

| Day | Page | One Idea |
|-----|------|----------|
| 13 | [CAP Theorem](days/day-13-cap-theorem.md) | When the network partitions, you must choose between returning stale data and returning no data — there is no third option |
| 14 | [Distributed Transactions](days/day-14-distributed-transactions.md) | You cannot have an atomic commit across two machines — the Saga pattern accepts this and designs compensating transactions instead |
| 15 | [Observability](days/day-15-observability.md) | A distributed system fails in ways you cannot reproduce locally — metrics, traces, and logs are the only way to see it |
| 16 | [Resilience at Scale](days/day-16-resilience-at-scale.md) | Chaos engineering proves your resilience patterns actually work — before a real incident proves they don't |

**Previous module:** [Module 3 — Compute and Communication Building Blocks](../03-compute-communication-building-blocks/overview.md)
**Next module:** [Module 5 — Designing Real Systems](../05-designing-real-systems/overview.md)
