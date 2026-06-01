# Learning Path — Spec-Driven Development with Coding Agents

## The rationale

Every day in this course earns exactly one thing the next day needs. The order is not arbitrary.

- **Days 1–2** build the mental model. You cannot write a good spec until you understand why specs fail.
- **Day 3** zooms out to the project level. You cannot write a good feature spec until you have a constitution for the feature to inherit from.
- **Day 4** zooms back in to write the feature spec, now with the full vocabulary from Days 1–3.
- **Day 5** teaches you to run the spec through an agent using a named, repeatable loop.
- **Day 6** wires Days 3–5 into a pipeline so the workflow runs end-to-end without manual steps.
- **Day 7** forces you to do the whole thing for real.

---

## Full path

| Day | Title | Load-Bearing Idea | Why It Comes Now | Prerequisite Pages |
|-----|-------|-------------------|------------------|--------------------|
| [1](days/day-01-spec-agent-contract.md) | The Spec-Agent Contract | A coding agent is a spec-interpreting machine — "vibe coding" fails because ambiguous input produces ambiguous output | Reframes the mental model from "magic AI" to "spec executor" before any technique is introduced | None |
| [2](days/day-02-anatomy-of-executable-spec.md) | Anatomy of an Executable Spec | Every spec a coding agent can act on has four layers: context, goal, constraints, and acceptance criteria | Gives the structural vocabulary you will use to write every spec from Day 3 onward | Day 1 |
| [3](days/day-03-project-constitution.md) | The Project Constitution | Before writing a single feature spec, you need one persistent document — mission, tech stack, conventions — that all feature specs inherit from | Without a constitution, each feature spec repeats context and agents drift across features | Day 2 |
| [4](days/day-04-writing-feature-specs.md) | Writing Feature Specs | A feature spec is a shaped pitch + explicit no-gos + testable acceptance criteria — three sections, nothing else | Builds the day-to-day writing skill on top of the Day 3 foundation | Day 3 |
| [5](days/day-05-plan-implement-verify.md) | Plan → Implement → Verify | The core agent workflow is a three-beat loop: get the agent to produce a plan first, then implement, then verify against your acceptance criteria | Teaches the steering loop as a named, repeatable pattern before wiring it into a pipeline | Day 4 |
| [6](days/day-06-full-pipeline.md) | The Full Pipeline | Spec → constitution → agent → plan-implement-verify → CI → deploy is a single template you fill once and reuse forever; the spec, not the tool, is the durable asset | Connects all prior days into the end-to-end system | Days 1–5 |
| [7](days/day-07-capstone.md) | Capstone: Ship a Feature | Write a project constitution + one feature spec, run the plan-implement-verify loop, push through CI, deploy | Forces full synthesis; the reader earns the outcome they came for | Days 1–6 |

---

## The learning arc

**Day 1 vs. Day 7.** On Day 1 you arrive thinking the hard part is choosing the right AI tool. By Day 3 you understand that the tool barely matters — the spec is the leverage point. By Day 7 you have a repeatable system: a constitution template you fill in once, a spec template you fill per feature, a three-beat conversation pattern with the agent, and a pipeline that takes the output to production.

**The single story.** This course is about one shift: from treating a coding agent as a smart assistant you talk to, to treating it as a deterministic pipeline you feed structured inputs. A PM already knows how to write a good PRD. This course shows you that a great spec *is* the program — you are not describing what you want, you are specifying a contract that an agent executes. Every day adds one more layer of that contract until the full pipeline snaps together on Day 6 and you ship something real on Day 7.
