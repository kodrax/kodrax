# Decision records

This folder holds kodrax's architecture decision records (ADRs). Together with the PRD and the SRS they are the source of truth for the project ([ADR 0002](0002-foundation-documents-are-the-source-of-truth.md)): code is checked against them, never the other way around.

## Conventions

- One decision per record, numbered in the order it was made: `NNNN-short-title.md`. Start from [template.md](template.md).
- **Status**:
  - `Proposed`: written, waiting for the owner's decision.
  - `Accepted`: decided.
  - `Accepted, with open questions`: the decision stands. Its "Open questions" section lists the parts that are still undecided, with proposals where there are some.
  - `Superseded by NNNN` or `Deprecated`.
- Answering an open question updates its record: the answer moves into the Decision section, marked with the date. Changing something already decided needs a new record that supersedes the old one; the old record only gets its status updated.
- External facts (terms of service, versions, prices, availability) carry an evidence date, because they change.
- The product name is written in lowercase in running text: kodrax.

## Index

| ADR | Decision | Status | Date |
|---|---|---|---|
| [0001](0001-product-name.md) | Product name: Kodrax | Accepted | 2026-10-11 |
| [0002](0002-foundation-documents-are-the-source-of-truth.md) | Foundation documents are the source of truth | Accepted | 2026-10-24 |
| [0003](0003-documentation-folder-and-management-modes.md) | Documentation folder and management modes | Accepted, with open questions | 2026-10-24 |
| [0004](0004-knowledge-database.md) | kodrax knowledge database | Accepted, with open questions | 2026-10-24 |
| [0005](0005-planning-through-overseer.md) | Planning through overseer | Accepted, with open questions | 2026-10-24 |
| [0006](0006-local-runtime.md) | Local runtime: separate server and UI | Accepted, with open questions | 2026-10-24 |
| [0007](0007-harness-connection-acp-first.md) | Harness connection: ACP first, vendor SDK as fallback | Accepted | 2026-10-24 |
| [0008](0008-harness-as-the-brain.md) | A harness CLI is kodrax's brain | Accepted | 2026-10-24 |
| [0009](0009-subscription-first-authentication.md) | Subscription first, API keys optional | Accepted, with open questions | 2026-10-24 |
| [0010](0010-mastra-factory-optional-backend.md) | Mastra Factory as an optional delivery backend | Accepted, with open questions | 2026-10-24 |
| [0011](0011-monorepo.md) | kodrax's own code is a monorepo | Accepted, with open questions | 2026-10-24 |
| [0012](0012-worktrees-and-sandboxes.md) | Worktrees by default, sandboxes when required | Accepted | 2026-10-24 |
| [0013](0013-approvals.md) | Approvals by default, auto-approval by choice | Accepted, with open questions | 2026-10-24 |
| [0014](0014-optional-capabilities.md) | kodrax and its capabilities can be turned off | Accepted, with open questions | 2026-10-24 |
| [0015](0015-repository-roles.md) | kodrax works on a project's repositories as a maintainer or a contributor | Accepted, with open questions | 2026-10-24 |
| [0016](0016-open-source-licence.md) | kodrax is open source under Apache-2.0 | Accepted, with open questions | 2026-10-24 |

## Pending decisions

Topics that don't have a record yet. The SRS lists every open issue that affects requirements in [its section 6](../requirements/srs.md#6-open-issues).

| Topic | Notes |
|---|---|
| Interface for other agents and systems | Implied by ADR 0001's scope ("conversations with other systems, agents and models"). Deferred by the owner on 2026-10-24. |
| First users: solo builders, teams, or both | Decides how plans, documents and approvals are shared between people. |
