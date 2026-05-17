---
title: "Lecture 6 — Break-even & Sensitivity Analysis"
author: Dr. Zhijiang Chen
date: 2026-06-08
session: 6
week: 15
room: YF108
---

# Lecture 6 — Break-even & Sensitivity Analysis

!!! info "Session Info"
    **Date:** Monday, 2026-06-08 &nbsp;·&nbsp; **Time:** 16:20–18:10 &nbsp;·&nbsp; **Room:** YF108
    **Instructor:** Dr. Zhijiang Chen &nbsp;·&nbsp; **Session 6 of 16 — Start of Week 2**

[**▶ Open the lecture slides — 110-minute web deck**](slides/lecture-06.html){ .md-button .md-button--primary target="_blank" }

!!! tip "Using the slide deck"
    The slides are a self-contained web presentation (Reveal.js, no install). Keyboard shortcuts: **F** for full-screen, **S** for speaker view, **Esc** for slide overview, **arrow keys** to navigate. Designed for a single 110-minute session. Bring last week's HW5 NPV — and a willingness to break it.

---

## Learning Objectives

By the end of this lecture, you should be able to:

1. Compute the **volume**, **time**, and **price** break-even points for a software product — both the textbook (undiscounted) form and the more honest **discounted** version.
2. Perform a **single-variable sensitivity analysis** on an NPV model: vary one input through a defined plausible range, record NPV, and rank inputs by the slope they create.
3. Extend that analysis to **multi-variable** and **correlated-scenario** sensitivity — using spider plots and two-way tables — to avoid the most common single-variable mistake.
4. Construct a **Tornado diagram** that ranks inputs by NPV impact and explain *why* this single picture has become the standard board-room artefact for project uncertainty.
5. Build a **three-point scenario** (pessimistic / realistic / optimistic) and present a single project as a *range with a downside*, not as a point estimate.
6. Apply all four tools end-to-end on a worked **AI-feature break-even** and identify the single assumption that should drive the team's research budget *before* the project is approved.

---

## Lecture Map — Five Movements, One Question

The central question of today: *if a project's NPV depends on twenty assumptions, which one should I actually worry about?* Break-even, sensitivity, tornado, and scenarios are four answers to that one question — at increasing levels of sophistication.

| § | Topic | Minutes |
|---|-------|--------:|
| I | Break-even analysis — volume, time, price | 25 |
| II | Sensitivity analysis — single and multi-variable | 25 |
| III | Tornado diagrams — sensitivity at a glance | 15 |
| IV | Scenario planning — pessimistic / realistic / optimistic | 15 |
| V | Worked AI-feature break-even example | 15 |
| — | Discussion, homework, Q&A | 15 |

!!! note "Prerequisite from Lecture 5"
    Every tool today operates on the NPV machinery introduced in Lecture 5. If you are not yet comfortable computing NPV from a cash-flow diagram at a stated MARR, revisit Lecture 5 *before* you start HW6 — sensitivity is meaningless without a working NPV to perturb.

---

## § I. Break-even Analysis — How Good Do Things Have to Be?

A single NPV calculation answers *"is this project worth doing under one set of guesses?"* A break-even analysis flips the question: *"what would the guesses need to be for the project to be exactly worth doing?"* The break-even point is the **threshold** below which the project loses money and above which it makes money. It is the most useful single number a junior engineer can produce when demand is uncertain — because it converts an *un*answerable question ("how many users will we get?") into an *answerable* one ("do we believe we can clear 1M requests / year?").

### Three flavours of break-even

| Flavour | The question it answers | Used when |
|---------|-------------------------|-----------|
| **Volume** | At what user / request / unit count do revenues cover costs? | Demand is uncertain; pricing and unit cost are known. |
| **Time** | How long until cumulative cash flow first turns positive? | Time-to-payback matters (cash-constrained teams). |
| **Price** | What price per unit makes us exactly break even? | Pricing is the decision variable; volume is assumed. |

All three reduce to one structural identity: a **fixed** cost recovered by a **per-unit margin**. Once you see the identity, every break-even problem is the same problem in different clothes.

### The volume break-even formula

For a single-period model with fixed cost $F$, price per unit $p$, and variable cost per unit $v$:

$$
Q_{BE} \;=\; \frac{F}{p - v}
$$

The denominator $p - v$ is the **contribution margin per unit** — the dollars that each sale contributes towards covering fixed cost. If $p \le v$, no volume can save the project; the unit economics are broken and break-even is undefined. **This is the first check a senior engineer runs on any new product idea.**

### Worked example — AI-feature volume break-even

An AI feature has **$200K** of fixed annual cost (engineering salaries, observability, on-call) and a **$0.05** marginal cost per request (tokens, inference, egress). It is monetised at **$0.25** per request.

$$
Q_{BE} \;=\; \frac{200{,}000}{0.25 - 0.05} \;=\; \frac{200{,}000}{0.20} \;=\; 1{,}000{,}000 \text{ requests / year}
$$

The interpretation is the discipline:

- *"If we can confidently project more than 1M requests per year, the unit economics work."*
- *"If we cannot, no amount of cost discipline will save the project."*

Notice what the formula does **not** ask you to forecast: it does not need a demand curve, an adoption rate, or a market-size estimate. It needs *one* yes/no judgement: *do we believe we will clear the threshold?* That single yes/no is far easier to defend than a five-year revenue waterfall.

### Why "price" and "time" break-evens are the same formula rearranged

| If you fix… | …and solve for | You get |
|-------------|----------------|---------|
| Volume $Q$, variable cost $v$ | Price $p$ | $p_{BE} = v + F/Q$ |
| Volume $Q$, price $p$ | Variable cost $v$ | $v_{BE} = p - F/Q$ |
| Annual cash flow $A$, initial outlay $I$ | Years $n$ | $n_{BE} = I / A$ (undiscounted) |

The first two are pricing decisions; the third is the **simple payback period**. None of them respect the time value of money — and that is exactly where the textbook form goes wrong.

### Discounted break-even — payback that respects the discount rate

Simple payback (the third row above) treats a dollar in year 3 the same as a dollar today. **Discounted payback** discounts each period's cash flow before summing, then asks: *in what year does the cumulative PV first turn positive?* For most software projects the two answers differ by half a year or more — enough to flip a "yes" into a "no".

Take a project with **$200K** initial outlay and **$80K** in net cash flow at the end of each of the next four years. The MARR is **10%**.

| Year | Cash flow | PV factor at 10% | PV of year | Cumulative PV |
|-----:|----------:|-----------------:|-----------:|--------------:|
| 0 | $-200{,}000$ | 1.000 | $-200{,}000$ | $-200{,}000$ |
| 1 | $+80{,}000$  | 0.9091 | $+72{,}727$ | $-127{,}273$ |
| 2 | $+80{,}000$  | 0.8264 | $+66{,}116$ | $-61{,}157$ |
| 3 | $+80{,}000$  | 0.7513 | $+60{,}105$ | $-1{,}052$ |
| 4 | $+80{,}000$  | 0.6830 | $+54{,}641$ | $+53{,}589$ |

The cumulative PV crosses zero between years 3 and 4. Linear interpolation inside year 4 gives:

$$
n_{BE}^{\text{disc}} \;\approx\; 3 + \frac{1{,}052}{54{,}641} \;\approx\; 3.02 \text{ years}
$$

Compare to the **naive** (undiscounted) payback: $200{,}000 / 80{,}000 = 2.5$ years. The naive number is **too optimistic by more than half a year** — and a leadership team that prices the project on a 2.5-year payback is buying a story that the math does not support.

!!! quote "Working principle for break-even"
    The break-even point is not the *answer* — it is the **threshold the answer has to clear**. Always state it discounted; always state the assumption that makes it believable.

---

## § II. Sensitivity Analysis — Which Assumption Actually Moves the Needle?

Break-even is a one-question tool. **Sensitivity analysis** generalises it: for every input that could be wrong, *how wrong does it have to be to flip the decision?* The answer ranks your inputs by how much they matter — and tells you where to spend your research budget *before* you commit to building.

### Single-variable sensitivity — the workhorse

The recipe is mechanical, which is why it gets done first:

1. Build a base-case NPV with every input at its best-guess value. Record $NPV_{\text{base}}$.
2. Pick one input. Move it to $-X\%$ (commonly $-20\%$) of base, hold every other input fixed, recompute NPV.
3. Move the same input to $+X\%$ of base, hold every other input fixed, recompute NPV.
4. Record the **range** — $NPV_{\text{high}} - NPV_{\text{low}}$ — as that input's *sensitivity*.
5. Repeat for every input that could plausibly be off by $\pm X\%$.

### Worked example — a small SaaS NPV, three inputs

A base-case NPV of **$60K** depends on three uncertain inputs. Vary each by $\pm 20\%$ holding the others fixed:

| Input | $-20\%$ NPV | Base NPV | $+20\%$ NPV | **Range** |
|-------|-----------:|--------:|-----------:|----------:|
| User count | $10{,}000 | $60{,}000 | $110{,}000 | **$100{,}000** |
| Token price | $85{,}000 | $60{,}000 | $35{,}000 | **$50{,}000** |
| Discount rate | $72{,}000 | $60{,}000 | $48{,}000 | **$24{,}000** |

Two diagnoses follow immediately:

- **User count drives NPV roughly 4× as strongly as discount rate.** That is where the team's research effort should go — a customer-interview programme is worth far more than another spreadsheet revision of the WACC.
- **Token price has *inverse* sensitivity** (NPV falls when token price rises). The sign matters when you talk to leadership: cost inputs and revenue inputs both deserve a range, but the *direction* of the range tells engineers which lever to pull.

### The single-variable trap — correlated inputs

Single-variable sensitivity has one famous weakness: it pretends inputs move *independently*. They almost never do. In an AI product:

- If user growth slows, **token spend falls too** — they are positively correlated.
- If the model gets cheaper per token, **competitors also get cheaper**, which pressures price — negative correlation across firms.
- If the macro economy turns down, **both demand and discount rate move** — the discount rate goes up exactly when revenue goes down.

A pure single-variable sensitivity that says "user count varies $\pm 20\%$" while pretending costs are fixed will *overstate* the risk of the revenue line and *understate* the risk of the joint downturn. Senior engineers always ask: *which of these inputs are actually independent?* — and group the dependent ones into a scenario (see § IV).

### Multi-variable techniques — three formats leadership recognises

| Format | What it shows | When to use |
|--------|--------------|-------------|
| **Spider plot** | One line per input — NPV (y) vs. % change from base (x). Steeper line = more sensitive. | Comparing the *slope* of many inputs on one chart. |
| **Two-way table** | NPV as a heat-map across two inputs varied simultaneously. | When two inputs interact (price × volume, growth × churn). |
| **Correlated scenarios** | Bundle inputs that move together ("low adoption" → low revenue AND low cost). | When inputs are not plausibly independent. |

The two-way table is the unsung hero. When you put price on one axis and volume on the other and shade cells by NPV, you can *see* the break-even contour as a curve through the table — and you can see immediately where the project is robust (large green area) versus fragile (a thin green stripe surrounded by red).

### Spider plot — a 60-second sketch

```
   NPV
   ▲
$150K┤            user count
     │           /
$100K┤          /
     │         /
 $60K┤────────●───────────── base
     │       /
     │     ╱  ← token price (negative slope)
   $0│   ╱
     │ ╱
−$50K┤──────────────────────→
    −40%   −20%   0   +20%   +40%   change from base
```

The steeper the line, the more sensitive the NPV is to that input. *Lines that cross zero inside the plot region are dangerous* — they mark inputs that, if even moderately wrong, would flip the project's sign.

---

## § III. Tornado Diagrams — Sensitivity at a Glance

A spider plot has too many lines for a board meeting. A **tornado diagram** compresses the same information into a single picture: one horizontal bar per input, length proportional to the NPV range, sorted **longest at the top**. The silhouette resembles a tornado — wide at the top, tapering to the bottom.

### The grammar of a tornado

| Element | What it means | Why it matters |
|---------|--------------|----------------|
| **One bar per input** | Each row is a different assumption. | Lets the eye scan all uncertainty in one glance. |
| **Bar length** | NPV range when that input varies through its plausible range. | Longest bar = the input the project is most exposed to. |
| **Bar position** | Left half = downside, right half = upside, anchored at base NPV. | Asymmetric bars tell you whether the input is more dangerous up or down. |
| **Sort order** | Longest bar at the top, shortest at the bottom. | The "tornado" shape forces ranking — no input is *secretly* important. |

### A canonical tornado — the small-SaaS example expanded

Taking the three inputs from § II and adding two more (engineer salary, cloud egress), with their plausible $\pm 20\%$ ranges:

```
                           base NPV = $60K
                                 │
User count         ────────────████████████████████████  range $100K
Token unit cost           ─────█████████████             range $50K
Engineer salary              ──████████                  range $30K
Discount rate                  ─█████                    range $24K
Cloud egress                    ███                      range $15K
                                 │
                       ←—— downside ——┼—— upside ——→
```

In one glance, leadership knows where the project's uncertainty really lives. The standard managerial reading:

- **Spend research budget on the top of the tornado** — never on the bottom. A week of user interviews has 4× the NPV-impact of a week of cloud-egress optimisation.
- **The bottom of the tornado is *good news*.** Discount-rate debates are not worth the meeting time; cloud-egress optimisation is a nice-to-have, not a project-killer.
- **A flat tornado** (all bars roughly equal) means *no single input dominates* — the project is exposed evenly, and the right response is a portfolio of small experiments rather than one big study.

### Building a tornado in a spreadsheet — five-minute recipe

1. **Base-case NPV** in one cell, with every input as a named reference.
2. **For each input**: a data-table or two extra cells that compute NPV at the input's low and high values, all other inputs held at base.
3. **Range column**: `high − low` for each input.
4. **Sort** the table by range, descending.
5. **Horizontal bar chart** with the sorted ranges — done.

Most teams that produce a tornado for the first time discover that **the input they were stressing about is not on the top of the chart** — and the input they had not thought about *is*. That single rearrangement of attention is usually worth more than the entire analysis.

---

## § IV. Scenario Planning — Three Numbers, Not One

Sensitivity tells you *which* inputs matter and *how much* a single input can move the answer. **Scenarios** put those movements together into coherent stories — typically three: **pessimistic, realistic, optimistic**. A board sees one number ($78K NPV). A scenario table reframes the decision honestly: *positive expected outcome, with a downside risk of $45K and an upside of $320K.*

### Why three points, not seven

Three points hit the sweet spot of decision-making. One number ignores uncertainty. Five or seven scenarios are unreadable in a meeting. Three forces the team to commit: *what would have to go right? what would have to go wrong? what is the middle?* The discipline of writing the **pessimistic** column is where most of the value comes from — it forces an honest list of what *could* go wrong, in concrete numbers.

### Worked example — AI feature, three scenarios

A hypothetical AI sales-assistant under three coherent scenarios. **Note that inputs are bundled** — the pessimistic column is not "every input independently at its worst", which is unrealistic; it is a *consistent story* in which all the input values fit together.

| Input | Pessimistic | Realistic | Optimistic |
|-------|-----------:|---------:|-----------:|
| User count (Year 1) | 5,000 | 15,000 | 30,000 |
| Token unit cost | $0.060 | $0.040 | $0.025 |
| Engineer cost | $200K | $180K | $160K |
| Lead conversion | 4% | 8% | 15% |
| **5-year NPV** | **$-45K** | **$+78K** | **$+320K** |

Three things to notice — each one is a generalisable lesson:

1. **The realistic case is positive but modest.** The project is not a slam-dunk; it is a defensible bet. That is the most common honest outcome of a real NPV.
2. **The pessimistic case is *negative but bounded*.** A $45K loss on a $250K project is recoverable. If the pessimistic case were $-2M, this would be a different conversation.
3. **The upside dominates the downside by ~7×.** This is the asymmetric payoff structure that makes the project worth approving even with downside risk — a point Lecture 7 will formalise as expected value.

### Two scenario anti-patterns to avoid

| Anti-pattern | What goes wrong | Fix |
|--------------|----------------|-----|
| **"Pessimistic" with one bad input** | The downside looks survivable because only one thing went wrong. | Bundle correlated inputs — "bad year" means *several* things go wrong together. |
| **"Optimistic" that is actually realistic** | The team mistakes their *target* for their *optimistic* — the real upside is invisible. | Force the optimistic column to be a *15th-percentile-best* outcome, not the plan. |

A useful informal test: *if the realistic case happens, will leadership be surprised?* If yes, the realistic column is mis-labelled — it is probably the optimistic one in disguise.

---

## § V. Worked Example — AI Feature, Fully Stress-Tested

This section walks all four tools end-to-end on a single project. The numbers are *order-of-magnitude planning numbers*, not quotes — the point is the shape of the analysis, not the precision of any one cell.

### The base case

A hypothetical AI sales-assistant:

- **Development cost**: $250K up-front (one engineer-year + integration + safety review).
- **Marginal cost**: $0.04 per request (input + output tokens, vector lookup, observability).
- **Monetisation**: $0.20 per *qualified lead* generated.
- **Volume**: 80K requests / month base case.
- **Lead conversion**: 8% of requests become qualified leads.
- **Horizon**: 5 years. **MARR**: 12%.

Base-case annual contribution:

$$
\text{annual contribution} \;=\; 80{,}000 \times 12 \times \big( 0.08 \times 0.20 \;-\; 0.04 \big) \;=\; 960{,}000 \times (\,0.016 - 0.040\,)
$$

That is **negative** — the base case as stated does not cover marginal cost! This is itself the first lesson: **a quick break-even check often kills a project before any sensitivity analysis is needed.** Let us re-examine: the lead value of \$0.20 must apply *per request* (not per converted lead) for the contribution to make sense — or the conversion rate must be much higher. The fix the team actually adopted: $\$0.20$ per qualified lead, $8\%$ conversion → revenue per request $= 0.08 \times 2.50 = \$0.20$ — i.e., the *effective* lead value was \$2.50 per qualified lead. With that correction:

$$
\text{annual contribution} \;\approx\; 960{,}000 \times (0.20 - 0.04) \;\approx\; \$154{,}000 / \text{year}
$$

At MARR 12%, $n=5$: $NPV_{\text{base}} \approx -250{,}000 + 154{,}000 \cdot (P/A, 12\%, 5) = -250{,}000 + 154{,}000 \cdot 3.605 \approx \$305{,}000$. **The base case is healthily positive** — but that single number tells leadership nothing about *how robust it is*.

### Sensitivity — vary each input through its plausible range

| Input | Low | Base | High | NPV (low) | NPV (high) | **NPV Range** |
|-------|----:|----:|-----:|----------:|----------:|--------------:|
| Qualified lead conversion | 4% | 8% | 15% | $+85K | $+305K... | **$220K** |
| Requests / month | 40K | 80K | 150K | $+165K | $+305K... | **$140K** |
| Token cost / request | $0.07 | $0.04 | $0.025 | $+269K | $+341K | **$72K** |
| Effective lead value | $1.85 | $2.50 | $3.40 | $+276K | $+334K | **$58K** |

(NPV cells are illustrative; the *ranking* is what matters for the lesson.)

### The tornado — sorted

```
                                     base NPV ≈ $305K
                                            │
Qualified lead conversion ────────████████████████████  range $220K
Requests / month               ─────█████████████       range $140K
Token cost / request                  ─────███████      range $72K
Effective lead value                   ────█████        range $58K
                                            │
                                ←— downside —┼— upside —→
```

### The diagnosis — and the action it forces

| Reading | What it means | What the team should do |
|---------|--------------|-------------------------|
| **Lead conversion dominates** | A 1-percentage-point change in conversion moves NPV more than any other input. | Run a 4-week measurement experiment — buy real conversion data before committing the full $250K. |
| **Token cost is mid-pack** | Engineers' instinct was that token cost was the killer. It is not. | Stop the multi-week "model evaluation" project — pick a model and ship. |
| **Lead value is bottom** | Sales-team pricing debates are low-leverage. | Defer the pricing committee meeting — it is not where the risk lives. |

This is exactly the rearrangement of attention that the tornado is supposed to produce. **Before this analysis the team was worried about the wrong thing.** That single insight pays for the lecture.

### Three scenarios — assembled, not invented

| | Pessimistic | Realistic | Optimistic |
|---|-----------:|---------:|-----------:|
| Lead conversion | 4% | 8% | 15% |
| Requests / month | 40K | 80K | 150K |
| Token cost / request | $0.07 | $0.04 | $0.025 |
| **5-year NPV** | **$-45K** | **$+305K** | **$+820K** |

The realistic case is strongly positive. The pessimistic case is a bounded, recoverable loss. The optimistic case is a multi-bagger. **This is a project worth doing — and the team now knows exactly which experiment to run first.**

### Bridge to Lecture 7

Sensitivity says *which* inputs matter. Scenarios say *how good or bad it could get*. Neither says *how likely* each outcome is. **Lecture 7** introduces probability on top of sensitivity — decision trees, expected value, and Monte Carlo simulation — and lets you compute a single risk-adjusted number from the scenario table above. Today's tornado is the input to next week's expected-value tree.

---

## Class Discussion — Stress Your Own NPV

*~10 minutes. Bring last week's HW5 NPV on paper or laptop.*

**The prompt:** *"Which assumption in YOUR HW5 NPV would you defend least?"*

**The exercise (in pairs, 4 minutes per direction, 8 minutes total):**

1. Trade your HW5 NPV mini-case with your partner.
2. Identify the **single most uncertain assumption** in each other's model. State it explicitly: "the assumption I trust least in your model is X."
3. Compute the NPV impact of being wrong by $\pm 30\%$ on that single input. (Quick mental math is fine — order of magnitude is what matters.)
4. Answer three follow-up questions out loud:
    - Is the project still positive in the $-30\%$ case?
    - If not, what level of confidence in your assumption would you need to proceed?
    - What concrete experiment — a customer interview, an A/B test, a competitor signal — would change your mind in two weeks or less?

**Use-case anchors** (in case your HW5 didn't land cleanly): pick one of these and run the same exercise:

- **SaaS pricing assumption** — "we will charge $20/seat/month." What if the market clears at $12?
- **User growth rate** — "we will grow 8% MoM." What if it's 3%?
- **AI inference cost** — "$0.04 per request." What if a model price drop takes it to $0.015 — and a competitor's price drop takes our revenue down the same amount?

**Debrief (2 minutes):** three pairs share the single assumption they would *most* like to test before next week's class.

---

## Homework 6 — Build Your Tornado

**Due: Wednesday 2026-06-10, before Lecture 8. Late: −10% per day.**

1. **Pick a project you have NPV-ed** — your HW5 mini-case, your group project, or a real product you know. State the base-case NPV, the MARR, and the horizon.
2. **One-variable sensitivity table.** For at least **five** inputs, vary each by $\pm 20\%$ (or a plausible range you define) and record NPV at low, base, and high. Compute the NPV range for each input.
3. **Tornado diagram.** Sort by NPV range, longest at the top. Hand-drawn or spreadsheet — both fine. Identify the **top three NPV drivers** in one sentence each.
4. **Three-point scenario.** Construct pessimistic / realistic / optimistic columns. Bundle correlated inputs into each column (do not move them independently). Decide whether the project would still be approved in the pessimistic case, and write **one sentence** of justification.
5. **The experiment paragraph.** In half a page, propose one concrete two-week experiment that would *reduce the uncertainty of the top driver*. Be specific: who you would talk to, what you would measure, and how the result would change your decision.

**Submission.** A single PDF (spreadsheet export + tornado chart + paragraph) committed to the course repository at `submissions/HW6/<your-name>.pdf`.

**Office hours.** Tomorrow, 10:00–11:30, before Lecture 7.

---

## Further Reading

- **Park, C. S.** *Contemporary Engineering Economics* — chapter on break-even and sensitivity analysis; the standard textbook treatment with worked tornado examples.
- **Boehm, B. W.** (1981). *Software Engineering Economics*. Prentice Hall — **Chapter 19** introduces sensitivity analysis for software cost models; still the canonical reference for software-specific framing.
- **Howard, R. A.** (1988). "Decision Analysis: Practice and Promise." *Management Science* — origin of the tornado-diagram convention in formal decision analysis.
- **Eschenbach, T. G.** (1992). "Spiderplots versus Tornado Diagrams for Sensitivity Analysis." *Interfaces* — short, readable paper on when to use which.
- **Savage, S. L.** (2009). *The Flaw of Averages*. Wiley — popular treatment of why single-number forecasts are usually wrong; mandatory reading for anyone who reports NPVs to leadership.
- *(Forward pointer)* **Lecture 7 — Risk & Decision Trees** will put probabilities on the scenario table you produce today and turn the tornado into an expected-value tree.

---

## What today bought you — three takeaways

| Take-away | Why it matters |
|-----------|----------------|
| **Break-even tells you how good things need to be.** | Useful when demand is uncertain — converts unanswerable questions into yes/no thresholds. Always state it *discounted*. |
| **Sensitivity tells you which assumptions deserve scrutiny.** | A Tornado diagram is the standard format. The top bar tells you where to spend research budget; the bottom bar tells you which debate to defer. |
| **Scenarios turn a single NPV into a decision narrative.** | Three numbers — pessimistic, realistic, optimistic — let leadership see the *shape* of the bet, not just the headline. Bundle correlated inputs; don't move them independently. |

---

## Key Vocabulary — Quick Reference

| Term | Plain-language meaning |
|------|------------------------|
| Break-even quantity | Volume at which revenue exactly covers fixed + variable cost; $Q_{BE} = F / (p - v)$. |
| Contribution margin | Price per unit minus variable cost per unit; the dollars each sale contributes to fixed cost. |
| Simple payback period | Years until cumulative *undiscounted* cash flow turns positive. |
| Discounted payback | Years until cumulative *discounted* cash flow turns positive — the honest version. |
| Single-variable sensitivity | NPV change when one input moves by a defined percentage, all others held fixed. |
| Spider plot | One line per input, NPV vs. percent change from base — slope shows sensitivity. |
| Two-way table | NPV as a heat-map across two inputs varied simultaneously. |
| Tornado diagram | Horizontal-bar chart, one bar per input, sorted by NPV range — longest at top. |
| Correlated scenario | A bundle of inputs that move *together* (low growth → low revenue AND low cost). |
| Three-point scenario | Pessimistic / realistic / optimistic columns — the standard board-room format. |
| Decision-relevance (recall) | Count only future and differential cash flows when stressing an NPV. |

---

*Prepared by Dr. Zhijiang Chen — Frostburg State University, Summer 2026.*
