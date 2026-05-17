---
title: "Lecture 11 — Build / Buy / Reuse & Value-Based Software Engineering"
author: Dr. Zhijiang Chen
date: 2026-06-13
session: 11
week: 15
room: SY109
---

# Lecture 11 — Build / Buy / Reuse & Value-Based Software Engineering

!!! info "Session Info"
    **Date:** Saturday, 2026-06-13 &nbsp;·&nbsp; **Time:** 16:20–18:10 &nbsp;·&nbsp; **Room:** SY109
    **Instructor:** Dr. Zhijiang Chen &nbsp;·&nbsp; **Session 11 of 16** &nbsp;·&nbsp; **Project teams must be confirmed today.**

[**▶ Open the lecture slides — 110-minute web deck**](slides/lecture-11.html){ .md-button .md-button--primary target="_blank" }

!!! tip "Using the slide deck"
    The slides are a self-contained web presentation (Reveal.js, no install). Keyboard shortcuts: **F** for full-screen, **S** for speaker view, **Esc** for slide overview, **arrow keys** to navigate. Designed for a single 110-minute session.

---

## Learning Objectives

By the end of this lecture, you should be able to:

1. Distinguish the **five alternatives** of a modern make-or-buy decision — **Build, Buy, Reuse, Open-source, SaaS** — and name the primary trade-off of each.
2. Compute **Equivalent Source Lines of Code (ESLOC)** with the **COCOMO II reuse model**, and explain why "free" reuse is a myth.
3. Construct and defend a **weighted decision matrix** comparing alternatives on TCO, time-to-value, control, compliance, lock-in risk, and talent availability.
4. State the **five principles of Value-Based Software Engineering (VBSE)** as articulated by Boehm & Sullivan (2000), and apply them to a real project decision.
5. Run a **stakeholder value elicitation** that names win conditions and visible conflicts — and recognise when a "decided" question will be relitigated because someone cannot win.
6. Use the language of **real options** to defend an alternative whose static NPV is *not* the highest — because it preserves the right to change your mind later.

---

## Lecture Map — Six Movements, One Decision

By the end of today you should be able to walk into a make-or-buy meeting and **change the question** from "which is cheapest?" to "which delivers the most stakeholder value at acceptable risk, with the most optionality left over?" That reframing is what VBSE is *for*.

| § | Topic | Minutes |
|---|-------|--------:|
| I | Five alternatives — Build · Buy · Reuse · OSS · SaaS | 15 |
| II | Reuse economics — the COCOMO II reuse model | 15 |
| III | The weighted decision matrix — a worked 6-criterion example | 15 |
| IV | Value-Based Software Engineering — Boehm & Sullivan's five principles | 20 |
| V | Stakeholder value & WinWin — conflicts made legible | 15 |
| VI | Real options — optionality as economic value | 15 |
| — | Discussion, homework, Q&A | 15 |

!!! note "Where this lecture sits in the arc"
    Lectures 2–4 gave you the *vocabulary of cost*. Lectures 5–7 gave you the *machinery of investment decisions* (NPV · IRR · payback · risk). Today we put both to work on the **single most common architecture-level question in industry**: should we build this thing, or get it from somewhere else?

---

## § I. Five Alternatives — Not Just "Build or Buy"

The classical software-engineering canon framed make-or-buy as a binary. The modern decision space is **five-way**, and the most common real answer is a *blend* — buy the core, build the customisation, integrate open-source pieces, and run it all on SaaS infrastructure. Pure choices are rare and usually unwise.

### The five alternatives, with their primary trade-off

| Option | What it means | Primary trade-off |
|--------|--------------|-------------------|
| **Build (in-house)** | Develop and own the code yourself. | Maximum control, maximum non-recurring cost, slowest time-to-value. |
| **Buy (commercial)** | License a packaged product from a vendor. | Predictable cost, faster delivery, vendor dependence. |
| **Reuse** | Adapt an existing internal component or library. | Low marginal cost, integration friction, hidden maintenance debt. |
| **Open-source (OSS)** | Adopt a community-maintained codebase. | Zero licence fee, real long-term maintenance burden, governance risk. |
| **SaaS** | Subscribe to a hosted service over an API. | Lowest time-to-value, smallest team, lowest control and highest lock-in. |

### Reading the trade-off — three things every option costs

Every option above costs you something **on three axes simultaneously**. A decision that optimises one axis at the expense of the other two is rarely defensible.

| Axis | Build | Buy | Reuse | OSS | SaaS |
|------|:-----:|:---:|:-----:|:---:|:----:|
| **Money** (TCO, see Lec 2) | High build, low ongoing | Medium · predictable | Low | Low licence · medium ops | Low build, high ongoing |
| **Time** (to first value) | Slowest | Medium | Fast | Medium | Fastest |
| **Control** (over roadmap, data, failure modes) | Highest | Low | High | Medium | Lowest |

The decision is rarely about *which option is best*; it is about *which axis matters most for this component, in this market, at this stage of the company*. Talking past one another on the make-or-buy question is almost always a sign that the parties have different rankings on this axis table — and haven't said so out loud.

### Three pitfalls in the framing itself

- **NIH bias ("Not Invented Here").** Engineers over-rate Build because they have personally suffered the integration pain of a bad Buy. The cure is to count the *future* engineer-years Build will consume — not to relive the past.
- **NMH bias ("Not Made Here").** The mirror image: managers over-rate Buy because a vendor name on the invoice feels like *risk transfer*. It rarely is — the vendor can fail, sunset the product, or 10× the price, and you wear the consequence.
- **Treating SaaS as "no decision."** A team that signs a 12-month SaaS contract has made a *capital-equivalent* commitment in opex clothing. The decision-relevance rule (Lec 2) still applies; the contract is just denominated in months instead of dollars.

---

## § II. Reuse Economics — The COCOMO II Reuse Model

Reuse looks free. It is not. Boehm's **COCOMO II reuse model** quantifies *how much* of the from-scratch cost you actually save when adapting an existing component — by converting "adapted lines" into **Equivalent Source Lines of Code (ESLOC)** that you then feed into your normal effort estimator (Lec 9).

### The ESLOC formula

$$
\mathrm{ESLOC} \;=\; \mathrm{ASLOC} \times \frac{\mathrm{AA} + \mathrm{SU} + 0.4 \cdot \mathrm{DM} + 0.3 \cdot \mathrm{CM} + 0.3 \cdot \mathrm{IM}}{100}
$$

| Symbol | Name | Range | What it measures |
|--------|------|:-----:|------------------|
| $\mathrm{ASLOC}$ | Adapted source lines | — | The raw count of existing lines you intend to reuse. |
| $\mathrm{AA}$ | Assessment & Assimilation | 0–8 | Effort to evaluate the component, decide to adopt, and bring it into the build. |
| $\mathrm{SU}$ | Software Understanding | 10–50 | Effort to read and comprehend unfamiliar code (50 = obscure, 10 = pristine). |
| $\mathrm{DM}$ | Design Modification | 0–100 (%) | Percentage of the original design you must change. |
| $\mathrm{CM}$ | Code Modification | 0–100 (%) | Percentage of the lines you must edit. |
| $\mathrm{IM}$ | Integration Modification | 0–100 (%) | Percentage of integration work (tests, glue) you must redo. |

The weights — $0.4, 0.3, 0.3$ — encode Boehm's empirical finding that design changes cost *more per percent* than code or integration changes, because design ripples.

### Worked example — adopting a 50,000-line vector-search library

A team wants to reuse a 50K-line vector-search library inside a new RAG service. They estimate:

- $\mathrm{ASLOC} = 50{,}000$
- $\mathrm{AA} = 4$ (moderate evaluation; the library is documented but unfamiliar)
- $\mathrm{SU} = 30$ (mid-range; readable but non-trivial code)
- $\mathrm{DM} = 20\%$ (most of the design survives; some interfaces must change)
- $\mathrm{CM} = 25\%$ (a quarter of the lines will be edited)
- $\mathrm{IM} = 40\%$ (significant glue and test work)

$$
\mathrm{ESLOC} = 50{,}000 \times \frac{4 + 30 + 0.4(20) + 0.3(25) + 0.3(40)}{100}
$$

$$
= 50{,}000 \times \frac{4 + 30 + 8 + 7.5 + 12}{100} = 50{,}000 \times \frac{61.5}{100} \approx 30{,}750 \text{ ESLOC}
$$

So this reuse costs the team **as if they had written ~31K new lines** — about **62% of a from-scratch effort**. The library "saved" 38% of the work, not 100%. That is the *typical* result: in industry data, reuse runs **30–60% of from-scratch cost**, depending mainly on how much design surgery is required.

!!! warning "The reuse trap"
    Teams that estimate reuse at *5% of from-scratch* (the "it's just a library call" estimate) and then ship at *60%* will blow their schedule by a factor of 12. Most "library integration" disasters in industry are exactly this estimation error. **Compute ESLOC before you commit.**

### Three reuse anti-patterns

- **The 90% library.** A library does 90% of what you need; the missing 10% lives across every layer. CM and IM both spike. ESLOC approaches ASLOC — reuse saves you nothing.
- **The vampire dependency.** A reused component pulls in transitive dependencies that you must now own forever. The *true* ASLOC is far larger than what you "reused."
- **The orphaned fork.** You modify the library, then can't take upstream updates. SU rises every year because you maintain the diff alone. Plan the abstraction layer **before** you fork.

---

## § III. The Weighted Decision Matrix — Making Trade-offs Legible

A **weighted multi-criteria decision matrix** is the simplest, most defensible tool for comparing five alternatives across six criteria. Its job is not to *produce* the answer — it is to *surface the weights*, because disagreement about weight is usually disagreement about strategy.

### Construction in four steps

1. **List the criteria** that matter for *this* decision. Keep it to 5–8; more dilutes the signal.
2. **Assign weights** (we use a 100-point budget below) that sum to 100. Every team member writes their weights first, *then* the group discusses.
3. **Score each option** on each criterion, 0–10. Higher is better. For cost-like criteria ("5-yr TCO"), invert so that *low TCO = high score*.
4. **Compute the weighted total**: $\text{score}(o) = \sum_c w_c \cdot s_{o,c} / 100$.

### Worked example — choosing an AI inference layer for a new product

Six criteria, four serious alternatives (Reuse is set aside here because no internal component exists).

| Criterion (weight / 100) | $w$ | **Build** | **Buy** | **OSS** | **SaaS** |
|--------------------------|----:|:---------:|:-------:|:-------:|:--------:|
| 5-yr TCO *(low cost → high score)* | 25 | 4 | 7 | 9 | 6 |
| Time-to-value | 20 | 3 | 7 | 8 | 10 |
| Control / flexibility | 15 | 10 | 5 | 8 | 4 |
| Compliance fit | 15 | 10 | 7 | 6 | 5 |
| Vendor / lock-in risk *(low risk → high score)* | 10 | 10 | 5 | 8 | 4 |
| Talent availability | 15 | 5 | 8 | 7 | 9 |
| **Weighted total** | **100** | **5.85** | **6.55** | **7.85** | **6.40** |

OSS wins on this weighting. Worked computation for Build, as a sanity check:

$$
\text{Build} = \frac{25 \cdot 4 + 20 \cdot 3 + 15 \cdot 10 + 15 \cdot 10 + 10 \cdot 10 + 15 \cdot 5}{100} = \frac{100 + 60 + 150 + 150 + 100 + 75}{100} = 5.85
$$

### What the matrix is really telling you

| Insight | What to do with it |
|---------|---------------------|
| OSS wins because **TCO** and **time-to-value** carry 45% of the weight. | If your strategy actually values **control** more than cost, *change the weights, not the scores.* |
| Build wins on every "owned" axis (control, compliance, lock-in) but loses on cost/speed. | Build is the right answer for **regulated cores**; the wrong answer for *every* component. |
| SaaS leads on time-to-value and talent but loses on control/lock-in. | SaaS is right for **commodity components** and dangerous for **strategic** ones. |

!!! quote "Working principle"
    The decision matrix is most useful when it *disagrees* with the room's gut. If everyone already wanted OSS, the matrix is theatre. If half the room wanted Build and the matrix points to OSS, you have a productive argument about weights — i.e. about strategy.

### Three traps in matrix-driven decisions

- **Score inflation.** When the matrix doesn't pick your favourite, the temptation is to raise its scores. Lock the scoring rubric *before* you tally.
- **Weight gaming.** A 1-point weight swing on a heavy criterion can flip the answer. If small weight changes flip the outcome, the decision is *inherently close* — and the matrix should not pretend otherwise.
- **Missing criterion.** A six-criterion matrix that omits "team morale" or "regulatory horizon" may be precise on the wrong question. Validate completeness before tallying.

---

## § IV. Value-Based Software Engineering — Boehm & Sullivan's Five Principles

In **2000**, Barry Boehm and Kevin Sullivan published a short, influential paper called *"Software economics: A roadmap"* that argued software engineering had spent thirty years optimising the wrong objective. Most methods — CMM, function-point counting, even early COCOMO — measured **effort, defects, conformance**. None of them measured **value to whom**. They named the corrective programme **Value-Based Software Engineering (VBSE)** and gave it five principles.

### The five principles

| # | Principle | Plain-language meaning | What it asks you to add to your process |
|---|-----------|------------------------|-----------------------------------------|
| 1 | **Benefits realisation analysis** | Connect every piece of technical work to a measurable business benefit. | A "benefits map" linking features → outcomes → dollars. |
| 2 | **Stakeholder value-proposition elicitation** | Name every party with a stake, and document each one's *win condition*. | The WinWin table of § V. |
| 3 | **Business case analysis** | Argue the project's value with NPV / IRR / payback / risk (Lec 5–7). | A live business case that updates as evidence arrives. |
| 4 | **Continuous risk & opportunity management** | Track real-options value across the project, not only at proposal. | A risk + opportunity register reviewed at every milestone. |
| 5 | **Concurrent system & process engineering** | Design the *build process* alongside the product. | Process-impact reviews in every architecture decision. |

### Why VBSE matters — the shift from conformance to value

Pre-VBSE, a "successful" project hit scope, on-time, on-budget. Post-VBSE, a successful project **delivered value to its stakeholders at acceptable risk**. The two are *not the same*: a project can land on-spec and on-budget and still be a value disaster (it shipped what nobody wanted) or a value triumph despite missing its dates (it pivoted into a market nobody had named at kickoff).

| Pre-VBSE question | VBSE question |
|-------------------|---------------|
| Did we ship on time? | Did the right stakeholders win, on a timeline they could absorb? |
| Did we hit the spec? | Was the spec still right by the time we shipped? |
| What is our defect rate? | What is the cost of our remaining defects, weighted by who suffers them? |
| Did we make budget? | Did the NPV of what we shipped exceed the NPV of what we spent? |

VBSE does not replace the older questions. It **embeds** them inside the value question — which is the only one that maps to a real business outcome.

### How VBSE changes a Build-vs-Buy decision

A non-VBSE Build-vs-Buy comparison adds up dollars. A **VBSE** Build-vs-Buy comparison asks four extra questions:

1. **Whose value** does each alternative create — engineering, sales, finance, customer, regulator?
2. **In what time horizon** — month-one, year-three, ten-year?
3. **At what risk** — what is the probability-weighted cost of the failure modes unique to each alternative?
4. **With what option value preserved** (§ VI) — what can we still change cheaply after we choose?

A pure NPV comparison answers none of these. VBSE is the discipline of asking them out loud.

---

## § V. Stakeholder Value & WinWin

VBSE Principle 2 deserves a section of its own. **WinWin** — also Boehm's, refined in the 1990s requirements-engineering literature — is the negotiation protocol that turns "what do we build?" into "what does each stakeholder need to *win* here?"

### The WinWin table — stakeholders, wins, conflicts

| Stakeholder | Win condition | Often conflicts with… |
|-------------|---------------|------------------------|
| **End user** | A frictionless feature that gets out of their way. | **Security team** — every friction point is a control. |
| **Engineering** | Maintainable architecture; sustainable on-call. | **Sales / Marketing** — who want a launch date now. |
| **Sales** | A demo-able feature for the Q3 customer pitch. | **Engineering quality** — demo-able ≠ supportable. |
| **Compliance / Legal** | An auditable trail and clear data residency. | **End-user simplicity** — disclosures and logging add UI. |
| **Finance** | Forecastable, capped cost. | **Variable AI / token spend** — by nature unforecastable. |
| **Security** | Defense-in-depth, least privilege, no shadow IT. | **Time-to-value** — every control delays first ship. |
| **Customer Success** | Stable feature surface; no churn-inducing rewrites. | **Product** — wants to iterate aggressively. |

### The WinWin principle

> A decision is **WinWin** when every named stakeholder achieves at least the *minimum* they consider acceptable. If a stakeholder cannot win — even minimally — the decision is **unstable**: they will relitigate it, route around it, or quietly sandbag execution until the decision is reopened.

This is why "we decided X, why is it back on the agenda?" recurs so often in industry. Almost always, the answer is *someone could not win at X*, the decision was made anyway, and the loser brought it back through another door.

### Three patterns of conflict resolution

| Pattern | What it looks like | When to use it |
|---------|--------------------|----------------|
| **Concession** | Stakeholder A accepts a worse outcome in exchange for a *separate* future win. | When the conflict is small and trust exists. |
| **Re-framing** | The "conflict" dissolves when both parties see a third axis. (E.g., security ↔ UX dissolves into a *passwordless* approach that wins both.) | When the conflict looks zero-sum but isn't. |
| **Escalation** | A higher authority makes a binding call; both stakeholders accept. | When the conflict is genuine and stakes are high. |

The worst pattern — **silent loss** — is when no one notices a stakeholder can't win, until the project ships and they refuse to adopt it.

---

## § VI. Real Options — Optionality Has Economic Value

Classical NPV (Lec 5) prices a decision *as if it were final*. Most software decisions are not. You can usually **change your mind later** — for a cost. The **real-options** view formalises this: the *right* to change your mind has economic value, even if you never exercise it.

### The core insight

> $\text{Total value} = \text{NPV (static)} + \text{Option value (the right to adapt)}$

A project with a lower static NPV may have *higher total value* if it preserves cheap exits, cheap pivots, or cheap upgrades — i.e. if it keeps options open.

### Three real-option moves in Build/Buy/Reuse

| Move | What you do | What option you preserve | What you pay for it |
|------|-------------|--------------------------|---------------------|
| **Buy now with a clean exit plan** | Sign with a vendor *and* design your integration so you can swap them in under 90 days. | The option to **leave** when the vendor disappoints or raises prices. | Some extra upfront integration work (abstraction layer). |
| **Reuse with abstraction** | Wrap a reused library behind an interface you own. | The option to **swap** the implementation without rewriting callers. | A small ongoing maintenance cost on the interface. |
| **SaaS trial → deepen or walk** | Adopt a SaaS tool with a 6-month evaluation period and explicit success metrics. | The option to **walk away cheaply** if metrics aren't met. | A modest subscription, plus discipline to *actually* measure. |

### Worked example — Buy with exit vs. Build

Two alternatives for a new component:

| | Static NPV | Switching cost if wrong | Probability you'll need to switch in 3 years |
|---|----------:|------------------------:|---------------------------------------------:|
| **Build** | $+\$120{,}000$ | $\$200{,}000$ (rewrite) | 15% |
| **Buy + exit plan** | $+\$95{,}000$ | $\$40{,}000$ (swap behind abstraction) | 25% |

Risk-adjusted expected cost of *future change*:

- **Build:** $0.15 \times \$200{,}000 = \$30{,}000$
- **Buy + exit:** $0.25 \times \$40{,}000 = \$10{,}000$

**Risk-adjusted NPV:**

- **Build:** $\$120{,}000 - \$30{,}000 = \$90{,}000$
- **Buy + exit:** $\$95{,}000 - \$10{,}000 = \$85{,}000$

Build still wins — but the gap closed from $\$25{,}000$ to $\$5{,}000$. **A small further increase in switching probability flips the answer.** Lecture 7's sensitivity analysis is the right tool to find that threshold.

!!! quote "The optionality rule of thumb"
    When two alternatives are close on static NPV, **choose the one with the cheaper exit.** You are buying yourself the right to be wrong cheaply — and in software, you *will* be wrong about something.

---

## Class Discussion — Build or Buy the AI Inference Layer?

**Format.** Pairs · 4 minutes · then share-back.

> For your group project, would you **Build** the AI inference layer (self-hosted model + GPU), or **Buy** an API (OpenAI / Anthropic / Bedrock)?

Fill in the four-row decision matrix:

| Criterion | Weight (you set) | **Build (self-host)** | **Buy (API)** |
|-----------|:----------------:|:---------------------:|:-------------:|
| 5-yr TCO | ? | ? | ? |
| Time-to-value | ? | ? | ? |
| Control over model behaviour | ? | ? | ? |
| Vendor / lock-in risk | ? | ? | ? |
| **Weighted total** | **100** | | |

Then answer three follow-ups in your pair:

1. **Which weight matters most in *your* project?** Why? (One sentence.)
2. **Would your answer flip** if a vendor offered to *pin model behaviour for 3 years*?
3. **Would your answer flip** if your internal engineering team *doubled* tomorrow?

Three pairs will share back. We will look for which *axis* drives each team's answer — that axis is your project's *strategy*, named out loud, often for the first time.

---

## Homework 11 — VBSE on Your Project

**Due: Monday 2026-06-15, 23:59. Late: −10% per day.**

1. **Stakeholder map.** For your group project, list at least **five stakeholders**. For each, write their *win condition* in one sentence. Identify the **two largest conflicts** between win conditions, and propose a resolution pattern (concession, re-framing, escalation) for each.
2. **Five-option decision matrix.** Pick **one major component** of your system. Build a six-criterion matrix (your own criteria; justify each weight in one sentence) comparing **Build · Buy · Reuse · OSS · SaaS**. Compute the weighted totals and name the winner.
3. **Real-options reflection (half page).** Which alternative offers the **best optionality value**, *separate* from its NPV? In what scenario would optionality be worth more than the static-NPV difference?

**Submission.** A single PDF committed to the course repo at `submissions/HW11/<your-name>.pdf`.

**Office hours.** Tomorrow 10:00–11:30, before Lecture 12.

---

## Further Reading

- **Boehm, B. W., & Sullivan, K. J.** (2000). *Software economics: A roadmap.* In *The Future of Software Engineering* (ICSE 2000). — The founding statement of VBSE.
- **Boehm, B. W., et al.** (2005). *Value-Based Software Engineering.* Springer. — Book-length treatment of all five principles.
- **Boehm, B. W., et al.** (2000). *Software Cost Estimation with COCOMO II.* Prentice Hall — Chapter 2 on the reuse model (ESLOC, AA, SU, DM, CM, IM).
- **Erdogmus, H., Favaro, J., & Strigel, W.** (2004). "Return on investment." *IEEE Software* 21(3). — Practical case studies of value-based investment in software.
- **Sullivan, K. J., Chalasani, P., Jha, S., & Sazawal, V.** (1999). "Software design as an investment activity: A real options perspective." — The real-options foundation.
- **Boehm, B. W., & Ross, R.** (1989). "Theory-W software project management: Principles and examples." *IEEE TSE.* — The original WinWin paper.
- *(Forward pointer)* **Lecture 12 — AI Estimation & Productivity** asks what changes in the make-or-buy calculus when AI writes much of the code.

---

## Key Vocabulary — Quick Reference

| Term | Plain-language meaning |
|------|------------------------|
| Build / Buy / Reuse / OSS / SaaS | The five alternatives in a modern make-or-buy decision. |
| ESLOC | Equivalent Source Lines of Code — what reused lines "count as" against from-scratch. |
| AA · SU · DM · CM · IM | The five COCOMO II reuse parameters: assessment, understanding, design / code / integration modification. |
| Decision matrix | A weighted multi-criteria scoring tool that makes trade-offs legible. |
| VBSE | Value-Based Software Engineering — Boehm & Sullivan's five-principle framework. |
| Benefits realisation | Connecting technical work to a measurable business outcome (VBSE #1). |
| Stakeholder value proposition | Each stakeholder's "what does winning look like for me?" (VBSE #2). |
| WinWin | A decision in which every stakeholder achieves at least their minimum acceptable outcome. |
| NIH bias | "Not Invented Here" — over-rating Build because of past Buy pain. |
| NMH bias | "Not Made Here" — over-rating Buy because vendor names *feel* like risk transfer. |
| Real option | The economic value of the right (but not the obligation) to adapt a decision later. |
| Optionality | The shape of a decision that keeps multiple futures cheaply reachable. |

---

## What Today Bought You

1. A **five-way** make-or-buy frame — Build · Buy · Reuse · OSS · SaaS — and the discipline to recognise that most real decisions are *blends*.
2. The **COCOMO II reuse model** — a defensible answer to "how much does reuse actually save?", and the data point that reuse typically runs **30–60% of from-scratch cost**.
3. A **weighted decision matrix** you can deploy in your next architecture meeting — and the awareness that its real job is to **surface the weights**, not to compute the answer.
4. The **five principles of VBSE** — a vocabulary for moving the conversation from "did we ship on time?" to "did the right stakeholders win?"
5. A **WinWin** lens on stakeholder conflict — and the recognition that a decision someone cannot win at is *unstable*.
6. **Real options** as economic value — a defence of the alternative whose static NPV is lower but whose exits are cheaper.

Tomorrow we cross the line into the AI era — and ask which of today's principles survive when much of the code is written by something that is neither a human engineer nor a vendor.

---

*Prepared by Dr. Zhijiang Chen — Frostburg State University, Summer 2026.*
