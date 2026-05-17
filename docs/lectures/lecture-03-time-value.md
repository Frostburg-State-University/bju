---
title: "Lecture 3 — Time Value of Money"
author: Dr. Zhijiang Chen
date: 2026-06-05
session: 3
week: 14
room: SY109
---

# Lecture 3 — Time Value of Money

!!! info "Session Info"
    **Date:** Friday, 2026-06-05 &nbsp;·&nbsp; **Time:** 16:20–18:10 &nbsp;·&nbsp; **Room:** SY109
    **Instructor:** Dr. Zhijiang Chen

[**▶ Open the lecture slides — 110-minute web deck**](slides/lecture-03.html){ .md-button .md-button--primary target="_blank" }

!!! tip "Using the slide deck"
    The slides are a self-contained web presentation (Reveal.js, no install). Keyboard shortcuts: **F** for full-screen, **S** for speaker view, **Esc** for slide overview, **arrow keys** to navigate. Designed for a single 110-minute session.

---

## Learning Objectives

By the end of this lecture, you should be able to:

1. Explain in plain English **why** money has time value, naming the three forces — **opportunity, risk, inflation** — that together set a firm's discount rate.
2. Apply the two core single-sum formulas — **$FV = PV(1+i)^n$** and **$PV = FV(1+i)^{-n}$** — fluently, both forward and backward, with a calculator in under thirty seconds.
3. Distinguish **simple** from **compound** interest and explain why every serious engineering-economics calculation assumes compounding.
4. Convert any quoted rate between **nominal**, **effective annual**, and **continuous** conventions — and detect when a contract or vendor quote silently mixes them.
5. Solve for the **break-even discount rate** that makes two single-sum alternatives equivalent — the conceptual seed of IRR in Lecture 5.
6. Apply present-value discounting to a **real TCO worksheet** (the one you built in Lecture 2) and explain what the discounted number teaches you that the undiscounted total did not.

---

## Lecture Map — Four Formulas, One Question

By the end of today you should be able to answer one question on demand: **how much less is a dollar in year $n$ worth than a dollar today, at rate $i$?** Everything else in this course — NPV, IRR, payback, lifecycle cost, build-vs-buy — is a recombination of the answer to that single question.

| § | Topic | Minutes |
|---|-------|--------:|
| I | Why money has time value — three forces, one discount rate | 15 |
| II | Future value and present value — the two core formulas | 25 |
| III | Simple vs. compound interest — and why nobody uses simple any more | 15 |
| IV | Nominal, effective, and continuous rates — read the quote carefully | 20 |
| V | Worked software-decision examples | 20 |
| — | Discussion: what is *your* personal discount rate? · HW3 brief · Q&A | 15 |

!!! note "Bridge from Lecture 2"
    Yesterday we built a five-year TCO worksheet and learned to read it through four taxonomies. Today we learn that **a dollar in year 5 is not the same as a dollar in year 0** — so simply *adding* the rows of a TCO over-states the real cost. Today's formulas are the correction. By the end of Lecture 4, you will know how to price an entire cash flow stream, not just a single sum.

---

## § I. Why Money Has Time Value — Three Forces, One Rate

The central claim of this lecture is the most important quantitative idea in the entire course: **a dollar today is worth strictly more than a promise of a dollar in the future.** Not because money is "magical," but because three independent forces drive a wedge between *now* and *later*. The size of that wedge — the **discount rate** — is the number we use to translate any future cash flow into today's terms.

### Three forces — and the discount rate is their sum

| Force | Why it makes future money worth less today | Order of magnitude (2026) |
|-------|--------------------------------------------|---------------------------|
| **Opportunity** | A dollar today can be invested and grow. $1 at 8% becomes $1.47 in 5 years. Holding it idle costs you that growth. | 4–8% (risk-free + a margin) |
| **Risk** | A future dollar may not arrive. Vendors fail; counterparties default; projects get cancelled. | 1–10% (project-specific) |
| **Inflation** | The future dollar buys less. 3% inflation cuts purchasing power ~14% in 5 years. | 2–4% (recent US average) |

These three forces do not act in isolation; they **sum into a single blended rate** — the discount rate $i$ — that the firm uses for every project decision. When finance hands you a 10% discount rate, that 10% is *already* an opportunity-plus-risk-plus-inflation composite for *your* firm in *this* market. It is not a number you get to negotiate per project; it is a policy parameter.

!!! quote "Working principle for this course"
    The discount rate is not a calculation — it is a *commitment*. It is the firm saying "below this return, we would rather do something else with the money."

### Why software engineers should care more than other engineers

Software has three properties that make time-value reasoning unusually important:

- **Long tails.** A system built in 6 months may live for 10 years (recall Boehm's iceberg from Lecture 2). Most of its cost arrives *late*. Discounting matters more when the horizon is long.
- **Recurring cost lines.** SaaS fees, cloud bills, and token spend repeat every period. A 1-percentage-point change in the discount rate moves a 10-year recurring stream by 5–8% in PV — bigger than most build-cost estimation errors.
- **Optionality everywhere.** Should we license now or in three years? Refactor this quarter or next? Each "now or later" question is a present-value comparison in disguise.

Engineers who lack time-value fluency systematically **under-value patience** — they shovel work into the present, then discover the present is more expensive than the future would have been. Senior engineers learn to *price* delay rather than fear it.

### The discount-rate ladder — orders of magnitude

| Decision-maker | Typical discount rate | Why |
|----------------|----------------------:|-----|
| US Treasury (risk-free) | ~4–5% | No default risk; nominal rate ≈ inflation + small term premium |
| Mature public company | 8–12% | WACC — weighted cost of debt and equity capital |
| Software startup (VC-backed) | 20–35% | High risk; investors demand a multiple |
| Personal finance — patient saver | 4–8% | Roughly equal to the safe return they could earn |
| Personal finance — credit-card user | 20–30% | Their *revealed* discount rate is the APR they pay |

The same project can be **profitable at the corporate rate and unprofitable at the startup rate**. There is no universal "right" discount rate — only the right one *for this decision-maker, at this point in time*.

---

## § II. Future Value & Present Value — The Two Core Formulas

Almost every formula in the rest of this course is a re-arrangement of two equations. If you can write them on a napkin from memory, you have already done 80% of the work of engineering economics.

### Future value of a single sum

$$
FV = PV \cdot (1 + i)^n
$$

Where:

- $PV$ = present value — the dollar amount you have *today*, at $t = 0$
- $i$ = interest rate per period (decimal — 8% is $i = 0.08$)
- $n$ = number of compounding periods
- $FV$ = future value — what the present sum grows to by the end of period $n$

The factor $(1+i)^n$ is called the **single-payment compound-amount factor** and is written $F/P$. Read "F-over-P at i percent for n periods." You will see this notation everywhere in Lectures 4–6.

### Present value of a single sum

$$
PV = \frac{FV}{(1+i)^n} = FV \cdot (1 + i)^{-n}
$$

The factor $(1+i)^{-n}$ is the **single-payment present-worth factor** $P/F$. **PV and FV are the same equation rearranged** — same parameters, same compounding assumption, opposite direction.

!!! note "Sign of an equation, not a rule of arithmetic"
    PV and FV are not separate formulas to memorise. They are the same algebraic statement read in two directions. If you only remember $FV = PV(1+i)^n$, you can always solve for PV by dividing. The advantage of writing both is that *each is the natural form for a different question*.

### Worked example 1 — FV of an investment

You invest **$100 today** at **8% annually**. What is it worth in **5 years**?

$$
FV = 100 \cdot (1.08)^5 = 100 \cdot 1.4693 = \$146.93
$$

That is, **$100 grows by ≈ 47% over 5 years** at 8% compounding. A useful mental check: the **Rule of 72** says money roughly doubles every $72/i$ years. At 8%, $72/8 = 9$ years to double — so 5 years should be a bit more than halfway there. 47% is on the money.

### Worked example 2 — PV of a future receipt

A vendor will pay you **$100,000 in 5 years**. At your firm's **10% cost of capital**, what is that promise worth to you today?

$$
PV = \frac{100{,}000}{(1.10)^5} = \frac{100{,}000}{1.6105} = \$62{,}092
$$

Translation: a guaranteed $100K in five years is economically equivalent to **$62,092 today** at this firm's discount rate. If anyone offers you more than $62K today for the right to that promise, you should take the cash; if less, you should hold the promise.

### Worked example 3 — License now or license later?

A vendor offers two pricing terms for the same software:

| Option | Pay now | Pay in 3 years | PV at 8% |
|--------|--------:|---------------:|---------:|
| **A. Upfront** | $50,000 | — | **$50,000** |
| **B. Deferred** | — | $60,000 | **$47,629** |

$$
PV_B = \frac{60{,}000}{(1.08)^3} = \frac{60{,}000}{1.2597} = \$47{,}629
$$

**B has the higher nominal price but the lower present value.** Provided you are confident in the vendor's solvency three years out, B is the better deal at 8%. Notice how much the rate matters: at **4%**, $PV_B = 60{,}000 / 1.1249 = \$53{,}340$, and Option A wins. The discount rate is a **policy choice** — and the verdict turns on it.

!!! quote "First rule of vendor negotiation"
    "What is the headline price?" is the wrong question. "What is the PV at our discount rate?" is the right one. The two answers can favour opposite vendors.

### Five common errors when applying PV/FV

| Error | What happens | How to avoid |
|-------|--------------|--------------|
| Using a *period* rate with an *annual* exponent | Off by orders of magnitude | If $i$ is monthly, $n$ must be in months |
| Forgetting that 8% is $i = 0.08$, not $i = 8$ | $(1+8)^5$ = catastrophe | Always convert percent to decimal first |
| Mixing nominal and effective rates | PV silently wrong by 1–3% | Identify the convention before computing (§ IV) |
| Discounting cash flows that are already in real (inflation-adjusted) terms | Double-counting inflation | Match real with real, nominal with nominal |
| Treating "year 1" as $t=0$ | Off by a factor of $(1+i)$ | End-of-period convention: year 1 = $t=1$ |

---

## § III. Simple vs. Compound Interest — and Why Nobody Uses Simple Any More

Two distinct accounting rules can both be called "interest." The difference looks trivial over a single year and grows enormous over a decade. Engineering-economics formulas — and almost every real financial instrument — assume **compounding**.

### Two ways to earn interest

| | **Simple interest** | **Compound interest** |
|--|---------------------|----------------------|
| Formula | $FV = PV \cdot (1 + i \cdot n)$ | $FV = PV \cdot (1 + i)^n$ |
| What earns interest | Only the *original principal* | Principal *plus* all previously accrued interest |
| Growth shape | Linear in time | Exponential in time |
| Where you still see it | A few short-term consumer notes, some auto loans | Everywhere else — bonds, mortgages, savings, NPV math |

### A small choice, a large difference

Starting with **$1,000 at 8%** for **20 years**:

| Method | Calculation | Result |
|--------|-------------|-------:|
| **Simple** | $1{,}000 \cdot (1 + 0.08 \cdot 20)$ | **$2,600** |
| **Compound** | $1{,}000 \cdot (1.08)^{20}$ | **$4,661** |

The compound result is **almost double the simple one** over the same horizon at the same rate. The gap is the "interest on interest" — and it is the entire reason wealth accumulates exponentially rather than linearly.

!!! note "Rule of 72 — engineer's mental math"
    For compound interest at rate $i\%$, money roughly doubles every $72/i$ years. At 8%, money doubles every 9 years. So 20 years at 8% is a bit more than **two doublings** — roughly $4\times$. The exact answer is $4.66\times$. Off by 15%, in under 5 seconds, no calculator. This is the kind of mental approximation senior engineers use to sanity-check spreadsheets.

### Why simple interest survives at all

Simple interest is computationally trivial — no calculator needed — and was the norm before the 17th century. Today it survives mainly in three places: very short-term consumer credit (where the difference is negligible), some legal/regulatory settings that *require* simple interest to limit punitive accumulation, and the occasional auto-loan disclosure. Outside those niches, **assume compounding unless the document explicitly says otherwise**. Most engineering-economics textbooks (Park, Newnan, Boehm) suppress simple interest entirely after the first chapter.

---

## § IV. Nominal, Effective, and Continuous Rates — Read the Quote Carefully

The same "18%" can mean three different things depending on the compounding convention. Mis-identifying which one is in front of you is one of the most common — and most expensive — quiet errors in finance.

### Three conventions for one rate

| Convention | Formula | What "18% with 12 periods" actually means |
|-----------|---------|-------------------------------------------|
| **Nominal annual rate** ($r$) | The quoted "headline" rate. Ignores compounding frequency. | $r = 18.00\%$ |
| **Effective annual rate** ($i_{\text{eff}}$) | $i_{\text{eff}} = \left(1 + \dfrac{r}{m}\right)^m - 1$ | $(1 + 0.015)^{12} - 1 = \mathbf{19.56\%}$ |
| **Continuous compounding** | $i_{\text{cont}} = e^r - 1$ | $e^{0.18} - 1 = \mathbf{19.72\%}$ |

The three numbers — **18.00%, 19.56%, 19.72%** — all describe "the same" 18%. The gap between them is the silent cost (or silent benefit) of compounding frequency. **Credit cards quote nominal, bonds quote effective, derivatives quote continuous.** A junior analyst who confuses them at a bank scaled $500M deal once posted an ~$8M valuation error in a single quarter. The error compounds — literally.

### The effective annual rate — derivation

If $r$ is the nominal annual rate and interest compounds $m$ times per year, then each sub-period rate is $r/m$ and there are $m$ such periods. One year of growth is:

$$
(1 + r/m)^m
$$

Subtract 1 to express the result as an *equivalent annual rate*:

$$
i_{\text{eff}} = \left(1 + \frac{r}{m}\right)^m - 1
$$

As $m$ grows, $i_{\text{eff}}$ grows monotonically — but with diminishing returns. In the limit $m \to \infty$, the formula collapses to the continuous form.

### Continuous compounding — when periods shrink to zero

$$
FV = PV \cdot e^{r n} \qquad PV = FV \cdot e^{-r n}
$$

The limit $m \to \infty$ of $(1 + r/m)^{m}$ is exactly $e^r$ — the natural exponential. Continuous compounding is the smooth, calculus-friendly cousin of compound interest. It is used widely in **derivatives pricing, treasury-bond math, and academic finance**. For engineering-economics decisions we usually stay with annual or monthly compounding, but you should be able to translate.

| $r$ | Effective annual ($m=12$) | Continuous |
|----:|--------------------------:|-----------:|
| 5% | 5.116% | 5.127% |
| 10% | 10.471% | 10.517% |
| 18% | 19.562% | 19.722% |
| 25% | 28.073% | 28.403% |

Notice that the spread between effective and continuous **widens with $r$ and with horizon**. At low rates and short periods the difference is rounding; at high rates and long horizons it is material.

### A small worked example — the 18% APR that wasn't

You sign a credit-card contract advertising **"18% APR."** Your statement compounds monthly. After one year on a $1,000 balance, your actual debt (no payments) is:

$$
FV = 1{,}000 \cdot (1 + 0.015)^{12} = 1{,}000 \cdot 1.1956 = \$1{,}195.62
$$

You owe **$195.62 in interest**, not $180. The contract is honest — it disclosed the nominal rate and the compounding frequency. You did not multiply.

!!! quote "First rule of reading a contract"
    When you see an interest rate, *do not compute anything* until you have identified its convention. Nominal, effective, or continuous? Period or annual? Until that is settled, every downstream number is suspect.

---

## § V. Worked Software-Decision Examples

We now apply the four formulas to three software-decision templates that will recur throughout the course. The first is the discounted TCO from yesterday; the second is a break-even rate problem that foreshadows IRR; the third is an everyday SaaS choice you will likely face within a year.

### Example 1 — Discounting yesterday's TCO worksheet

A team estimates the following annual cash outflows for an internal AI tool. Find the **PV of total cost** at a 10% discount rate.

| Year | Cash outflow | Discount factor $(1.10)^{-n}$ | PV |
|-----:|-------------:|------------------------------:|---:|
| 0 — build | $80,000 | 1.0000 | $80,000 |
| 1 | $30,000 | 0.9091 | $27,273 |
| 2 | $30,000 | 0.8264 | $24,793 |
| 3 | $30,000 | 0.7513 | $22,539 |
| 4 | $30,000 | 0.6830 | $20,490 |
| **Total** | **$200,000** | — | **$175,095** |

Without discounting, the total cost is **$200,000**. With it, the same stream of payments is worth **$175,095** — a **12% reduction**. The cost has not gone away; it has been *re-stated in today's dollars*, which is the only honest way to compare cash flows that arrive at different times.

**What changed about your reading of the project?** Three things:

1. The build cost (year 0) now looks proportionally *bigger* — it is $80K of a $175K PV (46%), not of a $200K nominal (40%).
2. Later years contribute *less* than their face value suggests. Year 4's $30K is really only $20K in today's terms.
3. If two alternatives have identical undiscounted totals but different *timing*, PV is the only way to tell them apart.

### Example 2 — Solving for the break-even rate (IRR preview)

**$50,000 today, or $80,000 in 5 years.** At what discount rate are these two equivalent?

Set the present values equal:

$$
50{,}000 = \frac{80{,}000}{(1+i)^5} \quad \Rightarrow \quad (1+i)^5 = 1.60 \quad \Rightarrow \quad i = 1.60^{0.2} - 1 = \mathbf{9.86\%}
$$

This **break-even discount rate** has a name we will formalise in Lecture 5: the **Internal Rate of Return (IRR)** of the difference between the two alternatives. The decision rule is mechanical:

- If your firm's discount rate is **below 9.86%**, the deferred $80K is more valuable — take it.
- If your firm's discount rate is **above 9.86%**, the present $50K is more valuable — take it.
- At exactly 9.86%, you are indifferent — and the choice should be made on non-financial grounds (risk, optionality, relationships).

**Knowing your discount rate is half the decision.** The other half is solving for the rate at which alternatives flip.

### Example 3 — Quarterly billing vs. annual billing

A SaaS vendor offers **$1,200/year billed annually upfront**, or **$300/quarter billed in advance**. Your firm's discount rate is **8% annual** (≈ 1.943% per quarter effective).

| Plan | Cash flow (year 1) | Effective PV |
|------|--------------------|-------------:|
| Annual | $1,200 at $t=0$ | $1,200.00 |
| Quarterly | $300 at $t=0, \tfrac{1}{4}, \tfrac{1}{2}, \tfrac{3}{4}$ | $1,165.93 |

Quarterly billing saves the customer **≈ $34/year** in PV terms. Modest in absolute dollars, but the principle scales linearly with contract size: a $1.2M annual contract billed quarterly saves **$34,000/year** in PV. This is exactly why every SaaS vendor offers a **10–20% discount for annual upfront payment** — they are pricing the customer's option to defer.

!!! note "A practical negotiating lever"
    Whenever a vendor offers an "annual upfront" discount, compute the *implied discount rate they are charging you* for the deferral option. If their implied rate exceeds your discount rate, take the annual deal; if it falls below, take the periodic one. This single calculation has paid for more than one engineer's lunch.

---

## Class Discussion — What Is *Your* Personal Discount Rate?

**Open floor, ~10 minutes. Pair up with the person next to you.**

I will offer you **$100 today** or **$X in one year**. What value of $X$ just barely tips you into waiting? That number reveals your **personal discount rate**: $r = X/100 - 1$.

Run the exercise in three rounds with your partner — actually trade offers verbally before computing:

1. **Round 1 — small stakes:** $100 today vs $X in one year. Write down the smallest $X$ that makes you wait.
2. **Round 2 — bigger stakes:** $1,000 today vs $X in one year. Does your implied rate *change*? Most people's does — they get more patient at higher stakes. This is called a **framing effect** and it tells you that "rationality" is harder than the formulas suggest.
3. **Round 3 — corporate comparison:** is your personal rate higher or lower than a typical firm's 8–15%? If yes, why? If no, why?

### Use cases to discuss in your pair

- **A junior developer with credit-card debt at 22% APR** has a *revealed* personal discount rate of at least 22%, even if she says she "values the future." Her actions price the future at less than her words do.
- **An engineer evaluating two job offers** — $120K now plus 4% raises, vs. $100K now plus 12% raises — is implicitly running a PV calculation. The right answer depends on her discount rate, her time horizon at the firm, and the *risk* of the higher-growth path.
- **A team choosing between refactoring now (1 sprint of effort) or later (3 sprints in 18 months due to accrued debt)** is making a discount-rate decision. Teams that always defer have an *implicit infinite* personal discount rate for technical debt — which is why the debt eats them.

!!! quote "What this discussion usually reveals"
    Most students report personal discount rates between **20% and 50%** — substantially higher than the corporate rates they'll work with professionally. This gap is not irrationality; it's *liquidity preference* combined with personal risk. But it does explain why engineers consistently *under-invest* in future maintenance, documentation, and tooling: their personal rate makes those investments look bad even when the firm's rate would approve them.

After 6 minutes, **three pairs will share** their two numbers (round-1 rate and round-3 rate) and one sentence about why they differ.

---

## Homework 3 — Make the Formulas Yours

**Due: Sunday 2026-06-07, before Lecture 5. Late: −10% per day.**

1. **Six PV/FV exercises.** For each, show the formula, the substitution, and the final answer. Round to the nearest dollar.
    1. FV of $5,000 invested for 8 years at 7% annual compounding.
    2. PV of $25,000 received in 6 years at a 9% discount rate.
    3. How long does it take for $1,000 to triple at 6% compound interest? (Hint: solve for $n$.)
    4. What rate doubles $10,000 in 8 years?
    5. PV of $1,000 received at the end of each of years 3, 4, and 5, discounted at 10%. (Sum three single-sum PVs — *no* annuity factor needed yet.)
    6. FV of $500 invested today *and* another $500 invested at end of year 3, both growing at 8% to end of year 7.
2. **Rate conversion.** A credit-card contract quotes **24% APR, compounding monthly**. Compute (a) the **effective annual rate** and (b) the **equivalent continuous-compounding rate**. Show both formulas.
3. **Discount your TCO.** Take the five-year TCO worksheet you built for Homework 2. Apply a **10% discount rate** to every year's outflow and compute the **PV of total cost**. Compare against your undiscounted total and write **one paragraph (3–5 sentences)** on what changed about your reading of the project — which year suddenly looks more (or less) significant, and what that implies for any decision you would make about the tool.
4. **One break-even problem.** A vendor offers either **$35,000 today** or **$50,000 in 4 years**. At what discount rate are these equivalent? At your firm's assumed 10% discount rate, which is the better deal — and by how much in PV?

**Submission.** A single PDF (handwritten work is fine — photograph and embed) committed to the course repository at `submissions/HW3/<your-name>.pdf`.

**Office hours.** Tomorrow, 10:00–11:30, before Lecture 4.

---

## Further Reading

- **Park, C. S.** *Contemporary Engineering Economics* — chapter on time value of money. The primary text for this lecture; worked examples almost identical to the ones above.
- **Boehm, B. W.** (1981). *Software Engineering Economics*. Prentice Hall — **Chapter 4: Present-Worth Analysis**. Boehm's framing of why software cost models without PV are misleading; still the cleanest statement of the software-specific case.
- **Brealey, R. A., Myers, S. C., & Allen, F.** *Principles of Corporate Finance* — **Chapters 2–3**. The corporate-finance perspective: where firms' discount rates actually come from (WACC, risk premia, market data).
- **Newnan, D. G., Eschenbach, T. G., & Lavelle, J. P.** *Engineering Economic Analysis* — alternative textbook treatment using identical notation. Useful for cross-checking factor formulas on exam day.
- *(Forward pointer)* **Lecture 4 — Cash Flow Analysis & Equivalence** will extend today's single-sum formulas to *streams* of payments — uniform, arithmetic-growing, and geometric-growing — using the $P/A$, $P/G$, and geometric-gradient factors.

---

## Key Vocabulary — Quick Reference

| Term | Plain-language meaning |
|------|------------------------|
| Present value (PV) | The value today of a cash flow occurring in the future, discounted at rate $i$. |
| Future value (FV) | The value at a future date of a cash flow occurring today, compounded at rate $i$. |
| Discount rate ($i$) | The rate used to translate future dollars into today's terms — sum of opportunity, risk, and inflation forces. |
| Simple interest | Interest earned only on the original principal — linear growth. |
| Compound interest | Interest earned on principal *plus* accumulated interest — exponential growth. |
| Nominal annual rate ($r$) | The headline annual rate before any compounding adjustment. |
| Effective annual rate ($i_{\text{eff}}$) | The actual annual growth after sub-period compounding: $(1+r/m)^m - 1$. |
| Continuous compounding | The limit as $m \to \infty$: $FV = PV \cdot e^{rn}$. |
| Rule of 72 | Money roughly doubles in $72/i$ years at rate $i\%$. |
| Personal discount rate | The rate that describes *your own* time preference — usually higher than a firm's. |
| Break-even discount rate | The rate that makes two alternatives equivalent in PV — a forward pointer to IRR (Lecture 5). |
| End-of-period convention | Unless stated otherwise, cash flows are placed at the *end* of the period they occur in. |

---

## What today bought you

- **One quantitative claim:** $1 today $\neq$ $1 tomorrow. The gap has a name (discount rate) and three drivers (opportunity, risk, inflation).
- **Two core formulas:** $FV = PV(1+i)^n$ and $PV = FV/(1+i)^n$. Almost every formula in the rest of this course is a recombination of these two.
- **One reading skill:** always identify which rate convention is in play before computing anything. Nominal $\neq$ effective $\neq$ continuous — and the gaps compound.
- **One self-knowledge:** your *own* personal discount rate is probably 20–50%, far above a firm's 8–15%. That gap is why engineers under-invest in their own future maintenance.
- **One foreshadowing:** the break-even rate of Example 2 is the IRR you will meet in Lecture 5. Today you solved it on a calculator; in two weeks you'll solve it on a cash-flow stream.

---

*Prepared by Dr. Zhijiang Chen — Frostburg State University, Summer 2026.*
