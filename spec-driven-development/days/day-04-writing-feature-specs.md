*[Spec-Driven Development](../../README.md) · Day 4 of 7*

# Day 4 — Writing Feature Specs

> **Today's one idea:** A feature spec is three sections — shaped pitch, no-gos, and acceptance criteria — and the fastest way to write it is to start with the acceptance criteria and work backwards.
> **Reading time:** ~35 min · **Prereqs:** [Day 2](day-02-anatomy-of-executable-spec.md), [Day 3](day-03-project-constitution.md)
> **Primary source for today:** *Shape Up*, Ryan Singer, Basecamp, 2019 — Chapter 5 ("Writing the Pitch"). Free at basecamp.com/shapeup/chapters/05-writing-the-pitch.

---

## The hook

You have a constitution (Day 3) and you understand the four layers (Day 2). Now comes the moment of truth: a product idea in your head, a coding agent waiting, and a blank document.

Most PMs write a feature spec the same way they'd write an email: start at the top, narrate the idea, add some bullets, done. The result is usually a document that a human developer would find helpful (because they ask clarifying questions) but that a coding agent will misinterpret (because it doesn't).

The professional spec-writer's trick is counterintuitive: **don't start with the feature. Start with done.**

Write the acceptance criteria first. If you can write three testable criteria, you know what you want. If you can't, you need to think more before you write anything else. The criteria are the real spec. Everything else — the pitch, the problem statement, the solution sketch — is context that helps the agent build the right thing.

---

## Building the intuition

A surgeon doesn't pick up a scalpel and start cutting. They review the patient's chart, confirm the procedure, mark the site. The chart is the spec. The "done" criteria (post-op vitals, recovery benchmarks) are agreed before the incision.

You are the surgeon. The coding agent is not — it is the operating theater: highly capable, completely literal, and entirely dependent on your prep work.

The feature spec is your pre-op chart. Its job is to make the agent's job deterministic: at the end, you check the vitals (acceptance criteria), and you know whether the procedure succeeded.

Here is the shape of a feature spec:

1. **Appetite** — how much time/complexity is this worth?
2. **Problem** — what pain does this solve, and for whom?
3. **Solution sketch** — what does the solution look like? (rough, not detailed)
4. **No-gos** — what are we explicitly NOT building in this spec?
5. **Acceptance criteria** — how do we know it's done?

You write them in reverse order (criteria → no-gos → sketch → problem → appetite), but you present them in this order. Writing in reverse forces clarity; presenting forward builds understanding.

---

## The formal picture

### The feature spec template

```markdown
# Feature: [Title — 5 words max]

## Appetite
[Small / Medium / Large]
- Small: a few hours, one focused session with the agent
- Medium: 1–2 days, possibly multiple agent sessions
- Large: 3–5 days, requires breaking into sub-specs

## Problem
[1 paragraph: what pain does this solve? Who experiences it? 
How do they experience it today, without this feature?]

## Solution sketch
[2–4 sentences or a short bullet list describing what the solution 
looks like from the user's perspective. Not the implementation — 
the user experience. Optionally include a rough ASCII sketch.]

## No-gos
- [What we are NOT building in this spec]
- [Scope creep prevention, explicit exclusions]
- [Things that might seem related but are out of scope]

## Acceptance criteria
- [ ] Given [context], when [action], then [observable outcome]
- [ ] Given [context], when [action], then [observable outcome]
- [ ] Given [context], when [action], then [observable outcome]
```

---

### Walking through it: CSV export feature

Let's write a complete spec for "add CSV export to the admin user list."

---

**Step 1 — Write acceptance criteria first.**

Before anything else, answer: *how will I know this is done?*

```markdown
## Acceptance criteria
- [ ] Given the admin is on the user list page, when they click 
      "Export CSV", then a CSV file downloads containing all 
      currently visible users (i.e., filtered by the active search).
- [ ] Given the CSV download, when opened in a spreadsheet, then 
      it contains columns: Name, Email, Role, Created Date — 
      in that order.
- [ ] Given the user list has 0 users (after filtering), when the 
      admin clicks "Export CSV", then the file downloads with only 
      the header row and no data rows.
```

Three criteria. Each one is testable in under 30 seconds. If all three are true, the feature is done.

Notice: the first criterion ties the export to "currently visible users" — that's an important business rule (export what you see, not everything). Writing the criterion first forced that decision.

---

**Step 2 — Write the no-gos.**

Now ask: *what might a reasonable developer build that I don't want?*

```markdown
## No-gos
- No server-side export (export should be generated client-side from 
  the already-fetched data, not a new API endpoint)
- No scheduled exports or email delivery
- No Excel (.xlsx) format — CSV only
- No custom column selection UI
- No export of archived/deleted users
```

These are the rabbit holes. A reasonable agent might build a server-side export endpoint with streaming (performant, complex). It might add a "choose your columns" modal (thoughtful, out of scope). The no-gos eliminate those paths.

---

**Step 3 — Write the solution sketch.**

Now that you know what done looks like and what's out of scope, describe the solution briefly:

```markdown
## Solution sketch
An "Export CSV" button appears in the top-right of the user list page, 
next to the search box. Clicking it generates a CSV file from the 
currently displayed (filtered) user data and triggers a browser download. 
No new page, no modal, no API call — just a client-side download.
```

Two sentences. No implementation details. The agent decides how to generate the CSV — `Blob`, `URL.createObjectURL`, a library — because that's an implementation detail and it's better at those than you are.

---

**Step 4 — Write the problem statement.**

```markdown
## Problem
Admins regularly need to share the user list with other teams (HR, finance, 
operations) who don't have system access. Currently they take a screenshot 
or manually copy the table into a spreadsheet — both are slow and error-prone 
when the list is long. They need a one-click export.
```

This paragraph serves two purposes: it helps the agent understand *why* the feature exists (useful for edge cases), and it doubles as your PRD justification for stakeholders.

---

**Step 5 — Set the appetite.**

```markdown
## Appetite
Small — client-side CSV generation from an already-fetched array is 
a 1–2 hour task. If a simple solution doesn't exist, we descope 
rather than add complexity.
```

The appetite is not just a time estimate. It is a decision rule: *if the simple path doesn't exist, we descope*. This prevents the agent from building an elaborate solution when you wanted a simple one.

---

### The complete spec

```markdown
# Feature: CSV Export — User List

## Appetite
Small (1–2 hours). If client-side CSV from existing data isn't 
straightforward, descope rather than add complexity.

## Problem
Admins need to share the user list with teams who lack system access. 
Currently they screenshot or copy manually — slow and error-prone for 
large lists. They need a one-click export.

## Solution sketch
An "Export CSV" button in the top-right of the user list page (next to 
the search box). Click → CSV downloads in the browser. Uses currently 
visible (filtered) users only. No new page, no modal, no API call.

## No-gos
- No server-side export endpoint
- No scheduled exports or email delivery
- No Excel format — CSV only
- No custom column selection
- No export of archived/deleted users

## Acceptance criteria
- [ ] Clicking "Export CSV" downloads a CSV of currently visible users.
- [ ] The CSV has columns: Name, Email, Role, Created Date (in that order).
- [ ] With 0 visible users, the CSV downloads with headers only.
```

This spec is one page. It took approximately 10 minutes to write (in reverse order). It is complete enough for a coding agent to implement correctly on the first try — because it closes the right degrees of freedom without prescribing the implementation.

---

### Where the spec lives in the repo

```
project-root/
├── CLAUDE.md           ← project constitution
├── specs/
│   ├── done/
│   │   └── feature-search-user-list.md   ← archived after ship
│   └── feature-csv-export-user-list.md   ← current spec
└── src/
    └── ...
```

Specs live in the repo, not in Notion or Confluence. This matters because:
1. The agent can reference them by file path
2. Git history shows when specs were written and how they evolved
3. Colleagues can review specs in pull requests alongside code

---

## Where it breaks / what it is not

**"My acceptance criteria are not testable — they're quality goals."**
Then they are not acceptance criteria. "The export should be fast" is not testable. "The CSV download initiates within 2 seconds of clicking the button on a list of 1000 users" is testable. If you can't make it testable, remove it from criteria and add it to constraints ("export must complete in under 2 seconds").

**"The solution sketch is too detailed."**
If your solution sketch tells the agent which React hook to use, which variable to name what, or how to structure the component, you have gone too far. The sketch describes user-visible behavior. Implementation is the agent's job.

**"I don't know enough to write the no-gos."**
That's actually fine — you can ask the agent. "Here is a feature idea: [1 sentence]. What are the most common implementation approaches, and which trade-offs exist?" Use the answer to inform your no-gos. This is using the agent as a thinking partner before it becomes an executor.

**"Do I need all five sections every time?"**
For small features: appetite + no-gos + acceptance criteria is enough. Skip problem and solution sketch if the feature is obvious and self-contained. Never skip acceptance criteria.

---

## Try it yourself

### Exercise 1 — Criteria-first practice
Pick a feature from your current product backlog. Write the acceptance criteria *before* anything else. Set a 5-minute timer. If you can't write three criteria in 5 minutes, that feature isn't ready to be specced yet — it needs more thinking first.

### Exercise 2 — The no-gos test
For the same feature: imagine you handed it to a junior developer who builds things very literally. What would they build that you don't want? Write those as no-gos.

### Exercise 3 — Write the full spec
Combine Exercises 1 and 2 with a problem statement and solution sketch. Time yourself — a well-understood feature should take under 15 minutes to spec. If it takes longer, the feature is not well-understood yet.

<details>
<summary>Hint for Exercise 1</summary>
If you're stuck on acceptance criteria, start with the "happy path" only: what is the single most important thing that must be true for this feature to be considered working? Write that as your first criterion. Then ask: what are the two most important edge cases? Those are criteria 2 and 3.
</details>

---

## Connect it back

Day 1 established that a spec is a function input. Day 2 gave you the four-layer anatomy. Day 3 gave you the project-level context that all specs inherit from. Today you have the full feature spec template — a one-page document that takes 10 minutes to write and gives the agent everything it needs.

The question you should now be able to answer: *What is the difference between a solution sketch and an acceptance criterion?*

A sketch describes the solution from the user's perspective (what they see and do). A criterion describes the outcome in verifiable terms (true or false after the agent is done). A sketch can be vague; a criterion cannot.

**Tomorrow (Day 5)** you learn what to actually *do* with this spec — how to run it through a coding agent using the plan-implement-verify loop. You will finally type the prompts.

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
Singer, *Shape Up*, Chapter 5 ("Writing the Pitch"), basecamp.com/shapeup/chapters/05-writing-the-pitch. Focus on the "no-gos" section and the "solution sketches" examples. Singer's hand-drawn sketches are deliberately rough — the point is to communicate direction, not prescription. That same principle applies to today's solution sketch section.

**If you want the deep version:**

- Singer, *Shape Up*, Chapter 2 ("Principles of Shaping"), basecamp.com/shapeup/chapters/02-principles-of-shaping. The concept of a "shaped" solution vs. a "raw" idea vs. a "over-specified" task. The right level of abstraction for a spec is the central argument. Read this if the question "how much detail is enough?" is still unresolved after today.

- Wynne & Hellesøy, *The Cucumber Book*, Chapter 4 ("Step Definitions: From the Outside In"). Shows how Given/When/Then criteria become automated tests. You don't need to automate your criteria (the agent writes the code), but seeing how a criterion becomes a test sharpens your instinct for what "testable" really means. ISBN 978-1680502381.

---

← [Day 3 — The Project Constitution](day-03-project-constitution) &nbsp;|&nbsp; [Day 5 — Plan → Implement → Verify →](day-05-plan-implement-verify)
