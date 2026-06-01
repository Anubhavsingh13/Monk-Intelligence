*[Decision Science & Game Theory](../../../../README.md) · [Module 1 — The Science of Choosing](../1-overview.md) · Day 1 of 65*

# Day 1 — How Decisions Actually Happen

> **Today's one idea:** Two parallel systems drive every choice — fast pattern-matching and slow deliberation — and they routinely conflict.
> **Reading time:** ~35 min · **Prereqs:** None
> **Primary source for today:** Kahneman, Daniel. *Thinking, Fast and Slow*. Farrar, Straus and Giroux, 2011. Chapters 1–3.

---

## The Hook

You are in a meeting. A vendor is pitching their API pricing model. The numbers add up, the slides are polished, the person is confident and warm. Something feels wrong. You cannot say what. You smile, take notes, say you will think about it.

Three days later, reading their docs at midnight, you find it: a clause that would have locked you into a pricing tier with no exit ramp. Your gut fired three days before your conscious mind caught up.

Question: Was that instinct reliable? Should you trust it next time?

The answer — which we will spend 13 days establishing — is: *sometimes yes, often no, and the difference is learnable.* But before you can learn it, you need a model of what is actually happening inside your head when you decide.

---

## Building the Intuition

Your brain does not work like a courtroom — gathering evidence, deliberating, then rendering a verdict. It works more like two people sharing a body, taking turns at the wheel.

**Person 1** drives most of the time. They are fast, automatic, and extraordinarily good at pattern-matching on familiar situations. They recognise faces instantly, complete the sentence "bread and ..." without effort, and swerve before they consciously decide to swerve. They are also wildly overconfident, easily fooled by vivid stories, and nearly useless at probability. They never sleep.

**Person 2** is the deliberate one. They can do arithmetic, reason about hypotheticals, hold several things in mind at once, and override Person 1's snap judgements. But they are lazy, expensive to run, and get tired quickly. They only show up when asked — and Person 1 usually does not bother asking.

Here is the key insight: **most decisions feel like Person 2's work but are actually Person 1's.** Person 1 proposes an answer. Person 2 endorses it without really checking. The feeling of having "thought it through" is often Person 1 narrating a conclusion they had already reached.

Let's make this concrete with three quick examples before we put any names on it.

---

**Example 1 — The bat and ball**

A bat and a ball cost $1.10 together. The bat costs $1.00 more than the ball. How much does the ball cost?

Person 1 says: *10 cents*. Instantly. Confidently.

Person 2, if you bother to wake them: Let ball cost = *x*. Then bat costs *x + 1.00*. So:

```
x + (x + 1.00) = 1.10
2x = 0.10
x = 0.05
```

The answer is **5 cents**. More than 50% of MIT undergrads in one study answered 10 cents — not because they lack arithmetic but because Person 1 was so fast and confident that Person 2 never got summoned.

---

**Example 2 — The hiring interview**

You meet a candidate. They are well-spoken, make eye contact, have a firm handshake. Person 1 has already decided within 90 seconds. Person 2 now has the job of "evaluating" them — reading the resume, asking questions — but the verdict was in before the first question. Person 2 is doing post-hoc justification, not evaluation.

This is not a flaw unique to bad interviewers. Research by psychologist Nalini Ambady shows that 30-second silent video clips of teachers predict end-of-semester student evaluations almost as accurately as the full semester of teaching. We are extraordinarily good at thin-slicing. And we cannot tell the difference between a reliable thin-slice and a prejudice.

---

**Example 3 — The unease before clicking "Deploy"**

You are about to push a major change to production. Something feels off. This is Person 1 firing on a pattern — they may have noticed an unusual log message, a config value that didn't match, a test coverage gap in the diff. This time, listen. This is Person 1 doing what they are genuinely good at: noticing pattern violations in a domain they know well.

The lesson: Person 1's instincts are most reliable in *high-familiarity domains with tight feedback loops* — firefighting, chess, production systems you have run for years. They are least reliable in *low-familiarity domains, probabilistic reasoning, and novel situations* — exactly the domains where consequential decisions get made.

---

Here is the architecture in one diagram:

```
┌──────────────────────────────────────────────────────────────┐
│                        THE DECIDING MIND                     │
│                                                              │
│   ┌─────────────────────┐       ┌─────────────────────────┐  │
│   │      SYSTEM 1       │       │       SYSTEM 2          │  │
│   │                     │       │                         │  │
│   │  Fast               │       │  Slow                   │  │
│   │  Automatic          │──────▶│  Deliberate             │  │
│   │  Always running     │proposes│  Effortful              │  │
│   │  Pattern-matching   │answer │  Limited capacity       │  │
│   │  Emotionally tuned  │       │  Rule-based             │  │
│   │  Heuristic-driven   │       │  Interruptible          │  │
│   └─────────────────────┘       └─────────────────────────┘  │
│            ▲                              │                   │
│            │   usually endorses ─────────┘                   │
│            │                                                  │
│            └──────────── OUTPUT: The decision ───────────────┘
│                     (feels deliberate; often isn't)           │
└──────────────────────────────────────────────────────────────┘
```

The arrow from System 2 back to System 1 is the override path — it exists but is rarely used.

---

## The Formal Picture

Psychologists call this **dual-process theory** (named by Keith Stanovich and Richard West in 2000, popularised by Kahneman in 2011). You may also see it called Type 1 / Type 2 processing.

**System 1 — your fast, pattern-matching mind**
- Runs automatically, below conscious awareness
- Uses mental shortcuts — cheap and usually accurate, but fails in *systematic, predictable* ways
- Never switches off; always generating an answer
- Strongest at: familiar situations, recognising patterns, emotional responses

**System 2 — your slow, deliberate mind**
- Operates consciously, one step at a time
- Follows rules, does careful calculation, can override System 1 — but only when it's actually engaged
- Tires quickly; stress and distraction drain it fast
- Strongest at: new problems, probability, planning, resisting impulse

**Three situations where things go wrong:**

| Situation | What happens | Everyday example |
|-----------|-------------|---------|
| Time pressure | System 2 never gets involved | Snapping at a colleague in a tense meeting |
| Mental overload | System 2 runs at reduced capacity | Making worse money decisions when you're stressed |
| Something feels obvious | System 1's confidence shuts System 2 down | Believing a plausible-sounding claim because it's stated confidently |

**One important note:** "System 1" and "System 2" are labels of convenience, not actual brain regions. They describe *how* you're thinking, not *where* in your brain. The distinction is real; the names are just shorthand.

---

## Where It Breaks / What It Is Not

**Misconception 1: "System 1 is the enemy."**

No. System 1 is the seat of expert intuition. A chess grandmaster's "feeling" that a position is dangerous is System 1 running pattern recognition over 100,000 stored positions. A firefighter's instinct to order evacuation seconds before a flashover is System 1 detecting structural cues System 2 could not articulate in time. The problem is not System 1 — it is applying System 1 *outside the domain where it was trained*, especially in abstract or probabilistic situations where no tight feedback loop exists.

**Misconception 2: "I can just engage System 2 when it matters."**

The insidious part: System 2 does not know when System 1 has made an error. System 1 delivers its answer with a feeling of confidence — no warning label attached. System 2 then searches for reasons to endorse the conclusion rather than testing it. You cannot will your way into better decisions just by "trying harder." You need structural interventions — checklists, decision journals, explicitly asking "what would have to be true for me to be wrong?" These are System 2 scaffolds.

**Misconception 3: "Experts are immune."**

Kahneman co-won the Nobel Prize in Economics partly by demonstrating this was false. Expert radiologists, experienced judges, and veteran investors all show systematic biases that expertise does not erase. Domain expertise improves System 1 accuracy — but only in the domain, and only when the environment provides regular, unambiguous feedback. Stock-pickers do not get this feedback. Radiologists reading the same scan repeatedly do.

**Misconception 4: "This is psychology, not product design."**

It is the most consequential product design principle in existence. Every default setting, progress bar, notification badge, "most popular" label, and AI recommendation is exploiting System 1. Every onboarding flow competes for System 1 attention. If you are building AI products that shape millions of decisions, understanding this architecture is not optional — it is the foundation of ethical and effective design. We will return to this on Day 53 with the formal theory of nudge architecture.

---

## Try It Yourself

**Exercise 1 — Catch your own System 1 (5 min)**

Answer each question *immediately*, before calculating:

a) A car travels 60 km at 40 km/h, then another 60 km at 60 km/h. What is the average speed for the whole journey?

b) A study finds that 90% of car accidents happen within 25 miles of home. Does this mean driving near home is more dangerous per kilometre than driving far away?

c) You flip a fair coin 5 times and get H, H, H, H, H. What is the probability the 6th flip is Heads?

Now work out each answer carefully. Where did System 1 go wrong?

<details>
<summary>Hints</summary>

a) Average speed is not the arithmetic mean of two speeds. Use: total distance ÷ total time.

b) Think about where most driving actually happens.

c) A fair coin has no memory of previous flips.

</details>

<details>
<summary>Worked answers</summary>

**a)** Total distance = 120 km. Time for leg 1 = 60 ÷ 40 = 1.5 hr. Time for leg 2 = 60 ÷ 60 = 1.0 hr. Total time = 2.5 hr.

Average speed = 120 ÷ 2.5 = **48 km/h**, not 50.

System 1 averaged the speeds directly — which is only valid if you spend equal *time* at each speed, not equal *distance*. This is the *denominator neglect* bias.

**b)** No. Most driving happens close to home. The statistic tells you nothing about risk per kilometre — it just reflects where people spend driving time. This is *base rate neglect*: System 1 ignored the denominator (kilometres driven near home) and focused on the raw number of accidents.

**c)** **50%.** A fair coin has no memory. Each flip is independent: P(H) = 0.5 always. System 1 falls for the *gambler's fallacy* — the intuition that a streak of Heads "should" be balanced by Tails. The coin does not know what happened before.

</details>

---

**Exercise 2 — Audit a past decision (10 min)**

Think of a significant decision you made in the last 6 months — a career move, a product call, a hire, a financial decision. Write down (physically — writing engages System 2 in a way that thinking does not):

1. What did System 1 tell you immediately when you first encountered the decision?
2. What did you tell yourself was your "reasoned" justification?
3. In hindsight: did System 2 actually evaluate the options, or did it construct a post-hoc rationale for what System 1 had already decided?

You do not need to share this. The value is in the noticing.

---

**Exercise 3 — Design a System 1 exploit (10 min, L2)**

You are designing an onboarding flow for a productivity app. Goal: get users to enable daily notifications.

Sketch two versions:
- **Version A:** Plain settings screen, "Enable notifications" toggle, off by default.
- **Version B:** An onboarding screen that uses System 1 mechanics.

For Version B, list three specific design choices that use System 1 — and for each, mark it as **Nudge** (alters choice architecture without restricting options) or **Manipulation** (exploits a bias in a way the user would object to if they understood it).

*(We will return to this with formal theory on Day 53.)*

---

## Connect It Back

Today you met the two characters who will appear throughout this course. System 1 is the mechanism behind every cognitive bias in Days 6–7, and the reason Prospect Theory (Day 8) describes actual behaviour better than Expected Utility. System 2 is the machinery that Bayesian updating (Day 5) requires — deliberate, step-by-step belief revision. Every tool in Module 1 is, in some sense, a System 2 scaffold: a structure that forces the deliberate processing that leads to better decisions.

Tomorrow we build System 2's primary tool: **expected value** — the rational baseline against which all of human decision-making is measured. Not because expected value is always the right answer (it isn't), but because you cannot understand where humans go wrong without first knowing what "right" looks like.

**The question you should be able to answer now that you could not this morning:**
*Why does the feeling of having "thought something through" not guarantee that System 2 was actually in charge?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Kahneman, *Thinking, Fast and Slow*, Chapters 1–3 ("The Characters of the Story," "Attention and Effort," "The Lazy Controller"). Primary source for today's framework, with richer examples and the original experimental evidence. ~25 pages.

**If you want the deep version:**

1. Stanovich, Keith E. & West, Richard F. "Individual differences in reasoning: Implications for the rationality debate." *Behavioral and Brain Sciences*, 23(5):645–665, 2000. — The paper that named System 1 and System 2. Read the abstract and Section 2; the full paper is long but the framing sections pay off for L2 readers.

2. Kahneman, *Thinking, Fast and Slow*, Chapter 12–13 ("The Science of Availability," "Availability, Emotion, and Risk"). — A preview of Days 6–7 for readers whose curiosity is running ahead. These chapters show System 1's availability heuristic in action.

3. Thaler & Sunstein, *Nudge*, Chapter 1 ("Biases and Blunders"). — A 20-minute read that immediately bridges today's framework to product design. Shows how dual-process theory becomes the foundation of choice architecture — the theme of Day 53.


---

[Day 2 — Expected Value →](day-02-expected-value)
