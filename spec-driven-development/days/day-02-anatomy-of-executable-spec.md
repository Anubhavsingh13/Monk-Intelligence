*[Spec-Driven Development](../../README.md) · Day 2 of 7*

# Day 2 — Anatomy of an Executable Spec

> **Today's one idea:** Every spec a coding agent can act on has exactly four layers — context, goal, constraints, and acceptance criteria — and a spec missing any one of them will fail in a predictable way.
> **Reading time:** ~35 min · **Prereqs:** [Day 1](day-01-spec-agent-contract.md)
> **Primary source for today:** *Shape Up*, Ryan Singer, Basecamp, 2019 — Chapter 3 ("Shaping"). Free at basecamp.com/shapeup/chapters/03-shaping.

---

## The hook

Three words that sound like a spec:

> *"Fix the login."*

Which login page? The main login? The SSO login? The mobile app login?

Fix *what*? The styling? The error message? The authentication logic? The redirect after login?

Fix it *how*? Rewrite it? Patch the bug? Add a fallback? Change the library?

Fix it for *whom*? All users? Enterprise users on SAML? Users on the legacy plan?

"Fix the login" has at least 40 valid interpretations. Every one of them is a reasonable reading of those three words. A coding agent will pick one confidently and build it completely. It will not tell you which one it picked until you read the code.

This is not because the agent is careless. It is because your spec gave it 40 degrees of freedom and it had to close all of them somehow.

Today you learn the four-layer structure that closes the right degrees of freedom — without turning your spec into a 20-page requirements document.

---

## Building the intuition

Imagine you are hiring a skilled contractor to renovate your kitchen. You could say "make it look modern" (3 degrees of freedom per square foot). Or you could say: "we have a 1960s bungalow, the kitchen is 12×10 feet, we want white cabinets and quartz countertops, we do not want to move any walls, and we're done when the dishwasher is installed and everything passes inspection." 

The second version is not a floor plan. It is not a blueprint. It is not a list of every screw. It is a structured brief that tells the contractor: here is the world, here is the outcome, here are the walls you cannot knock down, and here is how we'll know it's done.

A spec for a coding agent has the same four parts:

**Layer 1: Context** — *Here is the world.*
What does the agent need to know about the existing system to make this change correctly? The relevant file, the existing pattern it should follow, the part of the codebase it is touching.

**Layer 2: Goal** — *Here is the outcome.*
The single user-visible behavior change you want. Not the implementation method — the result. "The user can filter the list by typing" is a goal. "Add a useState hook that filters the array" is an implementation instruction (don't do this).

**Layer 3: Constraints** — *Here are the walls you cannot knock down.*
What the agent must not do. What it must not change. What it must stay consistent with. Every constraint is a rabbit hole closed.

**Layer 4: Acceptance criteria** — *Here is how we know it is done.*
Three to five statements, each one either true or false. No "it should look good." No "it should be fast." Given/When/Then format.

A spec missing any one of these layers fails in a specific, predictable way:
- Missing context → agent changes the wrong thing or uses the wrong pattern
- Missing goal → agent optimizes for the wrong outcome
- Missing constraints → agent introduces changes you didn't want
- Missing acceptance criteria → neither you nor the agent knows if it worked

---

## The formal picture

Let's build a complete spec, layer by layer, for a real feature: **"Add search to the admin user list page."**

---

### Layer 1: Context

Context answers: *What does the agent need to know about the state of the world right now?*

Good context is specific and bounded. It points at the relevant files. It describes the existing pattern to follow. It does not narrate the entire codebase.

```markdown
## Context

The admin panel is a React + TypeScript frontend (src/admin/).
The user list page is at src/admin/pages/UserListPage.tsx.
It fetches users from GET /api/admin/users and renders them in a 
<DataTable> component (src/admin/components/DataTable.tsx).

The Products page (src/admin/pages/ProductListPage.tsx) has a working
search box that filters client-side using a controlled input and 
Array.filter(). Follow that pattern exactly.
```

Notice what this does: it tells the agent which file to touch, which component to use, and which existing pattern to follow. The agent no longer needs to decide — it has been told.

---

### Layer 2: Goal

Goal answers: *What is the single user-visible outcome?*

State the outcome. Not the mechanism.

```markdown
## Goal

An admin can type in a search box on the user list page and see the
list filtered in real time to show only users whose name or email
contains the search string (case-insensitive).
```

One sentence. One behavior. The agent handles the implementation.

---

### Layer 3: Constraints

Constraints answer: *What must not change?*

Every line here is a degree of freedom you are explicitly closing. This section prevents scope creep and rabbit-hole implementations.

```markdown
## Constraints

- Do not add any new npm packages. Use only existing project dependencies.
- Do not change the /api/admin/users endpoint or any backend code.
- Search must be client-side only (filter the already-fetched list).
- Do not change the DataTable component itself; only change UserListPage.tsx.
- Match the styling of the search box on ProductListPage.tsx exactly.
```

Notice the pattern: each constraint is a "do not" or a "must." Constraints are negative requirements. They bound the solution space without prescribing the solution.

---

### Layer 4: Acceptance criteria

Acceptance criteria answer: *How do we know it worked?*

Each criterion is a Given/When/Then statement that is either true or false. No ambiguity. No subjectivity.

```markdown
## Acceptance criteria

- [ ] Given the user list page is open, when the admin types "alice" 
      into the search box, then only users whose name or email 
      contains "alice" (case-insensitive) are shown in the list.

- [ ] Given the search box contains text, when the admin clears the 
      search box, then all users are shown again.

- [ ] Given no users match the search term, when the admin types a 
      string that matches no user, then the list shows a "No users 
      found" message instead of an empty table.

- [ ] Given the page is loaded, when no search has been entered, 
      then all users are shown (the search box is empty by default).
```

Four criteria. Each one is a test case you can check manually in 30 seconds. If all four are true, the feature is done.

---

### The complete spec

```markdown
# Feature: Search on Admin User List

## Context
The admin panel is a React + TypeScript frontend (src/admin/).
The user list page is at src/admin/pages/UserListPage.tsx.
It fetches users from GET /api/admin/users and renders them in a 
<DataTable> component (src/admin/components/DataTable.tsx).
The Products page (src/admin/pages/ProductListPage.tsx) has a working
search box that filters client-side. Follow that pattern exactly.

## Goal
An admin can type in a search box and see the user list filtered in 
real time to show only users whose name or email contains the search 
string (case-insensitive).

## Constraints
- Do not add any new npm packages.
- Do not change the /api/admin/users endpoint or backend code.
- Search must be client-side only.
- Do not change the DataTable component; only change UserListPage.tsx.
- Match the styling of the search box on ProductListPage.tsx.

## Acceptance criteria
- [ ] Typing "alice" shows only users matching "alice" (case-insensitive).
- [ ] Clearing the search box restores the full user list.
- [ ] A non-matching search term shows "No users found".
- [ ] On page load with no search, all users are shown.
```

This spec fits on one page. It takes 10 minutes to write. It closes the degrees of freedom that matter while leaving the implementation to the agent.

---

## Where it breaks / what it is not

**"More constraints = better spec."**
No. A 20-line constraints section is a sign you are micromanaging the implementation. Constrain the *what*, not the *how*. If you find yourself writing "use a useState hook at line 42," you have gone too far. The agent is better at implementation decisions than you are — let it make them.

**"More acceptance criteria = safer spec."**
Not past 5–6. Each criterion should test a distinct behavior. If two criteria test the same behavior differently, cut one. Three sharp criteria beat ten fuzzy ones every time.

**"What if I don't know the file paths?"**
Ask the agent first: "Read the codebase and tell me which file contains the user list page and which existing component has a search box I could model." Use that answer to write your context layer. The agent is excellent at codebase navigation — use it for that, then write your spec.

**"Should I write the acceptance criteria before or after the goal?"**
Write acceptance criteria *first*. If you cannot write three testable criteria, you do not know what you want yet. The acceptance criteria are the real spec. The context, goal, and constraints exist to help the agent satisfy them.

---

## Try it yourself

### Exercise 1 — Diagnose a broken spec
Here is a real-looking spec. Identify which layer is missing or broken:

> *"Add a notification system to the app. Users should get notified when something happens. Make sure it works well on mobile."*

What layer is missing? What would happen if you gave this to a coding agent?

### Exercise 2 — Write the context layer
For a feature you are actually working on, write the context layer only. Include: the relevant file(s), the existing pattern to follow, and any component the agent will be touching. Stop before writing the goal.

### Exercise 3 — Write acceptance criteria first
For the same feature: write three acceptance criteria in Given/When/Then format before writing anything else. If you can't write three, you don't know what you want yet — that is valuable information.

<details>
<summary>Answer for Exercise 1</summary>

The missing layers are **constraints** and **acceptance criteria**.

- "Notification system" is the goal, roughly — but it's extremely vague. What triggers a notification? What does the notification look like? How is it delivered (in-app badge, email, push)?
- "When something happens" is unusable context — the agent will choose what "something" means.
- "Works well on mobile" is not an acceptance criterion. It's a quality attribute without a testable threshold.

If you gave this spec to a coding agent, it would produce a complete, fully functional notification system — probably using a library you didn't want, for events you didn't have in mind, with mobile behavior it defined itself. It would be technically impressive and contextually wrong.

**What the spec needs:** 1–2 specific trigger events ("when a new order is placed"), one delivery method ("in-app toast notification"), and two acceptance criteria ("Given an order is placed, when the buyer is logged in, then a toast appears with the order number and a link to the order page").
</details>

---

## Connect it back

Yesterday you learned that the agent is a function and ambiguity in your input produces ambiguity in its output. Today you have the structure: four layers that close the right degrees of freedom.

The question you should now be able to answer: *If the agent produces wrong output, which layer of the spec was missing or ambiguous?*

**Tomorrow (Day 3)** you'll zoom out from a single feature spec to the project level. The four-layer structure works for one feature — but when you're building a product with many features, writing context from scratch every time is wasteful and error-prone. There is a better way: one document that all feature specs inherit from.

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
Singer, *Shape Up*, Chapter 3 ("Shaping"), basecamp.com/shapeup/chapters/03-shaping. Focus on the "fat marker sketch" concept — Singer's argument that specs should be concrete enough to remove uncertainty but abstract enough to leave implementation decisions to the builder. This is the same principle as the four-layer structure, from a product design perspective.

**If you want the deep version:**

- Wynne & Hellesøy, *The Cucumber Book*, 2nd ed., Pragmatic Bookshelf, 2017. Chapter 2 ("First Scenario") and Chapter 3 ("Gherkin"). The clearest explanation of Given/When/Then as a discipline, not just a syntax. Understanding *why* the format works makes it much easier to write good acceptance criteria. ISBN 978-1680502381.

- Singer, *Shape Up*, Chapter 5 ("Writing the Pitch"), basecamp.com/shapeup/chapters/05-writing-the-pitch. The full shaped pitch format — a preview of Day 4's feature spec template, with richer examples.

---

← [Day 1 — The Spec-Agent Contract](day-01-spec-agent-contract) &nbsp;|&nbsp; [Day 3 — The Project Constitution →](day-03-project-constitution)
