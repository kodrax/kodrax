# 0015. kodrax works on a project's repositories as a maintainer or a contributor

- **Status**: Accepted, with open questions
- **Date**: 2026-10-24
- **Scope**: the role kodrax takes in the repositories of a project it is installed in.

## Context

kodrax is installed in projects that have their own repositories, owners and rules. The owner decided on 2026-10-24 that, in those projects, kodrax "only should manage the repos as a maintainer or contributor, not as part of the kodrax project". The monorepo decision covers only kodrax's own code ([ADR 0011](0011-monorepo.md)).

Hosts already distinguish the two roles. A contributor proposes changes through branches and pull requests; a maintainer can also merge them and manage labels, branch protection, releases and settings.

## Decision

1. kodrax keeps the repository structure a project already has. It doesn't move a project's code into a layout of its own, and the project doesn't become part of kodrax.
2. For each repository it works on, kodrax acts in one of two roles, chosen by the user:
   - **Contributor**: kodrax works on branches and delivers through pull requests. It doesn't merge, push to the base branch or change repository settings.
   - **Maintainer**: kodrax may also merge pull requests and manage labels, branch protection, releases and repository settings, each through an approval point ([ADR 0013](0013-approvals.md)).
3. kodrax never goes beyond the permissions of the identity it uses on the host, whatever its role.
4. For a new project, the repository layout (one repository or several, monorepo or not) is a decision of that project, recorded in its own decision records, not imposed by kodrax.

## Options considered

| Option | Result |
|---|---|
| Maintainer or contributor, per repository | **Chosen.** |
| kodrax organises every project as a monorepo of its own | Rejected by the owner on 2026-10-24. |
| One full-access mode | Rejected. It can't keep kodrax to pull requests in repositories where it shouldn't merge. |

## Consequences

- The contributor role works with protected branches and fork-based flows; kodrax's delivery ends at an open pull request.
- Maintainer actions are approval points, and their auto-approval follows ADR 0013.
- The role is part of the shared project settings ([ADR 0003](0003-documentation-folder-and-management-modes.md)).

## Open questions

1. **Identity on the host.** kodrax acts with the user's own credentials (`gh` or `glab` sign-in, SSH keys), or with an identity of its own, such as the `kodrax-agent[bot]` GitHub App already signed in on the owner's machine and committing to `kodrax/overseer`, granted the role in each repository. An identity of its own lets the host enforce the role and keeps kodrax's actions apart from the user's in the history.
2. **Default role** (proposed): contributor, the least privilege.
3. **Several repositories per project**: how kodrax registers them, and where planning lives, since overseer keeps one registry per repository.
