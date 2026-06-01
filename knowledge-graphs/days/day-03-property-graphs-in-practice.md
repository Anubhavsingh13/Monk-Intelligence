*[Knowledge Graphs: From Concept to Production](../README.md) · Day 3 of 15*

# Day 3 — Property Graphs in Practice

> **Today's one idea:** A property graph attaches key-value metadata directly to both nodes *and* edges, making edges first-class objects that can carry context — confidence, timestamps, provenance — without reification.
> **Reading time:** ~35 min · **Prereqs:** [Day 1](day-01-what-is-a-knowledge-graph.md), [Day 2](day-02-triples-atomic-unit.md)
> **Primary source for today:** Robinson, Ian, Jim Webber, and Emil Eifrem. *Graph Databases.* O'Reilly, 2nd ed., 2015. Chapter 2, "Graph Theory and Databases." Free edition at neo4j.com/graph-databases-book/

---

## The hook (2–4 min)

You have a research collaboration network. Two facts about the same paper:

- Alice and Bob co-authored a paper.
- That co-authorship happened in **2022**, was a **journal paper**, and your NLP extractor assigned it a confidence of **0.91**.

In pure RDF triples, `(Alice, co_authored_with, Bob)` gives you the first fact. But where do the year, paper type, and confidence go? You'd need to reify the statement — creating a new resource to represent the *fact itself* and attaching properties to it. [Yesterday](day-02-triples-atomic-unit.md) you saw how awkward that is: 5 triples to annotate 1 fact.

The property graph model solves this directly. Edges are *objects* with their own property dictionaries. You write:

```
(Alice) --[CO_AUTHORED {year: 2022, type: "journal", confidence: 0.91}]--> (Bob)
```

One edge. Three properties. No reification. This is why most live production systems — Neo4j, Amazon Neptune in Bolt mode, TigerGraph — use the property graph model rather than raw RDF.

---

## Building the intuition (10–15 min)

### What "property graph" actually means

Think of the property graph as RDF with two upgrades:

1. **Edges get a property bag.** Instead of a bare label, every edge can carry any number of key-value pairs.
2. **Nodes get label sets.** A node can have multiple labels (type tags), not just one. `Alice` could be labeled both `:Person` and `:Researcher`.

Here's the mental model side by side:

```
RDF Triple:
  (Alice)  --co_authored_with-->  (Bob)
   IRI              IRI             IRI
   [no metadata on the edge]

Property Graph Edge:
  (Alice:Person)  --CO_AUTHORED {year:2022, confidence:0.91}-->  (Bob:Person)
   node+labels    edge type + properties dict                      node+labels
```

The property graph model doesn't give up anything RDF can express. It adds expressiveness: edges become first-class citizens that carry their own data.

### The Labeled Property Graph (LPG) model

Neo4j's specific variant is called the **Labeled Property Graph**. Its rules:

| Component | Can have | Example |
|-----------|----------|---------|
| Node | Multiple labels + properties dict | `(:Person:Researcher {name: "Alice", age: 32})` |
| Edge | One type + properties dict + direction | `-[:CO_AUTHORED {year: 2022}]->` |
| Property value | String, number, boolean, list | `{score: 0.91, tags: ["NLP", "KG"]}` |

Every node and edge has a system-assigned unique ID. Labels are type tags (typically written in CamelCase for nodes: `Person`, `Company`). Edge types are typically written in SCREAMING_SNAKE_CASE: `WORKS_FOR`, `CO_AUTHORED`.

### Build a property graph in pure Python

Before setting up Neo4j, here's a minimal property graph implementation to make the model concrete:

```python
from dataclasses import dataclass, field
from typing import Any

@dataclass
class Node:
    id: str
    labels: list[str] = field(default_factory=list)
    props: dict[str, Any] = field(default_factory=dict)

    def __repr__(self):
        label_str = ":".join(self.labels)
        return f"(:{label_str} {self.props})"

@dataclass
class Edge:
    id: str
    type: str
    start: Node
    end: Node
    props: dict[str, Any] = field(default_factory=dict)

    def __repr__(self):
        return f"({self.start.id})-[:{self.type} {self.props}]->({self.end.id})"

class PropertyGraph:
    def __init__(self):
        self.nodes: dict[str, Node] = {}
        self.edges: list[Edge] = []
        self._edge_counter = 0

    def add_node(self, id: str, labels: list[str] = None, **props) -> Node:
        node = Node(id=id, labels=labels or [], props=props)
        self.nodes[id] = node
        return node

    def add_edge(self, start_id: str, type: str, end_id: str, **props) -> Edge:
        self._edge_counter += 1
        edge = Edge(
            id=f"e{self._edge_counter}",
            type=type,
            start=self.nodes[start_id],
            end=self.nodes[end_id],
            props=props,
        )
        self.edges.append(edge)
        return edge

    def match_edges(self, type: str = None, start_id: str = None, **prop_filters) -> list[Edge]:
        results = self.edges
        if type:
            results = [e for e in results if e.type == type]
        if start_id:
            results = [e for e in results if e.start.id == start_id]
        for key, val in prop_filters.items():
            results = [e for e in results if e.props.get(key) == val]
        return results

    def match_nodes(self, label: str = None, **prop_filters) -> list[Node]:
        results = list(self.nodes.values())
        if label:
            results = [n for n in results if label in n.labels]
        for key, val in prop_filters.items():
            results = [n for n in results if n.props.get(key) == val]
        return results
```

Now build a collaboration network:

```python
pg = PropertyGraph()

# Nodes with labels and properties
alice   = pg.add_node("alice",   ["Person", "Researcher"], name="Alice Chen",  field="NLP")
bob     = pg.add_node("bob",     ["Person", "Researcher"], name="Bob Kumar",   field="KG")
paper1  = pg.add_node("paper1",  ["Paper"],                title="KG+LLM Survey", year=2022)
ntu     = pg.add_node("ntu",     ["University"],           name="NTU Singapore")

# Edges with metadata — NO reification needed
pg.add_edge("alice", "CO_AUTHORED", "paper1",
            year=2022, paper_type="journal", confidence=0.91)
pg.add_edge("bob",   "CO_AUTHORED", "paper1",
            year=2022, paper_type="journal", confidence=0.91)
pg.add_edge("alice", "AFFILIATED_WITH", "ntu",
            since=2019, role="Associate Professor")

# Query 1: all co-authorships with confidence > 0.85
high_conf = [e for e in pg.match_edges(type="CO_AUTHORED")
             if e.props.get("confidence", 0) > 0.85]
for e in high_conf:
    print(f"{e.start.props['name']} co-authored {e.end.props['title']} "
          f"(conf={e.props['confidence']})")

# Query 2: all Researchers
researchers = pg.match_nodes(label="Researcher")
print(f"\nResearchers: {[n.props['name'] for n in researchers]}")
```

**Output:**
```
Alice Chen co-authored KG+LLM Survey (conf=0.91)
Bob Kumar co-authored KG+LLM Survey (conf=0.91)

Researchers: ['Alice Chen', 'Bob Kumar']
```

### The same graph in Neo4j Cypher (preview of Day 5)

When you connect to a Neo4j instance, you'd write this as:

```cypher
CREATE (:Person:Researcher {name: "Alice Chen",  field: "NLP"})
CREATE (:Person:Researcher {name: "Bob Kumar",   field: "KG"})
CREATE (:Paper             {title: "KG+LLM Survey", year: 2022})
CREATE (:University        {name: "NTU Singapore"})

MATCH (a:Person {name:"Alice Chen"}), (p:Paper {title:"KG+LLM Survey"})
CREATE (a)-[:CO_AUTHORED {year:2022, paper_type:"journal", confidence:0.91}]->(p)
```

You'll write this fluently after Day 5. For now, notice how the Cypher syntax mirrors the ASCII diagram from earlier: parentheses for nodes, arrows and square brackets for edges.

---

## The formal picture (10–15 min)

The LPG model is defined as:

```
LPG = (V, E, ρ, Λ_V, Λ_E, σ_V, σ_E)
```

Where:
- **V** — set of nodes (vertices)
- **E** — set of edges (directed)
- **ρ: E → V × V** — maps each edge to its (start, end) node pair
- **Λ_V: V → P(Labels)** — assigns a *set* of labels to each node
- **Λ_E: E → Labels** — assigns exactly *one* type to each edge
- **σ_V: V → Properties** — assigns a key-value property map to each node
- **σ_E: E → Properties** — assigns a key-value property map to each edge

The critical difference from RDF: `σ_E` — edges have their own property maps. In RDF, there is no equivalent; all information lives in triples hanging off IRIs.

### RDF vs. Property Graph: when to use which

| Concern | RDF | Property Graph (Neo4j) |
|---------|-----|----------------------|
| Schema enforcement | RDFS/OWL reasoners | Constraints in Neo4j |
| Edge metadata | Reification (awkward) | Native edge properties |
| Querying | SPARQL | Cypher (more readable) |
| Interoperability | W3C standard, web-scale | Proprietary (but widely adopted) |
| Data integration across orgs | Strong (IRIs = global IDs) | Weaker (local IDs) |
| Ecosystem maturity | Academic + enterprise | Very strong tooling |
| Best for | Linked Open Data, cross-org KGs | Application KGs, agent memory |

For the **live project use cases** in this course (agent memory, GraphRAG, entity extraction pipelines), you'll use the **property graph / Neo4j** model for everything except cross-organisation data integration.

---

## Where it breaks / what it is not (3–5 min)

**Property graphs are not relational databases.** There are no tables, no foreign keys, no JOIN operations. Two nodes with the same label (e.g., both `:Person`) are not "rows in the same table" — they're independent nodes. Schema constraints are optional, not enforced by default.

**Edge direction matters.** `(Alice)-[:MANAGES]->(Bob)` and `(Bob)-[:MANAGES]->(Alice)` are different edges. When you query for "who does Alice manage?" you traverse *outgoing* `MANAGES` edges. If you accidentally create the edge in the wrong direction, the query returns nothing and you'll spend 20 minutes debugging. Always be explicit about directionality.

**Property values are not nodes.** In a property graph, `{confidence: 0.91}` is an *attribute* of an edge, not an entity. You cannot traverse to `0.91` or ask "which edges have a higher confidence than this value, connected to this node, via two hops" — that requires a query filter, not a traversal. If you need to reason about a value as an entity, promote it to a node.

**Neo4j is not RDF.** Neo4j supports an optional RDF import plugin (Neosemantics / `n10s`) but its native model is LPG, not RDF. If you need standard SPARQL compatibility, use a triple store like Apache Jena, Stardog, or Oxigraph.

---

## Try it yourself (5–10 min)

**Exercise 1 — Identify edge properties (L1):** For each of these facts, identify what *properties* would live on the edge (not the nodes):

1. "Alice reviewed Bob's code on 2024-03-15 and approved it."
2. "The LangChain repo depends on the OpenAI SDK at version ≥1.0."
3. "Agent A called Tool B with latency 120ms and returned an error."

<details>
<summary>Solution</summary>

1. Edge type: `REVIEWED` / Properties: `{date: "2024-03-15", verdict: "approved"}`
2. Edge type: `DEPENDS_ON` / Properties: `{version_constraint: ">=1.0", dependency_type: "runtime"}`
3. Edge type: `CALLED` / Properties: `{latency_ms: 120, status: "error"}`
</details>

**Exercise 2 — Extend the graph (L1/L2):** Using the `PropertyGraph` class from above, extend the collaboration graph to add:
- A second paper that only Alice authored (solo-authored in 2023)
- A `CITES` edge from paper1 to a third paper (you choose the title)
- A `KNOWS` edge between Alice and Bob with a `since` property

Then query: "What papers has Alice been involved in?"

<details>
<summary>Solution</summary>

```python
paper2 = pg.add_node("paper2", ["Paper"], title="LLM Reasoning Survey", year=2023)
paper3 = pg.add_node("paper3", ["Paper"], title="Attention is All You Need", year=2017)

pg.add_edge("alice", "CO_AUTHORED", "paper2", year=2023, paper_type="arxiv", confidence=1.0)
pg.add_edge("paper1", "CITES", "paper3", context="transformer architecture")
pg.add_edge("alice", "KNOWS", "bob", since=2020, context="PhD cohort")

# Papers Alice co-authored
alice_papers = [e.end for e in pg.match_edges(type="CO_AUTHORED", start_id="alice")]
print([p.props["title"] for p in alice_papers])
# → ['KG+LLM Survey', 'LLM Reasoning Survey']
```
</details>

**Exercise 3 — Stretch (L2):** Implement a `shortest_path(graph, start_id, end_id)` function on `PropertyGraph` using BFS that ignores edge types. Use it to find the shortest path between `"bob"` and `"ntu"` in the extended graph.

<details>
<summary>Solution</summary>

```python
from collections import deque

def shortest_path(graph, start_id, end_id):
    visited = {start_id}
    queue = deque([[start_id]])
    while queue:
        path = queue.popleft()
        current = path[-1]
        if current == end_id:
            return path
        neighbors = [e.end.id for e in graph.edges if e.start.id == current
                     and e.end.id not in visited]
        for n in neighbors:
            visited.add(n)
            queue.append(path + [n])
    return None

print(shortest_path(pg, "bob", "ntu"))
# → ['bob', 'paper1', 'alice', 'ntu']  (via co-authorship → alice → affiliation)
```
</details>

---

## Connect it back

[Yesterday](day-02-triples-atomic-unit.md) you saw the RDF triple model and hit the reification problem: attaching metadata to a fact requires awkward extra triples. Today the property graph solved that by making edges first-class objects with their own properties. Tomorrow you'll learn how to define the *rules* of a graph — an ontology — so that queries are consistent and your agent can trust the schema. The property graph model you built today is what the ontology will govern.

**The question you can answer today that you couldn't this morning:** *Why does `{confidence: 0.91}` live on the edge rather than as a separate node in a property graph?*

---

## Suggested readings for today

**Required if you have 15 extra minutes:** Robinson et al., *Graph Databases* (O'Reilly, 2015), Chapter 2, "Graph Theory and Databases," pp. 11–33. Free at neo4j.com/graph-databases-book/ — best short explanation of why the LPG model outperforms relational databases for connected data.

**If you want the deep version:**
- Neo4j Graph Academy, "Neo4j Fundamentals" course (free, ~1 hour): graphacademy.neo4j.com. Run the interactive exercises — this is the hands-on complement to today's page. Complete it before Day 5.
- Hogan et al., *Knowledge Graphs*, §2.2 "Graph Data Models" (pp. 29–48). Covers the RDF-vs-LPG comparison with formal precision, including a discussion of which operations are easier in each model.
- Robinson et al., Chapter 3 "A Graph Database Application," pp. 35–60. A worked example of a full property graph design for a social network. Preview of the design patterns Day 13 will formalise.

---

← [Day 2 — Triples: The Atomic Unit](day-02-triples-atomic-unit.md) &nbsp;|&nbsp; [Day 4 — Ontologies: The Grammar →](day-04-ontologies-grammar-of-a-graph.md)
