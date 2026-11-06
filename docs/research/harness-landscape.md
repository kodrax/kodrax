# Harness landscape

- **Evidence date**: 2026-10-24, unless a row says otherwise.
- **Purpose**: the dated record of the coding harnesses kodrax can drive, how it can drive them and what their vendors allow. It feeds kodrax's harness registry ([ADR 0008](../decisions/0008-harness-as-the-brain.md)). Update it whenever a vendor changes its CLI, its protocol support or its terms.

## Harnesses

| Harness | Vendor | ACP | Other ways to drive it | Subscription use when another program drives it | Own sandbox or permission modes | Priority |
|---|---|---|---|---|---|---|
| Claude Code (2.1.296 on the owner's machine) | Anthropic | Adapter: [`@agentclientprotocol/claude-agent-acp`](https://github.com/agentclientprotocol/claude-agent-acp), built on the Claude Agent SDK | Claude Agent SDK, `claude -p` | Allowed when the user signs in to the unmodified binary through Anthropic's flow ([ADR 0009](../decisions/0009-subscription-first-authentication.md)) | Seatbelt (macOS), Bubblewrap (Linux) | First release |
| omp (18.12.6 on the owner's machine) | Open source (oh-my-pi) | Native: `omp acp` | `omp -p`, `--mode json`, `--mode rpc`; `omp usage` reports provider limits | omp signs in to model providers itself (`omp login`), so each model vendor's terms apply; see ADR 0009, open question 3 | `--approval-mode` (`always-ask`, `always-ask-cached`, `write`, `yolo`); sandbox not checked | First release |
| Codex CLI | OpenAI | Adapter: [`codex-acp`](https://github.com/agentclientprotocol/codex-acp), which starts the Codex app-server | Codex app-server, `codex exec` | Supported through [Sign in with ChatGPT](https://developers.openai.com/siwc/token-sharing-open-source/codex-app-server) | `read-only`, `workspace-write`, `danger-full-access` | Next |
| Antigravity CLI (`agy`) | Google | Community adapter only ([`agy-acp`](https://github.com/shindgew/agy-acp)) | [Headless mode](https://antigravity.google/docs/cli/headless) (`agy -p`), Antigravity SDK | Not verified. Google prohibits third-party software from using Gemini CLI's OAuth | Terminal sandbox setting | Next |
| Gemini CLI | Google | Native (`--acp`) | Headless mode | Consumer plans not served since 2026-06-18; Code Assist Standard and Enterprise, and paid API keys only | Not checked | Next, for enterprise and API-key users |
| GitHub Copilot CLI | GitHub | Native, [public preview since 2026-01-28](https://github.blog/changelog/2026-01-28-acp-support-in-copilot-cli-is-now-in-public-preview/) | | Not checked | Not checked | Next |
| Cursor CLI | Cursor | Native | Cursor SDK (`@mastra/cursor`) | Not checked | Not checked | Next |
| OpenCode, Cline, Goose, Kiro CLI, Qwen Code, Kimi CLI, Junie, Factory Droid, Augment Code, Mistral Vibe, OpenHands | Various | Native ([ACP agents list](https://agentclientprotocol.com/get-started/agents)) | | Not checked | Not checked | Generic ACP adapter, without per-vendor testing |

The first release supports the two harnesses on the owner's development machine, Claude Code and omp (owner, 2026-10-24). The other priorities are a proposal: "Next" follows the first release, and the generic adapter works with any ACP agent on a best-effort basis.

## Timeline

| Date | Event | Source |
|---|---|---|
| 2026-01-28 | ACP support in GitHub Copilot CLI enters public preview. | [GitHub changelog](https://github.blog/changelog/2026-01-28-acp-support-in-copilot-cli-is-now-in-public-preview/) |
| 2026-02 | Anthropic clarifies that subscription OAuth is only for Claude Code and its own apps, after blocking third-party harnesses such as OpenCode from using it. | [The Register, 2026-02-20](https://www.theregister.com/software/2026/02/20/anthropic-clarifies-ban-on-third-party-tool-access-to-claude/5014546), [summary](https://zylos.ai/en/research/2026-07-13-subscription-auth-boundaries-official-cli-agent-automation) |
| 2026-05-19 | Google announces that Gemini CLI is being replaced by Antigravity CLI. | [Google Developers Blog](https://developers.googleblog.com/an-important-update-transitioning-gemini-cli-to-antigravity-cli) |
| 2026-06-15 | Anthropic pauses its planned change to how the Agent SDK and `claude -p` count against subscriptions. | [Claude help center](https://support.claude.com/en/articles/15036540-use-the-claude-agent-sdk-with-your-claude-plan) |
| 2026-06-18 | Gemini CLI stops serving Google AI Pro, Ultra and free Code Assist users. | [Google Developers Blog](https://developers.googleblog.com/an-important-update-transitioning-gemini-cli-to-antigravity-cli) |
| 2026-10-07 | Anthropic: the Agent SDK, `claude -p` and third-party apps can still use subscription limits; Max and Team plans include monthly API credits that cover the Agent SDK and `claude -p`. | [Claude help center](https://support.claude.com/en/articles/15036540-use-the-claude-agent-sdk-with-your-claude-plan) |
| 2026-10-11 | `@mastra/core` 1.76.0 released; `@mastra/acp`, workspaces, sandboxes and the Harness features are available. | [Mastra release](https://github.com/mastra-ai/mastra/releases/tag/%40mastra/core%401.76.0) |

## What to watch

- Changes to each vendor's terms for subscription use by programs that drive its CLI, and Anthropic's position on third-party harnesses such as omp signing in to Claude.
- ACP support in Antigravity CLI.
- ACP features kodrax relies on: session resume and close, plans, diffs, MCP servers over HTTP.
- New harnesses on the [ACP agents list](https://agentclientprotocol.com/get-started/agents).
