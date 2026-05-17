---
title: "Lecture 12 — AI Topic I: Estimation & Productivity Disruption"
author: Dr. Zhijiang Chen
date: 2026-06-14
session: 12
week: 15
room: SY109
---

# Lecture 12 — AI Topic I: Estimation & Productivity Disruption

!!! info "Session Info"
    **Date:** Sunday, 2026-06-14 &nbsp;·&nbsp; **Time:** 16:20–18:10 &nbsp;·&nbsp; **Room:** SY109
    **Instructor:** Dr. Zhijiang Chen

[**▶ Open the lecture slides — 110-minute web deck**](slides/lecture-12.html){ .md-button .md-button--primary target="_blank" }

!!! tip "Using the slide deck"
    The slides are a self-contained web presentation (Reveal.js, no install). Keyboard shortcuts: **F** for full-screen, **S** for speaker view, **Esc** for slide overview, **arrow keys** to navigate. Designed for a single 110-minute session.

---

## Learning Objectives

By the end of this lecture, you should be able to:

1. Explain **why the LOC↔effort link breaks** under AI assistance — and why Function Points, surprisingly, still survive.
2. State the **2026 productivity paradox** in numbers: juniors $+10$ to $+30\%$, mid-level $+5$ to $+15\%$, seniors $-19\%$, and explain the **verification-overhead** mechanism behind the senior slowdown.
3. Propose **2–3 outcome metrics** to replace LOC for AI-augmented teams and articulate the Goodhart-law trap each metric creates.
4. **Re-calibrate COCOMO II** for AI assistance by adjusting the TOOL, APEX, and PLEX effort multipliers — without changing the model's structural equations.
5. Build a **disaggregated ROI model** for AI-tool adoption that separates juniors, mid-levels, and seniors, and interpret why a year-1 *negative* result can be a year-2 *positive* one.
6. **Stress-test** any vendor productivity claim with five disciplined questions: sample, metric, counterfactual, duration, hidden costs.

---

## Lecture Map — Five Movements, One Re-Calibration

Lectures 1–11 were the classical canon — Boehm, COCOMO, Function Points, all written before LLMs entered the developer's IDE. Today we ask what survives, what breaks, and what's new. Tomorrow (Lecture 13) we turn to the runtime cost side: tokens, caching, agent multipliers.

| § | Topic | Minutes |
|---|-------|--------:|
| I | Why classical estimation breaks under AI assistance | 15 |
| II | The productivity paradox: junior $+10$ to $+30\%$, senior $-19\%$ | 20 |
| III | New productivity metrics for AI-augmented teams | 20 |
| IV | Adapting COCOMO II — which knobs to turn | 15 |
| — | *Discussion: stress-test a "55% productivity" vendor claim* | 10 |
| V | Case study — 50-engineer SaaS company adopts AI coding tools | 20 |
| — | Homework, recap, Q&A | 10 |

!!! note "Prerequisite from Lectures 10–11"
    This lecture assumes you are comfortable with **Function Points (UFP, AFP)** and **COCOMO II** — the effort equation $PM = A \cdot Size^{E} \cdot \prod EM_i$, the five scale factors, and the seventeen effort multipliers. If those don't yet feel automatic, skim the Lecture 10–11 pages first; every adjustment today is an adjustment *to* that machinery.

---

## § I. Why Classical Estimation Breaks Under AI Assistance

The central claim of this section: **AI assistance does not invalidate software estimation — it invalidates the *coefficients*, not the *equations*.** The shapes of Boehm's curves are intact. What changed is the numbers you plug into them.

### Three classical pillars — wobbling at different rates

| Classical assumption | Status in 2026 | What broke |
|----------------------|----------------|------------|
| **LOC $\approx$ effort** | **Broken.** | AI generates working code in minutes that would have taken hours. The LOC-to-person-month curve is no longer stable — the same 1,000 lines can take 2 days or 20 days depending on *who* prompted it and *how much* verification it needed. |
| **FP $\approx$ scope** | **Survives.** | Function Points count *what the system does for the user* — inputs, outputs, inquiries, internal files, external interfaces. AI doesn't change the system's specification. The FP count of an order-entry screen is the same whether a human types the code or a model generates it. |
| **COCOMO calibration** | **Broken in coefficients, intact in form.** | The model was calibrated against 161 projects from a pre-AI era. The structural power-law $PM \propto Size^{E}$ still describes reality. The effort multipliers — TOOL, APEX, PLEX — no longer span the right range. |

!!! quote "Working principle for the AI era"
    Function Points still count the system's *specification* correctly. It is the *downstream conversion* — FP → KSLOC → person-months — that needs re-calibration.

### What AI assistance moves, but does not remove

The most common conceptual error in 2026 is to model AI as a *multiplier* on classical effort — "AI cuts coding time by $30\%$, so multiply person-months by $0.7$." This is wrong because AI doesn't eliminate effort; it **relocates** it. Five new effort categories enter the ledger:

| New effort category | What it pays for | Where it lands |
|---------------------|------------------|----------------|
| **Prompt engineering** | Crafting the prompts that drive code generation; iterating on phrasing until output is usable | Front-loaded onto the engineer writing the feature |
| **Verification** | Reading, testing, and validating generated code; running extra tests because trust is not earned | Distributed across reviewers — heaviest on seniors |
| **Integration overhead** | Fitting generated snippets into a real architecture; resolving name clashes, style drift, dependency mismatches | Lands on the merger of the PR |
| **Drift management** | Re-validating outputs as model versions change ("the model that wrote this last quarter is gone") | Recurring, often missed in plans |
| **Hallucination triage** | Catching plausible-looking but incorrect AI output — fabricated APIs, made-up function signatures, subtly wrong logic | Spiky; concentrated in unfamiliar domains |

### The verification-overhead concept

A senior engineer with a strong mental model of correctness does not *accept* AI output the way a junior does. They:

- Read every generated line to confirm correctness.
- Mentally check for security and performance issues the model may have missed.
- Verify that the suggested approach matches the existing architecture, not just compiles.
- Run extra tests because trust is not yet earned.

For routine code — a CRUD endpoint, a utility function — this verification can take **longer than typing the code from memory** would have. The senior loses time on tasks where AI offers the least leverage. This is the **verification overhead**, and it is the central puzzle of the next section.

The economic question is not *"should we adopt AI?"* It is **"for which tasks, by which engineers, with what verification protocol?"**

---

## § II. The Productivity Paradox — One Tool, Three Populations, Three Answers

The 2026 empirical record is the most interesting result in software economics in twenty years: a single tool produces *different signs* on productivity depending on the seniority of the user. The headline number — "$+18\%$ team productivity" or whatever vendor X claims — is a weighted average that hides a within-team rearrangement.

### The 2026 numbers

| Population | Productivity delta | Why |
|------------|-------------------:|-----|
| **Junior** (0–2 yrs) | $+10\%$ to $+30\%$ | AI fills knowledge gaps; reduces idle research and Stack-Overflow time. Junior accepts most suggestions; net effect is large and positive. |
| **Mid-level** (3–6 yrs) | $+5\%$ to $+15\%$ | AI accelerates boilerplate (test scaffolding, type definitions, docs). Modest verification cost. Net positive but small. |
| **Senior** (7+ yrs) | $-19\%$ | Verification overhead exceeds the time saved on routine code. On non-routine code, the AI rarely helps and often distracts. |

**Sources.** 2026 enterprise studies — *Larridin Developer Productivity Benchmarks 2026*, *Exceeds.ai AI Coding Agent Productivity Paradox*, plus internal data from three Fortune-500 engineering organisations. The senior slowdown has been **reproduced across at least four independent studies in 2025–2026** — it is not a statistical fluke and not a one-vendor artefact.

!!! warning "What the headline hides"
    "AI raises team productivity" is a true statement that hides redistribution: AI **lifts your junior staff and slows your senior staff**. If your team is half senior, your headline gain is a fraction of the vendor's number — and if your team is *mostly* senior, it can be *negative*.

### Why seniors slow down — the mechanism

Three reinforcing reasons:

1. **The acceptance threshold is higher.** A senior's prior probability that a generated line is wrong is much higher than a junior's. Higher prior $\Rightarrow$ more verification $\Rightarrow$ less net speedup.
2. **The hand-typed baseline is fast.** A senior typing a CRUD endpoint from muscle memory is *already* near the speed of light. There is no slack for AI to remove.
3. **The hardest tasks are the ones AI helps least with.** Architecture, security, performance — where seniors spend their time — are exactly where AI assistance is weakest. AI is best at routine code; seniors are not the ones who write the routine code.

The mid-level engineer sits between these forces — enough mental model to verify quickly, enough routine work to benefit. The net result is a small positive.

### The non-monotone curve, drawn

```
   productivity
   delta
       │
   +30 │  *
       │   \
   +20 │    \
       │     \                     (junior)
   +10 │      \
       │       \________
     0 │                ‾‾----.____      (mid)
       │                          \
   -10 │                           \
       │                            \____   (senior)
   -20 │                                 *
       └──────────────────────────────────── seniority (yrs)
        0       2       6              10+
```

The curve is **non-monotone**: productivity rises, plateaus, and then *falls* as seniority increases. Classical estimation models — which assume more experience monotonically reduces effort (lower APEX, lower PLEX) — get this wrong by construction.

---

## § III. New Productivity Metrics — When LOC Is No Longer Trustworthy

If LOC and lines-per-day are no longer trustworthy proxies for effort or value, what replaces them? The honest answer is: **no single number does.** What works is a small basket of *outcome* metrics, used together, re-evaluated quarterly.

### Five replacement candidates

| Metric | What it captures | Risk |
|--------|------------------|------|
| **PR-to-production cycle time** | Speed from commit to deployment — the end-to-end latency of value | Cycle-time gaming: tiny PRs that move the metric without moving the work |
| **Defect-free deploy frequency** | Reliable changes per period — DORA-style | Encourages small, safe changes; can punish risky-but-valuable refactors |
| **Customer-visible features shipped** | Value delivered to users, not just lines moved | Defining "feature" is hard; vendors will pad the count |
| **Tickets resolved per engineer** | Concrete backlog progress | Penalises engineers who take the hardest tickets; encourages cherry-picking |
| **Engineer-reported flow time** | Subjective effectiveness — the engineer's own answer to "did AI help today?" | Self-report bias; mood and politics intrude |

### The Goodhart's-law caveat

> *"When a measure becomes a target, it ceases to be a good measure."* — Goodhart's law.

Every metric on the list above is a *measure* that becomes a perverse *target* the moment it appears on a dashboard with someone's bonus attached. LOC was the textbook example — and it ended with `for (int i = 0; i < n; i++);` filler. The new metrics will end the same way unless you:

1. **Use 2–3 in combination**, so gaming one degrades another. Cycle time *and* defect-free deploys *and* customer features — gaming all three is harder than gaming one.
2. **Rotate them quarterly.** Last quarter's metric becomes this quarter's diagnostic, not this quarter's target.
3. **Tie them to outcomes the customer can see.** A metric the customer doesn't notice is a metric your team will eventually fake.

### What *not* to measure in the AI era

- **Lines of code per engineer.** Already discredited; AI makes it actively misleading. A junior using an aggressive AI tool can generate 10K lines a week of code nobody wants.
- **Number of AI suggestions accepted.** Tracks tool *usage*, not value. A team forced to "use AI more" will accept more suggestions; nothing about productivity follows.
- **Commits per day.** Trivially gameable; correlates with neither value nor effort.

The good news: **outcome metrics are stable across tooling regimes.** Cycle time, defect-free deploys, and features shipped are good metrics in 1985, in 2005, and in 2026 — the tools change; the *outcomes* you want from the tools do not.

---

## § IV. Adapting COCOMO II — Which Knobs to Turn

COCOMO II survives the AI era — its **structural equation** still describes reality. What changes is the **range** of three of its seventeen effort multipliers (EMs), and arguably the addition of a new one. We adjust coefficients, not equations.

### Recall: the COCOMO II form

$$
PM = A \cdot Size^{E} \cdot \prod_{i=1}^{17} EM_i
$$

where $Size$ is in KSLOC (or derived from FP), $E$ is a scale-factor-driven exponent, and the $EM_i$ are seventeen effort multipliers each rated *Very Low* to *Extra High*. The structural claim — effort scales *super-linearly* in size, modified multiplicatively by team and product factors — is independent of whether the code is human-typed or model-generated.

### The three EMs to re-calibrate

| Effort Multiplier | What it captures | Classical range (Boehm 2000) | Proposed AI-era range (2026) |
|-------------------|------------------|-----------------------------:|-----------------------------:|
| **TOOL** — Tool support | Maturity of the dev toolchain (compiler, IDE, debugger, AI assistant) | $0.78 - 1.17$ | $0.65 - 1.10$ (better tools, narrower range) |
| **APEX** — Application experience | Team experience with the problem domain | $0.81 - 1.22$ | $0.70 - 1.30$ (AI fills gaps for inexperienced; *widens* range) |
| **PLEX** — Platform experience | Team experience with the runtime/hardware platform | $0.85 - 1.19$ | $0.75 - 1.20$ (AI helps slightly with new platforms) |

Why TOOL **narrows** and APEX **widens** is worth dwelling on:

- **TOOL narrows** because AI assistants raise the *floor* of tool support — even a bad toolchain now ships with a usable AI assistant. The best-case multiplier improves modestly; the worst-case improves dramatically; the range compresses.
- **APEX widens** because AI assistance has a *bigger effect on inexperienced teams than experienced ones*. A team new to a domain that wields AI well can punch above its experience; a team that uses AI badly is no better off than before. The variance across teams grows.

### A possible new EM — AIUSE

| EM | Captures | Status |
|----|----------|--------|
| **AIUSE** — Effective use of AI assistance | Team practice quality: prompt discipline, verification protocols, restraint on critical-path code | **Research direction**, not yet a recommendation |

Calibration data for AIUSE is not yet sufficient. The hypothesis — that team AI-usage *practice* is a measurable, separable factor — is plausible but unproven. Adding an EM without calibration data is exactly the COCOMO sin Boehm warned against in 1981.

!!! quote "The COCOMO discipline in the AI era"
    Adjust EMs, but keep the model's *structure*. The power-law in size and the scale-factor mechanism still describe reality. The AI era changes coefficients, not equations.

### What this means for your estimates

A team that previously rated TOOL at *Nominal* ($1.00$) and APEX at *High* ($0.88$) might today rate TOOL at *High* ($0.85$) and APEX still at *High* ($0.88$) — knocking the product down from $0.88$ to $0.75$, a $15\%$ effort reduction *attributable to tools alone*. That number is **defensible and conservative**. A vendor's $55\%$ headline is not.

---

## § V. Case Study — 50-Engineer SaaS Adopts AI Coding Tools

The Lecture 10–11 case studies were about estimating a project to be built. This one is about a decision a 2026 CFO actually faces: **should we deploy an AI coding assistant across the engineering org, and what should we tell the board the ROI will be?**

### The setting

A mid-market B2B SaaS company. Engineering org: $50$ engineers, divided as:

- $15$ juniors at fully-loaded $\$90\text{K}$/yr each
- $20$ mid-levels at fully-loaded $\$130\text{K}$/yr each
- $15$ seniors at fully-loaded $\$180\text{K}$/yr each

Total annual payroll for the engineering org: $15 \cdot 90\text{K} + 20 \cdot 130\text{K} + 15 \cdot 180\text{K} = \$5{,}300{,}000$.

The vendor pitches an AI coding tool at $\$25$/seat/month. Expected token spend is roughly $\$80$/seat/month based on internal traffic estimates. The vendor's headline: *"55% productivity gains in pilot deployments."*

### Year-1 ROI ledger — disaggregated

| Item | Calculation | Per year |
|------|-------------|---------:|
| Licences | $50 \times \$25 \times 12$ | $-\$15{,}000$ |
| Token spend | $50 \times \$80 \times 12$ | $-\$48{,}000$ |
| Adoption / training (year 1 only) | $50 \times 8$ hrs $\times \$60$/hr | $-\$24{,}000$ |
| **Total costs** | | $-\$87{,}000$ |
| Productivity gain — **juniors** | $15 \times 20\% \times \$90\text{K}$ | $+\$270{,}000$ |
| Productivity gain — **mid-level** | $20 \times 10\% \times \$130\text{K}$ | $+\$260{,}000$ |
| Productivity LOSS — **seniors** | $15 \times (-19\%) \times \$180\text{K}$ | $-\$513{,}000$ |
| **Net productivity** | | $+\$17{,}000$ |
| **Net Year 1** | $+17{,}000 - 87{,}000$ | $\boxed{-\$70{,}000}$ |

### What just happened

A vendor headline of "$55\%$ productivity" — applied naively to the full $\$5.3\text{M}$ payroll — would have projected $0.55 \times 5.3\text{M} \approx \$2.9\text{M}$/yr in gains. The disaggregated reality is **negative \$70K in year 1**, because the senior loss ($-\$513\text{K}$) exceeds the combined junior and mid gains ($+\$530\text{K}$) by only a slim margin — and that slim margin is then erased by direct costs.

The single biggest line in the ledger is the **senior productivity loss**. Not the licences. Not the tokens. The *human verification tax*.

### Year 2 — what changes (and what doesn't)

Year 2 typically turns positive *if and only if* the organisation invests deliberately in change management. The mechanism:

| Year-1 problem | Year-2 mitigation | Effect on senior delta |
|----------------|-------------------|-----------------------:|
| Seniors use AI on routine code where verification overhead exceeds savings | **Restrict AI to leveraged tasks** — review prep, doc generation, exploratory prototyping | $-19\% \to -8\%$ |
| Seniors verify every line by hand | **Build verification habits** — test scaffolding, prompt templates with invariant checks | $-8\% \to -3\%$ |
| Seniors and juniors use the tool in isolation | **Pair seniors with juniors** — junior generates, senior reviews | $-3\% \to +2\%$ |

If those mitigations land, the senior contribution flips from $-\$513\text{K}$ to roughly $+\$54\text{K}$ — a **$\$567\text{K}$ swing**. Adoption/training drops out (one-time). Net year-2 result lands somewhere around $+\$500\text{K}$ — a real, defensible ROI.

!!! warning "Year-1 ROI is a change-management measurement"
    Year-1 ROI is mostly a measure of *change-management quality*, not tool quality. A team that bought the tool and walked away gets the negative number. A team that invested in workflow design gets the positive one. The vendor's "$55\%$" pretends those two outcomes are the same case.

### Four mitigations, applied in order

1. **Restrict AI to leveraged tasks.** Code review preparation, doc generation, exploratory prototyping, test scaffolding. *Avoid* on critical-path security or performance code. The leverage is in the boring stuff.
2. **Build verification habits.** Automated tests of AI output, prompt templates with built-in invariant checks, mandatory diff review even for "trivial" AI changes.
3. **Pair seniors with juniors.** Junior uses AI; senior verifies. Captures both populations' strengths and dilutes both their weaknesses.
4. **Track outcomes, not lines.** Measure features shipped and defects avoided, not LOC or AI suggestions accepted. The metric you choose determines the behaviour you get.

### A five-year view — forward pointer to Lecture 13

The Year-1/Year-2 picture is only the labour side. Tomorrow (Lecture 13) we add the **runtime cost side**: tokens, caching, agent multipliers, inference cost growth. The full five-year TCO of an AI-augmented engineering org has a different shape than the labour-only view shown here.

---

## Discussion — Stress-Test a Vendor Claim

**Setup.** Vendor X claims their AI tool delivers **"55% productivity gains"** in pilot deployments. In pairs (4 minutes), interrogate the claim. **Don't accept or reject it — figure out what you would need to know to decide.**

After 4 minutes, three pairs will share their toughest question. The five questions every engineer should have automatic:

| Question | Why it matters |
|----------|---------------|
| **1. Sample.** Whose engineers? What seniority mix? Self-selected enthusiasts or randomly assigned? | A junior-heavy pilot, or one with self-selected adopters, exaggerates gains by $2-3\times$. |
| **2. Metric.** Productivity measured *how*? LOC/hour? PRs merged? Self-reported flow? | Different metrics give different signs — see § III. |
| **3. Counterfactual.** Compared to *what* — last quarter, a control group, the same team without AI? | "Compared to last quarter" confounds AI gains with seasonal, team, and morale effects. |
| **4. Duration.** One-week pilot or a full quarter? Was the novelty boost still in play? | Productivity in a one-week novelty pilot can be $2\times$ steady-state. |
| **5. Hidden costs.** Verification time, integration overhead, model-drift re-work — included? | These are the costs that flip the sign — see the case study. |

The point is not that vendors lie. It is that **claims unconditioned on these five questions are not evidence** — they are marketing.

---

## Class Discussion Prompts

Open floor, ~10 minutes.

1. Of the five new effort categories in § I (prompt engineering, verification, integration, drift, hallucination), which one does *your* team under-count the most? What would it cost you in person-months over a year?
2. Pick a project you know. If you replaced LOC with two outcome metrics from § III, which two would you pick — and which Goodhart trap would you watch for?
3. The case study put the senior loss at $-19\%$. Imagine your own team's seniors. Would the loss be larger or smaller, and what specific habit or workflow drives your guess?

---

## Homework 12 — An Honest AI ROI Memo

**Due: Tuesday 2026-06-16, before Lecture 14. Late: −10% per day.**

1. **Disaggregated ROI model.** Build a year-1 ROI model for adopting an AI coding tool at a *hypothetical 30-person engineering org*. Pick a realistic seniority split (at least three buckets — junior / mid / senior). Use the productivity ranges from § II. Show every line: licences, tokens, training, per-bucket gain or loss, net.
2. **Breakeven analysis.** Identify the **single input** that, if it changed, would flip the sign of your year-1 net. State its current value and the breakeven value.
3. **Change-management proposal (one paragraph).** Propose a specific change-management investment to deploy *alongside* the tool (e.g. structured verification training, paired junior–senior sessions, prompt-template library). Estimate its cost and its NPV at MARR $= 10\%$ over three years.
4. **Critique of two vendor claims.** Find two real 2026 AI coding-tool vendor productivity claims (links allowed). For each, apply the **five-question stress test** from today's discussion. Two paragraphs each.

**Submission.** A single PDF (spreadsheet + paragraphs) committed to the course repository at `submissions/HW12/<your-name>.pdf`.

**Office hours.** Tomorrow, 10:00–11:30, before Lecture 13.

---

## Key Vocabulary — Quick Reference

| Term | Plain-language meaning |
|------|------------------------|
| Verification overhead | Engineer time spent reading, testing, and validating AI-generated output. The senior tax. |
| Productivity paradox | Headline team gain masks redistribution — juniors faster, seniors slower. |
| Hallucination triage | Catching plausible-looking but incorrect AI output. |
| Drift management | Re-validating AI outputs as model versions change over time. |
| Goodhart's law | "When a measure becomes a target, it ceases to be a good measure." |
| Outcome metric | A metric tied to customer-visible value, not internal activity (e.g. cycle time, features shipped). |
| TOOL · APEX · PLEX | COCOMO II effort multipliers most affected by AI assistance. |
| AIUSE | Proposed new COCOMO EM for team-level AI-usage practice. **Research direction**, not calibrated. |
| Change-management quality | The deliberate investment in workflow design that determines whether year-1 ROI is positive or negative. |
| Five-question stress test | Sample · Metric · Counterfactual · Duration · Hidden costs — the questions every vendor productivity claim must answer. |

---

## What Today Bought You

1. **Function Points still measure scope correctly.** It is the *downstream conversion* — FP → KSLOC → person-months — that the AI era breaks.
2. **The productivity paradox is real and reproducible.** Juniors faster, seniors slower. Headline gains hide redistribution.
3. **The right question is not "should we adopt AI?"** — it is **"for which tasks, by which engineers, with what verification protocol?"**

Tomorrow (Lecture 13) we move from how AI changes the *cost of building* software to how AI changes the *cost of running* it — tokens, caching, agent multipliers, payback periods.

---

## References

- **Larridin.** (2026). *Developer Productivity Benchmarks 2026* — the source of the juniors $+10$–$30\%$ / mid $+5$–$15\%$ / seniors $-19\%$ figures used throughout this lecture.
- **Exceeds.ai.** (2026). *AI Coding Agent Productivity Paradox* — independent replication of the senior slowdown across three enterprise deployments.
- **Exceeds.ai.** (2026). *Enterprise AI Coding Tools ROI* — the case-study data underpinning § V.
- **Boehm, B. W., et al.** (2000). *Software Cost Estimation with COCOMO II*. Prentice Hall — Chapter 2 (effort equation) and Chapter 3 (effort multipliers TOOL, APEX, PLEX).
- **Albrecht, A. J., & Gaffney, J. E.** (1983). "Software function, source lines of code, and development effort prediction." *IEEE TSE* — the canonical FP-to-KSLOC conversion that today's lecture qualifies.
- **Goodhart, C.** (1975). "Problems of Monetary Management" — origin of Goodhart's law as applied to metrics.
- *(Forward pointer)* **Lecture 13 — AI Token Economics & ROI** will quantify the runtime side of AI-augmented engineering, completing the five-year TCO begun today.

---

*Prepared by Dr. Zhijiang Chen — Frostburg State University, Summer 2026.*
