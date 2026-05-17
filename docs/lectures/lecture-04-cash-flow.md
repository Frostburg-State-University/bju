---
title: "Lecture 4 — Cash Flow Analysis & Equivalence"
author: Dr. Zhijiang Chen
date: 2026-06-06
session: 4
week: 14
room: SY109
---

# Lecture 4 — Cash Flow Analysis & Equivalence

!!! info "Session Info"
    **Date:** Saturday, 2026-06-06 &nbsp;·&nbsp; **Time:** 16:20–18:10 &nbsp;·&nbsp; **Room:** SY109
    **Instructor:** Dr. Zhijiang Chen

[**▶ Open the lecture slides — 110-minute web deck**](slides/lecture-04.html){ .md-button .md-button--primary target="_blank" }

!!! tip "Using the slide deck"
    The slides are a self-contained web presentation (Reveal.js, no install). Keyboard shortcuts: **F** for full-screen, **S** for speaker view, **Esc** for slide overview, **arrow keys** to navigate. Designed for a single 110-minute session.

---

## Learning Objectives

By the end of this lecture, you should be able to:

1. **Draw** a correct cash flow diagram (CFD) for any project — choosing a perspective, marking $t=0$, and orienting arrows consistently.
2. Apply the four standard **uniform-series factors** ($P/A$, $F/A$, $A/P$, $A/F$) and recognise which one a problem is asking for in a single reading.
3. Price an **arithmetic gradient** (`0, G, 2G, …`) with $P/G$ and $A/G$, and recognise its hidden presence in maintenance, churn, and growth lines.
4. Price a **geometric gradient** ($A_1, A_1(1+g), …$) with the closed-form expression — including the $i = g$ edge case and the "growth-adjusted rate" trick $i^* = (i-g)/(1+g)$.
5. State and apply the **definition of equivalence**: two cash-flow streams are equivalent at rate $i$ iff they share the same PV at $i$ — and explain why equivalence is rate-dependent.
6. **Decompose** an arbitrary software-project cash flow into a sum of the four canonical shapes (single sum, uniform, arithmetic, geometric) and price each piece separately.

---

## Lecture Map — Six Movements, Four Shapes, One Principle

By the end of today you should be able to **look at any cash flow and immediately see the shapes inside it**. The whole financial world of small projects is built from four primitives; the rest is composition.

| § | Topic | Minutes |
|---|-------|--------:|
| I | The cash flow diagram — drawing money in time | 15 |
| II | Uniform series — the same number, every period | 20 |
| III | Arithmetic gradient — the same *increment*, every period | 15 |
| IV | Geometric gradient — the same *percentage*, every period | 15 |
| V | Equivalence — when two streams are "worth the same" | 15 |
| VI | Composition — real cash flows as sums of clean pieces | 20 |
| — | Discussion, homework, Q&A | 10 |

!!! note "Prerequisite from Lecture 3"
    This lecture assumes you are comfortable with the **time value of money** and the single-sum factors $F = P(1+i)^n$ and $P = F(1+i)^{-n}$. If those don't yet feel automatic, revisit the Lecture 3 page first — every factor today is a sum of these.

---

## § I. The Cash Flow Diagram — Drawing Money in Time

The single most useful skill in this entire course is **drawing the picture before you compute anything**. A cash flow diagram (CFD) is a one-dimensional timeline with vertical arrows representing money flowing in (up) and out (down). It is to engineering economics what a free-body diagram is to mechanics: if you cannot draw it, you cannot solve it.

### The grammar of the diagram — four conventions

A CFD is a *convention*, not a physical object. Four rules let any two engineers read each other's diagrams without ambiguity:

| Convention | Rule | Why it matters |
|-----------|------|----------------|
| **1. Perspective** | Draw from *one* party's point of view — usually the project owner. State it on the diagram. | An "income" to you is an "expense" to the vendor; the same project has two valid diagrams. |
| **2. Arrows** | Up = cash in · Down = cash out. Length suggests magnitude, but the *number* is the truth. | Length is a hint; the label is the contract. |
| **3. Time origin** | $t = 0$ is always *now* — the moment of the decision, not the start of the calendar. | "Year 1" means "end of year 1", which is 12 months from now. |
| **4. End-of-period** | Unless stated, every cash flow lands at the *end* of the period in which it occurred. | This is the convention that makes formulas line up; deviations must be noted. |

### A small software project, drawn in full

Consider a four-year internal tool: **$100K** to build at $t=0$, then **$40K** in net benefits at the end of each of years 1–4.

```
         $40K   $40K   $40K   $40K
          ↑      ↑      ↑      ↑
   ───────┼──────┼──────┼──────┼─────── t
          1      2      3      4
   ↓
  $100K
  (build)
```

This single diagram answers more questions than three paragraphs of prose:

- The build cost is **non-recurring** (one arrow at $t=0$).
- The benefits form a **uniform series** of four payments — they will be priced with $P/A$ in § II.
- The project's PV is $-100\text{K} + 40\text{K} \cdot (P/A, i, 4)$ — almost mechanical once the picture is drawn.

The corresponding [slide deck](slides/lecture-04.html) includes a fully-styled SVG version of this diagram so you can refer back to it during the rest of today's material.

### Five pitfalls that quietly invalidate a CFD

| Pitfall | What happens | How to catch it |
|---------|--------------|-----------------|
| **Mixing perspectives mid-diagram** | An arrow flips direction halfway through. | Re-label every arrow with sign from a single point of view. |
| **Treating "year 1" as $t=1$ vs. $t=0$ inconsistently** | Off-by-one shifts the whole answer by a factor of $(1+i)$. | Write "**$t=0$ = today, end-of-period convention**" on every diagram. |
| **Forgetting the initial outflow** | The build cost is "obvious" and gets dropped. | Always *write* it, even if it feels silly. |
| **Skipping zero years** | "Nothing happens year 3" — but the timeline must still show year 3. | Tick every period; leave the position blank only by intent. |
| **Drawing nominal arrows for percentages** | A 10%-growing line drawn as four equal arrows. | If the cash flow has a growth rate, it is *not* uniform — see § IV. |

Most computational errors in this course originate from a bad picture, not a bad formula. **Draw twice, compute once.**

---

## § II. Uniform Series — The Same Number, Every Period

A **uniform series** is the simplest non-trivial cash flow: $A$ dollars at the end of each of $n$ periods. It is everywhere — mortgages, leases, salaries, subscriptions, loan repayments. The four standard factors all relate the uniform amount $A$ to either a present-value lump sum $P$ or a future-value lump sum $F$.

### Four directions, four factors

| Factor | Reads as | Formula | When to use it |
|-------:|----------|---------|----------------|
| $P/A$ | "Given $A$, find $P$" | $P = A \cdot \dfrac{(1+i)^n - 1}{i(1+i)^n}$ | Lease/subscription PV; loan principal from payments |
| $F/A$ | "Given $A$, find $F$" | $F = A \cdot \dfrac{(1+i)^n - 1}{i}$ | Sinking fund; future balance of a savings stream |
| $A/P$ | "Given $P$, find $A$" | $A = P \cdot \dfrac{i(1+i)^n}{(1+i)^n - 1}$ | **Capital recovery** — annual payment on a loan |
| $A/F$ | "Given $F$, find $A$" | $A = F \cdot \dfrac{i}{(1+i)^n - 1}$ | Sinking-fund deposit to hit a future target |

Memorise the **two formulas** for $P/A$ and $F/A$; the other two are just their reciprocals. Every factor table you will see — and every spreadsheet `PV`/`PMT`/`FV` function — is one of these four in disguise.

### Why these formulas are not magic — a 60-second derivation

$P/A$ is just a finite geometric series. Each payment $A$ has PV $A/(1+i)^t$, so:

$$
P = \sum_{t=1}^{n} \frac{A}{(1+i)^t} = A \cdot \frac{1 - (1+i)^{-n}}{i} = A \cdot \frac{(1+i)^n - 1}{i(1+i)^n}
$$

If you forget the factor in an exam, derive it from this sum in under a minute. Engineers who *understand* the formula never need to memorise the others — they fall out of algebra.

### Worked example 1 — $P/A$: A $1,500/month rental promise

A vendor offers to pay you **$1,500/month** for 36 months in exchange for software you will deliver today. Your MARR is **10%/year, compounded monthly** → $i = 0.10/12 \approx 0.00833$ per month, $n = 36$.

$$
P = 1{,}500 \cdot \frac{(1.00833)^{36} - 1}{0.00833 \cdot (1.00833)^{36}} \approx 1{,}500 \cdot 30.991 \approx \$46{,}486
$$

If your build cost is below ≈ $46.5K, the deal is attractive at your MARR — but only if the vendor's credit risk is acceptable. (Lecture 7 handles risk explicitly.)

### Worked example 2 — $A/P$: Server purchase vs. cloud

You can buy a server for **$80,000** today (5-year life, no salvage) or rent equivalent cloud capacity at **$X/month**. At $i = 12\%/\text{yr}$ compounded monthly, the buy option's equivalent monthly cost is:

$$
A = 80{,}000 \cdot \frac{0.01 \cdot (1.01)^{60}}{(1.01)^{60} - 1} \approx \$1{,}779/\text{month}
$$

So *only if cloud quotes you less than ≈ $1,779/month* is renting cheaper at this MARR. This is the canonical **build-vs-buy** template that Lecture 5 will extend with NPV/IRR.

---

## § III. Arithmetic Gradient — The Same *Increment*, Every Period

A **pure arithmetic gradient** is a cash flow that starts at zero and grows by a fixed amount $G$ each period: $0, G, 2G, 3G, \ldots, (n-1)G$. It is rare in pure form but extremely common as an *additive component*: a uniform base plus an arithmetic ramp.

```
                              5G
                       4G     ↑
                3G     ↑      │
         2G     ↑      │      │
   G     ↑      │      │      │
   ↑     │      │      │      │
───┼─────┼──────┼──────┼──────┼──────┼── t
   1     2      3      4      5      6
   ↑ 0 at t=1 — this is the convention
```

### Two factors that do the work

| Factor | Reads as | Formula |
|-------:|----------|---------|
| $P/G$ | "Given gradient $G$, find $P$" | $P = G \cdot \dfrac{1}{i}\left[\dfrac{(1+i)^n - 1}{i(1+i)^n} - \dfrac{n}{(1+i)^n}\right]$ |
| $A/G$ | "Given $G$, find equivalent uniform $A$" | $A = G \cdot \left[\dfrac{1}{i} - \dfrac{n}{(1+i)^n - 1}\right]$ |

**Conceptual shortcut.** $A/G$ tells you that "a gradient of $G$ feels like an extra uniform of $A$ on top of your base." This is exactly the conversion you want when comparing a ramping cash flow to a flat one.

### Worked example — Rising maintenance cost

A new system costs **$20K** to operate in year 1. Maintenance experience says operating cost **rises by $5K each year** for five more years. With $i = 8\%$:

- The cash flow is **uniform $20K** + **arithmetic gradient with $G = 5K$, $n = 6$**.
- $PV_{\text{uniform}} = 20{,}000 \cdot (P/A, 8\%, 6) = 20{,}000 \cdot 4.6229 \approx \$92{,}458$.
- $PV_{\text{gradient}} = 5{,}000 \cdot (P/G, 8\%, 6) = 5{,}000 \cdot 10.523 \approx \$52{,}615$.
- **Total PV of 6-year operating cost ≈ \$145,073.**

The lesson: a $5K/year increment **adds more than 50%** to the base PV over six years. Engineers who price only the *first-year* operating cost dramatically under-estimate lifecycle cost — this is one of the most common cost-modelling errors in industry.

---

## § IV. Geometric Gradient — The Same *Percentage*, Every Period

A **geometric gradient** grows by a constant *factor* $(1+g)$ each period. It is the natural shape for **inflation, SaaS price escalation, user growth, churn decay, and most market processes**.

$$
A_t = A_1 (1 + g)^{t-1}, \quad t = 1, 2, \ldots, n
$$

### The closed form — and the edge case

For $i \neq g$:

$$
P = A_1 \cdot \frac{1 - \left(\dfrac{1+g}{1+i}\right)^n}{i - g}
$$

For the special case $i = g$, the formula degenerates ($\div 0$), and the correct expression is:

$$
P = \frac{n \cdot A_1}{1 + i}
$$

This is a real edge case in software finance: when your discount rate happens to equal your growth rate, the PV is *linear* in $n$, not exponentially compressed. Forgetting this gives a divide-by-zero in spreadsheets.

### The growth-adjusted rate $i^*$ — the practical trick

Define $i^* = \dfrac{i - g}{1 + g}$. Then:

$$
P = A_1 \cdot \frac{(P/A, i^*, n)}{1 + g}
$$

That is: a **geometric gradient at rate $g$, discounted at $i$**, is *equivalent to a uniform series* at the modified rate $i^*$, scaled by $1/(1+g)$. This lets you use a standard $P/A$ table even when the cash flow grows — a huge convenience in exams and back-of-envelope work.

### Worked example — The syllabus problem

> *A SaaS subscription grows 10% per year for five years. Initial annual payment is $24,000 and the discount rate is 9%. Compute the PV of the five-year revenue stream.*

Here $A_1 = 24{,}000$, $g = 0.10$, $i = 0.09$, $n = 5$. Direct application of the formula:

$$
P = 24{,}000 \cdot \frac{1 - \left(\dfrac{1.10}{1.09}\right)^5}{0.09 - 0.10}
  = 24{,}000 \cdot \frac{1 - 1.0467}{-0.01}
  \approx 24{,}000 \cdot 4.624
  \approx \boxed{\$110{,}983}
$$

**Sanity check.** If you ignored growth and just summed five flat $24K payments at 9%, you would get $\$93{,}325$ — **\$17K too low**. Ignoring a 1-percentage-point gap between $g$ and $i$ costs you nearly 20% of the answer. Growth is not a rounding detail.

---

## § V. Equivalence — When Two Streams Are "Worth the Same"

So far we have priced streams. Equivalence is the *operational definition* that gives "worth the same" a precise meaning.

> **Definition.** Two cash-flow streams are **equivalent at interest rate $i$** if and only if they have **the same present value at $i$**. Equivalently, both have the same future value at $i$ at any common future date.

### Three properties of equivalence

1. **Symmetry.** If $A \equiv B$, then $B \equiv A$.
2. **Transitivity.** If $A \equiv B$ and $B \equiv C$, then $A \equiv C$ — so we can chain conversions through intermediate forms.
3. **Rate-dependence.** Equivalence is *not absolute* — it depends on $i$. Two streams equivalent at 8% can be wildly different at 12%.

### The rate-dependence table — same streams, different verdict

A **\$10,000 lump sum today** vs. **\$2,400 per year for 5 years**. At what rate $i$ are they equivalent?

Solve $10{,}000 = 2{,}400 \cdot (P/A, i, 5)$ → $(P/A, i, 5) = 4.1667$ → $i \approx 6.40\%$.

| If your MARR is… | The lump sum is worth | The 5-year stream is worth | Verdict |
|------------------|-----------------------:|---------------------------:|---------|
| **4%** | $10,000 | $10,683 | Take the stream |
| **6.40%** | $10,000 | $10,000 | **Equivalent** |
| **10%** | $10,000 | $9,098 | Take the lump sum |

Same two streams. Same dollar amounts. **The right answer changes with the rate.** This is why every project comparison in this course states a MARR up front (Lecture 5).

### The interest rate at which two streams are equivalent has a name: **IRR**

This is a forward-pointer to Lecture 5: solving "at what rate are streams A and B equivalent?" is exactly the IRR computation. Equivalence is the conceptual root of every comparison metric — NPV is "are they equivalent at the MARR?", IRR is "at what rate would they be equivalent?", payback is "after how long are cumulative cash flows equivalent to the initial outlay?".

---

## § VI. Composition — Real Cash Flows as Sums of Clean Pieces

Real software cash flows are rarely uniform. They are uniform plus a ramp, or a single sum followed by a growing tail, or a step-down at year 3. The discipline is to **decompose** the messy reality into a sum of the four canonical shapes — then price each piece separately.

### Four shapes, all the financial world

| Shape | Picture | Factor |
|-------|---------|--------|
| **Single sum** | one arrow | $P/F$ or $F/P$ |
| **Uniform series** | $n$ equal arrows | $P/A · F/A · A/P · A/F$ |
| **Arithmetic gradient** | $0, G, 2G, \ldots$ | $P/G · A/G$ |
| **Geometric gradient** | $A_1, A_1(1+g), \ldots$ | closed form (with $i^*$ shortcut) |

**Decomposition principle.** If a cash flow can be written as a *sum* of these shapes, its PV is the *sum of the PVs of each shape* — linearity of PV is the workhorse identity of this lecture.

### Worked example — A 3-piece SaaS contract

A vendor proposes the following SaaS deal:

- **$80,000** upfront integration fee (paid by you today).
- **$24,000/year**, growing 10%/year for 5 years (paid by you, annually).
- **$50,000** termination payment at end of year 5 (paid by you, if you cancel).

At $i = 9\%$, compute the PV cost.

| Piece | Shape | PV at $i = 9\%$ |
|-------|-------|----------------:|
| $80,000 upfront | Single sum at $t=0$ | $-\$80{,}000$ |
| $24K/yr, $g=10\%$, $n=5$ | Geometric gradient | $-\$110{,}983$ |
| $50K at $t=5$ | Single sum, $F/P$ | $-\$32{,}497$ |
| **Total PV cost** | | **$\approx -\$223{,}480$** |

A vendor selling this contract on "it's just $24K/year" framing has hidden **about $113K of cost in the upfront and tail** — slightly more than the headline-priced "main" payment stream. The right question is never "what's the monthly?" — it is always **"what is the PV?"**

### One-page factor cheat sheet — for the final

| Want | Have | Use |
|------|------|------|
| $P$ | Single $F$ at time $n$ | $P/F$: $P = F(1+i)^{-n}$ |
| $F$ | Single $P$ at $t=0$ | $F/P$: $F = P(1+i)^n$ |
| $P$ | Uniform $A$ for $n$ periods | $P/A$ |
| $A$ | Single $P$ | $A/P$ (capital recovery) |
| $F$ | Uniform $A$ | $F/A$ |
| $A$ | Future $F$ | $A/F$ (sinking fund) |
| $P$ | Pure gradient $G$ | $P/G$ |
| $A$ | Pure gradient $G$ | $A/G$ |
| $P$ | Geometric $A_1$, $g$ | closed form; or $P/A$ at $i^*$ |

If you can pick the right row in under five seconds while looking at a CFD, you are ready for the final.

---

## Class Discussion Prompts

Open floor, ~10 minutes.

1. Name a cash flow in your **own personal finances** (rent, tuition, a subscription, a phone payment, a saving plan) and identify which of the four shapes it is. Where does the analogy *break* — and what would you need to add to model it honestly?
2. Pick a software product or service in your group project. Sketch its 5-year cash flow on paper. Where is the **hidden gradient** — the line that looks flat but actually grows or decays each year?
3. A vendor's quote almost always frames cost as **a flat monthly number**. Why is that framing seductive — and what's the *PV truth* it hides? Give a concrete example you've seen.

---

## Homework 4 — Decomposition and Equivalence

**Due: Sunday 2026-06-07, before Lecture 5. Late: −10% per day.**

1. **Three decomposition CFDs.** For each of the following, draw the CFD *and* decompose into canonical shapes. Compute the PV at $i = 10\%$.
    1. A 3-year cloud contract: $5,000 setup, then $1,500/month for 36 months, with a $2,000 cancellation fee at the end.
    2. A maintenance schedule: $8,000 year 1, growing by $1,200 each year for 6 years.
    3. A 5-year licensing deal: $40,000 year 1, then growing 15%/year (renewal escalator).
2. **Four equivalence problems.** For each, state the rate at which the two streams are equivalent (find the IRR by inspection or trial-and-error to ±0.1%):
    1. \$50,000 now vs. \$10,000/year for 7 years.
    2. \$100,000 in 10 years vs. \$6,000/year for 10 years.
    3. \$25,000 now vs. \$3,500/year for 10 years.
    4. \$200,000 in 5 years vs. \$25,000/year for 8 years.
3. **Reflection (half page).** Pick one decomposition you did above. Argue *in plain English* whether your project should accept it, *at your team's assumed MARR*. State the MARR you chose and why.

**Submission.** A single PDF (handwritten CFDs are fine — photograph and embed) committed to the course repository at `submissions/HW4/<your-name>.pdf`.

**Office hours.** Tomorrow, 10:00–11:30, before Lecture 5.

---

## Key Vocabulary — Quick Reference

| Term | Plain-language meaning |
|------|------------------------|
| Cash flow diagram (CFD) | A timeline with arrows showing money in/out at each period, from a single perspective. |
| End-of-period convention | Unless stated otherwise, each period's cash flow lands at its *end*. |
| Uniform series | $n$ equal payments at the end of each period. |
| Arithmetic gradient | Cash flow growing by a fixed *amount* $G$ each period (pure form: $0, G, 2G, \ldots$). |
| Geometric gradient | Cash flow growing by a fixed *percentage* $g$ each period. |
| $P/A$ factor | Present-value factor for a uniform series. |
| $A/P$ factor | Capital-recovery factor — annual payment given a present sum. |
| $A/F$ factor | Sinking-fund factor — annual deposit to reach a future target. |
| $P/G$, $A/G$ | Arithmetic-gradient PV and equivalent-uniform factors. |
| Growth-adjusted rate $i^*$ | $i^* = (i-g)/(1+g)$ — lets you price a geometric gradient with $P/A$ at $i^*$. |
| Equivalence | Two streams are equivalent at rate $i$ iff they share the same PV at $i$. |
| Decomposition | Re-writing a messy cash flow as a sum of canonical shapes whose PVs add. |
| MARR | Minimum Acceptable Rate of Return — the discount rate used to make accept/reject decisions (Lecture 5). |

---

## References

- **Park, C. S.** *Contemporary Engineering Economics* — Chapters on cash flow diagrams, uniform/gradient series, and equivalence (the primary text for this lecture).
- **Newnan, D. G., Eschenbach, T. G., & Lavelle, J. P.** *Engineering Economic Analysis* — alternative treatment of the four standard factors with identical notation.
- **Boehm, B. W.** (1981). *Software Engineering Economics*. Prentice Hall — **Chapter 5** introduces equivalence as the basis for project comparison.
- *(Forward pointer)* **Lecture 5 — Investment Decisions** uses equivalence to define NPV, IRR, payback, and PI, and resolves the build-vs-buy template from Lecture 1.

---

*Prepared by Dr. Zhijiang Chen — Frostburg State University, Summer 2026.*
