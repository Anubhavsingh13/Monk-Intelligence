*[Knowledge Graphs: From Concept to Production](../README.md) · Day 7 of 15*

# Day 7 — Entity and Relation Extraction

> **Today's one idea:** A KG is only as good as the facts it contains — and NLP pipelines (NER for entities, RE for relationships, LLM prompting for both) are how you convert unstructured text into queryable `(subject, predicate, object)` triples automatically.
> **Reading time:** ~40 min · **Prereqs:** [Days 1–5](../README.md) + [Day 6](day-06-rest-and-synthesize-i.md)
> **Primary source for today:** Hogan et al., *Knowledge Graphs* (MIT Press, 2021), §5.1–5.3 ("KG Creation from Text"), pp. 133–162. Preprint: arXiv:2003.02320.

---

## The hook (2–4 min)

You have 500 GitHub issues, each a paragraph of text. You want a KG that answers: "Which components are mentioned together in bug reports? Which engineers fix which component types? Which issues are related to the same root cause?"

You cannot hand-craft these 5,000 triples. You need a pipeline that reads a paragraph and outputs structured facts. This is the **information extraction** problem, and it is the bottleneck in every real KG project.

Two paradigms exist. The classic approach: train a Named Entity Recogniser (NER) to find entity spans, then train a Relation Extractor (RE) to classify relationships between entity pairs. The modern approach: prompt an LLM to produce structured JSON output in one shot. Both have their place — and today you'll build both.

---

## Building the intuition (10–15 min)

### What you need to extract

From the sentence *"Alice Chen reported that the memory module in the AgentCore framework crashes when given a null input."*

You want:

| Subject | Predicate | Object |
|---------|-----------|--------|
| Alice Chen | reported | Bug #347 |
| Bug #347 | affects | memory module |
| memory module | part_of | AgentCore framework |
| Bug #347 | triggered_by | null input |

Three steps are required:
1. **Named Entity Recognition (NER):** identify spans — "Alice Chen" (Person), "AgentCore" (Software), "memory module" (Component), "null input" (Input).
2. **Relation Extraction (RE):** for each pair of entities, classify the relation — "Alice Chen → reported → Bug", "memory module → part_of → AgentCore".
3. **Triple normalisation:** clean, deduplicate, resolve coreferences ("it crashes" → "the memory module crashes").

### Approach A: spaCy NER (fast, deterministic)

spaCy's English model recognises standard entity types (PERSON, ORG, GPE, PRODUCT, etc.) out of the box:

```python
import spacy

# pip install spacy && python -m spacy download en_core_web_sm
nlp = spacy.load("en_core_web_sm")

text = """Alice Chen reported that the AgentCore memory module crashes 
when given a null input. Bob Kumar at DeepMind confirmed the bug."""

doc = nlp(text)

# Extract named entities
entities = [(ent.text, ent.label_, ent.start_char, ent.end_char)
            for ent in doc.ents]
for e in entities:
    print(f"  {e[0]:30s} → {e[1]}")
```

**Output:**
```
  Alice Chen                     → PERSON
  AgentCore                      → ORG         ← wrong: it's a software framework
  Bob Kumar                      → PERSON
  DeepMind                       → ORG
```

spaCy's stock model gets person and organisation types right but has no concept of "software component" or "bug." For domain-specific KGs, you need either a custom NER model or an LLM.

### Approach B: LLM extraction (flexible, powerful)

The LLM approach: give the model the text plus a structured output schema, ask for triples. This generalises to any domain without training data.

```python
import json
import anthropic  # pip install anthropic

client = anthropic.Anthropic()  # set ANTHROPIC_API_KEY env var

EXTRACTION_PROMPT = """Extract knowledge graph triples from the text below.

Output a JSON list of triples. Each triple is:
  {"subject": "...", "predicate": "...", "object": "...", "subject_type": "...", "object_type": "..."}

Rules:
- Use snake_case for predicates (e.g., "part_of", "reported_by", "affects")
- subject_type and object_type should be one of: Person, Software, Component, Bug, Location, Concept
- Include only facts explicitly stated in the text — no inference
- Normalise entity names (full names, not pronouns)

Text:
{text}

Return ONLY the JSON array, no explanation."""

def extract_triples(text: str) -> list[dict]:
    response = client.messages.create(
        model="claude-sonnet-4-6",
        max_tokens=1024,
        messages=[{
            "role": "user",
            "content": EXTRACTION_PROMPT.format(text=text)
        }]
    )
    raw = response.content[0].text.strip()
    # Strip markdown code fences if present
    if raw.startswith("```"):
        raw = raw.split("```")[1]
        if raw.startswith("json"):
            raw = raw[4:]
    return json.loads(raw)

# Test
text = """Alice Chen reported that the memory module in the AgentCore 
framework crashes when given a null input. Bob Kumar at DeepMind 
confirmed the bug and assigned it to the core team."""

triples = extract_triples(text)
for t in triples:
    print(f"  ({t['subject_type']}) {t['subject']:20s}"
          f"  --{t['predicate']}-->"
          f"  ({t['object_type']}) {t['object']}")
```

**Sample output:**
```
  (Person) Alice Chen            --reported-->       (Bug) memory module crash
  (Bug) memory module crash      --affects-->        (Component) memory module
  (Component) memory module      --part_of-->        (Software) AgentCore
  (Bug) memory module crash      --triggered_by-->   (Concept) null input
  (Person) Bob Kumar             --affiliated_with--> (Software) DeepMind
  (Bug) memory module crash      --confirmed_by-->   (Person) Bob Kumar
```

This is the approach you'll use in the ingestion pipeline on Day 8.

---

## The formal picture (10–15 min)

### The NLP pipeline stages

A complete extraction pipeline has five stages:

```
Raw Text
   │
   ▼
[1] Sentence Splitting + Tokenisation
   (split "Alice reported a bug." into tokens)
   │
   ▼
[2] Named Entity Recognition (NER)
   (tag spans: "Alice" → PERSON, "AgentCore" → SOFTWARE)
   │
   ▼
[3] Coreference Resolution (optional but important)
   ("it crashes" → "memory module crashes")
   │
   ▼
[4] Relation Extraction (RE)
   (for each entity pair, classify or extract the predicate)
   │
   ▼
[5] Triple Normalisation
   (deduplicate, canonicalise entity names, filter low-confidence)
   │
   ▼
Triples: [(subject, predicate, object), ...]
```

With an LLM, stages 2–4 collapse into one prompt. You still need normalisation (stage 5).

### Normalisation and entity resolution

The hardest problem in information extraction is not extraction — it's **entity resolution**: deciding that "Alice Chen", "A. Chen", "Dr. Chen", and "alice.chen@co.com" are all the same entity.

Three strategies:

**String matching (baseline):**
```python
def normalise_entity(name: str) -> str:
    """Lowercase + strip whitespace. Very crude."""
    return name.lower().strip()
```

**Fuzzy matching (practical for small KGs):**
```python
from difflib import SequenceMatcher

def entity_similarity(a: str, b: str) -> float:
    return SequenceMatcher(None, a.lower(), b.lower()).ratio()

def resolve_entity(candidate: str, existing_entities: list[str], threshold=0.85) -> str:
    """Return the best-matching existing entity, or the candidate if no match."""
    for entity in existing_entities:
        if entity_similarity(candidate, entity) >= threshold:
            return entity
    return candidate
```

**Embedding similarity (best for production):**
```python
# Use a sentence transformer to embed entity names
# then find nearest neighbour in existing entity set
# (covered in Day 11 — KG Embeddings)
```

### Building a reusable extractor class

```python
from dataclasses import dataclass, field

@dataclass
class Triple:
    subject: str
    predicate: str
    object: str
    subject_type: str = ""
    object_type: str = ""
    confidence: float = 1.0
    source_text: str = ""

class KGExtractor:
    def __init__(self, model="claude-sonnet-4-6"):
        self.client = anthropic.Anthropic()
        self.model = model
        self.entity_registry: dict[str, str] = {}  # normalised_name → canonical_name

    def extract(self, text: str, source_id: str = "") -> list[Triple]:
        raw_triples = extract_triples(text)  # from above
        return [
            Triple(
                subject=self._resolve(t["subject"]),
                predicate=t["predicate"].lower().replace(" ", "_"),
                object=self._resolve(t["object"]),
                subject_type=t.get("subject_type", ""),
                object_type=t.get("object_type", ""),
                source_text=source_id,
            )
            for t in raw_triples
        ]

    def _resolve(self, name: str) -> str:
        """Resolve to canonical entity name using fuzzy matching."""
        canonical = resolve_entity(name, list(self.entity_registry.values()))
        key = name.lower().strip()
        if key not in self.entity_registry:
            self.entity_registry[key] = canonical
        return canonical

    def extract_from_documents(self, docs: list[dict]) -> list[Triple]:
        """
        docs: [{"id": "doc1", "text": "..."}, ...]
        Returns all triples across all documents.
        """
        all_triples = []
        for doc in docs:
            triples = self.extract(doc["text"], source_id=doc["id"])
            all_triples.extend(triples)
        return all_triples
```

### Processing multiple documents

```python
extractor = KGExtractor()

# Sample documents (in a real project, these come from your loader)
documents = [
    {
        "id": "issue_001",
        "text": "Alice Chen reported that the memory module in AgentCore crashes on null input."
    },
    {
        "id": "issue_002",
        "text": "Bob Kumar at DeepMind confirmed the AgentCore memory bug and linked it to issue_001."
    },
    {
        "id": "issue_003",
        "text": "The AgentCore retrieval module was fixed by Alice Chen in PR #142."
    },
]

all_triples = extractor.extract_from_documents(documents)
print(f"\nExtracted {len(all_triples)} triples:\n")
for t in all_triples:
    print(f"  [{t.subject_type}] {t.subject:25s} --{t.predicate}--> [{t.object_type}] {t.object}")
```

---

## Where it breaks / what it is not (3–5 min)

**LLMs hallucinate triples.** The model may extract a relationship that isn't explicitly stated in the text — it infers it. Always add `"Include only facts explicitly stated"` to your prompt and spot-check a sample of extractions.

**Pronouns are killers.** "Alice reported the bug. *She* said it affects the core module." — "She" and "it" require coreference resolution. Stock LLM extraction often resolves pronouns correctly, but silently fails on complex cases. For high-stakes KGs, run a coreference resolution pass first (spaCy's `coreferee` extension, or ask the LLM to resolve pronouns before extraction).

**LLM extraction is expensive at scale.** 1000 documents × 1 LLM call each at $0.003/call = $3. Manageable. 1,000,000 documents = $3,000. At scale, consider: fine-tuning a smaller model, using spaCy for entity recognition + LLM only for relation extraction, or batch processing with caching.

**Predicate vocabulary drift.** If document 1 uses `reported_by` and document 2 uses `was_reported_by` and document 3 uses `filed_by`, you get three relation types that mean the same thing. Fix: provide an explicit allowed predicate vocabulary in your extraction prompt. Day 4's ontology is the right input for this.

---

## Try it yourself (5–10 min)

**Exercise 1 — Manual extraction (L1):** Extract all triples from this text manually (no code). Focus on getting the predicate names consistent.

> "The LangChain framework was created by Harrison Chase in 2022. It integrates with OpenAI, Anthropic, and HuggingFace. LlamaIndex, created by Jerry Liu, is a competing framework that focuses on RAG pipelines. Both frameworks support Neo4j as a vector and graph store."

**Exercise 2 — LLM extraction (L1/L2):** Run `extract_triples()` on the text above. Compare the LLM's output to your manual extraction from Exercise 1. What did the LLM get right, wrong, or add that you missed?

**Exercise 3 — Domain vocabulary (L2):** Modify the `EXTRACTION_PROMPT` to accept an explicit list of allowed predicate types. Test it with the predicates: `["created_by", "integrates_with", "competes_with", "focuses_on", "supports"]`. Does constraining the vocabulary improve consistency?

<details>
<summary>Key modification for Exercise 3</summary>

```python
EXTRACTION_PROMPT_CONSTRAINED = """Extract knowledge graph triples from the text below.

Allowed predicates (use ONLY these): {predicates}

Output a JSON list of triples:
  {{"subject": "...", "predicate": "...", "object": "..."}}

If no allowed predicate fits, skip the relationship.
Text: {text}

Return ONLY the JSON array."""

def extract_triples_constrained(text: str, predicates: list[str]) -> list[dict]:
    pred_str = ", ".join(f'"{p}"' for p in predicates)
    response = client.messages.create(
        model="claude-sonnet-4-6",
        max_tokens=1024,
        messages=[{"role": "user",
                   "content": EXTRACTION_PROMPT_CONSTRAINED.format(
                       text=text, predicates=pred_str)}]
    )
    raw = response.content[0].text.strip()
    if raw.startswith("```"):
        raw = raw.split("```")[1]
        if raw.startswith("json"): raw = raw[4:]
    return json.loads(raw)
```
</details>

**Exercise 4 — Stretch (L2):** Process the three GitHub issues from today's formal section using `KGExtractor`. Verify that "AgentCore" appears as the *same entity* in all three documents (entity resolution is working). Print the final entity registry.

---

## Connect it back

[Days 1–5](day-01-what-is-a-knowledge-graph.md) taught you the shape of a KG. [Day 6](day-06-rest-and-synthesize-i.md) confirmed you can reason about it. Today you learned how to *feed* a KG from raw text. Tomorrow you'll connect the extractor to a Neo4j instance, add deduplication, and build the complete loader → transformer → writer pipeline that takes a folder of documents and produces a live, queryable KG.

**The question you can answer today that you couldn't this morning:** *Why does providing an explicit predicate vocabulary in your extraction prompt improve the quality of a downstream KG?*

---

## Suggested readings for today

**Required if you have 15 extra minutes:** Hogan et al., *Knowledge Graphs*, §5.1 "Information Extraction," pp. 133–148. Free preprint: arXiv:2003.02320. Covers the classic NER+RE pipeline and the transition to neural approaches — the context for why LLM extraction works so well.

**If you want the deep version:**
- spaCy documentation, "Training Pipelines & Models": spacy.io/usage/training — if you want a custom NER model for your domain, this is the starting point. Requires ~500 annotated examples per entity type.
- spaCy `coreferee` extension: github.com/msg-systems/coreferee — coreference resolution for spaCy. Needed when your documents have many pronouns. Install: `pip install coreferee && python -m coreferee install en`.
- LlamaIndex, "Property Graph Index": docs.llamaindex.ai — LlamaIndex has a built-in KG extraction pipeline that wraps exactly what you built today. Read the source code to understand what it abstracts away before trusting it.

---

← [Day 6 — Rest & Synthesize I](day-06-rest-and-synthesize-i.md) &nbsp;|&nbsp; [Day 8 — The Ingestion Pipeline →](day-08-ingestion-pipeline.md)
