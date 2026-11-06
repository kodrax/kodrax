# 0014. kodrax and its capabilities can be turned off

- **Status**: Accepted, with open questions
- **Date**: 2026-10-24
- **Scope**: turning kodrax and each of its capabilities on and off per project, and the defaults.

## Context

kodrax is installed in projects of every size and maturity. Some users want the whole system, others only parts of it, and sometimes none of it for a while. The owner decided on 2026-10-24 that planning with overseer is on by default, that repository handling with the security of the code in mind is optional and on by default, and that kodrax itself can be turned off in a project.

## Decision

1. kodrax itself can be turned off in a project, and back on, as overseer can. While it's off, kodrax does no work there: no harness sessions, no indexing, no writes, and its MCP tools answer that kodrax is off. Its data in `.kodrax/` is kept.
2. Each capability can be turned on or off per project, and has a default.
3. Turning a capability off removes its tools from harness sessions instead of only blocking them, so they cost no context. This follows overseer's rule that off means off at every entry point.
4. The initial capabilities:

| Capability | Default | See |
|---|---|---|
| Documentation management, managed or propose | on | [ADR 0003](0003-documentation-folder-and-management-modes.md) |
| Knowledge index | on | [ADR 0004](0004-knowledge-database.md) |
| Planning with overseer | on | [ADR 0005](0005-planning-through-overseer.md) |
| Repository management: git, branches, worktrees, pull requests, GitHub and GitLab | on | [ADR 0012](0012-worktrees-and-sandboxes.md), [ADR 0015](0015-repository-roles.md) |
| Code security: secret scanning before commits and pushes, dependency audit, static analysis, least-privilege repository credentials | on | open question 1 |
| Sandboxes | off | ADR 0012 |
| Auto-approvals | off | [ADR 0013](0013-approvals.md) |
| Mastra Factory backend | off until a Factory is connected | [ADR 0010](0010-mastra-factory-optional-backend.md) |
| Features that need an API key | off until a key is set | [ADR 0009](0009-subscription-first-authentication.md) |

Protective capabilities default to on. Capabilities that add autonomy or cost default to off.

## Consequences

- Capabilities declare their dependencies (the Factory backend needs repository management), and kodrax refuses combinations that can't work.
- The settings are part of the shared project settings (ADR 0003).

## Open questions

1. **Code security tooling**: which scanners kodrax runs by default, and whether it installs them or uses what the project already has.
2. **Clones stay off** (proposed). A repository can't turn kodrax on by itself: a clone that contains `.kodrax/project.yaml` stays off for a user until that user turns kodrax on there, the rule overseer follows. Otherwise opening a stranger's repository could start harness sessions with that repository's settings.
