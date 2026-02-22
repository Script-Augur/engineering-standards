---
domain: "Configuration"
status: accepted
date: "2026-02-22"
---

# Environment variable best practices

- [Context and Problem Statement](#context-and-problem-statement)
- [Decision Drivers](#decision-drivers)
- [Decision Outcome](#decision-outcome)
- [Considered Options](#considered-options)
- [Pros and Cons of the Options](#pros-and-cons-of-the-options)
- [More Information](#more-information)

## Context and Problem Statement

We want applications to be configurable via environment variables (12-factor style) so that one build can behave differently per deployment environment. We need a way to document which variables exist, their shape and defaults, and where each variable’s purpose originates—without relying on separate `.env.defaults` or `.env.schema` files that can drift from reality. We want structure enforced at build or startup so missing or invalid configuration fails fast with clear errors.

## Decision Drivers

- **Single source of truth** — The schema that defines required/optional vars and types should be the same thing that validates at runtime; separate schema files can get out of sync.
- **Developer experience** — Developers should see what is configurable, get type safety and autocomplete, and understand the purpose and origin of each variable (e.g. which service or feature uses it).
- **Secrets out of SCM** — Actual secret values stay out of version control; only schema and non-secret documentation live in code.
- **Enforcement** — Invalid or missing required variables should be caught at build or application startup, not in production at first use.
- **Documentation** — Each variable’s purpose (and optionally where it originated) should be documented in a way that stays next to the schema and benefits from standard tooling (e.g. TSDoc).

## Decision Outcome

Chosen option: **Schema-in-code with a validation library (e.g. t3-env)**, because it keeps the schema, validation, and documentation in one place: the schema enforces structure and types, and TSDoc (or JSDoc) on the schema can document how and where each environment variable’s purpose originated. We prefer this over maintaining separate `.env.defaults` or `.env.schema` files, which do not enforce structure and can drift from the application.

Use a tool such as [t3-env](https://env.t3.gg/docs/introduction) (or any Standard Schema–compatible validator used in the same way) to define and validate environment variables in code. Document each variable’s purpose and origin in TSDoc/JSDoc on the schema so that editors and docs generators surface it. Keep actual values in `.env` (or platform env config) and out of version control when they are secret.

### Consequences

- **Good**, because validation runs at build or startup, so misconfiguration fails fast with clear messages.
- **Good**, because a single schema drives types, validation, and (via TSDoc) documentation; no separate schema file to keep in sync.
- **Good**, because server/client separation (e.g. t3-env’s client prefix) reduces accidental exposure of server-only variables.
- **Bad**, because this approach fits TypeScript/JavaScript ecosystems; other stacks need an equivalent “schema-in-code” pattern rather than this specific tool.

### Confirmation

Compliance can be confirmed by: (1) environment variables being defined and validated via a schema-in-code approach (e.g. t3-env or equivalent) rather than only `.env.defaults`/`.env.schema`; (2) required variables and types documented in code (e.g. TSDoc) where the schema is defined.

## Considered Options

- **.env with .env.defaults and .env.schema** — Separate files for defaults and key list (e.g. [environment-variable-configuration](https://github.com/joelparkerhenderson/architecture-decision-record/tree/main/locales/en/examples/environment-variable-configuration)); version-controlled schema keys and defaults.
- **Schema-in-code (e.g. t3-env)** — Define and validate env in TypeScript/JavaScript with a validator (Zod, etc.); document variables with TSDoc next to the schema.
- **Raw .env only** — No schema or defaults in repo; documentation elsewhere or ad hoc.

### Pros and Cons of the Options

#### .env with .env.defaults and .env.schema

- Good, because it is simple, framework-agnostic, and matches common practices.
- Good, because defaults and key list can be versioned without shipping secrets.
- Bad, because the schema does not enforce structure or types at runtime; invalid or missing vars can surface only when used.
- Bad, because purpose and origin of each variable are not co-located with the schema and require separate documentation that can drift.

#### Schema-in-code (e.g. t3-env)

- Good, because the same schema enforces structure and types at build/startup and provides type safety and autocomplete.
- Good, because TSDoc on the schema documents each variable’s purpose and origin next to the definition; tooling can surface it.
- Good, because server/client separation and presets (e.g. Vercel, Next.js) reduce mistakes and improve DX.
- Bad, because it is tied to TypeScript/JavaScript (or a similar “schema in code” approach in other languages).
- Neutral, because teams must adopt a validator (e.g. Zod) and a small amount of boilerplate.

#### Raw .env only

- Good, because there is no extra tooling or schema to maintain.
- Bad, because there is no enforcement of required variables or types; no single place to document purpose or origin.
- Bad, because misconfiguration is discovered late and is harder to debug.

## More Information

- [joelparkerhenderson/architecture-decision-record – environment-variable-configuration](https://github.com/joelparkerhenderson/architecture-decision-record/tree/main/locales/en/examples/environment-variable-configuration) — Reference example using .env, .env.defaults, and .env.schema; we diverge by preferring schema-in-code over separate schema files.
- [T3 Env – Introduction](https://env.t3.gg/docs/introduction) — Rationale, features (type safety, server/client separation, Standard Schema support), and framework-agnostic use.
- [T3 Env – Standard Schema](https://env.t3.gg/docs/standard-schema) — Use of Zod, Valibot, ArkType, Typia, or other Standard Schema–compatible validators.
- For non–TypeScript/JavaScript stacks, apply the same principle: define and validate environment variables in code (e.g. struct tags, config constructors) and document purpose/origin next to the definition, rather than relying only on separate .env.defaults/.env.schema–style files.
