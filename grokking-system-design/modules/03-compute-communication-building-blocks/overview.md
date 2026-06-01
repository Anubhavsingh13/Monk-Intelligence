# Module 3 — Compute and Communication Building Blocks

**Days 8–12 · 5 pages · ~3 hours**

---

## What This Module Earns You

Module 2 answered *where data lives*. This module answers *how requests travel* — from the user's browser to the right piece of code, and between services once they're inside your system.

By the end of this module you will have a working model for every building block that sits between the user and the storage layer:

- **Day 8 — Load Balancing:** Why a single server is a single point of failure, how a load balancer distributes traffic, and which Azure service (Application Gateway, Front Door, Load Balancer) maps to which layer of the request path.
- **Day 9 — API Design:** The difference between REST, GraphQL, and gRPC — not as a style debate, but as a trade-off matrix — and the role Azure API Management plays in any serious API surface.
- **Day 10 — Async Messaging:** Why synchronous request-response breaks under load, and how message queues (Service Bus) and event streams (Event Hubs / Event Grid) decouple producers from consumers.
- **Day 11 — Rate Limiting & Resilience:** How to prevent one misbehaving client from taking down your API, and how to stop cascading failures from propagating through a service mesh.
- **Day 12 — Rest & Synthesise I:** No new material. Consolidate Days 1–11 by reconstructing the URL shortener from Day 1 using every building block introduced so far.

---

## The Thread Running Through This Module

Storage building blocks answer *can I find and persist the data?*. Compute and communication building blocks answer *can the request reach the right service, at the right time, without cascading failure?*

Every day in this module has a **Decision Guide** that maps the communication pattern to the Azure service. By Day 12, you will have a mental checklist for any request path: synchronous or async? internal or external? latency-sensitive or throughput-sensitive? Each answer points to a specific building block.

---

## Navigation

| Day | Page | One Idea |
|-----|------|----------|
| 8 | [Load Balancing](days/day-08-load-balancing.md) | A load balancer is the entry point — make it the first resilience layer, not an afterthought |
| 9 | [API Design](days/day-09-api-design.md) | REST, GraphQL, and gRPC each optimise a different dimension — match the protocol to the client's access pattern |
| 10 | [Async Messaging](days/day-10-async-messaging.md) | A queue decouples sender from receiver in time — the most powerful resilience primitive in distributed systems |
| 11 | [Rate Limiting & Resilience](days/day-11-rate-limiting-resilience.md) | A circuit breaker stops calling a failing service — give it time to recover instead of hammering it into the ground |
| 12 | [Rest & Synthesise I](days/day-12-rest-synthesise.md) | Reconstruct everything you know so far into one coherent design |

**Previous module:** [Module 2 — The Storage Building Blocks](../02-storage-building-blocks/overview.md)
**Next module:** [Module 4 — Distributed Systems Reality](../04-distributed-systems-reality/overview.md)
