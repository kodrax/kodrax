# 0005. Planning through overseer

- **Status**: Accepted, with open questions
- **Date**: 2026-10-24
- **Scope**: how kodrax plans work and tracks progress, and how it talks to overseer.
- **Evidence date**: 2026-10-24 (`@kodrax/overseer` 0.1.1).

## Context

overseer is published as [`@kodrax/overseer`](https://www.npmjs.com/package/@kodrax/overseer) (0.1.1, Apache-2.0, Node.js ≥ 22.18). It keeps requirements, epics, stories, tasks and bugs in a local SQLite database per repository, with multilingual semantic search, duplicate detection, and coverage, drift and traceability reports. It never calls an LLM.

| Surface | Operations |
|---|---|
| MCP server (`overseer mcp`) | `find_similar`, `check_coverage`, `create_item` (up to 50 items per call), `update_item`, `get_item`, `list_items`, `search`, `link_items`, `delete_item`, `report`, `export_report`, `import_items`, `open_board` |
| CLI only, with `--json` | `worktree start/finish/list`, `pr draft/opened`, `sync` (connections, source of truth, outbox, plan, pull, confirm), `config`, `doctor` |
| Web board | kanban, requirements table, coverage, on `127.0.0.1:4477` |

The package publishes only a command; it has no library entry point. overseer is off in every project until someone runs `overseer enable`, and its instructions tell agents not to run that command unless the user asks.

## Decision

1. overseer owns the planning data: requirement items (projected from the SRS, [ADR 0002](0002-foundation-documents-are-the-source-of-truth.md)), epics, stories, tasks, bugs, their links, coverage and history. kodrax keeps no second copy; it may cache data for display.
2. kodrax uses overseer's MCP server for item operations and its CLI with `--json` for worktrees, pull requests and sync. It doesn't reach into overseer's database or internals.
3. Harness sessions that kodrax starts receive overseer's MCP server in the ACP session request ([ADR 0007](0007-harness-connection-acp-first.md)), so installing kodrax doesn't change the user's harness configuration. `overseer install` stays available for using overseer outside kodrax.
4. The overseer capability is on by default and the user can turn it off per project ([ADR 0014](0014-optional-capabilities.md)). Turning it on runs `overseer enable` for that project; the user's choice in kodrax's setup is the explicit request that overseer's rules require.
5. kodrax declares the overseer versions it supports and checks the installed one before using it.

## Options considered

| Option | Result |
|---|---|
| MCP plus CLI | **Chosen.** Uses overseer's published interfaces, and overseer can be upgraded on its own. |
| A library export from overseer, called in process | Rejected for now. overseer doesn't publish one, and it would tie kodrax to overseer's internals and database schema. |
| A planning store inside kodrax | Rejected. It duplicates overseer and splits the truth about progress. |

## Consequences

- With overseer off, kodrax still manages documents and knowledge but has no board, coverage or drift reports, and it creates worktrees with git directly ([ADR 0012](0012-worktrees-and-sandboxes.md)).
- overseer's database is per clone. Teams share plans through overseer's GitHub or GitLab sync, not through kodrax.
- Changes kodrax needs from overseer are made in the overseer repository, starting with the spec reference from ADR 0002.
- A harness where the user also ran `overseer install` already lists an `overseer` MCP server. kodrax must not attach a second one to that harness's sessions.

## Open questions

1. **Distribution**: kodrax depends on a pinned `@kodrax/overseer` and runs its command by absolute path, or it uses a global install and only checks the version. A pinned dependency guarantees compatibility; a global install lets the user upgrade overseer alone.
