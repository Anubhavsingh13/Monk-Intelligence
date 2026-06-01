*[Knowledge Graphs: From Concept to Production](../README.md) · Day 2 of 15*

# Day 2 — Triples: The Atomic Unit of Knowledge

> **Today's one idea:** Every fact — in any language, about any domain — can be expressed as a `(subject, predicate, object)` triple, and this universality is what makes knowledge graphs composable across datasets.
> **Reading time:** ~35 min · **Prereqs:** [Day 1](day-01-what-is-a-knowledge-graph.md)
> **Primary source for today:** Heath, Tom, and Christian Bizer. *Linked Data.* Morgan & Claypool, 2011. Chapter 2, §2.1–2.3. Free PDF at linkeddatabook.com/editions/1.0/

---

## The hook (2–4 min)

Open any sentence in any language and dissect it:

> "Einstein *discovered* the photoelectric effect."

Three parts: a *thing* (Einstein), a *relationship* (discovered), another *thing* (the photoelectric effect). You just wrote a triple.

> "The photoelectric effect *earned* Einstein *the Nobel Prize in 1921*."

Still three parts. Every declarative sentence about the world has this structure. What RDF did was make that observation a *storage format*: instead of writing prose or rows in a table, you write triples directly. The entire world's knowledge — if you could capture it — would be one big set of triples.

Why does this matter for building knowledge graphs? Because if every fact has the same shape `(subject, predicate, object)`, then any two knowledge graphs, no matter who built them, can be merged by simply combining their sets of triples — as long as they use the same names for the same things. The triple is not just a data format. It's the foundation of composability.

---

## Building the intuition (10–15 min)

### Step 1: The sentence → triple mapping

Any declarative sentence maps onto three slots:

| English | Subject | Predicate | Object |
|---------|---------|-----------|--------|
| "Einstein was born in Ulm." | Einstein | was born in | Ulm |
| "Ulm is in Germany." | Ulm | is in | Germany |
| "Einstein won the Nobel Prize." | Einstein | won | Nobel Prize in Physics 1921 |
| "The Nobel Prize was awarded in 1921." | Nobel Prize in Physics 1921 | was awarded in | 1921 |

String these together and you have a mini knowledge graph about Einstein — just by decomposing four sentences.

### Step 2: The naming problem

Now imagine two teams each build a KG about scientists. Team A writes `Einstein`. Team B writes `Albert_Einstein`. When you merge the graphs, the entity splits in two. Every fact Team A knows about "Einstein" is disconnected from every fact Team B knows about "Albert_Einstein" — even though they're the same person.

The solution: use globally unique identifiers instead of local strings. On the web, that means IRIs — *Internationalized Resource Identifiers*. An IRI is like a URL but for any concept, not just web pages:

```
http://www.wikidata.org/entity/Q937    ← Albert Einstein in Wikidata
http://www.wikidata.org/entity/Q61452  ← Photoelectric effect in Wikidata
```

These identifiers are:
- **Global** — the same IRI means the same thing anywhere on the web
- **Dereferenceable** — you can fetch them and get more facts back
- **Collision-free** — `dbpedia.org/resource/Einstein` and `wikidata.org/entity/Q937` can coexist without confusion

This is the core insight of RDF: *use IRIs so graphs from different sources can be merged without ambiguity.*

### Step 3: What the object can be

The object of a triple is not always another entity. Sometimes it's a raw value:

```
(Einstein, born_in_year, 1879)     ← year is a number, not an entity
(Einstein, name, "Albert Einstein") ← name is a string
```

RDF calls raw values **literals**. A literal has a value and a datatype:
- `"Albert Einstein"^^xsd:string`
- `1879^^xsd:integer`
- `"1879-03-14"^^xsd:date`

The key rule: **literals can only appear as the object**, never as subject or predicate. You can't say "1879 was born in Ulm" — 1879 is a value, not a named entity.

### Step 4: Assembling a real RDF graph

Install rdflib: `pip install rdflib`

```python
from rdflib import Graph, Namespace, Literal, URIRef
from rdflib.namespace import RDF, RDFS, XSD

# Create a graph
g = Graph()

# Define a namespace (your "domain" prefix)
EX = Namespace("http://example.org/scientists/")
SCHEMA = Namespace("http://schema.org/")

# Add triples: (subject, predicate, object)
g.add((EX.Einstein,          RDF.type,          EX.Physicist))
g.add((EX.Einstein,          RDFS.label,        Literal("Albert Einstein")))
g.add((EX.Einstein,          EX.bornIn,         EX.Ulm))
g.add((EX.Einstein,          EX.bornYear,       Literal(1879, datatype=XSD.integer)))
g.add((EX.Einstein,          EX.discovered,     EX.PhotoelectricEffect))
g.add((EX.Einstein,          EX.won,            EX.NobelPhysics1921))
g.add((EX.Ulm,               EX.locatedIn,      EX.Germany))
g.add((EX.PhotoelectricEffect, RDFS.label,       Literal("Photoelectric Effect")))
g.add((EX.NobelPhysics1921,  EX.awardedYear,    Literal(1921, datatype=XSD.integer)))

# Print all triples
print(f"Total triples: {len(g)}\n")
for s, p, o in g:
    print(f"  {s.split('/')[-1]:30s}  {p.split('/')[-1]:20s}  {o}")
```

**Output:**
```
Total triples: 9

  Einstein                        type                  .../Physicist
  Einstein                        label                 Albert Einstein
  Einstein                        bornIn                .../Ulm
  Einstein                        bornYear              1879
  Einstein                        discovered            .../PhotoelectricEffect
  Einstein                        won                   .../NobelPhysics1921
  Ulm                             locatedIn             .../Germany
  PhotoelectricEffect             label                 Photoelectric Effect
  NobelPhysics1921                awardedYear           1921
```

Nine sentences of English became nine triples. Now query: who is a Physicist?

```python
# Query pattern: ?entity rdf:type ex:Physicist
physicists = list(g.subjects(RDF.type, EX.Physicist))
print(f"Physicists in graph: {[str(p).split('/')[-1] for p in physicists]}")
# → ['Einstein']
```

This is a triple pattern match: find all values of `?entity` where `(?entity, rdf:type, ex:Physicist)` is in the graph.

---

## The formal picture (10–15 min)

### RDF data model

An RDF **triple** is an ordered tuple `(s, p, o)` where:

- **Subject** `s ∈ IRI ∪ BlankNode` — the entity the fact is about
- **Predicate** `p ∈ IRI` — the relationship type
- **Object** `o ∈ IRI ∪ BlankNode ∪ Literal` — the value or related entity

An **RDF graph** `G` is simply a set of triples:

```
G = {(s₁, p₁, o₁), (s₂, p₂, o₂), ..., (sₙ, pₙ, oₙ)}
```

A **triple store** is a database optimised to store and query RDF graphs.

### The three types of node

```
┌─────────────────────────────────────────────────────────────┐
│  IRI Node              │  Blank Node        │  Literal      │
│  (named entity)        │  (anonymous)       │  (raw value)  │
│  Can be S, P, or O     │  Can be S or O     │  Only O       │
│  ex:Einstein           │  _:b1              │  "1879"^^xsd  │
└─────────────────────────────────────────────────────────────┘
```

**Blank nodes** (`_:b1`, `_:b2`, ...) are anonymous resources — placeholders for entities you can't or don't want to name. They are scoped to their graph: two blank nodes in different graphs with the same label are NOT the same node. This makes blank nodes problematic for merging — use IRIs when you can.

### Namespaces and prefixes

Full IRIs are verbose. RDF allows prefix declarations to abbreviate:

```turtle
# Turtle notation (the human-readable RDF serialisation)
@prefix ex:     <http://example.org/scientists/> .
@prefix rdf:    <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix rdfs:   <http://www.w3.org/2000/01/rdf-schema#> .
@prefix xsd:    <http://www.w3.org/2001/XMLSchema#> .

ex:Einstein  rdf:type     ex:Physicist ;
             rdfs:label   "Albert Einstein" ;
             ex:bornIn    ex:Ulm ;
             ex:bornYear  "1879"^^xsd:integer .

ex:Ulm       ex:locatedIn ex:Germany .
```

The `;` after a subject means "same subject, next predicate-object pair." Turtle is the format you'll use to write ontologies on Day 4 and to export graphs for debugging.

### Serialise and load in rdflib

```python
# Serialise to Turtle
turtle_output = g.serialize(format="turtle")
print(turtle_output)

# Save to file
g.serialize("einstein.ttl", format="turtle")

# Load from file into a new graph
g2 = Graph()
g2.parse("einstein.ttl", format="turtle")
print(f"Loaded {len(g2)} triples")
```

---

## Where it breaks / what it is not (3–5 min)

**Triples can't express context without awkwardness.** `(Einstein, discovered, PhotoelectricEffect)` is a simple fact. But "Alice *claims* Einstein discovered the photoelectric effect" or "this fact was added to the graph *on 2024-01-15*" requires talking about a triple *as an entity* — a technique called **reification**. In RDF, reification requires four extra triples per annotated fact and is painful to query. Property graphs (Day 3) handle this elegantly by letting edges carry their own properties.

**Blank nodes create merge problems.** If you merge two RDF graphs both containing blank node `_:b1`, they're treated as different nodes — but if you serialize and re-parse, blank node labels may change. Never use blank nodes for entities you'll need to reference across graphs. Use IRIs.

**Literals are not entities.** You cannot write `(1879, is_the_birth_year_of, Einstein)` — 1879 is a literal and literals are objects only. If you need to reason about years as entities (e.g., "all events in 1879"), you should model the year as an IRI: `ex:year_1879`.

**RDF is verbose.** For large-scale production systems, you'll often use a property graph (Day 3) instead of raw RDF because the tooling is more mature and the model is more ergonomic. The triple model is still the right conceptual foundation — property graphs are triples with extra structure.

---

## Try it yourself (5–10 min)

**Exercise 1 — Triple decomposition (L1):** Take these five sentences and write them as `(subject, predicate, object)` triples on paper. Use short names (not full IRIs yet).

1. "LangChain is a Python framework."
2. "LangChain was created by Harrison Chase."
3. "Harrison Chase founded LangChain Inc."
4. "LangChain Inc. is headquartered in San Francisco."
5. "San Francisco is in California."

<details>
<summary>Solution</summary>

| Subject | Predicate | Object |
|---------|-----------|--------|
| LangChain | type | PythonFramework |
| LangChain | created_by | HarrisonChase |
| HarrisonChase | founded | LangChainInc |
| LangChainInc | headquartered_in | SanFrancisco |
| SanFrancisco | located_in | California |
</details>

**Exercise 2 — RDFLib (L1/L2):** Code the five triples above in rdflib. Then query: "What did Harrison Chase create or found?" using `g.subjects()` and `g.objects()`.

<details>
<summary>Hint</summary>

```python
# Find objects of a specific predicate from a specific subject:
objects = list(g.objects(EX.HarrisonChase, EX.founded))
```
</details>

<details>
<summary>Solution</summary>

```python
from rdflib import Graph, Namespace, Literal
from rdflib.namespace import RDF, RDFS

g = Graph()
EX = Namespace("http://example.org/ai/")

g.add((EX.LangChain,     RDF.type,          EX.PythonFramework))
g.add((EX.LangChain,     EX.created_by,     EX.HarrisonChase))
g.add((EX.HarrisonChase, EX.founded,        EX.LangChainInc))
g.add((EX.LangChainInc,  EX.headquartered_in, EX.SanFrancisco))
g.add((EX.SanFrancisco,  EX.located_in,     EX.California))

# What did Harrison Chase create?
created = list(g.objects(EX.HarrisonChase, EX.created_by))
founded = list(g.objects(EX.HarrisonChase, EX.founded))
print(f"Created: {[str(x).split('/')[-1] for x in created]}")
print(f"Founded: {[str(x).split('/')[-1] for x in founded]}")
# Note: created_by is reversed — fix:
created_by_him = list(g.subjects(EX.created_by, EX.HarrisonChase))
print(f"Created by Harrison: {[str(x).split('/')[-1] for x in created_by_him]}")
```
</details>

**Exercise 3 — Stretch (L2):** Reification. Express "According to Wikipedia, Einstein discovered the photoelectric effect." as RDF triples without a property graph. You'll need to create a new resource representing the *statement itself*.

<details>
<summary>Solution</summary>

```python
from rdflib.namespace import RDF

# The statement as a named resource
STMT = EX.stmt_einstein_discovered_photoelectric

g.add((STMT, RDF.type,             RDF.Statement))
g.add((STMT, RDF.subject,          EX.Einstein))
g.add((STMT, RDF.predicate,        EX.discovered))
g.add((STMT, RDF.object,           EX.PhotoelectricEffect))
g.add((STMT, EX.accordingTo,       EX.Wikipedia))

# Now you can query "what did Wikipedia say Einstein discovered?"
for stmt in g.subjects(EX.accordingTo, EX.Wikipedia):
    obj = list(g.objects(stmt, RDF.object))
    print(f"Wikipedia claims Einstein discovered: {obj}")
```

Notice how awkward this is — 5 triples to annotate 1 fact. Day 3's property graph will make this trivial.
</details>

---

## Connect it back

[Yesterday](day-01-what-is-a-knowledge-graph.md) you built a KG as a NetworkX graph with labeled edges. Today you learned that every one of those labeled edges *is* a triple, and that IRIs are what allow triples from different sources to be merged without ambiguity. Tomorrow you'll meet the property graph model — the practical evolution of the triple that makes it easier to attach metadata (confidence, timestamps, sources) directly to edges, solving the reification problem you just ran into.

**The question you can answer today that you couldn't this morning:** *Why does `(Einstein, discoveredIn, 1905)` work in RDF but `(1905, wasDiscoveredIn, PhotoelectricEffect)` doesn't?*

---

## Suggested readings for today

**Required if you have 15 extra minutes:** Heath & Bizer, *Linked Data* (Morgan & Claypool, 2011), Chapter 2, §2.1–2.3 (pp. 11–35). Free at linkeddatabook.com/editions/1.0/ — Chapter 2 is the best short explanation of why IRIs matter that exists in print.

**If you want the deep version:**
- Hogan et al., *Knowledge Graphs*, §2.1–2.3 (pp. 19–45). Covers the full RDF data model including blank nodes, literals, and named graphs — the complete formal treatment of what you built today.
- rdflib documentation: rdflib.readthedocs.io — specifically the "Getting Started" and "Navigating Graphs" sections. The official tutorial is short and concrete; read it after today's exercises.

---

← [Day 1 — What Is a Knowledge Graph?](day-01-what-is-a-knowledge-graph.md) &nbsp;|&nbsp; [Day 3 — Property Graphs in Practice →](day-03-property-graphs-in-practice.md)
