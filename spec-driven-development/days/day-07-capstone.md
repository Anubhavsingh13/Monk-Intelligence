*[Spec-Driven Development](../../README.md) · Day 7 of 7*

# Day 7 — Capstone: Ship a Feature

> **Today's one idea:** Everything you have learned becomes real only when you do it — write a constitution, write a spec, run the loop, push through CI, deploy.
> **Reading time + doing time:** ~45 min · **Prereqs:** [Days 1–6](../README.md)
> **Primary source for today:** Your own project. All prior days.

---

## The hook

You have spent six days learning a workflow. You have a mental model (Day 1), a spec anatomy (Day 2), a constitution template (Day 3), a feature spec template (Day 4), a three-beat loop (Day 5), and a pipeline structure (Day 6).

There is exactly one thing left: do it.

Today is not a reading day. It is a doing day. The reading is minimal. The exercises are the point.

By the end of today you will have:
- A project constitution in `CLAUDE.md`
- A feature spec in `specs/`
- A feature implemented by a coding agent
- All acceptance criteria verified
- Code pushed through CI
- Something deployed

"Done" is concrete: all acceptance criteria pass, CI is green, and you could hand your `CLAUDE.md` + spec + pipeline config to a colleague and they could reproduce the result from scratch without asking you a single question.

---

## Before you begin: the brief review

Spend 5 minutes re-reading these two things (do this now, before the exercises):

1. Your Day 3 notes or the constitution template — refresh the five sections in your mind.
2. The Day 4 spec template — refresh the five sections (appetite, problem, sketch, no-gos, criteria).

That is your rest-and-synthesize moment for this course. Five minutes. Then proceed.

---

## The capstone workflow

Work through these steps in order. Each step has a time budget. If a step takes significantly longer, that is diagnostic — it reveals a gap in your spec or constitution that is worth understanding before moving on.

---

### Step 1 — Choose your feature (5 min)

Pick one of these:

**Option A — A real feature** from a project you own, work on, or are building. Must be scoped to "Small" appetite (a few hours of agent work). If you're not sure, ask yourself: could a senior developer build this in half a day? If yes, it's scoped correctly.

**Option B — A practice project** if you don't have a real one. Here are three well-scoped options:
- A todo list app: add "due date" to tasks, with sorting by due date
- A recipe app: add a search box to filter recipes by ingredient
- A personal finance tracker: add CSV export for a transactions table

**Option C — Extend the running example** from this course: take the TaskFlow project from Day 3, the search feature from Day 2, and the CSV export from Days 4–5. Wire them together and add one new feature (e.g., email notifications when a task is assigned).

Whatever you pick, write it down as one sentence before moving to Step 2.

---

### Step 2 — Write the project constitution (15 min)

Create `CLAUDE.md` in your project root. Use the template from Day 3.

If you already have a CLAUDE.md from earlier exercises, update it. If not, build it from scratch.

**Minimum viable constitution (for today's capstone):**

```markdown
# Project Constitution: [Your Project Name]

## Mission
[1–2 sentences]

## Tech stack
### In use
- [List your actual stack]

### Not in scope
- [At least 3 explicit exclusions]

## Conventions
### File structure
[2–3 rules that actually affect code generation]

### Patterns
[1–2 patterns the agent should follow, with file path examples]

## Current roadmap
### Completed
- [x] [anything already built]

### In progress
- [ ] [Today's feature] (see specs/[filename].md)

## Known constraints
- [Any accessibility, security, or performance rules]

## Hosting
- [Where this deploys and how]
```

**Constitution quality check:** Read it once after writing. Ask: if a competent developer joined this project today and read only this document, would they know what tech to use, what patterns to follow, and what not to do? If yes, it's ready.

---

### Step 3 — Write the feature spec (10 min)

Create `specs/[feature-name].md`. Use the template from Day 4.

Write the sections **in this order** (reverse of how they appear):
1. Acceptance criteria first (if you can't write 3, stop and think more)
2. No-gos second
3. Solution sketch third
4. Problem statement fourth
5. Appetite last

**Spec quality check:**
- [ ] I can explain each acceptance criterion in one sentence and it is unambiguous
- [ ] My no-gos list has at least 3 items
- [ ] The solution sketch describes user-visible behavior, not implementation
- [ ] The appetite is set (Small / Medium / Large)

---

### Step 4 — Run Beat 1: Plan (5 min)

Open your coding agent. Run this prompt (adjust file paths):

```
Read CLAUDE.md and specs/[your-feature].md carefully.

Before writing any code, produce a step-by-step implementation plan:
- Number each step
- For each step: which file you will change, what the change is, 
  and which acceptance criterion it satisfies
- List any assumptions you are making

Do not write any code yet.
```

Read the plan. For each step, ask:
- Does this step violate a no-go?
- Does every acceptance criterion appear in at least one step?
- Are the assumptions reasonable?

If anything is wrong: correct it now, before any code is written.

---

### Step 5 — Run Beat 2: Implement (watch + intervene if needed)

Once the plan is approved, run:

```
The plan looks good. Implement it exactly as described.

If you encounter anything unexpected — a missing file, a type mismatch, 
a pattern that differs from what the plan assumed — stop and tell me 
before changing the plan.
```

Let the agent work. Do not add new requirements. If it stops and asks a question, answer it and let it continue.

---

### Step 6 — Run Beat 3: Verify (5 min)

```
Implementation complete. Go through each acceptance criterion in 
specs/[your-feature].md one by one.

For each: state whether it is satisfied, and how you verified it 
(test result, code trace, or manual check).
```

For each criterion the agent marks ✓: spot-check it yourself. Open the app (if possible) and test it manually. Don't trust the agent's self-assessment alone.

If a criterion fails: write a tiny spec for the gap (two lines: what failed, what the fix is), run the loop again for that gap only.

---

### Step 7 — Commit and push (2 min)

```bash
git checkout -b feature/[your-feature-name]
git add .
git commit -m "feat: [your feature name]

Implements specs/[your-feature].md.
All acceptance criteria verified."
git push origin feature/[your-feature-name]
```

Open a pull request. Add a description that links to the spec: "See `specs/[feature].md` for acceptance criteria."

---

### Step 8 — Watch CI (2 min)

Open your CI dashboard (GitHub Actions → your repo → Actions tab).

Watch the pipeline run. If it passes: move to Step 9.

If it fails:
1. Read the error message
2. Ask the agent: "CI is failing with this error: [paste error]. What is the fix?" — this is a new mini-spec
3. Fix, commit, push again
4. Repeat until green

A first-time CI failure is normal and instructive. The most common causes:
- Test that the agent wrote is wrong (fix the test or the code)
- Missing environment variable in CI (add to GitHub Secrets)
- Dependency not committed (check `.gitignore`)

---

### Step 9 — Review on preview + deploy (varies)

If you have a preview deployment (Vercel, Railway preview): open the preview URL and test the feature yourself against the acceptance criteria one final time.

If you are deploying to production: merge the PR to main.

If this is a practice project with no deployment: "deployed" means CI is green and you have a merged PR. That counts.

---

### Step 10 — Update the constitution (2 min)

Open `CLAUDE.md`. Update the roadmap:

```markdown
### Completed
- [x] [Your feature] ← add this

### In progress
- [ ] [Next feature, if you know it]
```

Move `specs/[feature].md` to `specs/done/[feature].md`.

You have shipped a feature using spec-driven development.

---

## Reflection questions

After you finish, spend 5 minutes writing answers to these (in a note, a Notion doc, anywhere):

1. **Which part of the spec was ambiguous?** The plan step will have revealed at least one place where the agent's interpretation differed from your intent. What was it? How would you write that part of the spec differently?

2. **What would you add to the no-gos section?** There was almost certainly one thing the agent built (or started to build) that you didn't want. What was it? Add it retroactively to your spec as a learning artifact.

3. **Does your constitution need updating?** After building one feature, you probably learned something about your project's conventions that wasn't in the original constitution. What is it? Add it.

4. **How long did it take?** Break it down: constitution writing, spec writing, plan review, implementation watch, verify, CI, deploy. Note which steps took longer than expected.

These four questions are not homework. They are the feedback loop that makes the second feature faster than the first, and the tenth feature faster than the second.

---

## The "done" checklist

You have completed the capstone when all of these are true:

- [ ] `CLAUDE.md` exists in the project root and has all five sections
- [ ] `specs/[feature].md` exists and has all five sections
- [ ] All acceptance criteria are checked ✓ by you (not just by the agent)
- [ ] CI is green (tests pass, build succeeds)
- [ ] The feature is deployed (or the PR is merged to main)
- [ ] `CLAUDE.md` roadmap is updated
- [ ] Spec is archived to `specs/done/`
- [ ] You could hand this repo to a colleague right now and they could run the next feature without asking you anything

If any item is unchecked, it is not done. Each unchecked item tells you exactly what to fix.

---

## What comes next

You have completed the course. Here is what spec-driven development looks like at steady state:

**Per feature (ongoing):**
- 10 min: write the spec
- 5 min: review the plan
- 5 min: verify the output
- CI and deploy: automated

**Per sprint (or equivalent):**
- 15 min: update the constitution roadmap
- Archive completed specs

**Per quarter:**
- Review the constitution for staleness. Has the tech stack changed? Have conventions drifted? Update it.

The workflow gets faster as the constitution gets richer. By your fifth feature, the spec takes 5 minutes and the plan is almost always right on the first try. By your tenth, you have a library of past specs to reference when writing new ones.

---

## Where to go from here

**If you want to go deeper on spec writing:**
Singer, *Shape Up*, Chapters 3–5. The full shaped pitch methodology, including how Basecamp decides what to build in each cycle. Directly applicable to writing better feature specs.

**If you want to go deeper on the pipeline:**
Humble & Farley, *Continuous Delivery*, Chapters 5–8. Automated acceptance testing, deployment pipelines, and zero-downtime deployments. The engineering foundation for a more robust version of Day 6's pipeline.

**If you want to package your workflow:**
Paul Everitt, DeepLearning.AI course, Lesson 9 ("Agent Skills"). How to package your plan-implement-verify prompts as reusable skills so you type them once, not every feature.

**If you want to apply this to a legacy codebase:**
Paul Everitt, DeepLearning.AI course, Lesson 8 ("Legacy Codebases"). The constitution additions for brownfield projects, and how to scope feature specs so they don't accidentally trigger large refactors.

---

## A final note

The hardest part of spec-driven development is not writing the spec. It is the discipline to write the spec *before* you talk to the agent.

The temptation is always to just start a conversation. "What if I just ask it to take a look at the codebase and suggest an approach?" That is vibe coding with extra steps. The conversation feels productive. The output is unpredictable.

The spec takes 10 minutes. It feels slower. It is faster — measured from "idea" to "shipped and verified," not from "first keystroke" to "code generated."

Write the spec first. Always.

---

## Suggested readings for today

**Required if you have 15 extra minutes:**
Singer, *Shape Up*, Chapter 7 ("Bets, Not Backlogs"), basecamp.com/shapeup/chapters/07-bets-not-backlogs. The argument for why shaped specs (not groomed backlogs) should be the input to a development cycle. Relevant if you want to integrate spec-driven development into a team workflow rather than a solo one.

**If you want the deep version:**

- Paul Everitt, DeepLearning.AI course, Lesson 10 ("Building an MVP"). How to sequence multiple feature specs to ship a minimal viable product. The constitution roadmap becomes a shipping plan. learn.deeplearning.ai/courses/spec-driven-development-with-coding-agents.

- Humble & Farley, *Continuous Delivery*, Chapter 1 ("The Problem of Delivering Software"). A sharp articulation of why manual deployment processes fail and what automation solves. Read this if your Day 6 pipeline setup revealed pain points you want to understand more deeply.

---

← [Day 6 — The Full Pipeline](day-06-full-pipeline) &nbsp;|&nbsp; [↑ Back to Course Home](README)
