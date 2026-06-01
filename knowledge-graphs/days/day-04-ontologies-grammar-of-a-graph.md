*[Knowledge Graphs: From Concept to Production](../README.md) · Day 4 of 15*

# Day 4 — Ontologies: The Grammar of a Graph

> **Today's one idea:** An ontology is the formal grammar of a knowledge graph — it defines what types of entities and relationships are allowed, and what constraints must hold, so that different graphs can be merged and queried consistently.
> **Reading time:** ~35 min · **Prereqs:** [Day 1](day-01-what-is-a-knowledge-graph.md), [Day 2](day-02-triples-atomic-unit.md), [Day 3](day-03-property-graphs-in-practice.md)
> **Primary source for today:** Hogan, Aidan et al. *Knowledge Graphs.* MIT Press, 2021. Chapter 2, §2.1–2.4 (pp. 19–68). Free preprint: arXiv:2003.02320.

---

## The hook (2–4 min)

Your company builds a KG of customers. Team A models the entity `Company` with properties `{name, revenue, sector}`. Team B models an entity called `Organization` with properties `{legal_name, industry, employee_count}`. Both teams are referring to the same real-world concept.

Now you need to merge the graphs to answer "which companies in the tech sector have more than 500 employees?" You can't. `Company` and `Organization` are different strings. `sector` and `industry` are different strings. `revenue` and `employee_count` don't even overlap.

This is not a data quality problem. It's a *vocabulary* problem. And it's the problem that ontologies were invented to solve.

An ontology is the shared contract that all teams in your organisation (and all the graphs in your system) agree to. It says: *"When we say `Company`, here is exactly what we mean. A `Company` must have a `name`. A `Company` may have an `industry`. A `Company` is a subtype of `LegalEntity`. `CompanyA` is equivalent to `OrganizationB` in Team B's vocabulary."* With that contract in place, the merge is automatic.

---

## Building the intuition (10–15 min)

### An ontology is a type system for facts

You already think in type systems. In Python:

```python
class Person:
    name: str
    age: int

class Employee(Person):     # subclass — inherits all of Person
    employer: Company
    start_date: date
```

An ontology does the same thing, but for graph entities:

- **Classes** = types of entities (`Person`, `Company`, `Product`)
- **Subclass relationships** = inheritance hierarchies (`Employee` is a `Person`)
- **Properties** = relationships or attributes entities can have (`worksFor`, `name`)
- **Domain constraints** = which class a property applies to (`worksFor` applies to `Person`)
- **Range constraints** = what type a property points to (`worksFor` points to `Company`)

The key difference from a Python class: an ontology is *declarative*. You're not writing methods; you're writing assertions that a reasoner can check.

### Two ontology languages: RDFS and OWL

**RDFS (RDF Schema)** — the minimal ontology language. Think of it as a thin type layer over RDF:

| RDFS term | What it says |
|-----------|-------------|
| `rdfs:Class` | This IRI is a class (a type) |
| `rdfs:subClassOf` | Class A is a subtype of class B |
| `rdfs:Property` | This IRI is a property |
| `rdfs:domain` | This property applies to entities of this type |
| `rdfs:range` | This property points to entities of this type |
| `rdfs:label` | A human-readable name for this term |
| `rdfs:comment` | A human-readable description |

**OWL (Web Ontology Language)** — RDFS plus expressive constraints:

| OWL term | What it says |
|----------|-------------|
| `owl:Class` | Richer class definition |
| `owl:equivalentClass` | Two classes refer to the same concept |
| `owl:disjointWith` | No entity can be both of these classes |
| `owl:ObjectProperty` | A property whose value is an entity (not a literal) |
| `owl:DatatypeProperty` | A property whose value is a literal |
| `owl:inverseof` | Property A is the inverse of property B |
| `owl:someValuesFrom` | An entity must have at least one value for this property from this class |

For most live project KGs, RDFS is enough. You reach for OWL when you need to enforce disjointness, equivalence, or cardinality — typically for cross-organisation data integration.

### Build a small ontology in Python + rdflib

```python
from rdflib import Graph, Namespace, Literal
from rdflib.namespace import RDF, RDFS, OWL, XSD

g = Graph()
EX = Namespace("http://example.org/org-ontology/")

# --- CLASSES ---
# Define entity types
g.add((EX.LegalEntity,  RDF.type,          OWL.Class))
g.add((EX.LegalEntity,  RDFS.label,        Literal("Legal Entity")))

g.add((EX.Company,      RDF.type,          OWL.Class))
g.add((EX.Company,      RDFS.subClassOf,   EX.LegalEntity))
g.add((EX.Company,      RDFS.label,        Literal("Company")))
g.add((EX.Company,      RDFS.comment,      Literal("A for-profit legal entity.")))

g.add((EX.Person,       RDF.type,          OWL.Class))
g.add((EX.Person,       RDFS.label,        Literal("Person")))

g.add((EX.Employee,     RDF.type,          OWL.Class))
g.add((EX.Employee,     RDFS.subClassOf,   EX.Person))
g.add((EX.Employee,     RDFS.label,        Literal("Employee")))

# Companies and Persons are disjoint: nothing can be both
g.add((EX.Company,      OWL.disjointWith,  EX.Person))

# --- PROPERTIES ---
# worksFor: Employee → Company
g.add((EX.worksFor,     RDF.type,          OWL.ObjectProperty))
g.add((EX.worksFor,     RDFS.domain,       EX.Employee))
g.add((EX.worksFor,     RDFS.range,        EX.Company))
g.add((EX.worksFor,     RDFS.label,        Literal("works for")))

# name: LegalEntity or Person → string literal
g.add((EX.name,         RDF.type,          OWL.DatatypeProperty))
g.add((EX.name,         RDFS.domain,       EX.LegalEntity))
g.add((EX.name,         RDFS.range,        XSD.string))

# foundedYear: Company → integer
g.add((EX.foundedYear,  RDF.type,          OWL.DatatypeProperty))
g.add((EX.foundedYear,  RDFS.domain,       EX.Company))
g.add((EX.foundedYear,  RDFS.range,        XSD.integer))

# employeeCount: Company → integer
g.add((EX.employeeCount, RDF.type,         OWL.DatatypeProperty))
g.add((EX.employeeCount, RDFS.domain,      EX.Company))
g.add((EX.employeeCount, RDFS.range,       XSD.integer))

# Serialise to Turtle — this is your reusable schema file
print(g.serialize(format="turtle"))
```

**Output (Turtle):**
```turtle
@prefix ex:   <http://example.org/org-ontology/> .
@prefix owl:  <http://www.w3.org/2002/07/owl#> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix rdf:  <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix xsd:  <http://www.w3.org/2001/XMLSchema#> .

ex:LegalEntity  a owl:Class ;
    rdfs:label "Legal Entity" .

ex:Company  a owl:Class ;
    rdfs:subClassOf ex:LegalEntity ;
    rdfs:label      "Company" ;
    rdfs:comment    "A for-profit legal entity." ;
    owl:disjointWith ex:Person .

ex:Person   a owl:Class ;
    rdfs:label "Person" .

ex:Employee  a owl:Class ;
    rdfs:subClassOf ex:Person ;
    rdfs:label      "Employee" .

ex:worksFor  a owl:ObjectProperty ;
    rdfs:domain ex:Employee ;
    rdfs:range  ex:Company ;
    rdfs:label  "works for" .

ex:name  a owl:DatatypeProperty ;
    rdfs:domain ex:LegalEntity ;
    rdfs:range  xsd:string .
```

This file is your schema. Save it as `ontology.ttl`. Every KG you build for this domain should load this schema alongside its data.

### Validate data against the ontology

```python
# Add instance data to the same graph
g.add((EX.Alice,        RDF.type,          EX.Employee))
g.add((EX.Alice,        EX.name,           Literal("Alice Chen")))
g.add((EX.Alice,        EX.worksFor,       EX.ToolaGen))
g.add((EX.ToolaGen,     RDF.type,          EX.Company))
g.add((EX.ToolaGen,     EX.name,           Literal("ToolaGen")))
g.add((EX.ToolaGen,     EX.foundedYear,    Literal(2023, datatype=XSD.integer)))
g.add((EX.ToolaGen,     EX.employeeCount,  Literal(12, datatype=XSD.integer)))

# Simple RDFS inference: Employee subClassOf Person → Alice is also a Person
def get_all_types(graph, entity, subclass_map):
    direct_types = set(graph.objects(entity, RDF.type))
    all_types = set(direct_types)
    for t in direct_types:
        all_types.update(subclass_map.get(t, set()))
    return all_types

# Build subclass closure
subclass_map = {}
for sub, sup in g.subject_objects(RDFS.subClassOf):
    subclass_map.setdefault(sub, set()).add(sup)

alice_types = get_all_types(g, EX.Alice, subclass_map)
print({str(t).split('/')[-1] for t in alice_types})
# → {'Employee', 'Person'}  ← inferred from subClassOf
```

This is the simplest form of reasoning: a subclass hierarchy. In production, you'd use a SPARQL-based reasoner or owlready2 for this.

---

## The formal picture (10–15 min)

### TBox and ABox

Ontology literature distinguishes two parts of a knowledge base:

**TBox (Terminological Box):** The schema — definitions of classes and properties. These are *general truths* that hold for all members of a type.
```
Company  rdfs:subClassOf  LegalEntity
worksFor  rdfs:domain  Employee
```

**ABox (Assertional Box):** The data — specific facts about instances. These are the actual triples in your KG.
```
Alice  rdf:type  Employee
Alice  worksFor  ToolaGen
```

An ontology is the TBox. A KG is the ABox. The two live in the same triple store and a reasoner can combine them: "Alice is an Employee (ABox). All Employees are Persons (TBox). Therefore Alice is a Person (inferred)."

### The RDFS Entailment Rules (simplified)

RDFS defines a small set of inference rules. The two most important:

**Subclass propagation:**
```
If:  X rdf:type C    AND   C rdfs:subClassOf D
Then: X rdf:type D
```
*"If Alice is an Employee, and Employees are Persons, then Alice is a Person."*

**Domain/range propagation:**
```
If:  X worksFor Y    AND   worksFor rdfs:domain Employee
Then: X rdf:type Employee

If:  X worksFor Y    AND   worksFor rdfs:range Company
Then: Y rdf:type Company
```
*"If Alice worksFor ToolaGen, and worksFor's domain is Employee, then Alice must be an Employee — even if you didn't say so explicitly."*

This last rule is powerful: your ontology can infer types from relationship usage alone. It's also dangerous: a typo in a `worksFor` edge can incorrectly classify an entity.

### OWL equivalence for merging

The Team A / Team B merge problem is solved with:

```turtle
# Team A's ontology
ex_a:Company  owl:equivalentClass  ex_b:Organization .

# Properties
ex_a:industry  owl:equivalentProperty  ex_b:sector .
```

Now a reasoner knows that `ex_a:Company` and `ex_b:Organization` are the same concept, and queries over one return results from both.

---

## Where it breaks / what it is not (3–5 min)

**An ontology is not data validation.** RDFS and OWL define what *should* be true, but most triple stores don't enforce constraints — they use the Open World Assumption. If you write `worksFor rdfs:domain Employee` and then add `(ToolaGen, worksFor, Microsoft)`, no error is thrown. The reasoner just infers that ToolaGen is an Employee — which is wrong. For closed-world validation (explicit errors for violations), use **SHACL** (Shapes Constraint Language) instead of RDFS/OWL.

**Ontology design is hard and slow.** A well-designed ontology for a production system takes weeks, not hours. The hardest decisions: what granularity to model at, whether to reuse existing vocabularies (schema.org, FOAF), and how to handle things that don't fit neatly into a hierarchy. Day 13 covers the design patterns that make this tractable.

**Don't over-engineer for live project KGs.** For most agent memory and GraphRAG use cases (Days 9–10), a minimal schema — a few node labels, a dozen relationship types — is all you need. Reserve OWL for integration scenarios. The property graph model's "schema-optional" approach is a feature, not a bug.

**Ontology ≠ taxonomy.** A taxonomy is just a hierarchy of terms (like a folder tree). An ontology includes properties, constraints, and inference rules. Wikipedia categories are a taxonomy; schema.org is closer to an ontology.

---

## Try it yourself (5–10 min)

**Exercise 1 — Schema design (L1):** Design a minimal ontology (TBox only, on paper) for a code repository KG. Include: at least 3 node types, 4 relationship types, 2 property constraints. Name your classes and properties in a way that prevents the Team A/Team B collision problem.

<details>
<summary>One solution</summary>

```
Classes: Repository, Commit, Developer, File
Properties:
  authored_by  domain: Commit,  range: Developer
  modifies     domain: Commit,  range: File
  belongs_to   domain: Commit,  range: Repository
  owns         domain: Developer, range: Repository

Subclass: Repository subClassOf codekg:Artifact
Domain/range enforce correctness: if you see authored_by, the subject must be a Commit
```
</details>

**Exercise 2 — Code it (L1/L2):** Implement the repository ontology from Exercise 1 in rdflib. Serialize it to Turtle. Add 3 instance triples (ABox data) and run the subclass propagation function from today's page to see what gets inferred.

**Exercise 3 — Stretch (L2):** SHACL preview. The proper way to validate a KG against a schema is SHACL. Install `pyshacl` (`pip install pyshacl`) and write a SHACL shape that enforces: "Every Commit must have exactly one `authored_by` property pointing to a `Developer`." Test it with a valid and an invalid graph.

<details>
<summary>Solution for Exercise 3</summary>

```python
# pip install pyshacl rdflib
from rdflib import Graph, Namespace, Literal
from rdflib.namespace import RDF, RDFS, SH, XSD
import pyshacl

EX = Namespace("http://example.org/coderepo/")

# SHACL shape (as an RDF graph)
shapes_g = Graph()
shapes_g.add((EX.CommitShape,    RDF.type,           SH.NodeShape))
shapes_g.add((EX.CommitShape,    SH.targetClass,     EX.Commit))
shapes_g.add((EX.CommitShape,    SH.property,        EX.AuthoredByConstraint))
shapes_g.add((EX.AuthoredByConstraint, SH.path,      EX.authored_by))
shapes_g.add((EX.AuthoredByConstraint, SH.minCount,  Literal(1, datatype=XSD.integer)))
shapes_g.add((EX.AuthoredByConstraint, SH.maxCount,  Literal(1, datatype=XSD.integer)))
shapes_g.add((EX.AuthoredByConstraint, SH.classConstraint, EX.Developer))

# Valid data graph
valid_g = Graph()
valid_g.add((EX.commit1,  RDF.type,       EX.Commit))
valid_g.add((EX.dev1,     RDF.type,       EX.Developer))
valid_g.add((EX.commit1,  EX.authored_by, EX.dev1))

conforms, results_g, results_text = pyshacl.validate(
    valid_g, shacl_graph=shapes_g, inference='rdfs')
print(f"Valid graph conforms: {conforms}")  # True

# Invalid: commit with no author
invalid_g = Graph()
invalid_g.add((EX.commit2, RDF.type, EX.Commit))  # no authored_by

conforms2, _, results_text2 = pyshacl.validate(
    invalid_g, shacl_graph=shapes_g, inference='rdfs')
print(f"Invalid graph conforms: {conforms2}")  # False
print(results_text2)
```
</details>

---

## Connect it back

[Yesterday](day-03-property-graphs-in-practice.md) you built a property graph where nodes had labels like `:Person` and `:Researcher`. Those labels are the beginning of an ontology — they imply that Persons and Researchers are distinct types. Today you made that implicit contract explicit: defining what types exist, what relationships they can have, and what constraints must hold. Tomorrow you'll write queries that *exploit* this schema — asking for all `:Person` nodes connected by `WORKS_FOR` edges to `:Company` nodes — and you'll see why a well-defined ontology makes queries both simpler and more reliable.

**The question you can answer today that you couldn't this morning:** *Why can two different teams independently build KGs about "companies" and still be unable to merge them, even if both use RDF?*

---

## Suggested readings for today

**Required if you have 15 extra minutes:** Hogan et al., *Knowledge Graphs* (MIT Press, 2021), §2.1–2.3, pp. 19–55. Covers the complete RDFS and OWL vocabularies with worked examples. The preprint version is at arXiv:2003.02320 — search for "2 Graph Data Models."

**If you want the deep version:**
- Heath & Bizer, *Linked Data*, Chapter 3, "Vocabularies," pp. 55–82. Free at linkeddatabook.com. Best explanation of why you should reuse existing vocabularies (schema.org, FOAF, Dublin Core) rather than reinventing them — key advice for integration use cases.
- Vrandecic, Denny, and Markus Krötzsch. "Wikidata: A Free Collaborative Knowledgebase." *CACM*, 2014. DOI:10.1145/2629489. §3 "Data Model" shows how Wikidata's ontology was designed to be multilingual and consensus-driven at 100M+ entity scale — the production design patterns Day 13 will build on.
- pyshacl documentation: github.com/RDFLib/pySHACL — if you want to do closed-world validation in your pipeline (recommended for production KGs), the README is the fastest path to working code.

---

← [Day 3 — Property Graphs in Practice](day-03-property-graphs-in-practice.md) &nbsp;|&nbsp; [Day 5 — Querying: Cypher & SPARQL →](day-05-querying-cypher-sparql.md)
