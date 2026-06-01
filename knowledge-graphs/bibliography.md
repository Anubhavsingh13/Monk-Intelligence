# Bibliography — Annotated Course Shelf

All sources cited in this course. Every entry includes a precise location (chapter / section / timestamp) for the content most relevant to the days that cite it.

---

## Foundational Books

### 1. Hogan, Aidan, Eva Blomqvist, Marc Groth, et al. *Knowledge Graphs.* MIT Press, 2021. ISBN 978-0-262-54238-8.

Also available as an arXiv preprint: arXiv:2003.02320 (freely accessible; identical to the book in content through the 2021 edition).

**What it covers:** The single most comprehensive modern treatment of knowledge graphs. Covers the full arc from graph data models (RDF, property graphs) through schema languages (RDFS, OWL, SHACL), knowledge graph construction (NLP extraction, entity linking), embeddings, and graph neural networks. Written by an international team of researchers who have built and studied production KGs.

**Whose intuition it favors:** Semantic web / linked data tradition, but Chapter 11 (embeddings) is squarely in the ML camp. Formal but readable; each chapter is largely self-contained.

**Where it's hardest:** Chapter 5 (Ontology-Mediated Query Answering) and Chapter 9 (Symbolic Reasoning) go deep into description logics — skip these for L1/L2 unless you need them.

**Days that rely on it most:** Days 1 (Ch. 1), 2 (Ch. 1–2), 4 (Ch. 2), 7 (Ch. 5 §5.1–5.3), 11 (Ch. 11), 13 (Ch. 3), 14 (Ch. 8).

---

### 2. Robinson, Ian, Jim Webber, and Emil Eifrem. *Graph Databases: New Opportunities for Connected Data.* O'Reilly Media, 2nd ed., 2015. ISBN 978-1-491-93200-1.

Free online edition at: neo4j.com/graph-databases-book/

**What it covers:** The definitive guide to the property graph model and Neo4j. Chapter 2 introduces the Labeled Property Graph (LPG) model. Chapter 3 covers Cypher in depth. Chapter 4 has excellent schema design patterns (entity-centric, hyper-relational, event-centric). Chapter 7 covers deployment and scaling.

**Whose intuition it favors:** Engineering and product-focused. Every concept is grounded in working Cypher and real use cases. Lighter on theory than Hogan et al.

**Where it's hardest:** Chapter 6 (Graph Model vs. Relational Model) is useful but can feel repetitive if you already understand the difference by Day 5.

**Days that rely on it most:** Days 3 (Ch. 2), 5 (Ch. 3), 8 (Ch. 3–4), 13 (Ch. 4), 14 (Ch. 7).

---

### 3. Heath, Tom, and Christian Bizer. *Linked Data: Evolving the Web into a Global Data Space.* Morgan & Claypool, 2011. DOI: 10.2200/S00334ED1V01Y201102WBE001.

Freely available as PDF: linkeddatabook.com/editions/1.0/

**What it covers:** A short (~160 pp) but foundational text on the Linked Data principles and the RDF data model. Chapter 2 explains IRIs and the triple model with clarity. Chapter 3 covers vocabularies and ontologies at an approachable level. Chapter 4 introduces SPARQL.

**Whose intuition it favors:** Web/semantic web tradition. Great for understanding *why* RDF was designed the way it was — the Web as a universal graph.

**Where it's hardest:** Chapter 5 (Linked Data Applications) is showing its age; the tooling examples are outdated. Read for principles, not for code.

**Days that rely on it most:** Days 2 (Ch. 2), 4 (Ch. 3), 5 (Ch. 4).

---

## Landmark Papers

### 4. Bordes, Antoine, Nicolas Usunier, Alberto Garcia-Duran, Jason Weston, and Oksana Yakhnenko. "Translating Embeddings for Modeling Multi-relational Data." *Advances in Neural Information Processing Systems (NeurIPS)*, 2013.

Search: "TransE NeurIPS 2013" or "Translating Embeddings Bordes 2013"

**What it covers:** Introduces TransE, the foundational geometric intuition for KG embeddings: a relation `r` between head `h` and tail `t` is modelled as a translation `h + r ≈ t` in a vector space. Simple, fast, and still the baseline everything else is measured against.

**Why it matters for this course:** Day 11's intuition section is built around this paper's geometric picture. Reading the first 4 pages (through §3) is enough to get the full insight.

**Limitation to be aware of:** TransE cannot model symmetric relations (if `h + r ≈ t`, it cannot also have `t + r ≈ h` unless `r = 0`). Day 11 covers this and introduces RotatE as the fix.

---

### 5. Sun, Zhiqing, Zhi-Hong Deng, Jian-Yun Nie, and Jian Tang. "RotatE: Knowledge Graph Embedding by Relational Rotation in Complex Space." *International Conference on Learning Representations (ICLR)*, 2019. arXiv:1902.10197.

**What it covers:** Extends TransE by modelling relations as rotations in the complex plane rather than translations in real space. This allows RotatE to represent symmetric, antisymmetric, inverse, and composition relations — all the patterns TransE cannot handle.

**Why it matters for this course:** Paired with TransE on Day 11 to show what an embedding model needs to express when the world has symmetric relationships (e.g., `is_sibling_of`, `co-author_with`).

**Key section:** §3 (Model) and §4 (Relation Patterns) are the essential read — about 3 pages.

---

### 6. Vrandecic, Denny, and Markus Krötzsch. "Wikidata: A Free Collaborative Knowledgebase." *Communications of the ACM* 57, no. 10 (2014): 78–85. DOI: 10.1145/2629489.

**What it covers:** Describes the design and evolution of Wikidata, the largest freely available knowledge graph (~100M items, ~1B statements). Explains how the ontology was designed to be multilingual and maintainable at scale.

**Why it matters for this course:** Used on Day 4 (ontology design) and Day 14 (production concerns) as a concrete existence proof that large-scale KG ontology maintenance is possible. The "data model" section (§3) is the key read.

---

### 7. Edge, Darren, Ha Trinh, Newman Cheng, Joshua Bradley, Alex Chao, Apurva Mody, Steven Truitt, and Jonathan Larson. "From Local to Global: A Graph RAG Approach to Query-Focused Summarization." Microsoft Research, 2024. arXiv:2404.16130.

Code: github.com/microsoft/graphrag

**What it covers:** Introduces GraphRAG — a retrieval approach that first extracts a KG from a corpus, then uses community detection to build hierarchical summaries, and finally retrieves over the graph structure rather than raw text. The central insight: some questions require global reasoning over a corpus that no single chunk answers.

**Why it matters for this course:** Primary source for Day 9. Section 3 ("Method") is the essential read. The GitHub repo is the working implementation adapted in Days 9–10.

**Key limitation:** GraphRAG's community-summarisation step is expensive (many LLM calls). Day 9 discusses when to use it vs. simpler KG-backed retrieval.

---

### 8. Wang, Quan, Zhendong Mao, Bin Wang, and Li Guo. "Knowledge Graph Embedding: A Survey of Approaches and Applications." *IEEE Transactions on Knowledge and Data Engineering* 29, no. 12 (2017): 2724–2743. DOI: 10.1109/TKDE.2017.2754499.

**What it covers:** A comprehensive survey of the KG embedding landscape: translation-based models (TransE, TransR, TransH), semantic matching models (RESCAL, DistMult, ComplEx), and applications (link prediction, triple classification, relation extraction).

**Why it matters for this course:** Used on Day 11 as the "map of the territory." Table 1 (model comparison) and §4 (applications) are the key reads if you want to know which embedding model to reach for given specific constraints.

---

## Video Lectures / Courses

### 9. Leskovec, Jure. *CS224W: Machine Learning with Graphs.* Stanford University, 2021.

Website: cs224w.stanford.edu  
YouTube: search "CS224W 2021 Stanford" for the full playlist.

**What it covers:** The best graph ML course available. Covers graph representation, spectral graph theory, node2vec, GNNs, KG completion, and reasoning. Lectures 1–3 (graph introduction, properties, and motifs) support Days 1–3. Lectures 7–9 (KG embeddings and completion) are required background for Day 11.

**Why it matters for this course:** Provides the mathematical foundations behind KG embeddings in a way that connects to your ML background. The lecture on "Knowledge Graphs" (Lecture 7) pairs perfectly with Day 11.

**Suggested viewing schedule:** Lecture 1 (first 20 min) alongside Day 1. Lecture 7 alongside Day 11.

---

### 10. Neo4j Graph Academy. *Neo4j Fundamentals + Cypher Fundamentals.* Free self-paced courses.

URL: graphacademy.neo4j.com

**What it covers:** Hands-on Cypher labs that pair with Days 3, 5, and 8. "Neo4j Fundamentals" (~1 hour) covers the property graph model interactively. "Cypher Fundamentals" (~2 hours) is the most efficient way to learn Cypher syntax with immediate feedback.

**Why it matters for this course:** These are labs, not lectures — use them as a companion to Days 3–5, not a substitute. Complete "Neo4j Fundamentals" before or alongside Day 3.

---

### 11. Microsoft Research. *GraphRAG: Unlocking LLM Discovery on Narrative Private Data.* 2024.

Blog post: microsoft.com/en-us/research/blog/graphrag-unlocking-llm-discovery-on-narrative-private-data  
GitHub: github.com/microsoft/graphrag  
YouTube: search "GraphRAG Microsoft Research 2024"

**What it covers:** The blog post is the best first read for Day 9 — it explains the motivation and pipeline in plain language before you read the paper. The GitHub repo is the implementation you will adapt on Days 9–10.

**Why it matters for this course:** Treat the repo README and `graphrag/index/` source code as required reading alongside Day 9. Understanding what the library does under the hood is the difference between using GraphRAG and being able to debug and extend it.

---

## Optional Deeper Dives

- **Pan, Jeff Z., et al. (eds.) *Exploiting Linked Data and Knowledge Graphs in Large Organisations.* Springer, 2017.** Enterprise-scale KG deployment patterns. Useful after Day 14 if you're architecting a multi-team KG.

- **Hamilton, William L. *Graph Representation Learning.* Morgan & Claypool, 2020. arXiv:2104.13478.** Deep dive into GNNs on KGs; relevant if you continue past this course toward graph neural networks and reasoning.

- **spaCy documentation — Relation Extraction projects.** spacy.io/usage/projects — Practical reference for the NLP pipeline on Day 7.

- **LlamaIndex — Property Graph Index documentation.** docs.llamaindex.ai — Ready-made KG + LLM integration. Inspect the source code during Days 9–10 to understand what it does under the hood before trusting it.

- **Angles, Renzo, and Claudio Gutierrez. "Survey of Graph Database Models." *ACM Computing Surveys* 40, no. 1 (2008): 1–39.** The canonical taxonomy of graph data models, pre-property-graph era. Historical context for why the LPG model won.
