*[Knowledge Graphs: From Concept to Production](../README.md) · Day 5 of 15*

# Day 5 — Querying Your Graph: Cypher and SPARQL

> **Today's one idea:** Cypher and SPARQL are declarative *pattern-matching* languages — you describe the graph shape you want, and the engine finds all subgraphs that match it — which is fundamentally different from SQL's row-filtering logic.
> **Reading time:** ~35 min · **Prereqs:** [Day 1](day-01-what-is-a-knowledge-graph.md), [Day 2](day-02-triples-atomic-unit.md), [Day 3](day-03-property-graphs-in-practice.md), [Day 4](day-04-ontologies-grammar-of-a-graph.md)
> **Primary source for today:** Robinson, Ian, Jim Webber, and Emil Eifrem. *Graph Databases.* O'Reilly, 2015. Chapter 3, "Querying Graph Databases." Free at neo4j.com/graph-databases-book/

---

## The hook (2–4 min)

You have a graph of 100,000 entities. You want: *"All AI researchers who work for companies founded after 2015 that received funding from investors headquartered in the USA."*

In SQL, that's 4 tables, 3 JOINs, 2 subqueries, and a WHERE clause with multiple conditions. In Cypher:

```cypher
MATCH (r:Researcher)-[:WORKS_FOR]->(c:Company)-[:FUNDED_BY]->(i:Investor)-[:HQ_IN]->(:Country {name: "USA"})
WHERE c.founded_year > 2015
  AND r.field = "AI"
RETURN r.name, c.name, i.name
```

Three hops. One MATCH. No JOINs. The pattern *is* the query.

This is not just syntactic sugar. The underlying evaluation model is different. SQL filters rows. Cypher traverses a graph and pattern-matches on structure. For connected data, traversal beats filtering every time — both in expressiveness and in performance.

Today you learn both Cypher (for property graphs / Neo4j) and SPARQL (for RDF / triple stores). After today, you can ask your graph any question it can answer.

---

## Building the intuition (10–15 min)

### The core idea: draw what you want

Cypher's design principle: the query should look like what it's finding.

```
What you want:        A person who works for a company in the USA
Cypher:              (p:Person)-[:WORKS_FOR]->(c:Company)-[:HQ_IN]->(:Country {name:"USA"})
What you wrote:       "a Person, that WORKS_FOR, a Company, that HQ_IN, a Country named USA"
```

Read Cypher left-to-right like a sentence. Parentheses `()` are nodes. Square brackets `[]` with arrows `-->` or `<--` are edges. Colons `:` denote labels or types. Curly braces `{}` are property filters.

SPARQL's design principle: list the triple patterns that must all be true simultaneously.

```sparql
SELECT ?p ?c
WHERE {
  ?p rdf:type    ex:Person .
  ?p ex:worksFor ?c .
  ?c ex:hqIn     ex:USA .
}
```

Read each line as "there exists a binding where this triple is in the graph." Variables start with `?`. The `WHERE` block is satisfied when all patterns match simultaneously.

### Build a sample graph in Python to query

First, a pure-Python property graph (no Neo4j needed) to query with code:

```python
# Re-using the PropertyGraph class from Day 3
# (paste the full class definition from day-03 here, or import from a shared module)

pg = PropertyGraph()

# Nodes
alice   = pg.add_node("alice",   ["Person", "Researcher"], name="Alice Chen",  field="AI")
bob     = pg.add_node("bob",     ["Person", "Researcher"], name="Bob Kumar",   field="KG")
carol   = pg.add_node("carol",   ["Person", "Engineer"],   name="Carol Smith", field="AI")
toolagen= pg.add_node("toolagen",["Company"],              name="ToolaGen",    founded_year=2023)
deepmind= pg.add_node("deepmind",["Company"],              name="DeepMind",    founded_year=2010)
usa     = pg.add_node("usa",     ["Country"],              name="USA")
uk      = pg.add_node("uk",      ["Country"],              name="UK")
accel   = pg.add_node("accel",   ["Investor"],             name="Accel Partners")

pg.add_edge("alice",    "WORKS_FOR",  "toolagen")
pg.add_edge("bob",      "WORKS_FOR",  "toolagen")
pg.add_edge("carol",    "WORKS_FOR",  "deepmind")
pg.add_edge("toolagen", "HQ_IN",      "usa")
pg.add_edge("deepmind", "HQ_IN",      "uk")
pg.add_edge("toolagen", "FUNDED_BY",  "accel")
pg.add_edge("accel",    "HQ_IN",      "usa")
```

Now implement and run queries:

```python
def cypher_like_query(pg, label=None, field=None, company_min_year=None):
    """
    Mimics: MATCH (r:Researcher)-[:WORKS_FOR]->(c:Company)
            WHERE r.field = field AND c.founded_year > year
            RETURN r.name, c.name
    """
    results = []
    for edge in pg.match_edges(type="WORKS_FOR"):
        researcher = edge.start
        company    = edge.end
        # Filter: researcher must have target label
        if label and label not in researcher.labels:
            continue
        # Filter: field must match
        if field and researcher.props.get("field") != field:
            continue
        # Filter: company founded after threshold
        if company_min_year:
            if company.props.get("founded_year", 0) <= company_min_year:
                continue
        results.append({
            "researcher": researcher.props["name"],
            "company":    company.props["name"],
        })
    return results

print(cypher_like_query(pg, label="Researcher", field="AI", company_min_year=2015))
# → [{'researcher': 'Alice Chen', 'company': 'ToolaGen'}]
# Carol is an Engineer, not Researcher; Bob's field is KG; DeepMind founded 2010
```

---

## The formal picture (10–15 min)

### Cypher in depth

**Anatomy of a Cypher query:**

```cypher
MATCH   <graph pattern>          -- find subgraphs that match this shape
WHERE   <filter conditions>      -- apply additional filters
RETURN  <what to return>         -- project the output
```

Extended clauses:

```cypher
OPTIONAL MATCH  -- like LEFT JOIN: include rows even if pattern doesn't match
WITH            -- pipeline: pass results to the next query stage
ORDER BY        -- sort
LIMIT / SKIP    -- paginate
CREATE          -- add new nodes/edges
MERGE           -- create if not exists, match if it does
SET             -- update properties
DELETE          -- remove nodes/edges
```

**Cypher pattern reference:**

```cypher
-- Any node:
()

-- Node with label:
(:Person)

-- Node with label and variable:
(p:Person)

-- Node with label and property filter:
(p:Person {name: "Alice"})

-- Directed edge:
(a)-[:WORKS_FOR]->(b)

-- Undirected edge (match either direction):
(a)-[:KNOWS]-(b)

-- Edge with variable:
(a)-[r:WORKS_FOR]->(b)

-- Edge with property filter:
(a)-[:CO_AUTHORED {year: 2022}]->(p)

-- Variable-length path (1 to 3 hops):
(a)-[:KNOWS*1..3]->(b)

-- Any path length:
(a)-[:KNOWS*]->(b)
```

**Five queries every KG developer writes:**

```cypher
-- 1. Find all nodes of a type
MATCH (p:Person) RETURN p.name LIMIT 10

-- 2. Find neighbours of a specific node
MATCH (:Person {name: "Alice"})-[:WORKS_FOR]->(c:Company)
RETURN c.name

-- 3. Two-hop traversal
MATCH (:Person {name: "Alice"})-[:WORKS_FOR]->(:Company)-[:HQ_IN]->(country:Country)
RETURN country.name

-- 4. Aggregate: how many people per company?
MATCH (p:Person)-[:WORKS_FOR]->(c:Company)
RETURN c.name, count(p) AS employee_count
ORDER BY employee_count DESC

-- 5. Find paths between two nodes
MATCH path = shortestPath(
  (:Person {name:"Alice"})-[*]-(:Person {name:"Carol"})
)
RETURN path
```

### SPARQL in depth

**Anatomy of a SPARQL query:**

```sparql
PREFIX ex:  <http://example.org/>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>

SELECT  ?subject ?predicate ?object    -- variables to return
WHERE {
  ?subject  rdf:type    ex:Person .    -- triple patterns
  ?subject  ex:name     ?name .
  ?subject  ex:worksFor ?company .
  FILTER (?name != "Bob")              -- inline filter
}
ORDER BY ?name
LIMIT 10
```

**SPARQL pattern reference:**

```sparql
-- Basic triple pattern (variables with ?):
?s  ex:worksFor  ?c .

-- Fixed subject:
ex:Alice  ex:worksFor  ?c .

-- OPTIONAL (like LEFT JOIN):
OPTIONAL { ?p ex:age ?age . }

-- FILTER:
FILTER (?year > 2015)
FILTER (regex(?name, "^A", "i"))

-- UNION:
{ ?p ex:worksFor ex:TechCo } UNION { ?p ex:freelanceFor ex:TechCo }

-- BIND (compute new variable):
BIND (xsd:integer(?year_str) AS ?year)

-- COUNT / GROUP BY:
SELECT ?company (COUNT(?p) AS ?emp_count)
WHERE { ?p ex:worksFor ?company }
GROUP BY ?company
ORDER BY DESC(?emp_count)
```

**The same five queries in SPARQL (running on the rdflib graph from Day 2/4):**

```python
from rdflib import Graph, Namespace
from rdflib.namespace import RDF, RDFS

g = Graph()
EX = Namespace("http://example.org/")

# Load your data (re-using ontology.ttl + instance data from Day 4)
# g.parse("ontology.ttl")

# Add sample data
g.add((EX.alice,    RDF.type,       EX.Person))
g.add((EX.alice,    EX.name,        Literal("Alice Chen")))
g.add((EX.alice,    EX.worksFor,    EX.toolagen))
g.add((EX.alice,    EX.field,       Literal("AI")))
g.add((EX.bob,      RDF.type,       EX.Person))
g.add((EX.bob,      EX.name,        Literal("Bob Kumar")))
g.add((EX.bob,      EX.worksFor,    EX.toolagen))
g.add((EX.toolagen, RDF.type,       EX.Company))
g.add((EX.toolagen, EX.name,        Literal("ToolaGen")))
g.add((EX.toolagen, EX.hqIn,        EX.usa))
g.add((EX.usa,      RDF.type,       EX.Country))
g.add((EX.usa,      EX.name,        Literal("USA")))

from rdflib import Literal

# Query 1: all Persons
q1 = """
PREFIX ex: <http://example.org/>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
SELECT ?name WHERE {
  ?p rdf:type ex:Person ;
     ex:name  ?name .
}"""
for row in g.query(q1):
    print(f"Person: {row.name}")

# Query 2: two-hop — what country does Alice's employer operate in?
q2 = """
PREFIX ex: <http://example.org/>
SELECT ?country_name WHERE {
  ex:alice  ex:worksFor  ?company .
  ?company  ex:hqIn      ?country .
  ?country  ex:name      ?country_name .
}"""
for row in g.query(q2):
    print(f"Alice's employer is in: {row.country_name}")
```

### Cypher vs. SPARQL: a direct comparison

Both queries find "AI researchers at companies founded after 2015 based in the USA":

**Cypher:**
```cypher
MATCH (r:Researcher {field: "AI"})-[:WORKS_FOR]->(c:Company)-[:HQ_IN]->(:Country {name: "USA"})
WHERE c.founded_year > 2015
RETURN r.name, c.name
```

**SPARQL:**
```sparql
PREFIX ex: <http://example.org/>
SELECT ?rname ?cname WHERE {
  ?r  ex:type         ex:Researcher ;
      ex:field        "AI" ;
      ex:worksFor     ?c ;
  ?c  ex:foundedYear  ?year ;
      ex:hqIn         ?country ;
      ex:name         ?cname .
  ?r  ex:name         ?rname .
  ?country ex:name    "USA" .
  FILTER (?year > 2015)
}
```

Cypher is more readable for multi-hop path queries. SPARQL is more portable (runs on any compliant triple store). For live projects with Neo4j, use Cypher. For cross-system integration with RDF, use SPARQL.

---

## Where it breaks / what it is not (3–5 min)

**Variable-length paths are expensive.** `(a)-[:KNOWS*]->(b)` with no upper bound can explore the entire graph. Always set an upper bound: `[:KNOWS*1..4]`. In production, unbounded path queries will time out or kill your database.

**MATCH without an index is a full scan.** In Neo4j, if you `MATCH (p:Person {name: "Alice"})` and there's no index on `Person.name`, Neo4j scans every `:Person` node. Always create indexes for properties you filter on frequently:

```cypher
CREATE INDEX person_name FOR (p:Person) ON (p.name)
```

**OPTIONAL MATCH is not free.** `OPTIONAL MATCH` must evaluate the optional pattern for every row in the result set so far. Use it sparingly in large graphs.

**SPARQL variables are unordered sets.** SPARQL `SELECT` returns a multiset — like a SQL `SELECT DISTINCT` but with set semantics. If you need ordering, use `ORDER BY`. If you need counts, use `COUNT()` in an aggregation.

**Cypher identifiers are case-sensitive.** `:Person` and `:person` are different labels. Conventionally: node labels in CamelCase (`Person`, `Company`), relationship types in SCREAMING_SNAKE_CASE (`WORKS_FOR`), property keys in camelCase (`foundedYear`). Follow this or your queries will silently return nothing.

---

## Try it yourself (5–10 min)

**Exercise 1 — Pattern reading (L1):** Translate these Cypher patterns into English sentences:

1. `MATCH (d:Developer)-[:COMMITTED_TO]->(r:Repository {lang: "Python"})`
2. `MATCH (a:Agent)-[:CALLED {status: "error"}]->(t:Tool)<-[:OWNS]-(u:User)`
3. `MATCH (p:Paper)-[:CITES*2..4]->(foundational:Paper {year: 1950})`

<details>
<summary>Solutions</summary>

1. "Find developers who have committed to Python-language repositories."
2. "Find agents that made error-status calls to tools owned by users."
3. "Find papers that cite (via 2 to 4 citation hops) papers written in 1950."
</details>

**Exercise 2 — Write Cypher (L1/L2):** Using the `PropertyGraph` from today, write Python code that mimics these two Cypher patterns:

1. Find all `Researcher` nodes whose `field` is `"AI"`.
2. Find the countries of all companies that Alice works for (two-hop).

<details>
<summary>Solution</summary>

```python
# 1. All AI Researchers
ai_researchers = pg.match_nodes(label="Researcher", field="AI")
print([n.props["name"] for n in ai_researchers])

# 2. Two-hop: Alice → WORKS_FOR → Company → HQ_IN → Country
alice_companies = [e.end for e in pg.match_edges(type="WORKS_FOR", start_id="alice")]
countries = [
    e.end for c in alice_companies
    for e in pg.match_edges(type="HQ_IN", start_id=c.id)
]
print([c.props["name"] for c in countries])
# → ['USA']
```
</details>

**Exercise 3 — SPARQL (L2):** Using the rdflib graph from today's formal section, write a SPARQL query that returns: "all people who work for a company headquartered in the USA, along with the company name." Run it with `g.query()`.

<details>
<summary>Solution</summary>

```python
q = """
PREFIX ex: <http://example.org/>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
SELECT ?person_name ?company_name WHERE {
  ?p  rdf:type     ex:Person ;
      ex:name      ?person_name ;
      ex:worksFor  ?c .
  ?c  ex:name      ?company_name ;
      ex:hqIn      ?country .
  ?country ex:name "USA" .
}
ORDER BY ?person_name
"""
for row in g.query(q):
    print(f"{row.person_name} works at {row.company_name}")
```
</details>

**Exercise 4 — Stretch (L2):** Write a Python function `find_paths(pg, start_id, end_id, max_hops=3)` that returns all paths (lists of node IDs) from `start_id` to `end_id` up to `max_hops` edges long. Test it between `"alice"` and `"usa"`.

<details>
<summary>Solution</summary>

```python
def find_paths(pg, start_id, end_id, max_hops=3):
    """BFS-based path finder with hop limit."""
    results = []
    queue = [(start_id, [start_id])]
    while queue:
        current_id, path = queue.pop(0)
        if len(path) - 1 >= max_hops:
            continue
        for edge in pg.edges:
            if edge.start.id == current_id:
                next_id = edge.end.id
                new_path = path + [next_id]
                if next_id == end_id:
                    results.append(new_path)
                elif next_id not in path:  # avoid cycles
                    queue.append((next_id, new_path))
    return results

paths = find_paths(pg, "alice", "usa", max_hops=3)
for p in paths:
    print(" → ".join(p))
# alice → toolagen → usa
# alice → toolagen → accel → usa  (via funded_by then hq_in)
```
</details>

---

## Connect it back

[Days 1–4](day-01-what-is-a-knowledge-graph.md) gave you the vocabulary: entities, triples, property graphs, ontologies. Today you asked your first questions and got answers. You can now feel the difference between "find rows that match these criteria" (SQL) and "find subgraphs that match this pattern" (Cypher/SPARQL). Tomorrow is Day 6 — Rest & Synthesize. You'll build a complete 10-node KG by hand, write 3 queries, and verify you can re-derive the 5 concepts from this arc without looking at the pages. After that, the course flips from reading to building.

**The question you can answer today that you couldn't this morning:** *Why does a 3-hop Cypher query not need any JOINs, and what is the performance risk of writing `[:KNOWS*]` with no upper bound?*

---

## Suggested readings for today

**Required if you have 15 extra minutes:** Robinson et al., *Graph Databases* (O'Reilly, 2015), Chapter 3, "Querying Graph Databases," pp. 35–70. Free at neo4j.com/graph-databases-book/ — the most comprehensive practical Cypher reference available. Focus on the MATCH pattern examples (§3.1–3.4).

**If you want the deep version:**
- Neo4j Graph Academy, "Cypher Fundamentals" course (free, ~2 hours): graphacademy.neo4j.com. Interactive lab environment — the fastest way to get Cypher fluency in your fingers before Day 8.
- Heath & Bizer, *Linked Data*, Chapter 4, "Querying Linked Data," pp. 83–110. Free at linkeddatabook.com. The definitive short intro to SPARQL, with a worked example over DBpedia. Read this if you'll be working with RDF stores (Apache Jena, Stardog, Oxigraph) rather than Neo4j.
- Neo4j documentation, "Query Tuning" section: neo4j.com/docs/cypher-manual/current/query-tuning/ — when your Day 8 queries are slow, this is where you look first. Skim it now so you know it exists.

---

← [Day 4 — Ontologies: The Grammar](day-04-ontologies-grammar-of-a-graph.md) &nbsp;|&nbsp; [Day 6 — Rest & Synthesize I →](day-06-rest-and-synthesize-i.md)
