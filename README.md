# kodrax

kodrax is a local agent harness that takes a software project from idea to delivery. It defines the product with you (PRD, SRS, architecture, contracts), plans the work in [overseer](https://github.com/kodrax/overseer), has your own coding harnesses (Claude Code, omp and others) build it in isolated git worktrees, and checks the result against the documents. It is built with Mastra and Next.js and runs on your machine.

**Status: design.** There is no code yet. The foundation documents are the source of truth, and the code will be checked against them:

- [Product requirements (PRD)](docs/product/prd.md)
- [Software requirements specification (SRS)](docs/requirements/srs.md)
- [Decision records](docs/decisions/README.md)
- [Harness landscape](docs/research/harness-landscape.md)

## License

[Apache-2.0](LICENSE). The license grants no rights to the KODRAX name or marks.
