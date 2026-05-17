---
title: "Lecture 8 — Cost Estimation I: Size Metrics & Function Points"
author: Dr. Zhijiang Chen
date: 2026-06-10
session: 8
week: 15
room: YF302
---

# Lecture 8 — Cost Estimation I: Size Metrics & Function Points

!!! info "Session Info"
    **Date:** Wednesday, 2026-06-10 &nbsp;·&nbsp; **Time:** 16:20–18:10 &nbsp;·&nbsp; **Room:** YF302
    **Instructor:** Dr. Zhijiang Chen

[**▶ Open the lecture slides — 110-minute web deck**](slides/lecture-08.html){ .md-button .md-button--primary target="_blank" }

!!! tip "Using the slide deck"
    The slides are a self-contained web presentation (Reveal.js, no install). Keyboard shortcuts: **F** for full-screen, **S** for speaker view, **Esc** for slide overview, **arrow keys** to navigate. Designed for a single 110-minute session.

---

## Learning Objectives

By the end of this lecture, you should be able to:

1. Explain **why size estimation is unavoidable** for any project that has a budget, a schedule, or a price — and **why it is empirically hard**, with industry error ranges of 2× to 4×.
2. State the **strengths and weaknesses of Lines of Code (LOC)** as a size metric, and explain why the rise of AI-assisted code generation in 2025–2026 has **broken the LOC ↔ effort link** that 1980s estimation relied on.
3. Count **Unadjusted Function Points (UFP)** for a small specification by identifying the five IFPUG function types — **EI, EO, EQ, ILF, EIF** — and applying the standard low/average/high complexity weights.
4. Apply the **Value Adjustment Factor (VAF)** using the **14 General System Characteristics** to convert UFP into **Adjusted Function Points (AFP)**.
5. Compare **Use-Case Points (Karner 1993)** to Function Points and choose one consistently across a project's alternatives.
6. Recognise and counter the three dominant **estimation biases** — optimism, anchoring, and the planning fallacy — and explain why a "method without a bias filter" is still a wrong number.
7. Read the **Group Project brief** released today and assemble a 3–4 person team with a candidate product by Lecture 11.

---

## Lecture Map — Six Movements, One Number

By the end of today you should be able to look at any software specification — yours or someone else's — and produce a **defensible number** for its size, knowing exactly which assumptions you have made and where the number is fragile.

| § | Topic | Minutes |
|---|-------|--------:|
| I | Why estimate? Why is it hard? | 10 |
| II | LOC — uses and abuses | 10 |
| III | IFPUG Function Points — the five types and the worked count | 30 |
| IV | Value Adjustment Factor (VAF) and the 14 GSC | 10 |
| V | Use-Case Points and modern variants | 10 |
| — | Discussion — *"How many Function Points is WeChat?"* | 10 |
| VI | Estimation biases — and the group project | 25 |
| — | Homework 8, Q&A | 5 |

!!! note "Prerequisite from Lectures 2–4"
    This lecture turns the **cost vocabulary** of Lecture 2 and the **cash-flow shapes** of Lecture 4 into a *number* you can feed into a spreadsheet. From Lecture 9 onward (COCOMO II, NPV/IRR comparisons), every model assumes you have a size estimate. Today is where that number first appears.

---

## § I. Why Estimate — and Why We Are So Bad At It

Every economic model in this course begins with a number that someone *made up*. Net present value, internal rate of return, payback, profitability index — all of them take a cost stream as input. **Without estimation, there is no economics.** And yet, estimation is one of the few engineering activities where being wrong by **a factor of two** is considered routine.

### Four reasons estimation is unavoidable

| Reason | What we need a number for | Failure mode if we skip it |
|--------|---------------------------|----------------------------|
| **Plan the work** | Schedule, staffing, dependency graph, critical path | "When will it ship?" → an honest answer is "I have no idea" |
| **Budget the work** | Funding requests, capital allocation, runway planning | Out-of-money before out-of-features |
| **Price the work** | Fixed-price contracts, internal chargeback, customer quotes | Sign a contract that loses money on every unit |
| **Manage the work** | Track progress vs plan; signal when to escalate | No baseline → no variance → no early warning |

Even a *wrong* estimate, used honestly, is more useful than no estimate. The estimate is the **baseline**; the deviation from the baseline is the signal that lets a manager act. A team with no estimate has no signal — they only know they are late after the deadline has already passed.

### Why it is so hard — Brooks's verdict

> "More software projects have gone awry for lack of calendar time than for all other causes combined." — Fred Brooks, *The Mythical Man-Month* (1975)

Brooks identified five reasons software estimation is structurally harder than estimating, say, a bridge:

1. **Software is invented, not assembled.** Every project does something at least slightly novel; calibration data is thinner than in civil engineering.
2. **Effort is non-linear in size.** Doubling the size more than doubles the effort, because communication paths grow as $n(n-1)/2$.
3. **Productivity varies 10×–25× between engineers** — yet estimators routinely assume an "average" rate.
4. **Requirements churn during the build.** The thing you are estimating is changing while you estimate it.
5. **Optimism is endemic.** The person making the estimate is usually the person who *wants the project to happen*.

Industry studies (Standish Group, McConnell, Jørgensen) consistently report estimation errors of **2× to 4×** as typical, and **10×** as not unheard of. The honest position: an estimate is a **probability distribution**, not a single number — and our job today is to produce a distribution we can defend.

---

## § II. Lines of Code — Uses and Abuses

The oldest size metric in software is **Lines of Code (LOC)**, usually counted as non-comment, non-blank source lines, sometimes called *SLOC* or *KSLOC* (thousands of source lines). It is also the metric that has caused the most harm.

### Strengths and weaknesses

| **Strengths** | **Weaknesses** |
|---------------|----------------|
| Trivial to count *after the fact* with a `wc -l` and a comment filter. | **Rewards verbosity.** A 20-line solution scores worse than a 200-line equivalent. |
| **Decades of calibration data.** COCOMO, COCOMO II, SLIM, and most pre-2000 cost models speak LOC fluently. | **Language-dependent.** 100 lines of Rust does very different work from 100 lines of Python or 100 lines of COBOL. |
| Useful as the **input to COCOMO II** (Lecture 9). | Says nothing about **feature value, complexity, or user-visible behaviour**. |
| Easy to communicate to non-technical stakeholders. | **You cannot count LOC of code that has not been written.** As an *upstream* estimate, it is guessing. |

### The AI-era complication — the LOC ↔ effort link is broken

For forty years, COCOMO-style models assumed a roughly stable conversion: **so many engineer-months produce so many KSLOC**, and so many KSLOC require so many engineer-months. That relationship was always noisy, but it was usable.

In 2026, the relationship has been **broken** by AI-assisted code generation. A pair of typical observations on real teams:

- A developer using a competent coding assistant can produce **3× to 10× more LOC per hour** on routine code — boilerplate, CRUD endpoints, test scaffolding.
- The same developer produces roughly the **same amount** of LOC per hour on novel, architectural, or debugging-heavy work.
- A *concise human author* may now ship **fewer LOC than ever** to deliver a given feature, because the assistant suggests refactors that reduce duplication.

The result: KSLOC has become an even worse proxy for effort than it was. A team that ships 50 KSLOC in 2026 might have done two months of work, or eight months of work, or six weeks of work with extensive AI generation. The number alone tells you nothing.

!!! quote "Working rule for this course"
    Use LOC as a **downstream output** that feeds COCOMO II — never as an **upstream estimate** of unwritten code. Estimating in LOC before any code exists is guessing dressed up as engineering.

This is the deep reason Function Points exist: they measure **what the system does**, not how many lines it takes to do it.

---

## § III. IFPUG Function Points — Size From the Specification

In 1979, Allan Albrecht at IBM proposed a size metric that is **language-neutral** and **computable from a specification before any code is written**. The International Function Point Users Group (IFPUG) standardised it, and the IFPUG counting practices manual is still maintained today.

### What you count: five function types

The IFPUG count enumerates five categories of system function, then weights each by complexity (low / average / high).

| Type | What it is | Examples | Weight (L/A/H) |
|------|------------|----------|---------------:|
| **EI — External Input** | Data crossing the system boundary *into* the application, maintaining an internal file. | "Add book", "submit order", "update profile" | **3 / 4 / 6** |
| **EO — External Output** | Data leaving the system that involves **derivation** (calculations, summaries, formatting). | Monthly sales report, computed analytics dashboard | **4 / 5 / 7** |
| **EQ — External Inquiry** | A **direct read** that returns data without derivation. | "Search catalogue", "look up customer by ID" | **3 / 4 / 6** |
| **ILF — Internal Logical File** | A logical group of data the application **maintains** itself. | Customer master, order table, audit log | **7 / 10 / 15** |
| **EIF — External Interface File** | A logical group of data the application **reads but does not maintain** — owned by another system. | Currency-rate feed, partner catalogue, third-party identity provider | **5 / 7 / 10** |

Complexity (L/A/H) is determined by **data element types (DETs)** and **record/file types (RETs/FTRs)** referenced — the IFPUG manual has lookup tables, but for course purposes the *average* weight is the safe default unless a function is clearly trivial or clearly elaborate.

The **Unadjusted Function Point (UFP)** total is simply:

$$
\text{UFP} = \sum_{\text{function}} \text{weight}(\text{type}, \text{complexity})
$$

### Worked count — a small library-management module

We illustrate with an eight-function library module — small enough to read in one screen, complete enough to exercise every function type.

| # | Function | Type | Complexity | Weight | UFP |
|--:|----------|:----:|:----------:|------:|----:|
| 1 | Add book | EI | average | 4 | **4** |
| 2 | Search catalogue | EQ | average | 4 | **4** |
| 3 | Borrow book | EI | average | 4 | **4** |
| 4 | Generate monthly report | EO | high | 7 | **7** |
| 5 | Catalogue (book master) | ILF | average | 10 | **10** |
| 6 | Member roster | ILF | low | 7 | **7** |
| 7 | Borrowing log | ILF | average | 10 | **10** |
| 8 | External vendor catalogue feed | EIF | average | 7 | **7** |
| | **Total UFP** | | | | **53** |

**How to read the count.**

- *Add book* and *Borrow book* are **EI** — data flows *in* across the boundary and maintains an internal file (the catalogue and the borrowing log). Average DET/FTR counts → weight 4.
- *Search catalogue* is **EQ** — direct read, no derivation, no calculation. Weight 4.
- *Monthly report* is **EO**, not EQ, because it **derives** new data: totals, averages, top-borrower lists. Elaborate → weight 7 (high).
- *Catalogue*, *Member roster*, *Borrowing log* are **ILF** — the system owns and updates them. The catalogue has many DETs (title, author, ISBN, year, publisher, shelf …) so it earns the average weight 10; the member roster is leaner → low, weight 7.
- *Vendor catalogue feed* is **EIF**, not ILF, because the library only *reads* it; the vendor owns it. Weight 7 (average).

The total is **53 UFP** — a small module. For perspective, a typical departmental web app runs 200–500 UFP; a serious enterprise system runs 2,000–10,000 UFP.

### Why this works as an estimate

The genius of Function Points is that **every quantity in the count comes from the specification** — no code required. If you have a use-case list, a screen mockup, a data dictionary, and an interface inventory, you have everything you need. That is why FP estimates can be made at the *proposal* stage, when LOC estimates are pure fantasy.

---

## § IV. Value Adjustment Factor — Tuning the Count for Context

The UFP captures *what the system does*. It does **not** capture **how the system has to do it** — whether it must be real-time, distributed, highly available, easy to install, or change-friendly. The **Value Adjustment Factor (VAF)** is IFPUG's correction for these context factors.

### The formula

$$
\text{VAF} = 0.65 + 0.01 \times \sum_{i=1}^{14} \text{GSC}_i \qquad \text{AFP} = \text{UFP} \times \text{VAF}
$$

Each of the 14 General System Characteristics is rated 0 (no influence) through 5 (essential). The sum ranges 0–70, so VAF ranges from **0.65** (no characteristic matters) to **1.35** (every characteristic is essential) — a ±35% adjustment.

### The 14 General System Characteristics

| # | GSC | What it asks |
|--:|------|-------------|
| 1 | Data communications | How much data crosses networks? |
| 2 | Distributed data processing | Is processing split across nodes? |
| 3 | Performance | Are response-time targets stringent? |
| 4 | Heavily used configuration | Does the system run hot — saturated infra? |
| 5 | Transaction rate | High volumes per second/day? |
| 6 | Online data entry | Interactive forms vs batch? |
| 7 | End-user efficiency | Is UX optimisation a priority? |
| 8 | Online update | Real-time maintenance of ILFs? |
| 9 | Complex processing | Heavy logic, math, state machines? |
| 10 | Reusability | Is code intended for reuse elsewhere? |
| 11 | Installation ease | Must installation be turnkey? |
| 12 | Operational ease | Easy to operate, backup, recover? |
| 13 | Multiple sites | Many deployments, many environments? |
| 14 | Facilitate change | Is the system designed for evolution? |

### Worked AFP for the library module

Suppose for our library module the GSC ratings sum to **35** (a fairly average system: moderate performance, some online efficiency, some reusability, mild change pressure). Then:

$$
\text{VAF} = 0.65 + 0.01 \times 35 = 1.00 \qquad \text{AFP} = 53 \times 1.00 = \mathbf{53\ \text{AFP}}
$$

By construction, **VAF = 1.00 means a "typical" system**. A safety-critical, high-throughput, multi-site rewrite might score VAF = 1.30, pushing 53 UFP to 69 AFP. A simple internal tool might score VAF = 0.80, pulling 53 UFP down to 42 AFP.

!!! note "Modern IFPUG counts the GSCs as optional"
    Newer IFPUG releases (4.3+) treat VAF as informational rather than mandatory — many counters report UFP and leave context adjustment to the downstream effort model. For this course, keep VAF in the workflow: it forces you to *name* the context factors, which is itself a useful discipline.

---

## § V. Use-Case Points — A Brief Comparison

In 1993, Gustav Karner at Objectory AB (later part of Rational) proposed **Use-Case Points (UCP)** as an FP analogue for **OO projects whose specification lives in use cases and class diagrams** rather than data-flow diagrams. UCP has the same shape as FP — count something, weight it, then adjust.

### How UCP differs from FP

| | **Function Points (IFPUG)** | **Use-Case Points (Karner)** |
|--|------------------------------|------------------------------|
| What you count | Functions: EI, EO, EQ, ILF, EIF | **Actors** (simple/average/complex by interaction style) + **use cases** (by number of transactions/steps) |
| Specification artefact assumed | Data-oriented spec, screen + file inventory | Use-case diagram + textual scenarios |
| Adjustment | VAF from 14 GSCs | **TCF** (Technical Complexity Factor, 13 items) × **ECF** (Environmental Complexity Factor, 8 items) |
| Final number | AFP | UUCP → UCP |
| Best fit | Information systems, CRUD-heavy, line-of-business apps | OO-modelled projects, customer-facing workflows, agile teams |

Empirically, **FP and UCP correlate well within a single domain** but are not directly interchangeable across domains. Different teams will get systematically different numbers using each method — that is fine *as long as your alternatives are counted consistently*.

!!! tip "For the group project"
    Choose **FP or UCP, not both**. Whichever you pick, use the *same* method on every alternative your team analyses. Consistency across alternatives matters far more than which method you chose.

---

## § VI. Estimation Biases — Why Method Alone Is Not Enough

A correct counting method, applied through an uncorrected optimism filter, is still a wrong number. Three biases reliably eat estimation accuracy.

### 1. Optimism bias

The estimator imagines the **happy path** — every API works, the team is fully staffed, requirements are stable, no one quits, the cloud bill is what the calculator says. Reality is the sum of small disasters: an integration that takes two weeks instead of two days, a security review that adds a sprint, a key engineer on parental leave. The output is consistently too low.

> **Counter:** **Reference-class forecasting** (Kahneman, Flyvbjerg). Find five comparable past projects from your organisation or the literature. Use *their* actual durations as the base rate; adjust only modestly from there.

### 2. Anchoring bias

The first number spoken in the room — usually by the most senior person, often before any analysis — **becomes the anchor**. Subsequent analysis only *nudges* the estimate by 10–20%. If the CEO said "we ship by July", every estimate that follows is mysteriously close to "we ship by July."

> **Counter:** **Estimate before discussing.** Each team member writes a number on an index card *before* hearing anyone else's. Reveal simultaneously. Discuss the spread, not the leader's number.

### 3. Planning fallacy

A specific case of optimism: the belief that "**this time will be different**" — *this* project won't suffer the integration delays, the scope creep, the requirements churn that every previous project suffered. It almost always does.

> **Counter:** **Add a buffer of 25%–50% to the bottom-up estimate** and *refuse to compress it without a structural reason*. The buffer is not a fudge; it is the empirical adjustment for the planning fallacy. A team that delivers a "25%-buffered" estimate on time will be trusted next quarter; a team that hits the unbuffered estimate is just lucky.

### The combined effect

In practice, these biases stack. A team that picks the happy-path number, anchored to a leadership target, with no buffer for "this time will be different", produces an estimate that is **2× to 4× too low** — the canonical industry error range. The method (FP, UCP, COCOMO) explains *some* of the gap, but never all of it. **Bias correction is half the work of estimation.**

---

## Group Project Brief — Released Today

Today, **Wednesday 2026-06-10**, your group-project assignment goes live. The project carries **30% of your course grade** and is the place you will put every concept from Lectures 1–13 to work on a problem of your choice.

### Team formation

- **Teams of 3 to 4 students.** Solo work and pairs are not accepted; teams of 5 require instructor approval.
- Teams self-organise. A signup sheet is on the course site and in this room today.
- **Teams must be confirmed by Lecture 11 (Saturday 2026-06-13).** Late team registration delays you from feedback in the Lec 11 office hours.

### Step 1 — Choose a software product

The product can be **real or hypothetical**:

- An app, service, or platform you would build (e.g. a campus food-delivery app, an AI tutor, an open-source dev tool).
- An existing product you would *re-engineer* (e.g. modernise a 1990s ERP, port a desktop app to web).
- A specific internal tool for a real organisation (with the org's consent).

The product must be **complex enough to require ≥ 200 Function Points** and **simple enough to specify in 2 pages**. Avoid "build the next Facebook" — pick something narrower and analysable.

### Step 2 — Required deliverables (all submitted as one PDF report)

| # | Deliverable | Lecture connection |
|--:|-------------|-------------------|
| 1 | **Project description and scope** (1–2 pages) — what it does, who uses it, what's *out* of scope. | Lec 1, Lec 8 |
| 2 | **Size estimate using FP or COCOMO II** (your choice). Show every function, weight, GSC. | Lec 8, Lec 9 |
| 3 | **Cash-flow forecast for ≥ 2 alternatives.** E.g. build-vs-buy, self-host-vs-SaaS, with-AI vs without-AI. | Lec 4 |
| 4 | **NPV, IRR, payback, and PI for each alternative.** State your MARR and justify it. | Lec 5, Lec 6 |
| 5 | **Sensitivity or risk analysis.** Tornado diagram, decision tree, or Monte Carlo on the two largest cost drivers. | Lec 7 |
| 6 | **AI-cost bonus** *(optional, +10%)* — model token spend, productivity uplift, and human-verification cost in your alternatives. | Lec 12, Lec 13 |
| 7 | **Final recommendation** with stakeholder justification (1 page). Who decides, on what basis, with what residual risk. | All |

### Step 3 — Presentation

- **Dates:** Lectures 14 (Tuesday 2026-06-16) and 15 (Wednesday 2026-06-17). Half the teams present each day; slot allocation by random draw at Lec 11.
- **Format per team:** **12 minutes of presentation + 5 minutes of Q&A + 3 minutes of written peer evaluation by the class.** Hard stop at 20 minutes total.
- **Slides:** any tool, but submit as PDF the morning of your slot.

### Rubric

The project is graded on four criteria, each weighted **25%**:

| Criterion | What earns full marks |
|-----------|----------------------|
| **Clarity of scope and stakeholders** | Every reader can answer "what is this, for whom, and against what alternative?" within 60 seconds of reading. |
| **Methodology rigour** | FP/COCOMO count is reproducible. NPV/IRR computation is correct. MARR is stated and justified. Unit consistency is maintained. |
| **Sensitivity and risk** | At least two cost drivers tested. The team can articulate the *break-even* values where the decision flips. |
| **Recommendation quality** | The recommendation is *defensible to a real stakeholder* — not a hedge, not "it depends." Residual risks are named and accepted. |

### Schedule at a glance

| Date | Milestone |
|------|-----------|
| **Wed 2026-06-10** (today, Lec 8) | Brief released, teams begin forming. |
| **Sat 2026-06-13** (Lec 11) | **Teams confirmed.** Office hours for product-scope check. |
| **Mon 2026-06-15** (Lec 13) | Last in-class checkpoint; draft alternatives reviewed. |
| **Tue 2026-06-16** (Lec 14) | First half of presentations. |
| **Wed 2026-06-17** (Lec 15) | Second half of presentations. |
| **Thu 2026-06-18** (Lec 16 — final) | **Final report PDF due 09:00**, before the final exam. |

The brief PDF, rubric, and signup form are at `projects/group/brief.pdf` on the course site.

---

## Class Discussion — How Many Function Points Is WeChat?

Open floor, **10 minutes**, in pairs.

WeChat is a familiar product to most of you and ridiculously over-scoped for a classroom exercise. Pick a target between you of **15 representative functions** (you will miss thousands — that is the point). Tag each with one of the five IFPUG types and an honest complexity guess. Sum the UFP.

**Suggested starting points** (don't restrict yourselves to these):

| Function | Possible classification |
|----------|------------------------|
| Send text message | EI |
| View chat history | EQ |
| Make voice call | EI / EO |
| Pay via WeChat Pay | EI (high) |
| Friend list | ILF |
| Moments feed | EO |
| Mini-program launcher | EI + EQ |
| Currency rate (for cross-border pay) | EIF |

After 4 minutes, compare with the team next to you. **Are your totals within 30%?** If yes, your team's instincts are calibrated; if no, *which functions did you classify differently?* The exercise is not about the right answer — it is about discovering where your estimating intuition diverges from a peer's, so that you can negotiate a shared baseline before any money is at stake.

---

## Homework 8 — Count Your Group Project

**Due: Friday 2026-06-12, before Lecture 10. Late: −10% per day.**

1. **Choose your group-project product** (or a candidate, if your team is not finalised). Write a **one-page scope** — what it does, who uses it, what is *out* of scope.
2. **Enumerate the functions.** Identify every EI, EO, EQ, ILF, EIF you can. Aim for at least **20 functions**.
3. **Assign weights** (L/A/H) for each function. Justify each non-average choice in one sentence.
4. **Compute UFP** as a table; sum to a total.
5. **Apply VAF.** Score the 14 GSCs for your system. Show the sum, the VAF, and the resulting **AFP**.
6. **Reflection (half page):** which three functions in your count are you *least confident* about, and what evidence would resolve them?

**Bring your AFP number to Lecture 9.** We will convert AFP → KSLOC using language-conversion ratios, then run it through COCOMO II for an effort and schedule estimate.

**Submission.** A single PDF (spreadsheet export + the paragraph) committed to `submissions/HW8/<your-name>.pdf`.

**Office hours.** Tomorrow, 10:00–11:30, before Lecture 9.

---

## Key Vocabulary — Quick Reference

| Term | Plain-language meaning |
|------|------------------------|
| LOC / SLOC / KSLOC | Lines of source code, in ones or thousands; a *lagging* size measure. |
| Function Points (FP) | A language-neutral size measure computable from a specification. |
| UFP | Unadjusted Function Points — sum of weighted function counts. |
| AFP | Adjusted Function Points — UFP × VAF. |
| EI / EO / EQ | External Input / Output / Inquiry — three transaction function types. |
| ILF / EIF | Internal Logical File / External Interface File — two data function types. |
| VAF | Value Adjustment Factor; ranges 0.65–1.35; depends on 14 GSCs. |
| GSC | One of fourteen General System Characteristics, each rated 0–5. |
| UCP | Use-Case Points (Karner 1993); FP analogue for OO/use-case projects. |
| Optimism bias | The estimator imagines the happy path; the cure is reference-class forecasting. |
| Anchoring bias | The first number spoken sticks; the cure is silent simultaneous estimation. |
| Planning fallacy | "This time will be different"; the cure is a 25%–50% buffer. |

---

## What Today Bought You

1. **LOC is a lagging measure**, not a forward-looking one — and the AI-coding era has **broken the link** between LOC and effort that 1980s estimation depended on.
2. **Function Points let you size a system from its specification**, before any code is written, in a language-neutral way. The five types — EI, EO, EQ, ILF, EIF — and the L/A/H weights are the entire vocabulary.
3. **VAF turns "what it does" into "what it does in this context."** The 14 GSCs force you to name the non-functional pressures that an unadjusted count ignores.
4. **Bias is the dominant error.** A correct method applied through an optimism filter is still a wrong number; reference-class forecasting and a buffer are part of the method.
5. **You now have the input to COCOMO II.** Tomorrow's lecture turns your AFP into an effort estimate (person-months), a schedule, and a staffing curve.

---

## Further Reading

- **Albrecht, A. J.** (1979). "Measuring application development productivity." IBM Conference on Application Development — the original Function Points paper.
- **IFPUG** (2010). *Function Point Counting Practices Manual, Release 4.3.1* — the authoritative reference; available through IFPUG membership and major university libraries.
- **Karner, G.** (1993). "Resource estimation for objectory projects." Objectory Systems internal report — the original Use-Case Points proposal.
- **Brooks, F. P.** (1975, 1995 anniversary ed.). *The Mythical Man-Month*. Addison-Wesley — Chapter 2 on the optimism of programmers and Brooks's Law on adding people to a late project.
- **Jørgensen, M., & Shepperd, M.** (2007). "A systematic review of software development cost estimation studies." *IEEE Trans. Software Engineering* — meta-analysis of estimation accuracy across 304 studies.
- **Boehm, B. W.** (1981). *Software Engineering Economics*. Prentice Hall — **Chapter 22** on size estimation.
- **McConnell, S.** (2006). *Software Estimation: Demystifying the Black Art*. Microsoft Press — practitioner-friendly, with the most usable treatment of bias correction.
- **Kahneman, D.** (2011). *Thinking, Fast and Slow* — Chapter 23 on the planning fallacy and reference-class forecasting.
- *(Forward pointer)* **Lecture 9 — COCOMO II** uses your AFP (converted to KSLOC) to compute effort, schedule, and team size.

---

*Prepared by Dr. Zhijiang Chen — Frostburg State University, Summer 2026.*
