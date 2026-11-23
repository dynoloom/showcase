# How DynoLoom works

DynoLoom takes a Dynamics 365 Finance & Operations requirement from a written document to compiled code in the
client's own model. A person stays in control at the two points that matter: approving the design, and (optionally)
reviewing and testing the result.

```
 1. Requirement      2. Readiness       3. Design (TDD)      4. Build            5. Delivery
 ──────────────      ────────────       ───────────────      ────────            ───────────
 Client submits  →   DynoLoom checks for →  Technical design  →  X++ written to  →   Change set delivered
 an FRD (.docx,      gaps and asks      + test plan +        the TDD, compiled   to the client's model;
 text, mock-ups)     only what it       estimate; client     with the real X++   optional human review,
                     cannot decide      approves it          compiler            testing and package
```

## 1. Requirement

The client writes the requirement the way they would for any partner: a functional requirement document with business
context, functional rules, mock-ups and acceptance criteria. The FRDs in this repository are exactly what was submitted
(see each `1-requirement` folder).

## 2. Readiness check

Before designing anything, DynoLoom reads the requirement against standard D365 behaviour and asks questions **only where
the document leaves a real decision open**: for example, which existing field holds the "Customer PO Reference", or what
to do when several orders are combined into one invoice. Where the answer follows from standard behaviour, it is
written into the design as a stated assumption instead of a question. The questions and the client's answers are
published in `2-design/readiness-questions.md` where there were any.

## 3. Technical design (TDD)

DynoLoom writes a Technical Design Document: summary, scope and assumptions, the data model, every object it will create
or extend (with names, fields, methods and security), the UI, and a **test plan** mapped to the acceptance criteria.
The client approves the TDD before any code is written, so the design, not the code, is where scope is agreed.

## 4. Build

The code is generated against the approved TDD into the client's extension model (`DYLOExtensions` here) and compiled
with the standard X++ compiler for the client's D365 version. Delivery happens **only on a clean compile**: a compile
error goes back to the build step to be fixed.

When two requirements for the same client are built at the same time, the later one is brought up to date with the
code delivered in the meantime (for example, both adding labels to the shared label file), merged, and compiled again
before it is delivered.

## 5. Delivery

The change set is delivered to the client's repository or dev box. The client can add, per requirement:

- **Human code review** by a D365 developer before delivery
- **Human functional testing** against the TDD's test plan
- **A deployable package**, built by the client's pipeline

In this repository, each requirement's `3-code/changes.patch` is the exact diff that was delivered, and
`4-testing/test-cases.md` records the test plan and the evidence collected for it.
