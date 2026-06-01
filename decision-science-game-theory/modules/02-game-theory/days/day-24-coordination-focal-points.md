*[Decision Science & Game Theory](../../../../README.md) · [Module 2 — Game Theory Fundamentals](../2-overview.md) · Day 24 of 65*

# Day 24 — Coordination Games & Focal Points

> **Today's one idea:** When a game has multiple Nash equilibria and players must coordinate without communication, they converge on the one that feels "natural" or "salient" — what Schelling called a focal point — and this non-mathematical property turns out to be as powerful as formal game theory.
> **Reading time:** ~35 min · **Prereqs:** [Day 16](day-16-nash-equilibrium.md), [Day 14](day-14-what-is-a-game.md)
> **Primary source for today:** Schelling, Thomas C. *The Strategy of Conflict*. Harvard University Press, 1960. Chapter 3 ("Focal Points: The Coordination Problem") and the appendix experiments. Also: Dixit & Nalebuff, *The Art of Strategy*, Chapter 7 ("The Strategy of Conflict: Focal Points").

---

## The Hook

In 1958, Thomas Schelling ran an experiment with Yale Law School students. He gave pairs of students a simple challenge:

> "You are to meet a stranger in New York City tomorrow. You haven't been told where or when. Where do you go, and at what time?"

The formal game-theoretic answer: there are infinitely many Nash equilibria (any location and time works, as long as both pick the same one). The model predicts nothing.

But the experimental answer was striking. Most students said: **noon, at the information booth in Grand Central Station.**

No communication. No prior agreement. Just a shared sense that Grand Central was the "obvious" place to be in New York City, and noon was the "obvious" time of day. Schelling called this a **focal point** — a solution that stands out by virtue of its salience, uniqueness, or cultural prominence, not by formal optimality.

The lesson: game theory tells you what is strategically stable; focal points explain which stable point humans actually land on. The two are complementary, not competing.

---

## Building the Intuition

### The coordination game structure

Coordination games are the structural opposite of Prisoner's Dilemmas: players want to coordinate, not defect. The problem is not "how do we resist temptation?" but "how do we agree on which equilibrium to play?"

**Battle of the Sexes** (revisited from Day 16):

```
                    B: Ballet     B: Basketball
A: Ballet           (2, 1)        (0, 0)
A: Basketball       (0, 0)        (1, 2)
```

Two Nash equilibria: (Ballet, Ballet) and (Basketball, Basketball). A prefers the first; B prefers the second. Both prefer any coordination to no coordination — but they disagree on which.

**The pure coordination game:**

Sometimes preferences are aligned — the only problem is picking the same option:

```
                    B: Left     B: Right
A: Left             (1, 1)      (0, 0)
A: Right            (0, 0)      (1, 1)
```

Two Nash equilibria: (Left, Left) and (Right, Right). Both players are indifferent between them — they just need to match. This is the driving-side convention. The UK landed on Left; the US on Right. History, path dependence, and early critical choices lock in one equilibrium or the other. Once locked in, no individual can profitably deviate.

**The technology standard game** (at scale):

VHS vs. Betamax. iOS vs. Android. QWERTY vs. Dvorak. In each case, the market has multiple possible equilibria, everyone benefits from being on the same standard, and history selects one. The game-theoretic model predicts stability once a standard is adopted — it does not predict which standard wins.

---

### What makes a focal point focal?

Schelling's insight: in the absence of formal coordination mechanisms, players look for a point that is:

1. **Unique:** Stands out from alternatives by some salient feature.
2. **Prominent:** Widely known to be prominent (Grand Central, not a specific café).
3. **Common knowledge:** Each player knows the other knows it's prominent, and knows the other knows they know, and so on.

The key is *common knowledge of salience*. For a focal point to work, it's not enough that you think it's obvious — you need to believe your partner also thinks it's obvious and believes you think so.

**Schelling's experiments produced striking results:**

- "Name a time for a meeting when no place is specified" → most people said noon or midnight.
- "Name a positive number" → most people said 1.
- "You and a partner are each given the numbers 1–8. You win if you pick the same number" → most picked 1.
- "Name a city in the US" → most named New York.

These are not random. They are *culturally shared signals about salience*. The logic is self-referential: you pick New York because you think your partner will pick New York, which you think because New York is the "obvious" choice, which makes it obvious to pick New York. Focal points bootstrap themselves.

---

### When game theory meets culture

Focal points are not part of the formal game — they are a property of the *context* in which the game is played. Two implications:

**Implication 1: Equilibrium selection depends on culture and context.**

The same game played in different cultures may have different focal points. In Japan, "meet in the middle" might be a focal point for compromise; in the US, "first-mover advantage" might coordinate on one player acting first. Game theory specifies the equilibrium set; culture selects from it.

**Implication 2: Designers can create focal points.**

If you design the context, you can shape which equilibrium is selected. A legal default (you are opted into organ donation unless you opt out) creates a focal point at the status quo. A naming convention ("the main branch is called `main`, not `master`") creates a focal point for new projects. A platform standard ("all API responses are JSON") coordinates developers without explicit negotiation. Focal point design is a form of soft mechanism design.

---

### Network effects as coordination games

A platform with network effects is a coordination game at scale. Facebook, LinkedIn, Slack — each has value only if others are on it. The payoff structure:

```
                    B: Join platform    B: Don't join
A: Join platform    (v, v)              (0, 0)
A: Don't join       (0, 0)              (s, s)
```

Where v > s > 0 (both join is better than both stay off; coordinating is what matters).

Two Nash equilibria: everyone joins, or nobody joins. The platform's challenge is getting to the "everyone joins" equilibrium — classic chicken-and-egg problem. Strategies to create a focal point:

- **Seed with high-value users:** Make joining the "obvious" choice for the early adopter demographic, whose participation makes it obvious for the next wave.
- **Create a visible commitment:** "100,000 developers have already joined" — exploiting Schelling's uniqueness property (the large number signals this is where the crowd is going).
- **Exploit an existing coordination point:** Slack replaced email lists that already had the group assembled. The existing coordination point was the email thread; Slack just claimed it.

---

### Anti-coordination games: the game of Chicken

Not all multi-equilibrium games are coordination games. In **Chicken**, both players want the other to swerve:

```
                    B: Swerve     B: Drive straight
A: Swerve           (0, 0)        (−1, 1)
A: Drive straight   (1, −1)       (−10, −10)
```

Two Nash equilibria: (A swerves, B drives straight) and (B swerves, A drives straight). But players have opposing preferences — each wants to be the one who doesn't swerve.

The game of Chicken creates the **commitment value** we saw on Day 20: if you can credibly commit to driving straight before the other player commits, they are forced to swerve. This is why Chicken combined with commitment (public announcements, visible steering-wheel locking) is one of the most analysed games in political science — arms races and military brinkmanship have exactly this structure.

The focal point in Chicken is typically whoever committed first, publicly, irreversibly — the logic of Day 20 selects the equilibrium.

---

## The Formal Picture

**Coordination game (definition):**

A game is a pure coordination game if it has multiple Nash equilibria and all players weakly prefer any Nash equilibrium to any non-equilibrium outcome. (They just need to match — not which match.)

**Battle of the Sexes (definition):**

A game with multiple Nash equilibria where players rank the equilibria differently. There is a conflict over which coordination point to select, even though coordination is better than miscoordination.

**Focal point (Schelling's definition):**

A focal point is a Nash equilibrium that is selected by players in the absence of explicit communication because it is salient — distinguished by some cultural, contextual, or structural feature that makes it the "natural" or "obvious" choice, and whose obviousness is common knowledge.

**Common knowledge of salience (formal):**

A property P is common knowledge among a set of players if:
- Everyone knows P
- Everyone knows that everyone knows P
- Everyone knows that everyone knows that everyone knows P
- ... (infinitely iterated)

Common knowledge is stronger than "everyone knows." In formal epistemic logic (relevant for Day 29), the distinction between knowledge, mutual knowledge, and common knowledge is crucial.

**Equilibrium refinements:**

Focal points are an informal equilibrium refinement — they select a Nash equilibrium based on salience rather than formal optimality criteria. Formal refinements include:
- **Risk-dominance** (Day 38): Select the equilibrium that is the best response to a 50/50 belief over the two equilibria. Often selects the same equilibrium as the focal point.
- **Payoff dominance:** Select the equilibrium that gives everyone the highest payoff. Sometimes conflicts with risk-dominance.

---

### Focal points in AI product design

**Naming and interface conventions:**

When multiple teams independently build compatible components, naming conventions are focal points. REST API conventions (GET for reading, POST for creating) are Nash equilibria of a naming game — no individual team benefits from deviating once the convention is established. Breaking convention is individually costly; establishing a new convention requires coordinating an industry-wide shift.

**Default settings as focal points:**

The default option in a product is the focal point for most users. Opting in vs. opting out changes the equilibrium without changing formal choice availability. Privacy defaults, notification preferences, and subscription models all exploit focal point logic. Design choice = equilibrium selection choice.

**Feature flag naming in organisations:**

When dozens of engineers set feature flags across a codebase, naming conventions coordinate their choices without explicit discussion. A team that names flags clearly and consistently creates a focal point for the engineering culture — new engineers adopt the convention, reinforcing it.

**Pricing anchors:**

A product priced at $99/month makes $79/month feel like a deal. The $99 is a focal point — it's the reference price from which all other prices are judged. This is the anchoring bias (Day 6) acting through focal point logic: the "obvious" comparison point is the anchor, and it shifts the equilibrium of the pricing game.

---

## Where It Breaks / What It Is Not

**Focal points are not predictive in novel situations.** Schelling's experiments worked because subjects shared cultural context (American English speakers, familiar with New York). In genuinely novel games — first contact between cultures, new technology markets with no established convention — there is no shared focal point, and coordination fails until experience creates one.

**Risk-dominance and focal points often disagree.** The risk-dominant equilibrium is not always the most salient. In some games, the Pareto-dominant equilibrium (everyone gets more) is focal even though it is not risk-dominant. Experimental evidence is mixed on which one prevails.

**Common knowledge is hard to establish.** Truly common knowledge requires infinite mutual knowledge. In practice, common knowledge is approximated by shared public signals (broadcast announcements, widely read documents). When the signal is not truly public (only 80% of people saw the announcement), common knowledge breaks down and coordination can fail.

**Focal points can be exploited.** If you know the focal point better than your opponent — you understand which equilibrium they'll coordinate on — you can force them into an unfavourable equilibrium (Chicken's first-mover commitment). Sophisticated players try to shift the focal point in their favour; less sophisticated players get locked into the pre-existing one.

---

## Try It Yourself

**Exercise 1 — Classify the coordination problem (5 min)**

For each, identify whether it is (a) pure coordination, (b) Battle of the Sexes, or (c) anti-coordination (Chicken). State the Nash equilibria and the focal point, if one exists.

i) Two developers must pick the same programming language for a shared project. Dev A prefers Python; Dev B prefers JavaScript. Both strongly prefer any common language to separate languages.

ii) Two firms must choose left or right at a four-way intersection — one is heading north, one south. Coordination prevents collision.

iii) Two competing products must decide who goes to market first. Being second is weakly preferable (learn from the first-mover's mistakes), but someone must go first.

<details>
<summary>Answer</summary>

**i) Battle of the Sexes.** Two NE: (Python, Python) and (JavaScript, JavaScript). Dev A prefers Python NE; Dev B prefers JavaScript NE. Focal point: likely whichever language the existing codebase uses, or the industry standard for the domain (Python for ML, JavaScript for web). Convention resolves the coordination conflict.

**ii) Pure coordination.** Two NE: (Left, Left) and (Right, Right). Neither player cares which side is chosen — just need to match. Focal point: existing traffic law (drive on the right/left depending on country). Once established, focal point is self-reinforcing.

**iii) Anti-coordination / Chicken variant.** Payoffs: (A goes first, B goes first) is miscoordination; (A goes first, B waits) and (B goes first, A waits) are the two NE; (neither goes first) is the worst outcome. Focal point: usually whoever has committed publicly (raised money, announced launch date) goes first. This is Chicken resolved by prior commitment — Day 20 logic.

</details>

---

**Exercise 2 — Design the focal point (15 min)**

You are launching a developer API platform. There are three possible JSON response formats (Format A, B, C). You want all developers to use the same format.

a) Model this as a coordination game. What are the Nash equilibria?

b) Format A is identical to the format used by the dominant incumbent platform. Formats B and C are your own designs. Which format should you launch with, and why?

c) Your competitor is also launching an API platform at the same time. You both choose formats independently. What is the game between you and your competitor, and how do focal points affect the outcome?

---

**Exercise 3 — The coordination failure post-mortem (10 min)**

Think of a coordination failure you have witnessed or read about — two teams that built incompatible systems, a standard war, a community that split over a naming convention, a meeting where two people showed up at the wrong location.

a) What were the multiple Nash equilibria?
b) Why did no focal point emerge in time?
c) What mechanism (contract, authority, convention, communication) eventually resolved it — or failed to?

---

## Connect It Back

Coordination games reveal a gap in the Nash equilibrium framework: it predicts what is stable but not which stable outcome players select. Focal points fill this gap with a behavioural observation — salience coordinates expectations without communication — and a design implication: you can engineer focal points to select the equilibrium you want.

Tomorrow (Day 25) moves to **bargaining theory**: when two players split a surplus, what determines the outcome? Rubinstein's alternating-offers model applies backward induction to infinite-horizon negotiation and produces a unique prediction based on impatience and outside options — the formal theory of "splitting the difference."

**The question you should be able to answer now:**
*Why is a focal point not part of the formal game-theoretic model — and why does this make it more important, not less, for predicting real-world coordination?*

---

## Suggested Readings for Today

**Required if you have 15 extra minutes:**
Schelling, Thomas C. *The Strategy of Conflict*. Harvard University Press, 1960. Chapter 3 ("Focal Points") — specifically pages 54–67, which describe the coordination experiments and the notion of salience. This is where the concept was born; 13 pages, unusually readable for foundational game theory.

**If you want the deep version:**

1. Schelling, Thomas C. *The Strategy of Conflict*, Chapter 4 ("Toward a Theory of Interdependent Decision"). — Schelling's attempt to formalise the focal point intuition. Less crisp than Chapter 3 but contains the common knowledge discussion. Read after Chapter 3 if the formalism interests you.

2. Harsanyi, John C. & Selten, Reinhard. *A General Theory of Equilibrium Selection in Games*. MIT Press, 1988. Introduction and Chapter 1. — The most ambitious attempt to formalise equilibrium selection, introducing risk-dominance and payoff dominance as selection criteria. Technical but relevant to understanding when focal points align with formal equilibrium refinements.

3. Camerer, Colin F. *Behavioral Game Theory: Experiments in Strategic Interaction*. Princeton University Press, 2003. Chapter 7 ("Coordination, Evolution, and Social Learning"). — The empirical record on how humans actually solve coordination problems. Includes lab evidence on focal points, with comparisons to Schelling's original experiments in modern settings.


---

← [Day 23 — Evolutionary Game Theory](day-23-evolutionary-game-theory) &nbsp;|&nbsp; [Day 25 — Bargaining Theory →](day-25-bargaining-theory)
