# Module 2 — The Storage Building Blocks

**Days 4–7 · 4 pages · ~2.5 hours**

---

## What This Module Earns You

Storage decisions are the most consequential in any system design. A wrong compute choice costs you money and a redeploy. A wrong storage choice costs you a data migration at 3am with users watching. By the end of this module you will have a mental model — and a Decision Guide — for every major storage building block:

- **Day 4 — Relational databases:** How indexes make queries fast, how replication buys availability, and why sharding is a last resort rather than a first move. Azure SQL is the lens.
- **Day 5 — NoSQL:** Four data models (document, key-value, wide-column, graph) that each optimise a different access pattern — and the Cosmos DB API selection framework that maps each model to its Azure service.
- **Day 6 — Caching:** Where to place a cache in the request path, which invalidation strategy to use, and how to prevent a cache stampede from taking down your database at peak load.
- **Day 7 — Blob storage, CDN, and search:** Three building blocks for three non-relational read patterns: large binary objects, geographically distributed static content, and full-text relevance queries.

---

## The Thread Running Through This Module

Every day in this module applies the trade-off framework from [Day 2](../01-design-methodology/days/day-02-trade-off-analysis.md). Each building block has a **Decision Guide** at the bottom of its page — a structured "choose this when / don't choose this when" section that maps quality attribute priorities to a concrete storage selection.

By Day 7, you will have a complete storage decision map: given any access pattern and quality attribute priority, you can identify which building block fits and which Azure service implements it.

---

## Navigation

| Day | Page | One Idea |
|-----|------|----------|
| 4 | [Relational Databases](days/day-04-relational-databases.md) | Sharding destroys JOINs — exhaust every other option first |
| 5 | [NoSQL](days/day-05-nosql.md) | Each NoSQL model optimises one access pattern — match model to pattern, not to trend |
| 6 | [Caching](days/day-06-caching.md) | A cache is a bet on re-use — the placement and invalidation strategy determine whether it pays off |
| 7 | [Blob Storage, CDN, and Search](days/day-07-blob-cdn-search.md) | Three building blocks for three non-relational read problems |

**Previous module:** [Module 1 — The Design Methodology](../01-design-methodology/overview.md)
**Next module:** [Module 3 — Compute and Communication Building Blocks](../../03-compute-communication-building-blocks/overview.md)
