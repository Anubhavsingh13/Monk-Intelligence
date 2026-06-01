*[Decision Science & Game Theory](../../../../README.md) · [Module 1 — The Science of Choosing](../1-overview.md) · Day 7 of 65*

# Day 7 — Cognitive Biases II: Motivation Traps

> **Today's one idea:** Loss aversion, sunk cost fallacy, and status quo bias warp decisions *after* perception — through the lens of what we stand to lose — and each has a distinct mechanism worth understanding separately.
> **Reading time:** ~35 min · **Prereqs:** [Day 1](day-01-how-decisions-actually-happen.md), [Day 6](day-06-cognitive-biases-i.md)
> **Primary source for today:** Kahneman, *Thinking, Fast and Slow*, Chapters 26–32 (losses, sunk costs, status quo, endowment effect).

---

## The Hook

Imagine two people. Both received identical job offers last month — $120,000 salary, fully remote, great team. Both negotiated.

Person A's base was $100,000. They asked for $130,000. The company countered at $122,000. They took it. They feel they won $2,000.

Person B's base was $140,000. They asked for $150,000. The company countered at $122,000 — identical offer. They turned it down. The number felt like a $18,000 cut.

Same salary. One feels like a win. One feels like a loss. And it changes the decision.

This is loss aversion — not irrationality, but a feature of how humans encode the world. The problem is that this feature, useful in some evolutionary contexts, systematically distorts decisions in ways that are costly.

---

## Building the Intuition

### Bias 1: Loss Aversion

**The core finding (Kahneman & Tversky, 1979):** When people evaluate prospects, losses loom larger than equivalent gains. The disutility of losing $100 is roughly twice the utility of gaining $100. This is not a ratio that varies much across people or cultures — it is remarkably stable.

**A simple demonstration:**

Would you accept this bet: 50% chance to win $150, 50% chance to lose $100?

EV = (0.5 × $150) + (0.5 × −$100) = +$25. This is a positive expected value gamble.

Most people say no. The potential $100 loss feels worse than the potential $150 gain feels good.

To make most people indifferent to a 50/50 bet, the gain side typically needs to be 2–2.5× the loss side. This *loss aversion coefficient* of ~2.25 is remarkably consistent across populations.

**Where it appears:**

- *Product retention:* "You'll lose your streak" is a more effective retention message than "Keep building your streak" — loss frame, same proposition.
- *Feature adoption:* Free trials with credit card required convert better than free trials without. The fear of an accidental charge (loss) drives sign-up more than the benefit of free access.
- *Investor behaviour:* Investors hold losing stocks too long (avoiding realising a loss) and sell winners too early (locking in a gain). This is called the *disposition effect*.
- *Negotiation:* Framing your offer as preventing the other side's loss is more persuasive than emphasising their gain — even when they're equivalent.
- *Career decisions:* People stay in bad jobs longer than they should because leaving means "giving up" what they have built — a loss frame on the transition cost.

---

### Bias 2: Sunk Cost Fallacy

**The core idea:** A sunk cost is an irrecoverable past expenditure. Rational decision theory says: ignore it. Only future costs and future benefits should influence a forward-looking decision.

Humans don't ignore sunk costs. They chase them.

**A clean experiment (Arkes & Blumer, 1985):** Subjects who paid $10 for a ski resort ticket were told the night before that a closer resort was offering better snow. Those who had paid $10 for the original ticket were significantly more likely to go to the worse resort than those who had received the ticket for free.

The $10 was spent either way. Only future enjoyment should matter. But the paid group felt compelled to "get their money's worth."

**Where it appears:**

- *Product development:* "We've already spent 6 months on this feature — we can't kill it now." The 6 months are gone regardless of the next decision. The decision should be made on future value only.
- *Startups:* Founders stay in failing companies longer than rational analysis would dictate because of the months/years invested — a sunk cost.
- *Hiring:* "We spent three months recruiting this person, so let's give them more time to turn around" — the recruiting cost is sunk.
- *Strategic projects:* Governments continue expensive infrastructure projects well past the point where cancellation is economically superior, because of prior expenditure (Concorde, various defence programs).

**The rational corrective:**

Before any decision involving prior investment, explicitly ask: *"If I were starting from scratch today, with no prior commitment, what would I do?"* If the answer differs from what you are about to do, you are being driven by sunk costs.

---

### Bias 3: Status Quo Bias

**The core finding:** People disproportionately prefer the current state of affairs. Changing from the status quo is perceived as a loss, even when the change is objectively neutral or positive.

**The evidence:** Samuelson and Zeckhauser (1988) showed that when people are assigned a random default choice in an experiment, they tend to stick with it — even when switching is costless and the options are clearly described.

**The strongest real-world evidence:** Organ donation rates across countries differ dramatically — not by culture, not by education, not by healthcare quality. They differ almost entirely based on whether the default is *opt-in* (low donation rates: US ~40%) or *opt-out* (high donation rates: Spain, Austria ~85–90%).

Same people. Same values. Completely different outcomes. The only difference: which option is the default.

**Where it appears:**

- *Product design:* The default setting is the most powerful UX decision you make. Users stick with defaults at high rates regardless of the "correct" setting for them.
- *Subscription businesses:* Annual billing defaults convert more users to annual plans than monthly defaults — even when monthly is displayed as an option.
- *AI model settings:* Temperature, verbosity, safety filters — most users never change defaults. Your defaults are your product.
- *Career:* People stay in cities they don't love, relationships that are "fine," and companies with declining cultures because the status quo requires no action. Inaction is not the absence of a choice — it is a choice.

---

## The Formal Picture

### Loss aversion — the numbers

In Prospect Theory (Day 8), your sense of value is measured relative to a starting point — a gain or a loss from where you currently are. Kahneman and Tversky found that losses hit roughly 2.25 times harder than equivalent gains feel good:

```
Loss aversion coefficient ≈ 2.25
The pain of losing $X ≈ 2.25 × the pleasure of gaining the same $X
```

So if you find $100, you feel a certain pleasure. To feel the same intensity in the opposite direction, you'd only need to *lose* about $44.

The value curve also has these properties:
- **In the gains zone:** each extra dollar gained matters a little less than the one before (the same diminishing returns from Day 3).
- **In the losses zone:** the first $100 lost hurts more than the *next* $100 — losing $200 doesn't hurt twice as much as losing $100.

This gives the characteristic S-shape:

```
Value
  ▲
  │        ────────────── (concave in gains)
  │    ────
  │────
──┼──────────────────────── x (outcome)
  │────
  │    ────
  │        ────────────── (steeper; convex in losses)
  ▼
```

### Sunk cost as violation of consequentialism

Formal decision theory (expected utility, consequentialism) requires that only *future consequences* influence current decisions. The sunk cost fallacy violates this by including past (irreversible) costs in the decision calculus.

The rational rule is simple: at each decision point, compute the *continuation value* — the expected value of going forward — versus the value of stopping. Sunk costs are not included in either calculation.

### Status quo bias as reference dependence + loss aversion

Status quo bias is not a separate mechanism — it is loss aversion applied to the *current state* as reference point. Changing from the status quo involves potential losses (what you give up) and potential gains (what you acquire). Loss aversion makes the losses feel larger, creating a net preference for staying put — even when the expected value of changing is positive.

In plain terms: if the status quo is your reference point, the things you'd gain by switching feel smaller than the things you'd give up — because losses loom larger. The "endowment effect" — valuing something more just because you already own it — is the same mechanism at work.

---

## Where It Breaks / What It Is Not

**Loss aversion is not always irrational.** In situations where you have genuinely limited resources and catastrophic downside risk, being more sensitive to losses than gains is *adaptive*. A hungry animal should be more sensitive to the risk of losing its only meal than to the possibility of finding a second one. The problem arises when this evolved sensitivity is applied to modern financial or probabilistic decisions where the adaptive logic no longer holds.

**Sunk cost avoidance can be overcorrected.** There are legitimate reasons to consider past investments — not because the cost is "sunk" in the accounting sense, but because:
1. Past investment signals something about *your type* — if you committed strongly to something, it might signal genuine conviction worth honouring.
2. Reputation and consistency matter — suddenly abandoning a project can signal untrustworthiness to stakeholders.
These are *future*-facing considerations, not sunk cost reasoning — but they can look similar from the outside.

**Status quo bias is not always wrong.** "If it ain't broke, don't fix it" reflects genuine value: switching costs, learning curves, and coordination costs are real. The bias is in systematically *overweighting* these costs relative to the gains from change — not in noticing them at all.

**The endowment effect (a fourth, related bias):** People value things more highly simply because they own them. Coffee mug experiments (Kahneman, Knetsch & Thaler, 1990) showed that sellers demand roughly twice what buyers will pay for identical mugs — after random assignment of ownership. This is loss aversion applied to the "loss" of giving up a possession. Watch for it in negotiations (Day 25) and startup equity discussions.

---

## Try It Yourself

**Exercise 1 — Identify the bias (5 min)**

Label each scenario as loss aversion, sunk cost fallacy, or status quo bias:

a) A team continues developing a feature for 3 more months because "we already built the backend."

b) A user doesn't switch to a clearly better productivity app because "my current one has all my files."

c) A PM argues for keeping a dark pattern in onboarding because "removing it will reduce conversion" (framing the loss, not the gain).

d) An investor refuses to cut losses on a bad startup investment because "selling now would mean accepting the loss as real."

e) A company doesn't change their pricing page layout despite clear A/B test evidence that the new layout converts better, because "we've always done it this way."

<details>
<summary>Answers</summary>

a) **Sunk cost fallacy** — "already built" is past, irreversible cost.
b) **Status quo bias** — preference for current state even when objectively inferior.
c) **Loss aversion** — framing the decision as a potential loss (reduced conversion) rather than a potential gain (ethical improvement, long-term trust).
d) **Loss aversion + sunk cost** — the sunk cost is the initial investment; loss aversion makes realising the loss feel worse than a comparable gain feels good.
e) **Status quo bias** — preference for familiar current state despite evidence.

</details>

---

**Exercise 2 — The rational corrective (15 min)**

Take a decision you have been avoiding or deferring in your professional life — a conversation you haven't had, a project you haven't killed, a tool you haven't migrated to.

Apply the three correctives:

1. *Sunk cost check:* "If I were starting fresh today with no prior commitment, what would I do?"
2. *Loss aversion check:* Am I framing this as a loss when it could equally be framed as a gain? What is the neutral description?
3. *Status quo check:* Is "do nothing" actually the best option, or does it just feel safe because it requires no action?

Write one sentence conclusion: what does your rational analysis say, and what does your gut still feel?

---

**Exercise 3 — Default design (10 min, L2)**

You are redesigning the notification settings for an AI assistant product. Users can choose: All notifications, Important only, or None.

a) Which default maximises engagement? Which default maximises user satisfaction long-term? Are they the same?

b) Thaler and Sunstein argue that choice architects have a responsibility to choose defaults in users' *long-term best interest*, not just what's best for the platform. How would you apply this principle here?

c) Is there a case where no default (forcing an explicit choice) is better than any default?

---

## Connect It Back

Yesterday's biases (anchoring, availability, framing) corrupted how the decision *entered* your mind. Today's biases (loss aversion, sunk cost, status quo) corrupt how you *evaluate* it — specifically through the lens of potential loss and inertia.

Together, these six biases constitute the primary failure modes of human decision-making in consequential, unfamiliar situations. They share a common thread: they are products of System 1's heuristic processing applied to situations that require System 2.

Tomorrow (Day 8) we get the unified formal model that wraps all of this into one elegant picture: **Prospect Theory**. Kahneman and Tversky did not just catalogue biases — they built a model that *predicts* which direction the bias will go, how large it will be, and why. Day 8 is where the descriptive account becomes rigorous.

**The question you should be able to answer now:**
*Why do people make worse decisions when framing makes a choice feel like avoiding a loss rather than achieving a gain — even when the underlying options are identical?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Kahneman, *Thinking, Fast and Slow*, Chapter 26 ("Prospect Theory") and Chapter 32 ("Keeping Score"). Chapter 26 is the formal heart of tomorrow's page — reading it now will prime you. Chapter 32 covers the sunk cost and mental accounting concepts with vivid examples.

**If you want the deep version:**

1. Thaler, Richard H. "Mental Accounting Matters." *Journal of Behavioral Decision Making*, 12(3):183–206, 1999. — The definitive treatment of how people organise financial decisions into mental accounts and why this leads to systematic errors. Directly relevant to pricing and subscription design. Read Sections 1–3.

2. Samuelson, William & Zeckhauser, Richard. "Status Quo Bias in Decision Making." *Journal of Risk and Uncertainty*, 1(1):7–59, 1988. — The foundational status quo bias paper. Read the abstract and the first two experiments (pages 7–20).

3. Ariely, *Predictably Irrational*, Chapter 7 ("The High Price of Ownership"). — The endowment effect explained accessibly, with experiments on Duke basketball tickets and other vivid examples. Directly applicable to product ownership and lock-in design.


---

← [Day 6 — Cognitive Biases I](day-06-cognitive-biases-i) &nbsp;|&nbsp; [Day 8 — Prospect Theory →](day-08-prospect-theory)
