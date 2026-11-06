# 0002. Foundation documents are the source of truth

- **Status**: Accepted
- **Date**: 2026-10-24
- **Scope**: which artefacts are canonical in a project kodrax manages, kodrax itself included, and in which direction kodrax checks them. It covers the PRD, the SRS, decision records, architecture diagrams, contracts and schemas, and how they relate to overseer's board and to kodrax's own database.

## Context

kodrax defines a product with the user (PRD, SRS, architecture, contracts) before code exists, plans it and then gets it built. Three stores could each claim to describe the project:

1. the foundation documents, committed in the repository;
2. overseer's planning database, `.overseer/overseer.db`, which git ignores and which exists once per clone;
3. kodrax's own database in `.kodrax/`, which git also ignores ([ADR 0004](0004-knowledge-database.md)).

overseer numbers items with per-kind counters kept in its local database. Two clones that build their boards separately can give the same requirement different IDs. overseer's exports are readable snapshots and can't restore a database ([overseer README](https://github.com/kodrax/overseer#project-data)).

## Decision

1. The foundation documents committed in the project's documentation folder ([ADR 0003](0003-documentation-folder-and-management-modes.md)) are the source of truth for what the product is and how it is built.
2. kodrax checks code against the documents. When they disagree, kodrax reports drift. It changes a document to match the code only when a person approves that document change.
3. The SRS owns requirement IDs. An ID is stable, is never reused and never depends on a local database.
4. overseer's requirement items are a projection of the SRS. kodrax creates and updates them from the SRS and records the SRS ID on each item, so a fresh clone can rebuild them.
5. Planning (epics, stories, tasks, bugs, their status and order) belongs to overseer ([ADR 0005](0005-planning-through-overseer.md)). It is not copied into the foundation documents.
6. kodrax's database indexes the documents and the code. It is never the source of truth.

| Information | Source of truth | Derived from it |
|---|---|---|
| Vision, users, goals, scope | PRD | knowledge index |
| Functional and non-functional requirements, acceptance criteria | SRS | overseer requirement items, tests, knowledge index |
| Architecture and the reasons for it | decision records, architecture diagrams | knowledge index |
| Interfaces and data | contracts and schemas (OpenAPI, AsyncAPI, GraphQL SDL, JSON Schema) | generated code, conformance tests, knowledge index |
| Plan and progress | overseer's database, optionally synced to GitHub or GitLab issues | overseer exports, kodrax UI |
| Implementation | the code in the repository | code index |

## Options considered

| Option | Result |
|---|---|
| Committed documents are canonical; overseer and kodrax's database derive from them | **Chosen.** Reviewable in git and identical in every clone. |
| overseer's database is canonical, and Markdown is exported from it | Rejected. The database is per clone and isn't committed, exports can't restore it, and IDs can differ between clones. |
| Two-way sync between documents and overseer | Rejected. Conflicts can start on either side, and no single place shows a change for review. |

## Consequences

- Every requirement change is a document change, with a diff, a review and history.
- The SRS format becomes a contract: kodrax has to parse its IDs, EARS statements, acceptance criteria and links. The format is defined together with the SRS.
- The projection works with today's overseer: the SRS path and ID go in the item's `source` field and in a tag such as `srs:FR-012`, and `list_items` filters by tag. Nothing stops two items from carrying the same tag, so a first-class spec reference in overseer would be safer (action item 2).
- Drift needs three checks: the SRS against overseer (projection up to date), the SRS against code and tests (requirements implemented and verified), and contracts against code (conformance).
- In propose mode (ADR 0003), fixing drift in a document is also a proposal.

## Action items

1. Define the SRS format, including the requirement ID scheme, in the SRS draft.
2. In the overseer repository: add a spec reference on requirement items (for example `spec:docs/requirements/srs.md#FR-012`) that is unique per project and lets the SRS win over the board for requirement fields, the way a connected repository can be the source of truth today.
