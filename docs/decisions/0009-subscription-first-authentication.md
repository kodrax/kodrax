# 0009. Subscription first, API keys optional

- **Status**: Accepted, with open questions
- **Date**: 2026-10-24
- **Scope**: how kodrax's harness sessions are signed in and paid for.
- **Evidence date**: 2026-10-24. Vendor terms change; check them again before each release.

## Context

For the user, kodrax should work like overseer: it works with whatever subscription the user has, Claude, ChatGPT or another, with API keys as an option. There is one technical difference. overseer is called by a harness the user started, and it never talks to a model. kodrax starts the harness itself and sends it prompts, so it is a product that runs the vendor's CLI, and the vendors' terms say how that may be done:

| Vendor | Terms | Source |
|---|---|---|
| Anthropic (Claude Code) | A product may run Claude Code if the binary isn't modified, each end user signs in with their own API key, subscription or cloud provider credential, and the product doesn't pay for, resell or intermediate Claude usage. Developers may not offer Claude.ai login in their own apps, route requests through plan credentials on behalf of their users, or collect, store or intermediate Claude.ai credentials; sign-in must complete through Anthropic's own flow. End users may sign in to the unmodified Claude Code binary with their own subscription, including when a product runs it. Advertised limits "assume ordinary, individual usage". | [Legal and compliance](https://code.claude.com/docs/en/legal-and-compliance) |
| Anthropic (help center) | 2026-06-15: planned Agent SDK changes paused. 2026-10-07: "You can still use the Claude Agent SDK, `claude -p`, and third-party apps with your subscription limits"; Max and Team plans also include monthly API credits that cover them. | [Use the Claude Agent SDK with your Claude plan](https://support.claude.com/en/articles/15036540-use-the-claude-agent-sdk-with-your-claude-plan) |
| OpenAI (Codex) | Third-party apps can use "Sign in with ChatGPT": the app gets an OAuth token for the user's ChatGPT plan, passes it to the Codex app-server and identifies itself with `clientInfo`. | [Codex app-server: Sign in with ChatGPT](https://developers.openai.com/siwc/token-sharing-open-source/codex-app-server) |
| Google (Gemini CLI, Antigravity CLI) | Accessing the services behind Gemini CLI with third-party software that uses its OAuth violates the terms. Since 2026-06-18 Gemini CLI no longer serves Google AI Pro, Ultra or free Code Assist users, who move to Antigravity CLI. Gemini CLI remains for Code Assist Standard and Enterprise and for paid API keys. | [Gemini CLI terms](https://geminicli.com/docs/resources/tos-privacy/), [Google Developers Blog](https://developers.googleblog.com/an-important-update-transitioning-gemini-cli-to-antigravity-cli) |
| omp | A third-party harness that signs in to model providers itself (`omp login anthropic`, `omp login openai-codex`). Each model vendor's terms apply to that sign-in, not kodrax's. | `omp login --help` (omp 18.12.6) |

Across vendors, automating the official client under the user's own account is the designed-for path; extracting its credentials into another client is what's prohibited ([summary, 2026-07-13](https://zylos.ai/en/research/2026-07-13-subscription-auth-boundaries-official-cli-agent-automation)).

## Decision

1. By default each harness runs on the user's own subscription, signed in inside the harness through the vendor's own flow. kodrax may start that flow (for example through ACP's `authenticate` method), but it never sees, stores, copies or forwards credentials.
2. kodrax runs the harness binary the user installed, unmodified. It doesn't ship its own copy of a harness.
3. The user can give an API key per harness instead. kodrax passes it only to that harness's process and never writes it into the project.
4. One person, one subscription: kodrax never uses one person's subscription for another person's work, and never pays for, resells or intermediates model usage.
5. kodrax limits concurrent sessions per subscription, shows usage and rate-limit state, and pauses work when a limit is reached. The user sets the limits.
6. Each adapter records which vendor terms it was checked against, and when ([ADR 0008](0008-harness-as-the-brain.md)).
7. Claude Code driven through ACP on the user's own subscription meets Anthropic's conditions above, so it needs no separate confirmation from Anthropic. kodrax checks the terms again at each release. *Resolved 2026-10-24.*

## Consequences

- A hosted or team-shared kodrax can't run on one person's subscription. Each user signs in on their own machine, or the deployment uses API keys. If kodrax is ever offered as a hosted product, Anthropic's Commercial Terms apply to running Claude Code in it.
- Heavy unattended use (many parallel implementers, around the clock) can exceed "ordinary, individual usage". kodrax recommends API keys for that.
- An API key also turns on the Mastra features that need a model of their own (ADR 0008).

## Open questions

1. **Codex sign-in.** Use the sign-in the user's Codex CLI already holds, or register kodrax for "Sign in with ChatGPT" so usage is attributed to kodrax.
2. **Antigravity CLI.** Check Google's terms for driving `agy` headless from another program under a consumer plan.
3. **Claude models through omp.** omp's own Anthropic sign-in is a third-party app offering Claude.ai login, which Anthropic's legal page doesn't permit; the help center says third-party apps can use subscription limits. Until that is clear, the safe route to Claude models on a subscription is Claude Code; omp is unaffected when it uses other providers or an API key.
4. **API key storage** (proposed): the operating system's keychain.
