---
title: "Lecture 1 — Introduction to Software Engineering Economics"
author: Dr. Zhijiang Chen
date: 2026-06-03
session: 1
week: 14
room: YF302
---

# Lecture 1 — Introduction to Software Engineering Economics

!!! info "Session Info"
    **Date:** Wednesday, 2026-06-03 &nbsp;·&nbsp; **Time:** 16:20–18:10 &nbsp;·&nbsp; **Room:** YF302
    **Instructor:** Dr. Zhijiang Chen

[**▶ Open the lecture slides — 110-minute web deck**](../slides/lecture-01.html){ .md-button .md-button--primary target="_blank" }

!!! tip "Using the slide deck"
    The slides are a self-contained web presentation (Reveal.js, no install). Keyboard shortcuts: **F** for full-screen, **S** for speaker view, **Esc** for slide overview, **arrow keys** to navigate. Designed for a single 110-minute session.

---

## Learning Objectives

By the end of this lecture, you should be able to:

1. Define **software engineering economics** and articulate its scope.
2. Explain *why* every software professional needs an economic mindset.
3. Describe **Boehm's 7-step framework** for economic analysis.
4. Identify the major categories of decisions software economics informs.
5. Navigate the course structure, deliverables, and assessment.

---

## 1. What Is Software Engineering Economics?

**Software Engineering Economics (SEE)** is the discipline that applies economic principles and decision-analysis techniques to the development, acquisition, evolution, and operation of software systems.

It sits at the intersection of three fields:

- **Software engineering** — the technical craft of building software.
- **Engineering economics** — the application of economic analysis to engineering decisions.
- **Management science** — the use of quantitative methods for organizational decision-making.

The field was systematized in 1981 by **Barry W. Boehm** in his seminal book *Software Engineering Economics*, which established the foundational vocabulary, models, and frameworks still in use today — most notably the **COCOMO** cost-estimation model.

### A working definition

> Software engineering economics is the study of *how to make good software decisions when resources are scarce*.

Resources here include:

- **Money** (development budget, operational expenses, licensing).
- **Time** (calendar time to market, engineer-hours, compute time).
- **People** (engineers, designers, product managers, support staff).
- **Computational resources** (CPUs, GPUs, memory, bandwidth — increasingly relevant in the AI era).
- **Attention** (cognitive load on users and on engineers themselves).

---

## 2. Why Should a Software Engineer Care About Economics?

Software engineers are not paid to write code. They are paid to **deliver value**, and every technical decision has economic consequences:

| Technical Question | Hidden Economic Question |
|--------------------|--------------------------|
| Should we refactor this module? | Is the future cost saving worth the present rework? |
| Microservices or monolith? | Which architecture minimizes total cost of ownership? |
| Should we adopt a new framework? | Does the productivity gain exceed the migration cost? |
| Build it ourselves or buy a SaaS product? | Which alternative has the better NPV over five years? |
| How much testing is enough? | What level of defect risk are we economically willing to accept? |
| Should we adopt an AI coding assistant? | Does the productivity benefit cover the license + token + verification overhead? |

Engineers who lack the vocabulary of economics tend to optimize for the wrong things:

- **Local code beauty** rather than system-level value.
- **Premature scalability** that locks in cost before revenue exists.
- **False economy** — saving a small effort now and paying a large cost later.
- **Sunk-cost fallacy** — continuing a failing project because of past investment.

The goal of this course is to give you the *tools and habits of mind* needed to avoid these pitfalls and to argue persuasively for the right technical decision.

---

## 3. Boehm's 7-Step Framework

Barry Boehm proposed a disciplined process for software economic analysis. We will return to this framework throughout the course.

| Step | Activity | Example |
|------|----------|---------|
| 1 | **Establish objectives** | "Reduce ticket resolution time by 30% within 12 months." |
| 2 | **Identify alternatives** | Hire more agents · deploy an AI assistant · outsource. |
| 3 | **Define assumptions & constraints** | Budget ceiling $500K; cannot store PII off-prem. |
| 4 | **Collect data & build a cost model** | Estimate ticket volume, labor cost, API cost, ramp time. |
| 5 | **Evaluate alternatives quantitatively** | Compute NPV, IRR, payback for each option. |
| 6 | **Perform sensitivity & risk analysis** | What if ticket volume grows 50%? What if API prices rise 30%? |
| 7 | **Iterate, decide, and document** | Choose option, document assumptions, set review checkpoints. |

This framework is **rigid** in form but **flexible** in application. Use it for a $10K tooling decision the same way you would for a $10M platform investment.

---

## 4. Categories of Decisions SEE Informs

Software economics shows up across the lifecycle. A non-exhaustive map:

### 4.1 Project decisions
- Should we start this project? *(NPV, payback)*
- How big will it be? *(Function Points, COCOMO II)*
- How risky? *(decision trees, Monte Carlo)*

### 4.2 Architecture decisions
- Build vs. buy vs. open source vs. SaaS *(Value-Based SE)*
- Monolith vs. microservices vs. serverless *(TCO comparison)*

### 4.3 Quality decisions
- How much to invest in testing, code review, monitoring *(quality economics)*
- Pay down technical debt now or later? *(debt modeling)*

### 4.4 Operations & lifecycle decisions
- When to retire a system, when to rewrite *(maintenance economics)*

### 4.5 AI-era decisions *(new!)*
- Self-host an open-weights model vs. call an API *(break-even analysis)*
- Adopt an AI coding assistant for the team *(productivity ROI)*
- How to price an AI-powered product *(unit economics)*

We will visit each category as the course progresses.

---

## 5. A Motivating Example

Suppose your team is choosing between two ways to add a search feature:

| Alternative | One-time cost | Annual operating cost | Useful life |
|-------------|--------------:|----------------------:|------------:|
| **A.** Build in-house with Elasticsearch | $120,000 | $30,000 | 5 years |
| **B.** Adopt a SaaS search vendor | $20,000 | $80,000 | 5 years |

At a 10% discount rate, which alternative is cheaper in present-value terms?

We will learn how to answer this rigorously in **Lectures 3–5**. For now, the intuition: A trades large upfront cost for low ongoing cost; B trades low upfront cost for high ongoing cost. The "right" answer depends on the **time value of money** — a concept that will become central to your engineering vocabulary.

!!! question "In-class poll"
    Without computing anything, which would *you* pick — and why? We will revisit this answer in Lecture 5 with numbers.

---

## 6. Course Roadmap

```
Week 14 — Foundations
  L1  Introduction
  L2  Cost concepts & lifecycle cost
  L3  Time value of money
  L4  Cash flow & equivalence
  L5  Investment decisions & alternative selection

Week 15 — Methods, Estimation & Strategy
  L6  Break-even & sensitivity
  L7  Risk & decision under uncertainty
  L8  Cost estimation I — size metrics & FP
  L9  Cost estimation II — COCOMO II
  L10 Quality & maintenance economics
  L11 Build / buy / reuse & VBSE
  L12 AI topic I — estimation & productivity

Week 16 — AI, Presentations & Exam
  L13 AI topic II — token economics & ROI
  L14 Student presentations (part 1)
  L15 Student presentations (part 2)
  L16 Final exam
```

Each lecture page will include: learning objectives, key concepts, worked examples, in-class exercises, and homework.

---

## 7. Assessment Summary

| Component | Weight |
|-----------|-------:|
| Participation & in-class quizzes | 15% |
| Homework assignments | 25% |
| Group project & presentation | 30% |
| Final exam | 30% |

See the [Syllabus](../syllabus.md) for project details and policies.

---

## 8. Class Discussion Prompts

1. Recall a software decision you have made or witnessed (in a job, a class project, or open source). Was *any* economic analysis performed? If not, what was used instead?
2. What is the "cost" — to the team, the user, the company — of (a) good code shipped late, vs. (b) average code shipped on time?
3. Which of Boehm's 7 steps do you think real engineering teams most often *skip*?

---

## 9. Homework 1 (due Lecture 3)

1. Read Boehm (1981), Chapter 1, and write a one-page reflection on which of Boehm's claims have aged well — and which have been overtaken by the AI era.
2. Identify one decision in your current job or last group project that, in retrospect, would have benefited from Boehm's 7-step framework. Write a 1–2 page memo applying the 7 steps *as if you were making the decision today*.

---

## 10. Further Reading

- Boehm, B. W. (1981). *Software Engineering Economics*. Prentice Hall — Chapters 1–2.
- Boehm, B. W., & Sullivan, K. J. (2000). "Software economics: a roadmap." In *The Future of Software Engineering*.
- Erdogmus, H., Favaro, J., & Halling, M. (2004). "Valuation of software initiatives under uncertainty." In *Value-Based Software Engineering*.

---

*Prepared by Dr. Zhijiang Chen — Frostburg State University, Summer 2026.*
