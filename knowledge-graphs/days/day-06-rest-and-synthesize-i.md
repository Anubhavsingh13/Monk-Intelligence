*[Knowledge Graphs: From Concept to Production](../README.md) · Day 6 of 15*

# Day 6 — Rest & Synthesize I

> **Today:** No new concepts. Only consolidation.
> **Format:** Re-state → Re-derive → Build → Quiz
> **Reading time:** ~30 min (longer if you go back to fix gaps — that's the point)
> **Prereqs:** [Days 1–5](../README.md)

---

This day is a deliberate pause before the building arc. Days 7–11 assume the five concepts from Days 1–5 are solid. If they're not, you'll hit walls that feel like "I don't understand Neo4j" but are actually "I never internalised what an ontology is."

The method: close all previous pages. Answer each section from memory. Only look things up after you've tried.

---

## Step 1 — Re-state the five ideas (5 min)

Without looking at any page, write one sentence for each. Your sentence should be specific enough that a colleague could distinguish the concept from its neighbours.

| Concept | Your sentence |
|---------|--------------|
| Knowledge Graph | |
| Triple | |
| Property Graph (LPG) | |
| Ontology | |
| Cypher / SPARQL | |

<details>
<summary>Compare to these</summary>

1. **Knowledge Graph:** A directed, labeled multigraph where every edge carries a named relation type — not just "A connects to B" but "A *founded* B."
2. **Triple:** The atomic unit `(subject, predicate, object)` — the minimum structure that expresses one fact; an RDF graph is simply a set of triples.
3. **Property Graph:** A graph model where both nodes and edges are first-class objects that carry key-value property dicts, solving the reification problem by putting metadata directly on the edge.
4. **Ontology:** A formal schema for a KG — defines allowed classes (`rdfs:subClassOf`), property constraints (`rdfs:domain`, `rdfs:range`), and equivalences (`owl:equivalentClass`) so separate graphs can merge consistently.
5. **Cypher / SPARQL:** Declarative pattern-matching query languages — you describe the graph shape you want (Cypher uses ASCII art; SPARQL uses triple patterns) and the engine finds all matching subgraphs.

If yours are vaguer than these, go back to that day's page before continuing.
</details>

---

## Step 2 — Re-derive from first principles (10 min)

Answer these without looking up anything. Write on paper.

**Q1.** You have this Python dict. What's wrong with using it as a knowledge graph, and what specific property of a KG fixes it?

```python
data = {"Alice": {"employer": "Google"}, "Google": {"industry": "tech"}}
```

**Q2.** Convert this sentence to an RDF triple using the `ex:` namespace. Then explain why you'd use an IRI rather than the bare string `"Alice"` as the subject.

> "Alice published a paper about transformers in 2023."

**Q3.** You have a co-authorship fact: "Alice and Bob wrote a paper together, with a confidence score of 0.87 extracted by your NLP system." Should you model this in RDF triples or a property graph? Justify.

**Q4.** What is the difference between `rdfs:domain` and `rdfs:range`? Give one example of each using the property `co_authored_with`.

**Q5.** Write the Cypher MATCH clause that finds: "all Researchers who co-authored papers with someone who works at Google."

<details>
<summary>Answers</summary>

**A1.** The dict stores relationships *inside* nodes as values. Finding "all Alice's employers" requires scanning every key-value. A KG stores relationships as named, directed edges between nodes — `(Alice)-[:WORKS_AT]->(Google)` — making traversal a direct graph operation instead of a linear scan.

**A2.**
```turtle
ex:Alice  ex:published  ex:TransformerPaper_2023 .
ex:TransformerPaper_2023  ex:publishedYear  "2023"^^xsd:integer .
```
You use an IRI (`ex:Alice`) not the bare string `"Alice"` because two datasets might both have an entity called "Alice" referring to different people. IRIs are globally scoped: `http://example.org/people/alice-chen-ntu` is unambiguous; the string `"Alice"` is not.

**A3.** Property graph. The confidence score `0.87` is metadata about the co-authorship *relationship* itself — it belongs on the edge as `{confidence: 0.87}`. In RDF, this requires awkward reification (4+ extra triples). In a property graph it's one edge with one property.

**A4.**
- `rdfs:domain P C` — *subjects* of property P must be of type C. Example: `co_authored_with rdfs:domain Researcher` — only Researchers can co-author.
- `rdfs:range P C` — *objects* of property P must be of type C. Example: `co_authored_with rdfs:range Researcher` — you can only co-author with Researchers.

**A5.**
```cypher
MATCH (r:Researcher)-[:CO_AUTHORED]->(p:Paper)<-[:CO_AUTHORED]-(colleague)
      -[:WORKS_AT]->(:Company {name: "Google"})
RETURN r.name, colleague.name, p.title
```
</details>

---

## Step 3 — Build a 10-node KG (15 min)

Design and build a knowledge graph in your own domain. It must have:
- Exactly 10 nodes across at least 3 distinct types
- At least 12 edges using at least 4 distinct relation types
- At least 2 edge properties (metadata on edges)
- At least 1 multi-hop query that answers a question you actually care about

Use the `PropertyGraph` class from Day 3. Draw it on paper first, then code it.

**Suggested domains** (pick yours, not these):
- AI research papers you've read
- Tools and frameworks in a project you've worked on
- An agent system's components, tools, and data sources
- Your organisation's teams, products, and technologies

```python
# Template — fill in with your domain
from dataclasses import dataclass, field
from typing import Any

@dataclass
class Node:
    id: str
    labels: list[str] = field(default_factory=list)
    props: dict[str, Any] = field(default_factory=dict)

@dataclass
class Edge:
    id: str
    type: str
    start: "Node"
    end: "Node"
    props: dict[str, Any] = field(default_factory=dict)

class PropertyGraph:
    def __init__(self):
        self.nodes: dict[str, Node] = {}
        self.edges: list[Edge] = []
        self._n = 0

    def add_node(self, id, labels=None, **props):
        n = Node(id=id, labels=labels or [], props=props)
        self.nodes[id] = n
        return n

    def add_edge(self, s, t, rel, **props):
        self._n += 1
        e = Edge(id=f"e{self._n}", type=rel,
                 start=self.nodes[s], end=self.nodes[t], props=props)
        self.edges.append(e)
        return e

    def follow(self, node_id, rel):
        return [e.end for e in self.edges
                if e.start.id == node_id and e.type == rel]

    def match_nodes(self, label=None, **props):
        nodes = list(self.nodes.values())
        if label:
            nodes = [n for n in nodes if label in n.labels]
        for k, v in props.items():
            nodes = [n for n in nodes if n.props.get(k) == v]
        return nodes

# Your KG here:
pg = PropertyGraph()

# Add your nodes and edges...
# Then write and run your multi-hop query.
```

**Checkpoint before moving on:** Can you answer your multi-hop query in one traversal, and can you express the same query as a Cypher MATCH clause (even though you're running it in Python)?

---

## Step 4 — Quiz (5 min)

Five true/false questions. Answer before looking at the answers.

1. `(Alice)-[:WORKS_AT]->(Google)` and `(Google)-[:WORKS_AT]->(Alice)` are the same edge in a property graph.
2. A Literal in RDF can appear as the subject of a triple.
3. The Open World Assumption means: if a fact is not in the KG, it is false.
4. `rdfs:subClassOf` enables RDFS reasoners to infer that instances of a subclass are also instances of the parent class.
5. In Cypher, `MATCH (a)-[:KNOWS*]->(b)` with no upper bound is safe to run on a large production graph.

<details>
<summary>Answers</summary>

1. **False.** Direction matters. `(Alice)-[:WORKS_AT]->(Google)` means Alice works at Google. The reverse means Google works at Alice — a different (invalid) claim.
2. **False.** Literals can only appear as the *object* of a triple, never as subject or predicate.
3. **False.** The Open World Assumption says absence of a fact means *unknown*, not false. This is the opposite of the Closed World Assumption in relational databases.
4. **True.** This is the subclass propagation rule: if `X rdf:type C` and `C rdfs:subClassOf D`, then a reasoner infers `X rdf:type D`.
5. **False.** Unbounded path traversal explores the entire graph and will timeout or crash on large graphs. Always set an upper bound: `[:KNOWS*1..4]`.
</details>

---

## What's ahead

Days 7–11 are the construction arc. Every day produces a working artifact:

| Day | What you'll build |
|-----|------------------|
| 7 | An NLP + LLM pipeline that reads a document and outputs triples |
| 8 | An end-to-end ingestion pipeline: raw docs → Neo4j |
| 9 | A GraphRAG retrieval system that answers multi-hop questions |
| 10 | An LLM agent with a Neo4j KG as its persistent memory |
| 11 | A TransE embedding model trained on your KG for link prediction |

You'll need Python and a running Neo4j instance for Days 8–10. Install and start Neo4j before Day 8:

```bash
# Option A: Docker (recommended)
docker run \
  --name neo4j-kg \
  -p 7474:7474 -p 7687:7687 \
  -e NEO4J_AUTH=neo4j/password123 \
  neo4j:latest

# Option B: Desktop app
# Download from: neo4j.com/download/

# Python dependencies
pip install neo4j rdflib spacy networkx pykeen
python -m spacy download en_core_web_sm
```

Verify your Neo4j connection before starting Day 8:

```python
from neo4j import GraphDatabase

driver = GraphDatabase.driver("bolt://localhost:7687", auth=("neo4j", "password123"))
with driver.session() as session:
    result = session.run("RETURN 1 AS n")
    print(result.single()["n"])  # → 1
driver.close()
```

---

## Connect it back

You've just verified that the five conceptual pillars are solid. The rest-and-synthesize format exists because KG concepts compound: if you're shaky on triples, the ingestion pipeline on Day 8 will feel arbitrary. If you're solid, it will feel inevitable. If any of the Step 2 answers surprised you, re-read that day's page before tomorrow.

**The question you carry into Day 7:** *You have 500 paragraphs of text. What information do you need to extract from each paragraph to populate a knowledge graph?*

---

← [Day 5 — Querying: Cypher & SPARQL](day-05-querying-cypher-sparql.md) &nbsp;|&nbsp; [Day 7 — Entity & Relation Extraction →](day-07-entity-relation-extraction.md)
