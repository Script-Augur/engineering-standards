---
status: accepted
date: '2026-02-22'
---

# Use Architecture Decision Records for organization-wide decisions

- [Context and Problem Statement](#context-and-problem-statement)
- [Decision Drivers](#decision-drivers)
- [Decision Outcome](#decision-outcome)
- [Considered Options](#considered-options)
- [Pros and Cons of the Options](#pros-and-cons-of-the-options)
- [More Information](#more-information)

## Context and Problem Statement

We need to capture important architectural and design decisions with their context and consequences so that teams and repositories can understand the "why" behind choices with minimal per-repo documentation. Without a shared approach, rationale is lost, onboarding is harder, and similar decisions are re-debated across the organization.

## Decision Drivers

- **Traceability** — Future readers need to see why a decision was made, not only what was decided.
- **Onboarding** — New team members and repos should quickly find relevant decisions and rationale.
- **Consistency** — Cross-repo and long-lived decisions should be documented in one place.
- **Reusability** — Many decisions recur; past good and bad outcomes are reusable assets.
- **Knowledge retention** — Implicit assumptions should be made explicit so they survive team changes.

## Decision Outcome

Chosen option: **ADRs with MADR template**, because we want a single, lean format that is comprehensible, fits normal development workflows, and supports both architectural and other significant decisions. The MADR template (the "decision record template of the MADR project" as referenced in [joelparkerhenderson/architecture-decision-record](https://github.com/joelparkerhenderson/architecture-decision-record)) gives us structure without unnecessary ceremony and is widely used and maintained.

### Consequences

- **Good**, because we get shared rationale, clearer context, and reusable decision records; per-repo updates stay minimal when org-wide decisions live here.
- **Good**, because MADR is lean and emphasizes options and pros/cons, which improves decision quality.
- **Bad**, because we need discipline to create and update decisions and lightweight governance (who creates, when to skip).

### Confirmation

Compliance can be confirmed by: (1) new or changing org-wide decisions being proposed as decisions in this repo, and (2) periodic review (e.g. annually or when context changes) to keep decisions accurate.

## Considered Options

- **No formal process** — Rely on tribal knowledge, READMEs, and ad-hoc docs.
- **ADRs with MADR template** — Use Architecture Decision Records with the [MADR](https://adr.github.io/madr/) (Markdown Architectural Decision Records) template.
- **Other formats** — e.g. [Michael Nygard's template](http://thinkrelevance.com/blog/2011/11/15/documenting-architecture-decisions), [Y-Statements](https://www.infoq.com/articles/sustainable-architectural-design-decisions), wiki pages, or formless notes.

### Pros and Cons of the Options

#### No formal process

- Good, because no extra process or docs to maintain.
- Bad, because rationale is lost, onboarding is harder, and decisions are re-debated.
- Bad, because implicit assumptions stay implicit.

#### ADRs with MADR template

- Good, because MADR is comprehensible, lean, and fits development workflows.
- Good, because it supports any significant decision, not only "architecture" in a narrow sense.
- Good, because the template makes options and consequences explicit.
- Bad, because it requires discipline and a clear rule for when to create vs skip a decision.

#### Other templates (Nygard, Y-Statements, wiki, formless)

- Good, because Nygard and others are well-known and simple.
- Neutral, because Y-Statements add structure but less tooling/community than MADR.
- Bad, because formless or wiki-only approaches give little consistency.
- Bad, because we want one org-wide format; MADR is a strong default with an active project and clear structure.

## More Information

- **Terminology:** We use the term **decisions** and the `decisions/` directory (not "ADRs" / `adr/`) in this repo, following [teamwork advice for ADRs](https://github.com/joelparkerhenderson/architecture-decision-record?tab=readme-ov-file#teamwork-advice-for-adrs): many teams find "decisions" clearer and more motivating; the underlying format remains the MADR template.
- [joelparkerhenderson/architecture-decision-record](https://github.com/joelparkerhenderson/architecture-decision-record) — ADR examples, templates, file naming, and suggestions for writing good ADRs.
- [MADR](https://adr.github.io/madr/) — Markdown Architectural Decision Records; template and rationale.
- [MADR 0000: Use Markdown Architectural Decision Records](https://adr.github.io/madr/decisions/0000-use-markdown-architectural-decision-records.html) — The MADR project's own "why MADR" decision.

**Conventions we follow:**

- **File names:** `NNNN-title-with-dashes.md` — lowercase, dashes, `.md` (e.g. `0001-use-architecture-decision-records.md`). A present-tense imperative in the title is also recommended (e.g. "use X", "choose Y").
- **Good decisions:** Include clear rationale; one decision per record; use timestamps for changing aspects; prefer adding new information (or superseding with a new decision) over rewriting history.
- **When to create a decision:** When we want future developers and repos to understand the "why," and when the decision is architecturally significant, cross-repo, or long-lived.
- **When to skip:** When the decision is limited in scope, time, risk, and cost, or is already covered by standards, policies, or other documentation.
