# Glossary

Alphabetical. Each entry: plain-English definition · formal definition · first introduced.

*Updated through Day 15.*

---

**Agent Memory**
Plain: The persistent store an AI agent uses to retain facts across conversation turns and sessions. A KG is used as semantic/episodic memory; the context window is working memory.
Formal: A read-write storage system accessed in the agent loop: retrieve relevant facts before each turn, extract and store new facts after each turn.
Introduced: Day 10.

**Community (graph)**
Plain: A cluster of nodes in a graph that are densely connected to each other and sparsely connected to the rest of the graph — intuitively, a "neighbourhood" or "topic cluster."
Formal: A partition of vertices `V` into groups `C₁, C₂, …, Cₖ` that maximises intra-community edges and minimises inter-community edges, typically measured by modularity `Q`.
Introduced: Day 9.

**Community Detection**
Plain: The algorithmic task of finding communities in a graph automatically — without being told how many there are or what they mean.
Formal: An unsupervised partitioning of graph vertices to maximise modularity `Q = Σ_c [L_c/m - (d_c/2m)²]` where `L_c` = edges within community `c`, `m` = total edges, `d_c` = sum of degrees in `c`.
Introduced: Day 9.

**Entity Resolution**
Plain: The task of deciding that two differently-named references in a text (or two different datasets) refer to the same real-world entity. "Alice Chen", "A. Chen", "Dr. Chen" → same person.
Formal: A function `f: Mentions → Entities` that maps raw mention strings to canonical entity identifiers using string similarity, embedding distance, or contextual cues.
Introduced: Day 7.

**Episodic Memory**
Plain: Memory of *what happened* — timestamped events and interactions, as opposed to general world knowledge (semantic memory). In a KG, episodic memory is stored as Event nodes with timestamps.
Formal: A time-indexed sequence of experience records `{(t₁, e₁), (t₂, e₂), …}` where `eᵢ` describes a discrete agent experience at time `tᵢ`.
Introduced: Day 10.

**GraphRAG**
Plain: A retrieval-augmented generation approach that builds a KG from a corpus, detects communities, summarises them, and uses those summaries (for global questions) or entity subgraphs (for local questions) as retrieval context for an LLM.
Formal: A two-mode RAG system: global mode retrieves community reports; local mode retrieves the k-hop subgraph around entities matched by the query.
Introduced: Day 9.

**Hits@K**
Plain: An evaluation metric for link prediction: the fraction of test triples where the true answer appears in the top K predicted entities. Hits@10 = 0.8 means the correct answer is in the top 10 predictions 80% of the time.
Formal: `Hits@K = |{(h,r,t) ∈ T_test : rank(t | h,r) ≤ K}| / |T_test|` where `rank(t | h,r)` is the rank of the true tail among all entities scored by the model.
Introduced: Day 11.

**Information Extraction (IE)**
Plain: The NLP task of pulling structured facts (entities, relationships) from unstructured text — converting prose into triples.
Formal: A pipeline of Named Entity Recognition (NER) + Relation Extraction (RE) that maps text `T` to a set of structured records `{(s, p, o)}`.
Introduced: Day 7.

**Ingestion Pipeline**
Plain: The three-stage process for loading facts into a KG: Loader (read raw data) → Transformer (extract and normalise triples) → Writer (load into graph store with MERGE semantics).
Formal: A data pipeline `P = (L, T, W)` where `L` reads source documents, `T` produces normalised triples, and `W` writes idempotently to a graph store.
Introduced: Day 8.

**KG Embedding**
Plain: A representation learning approach that assigns real-valued vectors to entities and relations in a KG, such that the geometry of the vector space captures the graph's structural patterns. Used for link prediction and entity similarity.
Formal: A function `f: (V ∪ R) → ℝᵈ` where `d` is the embedding dimension, trained to maximise a scoring function `φ(h, r, t)` for true triples and minimise it for corrupted triples.
Introduced: Day 11.

**Link Prediction**
Plain: The task of predicting which edges are missing from a knowledge graph — given `(Alice, knows, ?)`, predict the most likely tail entity.
Formal: Given a KG `G = (V, E, L)`, rank all candidate triples `(h, r, t) ∉ E` by their probability of being true, estimated by a scoring function `φ(h, r, t)`.
Introduced: Day 11.

**Louvain Algorithm**
Plain: A fast community detection algorithm that greedily optimises modularity by moving nodes between communities. The standard choice for graphs up to ~10M edges.
Formal: A two-phase iterative algorithm: (1) move each node to the community of its neighbour that maximises modularity gain; (2) aggregate communities into super-nodes. Repeat until stable.
Introduced: Day 9.

**MERGE (Cypher)**
Plain: A Cypher command that creates a node or edge if it doesn't exist, or matches it if it does. The correct command for idempotent KG ingestion — running MERGE twice on the same data produces the same graph as running it once.
Formal: `MERGE pattern` semantics: if a subgraph matching `pattern` exists, bind to it; otherwise create it. Equivalent to `MATCH OR CREATE` as an atomic operation.
Introduced: Day 8.

**Named Entity Recognition (NER)**
Plain: An NLP task that identifies and classifies named entity spans in text — finding "Alice Chen" and labelling it PERSON, "Neo4j" as SOFTWARE, etc.
Formal: A sequence labelling task `f: tokens → (span, label)*` that assigns entity type labels to contiguous token spans in input text.
Introduced: Day 7.

**Negative Sampling**
Plain: In KG embedding training, the technique of generating fake (corrupted) triples by randomly replacing the head or tail with another entity — used as negative training examples to teach the model what is NOT a valid fact.
Formal: For a true triple `(h, r, t)`, a negative sample is `(h', r, t)` or `(h, r, t')` where `h'` or `t'` is sampled uniformly from `V \ {h}` or `V \ {t}` respectively.
Introduced: Day 11.

**Relation Extraction (RE)**
Plain: An NLP task that, given two entities in a text, identifies and labels the relationship between them. Input: "Alice co-authored a paper with Bob." Output: `(Alice, co_authored_with, Bob)`.
Formal: A classification function `f: (e₁, e₂, context) → r ∈ R ∪ {None}` that maps an entity pair and their textual context to a relation type.
Introduced: Day 7.

**RotatE**
Plain: A KG embedding model that represents relations as rotations in complex vector space rather than translations in real space. Handles symmetric, antisymmetric, inverse, and compositional relation patterns that TransE cannot.
Formal: Scoring function `φ(h, r, t) = -||h ∘ r - t||` where `∘` is element-wise product in `ℂᵈ` and `r` has unit modulus (`|rᵢ| = 1`), so it acts as a rotation.
Introduced: Day 11.

**Semantic Memory**
Plain: Memory of *facts about the world* — entities, their properties, and their relationships — as distinct from episodic memory (events). A KG's triple store is semantic memory.
Formal: A long-term store of propositions `{(h, r, t)}` representing beliefs about entities and their relations, updated as new facts are learned and old ones are revised.
Introduced: Day 10.

**TransE**
Plain: The foundational KG embedding model (Bordes et al., NeurIPS 2013). Models a relation `r` as a translation vector: for a true triple `(h, r, t)`, the model learns that `vec(h) + vec(r) ≈ vec(t)`.
Formal: Scoring function `φ(h, r, t) = -||h + r - t||_p` for `p = 1` or `2`. Training minimises margin-ranking loss: push true triple scores above corrupted triple scores by margin `γ`.
Introduced: Day 11.

**Blank Node**
Plain: An anonymous entity in an RDF graph — a node with no IRI, used when you need a resource but don't have (or don't want to assign) a URI for it.
Formal: An element of the set `B` in the RDF triple model `(S ∪ B, P, S ∪ B ∪ L)` where `S` = IRIs, `B` = blank nodes, `L` = literals.
Introduced: Day 2.

**Cypher**
Plain: Neo4j's declarative graph query language. Queries look like ASCII art patterns — you draw the graph shape you want, and Cypher finds it.
Formal: A pattern-matching language over Labeled Property Graphs where `MATCH (n)-[r]->(m)` binds node and edge variables to subgraph patterns.
Introduced: Day 5.

**Domain (ontology)**
Plain: The type of entity that a property can describe. The `domain` of `founded` is `Person` — only Persons can found things.
Formal: `rdfs:domain P C` asserts that anything that appears as the subject of property `P` is of type `C`.
Introduced: Day 4.

**Edge**
Plain: A connection between two nodes in a graph. In a KG, every edge has a name (label) that describes the type of relationship.
Formal: An element `e ∈ E` in a directed graph `G = (V, E)`, where `E ⊆ V × V`.
Introduced: Day 1.

**Entity**
Plain: A thing in the world that the KG has a node for — a person, company, concept, place, event.
Formal: An element `v ∈ V` in the vertex set of a KG, identified by a unique IRI or internal ID.
Introduced: Day 1.

**Graph**
Plain: A structure of nodes connected by edges. In a KG, both nodes and edges carry labels that give them meaning.
Formal: `G = (V, E)` where `V` is a set of vertices and `E ⊆ V × V` is a set of directed edges.
Introduced: Day 1.

**IRI (Internationalized Resource Identifier)**
Plain: A globally unique name for a thing on the web — like a URL but for any resource, not just web pages. IRIs are how RDF ensures two datasets can be merged without name collisions.
Formal: A generalisation of URI to include Unicode characters; used in RDF as the identifier type for subjects, predicates, and non-literal objects.
Introduced: Day 2.

**Knowledge Graph (KG)**
Plain: A directed, labeled multigraph that stores structured facts about a domain — where the labels on edges encode the *type* of relationship, not just the fact that a connection exists.
Formal: `KG = (V, E, L, φ)` where `V` = entities, `E ⊆ V × V` = directed edges, `L` = relation labels, `φ: E → L` = labeling function. Equivalently represented as a set of triples `{(h, r, t) | h, t ∈ V, r ∈ L}`.
Introduced: Day 1.

**Labeled Property Graph (LPG)**
Plain: Neo4j's data model. Nodes have labels (type tags) and key-value properties. Edges have a type and also key-value properties. Both nodes and edges are first-class objects.
Formal: `LPG = (V, E, ρ, λ_V, λ_E, σ_V, σ_E)` where `ρ: E → V × V` maps edges to (start, end) pairs, `λ` functions assign labels, and `σ` functions assign property maps.
Introduced: Day 3.

**Literal**
Plain: A raw value in RDF — a string, number, date, or boolean. Literals are always the *object* of a triple; they cannot be subjects or predicates.
Formal: An element of set `L` in the RDF triple model; a literal has a lexical form and a datatype IRI (e.g., `"1879"^^xsd:integer`).
Introduced: Day 2.

**Multigraph**
Plain: A graph that allows multiple distinct edges between the same pair of nodes. Needed because two entities can have more than one type of relationship (e.g., Alice both *manages* Bob and *mentors* Bob).
Formal: A graph `G = (V, E, φ)` where `φ: E → V × V` is not required to be injective — multiple edges can map to the same (source, target) pair.
Introduced: Day 1.

**Namespace**
Plain: A prefix that scopes identifiers to avoid collisions between datasets. `ex:Person` and `foaf:Person` are different things even though both end in `Person`.
Formal: A base IRI used to construct full IRIs via concatenation or prefix expansion: `PREFIX ex: <http://example.org/>` means `ex:Person` expands to `<http://example.org/Person>`.
Introduced: Day 2.

**Node**
Plain: A point in a graph representing an entity. In a property graph, a node can hold key-value data directly.
Formal: An element `v ∈ V` in a graph `G = (V, E)`.
Introduced: Day 1.

**Object**
Plain: The third element of a triple — the "value" being asserted. It can be another entity (IRI) or a raw value (literal).
Formal: The third component `o` of an RDF triple `(s, p, o)` where `o ∈ IRI ∪ BlankNode ∪ Literal`.
Introduced: Day 2.

**OWL (Web Ontology Language)**
Plain: A richer schema language than RDFS. Lets you express disjointness ("a Person cannot also be an Organisation"), equivalence ("ex:Company owl:equivalentClass schema:Corporation"), and cardinality constraints.
Formal: An ontology language based on Description Logics, standardised by W3C. OWL 2 DL is decidable; OWL Full is not.
Introduced: Day 4.

**Ontology**
Plain: The "grammar book" of a knowledge graph — a formal specification of what types of entities and relationships are allowed, and what constraints must hold.
Formal: A set of axioms `O = (T, A)` where `T` (TBox) contains type-level constraints and `A` (ABox) contains instance-level assertions.
Introduced: Day 4.

**Open World Assumption (OWA)**
Plain: If a fact is not in the KG, it doesn't mean the fact is false — it means you don't know. This is the default assumption in RDF/OWL systems and is the opposite of the Closed World Assumption in relational databases.
Formal: Under OWA, `¬(KG ⊨ φ)` does not imply `KG ⊨ ¬φ`.
Introduced: Day 1.

**Predicate**
Plain: The relationship type in a triple — what connects the subject to the object. Also called a "property" or "relation."
Formal: The second component `p` of an RDF triple `(s, p, o)` where `p ∈ IRI`.
Introduced: Day 2.

**Property**
Plain: A key-value pair attached to a node or edge in a property graph (e.g., `{name: "Alice", age: 30}` on a Person node).
Formal: An element of the property function `σ: (V ∪ E) → (K ↦ V_domain)` in the LPG model, where `K` is the set of property keys.
Introduced: Day 3.

**Range (ontology)**
Plain: The type of entity that a property's *value* must be. The `range` of `bornIn` is `Place` — you can only be born in a Place, not a Person.
Formal: `rdfs:range P C` asserts that anything that appears as the object of property `P` is of type `C`.
Introduced: Day 4.

**RDF (Resource Description Framework)**
Plain: The W3C standard data model for knowledge graphs on the web. Everything is expressed as triples of IRIs and literals. RDF graphs are sets of triples.
Formal: An RDF graph is a set of triples `(s, p, o)` where `s ∈ IRI ∪ BlankNode`, `p ∈ IRI`, `o ∈ IRI ∪ BlankNode ∪ Literal`.
Introduced: Day 2.

**RDFS (RDF Schema)**
Plain: A minimal ontology language layered on top of RDF. Lets you define classes, subclasses, properties, and domain/range constraints.
Formal: An RDF vocabulary that adds semantics to RDF graphs via inference rules, standardised at W3C. Key terms: `rdfs:Class`, `rdfs:subClassOf`, `rdfs:domain`, `rdfs:range`.
Introduced: Day 4.

**Relation**
Plain: The label on an edge in a KG — the named type of connection between two entities (e.g., `founded`, `located_in`, `works_for`).
Formal: An element `r ∈ R` where `R` is the set of relation types in a KG; each triple `(h, r, t)` asserts that entities `h` and `t` stand in relation `r`.
Introduced: Day 1.

**Reification**
Plain: Saying something *about* a statement. "Alice said that Bob founded Tesla" — the claim about Bob is wrapped in a new statement. RDF handles this awkwardly with reification triples; property graphs handle it naturally with edge properties.
Formal: In RDF, reification represents a triple `(s, p, o)` as a resource with four new triples using `rdf:Statement`, `rdf:subject`, `rdf:predicate`, `rdf:object`.
Introduced: Day 2.

**SPARQL**
Plain: The W3C standard query language for RDF knowledge graphs. Queries are expressed as sets of triple patterns with variables, and the engine finds all bindings that satisfy them.
Formal: A query language for RDF graphs where `SELECT ?x WHERE { ?x rdf:type ex:Person }` returns all entities of type `ex:Person`.
Introduced: Day 5.

**Subject**
Plain: The first element of a triple — the entity the fact is *about*.
Formal: The first component `s` of an RDF triple `(s, p, o)` where `s ∈ IRI ∪ BlankNode`.
Introduced: Day 2.

**Triple**
Plain: The atomic unit of knowledge in a KG: `(subject, predicate, object)` — or equivalently `(head, relation, tail)`. Every fact in the graph is stored as one or more triples.
Formal: An ordered 3-tuple `(s, p, o)` where the components are drawn from defined sets depending on the data model (RDF or LPG).
Introduced: Day 2.

**Triple Store**
Plain: A database that stores and queries RDF triples. The graph database equivalent of a relational database, but optimised for triple patterns instead of JOINs.
Formal: A storage and retrieval system for RDF graphs, supporting at minimum SPARQL SELECT queries over triple patterns.
Introduced: Day 2.

**Turtle**
Plain: A human-readable syntax for writing RDF triples. Allows prefix declarations so you don't have to write full IRIs every time.
Formal: A W3C-standardised serialisation format for RDF graphs (file extension `.ttl`). Supports prefixes, base IRIs, and shorthand for common patterns.
Introduced: Day 4.

---

**Blast Radius**
Plain: The set of services or entities that would be affected if a given entity fails or changes — computed by traversing the dependency graph upward from the failing node.
Formal: The set of nodes reachable from a given node `v` via reverse traversal of dependency edges, within a bounded hop depth.
Introduced: Day 15.

**Contradiction (KG)**
Plain: The presence of two conflicting facts about the same entity — e.g., an entity has two `WORKS_AT` edges pointing to different organisations.
Formal: An inconsistency where `(s, p, o₁)` and `(s, p, o₂)` both exist in the graph and `o₁ ≠ o₂`, violating a functional property constraint.
Introduced: Day 14.

**Data Quality (KG)**
Plain: The degree to which facts in the KG are correct, complete, consistent, and timely. Measured via confidence distributions, contradiction counts, and staleness rates.
Formal: A multi-dimensional measure including completeness (fraction of true facts present), consistency (absence of contradictions), accuracy (fraction of present facts that are true), and timeliness (recency of last confirmation).
Introduced: Day 14.

**Entity-Centric Pattern**
Plain: A schema design approach where nodes represent "things" (people, organisations, products) and edges represent stable structural relationships between them.
Formal: A graph schema in which `V` contains entity instances and `E` encodes binary relations; temporal or interaction data is encoded as edge properties rather than intermediate nodes.
Introduced: Day 13.

**Event-Centric Pattern**
Plain: A schema design approach where actions, interactions, and occurrences are promoted to first-class nodes (with timestamps and participants), rather than being encoded as edges.
Formal: A graph schema where interaction events `e ∈ V` are connected to their participants via named edges; enables temporal queries and multiple interactions between the same entity pair.
Introduced: Day 13.

**Expiry / Staleness**
Plain: A fact is stale when its `last_seen` timestamp is old enough that it may no longer be true. Production KGs periodically delete or down-weight stale edges.
Formal: An edge `r` with `r.last_seen < now() - duration(T)` for some staleness threshold `T`; a common hygiene step in append-based KG pipelines.
Introduced: Day 14.

**Index (Neo4j)**
Plain: A B-tree data structure that lets Neo4j find nodes or edges by a property value in O(log N) time instead of scanning all nodes.
Formal: A Neo4j schema index created with `CREATE INDEX … FOR (n:Label) ON (n.property)`, enabling range and equality lookups without full graph scans.
Introduced: Day 14.

**KG Health Metrics**
Plain: A dashboard of statistics — node count, edge count, average confidence, low-confidence edge fraction, staleness rate, contradiction count — that tells you if your KG is drifting from a reliable state.
Formal: A set of KG quality indicators computed by running aggregate Cypher queries; used to trigger alerts when thresholds are breached.
Introduced: Day 14.

**Multi-Hop Query**
Plain: A query that traverses two or more edges to reach an answer — e.g., "find engineers on-call for services that share components with a failing service." Flat vector RAG cannot reliably answer these.
Formal: A Cypher (or SPARQL) query with path length ≥ 2 between the start node and the answer nodes; requires traversal of intermediate nodes that are not referenced in the question text.
Introduced: Day 15.

**Observability (KG)**
Plain: The ability to detect when a KG has problems — low confidence, contradictions, staleness — without waiting for users to report wrong answers.
Formal: A monitoring system that collects KG health metrics on a schedule and emits alerts when metrics breach defined thresholds.
Introduced: Day 14.

**Provenance-Aware Pattern**
Plain: A schema design approach where every extracted edge stores metadata about its origin (source document, extractor model, confidence score, human verification status).
Formal: A graph schema in which all `r ∈ E` carry at minimum `{source, confidence, extracted_at, human_verified}` properties, enabling trust-filtered queries.
Introduced: Day 13.

**Reconciler**
Plain: A component that detects and resolves contradictions in the KG — finding cases where the same entity has conflicting facts and keeping the higher-confidence version.
Formal: A process that identifies functional-property violations in the graph and removes or flags lower-confidence edges, restoring consistency.
Introduced: Day 14.

**Schema Evolution**
Plain: The process of adding new node labels, relationship types, or properties to a live KG without breaking existing queries or requiring a full data migration.
Formal: A set of additive changes to the graph schema — adding indexes, re-labelling existing nodes, extending property sets — that maintain backward compatibility with existing Cypher queries.
Introduced: Day 14.

**Triple Validator**
Plain: A filter that rejects low-quality triples before they enter the KG — enforcing minimum name lengths, confidence thresholds, and predicate blocklists.
Formal: A gate in the ingestion pipeline that applies validation rules to each `(s, p, o, conf)` tuple and returns valid/rejected partitions; reduces graph noise from LLM extraction errors.
Introduced: Day 14.
