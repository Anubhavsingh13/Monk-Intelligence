*[Decision Science & Game Theory](../../../../README.md) · [Module 4 — Decision Science in AI Products](../4-overview.md) · Day 52 of 65*

# Day 52 — Nudge Design & Behavioural Mechanism Design

**Today's one idea:** A nudge is a mechanism that changes behaviour by changing the choice architecture rather than the incentives — it exploits systematic cognitive biases to guide agents toward better outcomes without restricting their choices, and designing nudges requires understanding both the biases being targeted and the potential for manipulation.

**Prerequisites:** Day 30 (mechanism design), Day 9 (decision under uncertainty/Kahneman-Tversky heuristics — assumed from course background), Day 39 (information design), Day 51 (platform mechanism design)

**Primary sources:** Thaler, Richard H. & Sunstein, Cass R. "Nudge: Improving Decisions About Health, Wealth, and Happiness." Yale University Press, 2008. Chapter 1. Sunstein, Cass R. "The Ethics of Nudging." Yale Journal on Regulation, 32(2):413–450, 2015.

**Reading time:** ~35 minutes

---

## The Hook

In 2004, the UK government faced a familiar problem: too few people were registered as organ donors. The traditional policy toolkit offered two options. One: create financial incentives (pay donors or their families). Two: mandate registration. Both are politically and ethically fraught — paying for organs commodifies human bodies; mandates remove individual choice. Neither was enacted.

Instead, a third option was proposed. Don't change what people can choose. Don't change what they get paid. Change the starting point. Instead of registration requiring an explicit opt-in, make registration the default and require an explicit opt-out to decline. Everything else stays the same — full freedom to choose either way.

Donor registration rates went from roughly 30% to roughly 80%.

No new incentives. No mandates. No campaign. Just a different starting point.

This is a nudge. The insight behind it — that how choices are presented is as powerful as what the choices are — is the core contribution of behavioural economics to policy and product design. When Kahneman and Tversky documented systematic cognitive biases in the 1970s and 1980s, it was primarily a descriptive achievement: humans deviate from rational choice in predictable ways. The nudge framework, formalised by Thaler and Sunstein in 2008, asked the prescriptive question: if we know how humans systematically deviate from rationality, can we design choice environments that channel those deviations toward better outcomes?

For AI product designers, the relevance is immediate. Every interface is a choice architecture. Every choice architecture nudges. The question is not whether you nudge — it's whether your nudges are intentional, transparent, and aligned with user welfare, or accidental, opaque, and aligned with platform revenue at the user's expense. The ethical and design stakes of this distinction are the subject of today's page.

---

## Building the Intuition

### 1. What a Nudge Is (and Is Not)

The definition from Thaler and Sunstein is precise: a nudge is any aspect of the choice architecture that alters people's behaviour in a predictable way without forbidding any options or significantly changing economic incentives. The "significantly changing economic incentives" clause is load-bearing — it distinguishes nudges from classical mechanism design interventions.

Let's be explicit about the boundary:

**Not nudges:**
- Banning options (that's prohibition)
- Adding taxes or subsidies that change the economic payoff of choices (that's classical mechanism design — Pigouvian instruments)
- Restricting information access (that's information suppression, not choice architecture)
- Mandating a specific choice with no opt-out

**Are nudges:**
- Setting a default (opt-in vs. opt-out registration)
- Changing the ordering of options (the option listed first gets disproportionate selection)
- Adding descriptive social norms ("Most people in your area pay their taxes on time")
- Changing the framing of outcomes (presenting the same option as a gain vs. as avoiding a loss)
- Adding friction to undesirable choices (a confirmation dialog before proceeding)
- Removing friction from desirable choices (one-click checkout)
- Making relevant information more salient (calorie counts on menus)

The philosophical label Thaler and Sunstein attached to this — "libertarian paternalism" — captures the tension. Libertarian: every choice remains available; no coercion. Paternalistic: the choice architecture is designed by someone (the "choice architect") who has a view about what's better for the chooser. The organ donor default designer has a view that donation is better than non-donation for society. The retirement plan designer has a view that saving is better than not saving. The choice remains fully available — but the starting point encodes a value judgment.

### 2. The Cognitive Biases That Nudges Exploit

Nudges work because human cognition has systematic structure. The biases Kahneman and Tversky documented are not random noise — they are predictable departures from rationality that follow consistent patterns. Nudge design is the engineering of choice environments that channel these patterns toward intended outcomes.

**Default bias and status quo inertia.** The most powerful nudge lever. People disproportionately stick with whatever option is presented as the starting point or default. Why? Multiple mechanisms reinforce this: (a) Loss aversion — deviating from the default feels like accepting a loss (of the default option), while staying feels like avoiding a loss. Given λ ≈ 2.25 (Kahneman-Tversky loss aversion coefficient), the psychological cost of switching is roughly double the gain. (b) Cognitive effort — evaluating alternatives requires mental energy; the default is the "no-effort" choice. (c) Implicit endorsement — the default often feels like the choice the designer recommends, which anchors beliefs about what "normal" people do.

The organ donor case illustrates all three mechanisms. Opting out of donation (when donation is the default) feels like actively choosing not to donate — a loss-framed decision. It requires effort (finding the form, filling it out). And the default implicitly signals that the government/society endorses donation.

**Salience and attention.** People respond to what is prominent in their perceptual field, not to what is relevant to their decision. A study of cafeteria design found that placing fruit at eye level at the start of the serving line increased fruit selection by 30%, while placing the same fruit at the end reduced selection to near zero. The quality and price of the fruit didn't change — only its position in the visual field.

For AI products: what the interface makes visually salient drives behaviour independently of the underlying quality or relevance of the option. Dark patterns exploit this by making undesired options (unsubscribe) visually recessive and desired options (purchase) visually prominent.

**Descriptive social norms.** Humans use others' behaviour as a signal of the correct or appropriate behaviour, especially under uncertainty. "80% of your neighbours pay their taxes on time" — tested by the UK's HMRC — increased tax compliance by approximately 5 percentage points among delinquent filers, more than any monetary incentive at equivalent cost. The norm communicates both what is common (reducing uncertainty) and what is approved (providing social signal).

The connection to Day 39 (information design): descriptive norms are selective information revelation. The platform (or government) is choosing to reveal that "most people do X," which shifts the receiver's prior about appropriate behaviour. This is a persuasion strategy — and like all persuasion strategies, its legitimacy depends on whether the revealed norm is accurate (real vs. synthetic), representative (of the relevant reference group), and aligned with the receiver's actual interests.

**Present bias.** People discount near-term outcomes much more steeply than standard exponential discounting would suggest — they prefer $100 today over $110 tomorrow, but prefer $110 in 31 days over $100 in 30 days. This "present bias" or hyperbolic discounting means that future-self's preferences differ systematically from today-self's preferences. Today-self would like to save more but will spend the money when it arrives. Future-self would have preferred that today-self had saved it.

Nudges that exploit present bias in the user's interest: commitment devices. These are mechanisms that let today-self pre-commit future-self to a course of action, making it costly or inconvenient to deviate. The canonical example is Save More Tomorrow (SMarT, Thaler & Benartzi 2004): employees commit today to directing a fixed percentage of future pay raises to retirement savings. Because the commitment applies to future income (not yet received), present bias doesn't fight it — and by the time the raise arrives, the commitment is automatic. Participation in retirement plans increased substantially where SMarT was implemented.

**Choice overload.** When the number of options exceeds cognitive processing capacity, decision quality degrades. The paradox of choice (Schwartz 2004): more options produce worse decisions and lower satisfaction, not better. Iyengar and Lepper's famous jam study: a display of 24 jams attracted more visitors, but a display of 6 jams produced 10 times more purchases. Reducing the choice set — even removing options — can increase both decision quality and satisfaction.

For AI products: the "recommend" default in complex product categories (insurance, investment portfolios, software configuration) is a choice simplification nudge. By offering one recommended option prominently, the platform reduces cognitive load and increases selection. Whether this is beneficial depends on whose interests the recommendation optimises.

### 3. Nudge Design as Mechanism Design

Day 30 defined mechanism design as: given a goal outcome, design the rules of a game such that agents pursuing their own interests reach that outcome as an equilibrium. Nudge design is a variant of this problem with a twist: instead of assuming agents maximise utility rationally, it assumes agents follow predictable cognitive shortcuts (System 1 thinking, in Kahneman's framework) and designs the choice environment to make those shortcuts produce the intended outcome.

Classical mechanism design: design payoffs so that rational agents' optimal behaviour produces the desired outcome.

Nudge design: design the choice architecture so that cognitively bounded agents' heuristic behaviour produces the desired outcome.

Both are "rules → intended behaviour" design problems. The difference is in the model of the agent being designed for. Classical mechanism design works well when agents carefully evaluate all options and respond to incentive structures. Nudge design works well when agents rely on defaults, salience, social cues, and cognitive shortcuts.

The revelation principle (Day 30) has an analog in nudge design. The revelation principle says: any outcome achievable by a complex mechanism is achievable by a direct mechanism in which truth-telling is optimal. The nudge analog: if you correctly understand an agent's cognitive biases, you can design a choice architecture in which the agent's biased behaviour produces their own long-run preferred outcome. The architecture "reveals" and acts on the agent's true long-run preference — even when the agent would, in the absence of the nudge, act contrary to that preference due to present bias, status quo inertia, or inattention.

This framing is both the appeal and the ethical hazard of nudge design. "We're just helping people act on their own true preferences" is the nudge designer's justification. But it requires the designer to know what the agent's true long-run preferences are — which is often uncertain, context-dependent, and contested. The organ donor default assumes donation is the correct default for most people. This is probably right on aggregate, but individual preferences for organ donation vary for religious, cultural, and personal reasons that the default cannot accommodate.

### 4. Nudge Taxonomy for AI Products

**Default nudges.** The most powerful and most commonly deployed. Examples along the helpful-to-manipulative spectrum:

- Helpful: retirement plan auto-enrollment at a positive contribution rate (employees can opt down or out; most don't). Evidence consistently shows significantly higher savings rates without affecting satisfaction.
- Ambiguous: subscription auto-renewal. From the platform's perspective: users who signed up presumably want to continue; auto-renewal removes friction. From the user's perspective: they may have intended to re-evaluate and forgot. The same mechanism serves different interests depending on whether users' long-run preference aligns with the platform's revenue interest.
- Manipulative (dark pattern): default opt-in to marketing communications with the opt-out hidden in fine print. The default exploits inertia against users' stated preferences.

The structural feature that distinguishes helpful from manipulative defaults: whose long-run interest does the default serve? A default that saves users effort while producing an outcome they would have chosen anyway is helpful. A default that produces an outcome the user would reject if they noticed is manipulative.

**Friction nudges.** Adding effort to a path reduces how often people take it. Reducing effort increases frequency. Friction is a non-monetary cost that changes effective payoffs without changing stated prices.

Helpful friction nudge: a confirmation dialog before permanently deleting data ("Are you sure? This cannot be undone."). This adds friction to an irreversible action that users often regret.

Manipulative friction nudge: a multi-step unsubscribe flow that requires confirming through email, navigating multiple pages, and selecting a reason — with each step subtly offering a retention offer. The friction is designed to exhaust users into abandoning the unsubscribe attempt, not to prevent regretted decisions.

Helpful friction reduction: one-click purchase (reducing friction for a transaction the user has decided to make). Manipulative friction reduction: disabling the "pause subscription" option and making cancellation the only alternative (forcing an all-or-nothing decision to prevent a preferred intermediate option).

**Salience nudges.** Making relevant information visually or cognitively prominent changes behaviour without changing the information content. The same calorie information printed in small text in a menu footnote vs. displayed next to the item name in large type has dramatically different effects on choice — not because the information differs, but because salience determines whether it is processed.

For AI products: interface design choices about what to show prominently are salience nudges. Showing the "total price including fees" vs. "base price" in accommodation search is a salience nudge (Day 51 exercise). Showing the environmental impact of a purchase is a salience nudge. Adding a "most popular" badge to a pricing tier is a salience nudge that exploits both social norms and processing shortcuts.

**Social norm nudges.** "Other people like you do X" is information — but it is also a nudge. It changes the reference point for what is normal, and because humans calibrate their behaviour to social norms (conformity, approval-seeking), it shifts behaviour toward the stated norm.

The most extensively studied example is the Opower (now Oracle) energy efficiency program. Households received reports showing their energy usage compared to similar households in their area: "Your household used 38% more electricity than your efficient neighbours." This social comparison nudge reduced energy consumption by 2–3% among above-average users, with effects persisting for years. The nudge was effective, transparent, and welfare-improving by essentially any standard.

Contrast this with "People like you also buy..." on e-commerce platforms. The norm information is real (aggregated purchase behaviour) but the framing is designed to trigger social conformity and FOMO (fear of missing out) in service of additional purchases — which may or may not align with the user's actual preferences and budget.

**Commitment device nudges.** Today-self precommits future-self to a course of action, leveraging the asymmetry between present bias (strong resistance to committing current resources) and future-oriented preferences (genuine desire for future outcomes). Commitment devices turn this into a mechanism: "I, today-self, bind future-self to action X when trigger Y occurs."

Save More Tomorrow is the cleanest example: commit to saving future pay raises before they arrive, when the present-bias cost of commitment is near zero (the money doesn't exist yet). By the time the raise arrives and present bias kicks in, the commitment is automatic.

For AI products: language learning apps that let users set daily practice commitments with streak mechanics and loss penalties are commitment device nudges. The streak (Day 21 repeated game dynamics) creates a social and psychological commitment to daily practice that counteracts present bias. "Don't break the chain" is a commitment device delivered through interface design.

### 5. Dark Patterns as Adversarial Nudges

Dark patterns (term coined by Harry Brignull in 2010, now called "deceptive design patterns") are choice architecture manipulations that trick users into actions they would not take if fully informed and unconstrained. They are nudges — they exploit cognitive biases, use defaults and friction, leverage salience and social norms — but they are deployed against user welfare in the platform's interest.

Key examples with their mechanism design interpretation:

**Confirmshaming.** The opt-out option is phrased to make declining feel socially or morally bad: "No thanks, I don't want to save money." The cognitive mechanism: people avoid options that threaten their self-image. The dark pattern uses this to make declining feel like a character flaw. Mechanism interpretation: the mechanism designer (platform) has designed the choice framing so that the agent's self-image preservation (a real preference) conflicts with their budget preferences (also a real preference) — and bets on self-image winning.

**Hidden unsubscribe.** Unsubscribe is technically available (libertarian: freedom of choice preserved) but visually buried in light grey text, small font, at the bottom of a long page, behind a login wall. The friction is designed to increase opt-out abandonment. Mechanism interpretation: this exploits effort costs (friction) to make the status quo (continued subscription) dominant over the agent's revealed preference (they clicked to unsubscribe).

**Fake urgency.** "Only 2 left at this price!" or countdown timers that reset after expiring. These exploit scarcity signalling — a genuine heuristic (scarce things are often more valuable) — with fabricated signals. This crosses from nudge (exploiting real cognitive shortcuts) into deception (providing false information to trigger those shortcuts).

**Trick questions with double negatives.** Checkbox: "Uncheck this box if you do not want to not receive marketing communications." The cognitive load of parsing the double negative causes processing errors. Users check or uncheck based on their confusion, not their preferences.

Mechanism design framing: dark patterns are welfare-reducing mechanisms. They satisfy the platform's incentive compatibility constraint (users do what the platform wants) but violate the individual rationality constraint for users — participation in the mechanism leaves users worse off than they would have been without it. This is precisely the violation that mechanism design theory uses to identify bad mechanisms: any mechanism where IR is violated for some participants is extracting surplus from those participants against their interests.

The regulatory response reflects this framing. The EU Digital Services Act and General Data Protection Regulation (GDPR) explicitly prohibit certain dark patterns in consent interfaces. The US FTC has issued guidance on deceptive design. These regulations are external mechanism design: a regulator imposes constraints on platform mechanism design to ensure that mechanisms satisfy the IR constraint for users, not just the IC constraint for the platform.

### 6. Measuring Nudge Effectiveness and Its Limits

How do you know whether a nudge is working? The gold standard is the randomised controlled trial (A/B test) — which is tomorrow's full topic (Day 53). For today: the specific issues that make nudge evaluation harder than it might seem.

**The Hawthorne effect.** People change behaviour because they know they're being observed or experimented on, not because of the nudge itself. In the original Hawthorne Works studies (1920s-30s), worker productivity improved simply because researchers were paying attention — not because of any specific management change. For nudge evaluation: a nudge tested in a controlled experiment where participants know they're being studied may show effects that don't replicate at scale in production where no special attention is paid. The practical implication: run nudge experiments in realistic conditions, with large samples, over realistic time periods.

**Habituation and decay.** Nudges lose effectiveness as agents adapt. The social norm letter ("your neighbours use less energy") has strong initial effects that decay as recipients become familiar with the format and start to discount or ignore it. Defaults have strong initial effects but may be overcome as users become more familiar with the product and actively evaluate their choices. Unlike price incentives (which don't habituate — a $10 price is always $10), nudge effects can decay to near zero over time.

This has important design implications. A nudge that works powerfully in a first-week user experiment may have near-zero effect on the same user in week 52. Long-run evaluation periods are essential for understanding true nudge impact.

**Spillover effects.** A nudge in one domain can affect behaviour in adjacent domains through multiple mechanisms. Commitment device nudges (save more) might reduce consumption flexibility, which could increase financial stress in emergency situations (precisely when consumption flexibility is most valuable). A nudge that reduces calorie intake at lunch might increase calorie intake at dinner (compensation). A nudge that increases vaccination rates in one disease might crowd out vaccination decision-making bandwidth for other diseases.

These spillovers are essentially impossible to detect in narrow A/B tests that measure only the targeted outcome. They require broader evaluation frameworks — which is one of the genuine unsolved challenges in nudge policy design.

**Scale effects and aggregate impact.** A nudge that shifts behaviour for 3% of users sounds small. At the scale of a platform with 300 million monthly active users, 3% is 9 million people. The social impact of platform-scale nudges is enormous even when individual effect sizes are modest. This has two implications: (1) even small nudge improvements are worth implementing if they're genuinely welfare-improving; (2) even small nudges that are welfare-reducing at the individual level are worth regulating if they aggregate to large social harms.

---

## The Formal Picture

**Prospect Theory (Kahneman & Tversky, 1979).** People evaluate outcomes relative to a reference point, not in absolute terms. Gains feel good, but losses from the same reference point feel roughly 2.25 times worse — this is loss aversion. The sensitivity to additional gains or losses also diminishes: going from $0 to $10 feels better than going from $100 to $110, even though the monetary difference is the same. These two properties — loss aversion and diminishing sensitivity — explain default bias: switching away from a default feels like accepting a loss of the default option, while staying puts feels like the neutral reference point. Given that losses hurt 2.25 times more than equivalent gains feel good, the psychological pull to stay is substantial even when the alternative is objectively better.

**Default compliance.** Start with the fraction of people who would genuinely choose option A if the choice were presented neutrally with no default. Setting option A as the default adds a second group on top of that: people who would not have chosen A on their own, but follow the default out of inertia, loss aversion, or a sense that the default is the socially endorsed choice. Researchers call this additional pull the "default compliance rate." Empirically, defaults pull in 30–70% of people who would not otherwise have chosen the default option — depending on how much effort it takes to opt out, how salient the choice is, and how socially endorsed the default feels. Organ donation (high effort to opt out, socially endorsed default) sits near the upper end of that range. Cookie consent banners (often one click to decline) vary widely depending on how the interface is designed.

**Social norm adjustment.** When someone learns that their behaviour differs from the stated social norm — "your neighbours use 30% less electricity than you" — they tend to adjust toward the norm. The strength of this pull varies by person and context, but energy nudge experiments consistently find that the stated norm explains roughly 5–10% of the person's behaviour once they've had time to adjust. That sounds modest, but at the scale of millions of households, a 5% reduction in energy use from a cheap letter is economically significant.

**Donation default revenue calculation.** For a donation page experiment with three conditions and 1000 visitors:

- No suggested amount: 40% donate, average $25 → Revenue = 0.40 × $25 × 1000 = $10,000. Total donations: 400 donors.
- Default $10: 65% donate, average $12 → Revenue = 0.65 × $12 × 1000 = $7,800. Total donors: 650.
- Default $50: 45% donate, average $45 → Revenue = 0.45 × $45 × 1000 = $20,250. Total donors: 450.

The default-$50 condition maximises revenue but has fewer total donors than the default-$10 condition. Whether this is the "right" outcome depends on what the charity optimises for — revenue for programs or donor acquisition for long-run relationship building.

---

## Where It Breaks

**Nudges don't address root causes.** A nudge that increases retirement savings doesn't fix wage stagnation or medical debt — the structural reasons why many people can't save. Nudges are micro-interventions optimised for individual behaviour change; systemic problems require structural mechanisms (tax policy, healthcare policy, labour market regulation). Nudge advocates have been criticised — legitimately — for making small behavioural fixes to problems that require large structural changes, potentially substituting for rather than complementing structural interventions.

**Who decides "better"?** The organ donor default assumes that donation is the correct choice for most people. But religious beliefs, cultural practices, and personal convictions vary. The optimal default is heterogeneous across the population — but defaults must be uniform (you can't set a personalised default for each person without knowing their preferences, which defeats the purpose of the default). Any uniform default encodes a value judgment by the choice architect about what's better, which may not reflect individual preference diversity.

**The manipulation-guidance line is contested and shifting.** Thaler and Sunstein's definition — a nudge serves the agent's long-run interest, not the designer's interest at the agent's expense — is coherent in principle but contested in application. A streaming service that autoplay-starts the next episode is serving users who want to watch more (their stated long-run preference) but also serving the platform's engagement metrics. Whether autoplay is a helpful nudge or a dark pattern depends on a contested judgment about which "preference" is the user's "true" long-run preference. Regulators, courts, and researchers disagree — and the platform has a structural incentive to classify its own dark patterns as helpful nudges.

**Spillovers, long-run adaptation, and aggregate effects** are genuinely difficult to study and often inadequately accounted for. The evidence base for many specific nudges is strong in the short run and weak in the long run. Policymakers and product designers should demand long-run evaluation data before treating any nudge as a permanent solution.

---

## Try It Yourself

**Exercise 1 — Classification (20 min).** For each scenario below, identify: (a) the cognitive bias being targeted, (b) the choice architecture change being made, and (c) whether it is a nudge (serving user long-run interest) or a dark pattern (serving platform interest at user expense). Justify your classification.

- Scenario A: A streaming service automatically plays the next episode 15 seconds after the current episode ends, with a small cancel button visible during the countdown.
- Scenario B: A company's 401(k) plan automatically enrolls new employees at 6% contribution to a diversified target-date fund, with clear instructions to change the contribution rate or fund allocation.
- Scenario C: When a user clicks "cancel subscription," the confirmation page reads: "Are you sure you want to miss out on these exclusive savings and benefits?"
- Scenario D: A product page shows: "86% of verified buyers in your city rated this product 4 stars or above."
- Scenario E: A food delivery app shows the calorie count of each item in the same font size as the price, without requiring any interaction to view it.

**Exercise 2 — Design (30 min).** Design a nudge for a health app to increase daily step counts among users averaging fewer than 3,000 steps per day. Your design should address the following components:

1. **Cognitive target:** Which specific cognitive bias or heuristic is your nudge targeting? Why do you expect this bias to be present in this population?
2. **Choice architecture change:** What specifically changes in the app interface? Be precise — describe exactly what the user sees, when they see it, and what their options are.
3. **A/B test design (preview of Day 53):** How would you test whether this nudge works? Describe: the randomisation unit, the control condition, the primary outcome metric, and the time horizon over which you'd measure.
4. **Dark pattern failure mode:** What is the closest dark pattern analog to your nudge? If you wanted to deploy the same mechanism to harm users (increase screen time compulsively rather than increase healthy behaviour), how would you modify it? How does recognising this analog help you design the benign version more carefully?

**Exercise 3 — Quantitative (20 min).** A nonprofit charity tests three donation page designs:

- **Condition A (neutral):** No suggested donation amount. Result: 40% of visitors donate, average donation $25.
- **Condition B (low default):** Suggested donation defaulted to $10, other amounts clearly available. Result: 65% donate, average donation $12.
- **Condition C (high default):** Suggested donation defaulted to $50, other amounts clearly available. Result: 45% donate, average donation $45.

Per 1,000 page visitors:

1. Compute total revenue for each condition.
2. Compute total number of donors for each condition.
3. Compute average revenue per donor for each condition.
4. The charity has two objectives: (i) maximise revenue for current programs, (ii) maximise donor count for long-run relationship building and recurring giving. Which condition best serves each objective? Can you design a fourth condition that might do better on both simultaneously?
5. Interpret the difference between Condition A and Condition B through the lens of prospect theory. Why would a $10 default increase donor count so substantially even though the average donation falls?

---

## Connect It Back

Nudges are the soft end of the behaviour design toolkit. They harness the cognitive architecture of human decision-making rather than changing payoffs or restricting choices. As a mechanism design instrument, they sit at one end of a spectrum:

- **Hard mechanisms (Days 25-34):** change payoffs, allocate resources, assign rights. Auctions, matching, voting. Agents respond to incentive structures.
- **Information design (Day 39):** change what agents know before they choose. Agents respond to updated beliefs.
- **Platform mechanisms (Day 51):** deploy hard mechanisms at market scale. Ranking, pricing, access. Agents respond to algorithmic rules.
- **Nudges (today):** change how choices are presented without changing payoffs or information content. Agents respond to cognitive architecture.

In practice, effective behaviour design combines all four. A retirement savings system might use tax incentives (hard mechanism), employer contribution matching (incentive design), personalised projections showing future account balances (information design), and automatic enrollment defaults (nudge). The full toolkit is available to the AI product designer — and understanding all four levers, their interactions, and their ethical limits is what separates thoughtful product design from accidental or adversarial architecture.

Tomorrow (Day 53): A/B testing as sequential decision-making. Every nudge, platform mechanism, and product change you've studied needs empirical evaluation. How do you design experiments that give reliable causal estimates? What are the statistical traps? And how does sequential testing connect back to the multi-armed bandit and exploration-exploitation problems from Module 4's RL section?

---

## Suggested Readings

- **Primary:** Thaler, Richard H. & Sunstein, Cass R. "Nudge." Chapter 1 (Biases and Blunders) and Chapter 5 (How to Design a Better Organ Donation System). Both are short, clear, and directly relevant.
- **Ethics depth:** Sunstein, Cass R. "The Ethics of Nudging." Yale Journal on Regulation, 32(2):413–450, 2015. Addresses the paternalism objection directly. Read Sections I-III.
- **Dark patterns:** Brignull, Harry et al. "Deceptive Design" at deceptive.design. A catalogued, illustrative reference — browse 10-15 examples before tomorrow's session.
- **Evidence base:** Benartzi, Shlomo & Thaler, Richard. "Heuristics and Biases in Retirement Savings Behaviour." Journal of Economic Perspectives, 21(3):81–104, 2007. The SMarT plan evidence and broader savings nudge research.
- **Critical perspective:** Leggett, Will. "The Politics of Behaviour Change: Nudge, Neoliberalism and the State." Policy & Politics, 42(1):3–19, 2014. A critique of nudge as a substitute for structural policy — important for intellectual balance.


---

← [Day 51 — Platform Mechanism Design](day-51-platform-mechanism-design) &nbsp;|&nbsp; [Day 53 — A/B Testing as Sequential Decision-Making →](day-53-ab-testing)
