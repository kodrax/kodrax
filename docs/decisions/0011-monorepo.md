# 0011. kodrax's own code is a monorepo

- **Status**: Accepted, with open questions
- **Date**: 2026-10-24
- **Scope**: how kodrax's own code is organised. How kodrax works with the repositories of the projects it manages is [ADR 0015](0015-repository-roles.md).

## Context

kodrax is made of parts that change together: the `kodrax` command, the Mastra server, the Next.js UI, the harness adapters, the knowledge index and the schemas the server and the UI share.

An earlier version of this record also had kodrax organise every project it manages as a monorepo. The owner corrected that on 2026-10-24: the monorepo is kodrax's own code, and in other projects kodrax only works on the repositories, as a maintainer or a contributor, without making them part of kodrax (ADR 0015).

## Decision

1. kodrax's code lives in one public monorepo, [`kodrax/kodrax`](https://github.com/kodrax/kodrax) ([ADR 0016](0016-open-source-licence.md)).
2. Every package kodrax publishes is built from that repository.

## Options considered

| Option | Result |
|---|---|
| One monorepo | **Chosen.** Server, UI, command and adapters change together and share schemas, so a change lands in one pull request. |
| One repository per package | Rejected. Changes that cross packages would need coordinated releases. |

## Consequences

- kodrax's own foundation documents live in this repository's `docs/`, and kodrax will manage them once it can.
- The build has to publish several npm packages from one repository, starting with `kodrax`.

## Open questions

1. **Tooling** (proposed): pnpm workspaces with Turborepo, and packages along these lines: `apps/server` (Mastra), `apps/web` (Next.js), `packages/cli` (published as `kodrax`), `packages/harness` (adapters), `packages/knowledge`, `packages/docs` (document parsing and drift checks), `packages/contracts` (schemas shared by server and UI).
2. **Versioning**: one version for every package, or independent versions per package.
