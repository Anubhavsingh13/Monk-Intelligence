# Module 5 — Designing Real Systems

**Days 17–21 · 5 pages · ~3 hours**

---

## What This Module Earns You

The previous four modules built your toolkit. This module puts it to work.

Every day in Module 5 is a complete system design — requirements, capacity estimation, options considered, trade-off matrix, decision + justification, and a C4 Container diagram. Each system is chosen because it stress-tests a different cluster of building blocks:

- **Day 17 — Notification System:** The canonical async fan-out problem. Millions of users, multiple delivery channels (push, email, SMS), rate limiting per channel, and a delivery guarantee that must survive provider outages.
- **Day 18 — Social Media Feed:** The hardest read-scaling problem in system design. Fan-out on write vs fan-out on read — a trade-off that has no universally correct answer, only a correct answer given your user distribution.
- **Day 19 — Search Autocomplete:** Low-latency prefix search at scale. Trie data structures, pre-computation pipelines, and why the bottleneck in autocomplete is almost never the search — it's the ranking.
- **Day 20 — Rest & Synthesise II:** No new material. Review all five designs. Identify the recurring patterns. Build your personal decision guide for the next system design conversation you're in.
- **Day 21 — Capstone:** One open-ended design brief. No guided answer. This is the Feynman test: close the course, open a blank document, and produce a complete HLD + LLD for an Azure system from scratch.

---

## How to Use This Module

These pages are not lectures. They are **worked examples** — the same format you will use in a real system design session or architecture review. The structure is consistent across Days 17–19:

1. **Requirements** (functional + non-functional)
2. **Capacity estimation** (QPS, storage, bandwidth)
3. **Options considered** (2–3 approaches per major decision)
4. **Trade-off matrix** (score each option against the quality attribute priorities)
5. **Decision + justification** (ADR-style, referencing the framework from Day 2)
6. **Component design** (detailed walkthrough of the chosen architecture)
7. **C4 Container diagram**
8. **What we'd do differently at 10× scale**

Read actively: before you see the Decision section, stop and write your own recommendation. Then compare. The gap between your answer and the worked answer is where learning happens.

---

## Navigation

| Day | Page | One Idea |
|-----|------|----------|
| 17 | [Notification System](days/day-17-notification-system.md) | Fan-out at scale requires a tiered queue architecture — one queue per channel, not one queue for everything |
| 18 | [Social Media Feed](days/day-18-social-media-feed.md) | Fan-out on write pre-computes every user's feed; fan-out on read computes it on demand — the right choice depends on the ratio of celebrities to regular users |
| 19 | [Search Autocomplete](days/day-19-search-autocomplete.md) | Autocomplete is a pre-computation problem disguised as a real-time search problem |
| 20 | [Rest & Synthesise II](days/day-20-rest-synthesise-ii.md) | The patterns that appear in every design reveal the small number of underlying problems all distributed systems share |
| 21 | [Capstone](days/day-21-capstone.md) | Produce a complete HLD + LLD for a new system using everything you have built |

**Previous module:** [Module 4 — Distributed Systems Reality](../04-distributed-systems-reality/overview.md)
