# 0010. Mastra Factory as an optional delivery backend

- **Status**: Accepted, with open questions
- **Date**: 2026-10-24
- **Scope**: how kodrax relates to Mastra Factory and how it can use it.
- **Evidence date**: 2026-10-24 (`@mastra/factory` 0.21.0).

## Context

[Mastra Factory](https://factory.mastra.ai/what-is-factory) is an open source web application that takes an issue through investigation, planning, implementation and pull request review. What matters for kodrax:

- It is a separate server project. It clones the target repository into a sandbox for each session and never edits the local checkout.
- Work comes in from GitHub, GitLab, Linear, Jira, incident.io and Slack ([Intake](https://factory.mastra.ai/using/intake)).
- Its agents call model providers, with an API key or a supported subscription sign-in, using model packs for build, plan and fast modes ([Models](https://factory.mastra.ai/configure/models)). It doesn't drive harness CLIs.
- It "doesn't rank, sequence, or filter incoming work", and GitHub epics and sub-issues "aren't modeled" ([Intake](https://factory.mastra.ai/using/intake)).
- It can be operated from outside through the `mastra` CLI (`mastra api --url <factory> factory …`), which its [skill](https://factory.mastra.ai/skill) uses to list projects, inspect work items and start runs.
- It is extensible: integrations can contribute intake, version control and tools, and boards, phases and rules are configurable ([MastraFactory API](https://factory.mastra.ai/reference/mastra-factory-api)).
- It runs on Mastra platform by default, or fully locally with libSQL storage, no auth and `LocalSandbox` ([Sandboxes](https://factory.mastra.ai/configure/sandboxes)).

## Decision

1. kodrax and Factory are complementary. kodrax covers definition (discovery, PRD, SRS, architecture, contracts) and planning (requirements into ordered epics, stories and tasks with dependencies, in overseer) before any issue exists, and verification against the foundation documents afterwards. Factory covers the execution of one issue into a reviewed pull request.
2. Factory is an optional delivery backend next to the default one, the user's harness in a worktree ([ADR 0012](0012-worktrees-and-sandboxes.md)). It stays off until the user connects a Factory.

## Consequences

- Work sent to Factory runs on Factory's model credentials, not on the user's harness subscriptions ([ADR 0009](0009-subscription-first-authentication.md)). Costs and terms differ, and the UI must show which backend did what.
- Factory needs a hosted repository and its own server, so it doesn't help a local-only project before a remote exists.
- Factory has its own approvals (Auto-start runs, Auto-approve plans, merges). kodrax maps its approval policy onto them so the user isn't asked twice ([ADR 0013](0013-approvals.md)).
- kodrax integrates only through Factory's public interfaces (issues and `mastra api`), because Factory changes quickly.

## Open questions

1. **Integration path** (proposed, in this order):
   1. *Through the issue tracker.* overseer pushes tasks to GitHub or GitLab issues (`overseer sync`). Factory picks them up, and its pull requests close them. overseer pulls the new state, and kodrax checks the result against the SRS.
   2. *Through Factory's API.* kodrax starts Factory work items in dependency order, since Factory doesn't sequence work. It relays plan approvals to its own policy and shows Factory sessions in its UI.
   3. *A Factory integration for overseer*, so Factory takes work straight from overseer, if the first two prove too indirect.
2. **Choosing the backend**: per project, per epic or per task, and who chooses (the user, or kodrax with an approval).
