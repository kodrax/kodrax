# 0007. Harness connection: ACP first, vendor SDK as fallback

- **Status**: Accepted
- **Date**: 2026-10-24
- **Scope**: how kodrax starts, drives and listens to the coding harness CLIs on the user's machine.
- **Evidence date**: 2026-10-24.

## Context

kodrax ships no coding agent of its own; it drives the harness CLIs the user already has ([ADR 0008](0008-harness-as-the-brain.md)). The Agent Client Protocol (ACP) is JSON-RPC over stdio between a client and a coding agent.

- **Native ACP**: omp (`omp acp`), Gemini CLI (`--acp`), GitHub Copilot CLI (public preview since 2026-01-28), Cursor CLI, OpenCode, Cline, Goose, Kiro CLI, Qwen Code, Kimi CLI, Junie and others ([ACP agents list](https://agentclientprotocol.com/get-started/agents)).
- **Through adapters**: Claude Code via [`@agentclientprotocol/claude-agent-acp`](https://github.com/agentclientprotocol/claude-agent-acp), built on the Claude Agent SDK; Codex via [`codex-acp`](https://github.com/agentclientprotocol/codex-acp), which starts the Codex app-server.
- **Without ACP**: Google's consumer CLI is now Antigravity CLI (`agy`). It documents a headless mode and an SDK; ACP exists only through a community adapter ([harness landscape](../research/harness-landscape.md)).
- `session/new` takes a working directory and the MCP servers the agent must connect to. `session/load` and `session/resume` restore sessions when the agent supports them ([ACP session setup](https://agentclientprotocol.com/protocol/v1/session-setup)).
- Mastra's [`@mastra/acp`](https://mastra.ai/docs/connections/acp) wraps an ACP agent as a subagent (`AcpAgent`) or a tool (`createACPTool()`). Mastra also wraps vendor SDKs: `@mastra/claude`, `@mastra/openai` and `@mastra/cursor` ([SDK agents](https://mastra.ai/docs/connections/sdk-agents)).

## Decision

1. kodrax connects to harnesses through ACP over stdio by default.
2. Where ACP lacks something kodrax needs for a given harness, kodrax uses that vendor's own SDK or protocol for that part: the Claude Agent SDK, the Codex app-server, or Antigravity CLI's headless mode or SDK.
3. Every path runs the vendor's official, unmodified CLI or SDK, signed in by the user through the vendor's own flow. kodrax never calls a vendor's model API with the harness's credentials and never imitates a vendor's client ([ADR 0009](0009-subscription-first-authentication.md)).
4. Each harness sits behind an adapter that declares its capabilities: creating, loading, resuming and closing sessions; streaming messages, tool calls, plans and diffs; permission requests; MCP server injection; model and mode selection; sign-in methods; sandbox controls; usage and rate-limit reporting. The rest of kodrax depends on capabilities, never on harness names.
5. kodrax answers every permission request through its own approval policy ([ADR 0013](0013-approvals.md)).

## Options considered

| Option | Result |
|---|---|
| ACP first, vendor SDK where ACP falls short | **Chosen.** One protocol for most harnesses, with a way out per vendor. |
| ACP only | Rejected. Leaves out vendors without ACP, such as Antigravity CLI today. |
| Vendor SDKs only | Rejected. One integration per vendor, and SDKs lean towards API-key billing (ADR 0009). |
| Parsing each CLI's headless output (`claude -p`, `codex exec`, `agy -p`) | Only as a per-vendor fallback. It has no sessions, permission requests or structured streaming. |

## Consequences

- `@mastra/acp` is a starting point, not the whole answer. `AcpAgent.stream()` turns only `agent_message_chunk` updates into text deltas, and by default it answers a permission request by picking the first option offered ([AcpAgent reference](https://mastra.ai/reference/acp/acp-agent)). kodrax needs tool calls, plans, diffs and permission prompts in its UI, and must never pick a permission on its own. Expect a kodrax ACP client layer, built on `@mastra/acp`'s `createClient` hook or on the ACP SDK; a spike decides which.
- Adapters change fast (the Claude Code adapter had 175 published versions on 2026-10-20), so kodrax pins adapter versions and tests each one it supports.
- Harness-specific features (skills, subagents, hooks) don't travel through ACP. kodrax's instructions travel in prompts and in its MCP tool descriptions.
