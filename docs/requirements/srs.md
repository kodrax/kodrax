# kodrax: software requirements specification

- **Status**: Draft 0.1. Every requirement below has the status `proposed`.
- **Date**: 2026-10-24
- **Product requirements**: [PRD](../product/prd.md)
- **Decisions**: [decision records](../decisions/README.md)

This SRS states what kodrax must do, as requirements that can be checked. It follows the structure of ISO/IEC/IEEE 29148. It is the source of truth for kodrax's requirements ([ADR 0002](../decisions/0002-foundation-documents-are-the-source-of-truth.md)): overseer's requirement items are projected from it, and code and tests are checked against it.

## 1. Introduction

### 1.1 Scope

The first release of kodrax: the `kodrax` command, the local kodrax server and UI, the harness adapters for Claude Code and omp, and kodrax's use of overseer, git and GitHub. What is out of scope is listed in the [PRD, section 5.4](../product/prd.md#54-out-of-scope-for-the-first-release).

### 1.2 Requirement format

This format is a contract. kodrax writes the requirements of the projects it manages in it (FR-DOC-011) and parses it to project requirements into overseer (FR-PLN-003).

Each requirement is one block:

```markdown
#### FR-ABC-001: Short title

When <trigger>, kodrax shall <response>.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0003 · **Open issue**: 7
- **Acceptance criteria**:
  1. Given <context>, when <action>, then <observable result>.
```

| Field | Rule |
|---|---|
| ID | `FR-<AREA>-<NNN>` for functional requirements and `NFR-<AREA>-<NNN>` for non-functional ones. An ID is never reused or renumbered: a requirement that moves to another area keeps its ID, and a removed one stays with the status `obsolete`. |
| Title | Short, after the ID and a colon. |
| Statement | One EARS sentence with one "shall" (patterns below). |
| Priority | `must`, `should`, `could` or `wont`, the values overseer uses. |
| Status | The state of the definition: `proposed`, `approved`, `deferred`, `rejected` or `obsolete`. Delivery status (`implemented`, `verified`) belongs to overseer and is never written here (ADR 0002). |
| Verification | `test` (automated), `demonstration` (run and observe), `inspection` (review of code, configuration or documents) or `analysis` (calculation or model). |
| Source | Where the requirement comes from: an ADR, a PRD section, or the owner and a date. |
| Acceptance criteria | Given/When/Then statements. One criterion may be written inline after the label. Non-functional requirements give a fit criterion instead. |
| Category, fit criterion | Non-functional requirements only, on their own line: the ISO/IEC 25010 characteristic, and the measurable condition that satisfies the requirement. |
| Optional fields | **Rationale**, **Depends on** (IDs), **Open issue** (a number from section 6). |

Parsing: a requirement starts at a level-4 heading that matches `^#### ((?:FR|NFR)-[A-Z]{2,4}-\d{3}): (.+)$` and ends at the next heading. Fields are the `- **Name**: value` items, separated by ` · ` on the same line. Fenced code blocks are ignored.

EARS patterns:

| Pattern | Form |
|---|---|
| Ubiquitous | The <system> shall <response>. |
| Event-driven | When <trigger>, the <system> shall <response>. |
| State-driven | While <state>, the <system> shall <response>. |
| Unwanted behaviour | If <condition>, then the <system> shall <response>. |
| Optional feature | Where <feature is included>, the <system> shall <response>. |

Areas:

| Code | Area | Code | Area |
|---|---|---|---|
| LIF | Lifecycle | PLN | Planning |
| INS | Installation and project state | DLV | Delivery |
| RUN | Runtime | VER | Verification |
| CFG | Settings and capabilities | REP | Repositories |
| HAR | Harnesses | CSEC | Code security |
| CHT | Conversation | APR | Approvals and audit |
| DOC | Documents | UI | User interface |
| KNW | Knowledge | | |

Non-functional areas follow ISO/IEC 25010:2023: SEC security, REL reliability, PRF performance efficiency, CMP compatibility, FLX flexibility, MNT maintainability, USE interaction capability.

Projection into overseer (FR-PLN-003): each requirement becomes one overseer requirement item, tagged `srs:<ID>`, with the statement as its description and the same acceptance criteria, priority and verification method; its `source` names this file and the ID.

### 1.3 Definitions

| Term | Meaning |
|---|---|
| Approval point | A step where kodrax waits for the user unless an auto-approval rule covers it ([ADR 0013](../decisions/0013-approvals.md)). |
| Auto-approval rule | A rule the user creates that approves an approval point automatically, optionally under conditions. |
| Brain | The harness session that talks with the user, writes documents, plans and verifies ([ADR 0008](../decisions/0008-harness-as-the-brain.md)). |
| Capability | A part of kodrax that can be turned on or off per project ([ADR 0014](../decisions/0014-optional-capabilities.md)). |
| Contributor, maintainer | The two roles kodrax can take in a repository ([ADR 0015](../decisions/0015-repository-roles.md)). |
| Documentation folder | The folder that holds a project's foundation documents ([ADR 0003](../decisions/0003-documentation-folder-and-management-modes.md)). |
| Drift | A difference between the foundation documents and the code, the tests or overseer. |
| Foundation documents | PRD, SRS, decision records, architecture diagrams, contracts and schemas. |
| Harness | A coding agent CLI, such as Claude Code or omp. |
| Harness adapter | The part of kodrax that drives one harness and declares its capabilities ([ADR 0007](../decisions/0007-harness-connection-acp-first.md)). |
| Implementer | A harness session that works on one task in its own worktree. |
| Knowledge index | kodrax's database of a project's documents and code ([ADR 0004](../decisions/0004-knowledge-database.md)). |
| Management mode | `managed` or `propose`: whether kodrax may write documents itself (ADR 0003). |
| Project | A folder where kodrax is set up, with its repository. |
| Projection | overseer's requirement items, created and kept up to date from the SRS. |
| Proposal | A document change waiting for the user's acceptance. |
| Task worktree | The git worktree and branch where one task is implemented. |

### 1.4 References

- [PRD](../product/prd.md), [decision records](../decisions/README.md), [harness landscape](../research/harness-landscape.md).
- ISO/IEC/IEEE 29148:2018, requirements engineering. ISO/IEC 25010:2023, product quality model.
- EARS, the Easy Approach to Requirements Syntax (Mavin et al., 2009).
- [Agent Client Protocol](https://agentclientprotocol.com), [Model Context Protocol](https://modelcontextprotocol.io), [`@kodrax/overseer`](https://www.npmjs.com/package/@kodrax/overseer).

## 2. Overall description

### 2.1 Product perspective

kodrax sits between the user, the user's harness CLIs, overseer and the project's repositories, as the [PRD's context diagram](../product/prd.md#1-summary) shows. It runs as a local server and UI ([ADR 0006](../decisions/0006-local-runtime.md)).

### 2.2 Users

Pending: one person, teams, or both (open issue 1). The requirements assume one person on one machine with their own subscriptions, and the ones that would change for teams name open issue 1.

### 2.3 Constraints

- kodrax server on Mastra, kodrax UI on Next.js, both on the user's machine (ADR 0006).
- Node.js 22.18 or later, overseer's minimum.
- Projects live in git repositories.
- Harnesses are driven through ACP first, with the user's own installed binaries and sign-in (ADR 0007, ADR 0009).
- Planning goes through overseer's MCP server and command line ([ADR 0005](../decisions/0005-planning-through-overseer.md)).

### 2.4 Assumptions and dependencies

- At least one supported harness (Claude Code or omp) is installed and signed in on the user's machine.
- `@kodrax/overseer` 0.1.1 or later is installed when planning is on.
- git supports worktrees.
- Remote work needs credentials for GitHub or GitLab (open issue 3).

## 3. Functional requirements

### 3.1 Lifecycle

#### FR-LIF-001: Track the lifecycle phase

kodrax shall record each project's lifecycle phase: understanding, discovery, product definition, requirements, architecture, planning, delivery or verification.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: PRD §5.1
- **Acceptance criteria**:
  1. Given a new project, when setup finishes, then the project's phase is discovery.
  2. Given a project in any phase, when the server restarts, then the project is in the same phase.

#### FR-LIF-002: Phase gates

When the brain asks to start the next phase, kodrax shall start it only after the gate of the current phase is approved.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0013
- **Acceptance criteria**:
  1. Given an SRS whose gate isn't approved, when the brain asks to start architecture, then the phase stays at requirements and an approval request for the SRS gate is queued.
  2. Given an auto-approval rule for the SRS gate, when the brain asks to start architecture, then architecture starts and the audit log names the rule.

#### FR-LIF-003: Documents before changes

When the user asks to change a delivered product, kodrax shall update the affected foundation documents before planning the change.

- **Priority**: must · **Status**: proposed · **Verification**: demonstration · **Source**: ADR 0002
- **Acceptance criteria**: Given a delivered requirement, when the user asks to change its behaviour, then a change to the SRS, or a proposal in propose mode, exists before any overseer task for the change.

### 3.2 Installation and project state

#### FR-INS-001: Install the command

The `kodrax` npm package shall install the `kodrax` command without changing any project.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0006
- **Acceptance criteria**: Given a machine with Node.js 22.18 or later, when the user runs `npm install -g kodrax` inside a project folder, then `kodrax --version` prints the version and `git status` in that folder shows no change.

#### FR-INS-002: Set up a project

When the user runs `kodrax init` in a folder, kodrax shall set the project up from the user's answers on documentation folder, management mode, harness per role, repository role and capabilities.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0003, ADR 0008, ADR 0014, ADR 0015
- **Acceptance criteria**:
  1. Given a repository without `.kodrax/`, when `kodrax init` asks its questions, then each question offers a default.
  2. When `kodrax init` finishes, then the shared answers are in `.kodrax/project.yaml`, the personal answers are in the user's configuration folder, and the project appears in the kodrax UI.

#### FR-INS-003: New project folder

If `kodrax init` runs in a folder that isn't inside a git repository, then kodrax shall create a git repository there only after the user agrees.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: PRD §4
- **Acceptance criteria**:
  1. Given an empty folder, when the user agrees, then the folder is a git repository and the setup continues.
  2. Given an empty folder, when the user declines, then kodrax writes nothing and says that a git repository is required.

#### FR-INS-004: kodrax's own ignore file

When kodrax creates `.kodrax/` in a project, kodrax shall write a `.kodrax/.gitignore` that makes git ignore everything in `.kodrax/` except `project.yaml` and that file.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0003
- **Acceptance criteria**: Given a set-up project with a knowledge database in `.kodrax/`, when the user runs `git status`, then the only new paths under `.kodrax/` are `project.yaml` and `.gitignore`.

#### FR-INS-005: Turn kodrax off and on

When the user runs `kodrax disable` in a project, kodrax shall stop all its work in that project until the user runs `kodrax enable` there.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0014
- **Acceptance criteria**:
  1. Given running sessions in a project, when the user disables kodrax there, then the sessions stop, indexing stops and kodrax's MCP tools answer that kodrax is off.
  2. Given a disabled project, when the user runs `kodrax enable`, then kodrax works again with the data in `.kodrax/` unchanged.

#### FR-INS-006: Clones stay off

If a project contains `.kodrax/project.yaml` and the current user hasn't turned kodrax on there, then kodrax shall treat the project as off.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0014 · **Open issue**: 5
- **Acceptance criteria**: Given a fresh clone of a repository set up by someone else, when the kodrax server starts, then it starts no session, indexes nothing and writes nothing in the clone until the user runs `kodrax enable` there.

#### FR-INS-007: Remove kodrax from a project

When the user runs `kodrax remove` in a project, kodrax shall delete `.kodrax/` and unregister the project without changing the documentation folder.

- **Priority**: should · **Status**: proposed · **Verification**: test · **Source**: ADR 0003
- **Acceptance criteria**: Given a set-up project, when the user removes kodrax, then `.kodrax/` is gone, the project is no longer in the UI, and the documentation folder is byte-for-byte unchanged.

### 3.3 Runtime

#### FR-RUN-001: One server per machine

The kodrax server shall serve every project registered on the machine from one process.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0006
- **Acceptance criteria**: Given two registered projects, when the user starts kodrax, then one server process serves both and the UI lists both.

#### FR-RUN-002: Start, stop and status

The `kodrax` command shall start, stop and report the status of the kodrax server and UI.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0006
- **Acceptance criteria**:
  1. When the user runs `kodrax start`, then the server and the UI run and the UI's local address is printed.
  2. When the user runs `kodrax status`, then it prints each process's state, version and port, and the registered projects.
  3. When the user runs `kodrax stop`, then both processes exit and interrupted work can be resumed (NFR-REL-001).

#### FR-RUN-003: UI through the server

The kodrax UI shall read and change project data only through the kodrax server's API.

- **Priority**: must · **Status**: proposed · **Verification**: inspection · **Source**: ADR 0006
- **Acceptance criteria**: Given the UI's code, then it opens no project file, database or harness process directly.

#### FR-RUN-004: Port fallback

If the kodrax server's default port is taken, then kodrax shall use the next free port in its range and report it.

- **Priority**: should · **Status**: proposed · **Verification**: test · **Source**: ADR 0006 · **Open issue**: 11
- **Acceptance criteria**: Given the default port in use, when the user runs `kodrax start`, then the server listens on the next free port of the range and `kodrax status` shows it.

### 3.4 Settings and capabilities

#### FR-CFG-001: Shared and personal settings

kodrax shall store shared project settings in `.kodrax/project.yaml` and personal settings in the user's configuration folder.

- **Priority**: must · **Status**: proposed · **Verification**: inspection · **Source**: ADR 0003
- **Acceptance criteria**: Given a set-up project, then `.kodrax/project.yaml` holds the documentation folder, management mode, capabilities, approval defaults and repository roles, and holds no harness account, API key or personal auto-approval rule.

#### FR-CFG-002: Turn capabilities on and off

kodrax shall let the user turn each capability on or off per project, from the UI and from the command line.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0014
- **Acceptance criteria**: Given the knowledge index on, when the user turns it off from the command line, then the UI shows it off and indexing stops.

#### FR-CFG-003: Capability defaults

When kodrax sets a project up, kodrax shall turn on documentation management, the knowledge index, planning with overseer, repository management and code security, and leave the other capabilities off.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0014
- **Acceptance criteria**: Given a project set up with every default, then `.kodrax/project.yaml` lists exactly those five capabilities as on, and sandboxes, auto-approvals, the Mastra Factory backend and API-key features as off.

#### FR-CFG-004: Off removes tools

While a capability is off, kodrax shall leave that capability's tools out of every harness session it starts.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0014
- **Acceptance criteria**: Given planning off, when a brain session starts, then the session's tool list has no overseer tool.

#### FR-CFG-005: Capability dependencies

If the user turns on a capability whose dependencies are off, then kodrax shall refuse the change and name the dependencies.

- **Priority**: should · **Status**: proposed · **Verification**: test · **Source**: ADR 0014
- **Acceptance criteria**: Given repository management off, when the user turns on the Mastra Factory backend, then the change is refused and the message names repository management.

### 3.5 Harnesses

#### FR-HAR-001: First-release harnesses

kodrax shall support Claude Code and omp as harnesses for both the brain and the implementer roles.

- **Priority**: must · **Status**: proposed · **Verification**: demonstration · **Source**: owner, 2026-10-24
- **Acceptance criteria**: Given Claude Code and omp installed and signed in, when each is assigned to each role in turn, then the brain answers in the chat and an implementer completes a task in its worktree.

#### FR-HAR-002: Detect harnesses

kodrax shall detect the supported harness CLIs installed on the machine and report their versions.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0008
- **Acceptance criteria**: Given `claude` and `omp` on the user's `PATH`, when the user runs `kodrax doctor`, then both are listed with their versions and, where the harness reports it, whether it is signed in.

#### FR-HAR-003: Harness per role

kodrax shall let the user choose, per project, the harness for the brain and the harness for implementers.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0008
- **Acceptance criteria**: Given Claude Code as the brain and omp as the implementer, when a task starts, then the brain session runs Claude Code and the task's session runs omp.

#### FR-HAR-004: ACP first

Where a harness supports ACP, kodrax shall drive it through ACP over stdio.

- **Priority**: must · **Status**: proposed · **Verification**: inspection · **Source**: ADR 0007
- **Acceptance criteria**: Given the adapters for Claude Code and omp, then both open their sessions with the ACP `initialize` and `session/new` requests.

#### FR-HAR-005: Vendor fallback

Where a harness lacks an ACP feature kodrax needs, kodrax shall use that vendor's own SDK or protocol for that feature.

- **Priority**: should · **Status**: proposed · **Verification**: inspection · **Source**: ADR 0007
- **Acceptance criteria**: Given an adapter that uses a vendor SDK or protocol, then its capability declaration names each feature it gets that way.

#### FR-HAR-006: The user's own binaries

kodrax shall run the harness binary the user installed, unmodified.

- **Priority**: must · **Status**: proposed · **Verification**: inspection · **Source**: ADR 0009 · **Open issue**: 10
- **Acceptance criteria**: Given kodrax's published packages, then they contain no copy of a harness CLI, and each adapter starts the binary found on the user's `PATH` or at the path the user set.

#### FR-HAR-007: Sign-in through the harness

When a harness reports that it isn't signed in, kodrax shall hand the user to that harness's own sign-in flow.

- **Priority**: must · **Status**: proposed · **Verification**: demonstration · **Source**: ADR 0009
- **Acceptance criteria**: Given a signed-out harness, when kodrax needs a session, then kodrax shows the harness's sign-in instructions or starts its ACP `authenticate` method, and no kodrax screen asks for vendor credentials.

#### FR-HAR-008: kodrax and overseer tools in sessions

When kodrax starts a harness session, kodrax shall attach its own MCP server to the session, and overseer's MCP server while planning is on.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0005, ADR 0008
- **Acceptance criteria**:
  1. Given planning on, when a session starts, then the session lists kodrax's tools and overseer's tools, each once (NFR-CMP-002).
  2. When a session starts, then no harness configuration file on the machine changes.

#### FR-HAR-009: Adapter capabilities

Each harness adapter shall declare which harness capabilities it supports, from the list in ADR 0007.

- **Priority**: must · **Status**: proposed · **Verification**: inspection · **Source**: ADR 0007
- **Acceptance criteria**: Given an adapter without session resume, when the user reopens a project, then kodrax starts a new brain session and says why.

#### FR-HAR-010: Concurrent sessions

kodrax shall run no more concurrent sessions per harness account than the limit the user set.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0009
- **Acceptance criteria**: Given a limit of 2 and three tasks ready, when delivery runs, then two implementer sessions run and the third task waits.

#### FR-HAR-011: Usage limits

When a harness reports a usage or rate limit, kodrax shall pause the work that depends on that harness and show when it can resume.

- **Priority**: should · **Status**: proposed · **Verification**: test · **Source**: ADR 0009
- **Acceptance criteria**: Given a harness that reports a rate limit with a reset time, then its queued tasks show that time and start after it.

#### FR-HAR-012: API keys

Where the user gives an API key for a harness, kodrax shall pass the key only to that harness's process.

- **Priority**: should · **Status**: proposed · **Verification**: test · **Source**: ADR 0009
- **Acceptance criteria**: Given an API key for omp, when an omp session starts, then the key is in that process's environment and in no other process, project file or log (NFR-SEC-004).

#### FR-HAR-013: Any ACP agent

kodrax shall let the user add an ACP agent as a harness by giving its command and arguments.

- **Priority**: could · **Status**: proposed · **Verification**: demonstration · **Source**: ADR 0007
- **Acceptance criteria**: Given `opencode acp` added as a harness, when it is assigned to a role, then its sessions run with kodrax's tools attached.

#### FR-HAR-014: Vendor terms record

Each harness adapter shall record the vendor terms it was checked against and the date of the check.

- **Priority**: should · **Status**: proposed · **Verification**: inspection · **Source**: ADR 0008, ADR 0009
- **Acceptance criteria**: Given any supported adapter, then its manifest names the vendor's terms pages and a check date no older than the adapter's release.

### 3.6 Conversation

#### FR-CHT-001: Chat with the brain

The kodrax UI shall provide a chat with each project's brain session.

- **Priority**: must · **Status**: proposed · **Verification**: demonstration · **Source**: PRD §5.2
- **Acceptance criteria**: Given a set-up project, when the user sends a message in the chat, then the brain's answer streams into the chat.

#### FR-CHT-002: Live activity

While a harness session works, the kodrax UI shall show the session's messages, tool calls, plans and file changes as they happen.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0007
- **Acceptance criteria**: Given a session that edits a file, then the UI shows the tool call and its diff before the session ends.

#### FR-CHT-003: Continue the conversation

When the user reopens a project whose brain harness supports session resume, kodrax shall resume the previous brain session.

- **Priority**: should · **Status**: proposed · **Verification**: test · **Source**: ADR 0008
- **Acceptance criteria**: Given a brain session on a harness that supports resume, when the server restarts and the user reopens the project, then the chat continues with the earlier history.

### 3.7 Documents

#### FR-DOC-001: Documentation folder

kodrax shall keep a project's foundation documents in the documentation folder set in the project settings.

- **Priority**: must · **Status**: proposed · **Verification**: inspection · **Source**: ADR 0003
- **Acceptance criteria**: Given a set-up project, then every document kodrax created is inside the documentation folder.

#### FR-DOC-002: Move the documentation folder

When the user changes the documentation folder, kodrax shall move its documents and update the links to them in one change.

- **Priority**: should · **Status**: proposed · **Verification**: test · **Source**: ADR 0003
- **Acceptance criteria**: Given links between documents, when the folder moves, then every relative link that resolved before still resolves.

#### FR-DOC-003: Management mode

kodrax shall let the user choose managed or propose mode per project and switch modes at any time.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0003
- **Acceptance criteria**: Given a project in managed mode, when the user switches to propose mode, then the next document change kodrax makes is a proposal.

#### FR-DOC-004: Propose mode

While a project is in propose mode, kodrax shall change files in the documentation folder only by applying proposals the user accepted.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0003
- **Acceptance criteria**:
  1. Given propose mode, when the brain changes a requirement, then a pending proposal exists and the SRS file is unchanged.
  2. Given a pending proposal, when the user accepts it, then kodrax applies exactly the proposed diff.

#### FR-DOC-005: Managed mode

While a project is in managed mode, kodrax shall apply document changes within the approval policy.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0003, ADR 0013 · **Open issue**: 1, 8
- **Acceptance criteria**: Given managed mode and no rule for structural changes, when the brain moves a document, then an approval request is queued and the file stays in place until it is approved.

#### FR-DOC-006: Proposal content

kodrax shall present each document proposal with its diff, its reason and the requirements or decisions it affects.

- **Priority**: must · **Status**: proposed · **Verification**: demonstration · **Source**: ADR 0003
- **Acceptance criteria**: Given a proposal that changes FR-DOC-004, then the proposal shows the diff, the reason and FR-DOC-004.

#### FR-DOC-007: Document identity

kodrax shall give each document it creates front matter with the document's type and a stable identifier.

- **Priority**: must · **Status**: proposed · **Verification**: inspection · **Source**: ADR 0003
- **Acceptance criteria**: Given a document kodrax created, when it is moved or renamed, then kodrax still finds it by its identifier.

#### FR-DOC-008: Map existing documentation

When kodrax is set up in a project that already has documentation, kodrax shall map the existing files to the PRD, requirements, decisions and contracts with the user's confirmation.

- **Priority**: must · **Status**: proposed · **Verification**: demonstration · **Source**: ADR 0003
- **Acceptance criteria**: Given a project with `docs/adr/` and `docs/api.yaml`, when kodrax is set up, then it proposes them as decisions and contracts, and records the mapping the user confirms.

#### FR-DOC-009: Documents kodrax didn't create

kodrax shall change a document it didn't create only through a proposal, until the user hands that document over.

- **Priority**: should · **Status**: proposed · **Verification**: test · **Source**: ADR 0003 · **Open issue**: 7
- **Acceptance criteria**: Given managed mode and a document the user wrote, when the brain edits it, then a proposal is created and the file is unchanged.

#### FR-DOC-010: Document types

kodrax shall create and maintain PRDs, SRSs, decision records, architecture diagrams, contracts and schemas.

- **Priority**: must · **Status**: proposed · **Verification**: demonstration · **Source**: ADR 0002
- **Acceptance criteria**: Given a new project that has gone through architecture, then the documentation folder holds a PRD, an SRS, decision records, architecture diagrams and the contracts the architecture names.

#### FR-DOC-011: Requirement format

kodrax shall write requirements in the format of section 1.2 of this SRS.

- **Priority**: must · **Status**: proposed · **Verification**: inspection · **Source**: ADR 0002
- **Acceptance criteria**: Given an SRS kodrax wrote, when kodrax's parser reads it, then every requirement block parses without errors.

#### FR-DOC-012: Decision records

When the user takes a decision that changes a project's scope or architecture, kodrax shall record it as a decision record with its context, options and consequences.

- **Priority**: must · **Status**: proposed · **Verification**: demonstration · **Source**: ADR 0002
- **Acceptance criteria**: Given a conversation in which the user picks a database, then a new decision record names the options discussed and the one chosen.

#### FR-DOC-013: Default layout

Where the documentation folder has no structure, kodrax shall organise documents in the default layout.

- **Priority**: should · **Status**: proposed · **Verification**: inspection · **Source**: ADR 0003 · **Open issue**: 6
- **Acceptance criteria**: Given an empty documentation folder, when kodrax writes the first PRD, then it is in the layout's product folder.

#### FR-DOC-014: Diagrams as text

kodrax shall write diagrams as Mermaid inside Markdown documents.

- **Priority**: should · **Status**: proposed · **Verification**: inspection · **Source**: ADR 0003 · **Open issue**: 6
- **Acceptance criteria**: Given a diagram kodrax wrote, then it parses with the Mermaid parser.

#### FR-DOC-015: Documents from code

When kodrax is set up in a project that has code but no foundation documents, kodrax shall offer to draft the documents from the code.

- **Priority**: should · **Status**: proposed · **Verification**: demonstration · **Source**: PRD §5.2
- **Acceptance criteria**: Given a repository with code and no PRD or SRS, when setup finishes, then kodrax offers to draft them, and the drafts reference the code they were derived from.

### 3.8 Knowledge

#### FR-KNW-001: Index the documents

kodrax shall index every file in the documentation folder.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0004
- **Acceptance criteria**: Given a documentation folder with ten files, when indexing finishes, then a search for a sentence from each file finds that file.

#### FR-KNW-002: Index the code

kodrax shall index the project's source files that git doesn't ignore.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0004
- **Acceptance criteria**: Given a file listed in `.gitignore`, then the index has no chunk from it.

#### FR-KNW-003: File versions

kodrax shall record, for every indexed chunk, the version of the file it came from.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0004
- **Acceptance criteria**: Given an indexed document, then each of its chunks carries the file's content hash, and the commit when the file is committed.

#### FR-KNW-004: Follow changes

When an indexed file changes, kodrax shall re-index that file before using it in an answer.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0004
- **Acceptance criteria**: Given a requirement edited after indexing, when the brain searches for it, then the result shows the new text and the new version.

#### FR-KNW-005: Rebuild from the repository

When the user asks to rebuild the index, kodrax shall rebuild it from the repository's files alone.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0004
- **Acceptance criteria**: Given a deleted knowledge database, when the user rebuilds, then the same documents and code are indexed, and only conversations and run history are lost.

#### FR-KNW-006: Hybrid search

kodrax shall answer knowledge searches by combining keyword and vector retrieval.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0004 · **Open issue**: 9
- **Acceptance criteria**: Given a query with an exact identifier and a query that paraphrases a requirement, then each returns the matching requirement in its first five results.

#### FR-KNW-007: Local embeddings

kodrax shall compute embeddings on the user's machine.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0004, ADR 0009 · **Open issue**: 9
- **Acceptance criteria**: Given the embedding model downloaded, when the network is off, then indexing still produces vectors.

#### FR-KNW-008: Small projects

While a project's foundation documents fit the brain session's context budget, kodrax shall give the brain the documents in full.

- **Priority**: should · **Status**: proposed · **Verification**: test · **Source**: ADR 0004
- **Acceptance criteria**: Given documents within the budget, when a brain session starts, then its initial context contains every foundation document.

#### FR-KNW-009: Large projects

While a project's foundation documents exceed the brain session's context budget, kodrax shall give the brain a project map and retrieve the rest on demand.

- **Priority**: should · **Status**: proposed · **Verification**: test · **Source**: ADR 0004
- **Acceptance criteria**: Given documents over the budget, when a brain session starts, then its initial context contains the project map and stays within the budget.

#### FR-KNW-010: Summaries

kodrax shall regenerate a file's summary only when the file's content changes.

- **Priority**: should · **Status**: proposed · **Verification**: test · **Source**: ADR 0004
- **Acceptance criteria**: Given an unchanged file with a summary, when the project is re-indexed, then no harness call is made for that file.

#### FR-KNW-011: Cite sources

When kodrax answers from the index, kodrax shall cite the file and the version it used.

- **Priority**: should · **Status**: proposed · **Verification**: demonstration · **Source**: ADR 0004
- **Acceptance criteria**: Given a question answered from the SRS, then the answer names the SRS and the version of the file it read.

### 3.9 Planning

#### FR-PLN-001: Turn overseer on

When planning is turned on for a project, kodrax shall turn overseer on for that project.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0005
- **Acceptance criteria**: Given overseer off for the project, when the user turns planning on in kodrax, then `overseer doctor` reports the project as on.

#### FR-PLN-002: Supported overseer versions

If the installed overseer version isn't supported, then kodrax shall turn planning off and name the supported versions.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0005 · **Open issue**: 15
- **Acceptance criteria**: Given an unsupported overseer version, when a session starts, then it has no overseer tools and the UI names the supported versions.

#### FR-PLN-003: Requirements into overseer

When an SRS requirement is added or changed, kodrax shall create or update its overseer requirement item.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0002
- **Acceptance criteria**:
  1. Given a new SRS requirement, then overseer has one requirement item tagged `srs:<ID>` whose description, acceptance criteria, priority and verification method match the SRS.
  2. Given a changed statement, then that item is updated and no second item exists.

#### FR-PLN-004: Rebuild the projection

When the user asks, kodrax shall rebuild overseer's requirement items from the SRS.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0002
- **Acceptance criteria**: Given a fresh clone with an empty overseer registry, when the projection is rebuilt, then each SRS requirement has exactly one overseer item.

#### FR-PLN-005: Plan from the SRS

When the SRS gate is approved, kodrax shall propose overseer epics, stories and tasks that implement the approved requirements, with their dependencies.

- **Priority**: must · **Status**: proposed · **Verification**: demonstration · **Source**: ADR 0005 · **Open issue**: 1
- **Acceptance criteria**: Given a proposed plan, then each story or task implements at least one requirement, and each dependency is a `depends_on` link.

#### FR-PLN-006: Plan the applications

When a new project's architecture is approved, kodrax shall plan the creation of each application the architecture defines.

- **Priority**: should · **Status**: proposed · **Verification**: demonstration · **Source**: PRD §4
- **Acceptance criteria**: Given an architecture with a frontend, an API and a message consumer, then the plan has a task to create each one.

#### FR-PLN-007: overseer interfaces

kodrax shall use overseer's MCP server for item operations and overseer's command line with `--json` for worktrees, pull requests and sync.

- **Priority**: must · **Status**: proposed · **Verification**: inspection · **Source**: ADR 0005
- **Acceptance criteria**: Given kodrax's code, then it reaches overseer only through those two interfaces and never opens `.overseer/overseer.db`.

#### FR-PLN-008: Planning off

While planning is off, kodrax shall keep its document and knowledge features working.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0005
- **Acceptance criteria**: Given planning off, then the board, coverage and drift views are hidden, and documents, proposals and search still work.

#### FR-PLN-009: Coverage and drift

While planning is on, kodrax shall show overseer's coverage and drift reports for the project.

- **Priority**: should · **Status**: proposed · **Verification**: demonstration · **Source**: ADR 0005
- **Acceptance criteria**: Given a requirement whose tasks are all done, then the coverage view shows it as delivered.

### 3.10 Delivery

#### FR-DLV-001: Worktree per task

When an implementer starts a task, kodrax shall create or reuse a git worktree and branch for that task.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0012
- **Acceptance criteria**:
  1. Given planning on, then the worktree comes from `overseer worktree start`.
  2. Given planning off, then kodrax creates it with git, using the same branch pattern.

#### FR-DLV-002: Never the main checkout

kodrax shall start implementer sessions only inside task worktrees.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0012
- **Acceptance criteria**: Given any implementer session, then its working directory is a task worktree, and the main checkout has no change from it.

#### FR-DLV-003: Task context

When kodrax starts an implementer session, kodrax shall give it the task, its parent story and the linked requirements with their acceptance criteria.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0005
- **Acceptance criteria**: Given a task linked to FR-DOC-004, when its session starts, then the session's first prompt contains the task, its story and FR-DOC-004 with its acceptance criteria.

#### FR-DLV-004: Dependencies first

kodrax shall start a task only after the tasks it depends on are done, unless the user approves stacking it on their branch.

- **Priority**: should · **Status**: proposed · **Verification**: test · **Source**: ADR 0005
- **Acceptance criteria**: Given task B that depends on an unfinished task A, then B waits, unless the user approves starting B on A's branch.

#### FR-DLV-005: Pull request per task

When an implementer finishes a task, kodrax shall open a pull request that links the task and its requirements.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0012, ADR 0015
- **Acceptance criteria**:
  1. Given a repository with a remote, then a pull request exists whose body names the task and its requirements.
  2. Given a repository without a remote, then the branch stays for review and the task moves to review.

#### FR-DLV-006: Clean up after merge

When a task's pull request is merged, kodrax shall remove the task's worktree and mark the task done.

- **Priority**: should · **Status**: proposed · **Verification**: test · **Source**: ADR 0012
- **Acceptance criteria**: Given a merged pull request, then the task's worktree and branches are gone and the task is done in overseer.

#### FR-DLV-007: Sandboxed tasks

Where sandboxes are on for a task, kodrax shall run the task's implementer inside the harness's own sandbox, or inside a local container when the harness has none.

- **Priority**: should · **Status**: proposed · **Verification**: test · **Source**: ADR 0012
- **Acceptance criteria**: Given a sandboxed task with network access blocked, when its implementer tries to reach the network, then the attempt fails.

#### FR-DLV-008: Mastra Factory backend

Where a Mastra Factory is connected, kodrax shall let the user send a task to Factory instead of a local implementer.

- **Priority**: could · **Status**: proposed · **Verification**: demonstration · **Source**: ADR 0010
- **Acceptance criteria**: Given a connected Factory, when the user sends a task there, then Factory receives it as a work item and kodrax shows its status.

#### FR-DLV-009: Who worked on a task

kodrax shall record, for each task, the backend, harness and model that worked on it.

- **Priority**: should · **Status**: proposed · **Verification**: inspection · **Source**: ADR 0010
- **Acceptance criteria**: Given a finished task, then its record names the backend, the harness and the model.

### 3.11 Verification

#### FR-VER-001: SRS against overseer

kodrax shall report the SRS requirements whose overseer items are missing or differ from the SRS.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0002
- **Acceptance criteria**: Given an overseer item whose acceptance criteria were edited on the board, then the report lists that requirement and the field that differs.

#### FR-VER-002: Evidence for acceptance criteria

When a task's pull request is ready for review, kodrax shall report the acceptance criteria of its requirements that have no passing test or recorded evidence.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0002
- **Acceptance criteria**: Given a requirement with two acceptance criteria and a test for one of them, then the report lists the other one.

#### FR-VER-003: Contracts against code

Where a project has contracts, kodrax shall report the differences between the contracts and the code.

- **Priority**: should · **Status**: proposed · **Verification**: test · **Source**: ADR 0002
- **Acceptance criteria**: Given an OpenAPI contract and an endpoint missing from the code, then the report names that endpoint.

#### FR-VER-004: Drift leaves documents alone

If the code and the foundation documents disagree, then kodrax shall report the drift without changing the documents.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0002
- **Acceptance criteria**: Given code that differs from a contract, when verification runs, then a drift item is reported, and the contract changes only through an approved document change.

#### FR-VER-005: Trace requirements

kodrax shall show, for each SRS requirement, its overseer items, pull requests and tests.

- **Priority**: should · **Status**: proposed · **Verification**: demonstration · **Source**: ADR 0002
- **Acceptance criteria**: Given a delivered requirement, then its trace shows the overseer items, the merged pull requests and the tests named after its ID.

### 3.12 Repositories

#### FR-REP-001: Role per repository

kodrax shall record, for each repository it works on, whether it acts as maintainer or contributor.

- **Priority**: must · **Status**: proposed · **Verification**: inspection · **Source**: ADR 0015 · **Open issue**: 4
- **Acceptance criteria**: Given a set-up project, then `.kodrax/project.yaml` names a role for its repository.

#### FR-REP-002: Contributor role

While kodrax acts as contributor in a repository, kodrax shall deliver changes to that repository only through branches and pull requests.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0015
- **Acceptance criteria**: Given the contributor role, when delivery runs, then kodrax doesn't merge, push to the base branch or change the repository's settings.

#### FR-REP-003: Maintainer actions

While kodrax acts as maintainer in a repository, kodrax shall run each maintainer action through an approval point.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0013, ADR 0015
- **Acceptance criteria**: Given the maintainer role and no rule, when the brain wants to merge a pull request, then an approval request is queued and nothing is merged until it is approved.

#### FR-REP-004: Identity on the host

kodrax shall act on a remote repository only with the identity configured for that repository.

- **Priority**: must · **Status**: proposed · **Verification**: inspection · **Source**: ADR 0015 · **Open issue**: 3
- **Acceptance criteria**: Given a configured identity, then every push, pull request and merge kodrax makes in that repository is attributed to that identity.

#### FR-REP-005: Keep the project's structure

kodrax shall keep the repository structure a project already has.

- **Priority**: must · **Status**: proposed · **Verification**: inspection · **Source**: ADR 0015
- **Acceptance criteria**: Given an existing project, when kodrax is set up and delivers tasks, then it moves no existing file into a layout of its own.

#### FR-REP-006: Create the remote repository

When a new project has no remote repository, kodrax shall offer to create one on the host the user chooses.

- **Priority**: should · **Status**: proposed · **Verification**: demonstration · **Source**: ADR 0015
- **Acceptance criteria**: Given a new project without a remote, when the user accepts the offer and approves the action, then the repository exists on the chosen host and is the project's `origin`.

#### FR-REP-007: Several repositories

kodrax shall let a project include more than one repository.

- **Priority**: could · **Status**: proposed · **Verification**: demonstration · **Source**: ADR 0015 · **Open issue**: 4
- **Acceptance criteria**: Given a project with two repositories, then kodrax plans and delivers tasks in each with that repository's role.

### 3.13 Code security

#### FR-CSEC-001: Secret scanning

If the changes kodrax is about to commit or push contain a secret, then kodrax shall stop the commit or push and show the finding.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0014 · **Open issue**: 12
- **Acceptance criteria**: Given a change that contains a test credential, when kodrax commits, then the commit doesn't happen and the file and line are shown.

#### FR-CSEC-002: Dependency audit

When a task changes the project's dependencies, kodrax shall run a dependency audit and attach the findings to the task's pull request.

- **Priority**: should · **Status**: proposed · **Verification**: test · **Source**: ADR 0014 · **Open issue**: 12
- **Acceptance criteria**: Given a task that adds a package with a known vulnerability, then its pull request lists the advisory.

#### FR-CSEC-003: Static analysis

Where a project has a static analysis tool configured, kodrax shall run it on each task's changes before opening the pull request.

- **Priority**: should · **Status**: proposed · **Verification**: test · **Source**: ADR 0014
- **Acceptance criteria**: Given a configured linter that fails on a task's change, then the pull request isn't opened until the failure is fixed or the user approves opening it.

#### FR-CSEC-004: Least privilege

kodrax shall request only the repository permissions its role in that repository needs.

- **Priority**: should · **Status**: proposed · **Verification**: inspection · **Source**: ADR 0014, ADR 0015
- **Acceptance criteria**: Given the contributor role, then the permissions kodrax asks for exclude merging and administration.

### 3.14 Approvals and audit

#### FR-APR-001: Ask by default

kodrax shall wait for the user at every approval point that no auto-approval rule covers.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0013 · **Open issue**: 1
- **Acceptance criteria**: Given a project without rules, when kodrax reaches each approval point of ADR 0013, then each one waits for the user.

#### FR-APR-002: Approval points

kodrax shall provide the approval points listed in ADR 0013.

- **Priority**: must · **Status**: proposed · **Verification**: inspection · **Source**: ADR 0013
- **Acceptance criteria**: Given kodrax's approval settings, then every approval point of ADR 0013 is listed there.

#### FR-APR-003: Auto-approval rules

kodrax shall let the user create auto-approval rules per approval point and per project, with optional conditions.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0013 · **Open issue**: 2
- **Acceptance criteria**: Given the rule "auto-approve test runs inside worktrees", when an implementer runs the tests in its worktree, then no approval request is queued and the audit log names the rule.

#### FR-APR-004: Audit log

kodrax shall record every approval decision with its approval point, outcome, decider and time.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0013
- **Acceptance criteria**: Given one decision by the user and one by a rule, then the log holds both, naming the user and the rule.

#### FR-APR-005: Harness permission requests

When a harness asks for permission, kodrax shall answer through its approval policy.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0007, ADR 0013 · **Open issue**: 2
- **Acceptance criteria**:
  1. Given no rule, when a harness asks to run a shell command outside its worktree, then the request waits in the approval queue.
  2. Given any permission request, then kodrax never selects an option that neither the user nor a rule chose.

#### FR-APR-006: Policy changed by the user only

kodrax shall accept changes to approval rules only from the user, through kodrax's UI or command line.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0013 · **Open issue**: 2
- **Acceptance criteria**: Given a harness session, then none of kodrax's MCP tools can create, widen or delete an approval rule.

### 3.15 User interface

#### FR-UI-001: Projects overview

The kodrax UI shall list the registered projects with their on or off state, phase, running sessions and pending approvals.

- **Priority**: must · **Status**: proposed · **Verification**: demonstration · **Source**: ADR 0006
- **Acceptance criteria**: Given two projects, one off, then the overview shows both with their states.

#### FR-UI-002: Documents

The kodrax UI shall render the project's foundation documents with their Mermaid diagrams.

- **Priority**: must · **Status**: proposed · **Verification**: demonstration · **Source**: PRD §5.3
- **Acceptance criteria**: Given this PRD, then the UI shows its two diagrams rendered.

#### FR-UI-003: Proposals

The kodrax UI shall list pending document proposals and let the user accept or reject each one.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0003
- **Acceptance criteria**: Given two pending proposals, when the user rejects one, then only the other remains and the document is unchanged.

#### FR-UI-004: Approvals

The kodrax UI shall list pending approvals with the diff, command or document each one concerns and the requirement it serves.

- **Priority**: must · **Status**: proposed · **Verification**: demonstration · **Source**: ADR 0013
- **Acceptance criteria**: Given a pending shell command from an implementer, then the queue shows the command, the task and the task's requirements.

#### FR-UI-005: Sessions

The kodrax UI shall show each running harness session with its live output, file changes and commands.

- **Priority**: must · **Status**: proposed · **Verification**: demonstration · **Source**: ADR 0007
- **Acceptance criteria**: Given two running implementers, then the UI shows both sessions with their output and changes.

#### FR-UI-006: Board

While planning is on, the kodrax UI shall show the project's overseer items grouped by status.

- **Priority**: should · **Status**: proposed · **Verification**: demonstration · **Source**: ADR 0005
- **Acceptance criteria**: Given tasks in three statuses, then the board shows them in three columns.

#### FR-UI-007: Usage

The kodrax UI shall show, per harness account, the running sessions, the configured limits and the last reported usage or rate-limit state.

- **Priority**: should · **Status**: proposed · **Verification**: demonstration · **Source**: ADR 0009
- **Acceptance criteria**: Given an omp account with two running sessions and a limit of three, then the usage view shows 2 of 3 and the last usage omp reported.

#### FR-UI-008: Settings

The kodrax UI shall let the user change the project's settings and capabilities.

- **Priority**: must · **Status**: proposed · **Verification**: demonstration · **Source**: ADR 0003, ADR 0014
- **Acceptance criteria**: Given the settings page, when the user switches the management mode, then `.kodrax/project.yaml` holds the new mode.

#### FR-UI-009: Knowledge search

The kodrax UI shall let the user search the project's knowledge index.

- **Priority**: could · **Status**: proposed · **Verification**: demonstration · **Source**: ADR 0004
- **Acceptance criteria**: Given a search for a requirement's title, then the requirement is among the results with its file and version.

## 4. Non-functional requirements

### 4.1 Security

#### NFR-SEC-001: Local connections only

The kodrax server and UI shall accept connections only from the local machine.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0006
- **Category**: security · **Fit criterion**: both listen only on loopback addresses, and every test request with a non-local `Host` header is rejected.

#### NFR-SEC-002: Write token

The kodrax server shall reject state-changing requests that lack the token created at its start.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0006
- **Category**: security · **Fit criterion**: in the test suite, no state-changing endpoint accepts a request without a valid token.

#### NFR-SEC-003: No vendor credentials

kodrax shall not read, store, copy or forward harness vendor credentials.

- **Priority**: must · **Status**: proposed · **Verification**: inspection · **Source**: ADR 0009
- **Category**: security · **Fit criterion**: a file-access audit of kodrax's processes during the test suite shows no access to any harness's credential files or keychain entries.

#### NFR-SEC-004: API keys out of projects and logs

kodrax shall keep API keys out of project folders and logs.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0009
- **Category**: security · **Fit criterion**: after a full test run with a canary API key, a search of the project folders, `.kodrax/` and kodrax's logs finds the canary 0 times.

#### NFR-SEC-005: Data only to configured destinations

kodrax shall send project data only to the harnesses, hosts and services the user configured.

- **Priority**: must · **Status**: proposed · **Verification**: analysis · **Source**: ADR 0006, ADR 0009
- **Category**: security · **Fit criterion**: a network capture of kodrax's own processes during a full delivery cycle shows connections only to configured destinations.

### 4.2 Reliability

#### NFR-REL-001: Survive restarts

When the kodrax server restarts, kodrax shall resume every interrupted run or mark it as interrupted.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0006
- **Category**: reliability · **Fit criterion**: after the server is killed during 10 concurrent runs, all 10 are resumed or marked interrupted, and no approval or audit entry is lost.

#### NFR-REL-002: Contained harness failures

If a harness process exits unexpectedly, then kodrax shall mark that session as failed and keep the other sessions running.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0007
- **Category**: reliability · **Fit criterion**: killing one of three harness processes leaves the other two running, and the killed session is marked failed within 5 seconds.

### 4.3 Performance efficiency

#### NFR-PRF-001: Re-index time

When a document of up to 1 MB changes, kodrax shall re-index it within 5 seconds.

- **Priority**: should · **Status**: proposed · **Verification**: test · **Source**: ADR 0004 · **Open issue**: 13
- **Category**: performance efficiency · **Fit criterion**: 95th percentile at or under 5 seconds on the reference machine.

#### NFR-PRF-002: Search time

kodrax shall answer knowledge searches on a repository of 10,000 files within 1 second.

- **Priority**: should · **Status**: proposed · **Verification**: test · **Source**: ADR 0004 · **Open issue**: 13
- **Category**: performance efficiency · **Fit criterion**: 95th percentile at or under 1 second on the reference machine.

#### NFR-PRF-003: Streaming delay

The kodrax UI shall show a harness event within 1 second of the harness emitting it.

- **Priority**: should · **Status**: proposed · **Verification**: test · **Source**: ADR 0007 · **Open issue**: 13
- **Category**: performance efficiency · **Fit criterion**: 95th percentile delay at or under 1 second between the ACP update and its display.

### 4.4 Compatibility

#### NFR-CMP-001: Leave the project alone

When kodrax sets a project up, kodrax shall change no file outside `.kodrax/` and the documentation folder.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0003
- **Category**: compatibility · **Fit criterion**: after `kodrax init` in a clean repository, `git status --porcelain` lists only paths under those two folders.

#### NFR-CMP-002: Live with overseer's own install

kodrax shall work in harnesses where overseer was also installed with `overseer install`.

- **Priority**: should · **Status**: proposed · **Verification**: test · **Source**: ADR 0005
- **Category**: compatibility · **Fit criterion**: with overseer installed at user scope in Claude Code and omp, each kodrax session lists overseer's tools exactly once.

### 4.5 Flexibility and maintainability

#### NFR-FLX-001: Platforms

kodrax shall run on Linux and macOS with Node.js 22.18 or later.

- **Priority**: must · **Status**: proposed · **Verification**: test · **Source**: ADR 0005 · **Open issue**: 14
- **Category**: flexibility · **Fit criterion**: the test suite passes on Linux and macOS with Node.js 22.18 and with the current LTS release.

#### NFR-FLX-002: New harnesses through adapters

kodrax shall accept a new harness through a new adapter without changes to its core.

- **Priority**: should · **Status**: proposed · **Verification**: inspection · **Source**: ADR 0007, ADR 0008
- **Category**: flexibility · **Fit criterion**: adding a test adapter changes only files in the adapters package.

#### NFR-MNT-001: Pinned adapter versions

Each harness adapter shall pin the harness and ACP adapter versions it was tested with.

- **Priority**: must · **Status**: proposed · **Verification**: inspection · **Source**: ADR 0007
- **Category**: maintainability · **Fit criterion**: every adapter manifest lists its tested versions, and CI runs the adapter's tests against them.

### 4.6 Interaction capability

#### NFR-USE-001: Setup time

kodrax shall let a first-time user set it up in an existing repository within 5 minutes.

- **Priority**: could · **Status**: proposed · **Verification**: demonstration · **Source**: PRD §7 · **Open issue**: 13
- **Category**: interaction capability · **Fit criterion**: 4 of 5 first-time users finish `kodrax init` within 5 minutes in a moderated session.

## 5. External interfaces

| Interface | Between | Used for | Specified in |
|---|---|---|---|
| `kodrax` command | user → kodrax | `init`, `enable`, `disable`, `remove`, `start`, `stop`, `status`, `doctor` | sections 3.2 and 3.3 |
| kodrax HTTP API | UI → server | everything the UI shows and changes | to be written in `docs/contracts/` |
| ACP over stdio | server → harness | sessions, prompts, streaming, permission requests, sign-in | [ACP](https://agentclientprotocol.com) |
| MCP, kodrax tools | harness → server | documents, knowledge, project state, proposals | to be written in `docs/contracts/` |
| overseer MCP server | harness → overseer | planning items | [`@kodrax/overseer`](https://www.npmjs.com/package/@kodrax/overseer) |
| overseer command line | server → overseer | worktrees, pull requests, sync, doctor | [`@kodrax/overseer`](https://www.npmjs.com/package/@kodrax/overseer) |
| git | server → repository | worktrees, branches, commits | |
| GitHub and GitLab | server → host | repositories, pull requests, issues | open issue 3 |
| Mastra Factory | server → Factory | optional delivery backend | [ADR 0010](../decisions/0010-mastra-factory-optional-backend.md) |

## 6. Open issues

| # | Issue | Affects | Decided in |
|---|---|---|---|
| 1 | First users: one person, teams, or both | FR-APR-001, FR-DOC-005, FR-PLN-005 | [PRD, section 4](../product/prd.md#4-users) |
| 2 | Auto-approval tiers and the rules that apply to every tier | FR-APR-003, FR-APR-005, FR-APR-006 | ADR 0013, open question 1 |
| 3 | Identity on the host: the user's credentials or a kodrax identity | FR-REP-004 | ADR 0015, open question 1 |
| 4 | Default repository role; several repositories per project | FR-REP-001, FR-REP-007 | ADR 0015, open questions 2 and 3 |
| 5 | Clones stay off | FR-INS-006 | ADR 0014, open question 2 |
| 6 | Default documentation layout and diagram format | FR-DOC-013, FR-DOC-014 | ADR 0003, open question 1 |
| 7 | Documents kodrax didn't create | FR-DOC-009 | ADR 0003, open question 2 |
| 8 | How managed document changes reach git | FR-DOC-005 | ADR 0003, open question 3 |
| 9 | Knowledge engine and embedding model | FR-KNW-006, FR-KNW-007, NFR-PRF-001, NFR-PRF-002 | ADR 0004, open questions 1 and 2 |
| 10 | Driving the user's installed Claude Code through the ACP adapter, which is built on the Claude Agent SDK | FR-HAR-006 | a technical spike |
| 11 | Ports | FR-RUN-004 | ADR 0006, open question 1 |
| 12 | Code security tooling | FR-CSEC-001, FR-CSEC-002 | ADR 0014, open question 1 |
| 13 | Performance and setup targets, and the reference machine | NFR-PRF-001, NFR-PRF-002, NFR-PRF-003, NFR-USE-001 | this SRS |
| 14 | Windows support | NFR-FLX-001 | this SRS |
| 15 | How overseer is distributed with kodrax | FR-PLN-002 | ADR 0005, open question 1 |

## 7. Traceability

- Every requirement names its source: a decision record, a PRD section or the owner.
- Every requirement is projected into overseer as one requirement item tagged `srs:<ID>` (FR-PLN-003), where stories and tasks link to it with `implements`.
- Tests name the requirement they verify at the start of their title, for example `FR-DOC-004 keeps the SRS unchanged in propose mode`, so FR-VER-002 and FR-VER-005 can find them.
