# Engineering standards

This repository holds **organization-wide architecture and engineering decisions**. Each decision is recorded using the MADR (Markdown Architectural Decision Records) format so that teams and repositories can understand the "why" behind choices with minimal per-repo documentation.

We use the term **decisions** and the **`decisions/`** directory (not "ADRs" / `adr/`) in this repo. This follows [teamwork advice for ADRs](https://github.com/joelparkerhenderson/architecture-decision-record?tab=readme-ov-file#teamwork-advice-for-adrs): many teams find "decisions" clearer and more motivating; the underlying format remains the MADR template. Our first decision, [0001](decisions/0001-use-architecture-decision-records.md), documents this approach and the choice to use the MADR template.

## When to create a decision

Create a decision when it is:

- **Architecturally significant** — It has a measurable effect on systems, interfaces, or long-term design.
- **Cross-repo or org-wide** — It applies to more than one team or repository.
- **Long-lived** — Future developers will need the rationale.

## When to skip a decision

Skip a decision when it is:

- Limited in scope, time, risk, and cost.
- Already covered by standards, policies, or other documentation.
- Temporary (e.g. workarounds, experiments, proofs of concept).

## How to add a decision

1. Copy [decisions/template.md](decisions/template.md) to `decisions/NNNN-title-with-dashes.md`, where `NNNN` is the next sequential number (e.g. `0002-choose-database.md`).
2. Fill in the template: context, decision drivers, options, outcome, consequences, and pros/cons.
3. When the decision is accepted, add a row to the appropriate domain table in [registry.md](registry.md) (or add a new domain section).
4. Open a pull request (or follow your organization's review process).

**File naming:** Use lowercase words with dashes and a numeric prefix, e.g. `0003-manage-secrets.md`.

## Decision registry

Accepted decisions are listed by domain in the **[decision registry](registry.md)** (ID, Decision, Status, Superseded by). Use it to see current decisions and to update entries when a decision is superseded.

## First decision

The decision to use Architecture Decision Records (with the MADR template) for organization-wide decisions is documented here:

- **[0001: Use Architecture Decision Records for organization-wide decisions](decisions/0001-use-architecture-decision-records.md)** — Why we use this format and how we apply it.

## References

- [registry.md](registry.md) — Registry of accepted decisions by domain.
- [decisions/template.md](decisions/template.md) — MADR template to copy for new decisions.
- [joelparkerhenderson/architecture-decision-record](https://github.com/joelparkerhenderson/architecture-decision-record) — ADR examples and guidance.
- [MADR](https://adr.github.io/madr/) — Markdown Architectural Decision Records.
