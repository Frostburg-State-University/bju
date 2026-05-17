---
title: "Lecture 7 — Risk & Decision under Uncertainty"
author: Dr. Zhijiang Chen
date: 2026-06-09
session: 7
week: 15
room: YF108
---

# Lecture 7 — Risk & Decision under Uncertainty

!!! info "Session Info"
    **Date:** Tuesday, 2026-06-09 &nbsp;·&nbsp; **Time:** 16:20–18:10 &nbsp;·&nbsp; **Room:** YF108
    **Instructor:** Dr. Zhijiang Chen

[**▶ Open the lecture slides — 110-minute web deck**](slides/lecture-07.html){ .md-button .md-button--primary target="_blank" }

!!! tip "Using the slide deck"
    The slides are a self-contained web presentation (Reveal.js, no install). Keyboard shortcuts: **F** for full-screen, **S** for speaker view, **Esc** for slide overview, **arrow keys** to navigate. Designed for a single 110-minute session.

---

## Learning Objectives

By the end of this lecture, you should be able to:

1. **Distinguish risk from uncertainty** in the Knightian sense — knowing when probabilities are observable, when they are subjective, and why software economics lives mostly in the latter regime.
2. **Build and roll back a decision tree** using the three node types ($\square$ decision, $\bigcirc$ chance, $\triangle$ terminal), and explain why information has measurable **option value**.
3. Compute **Expected Monetary Value (EMV)** for any branch and identify when EMV alone is the wrong objective.
4. Use a **utility function** to encode risk attitude — concave for risk-averse, linear for risk-neutral, convex for risk-seeking — and translate between EMV and expected utility.
5. Design and interpret a **Monte Carlo simulation** of an NPV model: pick distributions, sample, summarise as $P_{10}/P_{50}/P_{90}$ and $P(\text{NPV} > 0)$.
6. Catalogue the **seven software-specific risk categories** (schedule, scope, technical, personnel, vendor, model drift, regulatory) and the **three estimation biases** (optimism, anchoring, planning fallacy) that systematically distort subjective probabilities.

---

## Lecture Map — Five Movements, One Distribution

By the end of today, *single-number* answers like "the NPV is $112K" should feel embarrassing to you. Every input is a distribution; every output deserves to be one too.

| § | Topic | Minutes |
|---|-------|--------:|
| I | Risk vs uncertainty — Knight's vocabulary | 10 |
| II | Decision trees & expected monetary value | 25 |
| III | Utility & risk attitude | 15 |
| IV | Monte Carlo simulation | 25 |
| — | Discussion: find your risk attitude | 10 |
| V | Software-specific risks & estimation bias | 15 |
| — | HW7, Q&A | 10 |

!!! note "Prerequisite from Lectures 4–6"
    Today assumes you are comfortable computing **NPV** for a deterministic cash flow at a given **MARR**. Every technique below either decorates that calculation with probabilities (decision trees, Monte Carlo) or adjusts the objective function on top of it (utility). If NPV does not yet feel automatic, re-read Lecture 5 before the in-class exercise.

---

## § I. Risk vs Uncertainty — The Knightian Distinction

In 1921 the Chicago economist **Frank Knight** drew a line that the textbooks have respected ever since. The line is not pedantic — it tells you which mathematical machinery is honest to use.

### Two regimes, one decision

| | **Risk** | **Uncertainty** (true / Knightian) |
|---|----------|------------------------------------|
| Outcomes | Uncertain | Uncertain |
| Probabilities | **Known** (objective, frequency-based) | **Unknown** (no reliable frequency to count) |
| Example | A fair die · a deployment with a measured 2% failure rate · insurance pricing | A new AI feature's adoption · a regulator's reaction · whether a new framework will catch on |
| Honest tool | Expected value, classical probability | Subjective probability, scenario thinking, real options |

The crucial admission for our field: **most software-economics problems are technically uncertainty wearing a risk costume.** We write down probabilities like 0.4 and 0.6, but those numbers come from judgement, analogy, and the occasional spreadsheet — not from rolling the die ten thousand times.

!!! quote "Working principle for this course"
    Even bad probability estimates beat unstated ones. The discipline of *writing down a number* — and being held to it — forces the team to argue about the right thing instead of the loudest voice in the room.

### Why we use probabilities anyway

Three reasons to push through and assign numbers:

1. **Forced clarity.** "Likely" and "unlikely" hide disagreement. "0.4" exposes it.
2. **Composability.** Subjective probabilities compose with the rules of probability theory; vibes do not.
3. **Calibration over time.** Teams that record predicted probabilities can — within a year of trying — check their own calibration and improve it. Vague forecasters cannot.

The honest stance, then, is: **treat your numbers as the best current guess, document the reasoning, and revisit when new evidence arrives.** That is the Bayesian spirit, even when we do not write the equations.

---

## § II. Decision Trees & Expected Monetary Value

A decision tree is the simplest model that puts decisions, chance events, and payoffs in the same picture. It forces three good habits: making your options explicit, attaching probabilities to events nature controls, and writing down what each end-state is actually worth.

### Three node types — a one-line grammar

| Symbol | Node | Who controls it | How to evaluate (rollback) |
|:------:|------|-----------------|----------------------------|
| $\square$ | **Decision** | You — the engineer or owner | Pick the branch with the **maximum** expected value (or utility). |
| $\bigcirc$ | **Chance** | Nature — the world | Compute the **probability-weighted sum** of the child branches' values. |
| $\triangle$ | **Terminal** | Neither | Record the **payoff** (or cost) in PV terms. |

Build the tree **left-to-right in time order** (today's decision first; the future to the right). Solve **right-to-left** — this is the **rollback** (or "backward induction") procedure. At every $\bigcirc$ node, take the expectation; at every $\square$ node, take the max.

### Worked example — Ship now, or study first?

Your team is debating an AI feature. You have two top-level options:

- **A. Ship now.** Spend **$60K** on development. If demand is high, the feature returns **+$300K** in NPV; if low, **−$60K** in NPV (the build is wasted; some support drag remains). Your prior is **P(high) = 0.4**.
- **B. Study first.** Spend **$40K** on a 6-week customer-discovery study. The study returns a "go" or "no-go" signal; if "go", you ship and earn the conditional outcome; if "no-go", you walk away with no further loss.

Let's compute EMV on branch A first — the baseline.

#### Branch A — Ship now

| Outcome | Probability | NPV | Contribution |
|---------|-------------:|-----:|-------------:|
| High demand | $0.4$ | $+\$300\text{K}$ | $+\$120\text{K}$ |
| Low demand | $0.6$ | $-\$60\text{K}$ | $-\$36\text{K}$ |
| **EMV(A)** | | | $\boxed{+\$84\text{K}}$ |

The build is positive-EMV — but with a 60% chance of losing $60K, plenty of risk-averse owners would still hesitate.

#### Branch B — Study, then decide

The study clarifies demand. Suppose it is a calibrated test: when demand really is high, it shouts "go" with probability $0.85$; when demand is low, it correctly says "no-go" with probability $0.80$. Using Bayes:

- $P(\text{"go"}) = 0.4 \cdot 0.85 + 0.6 \cdot 0.20 = 0.34 + 0.12 = 0.46$
- $P(\text{high} \mid \text{"go"}) = (0.4 \cdot 0.85) / 0.46 \approx 0.74$

Rolling back the ship-if-go subtree at the conditional probabilities, the conditional EMV is $0.74 \cdot 300 + 0.26 \cdot (-60) - 60 \approx +\$148\text{K}$. Multiplied by $P(\text{"go"})$ and combined with the no-go branch (zero further loss), and netting the **$40K study cost**, the rolled-back tree gives:

$$
\text{EMV}(B) \approx -40 + 0.46 \cdot 148 + 0.54 \cdot 0 \approx +\$108\text{K}
$$

| Option | EMV | What it costs | What it buys |
|--------|----:|---------------|--------------|
| A — Ship now | $+\$84\text{K}$ | Build now | Speed |
| B — Study, then ship if signal | $+\$108\text{K}$ | $40K study + delay | The **right** to not ship in the bad world |

**B wins by $24K of EMV.** That $24K is the **option value of information** — what it is worth to learn *before* committing the build budget. The study is not "data for the sake of data"; it is a financial instrument whose payoff we can price.

!!! note "Rollback procedure — in five steps"
    1. Draw the tree left-to-right in time order.
    2. Label each chance branch with its probability; check the children of each $\bigcirc$ sum to 1.
    3. Write the PV at each $\triangle$ (use Lecture 4–5 machinery).
    4. **Right-to-left:** at each $\bigcirc$, replace the subtree with its expected value. At each $\square$, replace with the max child.
    5. The number at the root is the project's expected value; the *path* of maxima is your recommended strategy.

---

## § III. Utility & Risk Attitude

EMV implicitly treats a 50/50 shot at $2M as exactly equivalent to $1M with certainty. **Almost no real decision-maker behaves that way.** A $100K loss usually hurts more than a $100K gain helps; the second million matters less than the first. **Utility theory** is the formal way to encode that intuition.

### Three risk attitudes, three curve shapes

A utility function $u(x)$ maps a dollar outcome $x$ to a "utility" — a personal measure of well-being. The curvature tells you the attitude:

| Attitude | Curve shape | Example $u(x)$ | What the decision-maker accepts |
|----------|-------------|----------------|---------------------------------|
| **Risk-averse** | Concave ($u'' < 0$) | $u(x) = \ln(x)$ · $u(x) = \sqrt{x}$ | Smaller, more certain payoffs over volatile bets |
| **Risk-neutral** | Linear ($u'' = 0$) | $u(x) = x$ | Anything with EMV $\geq 0$ |
| **Risk-seeking** | Convex ($u'' > 0$) | $u(x) = e^{\alpha x}$ | Lottery-style high-variance bets |

### From EMV to expected utility — a tiny example

Take the ship-now branch from § II: $0.4 \cdot 300 + 0.6 \cdot (-60) = +84$ in EMV. Now evaluate the same gamble under $u(x) = \sqrt{x + 100}$ (shift so all payoffs are positive; concave).

- High demand: $u(+300) = \sqrt{400} = 20$
- Low demand: $u(-60) = \sqrt{40} \approx 6.32$
- Expected utility: $0.4 \cdot 20 + 0.6 \cdot 6.32 \approx 11.79$
- **Certainty equivalent:** $u^{-1}(11.79) = 11.79^2 - 100 \approx +\$39\text{K}$

A risk-averse owner with this utility values the +$84K-EMV ship-now option at only **$39K** of certain money. The gap of **$45K** is the **risk premium** — the price they would pay to convert the gamble into a sure thing. That is why a positive-EMV project can be politically rejected by a risk-averse CFO: their *utility* says no even when the *expectation* says yes.

!!! tip "What to bring to the leadership conversation"
    When you present a positive-EMV project that has substantial downside, **don't argue with the EMV** — argue about the **risk premium**. Show the upside, the downside, and a concrete plan to *hedge or cap* the downside (a pilot, a kill switch, a smaller scope). That is the language risk-averse decision-makers can say yes to.

---

## § IV. Monte Carlo Simulation

A decision tree with three or four branches is tractable on paper. Real projects have **dozens of uncertain inputs** — costs, revenues, adoption curves, churn, token prices — and the branches multiply faster than any tree can handle. **Monte Carlo simulation** is the brute-force generalisation: instead of enumerating branches, sample them.

### The four-step recipe

1. **Identify the inputs** and, for each, choose a probability distribution. Common choices:
    - **Uniform $(a, b)$** — equally likely between two bounds; use when you only know the range.
    - **Triangular $(a, m, b)$** — min, mode, max; the workhorse for engineering estimates.
    - **Normal $(\mu, \sigma)$** — when many small effects compose and you expect a bell shape.
    - **Lognormal** — when the variable is the product of many factors (cost overruns, traffic spikes).
2. **Sample $N$ tuples** of inputs — one independent draw per uncertain quantity, repeated $N$ times. Typical $N$: **10,000** for a 1-second answer; 100K for higher-resolution tails.
3. **Compute the NPV** of each sample using the same deterministic formula you used in Lecture 5. You get $N$ NPVs.
4. **Summarise the distribution.** Report the **mean**, the percentiles $P_{10}$, $P_{50}$, $P_{90}$, and — most importantly — $P(\text{NPV} > 0)$, the probability the project creates value at all. Plot the histogram.

$$
P(\text{NPV} > 0) = \frac{1}{N}\sum_{k=1}^{N} \mathbf{1}\{\text{NPV}_k > 0\}
$$

This single quantity is the modern, intelligible answer to *"should we do this?"*

### Worked output — a 10,000-trial result

Suppose the simulation of an AI-feature business case returns:

| Statistic | Value | Interpretation |
|-----------|------:|----------------|
| Mean NPV | $+\$112\text{K}$ | Average across 10,000 simulated worlds. |
| $P_{10}$ NPV | $-\$45\text{K}$ | One world in ten ends *below* this — the realistic downside. |
| $P_{50}$ (median) NPV | $+\$95\text{K}$ | Half the worlds are above, half below. |
| $P_{90}$ NPV | $+\$310\text{K}$ | One world in ten ends *above* — the realistic upside. |
| $P(\text{NPV} > 0)$ | $76\%$ | Three-in-four chance of value creation. |

!!! quote "How to communicate the result"
    "*Mean NPV is $112K with a 24% chance of loss; in the worst tenth of cases we lose $45K, in the best tenth we make $310K.*" That sentence is **harder to misinterpret** than the single number "$112K", and it is **more honest**. Every business case in this course, from this lecture onward, should report in this style.

### A 12-line Python sketch

```python
import numpy as np
N = 10_000
rng = np.random.default_rng(42)
adoption   = rng.triangular(0.05, 0.15, 0.35, N)   # share of users
arpu       = rng.normal(48, 12, N)                  # $/user/yr, clip if needed
build_cost = rng.triangular(80, 120, 220, N) * 1e3  # $K
churn      = rng.uniform(0.10, 0.30, N)
years, i   = 5, 0.12
users      = 50_000 * adoption
revenue    = users * arpu * (1 - churn)            # year-1, simplified
pv_rev     = revenue * (1 - (1+i)**-years) / i
npv        = pv_rev - build_cost
print(np.percentile(npv, [10, 50, 90]), (npv > 0).mean())
```

That is the entire technique. Everything else is choosing distributions thoughtfully and resisting the urge to over-precise the inputs.

---

## Class Discussion — Find Your Risk Attitude (10 minutes)

**Vote first; discuss in pairs after.** Two rounds:

**Round 1.** Would you take a **50/50 shot at $1,000,000** — or a **guaranteed $400,000**?

Almost everyone has a quick gut answer. Note the EMV: $0.5 \cdot 1{,}000{,}000 = \$500{,}000$, which is more than $400K — so risk-neutral logic says take the gamble. If you chose the guaranteed $400K, your personal utility curve is concave; you are risk-averse over this stakes range.

**Round 2 — replay at 10× stakes.** Would you take a **50/50 shot at $10,000,000** — or a **guaranteed $4,000,000**?

If your answer changed (more people take the guarantee), notice the shape of your utility: the second tier of millions adds less personal utility than the first. Most students discover they are **more risk-averse at higher stakes** — which is exactly what a concave utility predicts.

**Round 3 — organisational layer.** Engineering management often demands risk-averse capital allocation, even when individual engineers are happy to gamble on a moonshot project.

- As an individual contributor, are you more or less risk-tolerant than your manager? Why?
- Where does the asymmetry come from? (Career risk for the manager · diversification across projects at company level · the "career-ending failure" being a much worse outcome for the person who approved it than for the engineer who proposed it.)
- How should you *frame* a risky proposal to clear that asymmetry? (Smaller pilots · explicit kill criteria · phased commitment.)

A useful insight: **the EMV calculation has not changed between you and your manager. The utility has.** Lecture 7's vocabulary lets you have that argument out loud.

---

## § V. Software-Specific Risks — A Starter Taxonomy

Risk catalogues are not just for project managers — they are *prompts* to make sure your decision tree has every chance node it should. Below: the seven categories every software project should consider, with the canonical mitigation for each.

| Category | Example | Typical mitigation |
|----------|---------|--------------------|
| **Schedule** | Slip past a launch window (a season, a fiscal quarter, a conference) | Buffer · scope discipline · weekly burn-down |
| **Scope** | Requirements creep — the feature list grows after estimate is locked | Change control · MVP first · explicit "v2" backlog |
| **Technical** | The chosen approach proves infeasible after partial build | Architecture spike · throwaway prototype early |
| **Personnel** | Key engineer departs mid-build; tacit knowledge leaves with them | Pairing · documentation discipline · bus-factor ≥ 2 |
| **Vendor** | A SaaS doubles its price; the vendor pivots away from your use case | Multi-vendor sourcing · escape clauses · open-source fallback |
| **Model drift** | LLM behaviour changes silently mid-contract; accuracy quietly slips | Pinned model versions · automated eval harness · regression alerts |
| **Regulatory** | A new AI law (EU AI Act, sector-specific rules) forces retrofit | Compliance monitoring · design with margin · external counsel |

!!! warning "Two categories under-counted in 2026"
    **Model drift** and **regulatory** are the categories most teams ignore. A model performing at 92% accuracy today may quietly slip to 86% next quarter. A regulation announced today may require a $400K retrofit a year from now. Your decision tree should include both as named chance nodes — not buried in "miscellaneous."

### Estimation biases — why our subjective probabilities are usually wrong

Subjective probabilities are unavoidable in software (§ I). They are also **systematically biased**. Knowing the bias lets you debias the input — *before* it feeds the Monte Carlo.

| Bias | What it is | Typical magnitude | Counter-move |
|------|------------|-------------------|--------------|
| **Optimism bias** | We over-weight our preferred outcome. P10 worlds materialise 25–35% of the time, not 10%. | 2–3× | Pre-mortem · explicit "what if it ships in half the time?" exercise |
| **Anchoring** | The first number on the table dominates the rest of the conversation. | 30–60% pull toward the anchor | Estimate **independently in writing**; reveal at once |
| **Planning fallacy** | We under-estimate time/effort even for tasks we have done before. | 30–50% under-estimate | Reference-class forecasting · base rates from prior projects |

!!! tip "A rule of thumb for the pessimistic case"
    Take your "realistic" estimate; ask *"what would I think if this had to ship in half the time, with one fewer engineer, and the vendor we depend on changed pricing?"* That sentence is your **new P10**. It will hurt. If it doesn't, you haven't stress-tested hard enough.

---

## Class Discussion Prompts (post-lecture)

Open floor, ~10 minutes if time permits beyond the in-class voting exercise.

1. Pick a real decision your team has faced. Identify which chance nodes you implicitly assumed had probability 1 (i.e. you assumed they would go the way you wanted). What would the decision tree have looked like with honest probabilities?
2. Name a software project you have observed that died from one of the seven risk categories. Which one — and which mitigation would have helped most?
3. Bring up an example of **anchoring** you have seen in an estimation meeting. What was the anchor, where did it come from, and how did the team's final number drift toward it?

---

## Homework 7 — Add Probability to Your NPV

**Due: Thursday 2026-06-11, before Lecture 9. Late: −10% per day.**

1. **Decision tree.** Build a decision tree for a real choice your team is facing (or has faced). Include at least one $\square$, two $\bigcirc$, and four $\triangle$ nodes. Roll it back. Show your probabilities and explain — in two sentences — where each one came from.
2. **Monte Carlo.** Run a **10,000-trial** Monte Carlo simulation on the same project's NPV. Report mean, $P_{10}$, $P_{50}$, $P_{90}$, and $P(\text{NPV} > 0)$. Plot the histogram. Python, R, or a spreadsheet are all acceptable; show the code or the formulas.
3. **Value of information.** One paragraph: of all the uncertain inputs in your model, which **one** would you spend a $5K research budget to reduce — and why? Estimate the option value of that information, ideally as a number, but at minimum as a qualitative argument.

**Submission.** A single PDF (or notebook export) committed to the course repository at `submissions/HW7/<your-name>.pdf`. Include the histogram inline.

**Office hours.** Wednesday 10:00–11:30, before Lecture 8.

---

## Key Vocabulary — Quick Reference

| Term | Plain-language meaning |
|------|------------------------|
| Risk (Knightian) | Outcomes uncertain, probabilities *known*. |
| Uncertainty (Knightian) | Outcomes uncertain, probabilities *unknown*; we assign subjective probabilities anyway. |
| Decision tree | A picture of decision, chance, and terminal nodes whose backward induction yields a recommended strategy. |
| EMV | Expected Monetary Value — probability-weighted average of branch payoffs. |
| Rollback | Backward induction: right-to-left, expectation at $\bigcirc$, max at $\square$. |
| Option value of information | Extra expected value buying you the right (not obligation) to act on what you learn. |
| Utility function $u(x)$ | Personal mapping of dollars to well-being; curvature encodes risk attitude. |
| Risk-averse | Concave utility; prefers certainty; pays a risk premium to avoid variance. |
| Risk premium | The gap between EMV and a decision-maker's certainty equivalent. |
| Monte Carlo simulation | $N$-sample evaluation of a stochastic model; outputs a distribution rather than a point. |
| $P_{10}$, $P_{50}$, $P_{90}$ | The 10th, 50th (median), and 90th percentiles of an output distribution. |
| $P(\text{NPV} > 0)$ | The share of simulated worlds in which the project creates value. |
| Model drift | Silent change in an external model's behaviour mid-contract. |
| Optimism bias / anchoring / planning fallacy | Three systematic distortions of subjective probabilities. |

---

## Further Reading

- **Knight, F. H.** (1921). *Risk, Uncertainty and Profit.* Houghton Mifflin. The original distinction; the early chapters remain readable.
- **Raiffa, H.** (1968). *Decision Analysis: Introductory Lectures on Choices under Uncertainty.* Addison-Wesley. The canonical textbook on decision trees and value of information.
- **Hubbard, D. W.** (2014). *How to Measure Anything: Finding the Value of Intangibles in Business* (3rd ed.). Wiley. Practical Monte Carlo for business cases; calibration training.
- **Savage, S. L.** (2009). *The Flaw of Averages: Why We Underestimate Risk in the Face of Uncertainty.* Wiley. Long argument against single-number forecasting.
- **Kahneman, D.** (2011). *Thinking, Fast and Slow.* Farrar, Straus and Giroux — chapters on anchoring, planning fallacy, and optimism bias.
- **Boehm, B. W.** (1991). "Software Risk Management: Principles and Practices." *IEEE Software* 8(1) — origin of the software risk taxonomy.
- *(Forward pointer)* **Lectures 8–9** turn the *cost* side of these models into estimable numbers via Function Points and COCOMO II.

---

## What today bought you

1. A vocabulary — **risk vs uncertainty** — that lets you call out when a probability is being smuggled into a discussion as if it were a fact.
2. A picture — **the decision tree** — that makes your assumptions visible to teammates and to your future self.
3. A number — **$P(\text{NPV} > 0)$** — that replaces the false precision of "the NPV is $112K" with an honest probabilistic answer.
4. A discipline — **debiasing subjective probabilities** — so your P10 actually hurts and your team's optimism does not silently bankroll a doomed project.

---

*Prepared by Dr. Zhijiang Chen — Frostburg State University, Summer 2026.*
