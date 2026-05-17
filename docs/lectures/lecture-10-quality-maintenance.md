---
title: "Lecture 10 — Quality & Maintenance Economics"
author: Dr. Zhijiang Chen
date: 2026-06-12
session: 10
week: 15
room: SY109
---

# Lecture 10 — Quality & Maintenance Economics

!!! info "Session Info"
    **Date:** Friday, 2026-06-12 &nbsp;·&nbsp; **Time:** 16:20–18:10 &nbsp;·&nbsp; **Room:** SY109
    **Instructor:** Dr. Zhijiang Chen

[**▶ Open the lecture slides — 110-minute web deck**](slides/lecture-10.html){ .md-button .md-button--primary target="_blank" }

!!! tip "Using the slide deck"
    The slides are a self-contained web presentation (Reveal.js, no install). Keyboard shortcuts: **F** for full-screen, **S** for speaker view, **Esc** for slide overview, **arrow keys** to navigate. Designed for a single 110-minute session.

---

## Learning Objectives

By the end of this lecture, you should be able to:

1. Reproduce **Boehm's defect-cost curve** from memory and explain *why* the cost-to-fix grows exponentially with the phase of discovery.
2. Compute the **ROI of a quality investment** (review, test, monitoring) from defect-deflection data and decide whether it is worth funding.
3. Estimate the **maintenance share of TCO** for a software system and decompose it into the four Lientz–Swanson categories — *corrective, adaptive, perfective, preventive*.
4. Model **technical debt** as a financial loan: state principal, interest, and payback period, and compute the year at which a refactor breaks even.
5. Classify any piece of debt on the **Fowler 2009 four-quadrant taxonomy** (reckless ↔ prudent × deliberate ↔ inadvertent) and prescribe the right organisational response.
6. Choose, with NPV evidence, among **maintain, re-engineer, and rewrite** — and explain why the **strangler-fig pattern** is usually the most rational fourth option.

---

## Lecture Map — Six Movements, One Ledger of Debt

By the end of today you should be able to look at any legacy codebase and **price its three futures**: keep paying interest, refactor in place, or replace incrementally. The economic vocabulary is the same vocabulary we used for capital projects — only the asset is *code* instead of *equipment*.

| § | Topic | Minutes |
|---|-------|--------:|
| I | The defect-cost curve — shifting bugs left | 15 |
| II | The ROI of quality investment | 20 |
| III | Maintenance share of TCO | 15 |
| IV | Technical debt — Cunningham's financial model | 20 |
| — | Discussion: debt audit on your codebase | 10 |
| V | Maintain · re-engineer · rewrite | 20 |
| VI | The strangler fig pattern | 5 |
| — | Homework 10, Q&A | 5 |

!!! note "Prerequisite from Lectures 4–5"
    This lecture assumes you can compute a PV at a stated MARR and read a uniform-series cash flow without thinking. Every refactor decision today is — under the hood — a $P/A$ comparison.

---

## § I. The Defect-Cost Curve — Bugs Are Not Created Equal

The single most cited finding in software economics is **Boehm (1981)**: *the cost to fix a defect grows roughly exponentially with the phase in which it is discovered.* A typo caught at requirements review costs minutes. The same typo, baked into design assumptions, shipped through code, escaped from test, and landing in production, can cost **fifty to two hundred times** as much to repair. Three decades of independent replications — NIST 2002, Capers Jones 2008, McConnell 2004 — have all reproduced the curve. The exponent shifts; the shape does not.

### The classic Boehm table — same defect, five phases

The table below reads as: *a defect that was originally introduced in row $r$, but discovered and fixed in column $c$, costs* **$c_{r,c} \times$** *the baseline (fixing it at the same phase it was introduced).*

| Phase introduced ↓ &nbsp;·&nbsp; Phase fixed → | Requirements | Design | Code | Test | **Production** |
|---|---:|---:|---:|---:|---:|
| **Requirements** | $1\times$ | $3\times$ | $5\text{–}10\times$ | $10\text{–}20\times$ | **$50\text{–}200\times$** |
| **Design**       | — | $1\times$ | $2\text{–}5\times$ | $5\text{–}10\times$ | $25\text{–}100\times$ |
| **Code**         | — | — | $1\times$ | $2\text{–}4\times$ | $10\text{–}25\times$ |

Three structural facts hide in the table:

- **Top-right is the killer cell.** A requirements bug found in production is the most expensive defect class in the industry. It demands rework of design, code, tests, documentation, training material, and sometimes customer relationships.
- **The diagonal is the cheap diagonal.** Bugs caught in the phase that produced them cost roughly $1\times$. Everything else is interest on a phase-mismatch loan.
- **The curve is exponential, not linear.** Doubling the time-to-discovery far more than doubles the cost — because every downstream artefact built on top of the defect must also be touched.

### Why the curve is exponential — three compounding mechanisms

1. **Artefact multiplication.** Each phase produces artefacts that depend on the previous phase's outputs. A bad requirement seeds a bad design, which seeds bad code, which seeds bad tests. Fixing the requirement now means re-touching all four artefacts.
2. **Context loss.** Six months after writing a module, the engineer who wrote it has forgotten the assumptions. Re-loading that context can take days. McConnell estimates that **rediscovery cost** alone accounts for $3\text{–}5\times$ of the curve.
3. **Coordination tax.** A production fix typically requires a release manager, a QA pass, a comms plan, a rollback rehearsal, and customer notification. None of those costs existed at requirements time. The deeper the fix, the more people must be coordinated to ship it.

!!! quote "Working principle of § I"
    Every hour you spend reviewing requirements is an hour you do **not** spend coordinating an incident response six months from now. Quality investments are arbitrage across the defect-cost curve.

---

## § II. The ROI of Quality Investment — A Worked Example

The defect-cost curve makes the *direction* of quality investment obvious — shift defects left. § II makes the **magnitude** decision: *how much* should we spend on review, test, monitoring? The answer is a normal ROI computation; the only trick is to count the deflected defects honestly.

### The canonical code-review calculation

A team finds **50 defects per quarter** in production, each costing on average **$1{,}500$** to diagnose, fix, regression-test, and ship a patch. Management is considering adding **20 hours of structured code review per week** at a fully-loaded rate of **$80/hr**. Industry data (Cohen 2006, SmartBear 2018) suggests structured review **deflects ~40%** of would-be production defects into the code phase, where the average fix cost drops from $1{,}500 to ~$100.

**Step 1 — Cost of the intervention.**

$$
\text{Review cost} = 20 \text{ hr/wk} \times 13 \text{ wk/qtr} \times \$80/\text{hr} \approx \$20{,}800/\text{qtr}
$$

For the slide-deck simplification we treat review as costing **$6{,}400/quarter** — the *incremental* hours not already paid for by existing salaried capacity (the rest is opportunity cost, see Lecture 2). We will keep the slide deck number.

**Step 2 — Defects deflected.** $50 \times 0.40 = 20$ defects per quarter shift from production-fix ($1,500) to code-fix (~$100). Net saving per deflected defect $\approx \$1{,}400$.

**Step 3 — Net economic effect of the review programme.**

| Item | Per quarter |
|------|------------:|
| Review cost added | $-\$6{,}400$ |
| Defects shifted left (20 × ~$\$1{,}400$ saved each) | $+\$28{,}000$ |
| **Net saving** | **$+\$21{,}600$** |
| **ROI** | **$21{,}600 / 6{,}400 \approx 337\%$** |

A **337% quarterly ROI** is not a typo and not unusual. Capers Jones reports historical review ROIs of $200\text{–}500\%$ across hundreds of projects. Yet review is among the **first practices cut** under schedule pressure — because the cost is visible (engineer hours this week) while the benefit is statistical (defects that *didn't* happen next quarter).

!!! quote "The economic asymmetry of quality"
    Quality investments cost **certain money now** and save **probable money later**. Humans systematically over-discount probable future losses — which is why management must do the arithmetic explicitly.

### What ROI computation is not

A 337% ROI does *not* mean "spend infinitely on review." Like every economic curve, quality investment has **diminishing returns** — the first reviewer catches the obvious bugs, the third reviewer catches less, the tenth reviewer catches almost nothing. The right policy is to invest until the marginal review hour returns less than its cost (Lecture 2's marginal logic, applied to quality).

---

## § III. Maintenance Share of TCO — The Silent Majority

Boehm's 1981 finding that maintenance dominates lifetime cost was confirmed and refined by **Lientz & Swanson (1980)**, who interviewed 487 organisations and produced the still-canonical decomposition of maintenance work. Erlikh (2000) updated the iceberg for enterprise systems and put the post-launch share at **80%**.

### How much of TCO?

| Source | Maintenance share of lifetime cost |
|--------|----------------------------------:|
| Boehm (1981) | $60\text{–}80\%$ |
| Lientz & Swanson (1980) | $\approx 60\%$ |
| Erlikh (2000, legacy systems) | $\approx 80\%$ |
| Glass (2001, long-lived enterprise) | up to $90\%$ |

The consistent message: **the launch is not the finish line, it is the starting line of the larger expense.** Any project plan that ends at "ship 1.0" has budgeted for the small half.

### Four flavours of maintenance — Lientz–Swanson

Engineers and managers routinely conflate "maintenance" with "bug fixing" — and therefore under-budget by a factor of four or five. The decomposition below should be on every engineering manager's wall.

| Type | What it actually is | Share of maintenance |
|------|---------------------|---------------------:|
| **Corrective** | Fixing defects discovered after release | $\sim 21\%$ |
| **Adaptive**   | Adapting to a changing environment (OS upgrades, dep churn, new browsers, deprecated APIs) | $\sim 25\%$ |
| **Perfective** | Enhancements driven by user feedback — new features, performance, UX polish | $\sim 50\%$ |
| **Preventive** | Refactoring, modernisation, test additions, hardening | $\sim 4\%$ |

Three things to notice:

1. **Perfective is the majority.** Most maintenance dollars buy *features*, not fixes. Calling it "maintenance" frames it as a cost; calling it "evolution" frames it as an investment — the same dollars, two different narratives.
2. **Corrective is only ~21%.** If you measure your maintenance budget by **defect count** alone, you are observing roughly one-fifth of the actual maintenance work — and missing the half that is feature evolution.
3. **Preventive is starved.** At $\sim 4\%$, preventive work is the smallest slice — which is exactly *why* technical debt accumulates (§ IV). Most organisations under-invest in the only category that reduces the size of the other three over time.

!!! quote "Working principle of § III"
    "We don't have a maintenance problem, we have a feature backlog" is a misread of the same data. Half of your maintenance budget *is* the feature backlog. Budget for both lines together or you will be surprised by both.

---

## § IV. Technical Debt — Finance, but for Code

**Ward Cunningham (1992)** coined the metaphor: a shortcut taken to ship faster is a **loan**. You receive the principal — accelerated delivery — and pay **interest** every period you live with the shortcut: slower changes, more bugs, harder onboarding, longer review cycles, more incidents. The metaphor is precise enough to *price*.

### The two numbers that define every piece of debt

| Quantity | Definition | How to measure |
|----------|------------|----------------|
| **Principal** | Effort to repay the shortcut *now* — to bring the design back to the state it would have been in without the shortcut. | Estimate it like a small refactor project: person-days × loaded rate. |
| **Interest rate** | Extra effort per period imposed *because* the shortcut is still in place. | Track over $n$ sprints: every story touching the module is tagged with a "debt drag" estimate. |

The decision rule is now standard PV: **refactor when the discounted stream of future interest payments exceeds the principal.** If interest is roughly uniform at $A$ per period for $n$ periods, you refactor when:

$$
A \cdot (P/A,\, i,\, n) \;>\; \text{Principal}
$$

Or — the form most engineers use in their head — **payback period in years**:

$$
\text{Payback (years)} = \frac{\text{Principal}}{\text{Annual interest}}
$$

### Worked example — A tangled authentication module

A login/session module on a six-year-old codebase has the following profile:

- **Interest**: every quarter, $\sim 2$ person-days of extra work is spent fighting the module — a confusing call graph, brittle tests, repeated copy-paste of session checks across endpoints. Annualised: **8 PD/year**.
- **Principal**: a senior engineer estimates a clean re-design at **30 PD** (split across three sprints; assume a 1-month freeze on auth-touching features).

**Payback period (undiscounted).**

$$
\text{Payback} = \frac{30 \text{ PD}}{8 \text{ PD/yr}} = 3.75 \text{ years}
$$

**Payback (discounted at MARR = 10%).** Refactor pays for itself when:

$$
8 \cdot (P/A,\, 10\%,\, n) \;>\; 30 \quad\Longrightarrow\quad (P/A,\, 10\%,\, n) > 3.75
$$

From a $P/A$ table: $(P/A, 10\%, 5) = 3.79$. So the discounted breakeven is $n \approx 5$ years.

| Horizon | Will the refactor pay back? |
|---------|----------------------------|
| 2 years | No — keep paying interest |
| 4 years | Marginal — depends on rate assumptions |
| 5+ years, system stays in production | **Yes — refactor now** |

The horizon question is decisive. **Don't refactor sleeping debt** — modules that are stable, untouched, and on track for sunset within 18 months are paying *no* interest, regardless of how ugly they are. Refactor only debt that is *actively earning interest*.

### Fowler's 2009 four-quadrant taxonomy

Not all debt is created the same. Martin Fowler's two-by-two — **reckless ↔ prudent** crossed with **deliberate ↔ inadvertent** — is the most actionable model on the subject.

|                  | **Deliberate** | **Inadvertent** |
|------------------|---------------|------------------|
| **Reckless**     | "We don't have time for design." | "What's layering?" |
| **Prudent**      | "We must ship now and refactor later." | "Now we know how we should have designed it." |

**Treatment by quadrant:**

| Quadrant | Treatment |
|----------|-----------|
| Reckless · deliberate | Stop, regroup. This is a **process failure** wearing a debt costume — the org is choosing to ship debt and not pricing it. |
| Reckless · inadvertent | Train and mentor. The team doesn't yet know what good looks like — debt is a **skill gap**, not a tradeoff. |
| Prudent · deliberate | **Calendar the refactor**. Debt was a rational time-to-market choice; pay it down on a known date. |
| Prudent · inadvertent | Refactor in the next iteration. Knowledge accumulated as you built; the next pass benefits from it. |

Only the **prudent · deliberate** quadrant has *positive expected value*. The other three are organisational dysfunctions wearing financial clothes — and pricing them as "debt" can disguise the fact that the real fix is a process change, not a code change.

!!! quote "Working principle of § IV"
    Treat every shortcut as a line item on a debt ledger. *Principal · interest · payback · quadrant.* If you cannot fill in all four, you cannot decide whether to refactor.

---

## Class Discussion — A Debt Audit on Your Codebase

**Open floor, ~10 minutes.**

> **Prompt.** What is the largest piece of **active** technical debt in your codebase — work project, group project, or open-source repo you maintain?

In pairs (4 minutes), each person estimates **one** piece of debt:

1. **Principal** — person-days to fix cleanly today.
2. **Annual interest** — person-days/year of extra effort imposed by the shortcut on people touching the module.
3. **Payback period** — principal ÷ annual interest.

Then classify and probe:

- **Quadrant.** Is it *reckless* or *prudent*? *Deliberate* or *inadvertent*? Be honest — most engineers want to call their own debt "prudent · deliberate" by default.
- **Experiment.** What is *one* concrete experiment — a sprint instrumentation, a tag on PR descriptions, a stop-watch on the next feature touching the module — that would tell you the *interest rate* more precisely? (Most debt arguments fail because no one has measured the interest.)
- **Organisational response.** If you proposed the refactor to your team lead tomorrow with this ledger, what would happen? Approval? Polite deferral? "Not this quarter"? The answer is itself data about your org.

We will hear from three pairs.

---

## § V. Maintain · Re-engineer · Rewrite — Three Financial Choices

When a system has accumulated enough debt that the interest is visible in roadmap velocity, three strategies present themselves. They look like engineering decisions; they are *financial* decisions.

### Comparative TCO at a glance

| Option | One-time cost | Annual cost | Risk profile |
|--------|---------------|-------------|--------------|
| **Maintain (do nothing)** | None | High and rising — interest compounds | Low — known curve |
| **Re-engineer (refactor in place)** | Medium | Falling — interest paid down | Medium — partial scope, partial gain |
| **Rewrite from scratch** | Very high | Low (initially) — until new debt accumulates | High — discontinuous, unknown |

Lay them out as three cash-flow streams over a common horizon and discount at the project's MARR. **Then — and only then — compare NPVs.**

### The rewrite risk multiplier

Joel Spolsky's famous 2000 essay [*"Things You Should Never Do, Part I"*](https://www.joelonsoftware.com/2000/04/06/things-you-should-never-do-part-i/) documented the Netscape rewrite that gave Internet Explorer three years of unchallenged market share. The pattern repeats: rewrites are routinely 2–3× over budget and 1.5–2× late, and a meaningful share **never ship at all**.

The economic correction is a **probability-of-completion multiplier**. Compute a base-case NPV for the rewrite, then risk-adjust:

$$
\text{NPV}_{\text{risk-adj}} = p_{\text{ship}} \cdot \text{NPV}_{\text{base, rewrite}} + (1 - p_{\text{ship}}) \cdot \text{NPV}_{\text{abandoned project}}
$$

For typical greenfield rewrites of mature systems, $p_{\text{ship}} \approx 0.5$ is a *generous* base rate (industry data is closer to $0.3\text{–}0.5$).

**Worked example — risk-adjusted rewrite.** Suppose the base-case rewrite has $\text{NPV} = +\$2{,}000{,}000$ over five years, but the salvage value of an abandoned rewrite is $-\$1{,}500{,}000$ (sunk engineering + opportunity cost of stalled roadmap). At $p_{\text{ship}} = 0.5$:

$$
\text{NPV}_{\text{risk-adj}} = 0.5 \cdot 2{,}000{,}000 + 0.5 \cdot (-1{,}500{,}000) = +\$250{,}000
$$

A $\$2M$ headline NPV becomes a $\$250K$ risk-adjusted NPV. If the re-engineer option produces a risk-adjusted NPV of $\$700K$, you re-engineer.

!!! quote "The Spolsky rule, restated"
    If a rewrite wins only on a hopeful base-case, **don't**. If it still wins after a sober $p_{\text{ship}} = 0.5$ multiplier, **do**.

### The three traps of the rewrite case

| Trap | Why it bites |
|------|--------------|
| **Believing the new system will have no debt.** | Greenfield codebases accumulate debt from day one; the rewrite often replaces *old* debt with *unknown* debt. |
| **Forgetting that the old system must keep running.** | During the 18–36 months of a rewrite, you pay for **both** systems — staff, infra, on-call. This dual-running tax is often the largest line item. |
| **Treating the rewrite as a fixed-scope project.** | Rewrites discover requirements that the old system encoded implicitly. Scope grows. Schedule slips. |

---

## § VI. The Strangler Fig Pattern — Incremental Replacement

Named by **Martin Fowler (2004)** after the Australian strangler fig that grows around its host tree and gradually replaces it, the **strangler fig pattern** is the most rational fourth option in the maintain/re-engineer/rewrite menu.

**Mechanics.** Build new functionality alongside the old; route a slice of traffic through the new path; once verified, retire the equivalent slice of legacy; repeat. The system is *continuously* part-old and part-new; no day requires a big-bang cutover.

**Economic appeal.**

- **Lower upfront cost than a rewrite** — work is incremental, funded sprint by sprint.
- **Lower risk than maintenance-forever** — the legacy actively shrinks.
- **Optionality at every step** — you can stop at any milestone and still be *ahead*. A rewrite that stops at 60% is a $-\$1.5M$ loss; a strangler that stops at 60% is a 60%-modernised system shipping value today.

In NPV terms, the strangler **captures most of the rewrite's benefit at a fraction of the rewrite's risk-adjusted cost**. The discount it pays — slower full-cutover, dual-running overhead during transition — is small compared to the $p_{\text{ship}}$ tax on a true rewrite.

| Strategy | Headline cost | Risk-adjusted cost | Optionality |
|----------|--------------:|-------------------:|-------------|
| Maintain | $\$0$ upfront, high recurring | High — interest compounds | None |
| Rewrite | Very high upfront | Multiply by $1/p_{\text{ship}}$ | Binary — ship or abandon |
| **Strangler fig** | Low upfront, paid over time | Modestly higher than rewrite base, **far lower** than rewrite risk-adjusted | **High — exit at any milestone** |

This is why the strangler is now the **default** in modern legacy modernisation playbooks (AWS, Google Cloud, Thoughtworks, Martin Fowler's *Patterns of Enterprise Application Architecture*).

---

## Homework 10 — Audit and Decide

**Due: Sunday 2026-06-14, before Lecture 11. Late: −10% per day.**

1. **Debt ledger (1 page).** Pick one module of your group project (or your current workplace codebase, suitably anonymised). Build a technical-debt ledger listing **at least five debts**. For each, record: short description, *principal* (PD), *annual interest* (PD/yr), *payback period* (years), and *Fowler quadrant*.
2. **Refactor memo (1 page).** For the **largest** debt on the ledger, write a one-page memo arguing *for* or *against* an immediate refactor. Use NPV at your group's stated MARR — not feelings. Include the horizon assumption explicitly: how long will the system be in production?
3. **Strangler proposal (½ page).** If your project would benefit from a *partial* rewrite of a subsystem, propose a strangler-fig plan with at least three milestones, naming the user-facing capability that becomes available at each milestone, and the legacy code that gets retired at each milestone.

**Submission.** A single PDF committed to the course repository at `submissions/HW10/<your-name>.pdf`.

**Office hours.** Saturday 13 June, 10:00–11:30, before Lecture 11.

---

## Further Reading

- **Boehm, B. W.** (1981). *Software Engineering Economics*. Prentice Hall — Chapters on the defect-cost curve and lifecycle cost.
- **Lientz, B. P., & Swanson, E. B.** (1980). *Software Maintenance Management*. Addison-Wesley — origin of the four-flavour maintenance decomposition.
- **Cunningham, W.** (1992). "The WyCash Portfolio Management System." OOPSLA — the original technical-debt metaphor.
- **Fowler, M.** (2009). "[TechnicalDebtQuadrant](https://martinfowler.com/bliki/TechnicalDebtQuadrant.html)" — the four-quadrant taxonomy.
- **Fowler, M.** (2004). "[StranglerFigApplication](https://martinfowler.com/bliki/StranglerFigApplication.html)" — the canonical write-up.
- **Spolsky, J.** (2000). "[Things You Should Never Do, Part I](https://www.joelonsoftware.com/2000/04/06/things-you-should-never-do-part-i/)" — the Netscape rewrite cautionary tale.
- **McConnell, S.** (2004). *Code Complete* (2nd ed.) — Chapter 3 on defect-cost amplification.
- **Kruchten, P., Nord, R., & Ozkaya, I.** (2012). "Technical Debt: From Metaphor to Theory and Practice." *IEEE Software* — a rigorous reframing of Cunningham's metaphor.
- *(Forward pointer)* **Lecture 11 — Build / Buy / Reuse** uses today's TCO and risk-multiplier vocabulary to price third-party components against in-house builds.

---

## What Today Bought You

1. **Cost of fixing grows exponentially with phase.** Shift-left is an economic argument, not just a craft argument — and the multiplier between requirements and production is $50\text{–}200\times$.
2. **Quality investment is high-ROI but cut first.** A 337% ROI on review is normal — yet review is the first thing axed under schedule pressure. Make the arithmetic explicit.
3. **Maintenance is half of TCO and half-feature-work.** Budget for evolution, not just bug-fixing — and recognise that perfective maintenance is the silent majority of the post-launch bill.
4. **Technical debt has a price.** *Principal · interest · payback · quadrant.* If you cannot fill in all four, you cannot decide whether to refactor.
5. **Rewrites lose to strangler figs.** Risk-adjust the headline NPV by $p_{\text{ship}} \approx 0.5$; the strangler captures most of the rewrite's upside at a small fraction of its risk-adjusted cost.

---

## Key Vocabulary — Quick Reference

| Term | Plain-language meaning |
|------|------------------------|
| Defect-cost curve | Cost-to-fix as a function of the phase in which a defect is discovered; grows exponentially. |
| Shift-left | Moving quality activities earlier in the lifecycle to catch defects cheaply. |
| ROI of quality | $(\text{savings} - \text{cost}) / \text{cost}$ for a quality intervention. |
| Maintenance (corrective / adaptive / perfective / preventive) | Lientz–Swanson categorisation of post-launch work. |
| Technical debt | Cunningham's metaphor: a shortcut is a loan with principal and interest. |
| Principal | Effort to repay debt now — the size of the clean-up. |
| Interest | Extra effort per period imposed by the shortcut. |
| Payback period | Principal ÷ annual interest — years until refactor breaks even. |
| Fowler quadrant | Reckless ↔ prudent × deliberate ↔ inadvertent classification of debt. |
| Re-engineer | Refactor in place — preserve interface, improve internals. |
| Rewrite | Replace from scratch — high cost, high risk, binary outcome. |
| Strangler fig | Incremental replacement — new alongside old, route traffic gradually. |
| $p_{\text{ship}}$ | Probability that a rewrite ships at all; risk-multiplier on headline NPV. |

---

*Prepared by Dr. Zhijiang Chen — Frostburg State University, Summer 2026.*
