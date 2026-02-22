---
domain: "Tech stack"
status: accepted
date: "2026-02-22"
---

# Programming languages

- [Context and Problem Statement](#context-and-problem-statement)
- [Decision Drivers](#decision-drivers)
- [Decision Outcome](#decision-outcome)
- [Considered Options](#considered-options)
- [Pros and Cons of the Options](#pros-and-cons-of-the-options)
- [More Information](#more-information)

## Context and Problem Statement

We need to choose programming languages for front-end and back-end so that we can build fully type-safe software. A critical requirement is that API interfaces between client and server cannot drift: shared types or a single source of truth for contracts must be enforced without relying on code generators (e.g. OpenAPI → client SDKs) that can get out of sync or add friction. We want one language used almost exclusively for both sides so that types and interfaces are shared by default.

## Decision Drivers

* **Full type safety** — We want compile-time and editor support for types across the whole stack; no untyped boundaries where bugs slip through.
* **No interface drift** — API contracts (request/response shapes, DTOs) must stay in sync between front-end and back-end without manual codegen or separate schema files that can diverge.
* **Single language across stack** — Using the same language for front and back allows sharing types, validation, and utilities directly; no code generation step to keep contracts aligned.
* **Developer experience** — Strong typing, autocomplete, and refactor safety matter; we prefer a single ecosystem and toolchain where possible.
* **Runtime and ecosystem** — The chosen language must be viable for both browser/server, with good frameworks and deployment options.

## Decision Outcome

Chosen option: **TypeScript for both front-end and back-end**, because it gives us full type safety and shared types across the stack without code generators. We can define API interfaces, DTOs, and shared domain types in TypeScript once and use them on both client and server, so API interfaces cannot drift. TypeScript is the only mainstream option that runs in the browser and on the server with a single type system and no codegen step for contract alignment.

We use TypeScript almost exclusively for front-end and back-end code. When we need another language (e.g. for a specific runtime or tool), we still prefer to keep API boundaries typed and documented; TypeScript remains the primary language for application and API code.

### Consequences

* **Good**, because API interfaces, DTOs, and shared types live in one place; front and back consume the same types, so drift is impossible by construction.
* **Good**, because we get full type safety and editor support across the stack without maintaining OpenAPI/Swagger and generated clients.
* **Good**, because one language reduces context-switching and allows sharing validation, utilities, and patterns (e.g. [0002](0002-environment-variable-best-practices.md) schema-in-code fits TypeScript).
* **Bad**, because we do not use a different language for back-end (e.g. Rust, Go) that might offer different tradeoffs (e.g. raw performance, memory control); we accept that tradeoff for type safety and shared types.
* **Neutral**, because Node/TS runtimes and tooling must be chosen to support TypeScript on the server (e.g. Node, Deno, or TS-native runtimes).

### Confirmation

Compliance can be confirmed by: (1) front-end and back-end application code being written in TypeScript; (2) API contracts (request/response types, DTOs) defined in shared TypeScript and imported by both client and server rather than generated from OpenAPI or similar; (3) no separate code-generation step required to keep API interfaces in sync.

## Considered Options

* **TypeScript full-stack** — TypeScript for front-end and back-end; shared types and interfaces in one codebase or shared package; no codegen for API contracts.
* **TypeScript front + other back (Rust, Go, etc.)** — TypeScript in the browser; another language on the server; API contracts kept in sync via code generators (e.g. OpenAPI, protobuf) or hand-maintained types.
* **Multi-language with codegen** — Any mix of languages with OpenAPI/Swagger or similar; generated client SDKs and server stubs; schema as source of truth.
* **JavaScript or other single language** — Plain JavaScript or another single language full-stack; weaker or no static typing; higher risk of interface drift.

### Pros and Cons of the Options

#### TypeScript full-stack

* Good, because one type system and one language; shared types mean API interfaces cannot drift.
* Good, because no code generators to run or keep in sync; types are the source of truth.
* Good, because strong typing and tooling across the whole stack; refactors and renames are safe.
* Bad, because we do not use a non-JS back-end language (e.g. Rust, Go) if we later need different runtime characteristics.
* Neutral, because Node/TS server runtimes are mature and widely supported (e.g. Vercel, Node on Lambda, Deno).

#### TypeScript front + other back (Rust, Go, etc.)

* Good, because we could optimize back-end for performance or safety (e.g. Rust) or simplicity (e.g. Go).
* Bad, because API contracts must be kept in sync via codegen or hand-written types; interfaces can drift if codegen is skipped or schema is updated in one place only.
* Bad, because we lose “single source of truth” for types; we either maintain OpenAPI/schema and generated clients or duplicate type definitions.
* Bad, because it conflicts with the goal of full type safety without code generators.

#### Multi-language with codegen

* Good, because it supports multiple languages and can work with OpenAPI-first workflows.
* Bad, because codegen adds a step that can be forgotten or run at the wrong time; generated code can drift from the schema or from actual server behavior.
* Bad, because it does not meet the requirement of type-safe interfaces without code generators.

#### JavaScript or other single language

* Good, because one language and no codegen.
* Bad, because without static types, API interfaces are not enforced; drift and runtime shape mismatches are likely.
* Bad, because it does not meet the requirement of full type safety.

## More Information

* [joelparkerhenderson/architecture-decision-record – programming-languages](https://github.com/joelparkerhenderson/architecture-decision-record/tree/main/locales/en/examples/programming-languages) — Reference example that chose TypeScript (front) and Rust (back); we diverge by using TypeScript for both to avoid codegen and keep API interfaces from drifting.
* TypeScript’s type system allows sharing interfaces, enums, and branded types across packages (e.g. a shared `api` or `contracts` package consumed by front-end and back-end). Use a monorepo or published package so both sides import the same types.
* When integrating with non-TypeScript services (e.g. third-party APIs), we still define types in TypeScript and validate at boundaries (e.g. with Zod or similar) rather than relying on codegen as the primary mechanism for our own APIs.
