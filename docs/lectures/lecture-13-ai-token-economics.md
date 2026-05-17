---
title: "Lecture 13 — AI Topic II: Token Economics & Project ROI"
author: Dr. Zhijiang Chen
date: 2026-06-15
session: 13
week: 16
room: YF108
---

# Lecture 13 — AI Topic II: Token Economics & Project ROI

!!! info "Session Info"
    **Date:** Monday, 2026-06-15 &nbsp;·&nbsp; **Time:** 16:20–18:10 &nbsp;·&nbsp; **Room:** YF108
    **Instructor:** Dr. Zhijiang Chen &nbsp;·&nbsp; **Session 13 of 16 — last content lecture**

[**▶ Open the lecture slides — 110-minute web deck**](slides/lecture-13.html){ .md-button .md-button--primary target="_blank" }

!!! tip "Using the slide deck"
    The slides are a self-contained web presentation (Reveal.js, no install). Keyboard shortcuts: **F** for full-screen, **S** for speaker view, **Esc** for slide overview, **arrow keys** to navigate. Designed for a single 110-minute session. Lectures 14–15 are student presentations; Lecture 16 is the final exam — **today is the last new material**.

---

## Learning Objectives

By the end of this lecture, you should be able to:

1. **Decompose** any LLM bill into its three token components — **input, output, cached** — and explain why output dominates and cache wins.
2. **Read the 2026 pricing landscape** for the major frontier models and choose a model *by task tier*, not by brand.
3. **Quantify agent fan-out**: estimate the 6–13 LLM calls behind a single user request and the $0.20–$8 per-task cost band that results.
4. **Apply the three-tier caching stack** — prompt cache, semantic cache, KV cache — and estimate hit rate by workload pattern.
5. **Build a payback-period analysis** for an AI initiative using industry-median benchmarks (4–14 months by use case).
6. **Synthesize the course** into a single decision framework: when to apply FP, COCOMO II, NPV/IRR, sensitivity, and AI cost modelling — and recognise the structure of next week's final exam.

---

## Lecture Map — Six Movements, One Bill, One Course Recap

Today is the last new content of the semester. Sections I–V give you the missing piece — the modern AI ledger expressed as **dollars per task**, not dollars per million tokens. Section VI wraps the whole course in five lines and previews the final exam.

| § | Topic | Minutes |
|---|-------|--------:|
| I | The token cost stack — input / output / cached | 15 |
| II | The 2026 LLM pricing landscape | 10 |
| III | Agent cost multipliers — 3–10× a chatbot | 15 |
| IV | Caching strategies — where 40–90% savings hide | 20 |
| — | *Discussion: which cache wins for your project?* | 10 |
| V | Payback periods by industry — 2026 benchmarks | 15 |
| VI | Course wrap-up & final-exam preview | 20 |
| — | HW13, Q&A | 5 |

!!! note "Prerequisite from Lecture 2 and Lecture 12"
    This lecture assumes you remember the **AI-era ledger lines** from Lecture 2 (§ VI) and the **productivity-adjusted COCOMO** from Lecture 12. Today we put dollars on those lines and connect them back to the NPV / payback machinery from Lectures 5–6.

---

## § I. The Token Cost Stack — Three Components, Three Prices

The cost of an LLM call is not a single number. It is a **sum of three priced quantities**, each measured per million tokens, each behaving differently as your system scales. Engineers who quote "we use Sonnet, it's $3/M" are quoting one of three numbers — usually the cheapest one — and then are surprised at the bill.

### The three token types

| Type | What it is | Relative cost (2026) | Why it costs what it costs |
|------|------------|---------------------:|----------------------------|
| **Input tokens** | Everything you send to the model — system prompt, user message, retrieved context, tool definitions. | **1× base** | The model must *read* every token. Cost scales linearly with context length. |
| **Output tokens** | Everything the model generates — response text, tool calls, reasoning traces. | **3–5× input** | *Generation* is inherently more compute-intensive than reading — each output token requires a fresh forward pass. |
| **Cached input tokens** | Input tokens already seen, served from the provider's prompt cache. | **~0.1× input (≈ 90 % cheaper)** | The expensive KV-state computation has already been done; the provider replays it. |

### Why output is 3–5× input — a one-paragraph intuition

When the model **reads** input, it processes the whole sequence *in parallel*: one forward pass over $n$ tokens. When the model **generates** output, it must produce each token *sequentially*: one forward pass per token, and each pass attends back to the entire prior context. The arithmetic is roughly $O(n)$ for input and $O(n \cdot m)$ for output of length $m$ — and at production scale, the GPU time per output token is several times that of an input token. Providers price what they pay for.

### Why cache is ~90 % cheaper

A prompt cache stores the **attention key/value tensors** for a long prefix (system prompt, codebase, document) so that the *next* call with the same prefix skips the expensive prefill. The provider has already paid the compute cost once and amortises it across reuses. For workloads where 80% of every request is the same 30K-token system prompt, this is the single largest line-item optimisation available.

### The cost structure rewards three product decisions

| Design choice | Why it lowers the bill |
|---------------|------------------------|
| **Short outputs** | Output is the expensive multiplier. A 200-token reply costs less than a 2,000-token one — by exactly 10×. |
| **Structured outputs** (JSON schema, function calls) | Forces brevity; eliminates filler tokens; makes downstream parsing free. |
| **Stable, repeated prompts** | Triggers prompt cache. A system prompt that changes on every request *cannot* be cached. |

!!! quote "Working principle for this lecture"
    Your model's pricing is the **boundary** of your product economics. Design the product to live inside that boundary, or the boundary will design the product for you.

---

## § II. The 2026 LLM Pricing Landscape — Shop Before You Commit

The frontier-model market in 2026 is competitive enough that the **cheapest viable option for a given task** is usually 10–50× cheaper than the most capable. There is no single right answer; there is a *task-tiered* answer.

### The major models, side by side (mid-2026)

| Model | Input $/MTok | Output $/MTok | Cached input | Position |
|-------|-------------:|--------------:|-------------:|----------|
| **Claude Opus 4.7** | $15.00 | $75.00 | $1.50 | Frontier reasoning, complex agents |
| **Claude Sonnet 4.6** | $3.00 | $15.00 | $0.30 | Workhorse — most production agents |
| **GPT-4o** | $2.50 | $10.00 | $1.25 | General-purpose chat, multimodal |
| **GPT-4o mini** | $0.15 | $0.60 | $0.075 | High-volume classification, routing |
| **Gemini 2.5 Pro** | $3.50 | $10.50 | $0.50 | Long context (1M+), grounded search |

**Two observations worth memorising.**

- GPT-4o mini's input price is **1% of Opus 4.7**. Routing easy traffic to a small model is the single highest-leverage architectural decision in a 2026 AI product.
- Cached-input prices are **2–10% of fresh input**. The gap between teams that cache well and teams that don't is roughly an order of magnitude on the monthly bill.

### Mix tiers by task difficulty — the standard production pattern

Mature production stacks rarely use one model for everything. The dominant pattern is a **three-tier router**:

| Tier | Model class | Use for | Share of traffic |
|------|-------------|---------|-----------------:|
| **Cheap** | GPT-4o mini, Haiku-class | Classification, routing, simple Q&A, schema extraction | 60–80 % |
| **Workhorse** | Sonnet 4.6, GPT-4o | Tool use, multi-step reasoning, drafting | 15–30 % |
| **Frontier** | Opus 4.7, Gemini 2.5 Pro | Hardest 5%, escalations, safety-critical | 2–10 % |

A team that runs **everything** on Opus is paying roughly **50×** what a tiered stack would cost for the same product. A team that runs **everything** on mini is shipping wrong answers on the hard 5%. The competitive engineering choice is the *router*, not the model.

!!! warning "Prices fluctuate"
    The numbers above are mid-2026 list prices. Vendor pricing has moved at roughly **30–50% per year** since 2023, almost always downward. Re-check before committing a procurement; build your cost model with a `price_per_mtok` variable, not a constant.

---

## § III. Agent Cost Multipliers — Why One User Request Becomes Ten LLM Calls

A chatbot makes one LLM call per user message. An **agent** makes 6–13. This is the single largest source of cost surprise in 2026 AI deployments.

### The five phases of an agent task

| Phase | Typical calls | What happens |
|-------|--------------:|--------------|
| **Planning** | 1–2 | Decompose the user request into sub-steps; choose a strategy. |
| **Tool selection / arg-building** | 1–3 | Pick which APIs or tools to invoke; generate well-formed arguments. |
| **Execution & iteration** | 2–5 | Run tool, inspect result, decide next step. *This is where loops happen.* |
| **Verification** | 1–2 | Re-check the answer for correctness, format, or policy violations. |
| **Response synthesis** | 1 | Compose the user-facing answer. |
| **Typical total** | **6–13** | Per user task |

An **unconstrained** agent — one with no max-iteration cap, no model routing, and no caching — costs **$5–8 per task** on a frontier model. Budgets that estimate "1 call × $0.01 = $0.01 per request" are wrong by **500–800×**. Most AI cost overruns in 2025–2026 trace back to this single mismodelling.

### Worked example — One user task, Claude Sonnet 4.6, full cost

A mid-complexity agent task: a developer asks the assistant to *"investigate why test X is flaky and propose a fix."* The agent reads the test, searches the codebase, runs the test three times, verifies its hypothesis, and writes a response.

| Call | Input tokens | Output tokens | Cost (Sonnet 4.6) |
|------|-------------:|--------------:|------------------:|
| Plan | 5,000 | 500 | $0.0225 |
| Tool #1 — read test file | 12,000 | 200 | $0.0390 |
| Tool #2 — codebase search | 8,000 | 400 | $0.0300 |
| Iterate (3 calls) | 30,000 | 1,200 | $0.1080 |
| Verify | 10,000 | 300 | $0.0345 |
| Synthesize | 12,000 | 800 | $0.0480 |
| **Total per task** | **77,000** | **3,400** | **$0.282** |

The arithmetic: input is $77{,}000 \times \$3 / 10^6 = \$0.231$; output is $3{,}400 \times \$15 / 10^6 = \$0.051$; sum $\approx \$0.282$. Note that output is **only 4.4%** of token count but **18%** of cost — because of the 5× multiplier.

### Scaling to a real product

At **100,000 tasks per month** (a mid-market SaaS):

$$
\text{Monthly inference} = 100{,}000 \times \$0.282 = \$28{,}200
$$

$$
\text{Annual inference} = 12 \times \$28{,}200 = \$338{,}400
$$

This is **inference only** — it excludes hosting, observability, vector DB, and human verification. A realistic all-in budget is roughly **1.5–2×** this number. And we have not yet turned on caching, which can cut the inference line by **50–70%**.

!!! danger "The most common 2026 cost-model error"
    Per-token cost × tokens-per-call is **not** per-task cost. Multiply by **fan-out** (6–13 for agents) and **retry rate** (1.1–1.4× for production resilience). Skipping either factor produces an estimate that is wrong by an order of magnitude.

---

## § IV. Caching Strategies — Where 40–90 % of Savings Hide

Caching is the highest-leverage optimisation available to an AI product team. Three distinct tiers exist; they **compose**; and the gap between teams that stack all three and teams that stack none is roughly **5–10× on the monthly bill**.

### The three caching tiers

| Tier | What it reuses | Where it lives | Cost / latency savings |
|------|----------------|----------------|------------------------|
| **① Prompt cache** | Identical input prefixes (system prompt, large context, retrieved docs). | Provider-managed (Anthropic, OpenAI, Google all support it). | **80–90 %** off cached tokens. |
| **② Semantic cache** | Responses for *similar* (not identical) requests, matched by embedding distance. | Application-side; small vector DB. | **40–70 %** on cacheable workloads. |
| **③ KV cache reuse** | The attention key/value state across decode steps. | Provider-internal; partially exposed via batch APIs. | **75 %** latency cut; modest cost savings. |

**They compose** — prompt cache reduces fresh-input cost; semantic cache eliminates the call entirely when an existing response will do; KV reuse cuts latency. A workload routed through all three pays a fraction of the naive bill.

### Hit rate by workload pattern

Caching savings depend on **cache hit rate**, which depends on the *shape* of your traffic. Below are typical ranges from 2026 production deployments.

| Workload pattern | Typical cache hit % | Resulting cost reduction |
|------------------|--------------------:|--------------------------|
| Customer support — repeated FAQs | 60–80 % | 50–70 % |
| Code assistant — repeated codebase context | 70–90 % | 60–80 % |
| Search / chat — unique queries | 15–30 % | 10–25 % |
| Document analysis — long shared prefix, varying tail | 85–95 % | 70–85 % |
| Agentic workflow — repeated planning prompts | 60–80 % | 50–70 % |

**Read this table conservatively.** Use the lower bound for budgeting. If you happen to land at the upper bound in production, that is a pleasant variance; if you assumed the upper bound and land at the lower bound, you have under-quoted the project.

### A simple model — what caching does to the worked example

Recall the $0.282 per-task cost from § III. Suppose we add prompt caching with a **70%** hit rate on input tokens. The new input cost per task is:

$$
0.30 \times 77{,}000 \times \$3/10^6 + 0.70 \times 77{,}000 \times \$0.30/10^6 \approx \$0.0693 + \$0.0162 \approx \$0.0855
$$

Output cost is unchanged at $0.051. New per-task total: **$0.137**, a **51% reduction** from $0.282. At 100K tasks/month, annual savings ≈ **$174K**. One engineer-month of prompt-cache engineering routinely pays back in **under 30 days**.

!!! tip "The single most under-used optimisation in 2026"
    If your AI product does **not** use prompt caching, that is almost certainly the highest-ROI engineering ticket on your backlog. Estimate the savings, write the ticket, ship it this quarter.

---

## Class Discussion — Which Caching Tier Wins for *Your* Project?

Open floor, ~10 minutes. In **pairs (4 min)**, then share.

For your group-project AI feature:

1. **Categorise your workload** — which of the five patterns in § IV does it most resemble? FAQs? Code context? Unique queries? Long shared prefix? Agentic?
2. **Estimate hit rate** — pick a defensible number from the table's range, and write down *why* you picked the low end vs. the high end.
3. **Compute monthly savings** — using your token-spend estimate from Homework 12, what would each caching tier save?
4. **Three sanity questions** the slide deck poses:
    - What **fraction of your requests share a long input prefix**? That is your prompt-cache opportunity.
    - Could **two requests safely share the same response**? That is your semantic-cache opportunity.
    - What **experiment** would prove your hit-rate assumption *before* committing engineering effort? (Hint: a one-day log analysis usually settles it.)

Three pairs will share which tier they'd implement first and why.

---

## § V. Payback Periods by Industry — 2026 Benchmarks

The single most important number in any AI business case is the **payback period**: how many months of net savings cover the build cost. Industry data from 2026 surveys (VentureBeat's 1,100-engineer-and-CTO study; Digital Applied's 100+ ROI data points; McKinsey's AI ROI tracker) shows striking variation by use case.

### Median payback by use case

| Use case | Median payback | Why fast / slow |
|----------|---------------:|-----------------|
| **Customer support** | **4.1 months** | High labour-cost displacement, narrow domain, easy to measure deflection. |
| **Marketing operations** | **6.7 months** | High volume of low-criticality work; easy to A/B test. |
| **Sales enablement** | **7.5 months** | Conversion lift offsets cost; harder attribution. |
| **Engineering productivity** | **9.3 months** | Senior-engineer verification overhead eats a big share of the gain. |
| **Compliance / legal** | **14 months** | High accuracy bar; many false positives require human triage. |

**The shape of the table is the lesson.** Payback is fastest where (a) AI displaces high-marginal-cost human labour, (b) the domain is narrow enough to verify cheaply, and (c) errors are tolerable. Payback is slowest where AI must clear a high accuracy bar in a broad domain — exactly where the human verification cost dominates.

!!! warning "The 18-month rule of thumb"
    For your group project: an AI feature with payback **greater than 18 months is rarely funded** in the current capital environment. If your model lands beyond 18 months, you have two levers — **halve the cost** (cheaper model, caching, smaller scope) or **double the value** (broader rollout, higher-leverage use case). Pick one and rebuild.

### Worked mid-market case — A 2.5-month payback

A hypothetical SaaS company: **8,000 monthly support tickets**, **$18.40 average resolution cost** (loaded agent salary, tooling, overhead). The company deploys an AI support agent that **deflects 34% of tickets** (industry-typical for a well-trained domain-tuned bot).

| Item | Monthly amount |
|------|---------------:|
| Tickets deflected ($8{,}000 \times 0.34$) | 2,720 |
| Resolution cost saved ($2{,}720 \times \$12.20$ net) | **+$33,184** |
| AI infrastructure (tokens + observability + LLM ops) | **−$3,800** |
| **Monthly net benefit** | **+$29,384** |
| One-time build cost | $72,000 |
| **Simple payback** = $72{,}000 / \$29{,}384$ | **≈ 2.5 months** |
| Discounted payback @ MARR = 12 % (monthly $i = 0.95\%$) | **≈ 2.6 months** |

The arithmetic uses Lecture 6's payback definition. Note that **deflection-cost savings** are slightly less than the headline $18.40 — the $12.20 figure assumes a *partial* deflection (some tickets still escalate to a human, who now starts mid-conversation), which is the honest way to model it. Optimists pencil in $18.40 and quote a 1.7-month payback; the project ships and they discover the real number is 2.5–3 months.

**Why this case pays back so fast** — it satisfies all three conditions: AI displaces high-cost labour ($18.40/ticket); the domain is narrow (this product's support); errors are recoverable (human escalation path exists). A compliance-screening AI with the same build cost would pay back **5–8×** more slowly because verification swallows the gain.

!!! quote "Working principle for AI ROI"
    When AI pays back fast, it is almost always because it **displaces high-marginal-cost labour at high volume**. When it pays back slowly, it is almost always because **a human still has to verify each output**.

---

## § VI. Course Wrap-Up — Thirteen Sessions in Five Lines

Today is the last new content of the semester. Lectures 14–15 are student presentations; Lecture 16 is the final exam. So this is the right moment to compress thirteen lectures into something portable.

### The whole course in five lines

1. **Every software decision is an economic decision.** Boehm's seven-step framework applies at any scale — from a one-week feature to a five-year platform.
2. **Cost lives in a four-axis taxonomy** (fixed/variable × direct/indirect × non-recurring/recurring × capex/opex) across **five lifecycle phases**. **60–80% of lifetime cost lives *after* launch** — Boehm's iceberg, restated every year since 1981.
3. **Time value of money is the calculus of comparison.** NPV is the verdict; IRR, payback, and PI are the footnotes. Equivalence is the operational definition under all of them.
4. **Sensitivity tells you which assumption to research; risk analysis tells you the probability of being wrong.** Tornado diagrams, Monte Carlo, decision trees — different tools for the same question.
5. **The AI era changes the *coefficients*, not the *equations*.** Same Boehm framework, recalibrated for tokens, agent fan-out, caching, and a re-allocated productivity curve (Lecture 12).

!!! quote "Working principle for the rest of your career"
    If you walk away with only those five lines, you can defend any software decision in any room you'll enter for the next decade.

### Final exam preview — Lecture 16, Thursday 18 June

| Section | Points | Material |
|---------|-------:|----------|
| **Multiple choice / short answer** | **20** | Definitions, frameworks, intuitions from all 13 lectures. ~20 questions, 1 pt each. |
| **Computation** | **50** | PV/FV, NPV/IRR, equivalence, sensitivity, FP, COCOMO II, token-cost arithmetic. ~5 problems. |
| **Integrative case** | **30** | A realistic project — full economic analysis including an AI cost dimension. One long question. |

**Logistics.** Closed book. One A4 cheat sheet, **single-sided, handwritten**, permitted. Non-programmable calculator. 110 minutes, room TBA. Bring a pencil and a backup pen.

**What I will *not* test.** Memorisation of factor-table values (you'll be given any non-trivial factor). Memorisation of 2026 vendor pricing (the test gives the prices). The names of every paper in the references. **Concepts and computation are the test.**

**How to study.** Re-do Homework 1, 4, 6, and 12 from scratch on a blank sheet. If you can finish each in 30 minutes with the right answer and a clean CFD, you are ready. The integrative case is built from those four shapes plus the AI-cost overlay from today.

---

## Homework 13 — Final Project Polish

**Due: Tuesday 2026-06-16, before Lecture 14 (your team's presentation). Late: not accepted — presentation slot is fixed.**

1. **Finalise your group-project presentation deck.** Required slides:
    - **Scope** — what the project is, who the user is, the decision being made.
    - **FP / COCOMO II estimate** — effort, duration, cost; reference Lecture 9–10.
    - **Cash flow + two alternatives** — CFD for the recommended option and one realistic comparator; Lecture 4.
    - **NPV / IRR / Payback / PI** — at your stated MARR; Lectures 5–6.
    - **Sensitivity analysis** — tornado diagram on the three biggest assumptions; Lecture 7.
    - **AI-cost dimension (bonus, +5 pts)** — token spend, fan-out, caching savings, AI-feature payback; today's lecture.
    - **Recommendation** — one slide, one sentence at the top.
2. **Rehearse** the talk to the clock: **12 min presentation + 5 min Q&A + 3 min peer evaluation.** Going long is the most common preventable failure.
3. **Submit the final project report PDF** to `submissions/PROJECT/<team-name>/final-report.pdf` before the start of Lecture 14.

**Office hours.** Tomorrow 10:00–11:30 (final pre-presentation slot) and Wednesday 13:00–14:00 (after Lecture 15, for exam questions).

---

## Class Discussion Prompts — Wrap-Up

Open floor, ~10 minutes (overflow from the cache discussion).

1. Pick one **AI-era cost line** from today (tokens, fan-out, cache, verification, payback gap). Argue in one paragraph whether your group project's recommendation **survives** that cost line being 2× worse than your central estimate.
2. Re-read the **five-line course summary** above. Which line do you find *most* useful — and which do you find *least* convincing? Defend in plain English.
3. Looking forward five years: **which AI-cost line do you expect to dominate** in production by 2031 — inference, verification, evaluation, compliance, or something not yet on the ledger? Make a falsifiable prediction.

---

## Further Reading — 2026 industry reports

- **VentureBeat (2026).** *"The Hidden Economics of AI Agents: 1,100 Engineers and CTOs Surveyed"* — empirical fan-out and cost data underlying § III.
- **Stevens Online (2026).** *"Token Cost Stack: A Production Engineer's Guide"* — the canonical 2026 reference for input/output/cache decomposition.
- **Silicon Data (2026).** *"LLM Cost Per Token: 2026 Practical Guide"* — month-by-month price history for the major vendors.
- **Prem AI (2026).** *"LLM Cost Optimization: 8 Strategies That Compound"* — extends the three-tier caching framework with batching, distillation, and routing.
- **Digital Applied (2026).** *"AI Agent Productivity & ROI Statistics — 2026 Edition"* — the 100+ ROI data points behind the payback table in § V.
- **McKinsey Global Institute (2026).** *"The State of AI: 2026 Survey"* — payback-period medians by industry and function.
- **Anthropic Engineering Blog.** *"Prompt Caching for Production Workloads"* — the canonical engineering reference for tier ① caching.
- *(Back-pointer)* **Boehm (1981).** *Software Engineering Economics*, Chapters 3 and 5 — the framework whose coefficients we just recalibrated.

---

## Key Vocabulary — Quick Reference

| Term | Plain-language meaning |
|------|------------------------|
| Input tokens | Tokens the model reads (prompt + context). Priced at the base rate. |
| Output tokens | Tokens the model generates. Priced at **3–5× input**. |
| Cached input tokens | Input tokens replayed from a prompt cache. Priced at **~10% of input**. |
| Prompt cache | Provider-managed cache of input prefixes; 80–90% off cached tokens. |
| Semantic cache | Application-side cache that reuses *responses* for similar requests. |
| KV cache | Provider-internal reuse of attention state; primarily a latency win. |
| Agent fan-out | Number of LLM calls per user task — typically 6–13 for an agent. |
| Per-task cost | Per-call cost × fan-out × retry rate. The *only* honest unit of AI cost. |
| Cache hit rate | Fraction of input tokens served from cache. Depends on workload shape. |
| Deflection rate | Fraction of human-bound work absorbed by the AI. Drives the savings line. |
| Verification overhead | Engineer or analyst time spent reviewing AI output. The hidden cost killer. |
| Payback period | Months of net benefit needed to recover the build cost; Lecture 6 definition. |
| Three-tier router | Cheap / workhorse / frontier model selection by task difficulty. |

---

## What Today Bought You

- **Token cost** = input + output (3–5× input) + cache (~10% of input). **Output dominates**; **cache wins**.
- **Agents fan out** 6–13×. *Per-call* cost is not *per-task* cost. Most cost overruns trace to this confusion.
- **Caching** is the highest-ROI optimisation available. 40–90% savings in production is **standard, not exceptional**.
- **Payback** depends on how much human labour you displace and how cheaply. **4–14 months by industry** is the 2026 band; **>18 months rarely funds**.
- **The whole course** compresses to five lines. Boehm still holds; only the coefficients have moved.

This is the last content lecture of Software Engineering Economics, Summer 2026. The vocabulary you now share — *NPV, IRR, sensitivity, payback, tokens, fan-out, cache* — is the lingua franca of every engineering economics discussion you will join for the next decade. Use it well.

---

*Prepared by Dr. Zhijiang Chen — Frostburg State University, Summer 2026.*
