*[Knowledge Graphs: From Concept to Production](../README.md) · Day 15 of 15*

# Day 15 — Capstone: Build a Domain Knowledge Graph for an AI Agent

> **Today's one idea:** Everything you have built across 14 days — extraction, ingestion, GraphRAG, agent memory, embeddings, schema design, production hygiene — combines into one deployable system that answers a multi-hop question flat vector RAG cannot.
> **Reading time:** ~90–120 min (this is a build day, not a reading day)
> **Prereqs:** [Days 1–14](../README.md)
> **No single primary source.** Your capstone synthesises all 11 sources on the Course Shelf. Keep `bibliography.md` open.

---

## What "done" looks like

You will have produced:

1. A **running Neo4j graph** with ≥ 200 entities and ≥ 500 edges, built from real documents in a domain you control.
2. A **5-query Cypher notebook** — five non-trivial queries that would be hard or impossible in a relational DB or flat vector store.
3. A **multi-hop question** that flat vector RAG cannot reliably answer, answered correctly by your KG.
4. An **agent integration** — the KG is wired into a Claude-backed agent via either GraphRAG (Day 9) or KGMemory (Day 10), your choice.
5. A **health report** from `KGHealthMonitor` (Day 14) with zero critical alerts.

This is not an exam. It is a portfolio piece. Build something you would actually use.

---

## Choose your domain

Pick one of these, or substitute your own if you have richer source material:

| Domain | Source documents | Recommended multi-hop question |
|--------|-----------------|-------------------------------|
| **Research network** | Papers + author bios from your field | "Which researchers have co-authored with someone who has cited the same foundational paper as me, but whom I've never directly worked with?" |
| **Internal tooling** | API docs, runbooks, incident reports | "Which services were involved in incidents that also affected the auth-service, and who are the on-call engineers for those services today?" |
| **Competitive intelligence** | Product pages, press releases, job listings | "Which competitor's products share components with ours, and who leads those product teams?" |
| **Codebase knowledge** | Git logs, PR descriptions, README files | "Which modules have been modified by engineers who also touched the payment module in the last 6 months?" |

If you work on agentic AI at a company (you do), the **internal tooling** domain is the highest-leverage choice for a live project.

---

## Build plan (work through these phases)

### Phase 1 — Schema design (15 min)

Before writing a single line of ingestion code, sketch your schema using the Day 13 decision tree.

**Deliverable: a schema block like this** (adapt to your domain):

```
ENTITY LAYER:
(:Engineer {name, team, role})
(:Service  {name, owner_team, sla_tier})
(:Incident {id, severity, status, created_at})
(:Runbook  {title, service, last_updated})
(:Component {name, language, repo})

EVENT LAYER (promote to nodes):
(:OnCallShift {engineer, service, start_date, end_date})
(:IncidentResponse {incident, engineer, action, timestamp})

RELATIONSHIPS:
(Engineer)-[:ON_CALL_FOR]->(Service)
(Service)-[:DEPENDS_ON]->(Service)
(Incident)-[:AFFECTS]->(Service)
(Engineer)-[:RESPONDED_TO]->(Incident)
(Service)-[:DOCUMENTED_BY]->(Runbook)
(Component)-[:PART_OF]->(Service)

PROVENANCE on all extracted edges:
{source, confidence, extracted_at, human_verified}
```

**Critical question to answer before Phase 2:** What is your multi-hop question? Write it down. Every schema decision should serve that question.

### Phase 2 — Document preparation (10 min)

Collect 10–20 source documents (plain text, markdown, or PDF-convertible). Minimum 5,000 words total. If you don't have real documents, generate synthetic ones:

```python
# Synthetic document generator — use this only if you lack real source material
import anthropic

client = anthropic.Anthropic()

DOMAINS = [
    "auth-service: handles OAuth2 flows, depends on user-db and token-cache",
    "payment-service: Stripe integration, depends on auth-service and ledger-db",
    "notification-service: email + SMS dispatch, depends on user-db and queue",
]

def generate_synthetic_doc(domain_description: str, doc_type: str) -> str:
    """Generate a realistic source document for ingestion."""
    response = client.messages.create(
        model="claude-haiku-4-5-20251001",
        max_tokens=800,
        messages=[{
            "role": "user",
            "content": (
                f"Write a realistic {doc_type} (300–400 words) for this system component: "
                f"{domain_description}. Include: engineer names, team names, dependencies, "
                f"recent incidents, and runbook references. Be specific, not generic."
            )
        }]
    )
    return response.content[0].text

# Generate 15 documents across 3 types
docs = []
for domain in DOMAINS:
    for doc_type in ["incident postmortem", "service README", "on-call handoff note",
                     "architecture decision record", "runbook"]:
        docs.append({
            "source": f"{domain.split(':')[0]}_{doc_type.replace(' ', '_')}",
            "text": generate_synthetic_doc(domain, doc_type)
        })

print(f"Generated {len(docs)} documents, "
      f"{sum(len(d['text'].split()) for d in docs)} words total")
```

### Phase 3 — Ingestion (20 min)

Run the Day 8 pipeline with the Day 14 validator wired in. Use your Day 13 schema: make the `subject_type` and `object_type` mapping explicit.

```python
from neo4j import GraphDatabase
import anthropic, json, re
from difflib import SequenceMatcher

# ── Configuration ─────────────────────────────────────────────────────
NEO4J_URI  = "bolt://localhost:7687"
NEO4J_USER = "neo4j"
NEO4J_PASS = "password123"   # change to your password

# ── Capstone extraction prompt (domain-specific) ──────────────────────
# Adapt this to your chosen domain
CAPSTONE_EXTRACTION_PROMPT = """Extract knowledge graph triples from this text.

Output ONLY a JSON array. Each object has:
  subject       (string, entity name)
  predicate     (string, snake_case relationship)
  object        (string, entity name or value)
  subject_type  (Engineer | Service | Incident | Component | Team | Runbook | Other)
  object_type   (Engineer | Service | Incident | Component | Team | Runbook | Other)
  confidence    (float 0–1, how certain you are this is an explicit fact)

Rules:
- Only include facts EXPLICITLY stated in the text, not inferred
- Use consistent canonical names (e.g. always "auth-service", not "auth service" or "AuthService")
- Confidence < 0.7 for facts implied by context; >= 0.85 for directly stated facts
- Skip facts about generic concepts; only extract facts about specific named entities

Text:
{text}"""

def extract_triples_capstone(text: str, source: str) -> list[dict]:
    client = anthropic.Anthropic()
    response = client.messages.create(
        model="claude-haiku-4-5-20251001",
        max_tokens=2048,
        messages=[{
            "role": "user",
            "content": CAPSTONE_EXTRACTION_PROMPT.format(text=text)
        }]
    )
    raw = response.content[0].text.strip()
    raw = re.sub(r"```(?:json)?\n?", "", raw).strip().rstrip("`")
    try:
        triples = json.loads(raw)
        # Attach provenance
        for t in triples:
            t["source"] = source
        return triples
    except json.JSONDecodeError:
        return []

# ── Entity registry (canonical name resolution) ───────────────────────
class EntityRegistry:
    def __init__(self, threshold: float = 0.88):
        self.canonical: dict[str, str] = {}
        self.threshold = threshold

    def resolve(self, name: str) -> str:
        name_lower = name.lower().strip()
        for canonical in self.canonical:
            ratio = SequenceMatcher(None, name_lower, canonical).ratio()
            if ratio >= self.threshold:
                return self.canonical[canonical]
        self.canonical[name_lower] = name
        return name

registry = EntityRegistry()

# ── Validation ────────────────────────────────────────────────────────
def is_valid(triple: dict) -> bool:
    return (
        len(triple.get("subject", "")) >= 2
        and len(triple.get("object", "")) >= 2
        and triple.get("predicate", "")
        and triple.get("confidence", 1.0) >= 0.5
    )

# ── Neo4j writer (provenance-aware) ──────────────────────────────────
def write_triples(driver, triples: list[dict]) -> int:
    written = 0
    with driver.session() as s:
        for t in triples:
            subj = registry.resolve(t["subject"])
            obj  = registry.resolve(t["object"])
            pred = t["predicate"].upper().replace(" ", "_")
            s_type = t.get("subject_type", "Entity")
            o_type = t.get("object_type", "Entity")
            s.run(f"""
                MERGE (sub:{s_type} {{name: $subject}})
                MERGE (ob:{o_type}  {{name: $object}})
                MERGE (sub)-[r:{pred}]->(ob)
                ON CREATE SET
                    r.source         = $source,
                    r.confidence     = $confidence,
                    r.extracted_at   = datetime(),
                    r.human_verified = false
                ON MATCH SET
                    r.last_seen      = datetime(),
                    r.confidence     = CASE
                        WHEN $confidence > r.confidence THEN $confidence
                        ELSE r.confidence END
            """,
            subject=subj, object=obj,
            source=t.get("source", "unknown"),
            confidence=t.get("confidence", 0.8))
            written += 1
    return written

# ── Main pipeline ─────────────────────────────────────────────────────
def run_capstone_pipeline(documents: list[dict]) -> dict:
    driver = GraphDatabase.driver(NEO4J_URI, auth=(NEO4J_USER, NEO4J_PASS))

    total_extracted = 0
    total_valid     = 0
    total_written   = 0

    for doc in documents:
        triples = extract_triples_capstone(doc["text"], doc["source"])
        valid   = [t for t in triples if is_valid(t)]
        written = write_triples(driver, valid)
        total_extracted += len(triples)
        total_valid     += len(valid)
        total_written   += written
        print(f"  {doc['source']}: {len(triples)} extracted, {len(valid)} valid, {written} written")

    driver.close()
    return {
        "documents": len(documents),
        "extracted": total_extracted,
        "valid":     total_valid,
        "written":   total_written,
        "rejection_rate": f"{100*(total_extracted-total_valid)//max(total_extracted,1)}%",
    }

# Run it
if __name__ == "__main__":
    # Replace with your actual documents
    # docs = [{"source": "postmortem_001", "text": open("pm_001.txt").read()}, ...]
    stats = run_capstone_pipeline(docs)
    print("\nPipeline stats:", stats)
```

### Phase 4 — The five Cypher queries (20 min)

Write five non-trivial Cypher queries against your graph. Each should require at least 2 hops. Here are templates for the internal tooling domain — adapt the relationship types to match your schema:

```cypher
-- Query 1: Which services have the most dependencies?
MATCH (s:Service)-[:DEPENDS_ON]->(dep:Service)
RETURN s.name, count(dep) AS dependency_count
ORDER BY dependency_count DESC
LIMIT 10

-- Query 2: Which engineers are on-call for services affected by open incidents?
MATCH (i:Incident {status: "open"})-[:AFFECTS]->(s:Service)
      <-[:ON_CALL_FOR]-(eng:Engineer)
RETURN i.id, i.severity, s.name, collect(eng.name) AS on_call_engineers

-- Query 3: Dependency blast radius — what would break if auth-service went down?
MATCH path = (s:Service {name: "auth-service"})<-[:DEPENDS_ON*1..4]-(downstream:Service)
RETURN downstream.name, length(path) AS hops_from_auth
ORDER BY hops_from_auth

-- Query 4: Engineers who responded to incidents on services they're not on-call for
-- (gap in coverage)
MATCH (eng:Engineer)-[:RESPONDED_TO]->(i:Incident)-[:AFFECTS]->(s:Service)
WHERE NOT (eng)-[:ON_CALL_FOR]->(s)
RETURN eng.name, s.name, count(i) AS incidents_responded_to
ORDER BY incidents_responded_to DESC

-- Query 5: THE MULTI-HOP QUESTION
-- "Which engineers have responded to incidents on services that share components
--  with auth-service, but are not documented in auth-service's runbooks?"
MATCH (auth:Service {name: "auth-service"})
      -[:DEPENDS_ON]->(shared:Component)
      <-[:PART_OF]-(related:Service)
      <-[:AFFECTS]-(i:Incident)
      <-[:RESPONDED_TO]-(eng:Engineer)
WHERE NOT EXISTS {
    MATCH (auth)-[:DOCUMENTED_BY]->(rb:Runbook)
    WHERE rb.title CONTAINS eng.name
}
RETURN eng.name, related.name, count(i) AS incidents,
       collect(DISTINCT shared.name) AS shared_components
ORDER BY incidents DESC
```

### Phase 5 — Agent integration (20 min)

Choose one integration path:

**Path A — GraphRAG (for corpus-wide questions):**

```python
# Reuse GraphRAG from Day 9, pointed at your capstone graph
from neo4j import GraphDatabase
import community as community_louvain
import networkx as nx
import anthropic

def build_capstone_graphrag():
    driver = GraphDatabase.driver("bolt://localhost:7687", auth=("neo4j", "password123"))
    # Load graph (same as Day 9)
    G = nx.MultiDiGraph()
    with driver.session() as s:
        for record in s.run("MATCH (a)-[r]->(b) RETURN a.name, type(r), b.name"):
            G.add_edge(record["a.name"], record["b.name"], relation=record["type(r)"])
    driver.close()
    return G

# Then use GraphRAG.query() for your multi-hop question
```

**Path B — MemoryAgent (for session-persistent reasoning):**

```python
from neo4j import GraphDatabase
# Reuse KGMemory + MemoryAgent from Day 10

memory = KGMemory("bolt://localhost:7687", "neo4j", "password123")
agent  = MemoryAgent(memory)

# Pre-populate agent with your domain knowledge
agent.chat(
    "The auth-service depends on user-db and token-cache. "
    "Alice is on-call for auth-service this week. "
    "Incident INC-042 (severity: P1) is currently affecting auth-service."
)

# Now ask the multi-hop question
answer = agent.chat(
    "Given the current incidents and on-call assignments, "
    "which engineers should I page if token-cache goes down, "
    "and what other services would be affected?"
)
print(answer)
```

### Phase 6 — Health report (10 min)

```python
from neo4j import GraphDatabase

driver  = GraphDatabase.driver("bolt://localhost:7687", auth=("neo4j", "password123"))
monitor = KGHealthMonitor(driver)
monitor.report()

# Target before calling it done:
# node_count:           >= 200
# edge_count:           >= 500
# avg_confidence:       >= 0.75
# low_confidence_edges: <= 10% of edge_count
# contradiction_pairs:  0 (or understood and documented)
# stale_edges_30d:      0 (fresh ingestion)
driver.close()
```

---

## Grading yourself

| Criterion | Pass | Stretch |
|-----------|------|---------|
| Graph size | ≥ 200 nodes, ≥ 500 edges | ≥ 500 nodes, ≥ 2,000 edges |
| Schema | Entity + event layers present | + provenance on all extracted edges |
| Queries | 5 queries run without error | Multi-hop query returns semantically correct results |
| Agent | Agent answers 1 question using KG context | Agent correctly answers a question it couldn't answer from context alone |
| Production | Health report runs; no critical alerts | Reconciler removes ≥ 1 stale edge; validator rejects ≥ 1 bad triple |
| Multi-hop test | KG answers correctly; flat RAG gives a generic or wrong answer | You document *why* flat RAG fails (missing entity linkage, no traversal) |

---

## The multi-hop test — how to demonstrate the KG beats flat RAG

This is the crux. Here is a clean experimental design:

```python
import anthropic

client = anthropic.Anthropic()

MULTI_HOP_QUESTION = (
    "Which engineers are on-call for services that have a P1 incident today, "
    "and what components do those services share with auth-service?"
)

# ── Flat RAG baseline (just document chunks, no graph) ────────────────
def flat_rag_answer(question: str, docs: list[str]) -> str:
    """Naive RAG: concatenate all document text, ask Claude."""
    context = "\n\n---\n\n".join(docs[:5])  # top 5 by keyword match
    response = client.messages.create(
        model="claude-sonnet-4-6",
        max_tokens=500,
        messages=[{
            "role": "user",
            "content": (
                f"Answer this question using only the context below.\n\n"
                f"Context:\n{context}\n\n"
                f"Question: {question}"
            )
        }]
    )
    return response.content[0].text

# ── KG answer (Cypher + agent) ────────────────────────────────────────
def kg_answer(question: str, driver) -> str:
    """
    Run structured Cypher query, then format with Claude.
    The graph traversal finds the answer; Claude formats it.
    """
    with driver.session() as s:
        result = s.run("""
            MATCH (i:Incident {status: "open", severity: "P1"})
                  -[:AFFECTS]->(s:Service)
                  <-[:ON_CALL_FOR]-(eng:Engineer)
            OPTIONAL MATCH (s)-[:DEPENDS_ON]->(comp:Component)
                           <-[:DEPENDS_ON]-(:Service {name: "auth-service"})
            RETURN eng.name     AS engineer,
                   s.name       AS service,
                   i.id         AS incident,
                   collect(DISTINCT comp.name) AS shared_components
        """)
        rows = [dict(r) for r in result]

    context = "\n".join(
        f"  {r['engineer']} is on-call for {r['service']} (incident {r['incident']}), "
        f"shared components with auth-service: {r['shared_components'] or 'none'}"
        for r in rows
    )
    if not rows:
        return "No open P1 incidents found in the graph."

    response = client.messages.create(
        model="claude-sonnet-4-6",
        max_tokens=500,
        messages=[{
            "role": "user",
            "content": (
                f"Using this structured data from a knowledge graph:\n{context}\n\n"
                f"Answer: {question}"
            )
        }]
    )
    return response.content[0].text

# Run the comparison
driver = GraphDatabase.driver("bolt://localhost:7687", auth=("neo4j", "password123"))

flat_answer = flat_rag_answer(MULTI_HOP_QUESTION,
                               [d["text"] for d in docs])
kg_answer_text = kg_answer(MULTI_HOP_QUESTION, driver)

print("=== FLAT RAG ===")
print(flat_answer)
print("\n=== KG ANSWER ===")
print(kg_answer_text)

driver.close()
```

**Expected result:** Flat RAG returns a vague answer (e.g., "Alice and Bob are engineers") without linking incidents → services → on-call assignments → shared components. The KG answer names specific engineers, specific incidents, and specific shared components — because it traversed 3 hops in a structured query.

---

## Reflection — what you built and why it works

You now have a system that:

1. **Extracts** structured facts from unstructured text (Day 7)
2. **Ingests** them idempotently into a graph (Day 8)
3. **Answers corpus-wide questions** via community detection (Day 9)
4. **Persists structured memory** across agent sessions (Day 10)
5. **Predicts missing edges** via embeddings (Day 11)
6. **Scales because the schema was designed** before extraction (Day 13)
7. **Stays correct** via validation, reconciliation, and monitoring (Day 14)

The multi-hop question is the proof. Flat RAG retrieves relevant chunks and hopes they co-occur. A KG *traverses* the graph: incident → affects → service → on_call_for → engineer is a path, not a coincidence.

---

## What's next after the capstone

You have built the core system. Three extensions are worth knowing about even if you don't implement them now:

| Extension | What it adds | Starting point |
|-----------|-------------|----------------|
| **Wikidata-style qualifiers** | Provenance and temporal scope on individual statements (not just edges) | Vrandecic & Krötzsch, CACM 2014 |
| **Knowledge Graph Question Answering (KGQA)** | Translate natural language to Cypher automatically | KGQA survey, Lan et al. 2021 |
| **Federated KGs** | Query across multiple KG databases as if they were one | SPARQL 1.1 Federated Query (W3C) |

---

## Suggested readings for today

**Required:** Go back to whichever Day page covers the component that gave you the most trouble today. Re-read §"Where it breaks / what it is not." The second read will land differently now that you've hit the production edge cases.

**If you want the deep version:**
- Edge, Jonathan et al. "From Local to Global: A Graph RAG Approach to Query-Focused Summarization." arXiv:2404.16130, 2024. Now that you've built a GraphRAG, read the original paper end-to-end — specifically Section 4 ("Community Summaries") and Section 6 ("Evaluation"). Your Day 9 implementation is a faithful subset of their architecture.
- Hogan et al., *Knowledge Graphs*, MIT Press, 2021. Chapter 9, §9.1–9.2 ("Embeddings" and "Neural Methods"). This chapter goes beyond TransE to graph neural networks (GNNs) — the direction production KG systems are moving. It is the natural Day 16 if there were one.
- Neo4j Graph Academy, "Building Neo4j Applications with Python": graphacademy.neo4j.com. The hands-on path covers connection pooling, transactions, and retry logic — production concerns that complement Day 14's monitoring code.

---

← [Day 14 — Production Concerns](day-14-production-concerns.md)
