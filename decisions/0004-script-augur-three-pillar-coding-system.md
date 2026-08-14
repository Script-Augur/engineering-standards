---
domain: "Tech stack"
status: accepted
date: "2026-08-13"
---

# Script-Augur three-pillar coding system

- [Context and Problem Statement](#context-and-problem-statement)
- [Decision Drivers](#decision-drivers)
- [Decision Outcome](#decision-outcome)
- [Considered Options](#considered-options)
- [Pros and Cons of the Options](#pros-and-cons-of-the-options)
- [More Information](#more-information)

## Context and Problem Statement

We need a durable way to reuse application scaffolding, shared tooling, and UI primitives across Script-Augur projects without collapsing those concerns into one repo or one distribution channel. App starters want copy-once bootstrap; infrastructure wants immutable versioned packages; UI wants editable source that can evolve in each app and occasionally flow back upstream. How should we structure that system?

## Decision Drivers

* **Clear update direction** — Immutable infra updates downhill via package versions; mutable UI is owned in the app after copy.
* **Tooling compatibility** — TanStack Start, Vite, Convex, Vitest, Changesets, and GitHub Packages assume Node + a conventional package manager.
* **One consumer standard** — Install, test, and CI docs should not fork across bun/Deno lockfiles.
* **UI stack alignment** — Prefer shadcn’s current Base UI default over carrying a Radix fork.
* **Private shared code** — Org-scoped packages must install locally and in CI without per-repo token sprawl.
* **Boundary hygiene** — Node/Vite/non-UI machines must not be smuggled into shadcn copy items.

## Decision Outcome

Chosen option: **three GitHub repos under `Script-Augur`**, because it separates operational models cleanly while sharing one stack standard.

| Pillar | Repo | Mechanism |
| ------ | ---- | --------- |
| Immutable infra | [`Script-Augur/packages`](https://github.com/Script-Augur/packages) | pnpm workspace publishing `@script-augur/*` to GitHub Packages |
| Mutable code blocks | [`Script-Augur/ui-registry`](https://github.com/Script-Augur/ui-registry) | Public shadcn GitHub registry (root `registry.json`) |
| App starter | [`Script-Augur/tanstack-convex-template`](https://github.com/Script-Augur/tanstack-convex-template) | GitHub template repo (TanStack Start + Convex) |

Locked stack choices:

* **Package manager / runtime:** pnpm + Node (not bun or Deno as the default)
* **Private registry:** GitHub Packages (`https://npm.pkg.github.com`) — installs always authenticate (even for public packages); local/CI use `NPM_TOKEN` with `read:packages`, or the template CI fallback that resolves from a checkout of `Script-Augur/packages`
* **UI primitives:** Base UI (`@base-ui/react`) via shadcn — not Radix
* **Local auth:** machine-wide `NPM_TOKEN` / `~/.npmrc` (not per-repo secrets)
* **CI auth:** composite action `Script-Augur/packages/.github/actions/setup-script-augur` (vendored into the public template because public workflows cannot `uses:` a private-repo action)
* **Packages vs registry:** Node built-ins, Vite hooks, non-UI state machines → packages only; UI/hooks/layouts/conventions → registry

Auth/tenant libraries already exist as npm packages (`@convex-dev/auth`, `@djpanda/convex-tenants`, `@djpanda/convex-authz`) and are **not** republished under `@script-augur` for v1.

### Consequences

* **Good**, because each pillar has a matching release/update model (publish, `shadcn add`, template clone).
* **Good**, because pnpm + Node matches the publish/test/CI path consumers already document.
* **Good**, because Base UI tracks shadcn’s current default and avoids forking ofy’s Radix UI tree.
* **Bad**, because private GitHub Packages require a PAT with `read:packages` on every developer machine and correct CI token scopes.
* **Bad**, because three repos add coordination overhead (versions, back-propagation PRs, template drift).
* **Neutral**, because ofy/mcc and other apps are not migrated in v1; adoption is opt-in via template and registry.

### Confirmation

Compliance: new Script-Augur apps start from `tanstack-convex-template` (or document why not); shared infra lives under `@script-augur/*`; UI shared via `Script-Augur/ui-registry`; READMEs document `NPM_TOKEN` / composite action; cursor rules encode packages-vs-registry and back-propagate.

## Considered Options

* **Three-pillar Script-Augur system (chosen)** — packages + ui-registry + template as above.
* **Single monorepo** — template, packages, and registry in one repository.
* **Bun-first (or Deno-first) shared system** — standardize on bun or Deno instead of pnpm + Node.
* **Verdaccio / self-hosted registry** — private npm without GitHub Packages.
* **Radix-based shadcn registry** — keep ofy’s Radix UI stack as the org default.

### Pros and Cons of the Options

#### Three-pillar Script-Augur system (chosen)

* Good, because operational models match artifact types (immutable vs copy-mutate vs bootstrap).
* Good, because GitHub Packages + public registry + template reuse existing GitHub org machinery.
* Bad, because cross-repo versioning and back-propagation need explicit discipline.

#### Single monorepo

* Good, because one clone and atomic cross-cutting changes.
* Bad, because publish boundaries, public registry hosting, and template “Use this template” UX fight each other in one tree.
* Bad, because private packages and public UI registry visibility differ.

#### Bun-first (or Deno-first) shared system

* Good, because some apps (e.g. ofy) already use bun locally.
* Bad, because Vitest/Vite plugins/Changesets/GitHub Packages paths still assume Node; dual lockfiles confuse consumers.
* Bad, because Deno would fight TanStack Start + Convex + Changesets rather than support them today.

#### Verdaccio / self-hosted registry

* Good, because full control over hosting and access policy.
* Bad, because ops cost and another credential plane; GitHub Packages is enough for v1.

#### Radix-based shadcn registry

* Good, because ofy already has Radix components to port.
* Bad, because shadcn’s current default is Base UI; forking Radix as the org standard increases long-term drift.

## More Information

* Packages seed: image-gen, svg-sprite, eslint module-ordering, eslint/prettier/tsconfig; composite `setup-script-augur`.
* Registry seed: Base UI `button` + `project-conventions` (agent/editor files); grow via back-propagation.
* Template: stripped scaffolding only — no OFY/Kibble domain or ofy Radix UI.
* Related: [0003](0003-use-typescript-full-stack.md) TypeScript full-stack.
