# 0016. kodrax is open source under Apache-2.0

- **Status**: Accepted, with open questions
- **Date**: 2026-10-24
- **Scope**: the licence and visibility of kodrax's repository and of the packages built from it.

## Context

overseer, which kodrax uses for planning, is public on GitHub under Apache-2.0. ADR 0001 files KODRAX as a word mark in classes 9 and 42. The owner decided on 2026-10-24 that kodrax is public, the same as overseer.

## Decision

1. kodrax's repository, [`kodrax/kodrax`](https://github.com/kodrax/kodrax), is public.
2. kodrax's code and the packages built from it are licensed under the Apache License 2.0.

## Options considered

| Option | Result |
|---|---|
| Apache-2.0, public | **Chosen**, to match overseer. |
| Other licences | Not considered. |

## Consequences

- The licence grants no rights to the KODRAX name or marks (Apache-2.0, section 6), so the trademark of ADR 0001 stays protected while the code is open.
- Contributors grant a patent licence for their contributions (section 3), and contributions are licensed under Apache-2.0 unless the contributor says otherwise (section 5).
- Anyone may run kodrax's code as a hosted service, though not under the KODRAX name.

## Open questions

1. **Contributions**: whether to require a Developer Certificate of Origin sign-off or a contributor licence agreement.
2. **GitLab mirror**: overseer also has a GitLab remote (`gitlab.com/kodrax/overseer`); whether kodrax is mirrored there too.
