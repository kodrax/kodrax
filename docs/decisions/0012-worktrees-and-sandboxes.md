# 0012. Worktrees by default, sandboxes when required

- **Status**: Accepted
- **Date**: 2026-10-24
- **Scope**: where implementers change code and run commands, and how that work is kept away from the user's checkout and machine.
- **Evidence date**: 2026-10-24.

## Context

Implementers are harness CLIs that edit files and run commands on the user's machine. `@mastra/acp` starts them in a working directory; it doesn't isolate them ([Mastra ACP](https://mastra.ai/docs/connections/acp)). The isolation options:

| Layer | Options |
|---|---|
| Git | One worktree and branch per task. overseer's `worktree start` and `worktree finish` create, reuse and clean them up. |
| Harness sandbox | Claude Code: Seatbelt on macOS, Bubblewrap on Linux ([docs](https://code.claude.com/docs/en/sandbox-environments)). Codex: `read-only`, `workspace-write` and `danger-full-access` modes ([docs](https://developers.openai.com/codex/sandboxing)). Antigravity CLI: a terminal sandbox setting. |
| Mastra sandboxes | `LocalSandbox` with native isolation (Seatbelt or Bubblewrap, with network and path limits), Docker, Apple Container, and remote providers such as E2B, Daytona and Modal ([Mastra sandboxes](https://mastra.ai/docs/sandbox/overview)). |

## Decision

1. Every code change runs in its own git worktree and branch. Implementers never edit the user's main checkout.
2. Sandboxes are optional and off by default. The user turns them on per project or per task, or kodrax asks to turn one on when a task needs it: untrusted code or dependencies, risky commands, or work that must not reach the network.
3. When a sandbox is on, kodrax prefers, in order: the harness's own sandbox, configured by kodrax for the session; the harness CLI running inside a local container with the worktree mounted; remote sandbox providers, later.
4. Harness permission requests always go through kodrax's approval policy ([ADR 0013](0013-approvals.md)).

## Consequences

- Each worktree costs disk space and a dependency install (overseer's `worktree_setup`).
- With overseer off ([ADR 0005](0005-planning-through-overseer.md)), kodrax creates and removes worktrees with git directly, using the same branch pattern.
- A harness inside a container needs its sign-in inside the container. Each vendor's device or headless sign-in flow has to be checked before kodrax supports it.
- Mastra's sandboxes isolate the commands Mastra runs itself. They don't wrap an ACP harness process, which is why the order above starts with the harness's own sandbox.
