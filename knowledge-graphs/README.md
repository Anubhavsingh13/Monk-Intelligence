# Knowledge Graphs: From Concept to Production

> **The promise:** By the end of this course, you can design, build, and serve a domain-specific knowledge graph that a real LLM agent queries as structured memory.

## Who this course is for

This course targets practitioners and builders (L1/L2) with a background in agentic AI and machine learning. You write Python daily, you've worked with LLMs and RAG pipelines, and you want to add knowledge graphs to your production toolkit — not just understand them abstractly, but build them from scratch and wire them into real agents.

You should *not* take this course if you want a deep theoretical treatment of description logics, a formal study of RDF semantics, or a research-level survey of KG embedding methods. This course optimises for one outcome: a working knowledge graph pipeline that an AI agent can depend on.

## The arc at a glance

On Day 1, you can articulate what a knowledge graph *is* and why it differs from a vector store or a relational table — but you cannot build one. By Day 6 you can query a small graph you've constructed by hand and begin to feel the difference between pattern-matching over structure and keyword search over text. The first half of the course is about earning the right vocabulary: triples, ontologies, property graphs, Cypher. These aren't academic formalities — each one is a tool decision you will make in a real project.

The second half flips from concept to construction. You build an ingestion pipeline that reads your own documents, extracts entities and relationships with an LLM, and loads them into Neo4j. You then wire that graph into a RAG pipeline and an agent memory system — exactly the two patterns that make KGs immediately valuable in production AI work. The course ends with you producing a working, domain-specific knowledge graph that a real agent can query. The single story tying the course together: *a knowledge graph is the difference between an agent that retrieves text and one that reasons over facts.*

## How to use this course

- **Rhythm:** one page per day, 30–45 minutes of focused reading. Don't skip exercises — for L2, they are where the learning actually happens.
- **Exercises:** Every page has 3–4 exercises. If you're targeting L1, do at least the first two. If L2, do all of them including the stretch. The capstone on Day 15 requires the stretch exercises from Days 8–11.
- **Bibliography:** When a page says "required if you have 15 extra minutes," do it. When it says "deep version," bookmark it for after the course. Don't derail your momentum by rabbit-holing into sources mid-course.
- **Rest & Synthesize days (6 and 12):** These are not days off. Re-derive the ideas without looking at the pages. Build the mini-project. If you can't, that's information — go back one day before proceeding.
- **If life intervenes:** Missing a day is fine. Don't skip rest days to catch up. If you miss more than 3 days in a row, re-read the most recent rest-day page before resuming.

## The learning path

| Day | Title | One idea | Core source |
|-----|-------|----------|-------------|
| [1](days/day-01-what-is-a-knowledge-graph.md) | What Is a Knowledge Graph? | A KG is a directed, labeled multigraph where edges carry named semantics | Hogan et al., Ch. 1 |
| [2](days/day-02-triples-atomic-unit.md) | Triples: The Atomic Unit | Every fact is a `(subject, predicate, object)` triple | Heath & Bizer, Ch. 2 |
| [3](days/day-03-property-graphs-in-practice.md) | Property Graphs in Practice | A property graph attaches key-value metadata to nodes and edges | Robinson et al., Ch. 2 |
| [4](days/day-04-ontologies-grammar-of-a-graph.md) | Ontologies: The Grammar | An ontology defines allowed types and constraints | Hogan et al., Ch. 2 |
| [5](days/day-05-querying-cypher-sparql.md) | Querying Your Graph | Cypher and SPARQL are declarative pattern-matching languages | Robinson et al., Ch. 3 |
| [6](days/day-06-rest-and-synthesize-i.md) | Rest & Synthesize I | Build a 10-node KG by hand and query it — no new material | — |
| [7](days/day-07-entity-relation-extraction.md) | Entity & Relation Extraction | NLP pipelines convert unstructured text into triples | Hogan et al., Ch. 5 |
| [8](days/day-08-ingestion-pipeline.md) | The Ingestion Pipeline | A KG lives inside a loader → transformer → writer pipeline | — |
| [9](days/day-09-graphrag.md) | GraphRAG | KG-backed retrieval answers multi-hop questions flat vector search cannot | Edge et al., 2024 |
| [10](days/day-10-kgs-as-agent-memory.md) | KGs as Agent Memory | A KG is the ideal persistent, queryable, updatable agent memory substrate | — |
| [11](days/day-11-kg-embeddings.md) | Knowledge Graph Embeddings | Mapping entities and relations to vectors enables ML reasoning | Bordes et al., 2013 |
| [12](days/day-12-rest-and-synthesize-ii.md) | Rest & Synthesize II | Re-derive the end-to-end pipeline: docs → triples → graph → agent | — |
| [13](days/day-13-schema-design-patterns.md) | Schema Design Patterns | Entity-centric vs. event-centric patterns determine KG maintainability | Robinson et al., Ch. 4 |
| [14](days/day-14-production-concerns.md) | Production Concerns | Incremental updates, data quality, and schema evolution | Hogan et al., Ch. 8 |
| [15](days/day-15-capstone.md) | Capstone | Design, build, and serve a domain KG for an AI agent — end-to-end | All days |

## The Course Shelf

1. **Hogan et al., *Knowledge Graphs* (MIT Press, 2021)** — The definitive textbook; spine of Days 1–5, 11–14.
2. **Robinson, Webber & Eifrem, *Graph Databases* (O'Reilly, 2015)** — Practical Neo4j guide; spine of Days 3, 5, 8, 13–14.
3. **Heath & Bizer, *Linked Data* (Morgan & Claypool, 2011)** — Foundation for the RDF/triple mental model; Days 2, 4–5.
4. **Edge et al., "From Local to Global: A Graph RAG Approach" (arXiv:2404.16130, 2024)** — Primary source for Day 9.
5. **Bordes et al., "Translating Embeddings…" (NeurIPS, 2013)** — TransE; primary source for Day 11.

Full annotated shelf in [bibliography.md](bibliography.md).

## The capstone

On Day 15 you will choose a domain from your own work (a codebase, a research area, a product domain), extract 200+ entities and relationships from real documents using an LLM pipeline, load them into Neo4j, write 5 Cypher queries that answer questions a human expert would ask, and expose the graph as a tool available to a LangChain or LlamaIndex agent. "Done" means: the agent correctly answers a multi-hop question that would fail with plain vector RAG.

## Glossary

[glossary.md](glossary.md)

## Meta

- **Depth level:** L1 (Practitioner) + L2 (Builder)
- **Estimated total hours:** ~9 hours reading + ~6 hours exercises = 15 hours
- **Last updated:** 2026-05-10
- **Feedback:** Open an issue in the course repository or flag corrections inline.
