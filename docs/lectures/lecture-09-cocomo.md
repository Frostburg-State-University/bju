---
title: "Lecture 9 — Cost Estimation II: COCOMO II"
author: Dr. Zhijiang Chen
date: 2026-06-11
session: 9
week: 15
room: YF302
---

# Lecture 9 — Cost Estimation II: COCOMO II

!!! info "Session Info"
    **Date:** Thursday, 2026-06-11 &nbsp;·&nbsp; **Time:** 16:20–18:10 &nbsp;·&nbsp; **Room:** YF302
    **Instructor:** Dr. Zhijiang Chen

[**▶ Open the lecture slides — 110-minute web deck**](slides/lecture-09.html){ .md-button .md-button--primary target="_blank" }

!!! tip "Using the slide deck"
    The slides are a self-contained web presentation (Reveal.js, no install). Keyboard shortcuts: **F** for full-screen, **S** for speaker view, **Esc** for slide overview, **arrow keys** to navigate. Designed for a single 110-minute session. **Bring the AFP count from Homework 8 — we convert it to person-months in § VI.**

---

## Learning Objectives

By the end of this lecture, you should be able to:

1. Trace the **lineage from COCOMO 81 to COCOMO II** — what the 2000 re-calibration changed and why the 1981 model is now historical rather than operational.
2. State the **COCOMO II Post-Architecture equation** $\text{PM} = A \cdot \text{Size}^E \cdot \prod \text{EM}_i$ from memory, identify each symbol, and explain *why* each appears.
3. Rate a project on the **five scale factors** (PREC, FLEX, RESL, TEAM, PMAT) and compute the resulting exponent $E$, predicting whether the project will exhibit *economies* or *diseconomies* of scale.
4. Apply the **seventeen effort multipliers** in their four groups (Product, Platform, Personnel, Project), and recognise that the **Personnel** group dominates the rest.
5. Choose the right COCOMO II **sub-model** (Application Composition, Early Design, Post-Architecture) for the lifecycle phase you are estimating in — and *refuse* to claim precision the phase does not support.
6. Run a complete **FP → KSLOC → PM → \$** pipeline, including optimistic / nominal / pessimistic scenarios, and explain why the spread between them is itself the headline number.
7. Articulate the **calibration imperative**: a national model with no local correction is decoration, not estimation.

---

## Lecture Map — From a Number on a Page to a Defensible Budget

By the end of today you will have taken an Adjusted Function Point count from yesterday's homework and turned it into a dollar figure you can defend in a planning meeting — together with an honest range, not a single deceptive point estimate.

| § | Topic | Minutes |
|---|-------|--------:|
| I | COCOMO 81 → COCOMO II: twenty years of industry data | 10 |
| II | The COCOMO II equation — a power-law with knobs | 15 |
| III | Scale factors — five knobs that set the exponent | 15 |
| IV | Effort multipliers — seventeen multiplicative knobs in four groups | 15 |
| V | Early Design vs. Post-Architecture — matching precision to phase | 10 |
| VI | Worked example — FP → KSLOC → PM → \$, three scenarios | 25 |
| — | Discussion: how would *you* calibrate COCOMO II for AI-assisted dev? | 10 |
| — | Homework 9, references, Q&A | 10 |

!!! note "Prerequisite from Lecture 8"
    This lecture assumes you have already produced an **Adjusted Function Point (AFP)** count for the group project. If yesterday's count is still wobbly, re-do it before § VI — the entire pipeline today multiplies whatever number you bring.

---

## § I. COCOMO 81 → COCOMO II — Twenty Years of Industry Data

The **Constructive Cost Model** is the most-cited parametric effort model in software engineering. It exists because Barry Boehm, in the late 1970s, did something almost nobody else in the field was doing: he collected actual effort data from real industrial projects and *fit a curve* through them.

### COCOMO 81 — the original 63 projects

**Boehm (1981)** — *Software Engineering Economics*, the same volume that gave us the iceberg from Lecture 2 — proposed an effort equation $\text{PM} = a \cdot \text{KSLOC}^{b}$ calibrated on **63 industrial projects** at TRW. The constants $(a, b)$ depended on which of three **development modes** the project belonged to:

| Mode | Typical project | $a$ | $b$ |
|------|-----------------|----:|----:|
| **Organic** | Small, familiar in-house tooling | 2.4 | 1.05 |
| **Semi-detached** | Mixed familiarity, medium size | 3.0 | 1.12 |
| **Embedded** | Tight constraints, hardware-coupled | 3.6 | 1.20 |

It came in three precision tiers — Basic, Intermediate, Detailed — and was an enormous step forward in 1981. By the 1990s the assumptions had aged poorly: waterfall lifecycles, procedural-language LOC counting, and a workforce that did not yet reuse components or write object-oriented code.

### COCOMO II — the 2000 re-calibration

**Boehm et al. (2000)** re-fit the model against **161 industrial projects** and modernised the drivers:

- Three rigid modes replaced by **five continuous scale factors** spanning the old organic-to-embedded range.
- New effort multipliers for **reuse (RUSE)**, **documentation (DOCU)**, **personnel continuity (PCON)**, **multi-site development (SITE)**.
- Three **sub-models** of different precision: **Application Composition**, **Early Design**, **Post-Architecture**.
- Every later variant — COSYSMO, COCOTS, COSECMO — is a descendant of this 2000 release.

!!! quote "Why this matters in 2026"
    COCOMO II remains the **de facto reference model** in the software-economics literature. When a textbook, a contract, or a defence acquisition reviewer says "the parametric estimate," they almost always mean COCOMO II. It is not perfect — but it is the *shared vocabulary* of cost estimation, the Ohm's law of the field.

---

## § II. The COCOMO II Equation — A Power-Law With Knobs

Strip away the table-lookup machinery and COCOMO II is a single, beautifully compact equation:

$$
\text{PM} \;=\; A \;\cdot\; \text{Size}^{E} \;\cdot\; \prod_{i=1}^{17} \text{EM}_i
$$

That is the entire model. Three multiplicative pieces. Read aloud: *"Effort equals a calibration constant, times size raised to an exponent, times the product of seventeen effort multipliers."*

### What every symbol means

| Symbol | Meaning | Default / range |
|--------|---------|-----------------|
| $\text{PM}$ | **Person-months** of effort (1 PM $\approx$ 152 person-hours) | output |
| $A$ | **Calibration constant** — sets the overall scale of the curve | default $2.94$ |
| $\text{Size}$ | **KSLOC** (thousands of source lines of code) — or KSLOC equivalent derived from FP | input |
| $E$ | **Exponent** — captures *economies or diseconomies of scale* | typically $1.00$–$1.20$ |
| $B$ | **Base exponent** that sits inside $E$ | default $0.91$ |
| $\text{SF}_j$ | **Five scale factors**, each rated Very Low → Extra High | each $\in [0, 6.20]$ |
| $\text{EM}_i$ | **Seventeen effort multipliers**, each rated Very Low → Extra High | each $\approx 0.7$–$1.4$ |

### The exponent — the only non-trivial piece

The exponent $E$ is not a free knob; it is derived from the five scale factors by

$$
E \;=\; B \;+\; 0.01 \,\cdot\, \sum_{j=1}^{5} \text{SF}_j
$$

With $B = 0.91$ and each $\text{SF}_j \in [0, 6.20]$, the exponent ranges from $0.91$ (perfect scores everywhere) to about $1.226$ (worst-case everywhere). In practice $E$ lands in **$[1.00, 1.20]$** — a slight **diseconomy of scale**: doubling the size *more than doubles* the effort.

!!! warning "Why diseconomy beats economy in software"
    A 200 KSLOC project requires more than 2× the effort of a 100 KSLOC project at the same exponent $E = 1.10$, because $2^{1.10} \approx 2.14$. The extra 7% looks tiny on the page; on a $10M project it is **$700K of communication, integration, and coordination cost** that the linear estimate quietly hid.

### The three pieces, geometrically

```
   PM   =     A      ×     Size^E         ×     ∏ EM
            (scale)        (size + scale       (17 quality
                            factor effect)      knobs)
            ↑              ↑                    ↑
       calibration    economies of scale    everything else:
        constant      from 5 SF              product, platform,
                                              personnel, project
```

If you can recite this picture cold, every COCOMO question on the final reduces to three lookups and an exponent.

---

## § III. Scale Factors — Five Knobs That Set the Exponent

The **five scale factors** modify the *shape* of the size-effort curve. Each is rated on a six-point scale — Very Low, Low, Nominal, High, Very High, Extra High — and translated to a numeric $\text{SF}_j$ between **6.20 (Very Low)** and **0 (Extra High)**. Higher capability → *lower* numeric value → smaller exponent → *less* diseconomy of scale.

| Code | Factor | What it captures | Why it bends the exponent |
|------|--------|------------------|---------------------------|
| **PREC** | Precedentedness | How much this team has built something like this before | Unprecedented work compounds: every new component is a research project |
| **FLEX** | Development flexibility | Latitude to vary requirements, processes, and interfaces | Rigid specs amplify the cost of every late discovery |
| **RESL** | Architecture / risk resolution | Fraction of top risks resolved before construction starts | Unresolved risk turns into rework that scales worse than linearly |
| **TEAM** | Team cohesion | Quality of collaboration across stakeholders, devs, ops | Friction between groups grows super-linearly with project size |
| **PMAT** | Process maturity | CMMI level or equivalent process discipline | Mature processes catch defects when they are still cheap to fix |

### The numeric scale, in full

The 2000 calibration assigns the following values:

| Rating | $\text{SF}_j$ value |
|--------|--------------------:|
| Very Low | 6.20 |
| Low | 4.96 |
| Nominal | 3.72 |
| High | 2.48 |
| Very High | 1.24 |
| Extra High | 0.00 |

A team that scores **Nominal across the board** has $\sum \text{SF}_j = 5 \times 3.72 = 18.60$, giving $E = 0.91 + 0.186 = 1.096$. A team that scores **High across the board** has $\sum \text{SF}_j = 12.40$, $E = 0.91 + 0.124 = 1.034$ — a much flatter curve.

### The leverage of a single scale factor

On a 50-KSLOC project, moving PMAT alone from Nominal (3.72) to High (2.48) drops $E$ from $1.096$ to $1.084$, giving $50^{-0.012} \approx 0.954$ — a **4.6% reduction in effort** from a single rating change. PMAT is one of *five* scale factors. Process maturity is not a virtue signal; it is a multiplicative discount on every project the organisation ever runs.

!!! tip "How to defend a scale-factor rating"
    For the homework, every rating must be defensible with *one sentence of evidence*. "We rate PREC = High because the team has shipped three similar React/FastAPI tools in the past two years" is defensible. "We rate PREC = High because we feel confident" is not.

---

## § IV. Effort Multipliers — Seventeen Multiplicative Knobs

The 17 **effort multipliers** in the Post-Architecture model modify the *vertical* position of the curve. Each is rated Very Low → Extra High and translated to a numeric multiplier roughly in the range **$[0.71, 1.43]$** — a single Nominal rating contributes $\times 1.00$, and the multipliers compound multiplicatively across all 17 dimensions.

### The four groups

| Group | Count | Codes | What it covers |
|-------|------:|-------|----------------|
| **Product** | 5 | RELY · DATA · CPLX · RUSE · DOCU | What we are building and how reliably / reusably |
| **Platform** | 3 | TIME · STOR · PVOL | Where it runs — execution time / storage / volatility |
| **Personnel** | 6 | ACAP · PCAP · PCON · APEX · PLEX · LTEX | Who builds it — analyst & programmer capability, continuity, experience |
| **Project** | 3 | TOOL · SITE · SCED | How we organise — tooling, location, schedule pressure |

### The seventeen, in detail

| Group | Code | Name | What it captures |
|-------|------|------|------------------|
| Product | **RELY** | Required reliability | Cost of a defect — recoverable vs. loss of life |
| Product | **DATA** | Database size | Ratio of test-data bytes to source lines |
| Product | **CPLX** | Product complexity | Control / data / computational / UI complexity |
| Product | **RUSE** | Required reusability | Reuse target: one project → many product lines |
| Product | **DOCU** | Documentation | Under- vs. over-documented relative to lifecycle |
| Platform | **TIME** | Execution-time constraint | % of available CPU consumed |
| Platform | **STOR** | Main-storage constraint | % of available memory consumed |
| Platform | **PVOL** | Platform volatility | Rate of change of the underlying platform |
| Personnel | **ACAP** | Analyst capability | Skill of requirements/design analysts |
| Personnel | **PCAP** | Programmer capability | Skill of implementers |
| Personnel | **PCON** | Personnel continuity | Annual turnover rate |
| Personnel | **APEX** | Applications experience | Years on similar applications |
| Personnel | **PLEX** | Platform experience | Years on the target platform |
| Personnel | **LTEX** | Language & tool experience | Years with the language and toolchain |
| Project | **TOOL** | Use of software tools | Edit/debug only → fully integrated environment |
| Project | **SITE** | Multi-site development | Co-located → globally distributed |
| Project | **SCED** | Required schedule | Compression — under 75% of nominal is expensive |

### Personnel dominates everything else

The four groups are not equal contributors. If you compute the **multiplicative range** of each group at the published ratings:

| Group | Approximate combined range (worst / best) |
|-------|------------------------------------------:|
| Product | $\approx 4.6\times$ |
| Platform | $\approx 2.0\times$ |
| **Personnel** | $\approx \mathbf{14.3\times}$ |
| Project | $\approx 2.4\times$ |

A team of *Very High* analysts and programmers with low turnover and deep platform experience can deliver the same product at **roughly a fifteenth** of the effort a Very Low team would need. No tooling, no process, no architecture decision in this model has comparable leverage.

!!! quote "Boehm's quietest punchline"
    The single largest cost lever in software is not technology. It is **who you hire and whether they stay**. Every other column in the COCOMO II spreadsheet is a rounding error compared to the Personnel group.

This is the empirical backing for the old hiring proverb "an A engineer is worth ten B engineers." COCOMO II does not say ten — it says roughly fifteen.

---

## § V. Early Design vs. Post-Architecture — Matching Precision to Phase

COCOMO II is **not one model** but three nested sub-models of increasing precision, each meant for a different lifecycle phase. The estimator's job is to pick the right one — and refuse to fake precision the phase does not support.

| Sub-model | Used when | Inputs | Realistic precision |
|-----------|-----------|--------|---------------------|
| **Application Composition** | Pre-spec, conceptual idea | **Object points** (very rough) | $\pm 50\%$ / $\times 2$ |
| **Early Design** | Spec exists, no architecture | FP or KSLOC + **7 aggregated EMs** | $\pm 25\%$ / $\times 1.5$ |
| **Post-Architecture** | Architecture exists | KSLOC + **all 17 EMs** + 5 SFs | $\pm 10\%$ / $\pm 20\%$ |

### The Early Design seven

Early Design collapses the 17 Post-Architecture multipliers into seven aggregated ones — each a geometric mean of the related Post-Architecture EMs:

| Early Design EM | Aggregated from |
|-----------------|-----------------|
| **PERS** (Personnel) | ACAP · PCAP · PCON |
| **RCPX** (Product reliability/complexity) | RELY · DATA · CPLX · DOCU |
| **RUSE** (Reusability) | RUSE |
| **PDIF** (Platform difficulty) | TIME · STOR · PVOL |
| **PREX** (Personnel experience) | APEX · PLEX · LTEX |
| **FCIL** (Facilities) | TOOL · SITE |
| **SCED** (Schedule) | SCED |

The 5 scale factors are identical between Early Design and Post-Architecture.

!!! warning "The precision trap"
    A common error is to use Post-Architecture during requirements gathering. The model will *give you a number* — to two decimal places — but that number is fiction. Use **Application Composition** before specs, **Early Design** between spec and architecture, **Post-Architecture** only after the architecture is real enough to estimate sub-system sizes. **The rubric for HW9 rewards a defensible Early Design or Post-Architecture estimate equally.**

---

## § VI. Worked Example — FP → KSLOC → PM → \$

Yesterday in Lecture 8 you produced **53 Adjusted Function Points** for a small internal classroom-booking tool. Today we walk that number all the way to a defensible dollar budget.

### Step 1 — Convert AFP to KSLOC

Each language has a published **language conversion factor** (lines of code per function point). The values used in COCOMO II / the SPR catalogue:

| Language | LOC / FP |
|----------|---------:|
| C | 128 |
| C++ | 55 |
| **Java** | **53** |
| **Python** | **42** |
| **TypeScript / React** | **48** |
| Ruby | 46 |
| Visual Basic | 32 |
| **SQL** | **21** |
| Spreadsheet | 6 |

The classroom-booking tool is in **Python**:

$$
\text{KSLOC} \;=\; \text{AFP} \;\times\; \text{LOC/FP} \;\div\; 1000
\;=\; 53 \;\times\; 42 \;\div\; 1000
\;\approx\; \mathbf{2.2\ \text{KSLOC}}
$$

### Step 2 — Pick a scale-factor profile

For a small in-house tool built by a student team, a defensible *nominal* profile is:

| SF | Rating | Value |
|----|--------|------:|
| PREC | Nominal | 3.72 |
| FLEX | High | 2.48 |
| RESL | Nominal | 3.72 |
| TEAM | High | 2.48 |
| PMAT | Low | 4.96 |
| **Sum** | | **17.36** |

$E_{\text{nominal}} = 0.91 + 0.01 \times 17.36 = 1.0936 \approx 1.10$.

### Step 3 — Three scenarios

We hold size constant and vary the scale-factor and EM ratings to span optimistic / nominal / pessimistic. Cost rate is **\$18K / person-month**, a defensible fully-loaded rate for the United States in 2026 including overhead and benefits.

| Scenario | $E$ | $\prod \text{EM}$ | $\text{PM}$ | \$ @ \$18K/PM |
|----------|----:|------------------:|------------:|--------------:|
| **Optimistic** | 1.03 | 0.70 | **4.7** | **\$85K** |
| **Nominal** | 1.10 | 1.00 | **6.9** | **\$125K** |
| **Pessimistic** | 1.17 | 1.40 | **10.3** | **\$185K** |

Worked nominal computation:

$$
\text{PM}_{\text{nom}} \;=\; 2.94 \;\cdot\; 2.2^{1.10} \;\cdot\; 1.00
\;\approx\; 2.94 \;\cdot\; 2.366
\;\approx\; \mathbf{6.96\ \text{PM}}
$$

At \$18K/PM, **6.96 PM × \$18K ≈ \$125K**.

### Step 4 — Read the spread, not just the middle

| Headline you could report | What it hides |
|---------------------------|---------------|
| "It will cost \$125K." | The 48% chance the real number is \$185K. |
| "It might cost \$85K." | The 50% chance you are funding only half the project. |
| **"\$125K nominal, range \$85K–\$185K. We will re-estimate after architecture in week 4."** | Honest engineering. |

The pessimistic estimate is **2.2× the optimistic**. Reporting only the nominal number hides a real decision: funding at the optimistic figure *guarantees* a budget overrun if the pessimistic case materialises, and budget overruns kill projects more often than scope creep does.

!!! tip "The estimator's vow"
    Always report a **three-point estimate** (optimistic, nominal, pessimistic), and re-state it after every major phase boundary. A single point estimate is not a budget — it is a wish.

---

## § VII. Calibration — Why the National Model Is the Starting Line, Not the Finish

COCOMO II's published constants were fit to **161 industrial projects in the late 1990s**. Your organisation is not in that dataset. Your team's productivity, language, tooling, and customer profile almost certainly do not match the median project Boehm measured. Therefore:

!!! danger "The calibration imperative"
    **Never trust the published constants in production.** Re-calibrate $A$ (and ideally $B$) against your organisation's own historical projects before treating any COCOMO II estimate as a planning number. A model that has never seen your data is decoration, not estimation.

### The local-calibration recipe

1. Collect **at least 5–10 completed projects** with known actual PM and known KSLOC.
2. Apply COCOMO II with the *published* constants and the *retrospective* SF / EM ratings to each one to produce a model PM.
3. Compute the ratio $r = \text{actual PM} / \text{model PM}$ for each.
4. Multiply $A$ by the geometric mean of the $r$ values. Re-fit if necessary.

Organisations that do this rigorously report estimates within $\pm 20\%$ of actuals. Organisations that don't routinely come in at 2–3× over budget — and blame the model.

### What invalidates a prior calibration

Any of the following means your last calibration is stale: a new dominant language (Python → Rust); a new dominant team profile (junior → senior majority); a material change in tooling — CI/CD, infrastructure-as-code, **and especially AI-assisted coding tools (see § VIII)**; or a change in customer profile that shifts RELY and DOCU across every project. Calibration is not a one-time event; it is a **standing process**.

---

## § VIII. Class Discussion — How Would You Adjust COCOMO II for AI-Assisted Development?

> **Pairs, 4 minutes. Then 6 minutes of open discussion.**
>
> *No agreed answer exists in 2026.* COCOMO II was calibrated long before GitHub Copilot, Claude Code, or Cursor existed. Your job is not to produce the right answer — there isn't one yet — it is to **surface where the model's seams are**.

### Three concrete prompts

1. **Which effort multiplier would you alter, and by how much?** The candidate EMs are:
    - **TOOL** — Use of software tools. AI-assisted IDEs are a tool. Should the "Very High" rating be re-scoped to include AI assistants — and a new "Extra High" added above it?
    - **APEX** — Applications experience. Does an AI assistant act as a *prosthetic* for missing experience, effectively raising the APEX rating of a junior team?
    - **PLEX** — Platform experience. Same question for the target platform.

2. **Would you adjust the constant $A$?** The published default is $2.94$. Some empirical work suggests AI assistance reduces total effort by 10–30% on common tasks. That implies $A_{\text{AI}} \approx 0.70 \cdot 2.94 \approx 2.06$ at the high end of that range. But effort reduction is **not uniform across projects** — see prompt 3.

3. **The empirical paradox to weigh.** Recent studies (METR 2025, GitHub 2024) report that:
    - **Junior developers** gain **+10% to +30% productivity** from AI assistants — the assistant fills in gaps in their knowledge.
    - **Senior developers** on complex unfamiliar codebases lose **−19% productivity** — the verification overhead of reviewing AI output exceeds the savings from generating it.

    Where does this hit the COCOMO II equation? It cannot be a single multiplier — it interacts with **APEX**, **PLEX**, and **CPLX** simultaneously. **Should the multipliers themselves become functions of each other, or does the entire post-architecture model need a new "AI assistance" dimension?**

!!! note "Foreshadow — Lecture 12"
    We return to this in depth in **Lecture 12: AI Estimation & Productivity**, where we examine the METR data carefully, the GitHub randomised study, and the proposed *COCOMO-AI* extensions in the recent literature. Today's discussion is meant to *frame the problem*; that lecture will *quantify it*.

---

## Homework 9 — Apply COCOMO II to Your Project

**Due: Saturday 2026-06-13, before Lecture 11. Late: −10% per day.**

1. **Size.** Use your AFP count from Homework 8 (or recompute if it was unreliable). Convert to **KSLOC** using the appropriate language conversion factor for your project's primary language. State the language and the factor you used.
2. **Scale factors.** Rate your project on all **five** scale factors (PREC, FLEX, RESL, TEAM, PMAT). For each, write **one sentence of evidence** justifying the rating. Compute $E$ and show your arithmetic.
3. **Effort multipliers.** Rate your project on all **17** Post-Architecture effort multipliers in their four groups. For each, write **one sentence of evidence**. Compute $\prod \text{EM}$ and show your arithmetic.
4. **Three-point estimate.** Compute PM under **optimistic, nominal, and pessimistic** scenarios. Convert each to dollars using a PM rate you can defend (state the rate and your rationale — geographic location, role mix, fully-loaded vs. salary-only).
5. **Comparison.** Compare your COCOMO II estimate with your FP-only estimate from Homework 8. Which is more credible? *Why?* Half a page of argument.
6. **Reflection (optional, +5%).** Pick one of the three discussion prompts in § VIII and write 200 words proposing a concrete calibration of COCOMO II for AI-assisted development in your project's context.

**Submission.** A single PDF (your spreadsheet export plus the written sections) committed to the course repository at `submissions/HW9/<your-name>.pdf`.

**Office hours.** Friday 10:00–11:30, before Lecture 10.

---

## Key Vocabulary — Quick Reference

| Term | Plain-language meaning |
|------|------------------------|
| COCOMO 81 | Boehm's original parametric model, 63 projects, three modes (organic / semi-detached / embedded). |
| COCOMO II | The 2000 re-calibration on 161 projects, three sub-models, modern cost drivers. |
| Person-month (PM) | The unit of effort: roughly 152 person-hours of productive work. |
| KSLOC | Thousands of source lines of code. |
| Scale factor (SF) | One of five knobs that modify the exponent $E$, bending economies/diseconomies of scale. |
| Effort multiplier (EM) | One of 17 knobs that multiply the effort estimate (Post-Architecture model). |
| Diseconomy of scale | $E > 1$: doubling the size more than doubles the effort. The default for software. |
| Application Composition | Sub-model for pre-spec rough estimates from object points. |
| Early Design | Sub-model with 7 aggregated EMs, for use after spec but before architecture. |
| Post-Architecture | The full 17-EM sub-model, used after architecture is defined. |
| Calibration | Adjusting $A$ (and ideally $B$) against your organisation's historical projects. |
| Language conversion factor | LOC per FP — multiplies AFP into a size estimate. |
| Three-point estimate | Reporting optimistic + nominal + pessimistic — never a single number. |
| PM rate | Fully-loaded cost per person-month, used to convert PM to dollars. |

---

## Further Reading

- **Boehm, B. W.** (1981). *Software Engineering Economics*. Prentice Hall — Chapters 8–11 cover the original COCOMO 81 model in full.
- **Boehm, B. W., et al.** (2000). *Software Cost Estimation with COCOMO II*. Prentice Hall — the canonical reference.
- **USC Center for Systems and Software Engineering** — model definitions, the COCOMO II calculator, downloadable calibration datasets.
- **Jones, C.** *Applied Software Measurement* — extensive language-conversion-factor tables.
- **Galorath & Evans.** *Software Sizing, Estimation, and Risk Management* — alternative parametric models (SEER-SEM, SLIM, PRICE-S).
- *(Forward pointer)* **Lecture 10 — Quality & Maintenance Economics** and **Lecture 12 — AI Estimation & Productivity** build directly on today's outputs.

---

## What Today Bought You

Three things you now own that you did not at 16:20:

1. **A vocabulary.** You can speak the language of parametric estimation — scale factors, effort multipliers, sub-models, calibration — in any planning meeting where COCOMO is on the table.
2. **A pipeline.** You can take a number of any kind (FP, AFP, KSLOC) and convert it through COCOMO II into a defensible three-point dollar estimate. The pipeline is mechanical once the inputs are honest.
3. **A scepticism.** You know that the national model with no local calibration is decoration, not estimation — and you know which lever (the Personnel group) moves the answer most. You will not be fooled by a confident estimator who skips both.

Tomorrow we use these numbers as the *input* to maintenance and defect-cost models. The effort estimate you produce in Homework 9 is the first column of the next lecture's spreadsheet.

---

*Prepared by Dr. Zhijiang Chen — Frostburg State University, Summer 2026.*
