*[Knowledge Graphs: From Concept to Production](../README.md) · Day 14 of 15*

# Day 14 — Production Concerns

> **Today's one idea:** A KG that works in a notebook dies in production unless you solve four problems: keeping it fresh (incremental updates), keeping it correct (data quality), keeping it fast (indexing + query tuning), and knowing when it breaks (monitoring).
> **Reading time:** ~40 min · **Prereqs:** [Days 7–13](../README.md)
> **Primary source for today:** Hogan, Aidan et al., *Knowledge Graphs*, MIT Press, 2021. Chapter 8, §8.1–8.4 ("Creation, Enrichment and Quality").

---

## The hook (2–4 min)

Your capstone KG has been running for two weeks. The ingestion pipeline re-runs every night. This morning:

- A source document was corrected — "Alice Chen works at *Stanford*" should have been "NTU." The old triple is still in the graph. Now there are two `WORKS_AT` edges from Alice to different institutions.
- The nightly pipeline ran fine, but a Cypher query that took 20ms last week now takes 4 seconds — the graph grew from 5,000 to 80,000 nodes.
- Three agents are querying the KG in parallel and occasionally writing conflicting facts.
- No one noticed that 18% of the extracted triples this week have `confidence < 0.5` because the source documents changed format.

None of these problems are hard to solve individually. They are all hard to *discover* if you haven't built the scaffolding to surface them. Production is not about making it work once — it is about knowing when it stopped working.

---

## Building the intuition (10–15 min)

### The four production problems

| Problem | Symptom | Root cause |
|---------|---------|------------|
| **Staleness** | Facts in graph are no longer true | Update pipeline doesn't remove contradicted facts |
| **Data quality** | Wrong, duplicate, or low-confidence triples quietly accumulate | No validation gate before MERGE |
| **Performance** | Queries slow as graph grows | Missing indexes; traversal patterns not optimised |
| **Observability** | You learn about failures from users, not dashboards | No health metrics, no anomaly detection |

### Mental model: the living graph

A notebook KG is static — you write it once and query it. A production KG is a **river**, not a lake. Facts flow in, facts flow out. Some facts are corrected. Some facts expire. The schema evolves as new entity types emerge from your documents.

The practical stance: *never assume the KG is correct; always know its confidence distribution*.

---

## The formal picture (10–15 min)

### Problem 1: Incremental updates — handling corrections and deletions

The Day 8 pipeline uses MERGE, which is append-only (it never removes). To handle corrections, add a reconciliation step.

```python
# pip install neo4j
from neo4j import GraphDatabase
from datetime import datetime, UTC

class KGReconciler:
    """
    Keeps a production KG fresh by:
    1. Updating confidence on re-confirmed facts
    2. Flagging contradictions (same subject, same predicate, different object)
    3. Expiring facts not seen in N days
    """

    def __init__(self, driver):
        self.driver = driver

    def flag_contradictions(self) -> list[dict]:
        """
        Find entities with multiple outgoing edges of the same type
        to different targets — a likely contradiction.
        """
        with self.driver.session() as s:
            result = s.run("""
                MATCH (n)-[r1]->(m1), (n)-[r2]->(m2)
                WHERE type(r1) = type(r2)
                  AND id(m1) < id(m2)     // avoid double-counting
                  AND m1.name <> m2.name
                RETURN n.name            AS entity,
                       type(r1)          AS relation,
                       collect(DISTINCT m1.name + ' vs ' + m2.name) AS conflicts
                LIMIT 50
            """)
            return [dict(r) for r in result]

    def expire_unseen_facts(self, days: int = 90) -> int:
        """
        Delete edges not confirmed in the last N days.
        Only applies to automatically extracted facts (human_verified = false).
        """
        with self.driver.session() as s:
            result = s.run("""
                MATCH ()-[r]->()
                WHERE r.human_verified = false
                  AND r.last_seen < datetime() - duration({days: $days})
                DETACH DELETE r
                RETURN count(r) AS deleted
            """, days=days)
            return result.single()["deleted"]

    def resolve_contradiction(self, entity: str, relation: str,
                               keep_target: str, remove_target: str) -> None:
        """Remove the lower-confidence edge in a detected contradiction."""
        with self.driver.session() as s:
            s.run(f"""
                MATCH (n {{name: $entity}})-[r:{relation.upper()}]->(m {{name: $remove_target}})
                DELETE r
            """, entity=entity, remove_target=remove_target)
```

### Problem 2: Data quality — validation before merge

Never MERGE triples blindly from an LLM extractor. Add a validation gate.

```python
from dataclasses import dataclass
from typing import Optional

@dataclass
class ValidationResult:
    valid: bool
    reason: Optional[str] = None

class TripleValidator:
    """
    Gates triples before they enter the KG.
    Rules: minimum field lengths, confidence threshold, blocklist.
    """

    BLOCKLIST_PREDICATES = {"is", "has", "was", "did"}  # too vague
    MIN_NAME_LENGTH = 2
    MIN_CONFIDENCE = 0.5

    def validate(self, triple: dict) -> ValidationResult:
        # Required fields
        for field in ("subject", "predicate", "object"):
            if not triple.get(field):
                return ValidationResult(False, f"missing field: {field}")

        # Name length
        if len(triple["subject"]) < self.MIN_NAME_LENGTH:
            return ValidationResult(False, f"subject too short: '{triple['subject']}'")
        if len(triple["object"]) < self.MIN_NAME_LENGTH:
            return ValidationResult(False, f"object too short: '{triple['object']}'")

        # Blocklist
        pred = triple["predicate"].lower().strip()
        if pred in self.BLOCKLIST_PREDICATES:
            return ValidationResult(False, f"predicate '{pred}' is too vague")

        # Confidence
        conf = triple.get("confidence", 1.0)
        if conf < self.MIN_CONFIDENCE:
            return ValidationResult(False, f"confidence {conf:.2f} below threshold")

        return ValidationResult(True)

    def filter(self, triples: list[dict]) -> tuple[list[dict], list[dict]]:
        """Returns (valid_triples, rejected_triples)."""
        valid, rejected = [], []
        for t in triples:
            result = self.validate(t)
            (valid if result.valid else rejected).append(
                {**t, "_rejection_reason": result.reason}
            )
        return valid, rejected


# Integrate into the Day 8 pipeline
def write_with_validation(pipeline, triples: list[dict]) -> dict:
    validator = TripleValidator()
    valid, rejected = validator.filter(triples)

    stats = {
        "submitted": len(triples),
        "valid": len(valid),
        "rejected": len(rejected),
        "rejection_reasons": [r["_rejection_reason"] for r in rejected],
    }

    if valid:
        pipeline.writer.write(valid)

    return stats
```

### Problem 3: Indexing and query tuning

Neo4j uses B-tree indexes. Without them, every `MATCH (n {name: "Alice"})` is a full node scan — O(N) even when the result is one node.

```python
def create_production_indexes(driver) -> None:
    """
    Run once on a fresh database.
    Creates indexes for all high-cardinality lookup patterns.
    """
    indexes = [
        # Entity name lookups (the most common query pattern)
        "CREATE INDEX entity_name IF NOT EXISTS FOR (n:Entity)   ON (n.name)",
        "CREATE INDEX person_name IF NOT EXISTS FOR (n:Person)   ON (n.name)",
        "CREATE INDEX org_name   IF NOT EXISTS FOR (n:Organisation) ON (n.name)",

        # Event timestamp range queries (Day 13 event-centric pattern)
        "CREATE INDEX event_ts   IF NOT EXISTS FOR (n:SupportEvent) ON (n.timestamp)",
        "CREATE INDEX mem_event  IF NOT EXISTS FOR (n:Event)        ON (n.timestamp)",

        # Edge confidence for provenance-filtered queries
        # Note: Neo4j 5.x supports relationship property indexes
        "CREATE INDEX rel_conf   IF NOT EXISTS FOR ()-[r:WORKS_AT]-() ON (r.confidence)",
    ]
    with driver.session() as s:
        for idx in indexes:
            s.run(idx)
            print(f"Created: {idx[:60]}...")
```

**Query anti-patterns to avoid:**

```cypher
-- SLOW: full scan then filter
MATCH (n) WHERE n.name = "Alice Chen" RETURN n

-- FAST: use the label (narrows scan to label partition)
MATCH (n:Person {name: "Alice Chen"}) RETURN n

-- SLOW: unbounded variable-length path
MATCH (a)-[*]->(b) WHERE a.name = "Alice" RETURN b

-- FAST: bound the depth
MATCH (a:Person {name: "Alice"})-[*1..3]->(b) RETURN b LIMIT 50

-- SLOW: DISTINCT on a huge result set
MATCH (n:SupportEvent)-[:ON_TICKET]->(t) RETURN DISTINCT t.id

-- FAST: count in Cypher, not in Python
MATCH (n:SupportEvent)-[:ON_TICKET]->(t)
RETURN t.id, count(n) AS event_count
ORDER BY event_count DESC LIMIT 20
```

### Problem 4: Observability — health metrics

```python
from datetime import datetime, UTC
import json

class KGHealthMonitor:
    """
    Collect and log KG health metrics.
    Run on a schedule (cron, APScheduler, etc.) to detect drift.
    """

    def __init__(self, driver):
        self.driver = driver

    def collect_metrics(self) -> dict:
        with self.driver.session() as s:
            # Volume
            node_count  = s.run("MATCH (n) RETURN count(n) AS c").single()["c"]
            edge_count  = s.run("MATCH ()-[r]->() RETURN count(r) AS c").single()["c"]

            # Quality: confidence distribution
            conf_stats = s.run("""
                MATCH ()-[r]->()
                WHERE r.confidence IS NOT NULL
                RETURN
                    avg(r.confidence)                        AS avg_conf,
                    count(CASE WHEN r.confidence < 0.5 THEN 1 END) AS low_conf_count,
                    count(CASE WHEN r.human_verified = true THEN 1 END) AS verified_count
            """).single()

            # Staleness: edges not seen in 30 days
            stale = s.run("""
                MATCH ()-[r]->()
                WHERE r.last_seen IS NOT NULL
                  AND r.last_seen < datetime() - duration({days: 30})
                RETURN count(r) AS c
            """).single()["c"]

            # Contradictions
            contradiction_count = s.run("""
                MATCH (n)-[r1]->(m1), (n)-[r2]->(m2)
                WHERE type(r1) = type(r2) AND id(m1) < id(m2) AND m1.name <> m2.name
                RETURN count(*) AS c
            """).single()["c"]

        return {
            "timestamp":           datetime.now(UTC).isoformat(),
            "node_count":          node_count,
            "edge_count":          edge_count,
            "avg_confidence":      round(conf_stats["avg_conf"] or 0, 3),
            "low_confidence_edges": conf_stats["low_conf_count"],
            "verified_edges":      conf_stats["verified_count"],
            "stale_edges_30d":     stale,
            "contradiction_pairs": contradiction_count,
        }

    def check_alerts(self, metrics: dict) -> list[str]:
        """Return a list of alert messages if any thresholds are breached."""
        alerts = []
        if metrics["avg_confidence"] < 0.7:
            alerts.append(f"[WARN] Avg confidence {metrics['avg_confidence']:.2f} below 0.70")
        if metrics["low_confidence_edges"] > 0.2 * metrics["edge_count"]:
            alerts.append(f"[WARN] {metrics['low_confidence_edges']} low-confidence edges "
                          f"({100*metrics['low_confidence_edges']//metrics['edge_count']}% of total)")
        if metrics["stale_edges_30d"] > 0.1 * metrics["edge_count"]:
            alerts.append(f"[WARN] {metrics['stale_edges_30d']} stale edges — run reconciler")
        if metrics["contradiction_pairs"] > 50:
            alerts.append(f"[ALERT] {metrics['contradiction_pairs']} contradiction pairs detected")
        return alerts

    def report(self) -> None:
        metrics = self.collect_metrics()
        alerts  = self.check_alerts(metrics)
        print(json.dumps(metrics, indent=2))
        for alert in alerts:
            print(alert)
```

### Schema evolution: adding a new entity type without breaking existing queries

When your corpus grows and new entity types appear (say, `:Product` in a system that only had `:Person` and `:Organisation`), Neo4j's schema-free model means you can add nodes with new labels at any time. The only work is:

1. Add a new index: `CREATE INDEX product_name IF NOT EXISTS FOR (n:Product) ON (n.name)`.
2. Update the ingestion pipeline's `subject_type`/`object_type` mapping in the Transformer.
3. Audit existing generic `:Entity` nodes that should be retroactively re-labelled.

```cypher
-- Retroactively add :Product label to Entity nodes that look like products
MATCH (n:Entity)
WHERE n.name IN ["GPT-4", "Claude", "Gemini", "Llama 3"]
SET n:Product
RETURN count(n) AS relabelled
```

---

## Where it breaks / what it is not (3–5 min)

**MERGE is not a transaction manager.** If two writers MERGE the same node simultaneously, Neo4j handles it correctly (locks at the node level). But if one writer creates `(Alice)-[:WORKS_AT]->(Stanford)` while another creates `(Alice)-[:WORKS_AT]->(NTU)`, you get a contradiction — MERGE doesn't know one fact supersedes the other. Solve this in application logic (the reconciler above), not in Cypher.

**Indexes don't help unbounded paths.** `MATCH (a)-[*]->(b)` traverses the whole reachable subgraph regardless of indexes. Always bound variable-length paths: `[*1..3]` is usually sufficient for 2–3 hop reasoning.

**Health metrics lag.** `collect_metrics()` is a point-in-time snapshot. It tells you the graph is unhealthy after the damage has accumulated. For real-time detection, hook into the Neo4j transaction log (available in Enterprise) or add application-level counters at write time.

**Schema evolution can't rescue a fundamentally wrong pattern.** If you modelled escalations as edge properties on Day 1 and now need to query "who escalated on Tuesday between 2–4pm?", re-labelling doesn't help — you need to migrate those properties to new `(:SupportEvent)` nodes. Day 13's design upfront is cheaper than Day 14's migration.

---

## Try it yourself (5–10 min)

**Exercise 1 — Find contradictions (L1):** Run `KGReconciler.flag_contradictions()` against your Day 8 KG. Intentionally introduce a contradiction first:
```cypher
MATCH (a:Person {name: "Alice Chen"})
MERGE (stanford:Organisation {name: "Stanford"})
MERGE (a)-[:WORKS_AT {confidence: 0.6, source: "manual"}]->(stanford)
```
Then verify the reconciler surfaces it.

**Exercise 2 — Measure validation impact (L1/L2):** Take a batch of 50 triples from your Day 8 pipeline run. Pass them through `TripleValidator`. What percentage are rejected? What are the most common rejection reasons?

**Exercise 3 — Index impact (L2):** Without any indexes (drop them: `DROP INDEX entity_name`), run `MATCH (n:Person {name: "Alice Chen"}) RETURN n` using `EXPLAIN` or `PROFILE` in Neo4j Browser. Note the `db hits`. Re-create the index, run again. Compare.

```cypher
PROFILE MATCH (n:Person {name: "Alice Chen"}) RETURN n
```

**Exercise 4 — Stretch (L2):** Wire `KGHealthMonitor.report()` to run every time your Day 8 pipeline completes. Log the output to a file. After 3 pipeline runs, check whether any alert thresholds have been breached.

<details>
<summary>Exercise 3 expected result</summary>

Without index: `db hits` is approximately equal to the total node count (full scan).  
With index: `db hits` is 1–2 (index lookup + node read). For a graph with 10,000 nodes, the speedup is ~5,000×. The difference is negligible for 100 nodes; it's the difference between 20ms and 4s at 80,000 nodes — exactly the production symptom in the hook.
</details>

---

## Connect it back

[Day 13](day-13-schema-design-patterns.md) decided what goes into the graph. Today decided how to keep what's in the graph alive, correct, and fast. The production KG is not the one you built in a notebook — it is the one you can operate at 3am without logging in, because the monitoring tells you before the users do.

**The question you can answer today that you couldn't this morning:** *If your nightly ingestion pipeline has been running for 60 days and you've never run `expire_unseen_facts()`, what is the risk — and what query would you run first to quantify it before taking any action?*

Tomorrow is the capstone. You will design, build, and serve a domain KG that answers a multi-hop question that flat vector RAG cannot.

---

## Suggested readings for today

**Required if you have 15 extra minutes:** Hogan et al., *Knowledge Graphs*, MIT Press, 2021. Chapter 8, §8.1–8.4 ("Creation, Enrichment and Quality"), pp. 245–278. Section 8.3 ("Quality") covers the exact metrics we computed today — completeness, consistency, timeliness — in a formal framework that generalises beyond Neo4j.

**If you want the deep version:**
- Neo4j documentation, "Indexes for search performance": neo4j.com/docs/cypher-manual/current/indexes/ — covers B-tree, full-text, and vector indexes in Neo4j 5.x. The section on "index hints" (`USING INDEX`) is directly applicable to the slow-query debugging in Exercise 3.
- Ehrlinger, Lisa & Wöß, Wolfram. "Towards a Definition of Knowledge Graphs." *SEMANTICS Posters & Demos*, 2016. SEMANTiCS 2016. A short paper (4 pages) that surveys 13 definitions of KG from the literature and synthesises them — directly relevant to the "what is a KG?" question you'll need to answer in your capstone presentation.
- Dong, Xin Luna et al. "Knowledge Vault: A Web-Scale Approach to Probabilistic Knowledge Fusion." *KDD*, 2014. DOI: 10.1145/2623330.2623623. Google's system for building a production-scale KG from noisy web extractions — the provenance + confidence framework from Day 13, applied at web scale. Section 4 ("Quality Estimation") is the direct ancestor of today's `TripleValidator`.

---

← [Day 13 — Schema Design Patterns](day-13-schema-design-patterns.md) &nbsp;|&nbsp; [Day 15 — Capstone →](day-15-capstone.md)
