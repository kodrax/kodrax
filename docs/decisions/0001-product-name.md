# 0001. Product name: Kodrax

- **Status**: Accepted
- **Date**: 2026-10-11
- **Scope**: the name of the main system, the one that oversees an agentic, multi-app, multi-repo delivery system (frontend, API, GraphQL, game assets and so on) and builds those systems from conversations with users or with other systems, agents and models. It also covers the identifiers derived from the name: domains, package names, the CLI command and the trademark.
- **Evidence date**: all availability, registry and trademark checks below were run on 2026-10-10. Availability changes; run the checks again right before you register or file anything.

## Context

The system needs a name that:

1. sounds distinctive and modern, not too plain (Codalus felt too soft) and not too epic (Myrgos felt like a fantasy character);
2. is short enough to work as a CLI command and a package scope;
3. is free as a GitHub org, an npm package and scope, and on the other main package registries;
4. has good domains available. A `.com` is not required. `.ai`, `.dev` and `.sh` are acceptable, and so are domain hacks, where the extension completes the word;
5. can be registered as a trademark in Chile, where the company will be run, in Nice class 9 (downloadable software) and class 42 (SaaS, software design and development), and extended abroad later.

## Decision

The system is called **Kodrax**, pronounced **KOD-rax**.

| Identifier | Value |
|---|---|
| Product name | Kodrax |
| CLI command | `kodrax` |
| npm | `kodrax`, scope `@kodrax/*` |
| GitHub org | `kodrax` |
| Docker Hub namespace | `kodrax` |
| Main domain | `kodrax.ai` |
| Docs | `kodrax.dev` |
| Installer | `kodrax.sh` (`curl -fsSL kodrax.sh \| sh`) |
| Chile | `kodrax.cl` |
| Optional domain hack | `kodr.ax` |
| Trademark | word mark KODRAX, filed with INAPI (Chile) in classes 9 and 42 |

The word combines "code" (spelled with a K) with the hard "-ax" ending of the shortlisted names. It carries no existing meaning, which makes it easier to protect as a trademark.

## Options considered

The name was chosen over several rounds. Each round kept the sound or idea that worked and dropped the rest.

| Round | Candidates | Why they were dropped |
|---|---|---|
| Mythic and craft names | Daedalus, Demiurge, Argus, Atelier, Foundry, Loom, Mycelium, Hive | Plain words are heavily used. Daedalus is a well-known Cardano wallet; Foundry belongs to Palantir; Loom is a video app. |
| Daedalus blends | Daedalops, Codalus, Nodalus, Daedalgo | Codalus was the favourite: every domain and registry was free except a dormant GitHub user created in 2023. It sounded too plain and soft. |
| Blends of six roots (Codalus, Daedalus, Nexus, Lattice, Mycelium, Argus) | Argodalus, Sporalus, Hyphalus, Rhizalus, Lattalus | The "-alus" ending sounded soft and academic. Argodalus was rejected for its sound. |
| Shorter, harder sounds | Myrgos, Daedris, Argrix, Rhizex, Perdix, Labrys | Short real words (Perdix, Labrys, Talyx, Hypha, Nexum) were all taken. Myrgos was free everywhere but sounded too epic. Its only existing use is a dragon king in the 2014 board game *Draco Magi*. |
| Domain hacks | `daedr.is`, `myrg.us`, `rhyz.us`, `ded.ax`, `myrno.de`, `myr.dev`, `myr.sh` | `.us` needs a US citizen, resident or company, which rules it out from Chile. `myrco.de` is taken. `.de` needs a German contact address for owners outside Germany. |
| Between Myrgos and Codalus | Myrgen, Myrdus, Kodagos, Codmyr, Myrcode | Myrgen's `.com` is taken. Myrcode's GitHub user is taken. They weren't distinctive enough to beat the final shortlist. |
| Final shortlist | Myrix, Myrcore, Codris, Codryn, Kodra, Zyrn and their variants | Five were checked in depth; see the next table. |

### Final five

| Name | Result | Reason |
|---|---|---|
| **Kodrax** | **Chosen** | No product or company uses the name. Free on every package registry and on GitHub. Only `.com` is taken. No identical trademark in WIPO or INAPI. |
| Codris | Rejected | CODRIS is an existing French software company (codris.fr; transport and construction software), so a trademark clash is possible in the EU. `codris.com` is for sale on Afternic. The GitHub org `codris` is taken (empty, created 2020). Docker Hub is taken. |
| Kodrix | Rejected | Kodrix Technologies sells AI software (kodrixtech.com). Kodrix IDE is an Android IDE with 15 GitHub stars. There is also a Kodrix Web Agency. `.com`, `.dev` and `.co` are taken. |
| Zyrnex | Rejected | Zyrnex AI is a funded company, founded 2024, that sells "AI test engineers", which is very close to this product. |
| Zyrn | Rejected | zyrn, an "AI-native database", runs on `zyrn.ai`, `zyrn.dev` and `zyrn.app`. The npm name, GitHub user, GitLab and Docker Hub are taken, and 52 GitHub repos use the name. |

## Evidence for Kodrax

### Domains

| Domain | Status |
|---|---|
| `kodrax.com` | Taken. It points to GoDaddy nameservers and serves no content. |
| `kodrax.ai`, `.io`, `.dev`, `.sh`, `.co`, `.app`, `.build`, `.systems`, `.codes`, `.tools`, `.run`, `.cloud` | Free |
| `kodrax.cl` | Free (NIC Chile WHOIS: "no entries found") |
| `kodrax.lat` | Free |
| `kodr.ax` | Free. Check with the registrar whether non-residents can register `.ax` domains. |

Method: registry WHOIS (port 43) for `.com`, `.ai`, `.io`, `.sh`, `.co` and `.cl`, and registry RDAP for the rest. Each method was checked against domains known to be registered (`google.com`, `google.cl`, `github.io`, `t.co`, `bun.sh`, `nic.<tld>`), which all came back as registered.

### Package registries and code hosts

| Where | Status |
|---|---|
| npm `kodrax` and `@kodrax/core` | Free |
| PyPI, crates.io, RubyGems, Homebrew | Free |
| Docker Hub, GitLab | Free |
| GitHub user/org `kodrax` | Free |
| GitHub repos named kodrax | 2 trivial ones with 0 stars (`Pardeep-Shyoran/KodraX`, `Basma-Hassan95/kodraxelsoft`) |
| Web search | No product or company named Kodrax |

### Trademarks: WIPO Global Brand Database

The database holds about 77 million records from 89 national and regional offices, including the USPTO and EUIPO.

- **Exact word "kodrax", any class**: 0 results. "codrax" and "kodraks" also return 0.
- **Similar spelling and similar sound, classes 9 and 42**: no identical mark. The closest live marks are:

| Mark | Classes | Where | Status | Owner | Risk |
|---|---|---|---|---|---|
| kodax | 9, 42 | Switzerland | Registered 2026-08-11 | DEEPBEATS GMBH | High in Switzerland: one letter off, both classes |
| KODRX | 42 | Mexico | Registered 2026-01-19 | Ramses Gabriel Hernández Zuppa | High in Mexico: one letter off |
| CODRA | 9, 35, 38, 42 | France, USA, and an international registration covering DE, CH, UK, IT, ES, Benelux, PT | In force | CODRA Ingénierie Informatique | Medium-high: software company with a very similar sound |
| NODRAX | 9 | USA | Registered 2025-12-09 | Sichuan Bosimai Innovation Technology | Medium: the first letter sounds different |
| KORDAX | 9 | Mexico | Registered 2025-08-19 | Miguel Ángel Cruz González | Medium: two letters swapped |
| Korax | 9; 42 pending | Brazil | Class 9 registered 2026-01; class 42 pending since 2023 | KORAX LTDA | Medium in Brazil |
| CORAX | 9, 39, 42 | International registration (EU, US, UK, AU and others) | In force | Davanti-WICS | Low-medium: logistics software |
| Codeax | 9 | EU | Registered 2023 | Codeax Oy | Low-medium |
| KODRA | 9 (with 18, 24, 25, 28) | UK, Germany | Registered | Römer Systems GmbH | Low: different goods |

The similar-spelling and similar-sound searches were reviewed through the most relevant results of each query. Results are sorted by relevance, so the closest marks come first.

### Trademarks: INAPI (Chile)

Searched in INAPI's trademark search (buscadormarcas.inapi.cl). It only matches text literally and has no similar-sound search.

| Search | Hits | Conflict in class 9 or 42? |
|---|---|---|
| Contains kodrax, codrax, kodra, kodrx, korax, kordax, nodrax, codeax, odrax | 0 | — |
| Starts with kodr, kodrak, codrak, qodr | 0 | — |
| Starts with codr | CODRIANSKY (classes 9, 16), lapsed | No |
| Contains kodax | Kodax (class 44), application refused | No |
| Contains codra | 7 marks, e.g. SICODRAL, RAUCODRAPE; EcoDrago is live in 39–42 but is a different word | No |
| Contains corax | 12 marks; the live ones are in classes 1, 5, 6, 17 and 19 | No |
| Contains drax | 35 marks; the live ones are in classes 1, 3, 5 and 37 | No |

Nothing in Chile comes close in classes 9 or 42.

## Consequences

- **Chile is clear.** Kodrax can be filed with INAPI as a word mark in classes 9 and 42 with no known conflict.
- **Expanding abroad is possible but not free of risk.** Chile has been part of the Madrid Protocol since 2022-07-04, so one international application based on the Chilean filing can extend the mark to other countries. Countries with known close marks:
  - **Mexico**: KODRX (class 42) and KORDAX (class 9). This is the main risk in Latin America.
  - **Brazil**: Korax.
  - **Switzerland**: kodax (classes 9 and 42).
  - **EU and international registrations**: CODRA, a software company with a similar sound.
  - **USA**: NODRAX (class 9), a weaker conflict.
- **No `.com`.** The brand lives on `kodrax.ai`. Expect some users to type `kodrax.com`; buying it from its current owner is optional.
- **Name collisions are low.** No existing project or company uses the name, so it's easy to search for and SEO isn't diluted.

## Action items

1. Register `kodrax.ai`, `kodrax.dev`, `kodrax.sh` and `kodrax.cl`. `kodr.ax`, `kodrax.io` and `kodrax.build` are optional.
2. Create the GitHub org `kodrax` and the Docker Hub namespace `kodrax`.
3. On npm, create the `@kodrax` org and publish a placeholder `kodrax` package.
4. Claim social handles (X, LinkedIn, Discord, YouTube). These weren't checked.
5. File KODRAX as a word mark with INAPI (tramites.inapi.cl) in class 9 and class 42. Check the current fees on inapi.cl before filing.
6. Within 6 months of the Chilean filing, decide whether to extend through the Madrid Protocol, for example to the USA, the EU, Mexico or Brazil. Inside that window the Chilean filing date also counts abroad (Paris Convention priority). Have a trademark attorney review the KODRX (Mexico), kodax (Switzerland) and CODRA conflicts first.

This record is a database search, not a legal opinion. Before investing in the brand, get a clearance search from a trademark attorney for the countries where you'll launch.
