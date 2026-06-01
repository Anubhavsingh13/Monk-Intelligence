*[Spec-Driven Development](../../README.md) · Day 3 of 7*

# Day 3 — The Project Constitution

> **Today's one idea:** Before writing a single feature spec, you need one persistent document — mission, tech stack, conventions, roadmap — that all feature specs inherit from, so you never repeat yourself and agents never drift.
> **Reading time:** ~35 min · **Prereqs:** [Day 1](day-01-spec-agent-contract.md), [Day 2](day-02-anatomy-of-executable-spec.md)
> **Primary source for today:** Spec-Driven Development with Coding Agents, Paul Everitt, DeepLearning.AI, Lesson 3. learn.deeplearning.ai/courses/spec-driven-development-with-coding-agents

---

## The hook

You write a perfect four-layer spec on Day 2. The context is specific, the goal is clear, the constraints are tight, the acceptance criteria are testable. You give it to your coding agent. It produces exactly what you asked for — in Vue.js.

Your project uses React.

The agent didn't know. You didn't tell it. It saw a frontend project with a component structure that looked somewhat like Vue patterns and made a reasonable choice.

Now imagine this happening not with a framework choice, but with: naming conventions, file structure, testing patterns, state management libraries, authentication approach, API design, accessibility standards. Every feature spec you write has a context layer — but context repeated in every spec is context that drifts, goes stale, and gets omitted when you're in a hurry.

There is a better solution: write the context *once*, at the project level, in a document every feature spec inherits from automatically.

That document is the **project constitution**.

---

## Building the intuition

Think about the employee handbook at a company. When a new employee joins, they read the handbook — once. It tells them: here is the mission, here is how we communicate, here is what tools we use, here is what is expected of you. They do not re-read the handbook before every meeting. But everything they do is shaped by it.

A project constitution is the employee handbook for your coding agent. You write it once, at the start of the project. Every time you start an agent session, you give the agent the constitution. The agent absorbs it and applies it to every feature spec it receives.

The result: your feature specs can be shorter, because they don't repeat what the constitution already defines. Your agent stays consistent across features, because it is always operating from the same foundation. And when you switch agents — from Claude Code to Cursor to a new tool that doesn't exist yet — you hand them the constitution and they are immediately productive.

Paul Everitt (the DeepLearning.AI course) calls this a "project constitution." Anthropic implements it as `CLAUDE.md`. Ryan Singer (Shape Up) calls it the "project environment." The name varies; the idea is identical: **one document that defines the invariants of the project — the things that are true of every feature, not just this one**.

---

## The formal picture

### What belongs in a project constitution

A constitution has five sections. Each one answers a question the agent would otherwise have to guess.

---

**Section 1: Mission**
*What is this product? Who is it for? What problem does it solve?*

One to two sentences. Not marketing copy — operational clarity.

```markdown
## Mission

TaskFlow is a project management tool for small software teams (5–15 people).
It helps teams track features from idea to deployment without the overhead
of enterprise tools like Jira. The primary users are engineering leads
and product managers who want simple kanban boards and automated
status updates.
```

Why this matters: when the agent encounters an edge case not covered by the feature spec, it uses the mission to make the right call. "Should I add analytics tracking to this button?" — the mission says "without overhead," so probably no.

---

**Section 2: Tech stack**
*What technologies are in use? What is explicitly out of scope?*

```markdown
## Tech stack

- Frontend: React 18, TypeScript, Tailwind CSS
- Backend: Node.js 20, Express 5, Prisma ORM
- Database: PostgreSQL 15
- Testing: Vitest (unit), Playwright (end-to-end)
- Hosting: Railway (backend + database), Vercel (frontend)
- CI: GitHub Actions

### Not in scope
- No Vue, Angular, or any other frontend framework
- No new CSS-in-JS libraries (we use Tailwind only)
- No new ORMs or database drivers
- No server-side rendering (frontend is a SPA)
```

The "not in scope" subsection is the most important part. It is the constraints layer at the project level. Every item here is a rabbit hole that does not exist for any feature spec.

---

**Section 3: Conventions**
*How do we name things? How do we structure files? How do we write tests?*

```markdown
## Conventions

### File structure
- Components: src/components/<ComponentName>/<ComponentName>.tsx
- Pages: src/pages/<PageName>Page.tsx
- API routes: src/api/<resource>/<method>.ts
- Tests: co-located with source files as <file>.test.ts

### Naming
- Components: PascalCase
- Functions and variables: camelCase
- Database tables: snake_case
- API endpoints: /api/<plural-noun>/<id>/<sub-resource>

### Patterns
- All API calls go through src/lib/api.ts (never fetch() directly)
- All forms use React Hook Form
- All date handling uses date-fns (never new Date() directly)
- Error boundaries wrap every page component
```

This section prevents the most common class of "technically correct but contextually wrong" output. The agent now knows your patterns without being told in every spec.

---

**Section 4: Current roadmap**
*What are the next 3–5 features in sequence?*

```markdown
## Current roadmap

Completed:
- [x] User authentication (email/password)
- [x] Project creation and basic kanban board

In progress:
- [ ] Feature: Search on user list (see specs/feature-search-user-list.md)

Up next:
- [ ] Feature: CSV export for project reports
- [ ] Feature: Email notifications on task assignment
- [ ] Feature: Team member roles (admin/member/viewer)
```

Why this matters: the agent understands where the current feature fits in the sequence. It won't build the email notification system inside the search feature because it can see that notifications are a separate upcoming item. It won't add "admin/member" role checks to the search feature because it can see that roles are coming later.

Update this section every time you complete a feature and start the next one.

---

**Section 5: Known constraints and standards**
*What project-wide rules apply to every implementation?*

```markdown
## Known constraints and standards

- Accessibility: All interactive elements must have ARIA labels.
  Use semantic HTML. Never use divs for buttons.
- Performance: No synchronous operations on the main thread.
  Paginate any list with more than 100 items.
- Security: Never log user data (emails, names, IDs) to the console.
  All API endpoints require authentication unless explicitly noted.
- Error handling: All async operations are wrapped in try/catch.
  User-visible errors use the <ErrorMessage> component.
```

---

### The complete constitution (template)

```markdown
# Project Constitution: [Project Name]

## Mission
[1–2 sentences: what is this product, who is it for, what problem does it solve]

## Tech stack
### In use
- Frontend: [framework, language, CSS approach]
- Backend: [runtime, framework, ORM]
- Database: [database]
- Testing: [unit framework, e2e framework]
- Hosting: [platform]
- CI: [platform]

### Not in scope
- [Explicit exclusions]

## Conventions
### File structure
[file organization rules]

### Naming
[naming conventions]

### Patterns
[recurring implementation patterns to follow]

## Current roadmap
### Completed
- [x] [feature]

### In progress
- [ ] [feature] (see specs/[filename].md)

### Up next
- [ ] [feature]

## Known constraints and standards
- [Accessibility, performance, security, error handling rules]
```

---

### CLAUDE.md: the constitution in practice

For Claude Code users, the project constitution lives in a file called `CLAUDE.md` in the project root. Claude Code reads this file automatically at the start of every session — you don't need to paste it into every prompt.

```
project-root/
├── CLAUDE.md          ← your project constitution lives here
├── specs/
│   └── feature-search-user-list.md
└── src/
    └── ...
```

The CLAUDE.md format is identical to the constitution template above. When you run Claude Code, it loads CLAUDE.md before doing anything else.

Other agents have equivalent mechanisms: Cursor uses `.cursorrules`, Copilot Workspace uses repository instructions. The file name differs; the purpose is identical.

---

## Where it breaks / what it is not

**"I'll just paste the constitution in every feature spec."**
You can — but you shouldn't. A pasted constitution drifts: different versions get used for different features, and the pasted text is stale as soon as you update the original. Keep the constitution in one place and reference it by path.

**"My constitution is getting long — 5 pages and growing."**
That is a problem. A constitution that is too long gets partially read or ignored by the agent (context window limits). Aim for 1–2 pages. If you have 10 naming conventions, ask: which three actually affect agent behavior? Keep those. Delete the rest.

**"Feature specs in the constitution or separate?"**
Always separate. The constitution is stable — it describes the invariants that are always true. Feature specs are ephemeral — they describe a specific change at a specific time. Once a feature ships, its spec is archived. The constitution remains.

**"What if I'm working on a legacy codebase with inconsistent conventions?"**
Write the constitution to describe the *intended* state, not the current state. Add a section: "Known inconsistencies" that tells the agent which parts of the codebase do not follow conventions and should not be used as patterns.

---

## Try it yourself

### Exercise 1 — Write your tech stack section
For a project you are working on (or a side project, or a tutorial app), write the tech stack section of your constitution. Include both "in use" and "not in scope." The "not in scope" list is more important — take time on it.

### Exercise 2 — Write three naming conventions
Pick three naming conventions that actually affect how code is generated. For each one, give the rule and a 2-line example of what it looks like in your codebase.

### Exercise 3 — The inheritance test
Look at the feature spec you drafted (or imagined) after Day 2. How many lines in the context layer would be eliminated if you had a complete constitution? How much shorter would the spec be?

<details>
<summary>Worked example for Exercise 3</summary>

**Day 2 spec context layer (without a constitution):**

```markdown
## Context
The admin panel is a React + TypeScript frontend (src/admin/).
The user list page is at src/admin/pages/UserListPage.tsx.
It fetches users from GET /api/admin/users and renders them in a 
<DataTable> component (src/admin/components/DataTable.tsx).
The Products page (src/admin/pages/ProductListPage.tsx) has a working
search box that filters client-side. Follow that pattern exactly.
```

**The same spec context layer with a complete constitution:**

```markdown
## Context
See CLAUDE.md for project conventions and tech stack.
The user list page is at src/admin/pages/UserListPage.tsx.
The search pattern to follow is on ProductListPage.tsx.
```

Three lines instead of six. The framework, the file structure, the component pattern — all in the constitution. The spec only specifies what is unique to this feature.
</details>

---

## Connect it back

Yesterday you learned the four-layer anatomy of an executable spec. Today you learned that one of those layers — context — has a project-level version that doesn't belong in every spec. The constitution writes it once; every spec inherits it.

The question you should now be able to answer: *What is the difference between something that goes in the project constitution vs. something that goes in a feature spec?*

If it is always true (tech stack, conventions, mission) → constitution.
If it is true only for this feature, this week → feature spec.

**Tomorrow (Day 4)** you will write the feature spec in full, using the complete template. With the constitution in place, the feature spec becomes short, sharp, and fast to write.

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
Paul Everitt, "Spec-Driven Development with Coding Agents," Lesson 3 ("Project Constitution"), DeepLearning.AI. learn.deeplearning.ai/courses/spec-driven-development-with-coding-agents. Watch the lesson where Everitt builds the constitution from scratch for his demo project. Pay attention to what he puts in vs. what he leaves out — the editorial judgment is the lesson.

**If you want the deep version:**

- Anthropic, "CLAUDE.md guide," docs.anthropic.com/claude-code. The official reference for the CLAUDE.md format. Includes sections on memory, tool permissions, and how Claude Code prioritizes context. Read the "What to put in CLAUDE.md" section specifically.

- Singer, *Shape Up*, "How We Work" companion essays, basecamp.com/how-we-work. The essays on "scope hammering" — how Basecamp decides what NOT to include in a feature — are the product-level version of writing good constraints. Relevant to tomorrow's no-gos section.

---

← [Day 2 — Anatomy of an Executable Spec](day-02-anatomy-of-executable-spec) &nbsp;|&nbsp; [Day 4 — Writing Feature Specs →](day-04-writing-feature-specs)
