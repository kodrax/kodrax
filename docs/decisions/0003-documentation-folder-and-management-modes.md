# 0003. Documentation folder and management modes

- **Status**: Accepted, with open questions
- **Date**: 2026-10-24
- **Scope**: where kodrax keeps a project's foundation documents and how much it may change them on its own, in new projects and in projects that already have documentation.

## Context

kodrax installs into projects that may already have documentation, written by people and organised their way. It must not disrupt that documentation, yet it has to maintain the documents that are the project's source of truth ([ADR 0002](0002-foundation-documents-are-the-source-of-truth.md)). Some users will want kodrax to keep the documents up to date by itself; others will want to approve every change.

## Decision

1. The user chooses the documentation folder when kodrax is installed in a project, and can change it later. When the folder changes, kodrax moves its documents and fixes the links as one reviewable change.
2. The user chooses one of two management modes at installation and can switch later:
   - **Managed**: kodrax creates, updates and reorganises the foundation documents itself, within the approval policy ([ADR 0013](0013-approvals.md)).
   - **Propose**: kodrax never writes in the documentation folder. It prepares each change as a proposal with a diff and applies it only when the user accepts it.
3. kodrax reads and indexes everything in the documentation folder, whoever wrote it.
4. Shared project settings (documentation folder, management mode, capabilities, approval defaults, repository roles) live in `.kodrax/project.yaml`, which is committed. Everything else in `.kodrax/` is local state, ignored by a `.kodrax/.gitignore` that kodrax writes, so the project's own `.gitignore` is never edited. Personal settings (harness accounts, one person's auto-approvals, session limits) live in the user's config folder. *Resolved 2026-10-24.*

## Options considered

| Option | Result |
|---|---|
| Configurable folder and two modes | **Chosen.** |
| A fixed folder, for example `docs/kodrax/` | Rejected. It ignores the structure a project already has. |
| Managed mode only | Rejected. Users who review every document change would have no option. |
| Propose mode only | Rejected. Too slow for a new project, where kodrax writes most of the documents. |

## Consequences

- Every document kodrax writes needs a type and a stable identity in its front matter, so kodrax can find it again after the folder or the layout changes.
- Propose mode needs a proposals queue in the UI, and kodrax's work can wait on it.
- Installing in a project with documentation needs a mapping step: which existing files hold the PRD, the requirements, the decisions and the contracts.

## Open questions

1. **Default layout** (proposed). For a new project, or a folder without structure:

   ```
   <docs>/
     product/        PRD
     requirements/   SRS
     architecture/   diagrams (C4 views in Mermaid)
     decisions/      decision records and their index
     contracts/      OpenAPI, AsyncAPI, GraphQL SDL, JSON Schema
     research/       findings and evidence
   ```

   With an existing structure, kodrax maps each document type to the existing location instead.
2. **Files kodrax didn't write** (proposed). Even in managed mode, kodrax only proposes changes to files it didn't create, until the user hands a file over. A marker in the front matter records the handover, the same idea as overseer's `managed-by` marker.
3. **How managed changes reach git**: as uncommitted changes in the main checkout, as commits on the current branch, or on a branch with a pull request, like code changes ([ADR 0012](0012-worktrees-and-sandboxes.md)).
