---
title: "Lecture 2 — Software Cost Concepts & Lifecycle Cost"
author: Dr. Zhijiang Chen
date: 2026-06-04
session: 2
week: 14
room: YF302
---

# Lecture 2 — Software Cost Concepts & Lifecycle Cost

!!! info "Session Info"
    **Date:** Thursday, 2026-06-04 &nbsp;·&nbsp; **Time:** 16:20–18:10 &nbsp;·&nbsp; **Room:** YF302
    **Instructor:** Dr. Zhijiang Chen

[**▶ Open the lecture slides — 110-minute web deck**](slides/lecture-02.html){ .md-button .md-button--primary target="_blank" }

!!! tip "Using the slide deck"
    The slides are a self-contained web presentation (Reveal.js, no install). Keyboard shortcuts: **F** for full-screen, **S** for speaker view, **Esc** for slide overview, **arrow keys** to navigate. Designed for a single 110-minute session.

---

## Learning Objectives

By the end of this lecture, you should be able to:

1. Distinguish **fixed vs. variable**, **direct vs. indirect**, **recurring vs. non-recurring**, and **capital vs. operating** costs in software projects.
2. Apply the four **behavioural cost concepts** — opportunity, sunk, marginal, and average — to real engineering decisions.
3. State and apply the **decision-relevance rule**: count only costs that are *future* and *differential*.
4. Describe the **five lifecycle phases** of a software system and explain Boehm's "iceberg" finding that maintenance dominates lifetime cost.
5. Construct a **Total Cost of Ownership (TCO)** view that crosses the launch line and includes operate-and-maintain costs.
6. Identify modern AI-era cost categories — **tokens, embeddings, vector storage, GPU hours, human verification, evaluation** — that did not appear in the classical SEE canon.

---

## Lecture Map — Six Movements, One Ledger

By the end of today you should be able to name every line in a software cost ledger — classical or modern — and tell which lines belong in a *decision* and which do not.

| § | Topic | Minutes |
|---|-------|--------:|
| I | Why cost concepts are a decision lens, not bookkeeping | 10 |
| II | The taxonomy: fixed/variable, direct/indirect, recurring/non-recurring, capex/opex | 20 |
| III | Behavioural costs: opportunity, sunk, marginal, average — and the decision-relevance rule | 20 |
| IV | Lifecycle cost — and Boehm's iceberg | 15 |
| V | Total Cost of Ownership — a worked TCO and in-class exercise | 25 |
| VI | The modern ledger: cloud, API, tokens, GPUs, evaluation | 10 |
| — | Discussion, homework, Q&A | 10 |

---

## § I. A Cost Concept Is a *Lens*, Not a Line in a Spreadsheet

The central claim of this lecture: **a cost concept is not bookkeeping — it is the question you ask money about.** The same $100 cloud bill can be a *fixed cost*, a *sunk cost*, an *opportunity cost*, or an *opex line item* depending on the decision in front of you. Engineers who lack the vocabulary make decisions on the wrong cost.

### One $100 line — four different stories

Your team pays **$100/month** for a code-search SaaS. Depending on the question, that single line item changes character:

| The question we're asking | What kind of cost is the $100? | Decision consequence |
|---------------------------|-------------------------------|----------------------|
| Should we keep using it? | A **recurring**, **variable-with-headcount** cost | Compare to value delivered each month. |
| Should we cancel because we paid annually? | The pre-paid year is a **sunk cost**. | Ignore it — decide on remaining months only. |
| Should we hire a contractor to replace it? | The $100 is the **opportunity cost** of replacement. | Build alternative only if it beats $100 + risk. |
| How should accounting record it? | An **operating expense** (opex), not capex. | Affects margin in the quarter it is paid. |

The number is identical in every row. **Only the question changes.** This is why the same software project can be "expensive" or "cheap" depending on who's asking — the CFO, the engineering manager, the customer, and the founder all ask different questions of the same numbers.

!!! quote "Working principle for this course"
    A cost concept is not a number — it is the *question* you ask money about.

---

## § II. The Taxonomy — Four Axes for Every Software Dollar

Every cost in a software project has a coordinate on **four independent axes**. A junior engineer typically knows none of these; a senior engineer can place any line item in seconds. That fluency is what this section builds.

### Axis 1 — Fixed vs. Variable (does the cost move with volume?)

| | **Fixed** | **Variable** |
|--|----------|--------------|
| Definition | Does not change as volume changes — at least within the *relevant range*. | Scales (often linearly) with units of output, users, or load. |
| Software examples | Reserved server lease · team salaries · annual licence | Per-request inference · per-GB egress · token spend · seat-based SaaS |
| Bill arrives | Whether or not anyone uses the system | Only when someone uses the system |

**Why this matters.** At zero volume, you still pay the fixed cost. "Fixed" is fixed *only inside a range* — a server is fixed until you outgrow it; a salary is fixed until you hire one more engineer. Fixed costs are *sticky*: a startup that "shut down" a service to save money kept paying $40K/month for the reserved Kubernetes cluster because no one owned the bill.

The total cost of any system is **`Total = Fixed + Variable × Volume`**. Two systems with identical totals at one volume can diverge dramatically at another — which is why "what does it cost?" is not a complete question without "at what volume?"

### Axis 2 — Direct vs. Indirect (can we trace it?)

A **direct** cost can be traced to one project or product without allocation. An **indirect** cost serves many and must be split.

| Type | Examples |
|------|----------|
| **Direct** | Salary of an engineer working only on Project X; the cloud account dedicated to Project X. |
| **Indirect** | HR, IT support, the office lease, the CI runner pool; the CTO's time — shared across every project. |

Indirect costs are often called **overhead** and applied as a percentage on top of direct cost. Students often miss that the CTO's time is a *real* cost: when the CTO spends two days on your project, the company is paying for it; it just doesn't appear on your project's cost line. This is why companies allocate overhead — to make sure indirect costs are recovered.

### Axis 3 — Recurring vs. Non-recurring (will it come back?)

A **non-recurring** cost is paid once. A **recurring** cost returns every month, quarter, or year for the life of the system.

| Type | Examples |
|------|----------|
| **Non-recurring** | Initial data migration; architecture spike; one-time hardware purchase; build setup. |
| **Recurring** | Monthly SaaS bill; per-token API cost; on-call rota; annual compliance audit. |

Engineers under-count recurring; finance under-counts non-recurring. Both biases bite. Canonical anecdote: a team that celebrated shipping a feature "for $40K" and then paid $25K/year forever in cloud costs they hadn't planned. By year 3, the *recurring* cost had already eclipsed the build cost. **Recurring is the silent killer.**

### Axis 4 — Capital vs. Operating (how is it accounted?)

| | **Capital Expenditure (CapEx)** | **Operating Expenditure (OpEx)** |
|--|--------------------------------|----------------------------------|
| What it is | Money spent to acquire a long-lived asset | Money spent to run the business this period |
| Software examples | Server purchase · perpetual licence · self-built platform | Cloud bill · SaaS subscription · per-token API spend |
| Accounting | **Capitalised** and depreciated over useful life | Expensed in the period incurred — hits margin directly |
| Cash flow | Big up-front outflow; smaller drag thereafter | Smaller, steady, predictable outflow |
| Strategic effect | Builds an owned asset; cheaper at scale; less flexible | No asset on the balance sheet; flexible; cheaper at low scale |

The 2010s **"shift from CapEx to OpEx"** is exactly the move from data centres to the cloud — same workload, different accounting, different freedom to scale. Many AI workloads today face the same fork: do we buy GPUs (capex) or rent them (opex)? Lecture 13 returns to this in token economics.

---

## § III. Behavioural Costs — Four Concepts, Four Famous Mistakes

The previous section was *what kind* of cost. This section is *how the cost behaves in a decision*. It is a smaller set — only four concepts — but each one is responsible for a famous engineering mistake.

### 1. Opportunity Cost — The Road Not Taken

The **opportunity cost** of a choice is the value of the *next-best alternative you gave up* by choosing what you chose. It is almost never on an invoice — so it is almost never in the spreadsheet — so it is almost never in the decision.

> **true cost = cash cost + opportunity cost**

**Worked example.** Your three best engineers spend Q3 rewriting the build system.

- Cash cost of the rewrite: **$0** — they are salaried.
- Opportunity cost: **the customer feature they did *not* ship** in those three months.
- If that feature would have generated $300K, the rewrite cost **$300K**, not $0.

Engineers who say *"internal work is free"* are silently making the largest economic error in the field. "Free" never means free; it means the cost is not on an invoice. Senior engineers learn to see invisible costs — that is part of what makes them senior.

### 2. Sunk Cost — Money Already Spent

A **sunk cost** has already been incurred and cannot be recovered, regardless of which option you now choose. It is therefore **irrelevant** to the decision in front of you. Rationally, you must ignore it. In practice — especially when the people deciding are the same people who championed the original investment — humans cannot ignore it. This is the **sunk-cost fallacy**.

**Three software sunk-cost traps:**

- A six-month migration is 80% done; the new path is worse than where we started — but *"we've come too far to turn back."*
- An annual SaaS subscription was paid in January; we keep forcing the team to use a tool nobody likes *"to get our money's worth."*
- Three engineers wrote a custom framework no one else uses; we maintain it forever rather than admitting it was a wrong turn.

**The cure is a literal mental phrase:** *"What would we decide if we were arriving today, with no history?"* If the answer is "we would not choose this path," then the sunk cost is dragging you. Walk away.

### 3. Marginal Cost vs. Average Cost

| | **Marginal cost** | **Average cost** |
|--|-------------------|------------------|
| Definition | The cost of *one more* unit — the next request, inference, user, feature | Total cost *divided by* total units |
| Used for | Operational decisions — "should we accept one more API request?" | Reporting, pricing benchmarks — "what does our service cost per user?" |
| Why it matters | Tells you whether the next unit is worth selling | Easy to compute, easy to mislead with |

In software, **most decisions are marginal, not average**. Serverless APIs have near-zero marginal cost at low scale, then a sharp climb when you hit per-request pricing tiers. LLM inference has marginal cost dominated by *tokens* — easy to compute, often ignored. The classical "U-shaped" cost curves cross at the *optimal scale* — the volume at which marginal cost equals average cost. Lecture 13 returns to this.

### 4. The Decision-Relevance Rule

The vocabulary above is useful only because of one rule. **In any decision, count only the *future* and the *different*.**

| Count these — **decision-relevant** | Ignore these — **irrelevant to this decision** |
|-------------------------------------|------------------------------------------------|
| **Future** costs that have not yet been incurred | **Sunk** costs — already spent, unrecoverable |
| **Differential** costs that differ between alternatives | **Common** costs that are identical across alternatives |
| **Opportunity** costs — value of what you give up | **Allocated overhead** that will be paid either way |
| **Cash-flow** consequences, including risk | **Accounting artefacts** with no cash consequence |

If you internalise nothing else today, internalise this. Most bad project decisions are rule violations — someone counted a sunk cost, or someone ignored an opportunity cost, or someone compared total cost when they should have compared *incremental* cost.

---

## § IV. Lifecycle Cost — and Boehm's Iceberg

Software has a life. Most of its cost lives *after* the launch party.

### The five phases

| Phase | What happens | Typical cost character |
|-------|--------------|------------------------|
| **I. Acquisition** | Specification, vendor selection, contract, procurement | Non-recurring, direct |
| **II. Development** | Design, build, test, deploy. The phase engineers see most | Non-recurring, direct, mostly labour |
| **III. Operation** | Run-time hosting, support, on-call, observability | Recurring, mixed direct/indirect |
| **IV. Maintenance** | Bug fixes, evolution, dependency updates, security patches | Recurring, often the largest line |
| **V. Retirement** | Data migration, sunsetting, archival, decommissioning | Non-recurring, often forgotten |

### Boehm's iceberg

Most engineering culture, most planning meetings, and most cost spreadsheets concern themselves with the *visible 20%* — design and build. Boehm (1981) found that **60–80% of lifetime cost lives below the waterline** — in operation, maintenance, and evolution. Some studies (Lientz & Swanson) put maintenance at 60%; others as high as 90% for long-lived enterprise systems. The consistent story: **development is the tip, not the body.**

```
   ▲  Development  (~ 20%)     ← above the waterline
   │
═══╪═══════════════ Launch ═══════════════════════════
   │
   │  Operation + Maintenance  ( ≈ 80% )
   │    + Evolution
   │    + Retirement
   ▼
```

**Implication for decisions.** Any decision made during build that shifts cost into ops or maintenance can wipe out apparent build savings. A choice that lowers build cost 30% but raises maintenance cost 30% almost always makes the project *more* expensive. This is the rationale for **TCO thinking** — the next section.

---

## § V. Total Cost of Ownership

**Total Cost of Ownership (TCO)** is the only honest way to *add up* a software bill. It puts the taxonomy, the behavioural concepts, and the lifecycle into one practical artefact: a table that totals every cost across every phase. If your cost model has only the build, you have a build estimate — not a TCO.

### Worked TCO — Internal AI-assisted code-review tool, 60 engineers, 5 years

The figures below are *order-of-magnitude planning numbers*, not quotes. The point is the *shape* of the spreadsheet, not the precision of any one cell.

| Year | Build / migration | Ops · on-call | SaaS licence | Tokens (API) | Vector DB · infra | Training / eval | **Year total** |
|-----:|------------------:|--------------:|-------------:|-------------:|------------------:|-----------------:|--------------:|
| **Yr 0 — Setup** | $42K (build) + $18K (integration) | — | — | — | — | $15K | **$ 75,000** |
| **Yr 1** | — | $15K | $36K | $28K | $18K | $10K | **$ 107,000** |
| **Yr 2** | — | $15K | $36K | $31K | $18K | $10K | **$ 110,000** |
| **Yr 3** | — | $18K | $36K | $34K | $18K | $10K | **$ 116,000** |
| **Yr 4** | — | $18K | $36K | $37K | $18K | $12K | **$ 121,000** |
| **Yr 5 — Retire** | $8K (archive) + $25K (data migration) | — | $18K | $20K | — | — | **$ 71,000** |
| **5-year TCO** | — | — | — | — | — | — | **≈ $ 600,000** |

**Three things to notice.**

1. **Year 0 (the "build") is dwarfed by every operating year.** The build is *12%* of the 5-year TCO. Everything below the launch waterline — operations, tokens, infrastructure — is the other *88%*.
2. **Tokens grow every year.** Token spend — a line that *did not exist in Boehm's 1981 canon* — grows as the team uses the tool more. It is a recurring, variable-with-usage cost.
3. **Retirement spikes year 5.** Data migration off the system, archival, and decommissioning routinely get forgotten in cost models. They are non-recurring but often substantial.

### In-class exercise (Part V — 15 minutes)

In pairs, pick a small internal tool you have used. List every cost line that *should* be in its 5-year TCO. Don't compute amounts — focus on **completeness**. After 10 minutes, three pairs will share the cost line *most teams forget*.

**Worksheet — cost lines to consider** (don't restrict yourself to these):

| Build & setup | Operate & maintain | AI-era & retire |
|---------------|--------------------|--------------------|
| Engineer time | SaaS / licence fees | LLM tokens (input / output / cache) |
| Architecture / spike | Cloud / infra | Vector DB · embeddings |
| Initial data migration | On-call rota | GPU hours (if any) |
| Security review | Observability tooling | Human verification time |
| UX research | Annual security audit | Retirement / archive |

For each line you list, mark it on three axes: **fixed / variable · recurring / one-time · direct / indirect.** Then circle anything that would be *different* between two realistic alternatives — those are the lines that will drive the decision.

---

## § VI. The Modern Ledger — Lines Boehm Could Not Have Imagined

The taxonomy is timeless; the ledger is not. The 2026 software bill contains lines no 1981 spreadsheet had. Today we name them; **Lectures 12 and 13 will quantify them in detail**.

### The AI-era ledger — eight modern cost lines

| Line | What it pays for | Behaviour | Order of magnitude (2026) |
|------|------------------|-----------|---------------------------|
| Per-seat licence | An AI coding assistant or copilot per developer | Fixed · recurring | $20–60 / dev / mo |
| **Input tokens** *(new)* | Prompt + context sent into the model | Variable · recurring | $3–15 / 1M tok |
| **Output tokens** *(new)* | Model-generated text — usually 3–5× input price | Variable · recurring | $15–75 / 1M tok |
| **Cache reads** *(new)* | Repeated context served from a cheaper cache tier | Variable · recurring | ~10% of input |
| **Embeddings & vector DB** *(new)* | Storing and querying semantic vectors for RAG | Fixed + variable | $0.10–0.50 / 1K vec / mo |
| **GPU hours** *(new)* | Fine-tuning, eval, custom inference | Variable · spiky | $2–8 / GPU-hr |
| **Human verification** *(new)* | Engineer time reviewing AI output | Variable · hidden | 5–25% of dev time |
| Compliance / eval | Red-team, safety review, hallucination audits | Fixed · recurring | $10K–100K / yr |

**Five of the eight modern lines are new** — they have no place in Boehm (1981). Two non-obvious ones deserve highlighting:

- **Cache reads.** Most teams do not realise that prompt caching can cut input cost by **~90%** in well-tuned systems. This single design choice is often the difference between an AI feature being profitable or not.
- **Human verification.** The most under-counted modern cost. Every AI output reviewed by an engineer costs engineer time. Treat it as part of the *per-task* cost. Lecture 13 has a model.

Any AI cost model that omits these lines is **wrong by construction**.

### Three planning numbers to carry into Lecture 13

| Number | Meaning |
|-------:|---------|
| **88 %** | Share of our worked TCO that sits *below* the launch waterline. |
| **5 of 8** | Modern ledger lines that *did not exist* in Boehm (1981). |
| **90 %** | Peak input-cost saving from *prompt caching*, in well-tuned systems. |

Together, these three numbers explain why naive AI cost estimates are routinely off by **5–10×**.

---

## Class Discussion Prompts

Open floor, ~10 minutes.

1. Name a software cost in your own life or job that you suspect is *under-counted*. Which axis of the taxonomy does it sit on, and why is it invisible?
2. Describe a software decision you have seen made on the *wrong* cost — sunk, common, allocated, or accounting-only. What did the better cost lens look like?
3. Pick one line from the AI-era ledger. Is it *going up* or *down* in your organisation over the next two years — and what would change that direction?

---

## Homework 2 — Build a Real TCO

**Due: Saturday 2026-06-06, before Lecture 4. Late: −10% per day.**

1. Choose a software product you know well — a tool you use, a system you maintain, or a small product idea. Build a **five-year Total Cost of Ownership spreadsheet**: every line, every year. Include the modern AI-era lines where applicable.
2. Identify the **three largest cost categories** over the five years, and write a one-paragraph explanation of *why* — referring to at least two terms from today's vocabulary (e.g. *recurring · opex · variable · opportunity*).
3. Mark each line as **decision-relevant** or **not**, assuming the decision is *"keep using this tool or replace it next year?"*

**Submission.** A single PDF (your spreadsheet export + the paragraph) committed to the course repository at `submissions/HW2/<your-name>.pdf`.

**Office hours.** Tomorrow, 10:00–11:30, before Lecture 3.

---

## Key Vocabulary — Quick Reference

| Term | Plain-language meaning |
|------|------------------------|
| Fixed cost | Does not change as volume changes (within a relevant range). |
| Variable cost | Scales with units of output or usage. |
| Direct cost | Traceable to one project without allocation. |
| Indirect cost (overhead) | Serves many projects; must be allocated. |
| Recurring cost | Returns every period for the life of the system. |
| Non-recurring cost | Paid once. |
| CapEx | Spent to acquire a long-lived asset; capitalised and depreciated. |
| OpEx | Spent to run the business this period; expensed immediately. |
| Opportunity cost | Value of the next-best alternative you gave up. |
| Sunk cost | Already spent, unrecoverable — *irrelevant* to future decisions. |
| Marginal cost | Cost of the *next* unit. |
| Average cost | Total cost / total units. |
| Lifecycle cost | Sum across acquisition, development, operation, maintenance, retirement. |
| TCO | Lifecycle cost expressed as a full, year-by-year ledger. |
| Decision-relevance rule | Count only future and differential costs. |

---

## References

- **Boehm, B. W.** (1981). *Software Engineering Economics*. Prentice Hall — **Chapter 3** (cost concepts), Chapter 4 (lifecycle).
- **Park, C. S.** *Contemporary Engineering Economics* — chapter on cost concepts and cost behaviour.
- **Lientz, B. P., & Swanson, E. B.** (1980). *Software Maintenance Management* — origin of the maintenance-dominates-lifetime-cost finding.
- **Erlikh, L.** (2000). "Leveraging legacy system dollars for e-business." *IT Professional* — modern restatement of the iceberg.
- *(Forward pointer)* **Lecture 12 — AI Estimation & Productivity** and **Lecture 13 — AI Token Economics** will quantify the new ledger lines.

---

*Prepared by Dr. Zhijiang Chen — Frostburg State University, Summer 2026.*
