*[Knowledge Graphs: From Concept to Production](../README.md) · Day 10 of 15*

# Day 10 — KGs as Agent Memory

> **Today's one idea:** A knowledge graph is the ideal persistent memory substrate for a multi-step AI agent — it is updatable, queryable, and traversable in ways that a vector store or a plain conversation log are not.
> **Reading time:** ~40 min · **Prereqs:** [Days 1–9](../README.md)
> **Primary source for today:** Robinson, Webber & Eifrem, *Graph Databases*, Chapter 4, §4.1–4.3 ("Building a Domain Graph"). Read alongside the code.

---

## The hook (2–4 min)

Your research agent just finished a 10-step task. Over those steps it learned:

- Alice Chen is the lead author on the most-cited paper about KG embeddings.
- That paper contradicts Bob Kumar's earlier findings.
- The DeepMind team is working on a follow-up, expected in Q3.
- Alice and Bob both appeared on the same panel at NeurIPS 2023.

Where do these facts go?

**Option A: Context window.** Gone next session.

**Option B: Vector store.** You can store "Alice Chen is the lead author..." as a chunk. But you can't efficiently answer "who are Alice's collaborators?" or "which of Bob's claims have been contradicted, and by whom?" without embedding and retrieving every relevant chunk and hoping they come back together.

**Option C: Knowledge graph.** Four triples, permanently stored:
```
(Alice Chen) --lead_author_of--> (KG Embeddings Paper)
(KG Embeddings Paper) --contradicts--> (Bob Kumar's Findings)
(DeepMind Team) --working_on--> (Follow-up Paper)
(Alice Chen) --appeared_with--> (Bob Kumar {event: "NeurIPS 2023"})
```

Next session, the agent runs a Cypher query: `MATCH (a:Person {name:"Alice Chen"})-[:APPEARED_WITH]->(b) RETURN b` and retrieves Bob Kumar in milliseconds — without hallucinating or confusing him with another Bob.

A KG as agent memory is not a nice-to-have. For agents that accumulate structured knowledge across sessions, it is architecturally correct.

---

## Building the intuition (10–15 min)

### Memory types in agent systems

Cognitive science distinguishes several memory types. Agents need all of them:

| Memory type | What it stores | Best substrate | KG's role |
|-------------|---------------|----------------|-----------|
| **Episodic** | What happened: events, interactions, sequences | Timestamped KG events or a log | ✓ Stores session events as nodes |
| **Semantic** | World knowledge: facts, entities, relationships | KG triples | ✓ Primary use case |
| **Procedural** | How to do things: skills, tool schemas | Code / few-shot prompts | ✗ Not a KG job |
| **Working** | Current task context | Context window | ✗ Not a KG job |

A KG handles episodic + semantic memory. The context window handles working memory. Don't try to put everything in one place.

### The agent memory loop

```
┌─────────────────────────────────────────────────────────────────┐
│                     AGENT TURN N                                │
│                                                                 │
│  1. RETRIEVE    Query KG for facts relevant to current task     │
│     (READ)      Cypher: MATCH relevant subgraph                │
│                 → inject as "memory context" into LLM prompt    │
│                                                                 │
│  2. REASON      LLM generates response using retrieved context  │
│                                                                 │
│  3. UPDATE      Extract new facts from LLM output               │
│     (WRITE)     Write triples to KG with MERGE                 │
│                 → graph grows with every turn                   │
│                                                                 │
│  4. Next turn: go to 1                                          │
└─────────────────────────────────────────────────────────────────┘
```

The KG grows smarter with every agent turn. Across sessions, the accumulated knowledge is always available.

---

## The formal picture (10–15 min)

### KG memory implementation

```python
# pip install neo4j anthropic
from neo4j import GraphDatabase
from datetime import datetime, UTC
import anthropic, json, re

client = anthropic.Anthropic()

# ── The Memory Store ──────────────────────────────────────────────────

class KGMemory:
    """
    A Neo4j-backed memory store for AI agents.
    Handles: write facts, retrieve by entity, retrieve by context, episodic log.
    """

    def __init__(self, uri: str, user: str, password: str):
        self.driver = GraphDatabase.driver(uri, auth=(user, password))
        self._ensure_indexes()

    def _ensure_indexes(self):
        with self.driver.session() as s:
            s.run("CREATE INDEX entity_name IF NOT EXISTS FOR (n:Entity) ON (n.name)")
            s.run("CREATE INDEX event_time IF NOT EXISTS FOR (n:Event) ON (n.timestamp)")

    # ── Write ─────────────────────────────────────────────────────────

    def add_fact(self, subject: str, predicate: str, obj: str,
                 subject_type: str = "Entity", object_type: str = "Entity",
                 confidence: float = 1.0, source: str = "agent") -> None:
        """Write a single triple to the KG."""
        with self.driver.session() as s:
            s.run(f"""
                MERGE (sub:{subject_type} {{name: $subject}})
                MERGE (ob:{object_type}  {{name: $object}})
                MERGE (sub)-[r:{predicate.upper()}]->(ob)
                ON CREATE SET r.confidence = $confidence,
                              r.source     = $source,
                              r.created_at = datetime()
                ON MATCH  SET r.confidence = $confidence,
                              r.last_seen  = datetime()
            """, subject=subject, object=obj,
                 confidence=confidence, source=source)

    def add_facts(self, triples: list[dict]) -> None:
        """Write multiple triples. Each dict: {subject, predicate, object, ...}"""
        for t in triples:
            self.add_fact(
                subject=t["subject"], predicate=t["predicate"], obj=t["object"],
                subject_type=t.get("subject_type", "Entity"),
                object_type=t.get("object_type", "Entity"),
                confidence=t.get("confidence", 1.0),
                source=t.get("source", "agent"),
            )

    def log_episode(self, session_id: str, turn: int, summary: str) -> None:
        """Log a session episode as a timestamped Event node."""
        with self.driver.session() as s:
            s.run("""
                CREATE (e:Event {
                    session_id: $session_id,
                    turn:       $turn,
                    summary:    $summary,
                    timestamp:  datetime()
                })
            """, session_id=session_id, turn=turn, summary=summary)

    # ── Read ──────────────────────────────────────────────────────────

    def get_facts_about(self, entity: str, hops: int = 1) -> list[dict]:
        """Return all facts within N hops of an entity."""
        with self.driver.session() as s:
            result = s.run(f"""
                MATCH (n {{name: $entity}})-[r*1..{hops}]-(m)
                RETURN n.name AS source, type(last(r)) AS relation, m.name AS target
                LIMIT 50
            """, entity=entity)
            return [dict(record) for record in result]

    def get_related_entities(self, entity: str, predicate: str = None) -> list[str]:
        """Return direct neighbours, optionally filtered by relation type."""
        cypher = "MATCH ({name: $entity})-"
        cypher += f"[:{predicate.upper()}]" if predicate else "[r]"
        cypher += "->(m) RETURN m.name AS name"
        with self.driver.session() as s:
            return [r["name"] for r in s.run(cypher, entity=entity)]

    def search_entities(self, keyword: str) -> list[dict]:
        """Find entities whose name contains a keyword (case-insensitive)."""
        with self.driver.session() as s:
            result = s.run("""
                MATCH (n) WHERE toLower(n.name) CONTAINS toLower($keyword)
                RETURN n.name AS name, labels(n) AS types
                LIMIT 20
            """, keyword=keyword)
            return [dict(r) for r in result]

    def get_recent_episodes(self, n: int = 5) -> list[dict]:
        """Return the N most recent session events."""
        with self.driver.session() as s:
            result = s.run("""
                MATCH (e:Event)
                RETURN e.session_id AS session, e.turn AS turn,
                       e.summary AS summary, e.timestamp AS ts
                ORDER BY e.timestamp DESC LIMIT $n
            """, n=n)
            return [dict(r) for r in result]

    def close(self):
        self.driver.close()

# ── Fact extraction from agent output ────────────────────────────────

FACT_EXTRACTION_PROMPT = """Extract factual statements from this text as knowledge graph triples.

Output ONLY a JSON array of objects with keys: subject, predicate, object, subject_type, object_type.
Use snake_case predicates. subject_type/object_type: Person, Organisation, Software, Concept, Event, Other.
Only include facts explicitly stated — no inference.

Text: {text}"""

def extract_facts_from_text(text: str) -> list[dict]:
    response = client.messages.create(
        model="claude-haiku-4-5-20251001",
        max_tokens=1024,
        messages=[{"role": "user",
                   "content": FACT_EXTRACTION_PROMPT.format(text=text)}]
    )
    raw = response.content[0].text.strip()
    raw = re.sub(r"```(?:json)?\n?", "", raw).strip().rstrip("`")
    try:
        return json.loads(raw)
    except json.JSONDecodeError:
        return []

# ── The Memory-Augmented Agent ────────────────────────────────────────

AGENT_SYSTEM_PROMPT = """You are a research assistant with access to a knowledge graph memory.

At the start of each turn you receive:
- MEMORY CONTEXT: facts retrieved from your KG about entities in the user's question
- RECENT EPISODES: summaries of your recent activity

Use the memory context to give specific, factual answers. 
After answering, always state what new facts (if any) you've learned that should be stored."""

class MemoryAgent:
    def __init__(self, memory: KGMemory, session_id: str = None):
        self.memory = memory
        self.session_id = session_id or f"session_{datetime.now(UTC).strftime('%Y%m%d_%H%M%S')}"
        self.turn = 0
        self.history = []

    def _build_memory_context(self, query: str) -> str:
        """Retrieve relevant facts from KG for this query."""
        # Extract likely entity names from query (crude: capitalised words)
        words = [w.strip("?.!") for w in query.split() if w[0].isupper()]
        facts = []
        for word in words[:3]:  # top 3 entities
            entity_facts = self.memory.get_facts_about(word, hops=2)
            facts.extend(entity_facts)

        if not facts:
            return "No relevant facts found in memory."
        return "\n".join(
            f"  {f['source']} --{f['relation']}--> {f['target']}"
            for f in facts[:20]
        )

    def chat(self, user_message: str) -> str:
        self.turn += 1

        # Step 1: Retrieve relevant memory
        memory_ctx = self._build_memory_context(user_message)
        episodes = self.memory.get_recent_episodes(3)
        episode_ctx = "\n".join(
            f"  Turn {e['turn']}: {e['summary']}" for e in episodes
        ) or "  No recent episodes."

        # Step 2: Build the prompt with memory context
        context_block = (
            f"MEMORY CONTEXT:\n{memory_ctx}\n\n"
            f"RECENT EPISODES:\n{episode_ctx}"
        )
        messages = self.history + [
            {"role": "user", "content": f"{context_block}\n\nUser: {user_message}"}
        ]

        # Step 3: Generate response
        response = client.messages.create(
            model="claude-sonnet-4-6",
            max_tokens=1024,
            system=AGENT_SYSTEM_PROMPT,
            messages=messages,
        )
        agent_reply = response.content[0].text

        # Step 4: Extract and store new facts
        new_facts = extract_facts_from_text(agent_reply)
        if new_facts:
            self.memory.add_facts(new_facts)

        # Step 5: Log the episode
        self.memory.log_episode(
            session_id=self.session_id,
            turn=self.turn,
            summary=f"User asked: {user_message[:60]}... | Stored {len(new_facts)} facts"
        )

        # Update conversation history (keep last 6 turns)
        self.history.append({"role": "user",    "content": user_message})
        self.history.append({"role": "assistant","content": agent_reply})
        self.history = self.history[-12:]

        return agent_reply
```

### Running the memory agent

```python
memory = KGMemory("bolt://localhost:7687", "neo4j", "password123")
agent = MemoryAgent(memory)

# Turn 1: learn some facts
reply1 = agent.chat(
    "I just read that Alice Chen at NTU published a paper showing that "
    "TransE fails on symmetric relations. The paper cites Bob Kumar's work."
)
print("AGENT:", reply1[:200])

# Turn 2: the agent should remember Alice from turn 1
reply2 = agent.chat("What do you know about Alice Chen's research?")
print("\nAGENT:", reply2[:200])

# Verify the graph grew
print("\nFacts about Alice Chen:")
for fact in memory.get_facts_about("Alice Chen"):
    print(f"  {fact['source']} --{fact['relation']}--> {fact['target']}")

# New session: memory persists
agent2 = MemoryAgent(memory, session_id="session_day2")
reply3 = agent2.chat("Remind me about the TransE paper we discussed before.")
print("\nNEW SESSION AGENT:", reply3[:200])
```

---

## Where it breaks / what it is not (3–5 min)

**KG memory has no built-in forgetting.** A vector store with a recency-weighted retrieval naturally surfaces recent information. A KG stores all facts equally, forever. For production agents, add a `staleness` property to edges and filter in queries: `WHERE r.last_seen > datetime() - duration('P30D')`.

**The retrieval step is a bottleneck.** "Retrieve all facts within 2 hops of 3 entities" can return hundreds of triples. If you inject all of them into the prompt, you'll overflow the context window. Add a relevance-based filter or summarise the retrieved subgraph before injection.

**The extraction step is noisy.** Extracting facts from the agent's own output means the KG accumulates the agent's assertions, not just verified facts. Hallucinated facts become memory. Consider: only store facts the agent explicitly marks as verified, or include a `confidence` score from the extraction step.

**KG memory doesn't replace vector memory.** For "what did the user say 3 sessions ago in full detail?" a vector store over conversation chunks is better. For "what facts does the agent know about Alice Chen?" the KG is better. In production, use both: a vector store for episodic recall, a KG for structured semantic memory.

---

## Try it yourself (5–10 min)

**Exercise 1 — Run it (L1/L2):** Run the memory agent for 3 turns. Teach it something in Turn 1 and check whether it recalls it in Turn 3 without you repeating it.

**Exercise 2 — Cross-session persistence (L2):** Create a second `MemoryAgent` with a new session ID but the *same* `KGMemory` instance. Ask it about a fact that was only mentioned in the first agent's session. Verify it retrieves it correctly.

**Exercise 3 — Add forgetting (L2):** Add a `forget_older_than_days(n: int)` method to `KGMemory` that deletes edges whose `last_seen` is older than N days. Use:
```cypher
MATCH ()-[r]->() 
WHERE r.last_seen < datetime() - duration({days: $n})
DELETE r
```

**Exercise 4 — Stretch (L2):** Add a `summarise_memory(entity: str)` method that: (1) retrieves the 2-hop subgraph around the entity, (2) passes it to Claude Haiku, (3) returns a 2-sentence natural language summary. Use this to inject a compact memory context instead of raw triples.

---

## Connect it back

[Day 9](day-09-graphrag.md) showed how to *retrieve* from a KG. Today you showed how an agent can *write to* a KG — accumulating knowledge across turns and sessions. The KG is no longer just a static index; it is a growing, queryable, persistent mind. Tomorrow you'll learn KG embeddings: how to turn the graph's structure into vectors, so that you can predict *missing facts* — the edges the agent doesn't know exist yet.

**The question you can answer today that you couldn't this morning:** *Why is a KG better than a vector store for answering "who are all the people connected to Alice, and via what relationships?" — and when is the vector store better?*

---

## Suggested readings for today

**Required if you have 15 extra minutes:** Robinson et al., *Graph Databases*, Chapter 4, §4.1–4.3, pp. 75–105. Free at neo4j.com/graph-databases-book/ — walks through a real-world domain graph design that maps directly to the agent memory schema you built today.

**If you want the deep version:**
- LangChain Neo4j integration: python.langchain.com/docs/integrations/graphs/neo4j_cypher — if you use LangChain, `GraphCypherQAChain` wraps the local search pattern from today into a tool-calling agent. Read the source to understand what it abstracts.
- Park, Joon Sung, et al. "Generative Agents: Interactive Simulacra of Human Behavior." arXiv:2304.03442, 2023. The landmark paper on agent memory architectures — includes importance scoring and reflection synthesis. Today's KGMemory is a structured version of their memory stream.
- Neo4j `neo4j-graphrag-python` library: github.com/neo4j/neo4j-graphrag-python — Neo4j's official Python library for RAG over property graphs. Combines the Day 9 retrieval patterns with the Day 10 memory patterns in a production-ready package.

---

← [Day 9 — GraphRAG](day-09-graphrag.md) &nbsp;|&nbsp; [Day 11 — KG Embeddings →](day-11-kg-embeddings.md)
