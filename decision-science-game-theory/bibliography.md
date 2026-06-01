# Bibliography — Annotated Course Shelf

> Every source listed here has been verified to exist. Sources marked `[VERIFY]` should be confirmed before citing. Full citation format: Author(s), *Title*, Publisher, Year — plus chapter/section/timestamp pointers and a role annotation.

---

## Foundational Books

### 1. Kahneman, Daniel. *Thinking, Fast and Slow*. Farrar, Straus and Giroux, 2011.

**Role in course:** Spine for Module 1 (Days 1–13). The definitive popular-academic treatment of dual-process theory, cognitive biases, Prospect Theory, and the limits of rational models.

**Coverage:** Part I (Systems 1 and 2) maps to Days 1 and 6–7. Part II (Heuristics and Biases) maps to Days 6–7. Part III (Overconfidence) maps to Day 4. Part IV (Choices, Prospect Theory) maps to Days 3 and 8. Part V (Two Selves) rounds out Day 12.

**Hardest section:** Chapter 25 (Bernoulli's errors) — requires patience with the formalism, but it's worth it; it's the most important 15 pages in the book for this course.

**Favoured intuition:** Kahneman's intuitions are always empirical — built from decades of lab experiments. He will make you doubt yourself productively.

**Days relying on it most:** 1, 3, 6, 7, 8.

---

### 2. Dixit, Avinash K. & Nalebuff, Barry J. *The Art of Strategy: A Game Theorist's Guide to Success in Business and Life*. W.W. Norton, 2008.

**Role in course:** Primary intuition engine for Module 2 (Days 14–27). Extraordinary at building strategic instinct through vivid, real-world examples before any formalism. Read this alongside Tadelis — Dixit & Nalebuff give you the picture, Tadelis gives you the proof.

**Coverage:** Chapter 1 (Ten Tales of Strategy) maps to Day 14. Chapters 2–3 (Dominant Strategies, Nash) map to Days 15–16. Chapter 4 (Beautiful Mind) maps to Day 18. Chapter 6 (Sequential games) maps to Day 19. Chapter 7 (Commitment) maps to Day 20. Chapter 10 (Auctioning and Bidding) maps to Day 26.

**Hardest section:** Chapter 11 (Bargaining) — the formal model comes late; supplement with Day 25's treatment.

**Favoured intuition:** Business-world analogies and political examples. Excellent for connecting theory to product strategy decisions.

**Days relying on it most:** 14, 15, 16, 17, 19, 20, 26.

---

### 3. Tadelis, Steven. *Game Theory: An Introduction*. Princeton University Press, 2013.

**Role in course:** Formal backbone for Modules 2–3 (Days 14–39). Rigorous enough for L2 builders, clear enough for practitioners. The most accessible graduate-level game theory text available. Proofs are included but cleanly motivated.

**Coverage:** Part I (Rational Decision-Making) maps to Days 2–3. Part II (Static Games) maps to Days 15–18. Part III (Dynamic Games) maps to Days 19–20. Part IV (Incomplete Information) maps to Days 27–29. Part V (Advanced Topics) maps to Days 30, 36–38.

**Hardest section:** Chapter 12 (Repeated Games with Incomplete Information) — treat as optional deep dive for L2.

**Favoured intuition:** Mathematical rigour with deliberate pacing. Every definition is motivated before being stated. If Dixit & Nalebuff is the novel, Tadelis is the textbook.

**Days relying on it most:** 16, 18, 19, 25, 27, 28, 29, 30.

---

### 4. Sutton, Richard S. & Barto, Andrew G. *Reinforcement Learning: An Introduction*. 2nd ed. MIT Press, 2018.

**Free PDF:** `http://incompleteideas.net/book/the-book-2nd.html`

**Role in course:** Spine for Module 4 (Days 41–50). The authoritative RL textbook. Every AI builder who works with RL needs this on their shelf. We use a focused subset — the full book is 500+ pages.

**Coverage:** Chapter 1 (Introduction) maps to Day 41. Chapter 2 (Multi-Armed Bandits) maps to Day 43. Chapters 3–4 (MDP, Dynamic Programming) map to Day 44. Chapter 6 (TD Learning, Q-Learning) maps to Day 45. Chapter 13 (Policy Gradient Methods) maps to Day 46. Chapter 16 (Applications and Case Studies) enriches Day 47.

**Hardest section:** Chapter 9 (On-policy Approximation with Function Approximation) — skip on first pass unless you're implementing deep RL.

**Favoured intuition:** Engineering-first. Sutton & Barto always motivate algorithms from the agent's experience, not from equations downward.

**Days relying on it most:** 41, 43, 44, 45, 46, 47.

---

### 5. Thaler, Richard H. & Sunstein, Cass R. *Nudge: Improving Decisions About Health, Wealth, and Happiness*. Yale University Press, 2008. (Revised & Expanded ed., Penguin, 2021 also available.)

**Role in course:** Primary source for Day 53 and the behavioural design thread through Modules 4–5. The book that created behavioural product design as a discipline.

**Coverage:** Part I (Humans and Econs) reinforces Days 6–8 with policy framing. Part II (Money) maps to Day 57 (portfolio thinking). Part III (Health) maps to Day 53 (nudge architecture). Part IV (Objections) maps to Day 62 (decision hygiene).

**Hardest section:** The chapters on retirement savings — dense with US-specific policy, but the *principles* transfer universally.

**Favoured intuition:** Policy and product examples, never mathematical. Complements the more formal treatments in Module 1.

**Days relying on it most:** 53, 58, 62.

---

## Landmark Papers

### 1. Nash, John F. "Equilibrium Points in N-Person Games." *Proceedings of the National Academy of Sciences*, 36(1):48–49, 1950.

**Why it matters:** The two-page paper that launched modern game theory. Nash proves existence of equilibrium in any finite game using Kakutani's fixed-point theorem. Read it in full — it is genuinely two pages.

**Where to find it:** PNAS digital archive; also freely available via JSTOR.

**Day:** 16 (primary source). Read on the evening of Day 16 or morning of Day 17.

---

### 2. Kahneman, Daniel & Tversky, Amos. "Prospect Theory: An Analysis of Decision under Risk." *Econometrica*, 47(2):263–291, 1979.

**Why it matters:** One of the most-cited papers in social science. Introduces the value function (S-shaped, asymmetric), the probability weighting function, and the reference dependence framework that replaces Expected Utility as the *descriptive* model.

**Where to find it:** JSTOR; also available at `prospect-theory.com` (fan archive — verify access).

**Read:** Sections I–IV (pages 263–279). Section V (the axioms) is for L3 readers only.

**Day:** 8 (primary source).

---

### 3. Axelrod, Robert & Hamilton, William D. "The Evolution of Cooperation." *Science*, 211(4489):1390–1396, 1981.

**Why it matters:** Shows that Tit-for-Tat wins iterated Prisoner's Dilemma tournaments — cooperation can emerge from selfish agents without central enforcement. The foundation of evolutionary game theory.

**Where to find it:** Science magazine archive; widely available on research-gate and institutional access.

**Day:** 23 (primary source). Read in full — 6 pages.

---

### 4. Myerson, Roger B. "Optimal Auction Design." *Mathematics of Operations Research*, 6(1):58–73, 1981.

**Why it matters:** The mechanism design paper that earned Myerson a share of the 2007 Nobel Prize. Proves that revenue-optimal auctions must be incentive-compatible, and derives the optimal reserve price.

**Where to find it:** JSTOR; Myerson's website at University of Chicago.

**Read:** Introduction and Section 2 for intuition. Section 3 (the proof) is for L2 builders.

**Days:** 26 (background), 30 (primary source for mechanism design formalism).

---

### 5. Vickrey, William. "Counterspeculation, Auctions, and Competitive Sealed Tenders." *Journal of Finance*, 16(1):8–37, 1961.

**Why it matters:** Introduced the second-price (Vickrey) auction and the principle of incentive compatibility — bidding your true value is a dominant strategy. Google's ad auction is a Vickrey auction at scale.

**Where to find it:** JSTOR; Wiley Online Library.

**Read:** Sections I–III (pages 8–20). The "competitive sealed tenders" section is less relevant.

**Day:** 26 (primary source for auction theory).

---

### 6. Mnih, Volodymyr et al. "Human-level control through deep reinforcement learning." *Nature*, 518:529–533, 2015. DOI: `10.1038/nature14236`.

**Why it matters:** The DQN paper — the moment deep RL became real. A single algorithm (Q-learning + convolutional networks + experience replay) achieves human-level performance across 49 Atari games. The paper that triggered the modern RL research wave.

**Where to find it:** `nature.com/articles/nature14236`; preprint on DeepMind's website.

**Read:** Main paper (5 pages) + Methods section. The supplementary data shows the per-game performance and is worth scanning.

**Day:** 45 (supplementary reading after Q-Learning page).

---

### 7. Shapley, Lloyd S. & Shubik, Martin. "A Method for Evaluating the Distribution of Power in a Committee System." *American Political Science Review*, 48(3):787–792, 1954.

**Why it matters:** Original Shapley value paper — 6 pages, readable in full. The Shapley-Shubik power index operationalises the abstract Shapley value for voting games. Directly ancestral to SHAP explainability in ML.

**Where to find it:** Cambridge University Press / APSR archive; JSTOR.

**Day:** 37 (primary source for the Shapley value).

---

### 8. Roughgarden, Tim. "Algorithmic Game Theory." *Communications of the ACM*, 53(7):78–86, 2010. `[VERIFY — confirm volume/page via ACM Digital Library]`

**Why it matters:** Accessible 8-page survey of the Price of Anarchy, mechanism design complexity, and the interface between CS theory and game theory. Written for a computer science audience — good bridge article before Day 39.

**Day:** 39 (preparatory reading).

---

### 9. Christiano, Paul et al. "Deep Reinforcement Learning from Human Preferences." *Advances in Neural Information Processing Systems (NeurIPS)*, 30, 2017. arXiv: `1706.03741`.

**Why it matters:** The paper that gave RLHF its modern form: a human comparator labelling pairs of trajectory clips, a reward model trained on those preferences, and PPO fine-tuning — exactly the pipeline used in InstructGPT and GPT-4. Highly readable at 9 pages.

**Where to find it:** `arxiv.org/abs/1706.03741`; also on OpenAI's blog.

**Read:** Sections 1–3 (motivation and method). Section 4 (experiments on Atari and MuJoCo) is instructive but not required for the RLHF conceptual foundation.

**Day:** 50 (primary source for RLHF pipeline).

---

### 10. Bai, Yuntao et al. "Constitutional AI: Harmlessness from AI Feedback." Anthropic, 2022. arXiv: `2212.08073`.

**Why it matters:** Introduces Constitutional AI (CAI): having the model critique and revise its own outputs using a written constitution, then using AI-generated preference labels (rather than human harm labels) to train the reward model. Reduces reliance on human labellers for sensitive content, and introduces the idea of principle-based alignment.

**Where to find it:** `arxiv.org/abs/2212.08073`; Anthropic research page.

**Read:** Abstract, Introduction, and Sections 2–3. The full experimental detail is extensive — focus on the two-phase CAI pipeline description.

**Day:** 50 (primary source for Constitutional AI).

---

### 11. Rochet, Jean-Charles & Tirole, Jean. "Platform Competition in Two-Sided Markets." *Journal of the European Economic Association*, 1(4):990–1029, 2003.

**Why it matters:** The foundational paper on two-sided market economics. Derives the Rochet-Tirole pricing formula, explains why platform pricing defies single-sided intuitions (subsidise one side, monetise the other), and shows why the allocation of costs between sides depends on cross-side elasticities.

**Where to find it:** JSTOR; MIT Press Journals; Jean Tirole's website at Toulouse School of Economics.

**Read:** Sections 1–3 (model setup and main results). Section 4 (welfare analysis) is for L3 readers.

**Day:** 51 (primary source for platform mechanism design).

---

### 12. Granovetter, Mark S. "The Strength of Weak Ties." *American Journal of Sociology*, 78(6):1360–1380, 1973.

**Why it matters:** One of the most-cited papers in sociology. Demonstrates empirically that weak acquaintances (not close friends) are the primary channel through which new job opportunities and novel information flow — because they bridge different social clusters. Directly foundational to Day 57's network strategy content.

**Where to find it:** JSTOR; University of Chicago Press Journals.

**Read:** In full — 20 pages, highly readable. Sections 1–3 build the theory; Section 4 applies it to job search.

**Day:** 57 (primary source for network strategy and structural holes).

---

### 13. Markowitz, Harry. "Portfolio Selection." *Journal of Finance*, 7(1):77–91, 1952.

**Why it matters:** The 14-page paper that created modern portfolio theory. Proves that rational investors should choose portfolios on the efficient frontier (maximum return for given risk), and that diversification reduces variance without proportionally reducing expected return. The conceptual spine of Day 62.

**Where to find it:** JSTOR; Wiley Online Library.

**Read:** In full — 14 pages, mathematically accessible. The mean-variance framework is presented with full clarity.

**Day:** 62 (primary source for portfolio thinking and the efficient frontier).

---

### 14. Johari, Ramesh, Pete Koomen, Leonid Pekelis & David Walsh. "Peeking at A/B Tests: Why It Matters, and What to Do About It." *Proceedings of the 23rd ACM SIGKDD International Conference on Knowledge Discovery and Data Mining*, pp. 1517–1525, 2017. DOI: `10.1145/3097983.3097992`.

**Why it matters:** The paper that formalised the "peeking problem" in A/B testing and introduced mSPRT (mixture Sequential Probability Ratio Test) as an always-valid inference procedure — allowing practitioners to peek at results continuously without inflating Type I error rates. Used daily at LinkedIn, Airbnb, and most major tech platforms.

**Where to find it:** ACM Digital Library; preprint at `arxiv.org/abs/1512.04922`.

**Read:** Sections 1–4 (problem formulation and the mSPRT construction). Section 5 (empirical validation at LinkedIn) demonstrates the practical stakes.

**Day:** 53 (primary source for sequential A/B testing).

---

### 15. Flyvbjerg, Bent. "From Nobel Prize to Project Management: Getting Risks Right." *Project Management Journal*, 37(3):5–15, 2006.

**Why it matters:** Formalises reference class forecasting into a four-step practitioner method, drawing on decades of infrastructure project data to show that the planning fallacy is systematic and quantifiable — and directly correctable by outside-view base rates. The methodological companion to Kahneman's inside/outside view distinction.

**Where to find it:** Wiley Online Library; also available on Flyvbjerg's faculty page at Oxford Said Business School.

**Read:** In full — 10 pages. The four-step method (Sections 3–4) is directly actionable.

**Day:** 60 (primary source for reference class forecasting).

---

## Video Lectures & Courses

### 1. Polak, Ben. *Game Theory (ECON 159)*. Yale Open Courses, 2007.
**Access:** `oyc.yale.edu/economics/econ-159` and YouTube (search "Yale Game Theory Ben Polak").
**Format:** 24 lectures, ~75 min each.

**Why it's here:** The clearest spoken introduction to game theory in existence. Polak is a superb teacher who does blackboard derivations slowly and carefully, fields student questions live, and connects theory to real examples.

**Course mapping:**
- Lectures 1–3 → Days 14–16
- Lectures 4–6 → Days 17–18
- Lectures 7–9 → Days 19–20
- Lectures 10–12 → Days 21, 23
- Lectures 14–16 → Days 25–27
- Lectures 19–21 → Days 28–30

---

### 2. Silver, David. *Introduction to Reinforcement Learning*. DeepMind / UCL, 2015.
**Access:** YouTube (search "David Silver RL UCL 2015" or "DeepMind x UCL RL").
**Format:** 10 lectures, ~90 min each.

**Why it's here:** The canonical RL course from the lead researcher behind AlphaGo. Silver's lectures are precise, well-paced, and perfectly aligned with Sutton & Barto.

**Course mapping:**
- Lecture 1 (Introduction to RL) → Day 41
- Lecture 2 (MDPs) → Day 44
- Lecture 3 (Dynamic Programming) → Day 44 extension
- Lecture 4 (Model-Free Prediction) → Day 45
- Lecture 5 (Model-Free Control) → Day 45 extension
- Lecture 7 (Policy Gradient) → Day 46

---

### 3. Brunskill, Emma. *CS234: Reinforcement Learning*. Stanford University, 2023.
**Access:** YouTube (Stanford Online channel, search "CS234 2023").
**Format:** ~16 lectures, ~75 min each.

**Why it's here:** More recent than Silver; covers bandits, RLHF, and offline RL. Excellent for the AI product focus of Module 4.

**Course mapping:**
- Lecture 2 (Bandits) → Day 43
- Lectures 3–5 (MDPs, Q-Learning) → Days 44–45
- Lecture 10 (Policy Gradient) → Day 46
- Lecture 14 (RLHF / Alignment) → Day 52

---

### 4. Case, Nicky. *The Evolution of Trust*. Interactive web game, 2017.
**Access:** `ncase.me/trust`
**Format:** 30-minute interactive simulation.

**Why it's here:** The best single piece of game theory pedagogy on the internet. Simulates iterated Prisoner's Dilemma tournaments, lets you play different strategies, and builds deep intuition for why cooperation emerges. Not a lecture — play it.

**Course mapping:** Play the night before Day 21 or Day 23.

---

### 5. Thrun, Sebastian & Norvig, Peter. *CS221: Artificial Intelligence — Principles and Techniques*. Stanford, various years.
**Access:** YouTube (Stanford Online channel, search "CS221 Stanford AI").
**Format:** Selected lectures.

**Why it's here:** The lectures on MDPs and utility theory offer an alternative exposition to Silver — useful if Silver's framing doesn't click.

**Course mapping:**
- MDP lecture → Day 44 (alternative framing)
- Utility / decision theory lecture → Day 3 (alternative framing)

---

---

## Addendum A — Share Market Investing

### Malkiel, Burton G. *A Random Walk Down Wall Street*. 13th ed. W.W. Norton & Company, 2024.

**Role in addendum:** The primary practical spine of Addendum A. The definitive lay-academic treatment of the Efficient Market Hypothesis and its implications for individual investors. Malkiel synthesises fifty years of empirical finance research into a coherent investment philosophy without requiring mathematical sophistication.

**Coverage:** Part I (Stocks and Their Value) builds the case against technical and fundamental analysis. Part III (The New Investment Technology) covers Modern Portfolio Theory and CAPM. Part IV (A Practical Guide) gives the actionable recommendations — tax-advantaged accounts, index funds, rebalancing.

**Hardest section:** Chapter 9 (Behavioural Finance) — requires familiarity with the Module 1 biases to appreciate fully.

**Favoured intuition:** Institutional and empirical. Malkiel's evidence accumulates rather than theorises. After this book, the burden of proof rests with the active manager.

**Addendum sections relying on it most:** The EMH as Nash equilibrium, the practical investor recommendations.

---

### Shiller, Robert J. *Irrational Exuberance*. 3rd ed. Princeton University Press, 2015.

**Role in addendum:** The counterweight to Malkiel — Shiller's case that markets are driven by narrative and psychology, and that prices can deviate from fundamentals for long periods. Both Malkiel and Shiller are correct; they are describing different aspects of the same market.

**Coverage:** Chapter 1 (The Stock Market Level in Historical Perspective) introduces CAPE. Chapters 3–4 (Precipitating Factors, Amplification Mechanisms) trace how bubbles form. Chapter 8 (Psychological Anchors) connects directly to Modules 1 and 5.

**Hardest section:** Chapter 11 (Efficient Markets, Random Walks, and Bubbles) — Shiller's direct engagement with the EMH. Worth reading after Malkiel.

**Favoured intuition:** Historical and narrative. Shiller's charts are among the most instructive in all of finance.

**Addendum sections relying on it most:** Behavioural biases as mispricings; bubbles as coordination equilibria.

---

### Grossman, Sanford J. & Stiglitz, Joseph E. "On the Impossibility of Informationally Efficient Markets." *American Economic Review*, 70(3):393–408, 1980.

**Why it matters:** The formal proof that perfect price efficiency is impossible: if prices fully reflected all information, no one would pay to collect it, causing prices to stop reflecting information. The equilibrium is partial efficiency, where the marginal informed trader earns just enough to cover their information costs. The theoretical foundation for understanding *why* active management survives even though most active managers underperform.

**Where to find it:** JSTOR; AEA website.

**Read:** Pages 393–400 (the model and main result). The technical appendix is for L3 readers.

**Addendum section:** EMH as Nash equilibrium.

---

### Barber, Brad M. & Odean, Terrance. "Trading Is Hazardous to Your Wealth: The Common Stock Investment Performance of Individual Investors." *Journal of Finance*, 55(2):773–806, 2000. DOI: `10.1111/0022-1082.00226`.

**Why it matters:** The landmark empirical paper on retail investor underperformance. Using brokerage account data for 66,465 US households, Barber & Odean show that the most active 20% of traders earn 6.5% per year less than the least active 20% — driven by overconfidence and transaction costs, not bad stock picking. The data is from the 1990s; the behavioural mechanism is timeless and has been replicated across markets worldwide.

**Where to find it:** Wiley Online Library; Odean's faculty page at UC Berkeley.

**Read:** Introduction, Section II (Data), and Section IV (Results). Section III is the statistical methodology — skim unless L2.

**Addendum section:** Overconfidence and its market costs.

---

### Harris, Larry. *Trading and Exchanges: Market Microstructure for Practitioners*. Oxford University Press, 2003.

**Role in addendum:** The definitive reference on how equity markets actually work mechanically — order books, market makers, bid-ask spreads, transaction costs, and the information economics of trading. Dense but comprehensive; the relevant chapters can be read in isolation.

**Coverage:** Chapters 1–4 (Introduction to Trading and Market Structure) explain the mechanics. Chapter 9 (Why People Trade) is particularly useful — it classifies traders by their information status and explains why spreads exist. Chapter 14 (Liquidity) connects to transaction cost analysis.

**Hardest section:** Chapters 17–20 on market design and regulation — relevant for L3 readers interested in exchange design as a mechanism design problem.

**Favoured intuition:** Practitioner-first. Harris builds from the mechanics of a trading desk outward to theory.

**Addendum section:** The market as a mechanism; bid-ask spreads; limit vs. market orders.

---

## Optional Deeper Dives

| Work | Why it's worth it | After which day |
|------|-------------------|-----------------|
| Russell, Stuart. *Human Compatible: Artificial Intelligence and the Problem of Control*. Viking, 2019. | The most compelling book-length argument for preference-based AI and alignment by design. Deepens Days 50 and 54 significantly. | Day 50 |
| Axelrod, Robert. *The Evolution of Cooperation*. Basic Books, 1984. | Full book treatment of the tournament results summarised in the 1981 paper. Richer case studies and implications. | Day 23 |
| Roughgarden, Tim. *Twenty Lectures on Algorithmic Game Theory*. Cambridge University Press, 2016. Free: `timroughgarden.org`. | Graduate textbook for Day 38's Price of Anarchy material. Chapters 1–5 are accessible; go deeper for L3. | Day 38 |
| Tetlock, Philip & Gardner, Dan. *Superforecasting: The Art and Science of Prediction*. Crown, 2015. | The empirical backbone of Day 61 — the Good Judgment Project results, how superforecasters think, and what separates them from experts. Read before or after Day 61. | Day 61 |
| Osborne, Martin J. *An Introduction to Game Theory*. Oxford University Press, 2004. Free PDF: `economics.utoronto.ca/osborne/igt/`. | A clean, freely available alternative to Tadelis for the formal game theory material. Excellent exercises. | Day 14 |
| Fudenberg, Drew & Tirole, Jean. *Game Theory*. MIT Press, 1991. | The graduate-level reference — encyclopaedic and rigorous. For L3 readers or when Tadelis leaves gaps. | Day 38 |
| Milgrom, Paul. *Putting Auction Theory to Work*. Cambridge University Press, 2004. | Deep dive into auction design for practitioners — spectrum auctions, combinatorial auctions. After Day 26 for L2. | Day 26 |
| Burt, Ronald S. *Structural Holes: The Social Structure of Competition*. Harvard University Press, 1992. | Book-length treatment of brokerage, information arbitrage, and network positioning strategy — extends Day 57's content for anyone thinking seriously about career or organisational network design. | Day 57 |
| Ariely, Dan & Wertenbroch, Klaus. "Procrastination, Deadlines, and Performance: Self-Control by Precommitment." *Psychological Science*, 13(3):219–224, 2002. | The clean experimental evidence behind commitment devices: students given self-imposed deadlines outperform those with free choice, even when the deadlines are binding. Six pages, directly actionable after Day 58. | Day 58 |
| Laibson, David. "Golden Eggs and Hyperbolic Discounting." *Quarterly Journal of Economics*, 112(2):443–477, 1997. | The landmark economics paper formalising quasi-hyperbolic discounting and its implications for savings, consumption, and commitment devices. More technical than Day 58 — for L2/L3 readers wanting the formal model. | Day 58 |
| Mnih, Volodymyr et al. "Playing Atari with Deep Reinforcement Learning." arXiv: `1312.5602`, 2013. | The DQN preprint (precursor to the 2015 Nature paper, Landmark Paper 6). Explains experience replay and fixed Q-targets in more pedagogical detail. Useful supplement after Days 44–45. | Day 45 |
| Silver, David et al. "Mastering the Game of Go without Human Knowledge." *Nature*, 550:354–359, 2017. DOI: `10.1038/nature24270`. | AlphaGo Zero — self-play from scratch using MCTS + neural network, no human data. The clearest demonstration that multi-agent self-play produces superhuman policies. Directly relevant to Days 48 and 54. | Day 48 |
| Kahneman, Daniel, Lovallo, Dan & Sibony, Olivier. "Before You Make That Big Decision…" *Harvard Business Review*, June 2011. | Practical 12-question checklist for auditing strategic recommendations for cognitive bias — the decision-hygiene toolkit from Day 60 operationalised for organisations. 10 pages, immediately applicable. | Day 60 |