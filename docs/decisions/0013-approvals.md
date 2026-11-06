# 0013. Approvals by default, auto-approval by choice

- **Status**: Accepted, with open questions
- **Date**: 2026-10-24
- **Scope**: which kodrax actions wait for a person, and how the user lets some of them run without asking.

## Context

kodrax writes documents, plans work, starts harness sessions that change code and run commands, and acts on repositories. The user has to stay in control of all of it, and also be able to let routine steps run on their own.

Harnesses ask for permission through ACP. Each request names the tool call, with a kind (`read`, `edit`, `delete`, `move`, `search`, `execute`, `fetch` and others), and offers options of four kinds: allow once, allow always, reject once and reject always. "Always" makes the harness remember the choice itself ([ACP tool calls](https://agentclientprotocol.com/protocol/v1/tool-calls)).

## Decision

1. Every approval point asks the user by default.
2. The user can turn on auto-approval per approval point and per project, optionally with conditions (for example, only inside the task's worktree).
3. Every decision, manual or automatic, is recorded: what was decided, by whom or by which rule, and when.
4. The initial approval points:

| Group | Approval point |
|---|---|
| Phase gates | PRD, SRS, architecture and plan approved before the next phase starts |
| Documents | each change in propose mode ([ADR 0003](0003-documentation-folder-and-management-modes.md)); structural changes in managed mode |
| Planning | creating, splitting or reprioritising epics and stories in overseer |
| Delivery | starting a task, choosing its backend ([ADR 0010](0010-mastra-factory-optional-backend.md)), turning its sandbox off ([ADR 0012](0012-worktrees-and-sandboxes.md)) |
| Harness permissions | file writes outside the worktree, shell commands, network access, new MCP servers |
| Repository | creating repositories, pushing, opening and merging pull requests, changing branch protection ([ADR 0015](0015-repository-roles.md)) |
| Spending | going above the session or quota limits ([ADR 0009](0009-subscription-first-authentication.md)) |

## Consequences

- The UI needs an approval queue that shows enough to decide quickly: the diff, the command, the requirement the work serves.
- Harness permission prompts become kodrax approvals; nothing is allowed on the user's behalf without a rule the user set ([ADR 0007](0007-harness-connection-acp-first.md)).
- Factory's own automation switches are set from this policy (ADR 0010).

## Open questions

1. **How far auto-approval can reach** (proposed). Approval points don't carry the same risk. A wrong automatic edit inside a task's worktree costs a revert; a wrong automatic force push, release or secret change can't be undone, and other people may already depend on it. Broad switches ("auto-approve everything in Repository") make it easy to include such an action by accident. And an agent that reads untrusted text, such as an issue or a dependency's README, can be steered into asking for the wrong action at the wrong time; a rule that answers yes without looking turns that into damage. The proposal is three tiers:

   | Tier | Examples | How it can be auto-approved |
   |---|---|---|
   | Routine: reversible and confined to the task | reading and searching; edits, moves and commands inside the task's worktree or sandbox; running tests | per point or per group ("auto-approve routine work in worktrees") |
   | Visible: reversible, but other people see it | pushing a task branch, opening a pull request, creating or updating overseer items, document changes in managed mode | per point |
   | Critical: irreversible or security-sensitive | merging into the base branch, force pushes, deleting branches or data, publishing packages or releases, changing secrets or credentials, changing branch protection or repository settings, turning security checks off | only one action type at a time and only with conditions, for example "merge into `main` only when the required checks pass and a person has reviewed"; never through a group switch |

   kodrax places each harness permission request in a tier from its ACP tool kind and the paths or commands it touches. Three rules apply to every tier:
   - Only the user changes the approval policy, through kodrax's UI or CLI. No harness session and no MCP tool can create, widen or remove a rule, so text an agent reads can't grant it permissions.
   - When a harness offers "allow always", kodrax answers "allow once" and applies its own rule again next time. The harness never remembers a permission on kodrax's behalf, so every decision stays in kodrax's audit log.
   - A rule can be limited to the current session or to a time window, and one action revokes every rule of a project.

   Alternatives: a flat model where any point can be auto-approved in any grouping (simplest, easiest to over-grant), or a model where critical actions can never be auto-approved (safest, but it rules out automatic merges gated by CI).
