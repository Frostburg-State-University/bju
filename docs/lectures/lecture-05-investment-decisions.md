---
title: "Lecture 5 — Investment Decisions & Alternative Selection"
author: Dr. Zhijiang Chen
date: 2026-06-07
session: 5
week: 14
room: SY109
---

# Lecture 5 — Investment Decisions & Alternative Selection

!!! info "Session Info"
    **Date:** Sunday, 2026-06-07 &nbsp;·&nbsp; **Time:** 16:20–18:10 &nbsp;·&nbsp; **Room:** SY109
    **Instructor:** Dr. Zhijiang Chen &nbsp;·&nbsp; **Session 5 of 16 — End of Week 1**

[**▶ Open the lecture slides — 110-minute web deck**](slides/lecture-05.html){ .md-button .md-button--primary target="_blank" }

!!! tip "Using the slide deck"
    The slides are a self-contained web presentation (Reveal.js, no install). Keyboard shortcuts: **F** for full-screen, **S** for speaker view, **Esc** for slide overview, **arrow keys** to navigate. Designed for a single 110-minute session.

---

## Learning Objectives

By the end of this lecture, you should be able to:

1. State the **Net Present Value (NPV)** definition, compute NPV from a cash flow stream at a given discount rate, and apply the **accept-iff-NPV>0** decision rule.
2. Define the **Internal Rate of Return (IRR)** as the rate at which NPV = 0, compute it (analytically for short streams, iteratively otherwise), and apply the **IRR > MARR** decision rule — including its multiple-root and reinvestment-rate caveats.
3. Choose an appropriate **MARR** for a given organisational setting — cash-rich enterprise, venture-backed startup, government, AI/experimental — and explain why an engineer should never silently substitute their own.
4. Compute **simple payback** and **discounted payback**, and recognise when payback is a *useful screen* and when it is a *dangerous sole criterion*.
5. Compute the **Profitability Index (PI)** and use it to rank projects under **capital rationing** — the situation where you must choose a subset of positive-NPV projects under a fixed budget.
6. Choose between **mutually-exclusive alternatives** correctly — picking by NPV (not IRR) when sizes or lives differ — and reconcile the rules with **incremental IRR analysis**.
7. **Resolve the Lecture 1 Elasticsearch-vs-SaaS puzzle** end-to-end, including a sensitivity check that identifies which assumptions actually drive the verdict.

---

## Lecture Map — Four Rules, One Verdict

By the end of today you should be able to look at any project's cash flow and reach a **defensible accept/reject verdict** using four decision rules — and know which rule to trust when they disagree. Today closes Week 1: every cost concept, every cash-flow shape, and every equivalence move from Lectures 1–4 funnels into a single answer.

| § | Topic | Minutes |
|---|-------|--------:|
| I | Net Present Value (NPV) & the discount-rate question | 20 |
| II | Internal Rate of Return (IRR) & MARR | 20 |
| III | Payback period & Profitability Index (PI) | 15 |
| IV | Comparing mutually-exclusive alternatives | 15 |
| — | Discussion — a real decision from your team | 10 |
| V | Verdict on Lecture 1's Elasticsearch vs SaaS | 20 |
| — | HW 5, Week 1 recap, questions | 10 |

!!! note "Prerequisites from Lectures 3–4"
    This lecture assumes you can price a single sum ($P/F, F/P$), a uniform series ($P/A, A/P$), an arithmetic gradient ($P/G, A/G$), and a geometric gradient. If any of those still feel slow, revisit Lecture 4 before today — every decision rule below is *NPV underneath*, and NPV is a sum of priced cash-flow shapes.

---

## § I. Net Present Value — The One Number That Says Yes or No

If you remember only one rule from this entire course, remember NPV. Every other rule we discuss today earns a footnote; NPV is the only one finance professors will defend without one.

### Definition

The **Net Present Value** of a project is the sum of every cash flow, each discounted to the present at the firm's cost of capital $i$:

$$
\text{NPV} = \sum_{t=0}^{n} \frac{CF_t}{(1+i)^t}
$$

where $CF_t$ is the net cash flow at the end of period $t$ (positive for inflows, negative for outflows), $i$ is the discount rate (the firm's MARR — see § II), and $n$ is the project horizon. Conventionally $CF_0$ is the initial investment and is negative.

### Decision rule

> **Accept the project iff NPV > 0.** The positive NPV is the *surplus value* the project creates over and above the firm's required return.

NPV = 0 means the project earns *exactly* the required return — the firm is indifferent. NPV < 0 means the firm would be richer doing literally anything else that earns the MARR. There is no "small negative NPV is fine" tier; if the rate truly is your MARR, the rule is binary.

### Why NPV is the gold standard

| Property | What it gives you |
|----------|-------------------|
| **Uses every cash flow** | Doesn't truncate at a payback horizon; doesn't ignore tails. |
| **Respects time value of money** | Each dollar lives at its real economic time. |
| **Directly measures value created** | The number is dollars in the shareholder's pocket today. |
| **Additive across a portfolio** | NPV(A + B) = NPV(A) + NPV(B). Lets you build budgets bottom-up. |
| **Currency-denominated** | "+\$35K" tells you something "+17% IRR" never can. |

### Worked example — NPV of an internal AI code-review tool

Your team proposes to build an internal AI-assisted code-review tool. The estimated cash flow is:

| Year | Cash flow |
|-----:|----------:|
| 0    | −\$200,000 (build) |
| 1    | +\$60,000  |
| 2    | +\$80,000  |
| 3    | +\$80,000  |
| 4    | +\$80,000  |

At a MARR of **10%**, compute NPV. The discount factors $1/(1.10)^t$ are tabulated, so this is mechanical:

| Year | Cash flow | DF @ 10% | PV |
|-----:|----------:|---------:|---:|
| 0    | −\$200,000 | 1.0000 | −\$200,000 |
| 1    | +\$60,000  | 0.9091 | +\$54,545  |
| 2    | +\$80,000  | 0.8264 | +\$66,116  |
| 3    | +\$80,000  | 0.7513 | +\$60,105  |
| 4    | +\$80,000  | 0.6830 | +\$54,641  |
| **NPV** | | | **+\$35,407** |

**Verdict.** NPV ≈ **+\$35K > 0** — the project creates value over the firm's 10% cost of capital. **Accept.**

!!! quote "Working principle"
    NPV converts a *stream* of dollars across years into a *single* dollar amount today. The single dollar amount is the only number that decides yes or no.

### The discount-rate question — where NPV is fragile

NPV depends on the discount rate you choose. Halve it and the project looks great; double it and it looks terrible. At $i = 5\%$ the AI tool above is worth about \$53K; at $i = 20\%$ it is worth about −\$8K. Same cash flows, opposite verdicts. **The rate is half the answer.** This is why § II is about the discount rate, not the math.

---

## § II. Internal Rate of Return — The Rate Implied by the Cash Flows

NPV needs a rate as input. IRR *outputs* a rate from the cash flows themselves — and asks the firm whether that rate clears the bar.

### Definition

The **Internal Rate of Return** is the discount rate $i^*$ at which the project's NPV equals zero:

$$
\text{find } i^* \text{ such that } \sum_{t=0}^{n} \frac{CF_t}{(1+i^*)^t} = 0
$$

It is the project's *implied return* — what you would earn if you put your money in *this* project and reinvested all the returns at $i^*$.

### Decision rule

> **Accept iff IRR > MARR.** If the project's implied return clears the firm's required return, accept.

### How to compute IRR

| Stream | Method |
|--------|--------|
| 2 cash flows (one out, one in) | Solve algebraically: $-P + F/(1+i)^n = 0 \Rightarrow i = (F/P)^{1/n} - 1$. |
| 3+ cash flows | Numerical: spreadsheet `IRR()` / `XIRR()`, Newton's method, or bisection. |
| Uniform-series projects | Solve $(P/A, i, n) = P / A$ for $i$ — interpolate in a factor table. |

For the AI tool above, the IRR is ≈ **17.5%** — well above the 10% MARR. **Decision: accept.** Consistent with the NPV verdict (they almost always agree on accept/reject for a single project — the disagreements appear when comparing two projects, in § IV).

### Three honest limitations of IRR

1. **Multiple IRRs.** Any cash flow with more than one sign change can have more than one IRR. A project that costs money, makes money, then costs decommissioning money has *two* roots — neither of which is "the" return. In practice, when sign changes are present, fall back to NPV.
2. **Reinvestment-rate assumption.** IRR implicitly assumes intermediate cash flows are reinvested at the IRR itself. If your IRR is 35% but the rest of the firm earns 10%, that assumption is fiction. The **Modified IRR (MIRR)** fixes this by assuming reinvestment at the MARR.
3. **Scale blindness.** IRR is a *rate*, so it ignores project size — a 30% return on \$1K beats a 16% return on \$1M *on rate*, but the firm cashes the dollars, not the rate. See § IV.

!!! warning "When NPV and IRR disagree, NPV wins"
    NPV measures value created in dollars. IRR measures rate of return. The firm is in business to maximise dollars — not rates. Whenever the two rules conflict on a project comparison, NPV is the rule that aligns with shareholder wealth.

### MARR — the hurdle the firm sets

**MARR** (Minimum Attractive Rate of Return) is the lowest return the firm will accept for an investment of similar risk. It is *set by leadership*, not chosen by the engineer — and it varies enormously across settings:

| Setting | Typical MARR | Rationale |
|---------|-------------:|-----------|
| Cash-rich enterprise | **8–12%** | Cost of capital + small risk premium |
| Venture-backed startup | **25–40%** | Equity investors demand high return |
| Government / non-profit | **3–5%** | Treasury-rate floor |
| AI / experimental product | **20–30%** | Outcome uncertainty bumps the floor |

**Three rules for engineers using a MARR:**

- If the firm has stated a discount rate, **use it** — do not quietly substitute a number you prefer.
- If the firm has *not* stated a rate, **ask finance**. Most students do not realise they are allowed to.
- If you must default, **10% is the safe textbook number** for a mid-cap technology company in a normal interest-rate environment. State the assumption explicitly.

The same project can be accepted at one MARR and rejected at another. That is not a flaw; it is the *point* — the MARR encodes the firm's risk appetite, capital cost, and opportunity set. A startup that accepts only IRR ≥ 30% is making the right call for its investors even if a large bank would happily accept the same project at IRR = 9%.

---

## § III. Payback Period & Profitability Index — The Rough-and-Ready Rules

NPV and IRR answer "is it worth it?" Payback and PI answer two follow-up questions managers actually ask: "when do we get our money back?" and "what's the bang for the buck?"

### Payback period

The **payback period** is the number of periods until the cumulative cash flow turns from negative to positive. For the AI tool above:

| Year | Cash flow | Cumulative |
|-----:|----------:|-----------:|
| 0    | −\$200,000 | −\$200,000 |
| 1    | +\$60,000  | −\$140,000 |
| 2    | +\$80,000  | −\$60,000  |
| 3    | +\$80,000  | **+\$20,000** ← crosses zero |
| 4    | +\$80,000  | +\$100,000 |

Cumulative cash crosses zero partway through year 3. Interpolating: \$60K still owed at start of year 3 ÷ \$80K incoming that year = **0.75 years**, so payback ≈ **2.75 years**.

**Decision rule:** Accept iff payback < some firm-set ceiling (commonly 2–3 years for IT projects, longer for infrastructure).

#### Strengths and weaknesses

| Strengths | Weaknesses |
|-----------|------------|
| Easy to compute, easy to explain | **Ignores all cash flows after payback** |
| Prioritises liquidity — protects against running out of cash | **Ignores the time value of money** (unless discounted) |
| Reasonable proxy for risk in unstable or fast-changing environments | Biases against long-horizon projects (R&D, infrastructure) |
| Useful as a *screen* before deeper analysis | Says nothing about value *created* |

#### Discounted payback

A halfway-house: discount each year's cash flow first, then find when cumulative *discounted* cash flow turns positive. This fixes the time-value-of-money flaw but still ignores post-payback flows. For the AI tool at 10%, discounted payback comes in at ≈ **3.4 years** — meaningfully longer than the undiscounted 2.75.

!!! warning "Payback is a screen, never a verdict"
    Treat payback as a *secondary criterion*, never the sole decision rule. A high-NPV project with a 4-year payback is still a high-NPV project. Conversely, a 1-year payback on a project that *also* has negative NPV is still a value-destroyer. The classic management error is rejecting a great long-tailed project because its payback exceeds an arbitrary ceiling.

### Profitability Index

The **Profitability Index (PI)** measures bang-for-the-buck — present-value benefit per dollar of capital invested:

$$
\text{PI} = \frac{\text{PV of future cash flows}}{|\text{initial investment}|}
$$

For our AI tool: PV(future) = \$54,545 + \$66,116 + \$60,105 + \$54,641 = \$235,407. Divide by initial \$200,000:

$$
\text{PI} = \frac{235{,}407}{200{,}000} \approx 1.18
$$

**Interpretation.** Every \$1 of capital deployed returns \$1.18 of present-value benefit — i.e., \$0.18 of NPV per \$1 invested.

**Decision rule:** PI > 1 ⟺ NPV > 0. So as a single-project rule, PI gives the same accept/reject as NPV.

#### Where PI earns its keep — capital rationing

The real power of PI is in **capital rationing** — when you have several positive-NPV projects but a fixed total budget. Ranking by NPV alone would have you pick the biggest projects regardless of efficiency; ranking by PI picks the *most efficient* use of each scarce dollar.

| Project | Investment | NPV | PI |
|---------|-----------:|----:|---:|
| α | \$100K | \$40K | 1.40 |
| β | \$200K | \$60K | 1.30 |
| γ | \$80K  | \$24K | 1.30 |
| δ | \$120K | \$24K | 1.20 |

If the budget is **\$300K**, taking the single highest-NPV project (β) captures \$60K of NPV. Ranking by PI lets you assemble the *combination* α + γ + δ (total cost = \$300K) for total NPV of \$88K — **\$28K better** than β alone. PI is the right ranking when capital is the binding constraint.

---

## § IV. Mutually-Exclusive Alternatives — When "Accept All Positives" Is the Wrong Rule

So far we have evaluated projects one at a time. Many real engineering decisions are *choose-one*: build vs. buy, vendor A vs. vendor B, architecture α vs. architecture β. The rule changes.

> **For mutually-exclusive alternatives, pick the one with the highest NPV — not the highest IRR.**

### Why IRR misleads here

Consider two ways of solving the same internal-tooling problem:

| Project | Investment | NPV @ 10% | IRR |
|---------|-----------:|----------:|----:|
| A (small) | \$10,000 | \$3,500 | **30%** |
| B (large) | \$80,000 | **\$15,000** | 16% |

A wins on IRR; B wins on NPV. Which should you choose?

**Pick B.** The firm exists to maximise *wealth*, not *rate*. A's "extra" \$70K of capital (not invested in A) can presumably earn the MARR somewhere else — but no better than that, so B's larger absolute value wins. A 30% return on \$10K is \$3K of value; a 16% return on \$80K is \$15K of value. The firm cashes the \$15K, not the rate.

This is the **scale problem**: IRR doesn't know how big the project is. A 100% IRR on a \$1 investment is meaningless if there's a 12% IRR on a \$1M investment on the table.

### Incremental IRR analysis — reconciling NPV and IRR

If your CFO insists on rate-based language, there is a way to use IRR consistently with NPV: compute the IRR of the **incremental** cash flow (B − A). The question becomes: *"Is the extra investment in B (beyond what A would cost) worth it at our MARR?"*

For our two projects, the increment is: invest \$70K more today to gain \$11,500 more NPV at 10%. Solving for the rate that zeroes the incremental NPV gives an incremental IRR of **≈ 13.7%**. Since 13.7% > 10% MARR, the additional capital deployed in B beats the MARR — so **choose B**. Consistent with the NPV verdict.

| Approach | Says |
|----------|------|
| Pick higher NPV | B (\$15K > \$3.5K) |
| Pick higher IRR | A (30% > 16%) — **wrong** |
| Incremental IRR | B (13.7% > 10% MARR) |

!!! tip "When to use which"
    For mutually-exclusive alternatives, **default to NPV** — it never lies. Use incremental IRR only when you need to *speak rate to a rate-speaking audience*. Never use plain IRR ranking for mutually-exclusive alternatives of different sizes.

### Different lives — the LCM trick

A second wrinkle: what if alternative A has a 4-year life and B has a 6-year life? Comparing their NPVs directly is unfair — B has 50% more time to generate value. Two clean fixes:

1. **Repeat each to the least common multiple of lives** (here, 12 years), then compare NPVs of the replicated streams. Crude but transparent.
2. **Convert each to its Equivalent Annual Annuity (EAA)** using $A = \text{NPV} \cdot (A/P, i, n)$. The EAA is the project's "value per year" and is directly comparable across lives.

We will return to the EAA approach in Lecture 7 (mutually-exclusive decisions with unequal lives).

---

## Class Discussion — A Real Decision From Your Team

**Format.** Pairs, 4 minutes each, then 6 minutes whole-room. Total ~10 minutes.

> **Prompt:** Bring me a decision your team (or someone you've worked with) actually made — and tell me whether it would survive an NPV check.

**Step 1 — Pair (4 min).** Each partner describes *one* real decision: a tooling switch, a hire, a contract, a build-vs-buy, a refactor. Estimate the cash flows on paper: initial cost, annual benefits or savings, horizon. Use **10% as the discount rate** unless your organisation has a stated MARR.

**Step 2 — Compute (4 min).** Rough NPV. Two columns are fine: cumulative cost, cumulative discounted benefit. Doesn't need to be precise — just *defensible*.

**Step 3 — Whole room (6 min).** Two pairs share. We'll surface one decision where NPV would have *endorsed* the call and one where it would have *rejected* it.

**Questions to wrestle with** while you compute:

| Question | Why it matters |
|----------|----------------|
| What discount rate did you assume — and why? | Half the answer lives in the rate. |
| Which assumption is your NPV most sensitive to? | Sensitivity tells you where to investigate. |
| Did the decision look smart in NPV terms — or only with hindsight? | Hindsight bias inflates ex-post judgements. |
| What cash flows did you exclude — and why? | Sunk vs. opportunity vs. common — see Lecture 2. |

!!! note "On hindsight bias"
    A decision that turned out well is not the same as a decision that *was* well-made. Conversely, a good decision can produce a bad outcome (the world is uncertain). NPV asks about the *quality of the decision at the time it was made*, with the information available then — not about the eventual outcome. That is exactly why it survives as a discipline.

---

## § V. Verdict — Elasticsearch vs SaaS, The Answer At Last

In Lecture 1 we posed a build-vs-buy puzzle and asked you to vote on instinct. Today we settle it with numbers.

### The setup, recalled

| Alternative | Year 0 cost | Annual operating cost | Life |
|-------------|------------:|----------------------:|-----:|
| **A. Build in-house (Elasticsearch)** | \$120,000 | \$30,000 | 5 years |
| **B. Subscribe to SaaS search** | \$20,000 | \$80,000 | 5 years |

Same functionality, same horizon. **Discount rate: 10%.** Which alternative is economically preferred?

A quick undiscounted glance: A totals \$120K + 5 × \$30K = **\$270K**; B totals \$20K + 5 × \$80K = **\$420K**. A "wins" by \$150K on raw totals — but the timing matters, and discounting will sharpen the picture.

### The math — both alternatives, both PVs

Both alternatives have a one-time Year-0 outflow plus a 5-year uniform annual operating cost. At $i = 10\%$, the uniform-series PV factor is $(P/A, 10\%, 5) = 3.7908$.

| Alt. | Build / Subscribe | Year 0 | Years 1–5 stream | **PV total cost** |
|------|-------------------|-------:|------------------|------------------:|
| A | Elasticsearch | \$120,000 | \$30,000 × 3.7908 = \$113,723 | **\$233,723** |
| B | SaaS | \$20,000 | \$80,000 × 3.7908 = \$303,261 | **\$323,261** |

**Verdict.** At a 10% discount rate, **build in-house has a PV of total cost \$89,538 lower** than the SaaS option. **Pick A — build the Elasticsearch cluster.**

!!! quote "The discipline behind the verdict"
    Same conclusion as the back-of-envelope total — but now with discipline. The math protects us when the gap is narrower; it lets us say *exactly how big* the advantage is; and it gives us a starting point for sensitivity analysis.

### How robust is this verdict? — A sensitivity preview

A verdict is only as good as the assumptions behind it. The right post-NPV question is always: **what would have to change for the verdict to flip?**

| Assumption | Change tested | New verdict |
|------------|---------------|-------------|
| Discount rate up to **25%** | from 10% → 25% | A still wins, by ≈ \$43K |
| Discount rate down to **5%** | from 10% → 5% | A still wins, by ≈ \$129K |
| SaaS price falls **30%** | annual \$80K → \$56K | **B wins by ≈ \$13K** |
| Build cost overruns **50%** | \$120K → \$180K | A still wins, by ≈ \$30K |
| Useful life shrinks to **3 years** | 5 → 3 years | **B wins by ≈ \$25K** |
| Build needs a 2nd hire (\$50K/yr op) | annual op \$30K → \$50K | A still wins, by ≈ \$14K |

**Two findings worth carrying away.**

1. The verdict is **insensitive to the discount rate** across any plausible MARR for a mid-cap firm. Even at 25% (well above any realistic MARR), A wins. This is the kind of robustness that earns a CFO's trust.
2. The verdict **is sensitive to useful life and SaaS pricing trajectory**. If the team is unsure they will still be using this search system in 3 years — or if there is a credible chance SaaS prices fall 30% (a real possibility in a competitive market) — the verdict flips.

**The meta-lesson.** NPV gives you *an answer*. Sensitivity tells you *which assumptions you should investigate before signing*. Today's homework includes a sensitivity exercise; tomorrow's lecture (Lecture 6) makes sensitivity formal with break-even analysis, tornado diagrams, and scenario tables.

### Bridge to Lecture 6

You now know how to reach a verdict. Tomorrow you will learn how to **stress-test** that verdict, find the assumptions that matter, and present a *confidence range* — not a single number — to a decision-maker. NPV is a number; engineering judgement is what tells the decision-maker whether to trust it.

---

## Homework 5 — Investment Decisions in Practice

**Due: Tuesday 2026-06-09, before Lecture 7. Late: −10% per day.**

### Part A — Six problems (40 points)

Solve and show working. Use 10% MARR unless the problem states otherwise.

1. **NPV.** A project costs \$150K today and returns \$45K/yr for 5 years. Compute NPV at $i = 8\%$, $12\%$, and $18\%$. At which rate does it break even (approximately)?
2. **IRR.** Compute the IRR of: −\$200K, +\$60K, +\$80K, +\$80K, +\$80K. (Use spreadsheet or trial-and-error to ±0.1%.) Compare with the NPV-based decision at MARR = 10%.
3. **Payback & discounted payback.** For the project in Q1, compute simple payback and discounted payback at 10%. Comment on the difference.
4. **PI under capital rationing.** Given four projects with (investment, NPV) of (\$80K, \$24K), (\$120K, \$30K), (\$60K, \$21K), (\$100K, \$22K), and a budget of \$240K, choose the optimal portfolio using PI ranking. Show the PI for each and the total NPV captured.
5. **Mutually exclusive — same life.** Two designs: D1 costs \$50K and saves \$18K/yr for 5 years; D2 costs \$120K and saves \$36K/yr for 5 years. At MARR = 10%, compute NPV and IRR of each. Which do you pick? Then compute the incremental IRR (D2 − D1) and confirm.
6. **Mutually exclusive — different lives.** Alternative X has NPV \$60K over 4 years; Alternative Y has NPV \$80K over 6 years. At MARR = 10%, compute the Equivalent Annual Annuity (EAA) of each. Which is preferred per year?

### Part B — Mini-case (40 points)

Propose **two implementation alternatives** for a real product (yours, your team's, or a hypothetical you sketch in one paragraph). The alternatives must be genuinely mutually exclusive — e.g., build vs. buy, two cloud architectures, two vendor contracts.

1. **Cash flows.** Build a 5-year cash flow table for each alternative. Identify every line: initial cost, recurring operating cost, expected benefits or savings, retirement/migration costs. Use Lecture 2's TCO discipline.
2. **All four rules.** Compute **NPV, IRR, simple payback, and PI** for each alternative at MARR = 10%.
3. **Verdict.** Pick a winner and defend it in **one page**. State explicitly: which rule did you rely on, and why? Did any rules disagree?
4. **Sensitivity (≥ 3 variables).** For your chosen alternative, test sensitivity to (at least) the discount rate, the largest cost line, and the useful life. Present a small table like the Elasticsearch sensitivity table above. Which assumption(s) actually drive the verdict?

### Part C — Reflection (20 points)

Half-page maximum. **Would you have made the same recommendation *before* running the numbers?** Why or why not? If the answer is "yes, the numbers just confirmed what I already thought" — be honest about whether that means the analysis was redundant, or that your intuition was well-calibrated. If the answer is "no" — what cost or benefit had you been ignoring?

**Submission.** A single PDF (your problems + spreadsheet export + mini-case writeup + reflection) committed to the course repository at `submissions/HW5/<your-name>.pdf`.

**Office hours.** Monday 2026-06-08, 10:00–11:30, before Lecture 6.

---

## Week 1 — What Today Bought You

Five lines, each a load-bearing claim the rest of the course will build on.

1. **Engineers make economic decisions** whether they admit it or not. Refusing to engage with the money does not exempt you — it just means someone else makes your decisions for you.
2. **Every cost lives in a *fixed/variable × direct/indirect × non-recurring/recurring* cell**, across five lifecycle phases, and only *future, differential* costs are decision-relevant.
3. **Money has time value** — opportunity cost, risk, inflation — and the discount rate $i$ is how we encode all three in one number.
4. **Cash-flow streams have closed-form factors**: $P/A$, $F/A$, $P/G$, geometric. Any messy real-world stream decomposes into a sum of these shapes, and PV is linear.
5. **The decision rule is NPV > 0.** Everything else — IRR, payback, PI — is a useful footnote on the conversation NPV is having with shareholder wealth.

From here forward, every lecture is built on these five lines.

---

## Key Vocabulary — Quick Reference

| Term | Plain-language meaning |
|------|------------------------|
| NPV | Sum of every cash flow, each discounted to today at rate $i$. |
| IRR | The rate that makes NPV = 0. Project's implied return. |
| MARR | Minimum Attractive Rate of Return — the firm's hurdle rate. |
| Payback period | Time until cumulative cash flow turns positive. |
| Discounted payback | Same, but on discounted cash flows. |
| Profitability Index (PI) | PV(future) ÷ \|initial investment\|. PI > 1 ⟺ NPV > 0. |
| Capital rationing | Choosing a subset of positive-NPV projects under a fixed budget — rank by PI. |
| Mutually exclusive | Choose at most one of several alternatives — pick highest NPV. |
| Incremental IRR | IRR of (B − A) cash flow; lets IRR language agree with NPV. |
| Equivalent Annual Annuity (EAA) | NPV converted to a per-year amount via $A/P$ — for comparing different-life alternatives. |
| MIRR | Modified IRR — assumes reinvestment at the MARR, not the IRR. |
| Sensitivity analysis | Re-running the verdict under perturbed assumptions to see what flips it. |

---

## Further Reading

- **Park, C. S.** *Contemporary Engineering Economics* — chapters on NPV, IRR, payback, PI, and incremental analysis (the primary text for this lecture).
- **Boehm, B. W.** (1981). *Software Engineering Economics*. Prentice Hall — **Chapter 5** on present-worth analysis and incremental comparison.
- **Brealey, R., Myers, S., & Allen, F.** *Principles of Corporate Finance* — capital budgeting chapters; the canonical treatment of NPV vs. IRR.
- **Ross, S., Westerfield, R., & Jaffe, J.** *Corporate Finance* — clean treatment of the multiple-IRR problem and MIRR.
- *(Forward pointer)* **Lecture 6 — Break-Even & Sensitivity Analysis** formalises the sensitivity check you saw in § V; **Lecture 7 — Mutually-Exclusive Alternatives** treats unequal lives and the EAA in depth.

---

*Prepared by Dr. Zhijiang Chen — Frostburg State University, Summer 2026.*
