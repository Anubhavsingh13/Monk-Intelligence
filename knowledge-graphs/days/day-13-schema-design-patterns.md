*[Knowledge Graphs: From Concept to Production](../README.md) · Day 13 of 15*

# Day 13 — Schema Design Patterns

> **Today's one idea:** The choice of schema pattern — entity-centric, event-centric, or provenance-aware — determines whether your KG answers the queries you need in 6 months, not just the ones you have today.
> **Reading time:** ~40 min · **Prereqs:** [Days 1–12](../README.md)
> **Primary source for today:** Robinson, Webber & Eifrem, *Graph Databases* (O'Reilly, 2015), Chapter 4, §4.1–4.4 ("Designing Graph Databases"). Free at neo4j.com/graph-databases-book/

---

## The hook (2–4 min)

You're building a KG of customer support interactions. Every day: customers open tickets, agents respond, tickets get escalated, components get tagged, issues get resolved.

First instinct: `(Customer)-[:OPENED]->(Ticket)-[:TAGGED_WITH]->(Component)`.

Works fine for "how many tickets is Alice's component involved in?" Six months later, someone asks:

- "Which agent handled the most escalations *in Q2* for *cloud components*?"
- "Show me the sequence of actions on ticket #892 in chronological order."
- "Which tickets were escalated more than once, and by whom?"

Suddenly, `[:OPENED]` and `[:TAGGED_WITH]` don't carry enough context. The date, the agent, the escalation count — none of this fits on a simple edge.

Schema design is the decision you make *before* you ingest: what are nodes, what are edges, and what is a property? Get it right and complex queries are one-hop traversals. Get it wrong and every query requires awkward workarounds.

---

## Building the intuition (10–15 min)

### Three patterns, three philosophies

**Pattern 1 — Entity-centric:** The default. Nodes are "things" (nouns); edges are "relationships" (verbs). Best for: domains where the primary questions are about entities and their connections.

```
(Customer)--[:OPENED]-->(Ticket)--[:TAGGED_WITH]-->(Component)
```

**Pattern 2 — Event-centric:** Actions and interactions become nodes with timestamps. Best for: domains where temporal sequences matter, or where the same two entities interact multiple times in different contexts.

```
(Customer)--[:INITIATED]-->(SupportInteraction {timestamp, channel})
(SupportInteraction)--[:CONCERNS]-->(Ticket)
(SupportInteraction)--[:HANDLED_BY]-->(Agent)
```

**Pattern 3 — Provenance-aware:** Every fact tracks where it came from and how confident you are. Best for: KGs built from automated extraction where fact quality varies.

```
(Alice)--[:WORKS_AT {source: "doc_42", confidence: 0.91, extracted_at: datetime()}]-->(ToolaGen)
```

In practice, production KGs combine all three. The art is knowing which to apply to which part of your domain.

### Decision tree: node or edge?

The most common schema mistake: modelling something as an edge when it should be a node.

**Make it a node when:**
- It has more than 2–3 properties you'll need to query on
- You need to traverse *through* it to other entities
- It has its own lifecycle (can be created, modified, deleted independently)
- Multiple entities are connected to it (it's a hub)
- Temporal queries need it (you'll ask "show me all events on date X")

**Keep it an edge when:**
- It is purely a binary relationship with 0–2 metadata properties
- You never need to ask "find all edges of this type"
- It's an intrinsic connection (not an event or interaction)

| Candidate | Node or Edge? | Reason |
|-----------|--------------|--------|
| "Alice *works at* ToolaGen" | Edge `[:WORKS_AT]` | Binary relationship; start_date fits as property |
| "Alice *submitted* ticket #892 *on* 2024-03-15" | Node `(:Submission)` | Has timestamp, connects Alice + ticket + agent; queries need it |
| "Alice *knows* Bob" | Edge `[:KNOWS]` | Simple binary; since-date fits as property |
| "Alice *co-authored* paper at NeurIPS 2022" | Node `(:Publication)` | Has venue, year, connects multiple authors + conference |
| "Component B *is part of* System A" | Edge `[:PART_OF]` | Static structural relationship; no lifecycle |

---

## The formal picture (10–15 min)

### Pattern 1: Entity-centric — the backbone

Every KG needs this. Model the core entities and their stable, structural relationships.

```python
# Cypher — entity-centric schema
"""
CREATE CONSTRAINT unique_customer FOR (c:Customer) REQUIRE c.id IS UNIQUE
CREATE CONSTRAINT unique_ticket   FOR (t:Ticket)   REQUIRE t.id IS UNIQUE

// Add entities
MERGE (c:Customer {id: "cust_001", name: "Acme Corp", tier: "Enterprise"})
MERGE (t:Ticket   {id: "tkt_892",  title: "Auth service timeout", priority: "HIGH"})
MERGE (comp:Component {name: "auth-service", team: "Platform"})

// Add structural relationships
MERGE (c)-[:OPENED]->(t)
MERGE (t)-[:AFFECTS]->(comp)
"""
```

**Queries this enables:**
```cypher
-- How many high-priority tickets affect each component?
MATCH (t:Ticket {priority: "HIGH"})-[:AFFECTS]->(c:Component)
RETURN c.name, count(t) AS high_priority_count
ORDER BY high_priority_count DESC
```

### Pattern 2: Event-centric — the temporal layer

Elevate actions to nodes. This is the pattern for agent interaction logs, workflow steps, or any sequence of events.

```python
"""
// A support interaction is a node, not just an edge
MERGE (a:Agent    {id: "agent_007", name: "Bob Kumar"})

// The interaction is a first-class entity
CREATE (ev:SupportEvent {
    id:        "evt_20240315_001",
    type:      "initial_response",
    timestamp: datetime("2024-03-15T10:23:00"),
    channel:   "email",
    content:   "Thanks for reporting. Investigating now."
})

// Connect the event to its participants
MERGE (a)-[:PERFORMED]->(ev)
MERGE (ev)-[:ON_TICKET]->(t)   // t is tkt_892 from above

// An escalation event
CREATE (esc:SupportEvent {
    id:        "evt_20240315_002",
    type:      "escalation",
    timestamp: datetime("2024-03-15T14:45:00"),
    reason:    "No fix within SLA"
})
MERGE (a)-[:PERFORMED]->(esc)
MERGE (esc)-[:ON_TICKET]->(t)
MERGE (esc)-[:ESCALATED_TO]->(:Team {name: "Platform Engineering"})
"""
```

**Queries this enables:**
```cypher
-- Show the full timeline of ticket #892
MATCH (ev:SupportEvent)-[:ON_TICKET]->(t:Ticket {id: "tkt_892"})
MATCH (a:Agent)-[:PERFORMED]->(ev)
RETURN ev.timestamp, ev.type, a.name, ev.content
ORDER BY ev.timestamp ASC

-- Which agents escalated the most tickets in Q2?
MATCH (a:Agent)-[:PERFORMED]->(ev:SupportEvent {type: "escalation"})
WHERE ev.timestamp >= datetime("2024-04-01") AND ev.timestamp < datetime("2024-07-01")
RETURN a.name, count(ev) AS escalations
ORDER BY escalations DESC
```

### Pattern 3: Provenance-aware — the trust layer

Every fact extracted by your Day 7/8 pipeline has a source and a confidence. Store this on edges so downstream queries can filter by trust level.

```python
# Python — write with provenance metadata
def write_with_provenance(tx, triple, source_doc, confidence, extractor_model):
    tx.run(f"""
        MERGE (s {{name: $subject}})
        MERGE (o {{name: $object}})
        MERGE (s)-[r:{triple['predicate'].upper()}]->(o)
        ON CREATE SET
            r.source           = $source,
            r.confidence       = $confidence,
            r.extractor        = $extractor,
            r.extracted_at     = datetime(),
            r.human_verified   = false
        ON MATCH SET
            r.last_confirmed   = datetime(),
            r.confidence       = CASE
                WHEN $confidence > r.confidence THEN $confidence
                ELSE r.confidence
            END
    """,
    subject=triple["subject"], object=triple["object"],
    source=source_doc, confidence=confidence, extractor=extractor_model)
```

**Queries this enables:**
```cypher
-- Only use facts with high confidence
MATCH (p:Person)-[r:WORKS_AT]->(c:Company)
WHERE r.confidence >= 0.85 AND r.human_verified = false
RETURN p.name, c.name, r.confidence, r.source
ORDER BY r.confidence DESC

-- Find unverified facts about critical components
MATCH (:Component {name: "auth-service"})<-[r]-(n)
WHERE r.human_verified = false AND r.confidence < 0.8
RETURN type(r), n.name, r.confidence, r.source
```

### Combining all three: the full schema

A production KG for agent-assisted customer support looks like this:

```
ENTITY LAYER (entity-centric):
(:Customer) --[:OPENED]--> (:Ticket) --[:AFFECTS]--> (:Component)
(:Component) --[:OWNED_BY]--> (:Team)

EVENT LAYER (event-centric):
(:Agent) --[:PERFORMED]--> (:SupportEvent) --[:ON_TICKET]--> (:Ticket)
(:SupportEvent) --[:ESCALATED_TO]--> (:Team)

PROVENANCE LAYER (on edges):
All edges carry: {source, confidence, extracted_at, human_verified}

MEMORY LAYER (agent memory from Day 10):
(:Event {session_id, turn, summary, timestamp})
```

**The capstone-ready multi-hop query:**
```cypher
-- Which engineers from high-escalation teams could help with open high-priority tickets?
MATCH (t:Ticket {priority: "HIGH", status: "open"})
      -[:AFFECTS]->(comp:Component)
      <-[:OWNED_BY]-(team:Team)
      <-[:MEMBER_OF]-(eng:Person)
WHERE EXISTS {
    MATCH (:SupportEvent {type: "escalation"})-[:ON_TICKET]->(t2:Ticket)
          -[:AFFECTS]->(comp)
}
RETURN t.id, comp.name, team.name, collect(eng.name) AS available_engineers
ORDER BY t.id
```

---

## Where it breaks / what it is not (3–5 min)

**Event nodes get expensive.** Every user action becomes a node. For high-traffic systems (thousands of events/minute), the node count grows fast and traversals through event nodes slow down. Index event timestamps aggressively: `CREATE INDEX event_ts FOR (e:SupportEvent) ON (e.timestamp)`.

**Provenance doubles your edge count.** If every edge carries 5 properties, you're effectively storing twice the data. For large KGs, consider: only track provenance for edges with confidence < 1.0 (i.e., automatically extracted facts), not for manually curated ones.

**Over-normalisation is real.** It's tempting to turn everything into nodes for maximum flexibility. Resist. If you never query "all edges of type X," X doesn't need to be a node. Model for the queries you *know* you'll write, not hypothetical future ones.

**Pattern mixing needs discipline.** Event nodes and entity nodes living in the same graph can be hard to distinguish in queries. Use label naming conventions: entity labels in CamelCase (`:Customer`, `:Ticket`), event labels with an `Event` suffix (`:SupportEvent`, `:DeploymentEvent`).

---

## Try it yourself (5–10 min)

**Exercise 1 — Pattern selection (L1):** For each of these, decide: entity-centric node, event-centric node, or edge property?

1. An API request made by Agent A to Tool B at 14:23:05 with status 200 and latency 45ms
2. The fact that Alice is the manager of Bob
3. A code review approved by Carol on PR #142 with a comment
4. A document's classification as "confidential"
5. The relationship between a paper and its venue (NeurIPS 2023)

<details>
<summary>Solutions</summary>

1. **Event node** `(:APICall {timestamp, status, latency_ms})` — has 3+ queryable properties, temporal, connects two entities (agent + tool)
2. **Edge** `(Alice)-[:MANAGES {since: date}]->(Bob)` — simple binary, 1 property
3. **Event node** `(:CodeReview {approved_at, comment})` — has content, temporal, connects reviewer + PR
4. **Edge property** `{classification: "confidential"}` on the document's edges, OR a simple label `:Confidential` on the node — no lifecycle, no traversal needed
5. **Edge** `(Paper)-[:PUBLISHED_AT]->(Venue)` — structural, stable, minimal properties. Year fits as edge property.
</details>

**Exercise 2 — Schema for your capstone (L1/L2):** Design a schema for the domain you chose in Day 12's "what's ahead" reflection. Sketch:
- 5–8 node types with labels
- 8–12 relationship types with names
- Which edges need provenance properties
- Which entities need event-centric treatment

Write the schema in Cypher `CREATE CONSTRAINT` and `MERGE` notation.

**Exercise 3 — Stretch (L2):** Take the customer support schema from today and add a Pattern 3 (provenance) layer to the existing entity edges. Write a migration Cypher query that adds `{human_verified: false, confidence: 1.0, source: "manual"}` to all existing edges that lack provenance properties. Test it runs without error.

<details>
<summary>Solution</summary>

```cypher
-- Add provenance to all edges that don't have it yet
MATCH ()-[r]->()
WHERE r.confidence IS NULL
SET r.confidence = 1.0,
    r.human_verified = false,
    r.source = "manual",
    r.extracted_at = datetime()
RETURN count(r) AS edges_updated
```
</details>

---

## Connect it back

[Day 8](day-08-ingestion-pipeline.md) showed how to get data into a graph. Today showed how to *design* what goes in before you extract anything. The schema pattern determines which questions your KG can answer efficiently in 6 months — not just now. Tomorrow's production concerns (Day 14) address what happens when that schema needs to change after data is already in the graph.

**The question you can answer today that you couldn't this morning:** *Why should a customer support "escalation" be a node rather than a property on the `:OPENED` edge, even though it only ever connects exactly two entities?*

---

## Suggested readings for today

**Required if you have 15 extra minutes:** Robinson et al., *Graph Databases*, Chapter 4, §4.1–4.4, pp. 75–115. Free at neo4j.com/graph-databases-book/ — the clearest treatment of entity-centric vs. hyper-relational design tradeoffs. Table 4.1 (when to use each pattern) is worth bookmarking.

**If you want the deep version:**
- Vrandecic & Krötzsch, "Wikidata: A Free Collaborative Knowledgebase," CACM 2014, §3 "Data Model." DOI: 10.1145/2629489. Shows how Wikidata handles the provenance problem at 100M+ statement scale using "qualifiers" — essentially the same pattern you built today, but implemented for the world's largest open KG.
- Neo4j documentation, "Graph Modeling Guidelines": neo4j.com/docs/getting-started/graph-modeling/ — 15 practical modeling rules from the Neo4j engineering team. Specifically: Rule 7 ("Don't model the graph for a specific query") and Rule 12 ("Use relationship types to encode meaning") pair directly with today's decision tree.

---

← [Day 12 — Rest & Synthesize II](day-12-rest-and-synthesize-ii.md) &nbsp;|&nbsp; [Day 14 — Production Concerns →](day-14-production-concerns.md)
