# 0008. A harness CLI is kodrax's brain

- **Status**: Accepted
- **Date**: 2026-10-24
- **Scope**: where kodrax's reasoning runs (the orchestrator that talks with the user, writes documents, plans and verifies), and how kodrax keeps up with new harness vendors.

## Context

Mastra agents normally reason with a model called through an API. kodrax's users already pay for coding harness subscriptions, and the decision is to reason through those instead of adding a model bill. The harness market moves quickly: Gemini CLI stopped serving consumer plans on 2026-06-18 in favour of Antigravity CLI, and vendor terms for third-party use changed several times in 2026 ([harness landscape](../research/harness-landscape.md)).

## Decision

1. kodrax's orchestrator reasons inside a harness session, not through a Mastra model call. Mastra provides workflows, state, storage, tools and streaming; the harness provides the model and the agent loop.
2. kodrax gives the brain its tools through an MCP server: documents, knowledge search, project state, approvals and proposals. overseer's MCP server is added next to it ([ADR 0005](0005-planning-through-overseer.md)).
3. There are two roles, set per project: the **brain** (conversation, documents, planning, verification) and the **implementers** (one session per task, in a worktree). Each role can use a different harness, depending on the subscriptions the user has.
4. Deterministic steps run as Mastra workflows, not as model decisions: lifecycle phases and gates, approvals, indexing, overseer calls, worktree setup.
5. kodrax keeps a harness registry: one adapter per harness ([ADR 0007](0007-harness-connection-acp-first.md)), with its capabilities, the versions it supports and the date its vendor's terms were last checked. The [harness landscape](../research/harness-landscape.md) is the dated timeline that feeds the registry; supporting a new vendor means adding an adapter.

## Options considered

| Option | Result |
|---|---|
| A harness is the brain | **Chosen.** Uses the subscription the user already has and follows the user's choice of vendor. |
| A Mastra agent with its own model is the brain; harnesses only implement | Rejected as the default. It needs API keys and a second bill. It can return as an option once API keys are configured ([ADR 0009](0009-subscription-first-authentication.md)). |

## Consequences

- Mastra features that call a model on their own (Observational Memory's observer and reflector, model-based scorers and summaries) stay off unless the user adds an API key. Conversation continuity comes from the harness session (ACP `session/load` or `session/resume`) and from kodrax's database ([ADR 0004](0004-knowledge-database.md)).
- The brain's quality and limits follow the user's plan. kodrax has to handle rate limits, pause and resume work, and show quota use.
- kodrax's prompts must work across harnesses, so they are tested against every supported adapter.
- Every brain turn spends the user's quota, so kodrax keeps prompts small and answers from its knowledge index instead of re-reading files.
