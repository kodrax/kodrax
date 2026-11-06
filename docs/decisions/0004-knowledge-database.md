# 0004. kodrax knowledge database

- **Status**: Accepted, with open questions
- **Date**: 2026-10-24
- **Scope**: the database kodrax keeps for each project: what it stores, where it lives and how its retrieval grows with the project. Planning data is out of scope ([ADR 0005](0005-planning-through-overseer.md)).

## Context

kodrax has to understand a project whether it has three documents or a large codebase with years of history. Loading everything into the model's context works for a small project and fails for a large one. The techniques by project size:

| Project size | Technique |
|---|---|
| Small (fits in context) | Load all the foundation documents into each session. |
| Medium | A generated project map: a summary per module and a symbol map. Summaries are cached by content hash and rebuilt when a file changes. |
| Large | Hybrid search (keyword plus vectors, merged with reciprocal rank fusion, as overseer does), a graph linking requirements, contracts, modules, tests and tasks, and reindexing of only what a git diff touched. |

## Decision

1. kodrax keeps its own database for each project inside `.kodrax/`, which git ignores.
2. It stores:
   - chunks and vectors of the foundation documents;
   - an index of the code: files, symbols, summaries and, later, a dependency graph;
   - links between requirement IDs, contracts, code and tests;
   - kodrax's runtime state: conversations, workflow runs, approvals and their audit trail.
3. Everything in it except runtime history can be rebuilt from the repository. The documents and the code are the truth ([ADR 0002](0002-foundation-documents-are-the-source-of-truth.md)); the database is an index of them.
4. It holds no planning data. kodrax reads plans from overseer.
5. kodrax picks the retrieval technique by project size, from the table above, and combines them as the project grows.
6. Documents change over time, and the index follows them, so kodrax can answer from the index in place of the documents. Every chunk records the version of the file it came from: its content hash, and the commit when the file is committed. When a file changes, kodrax re-indexes it before relying on it. A document's history is read from git, not kept in the database. *Clarified by the owner on 2026-10-24.*

## Consequences

- Deleting `.kodrax/` loses conversations and run history, not project knowledge.
- Indexing runs on the user's machine and has to be incremental, driven by git changes, to stay fast on large repositories.
- Summaries need a model. Without an API key they are produced through the user's harness ([ADR 0008](0008-harness-as-the-brain.md)), which spends subscription quota, so they are cached by content hash and generated on demand.

## Open questions

1. **Engine** (proposed): one libSQL file, `.kodrax/kodrax.db`, through Mastra's libSQL storage and vector adapters, so runtime state and vectors share one file. Mastra Factory's local setup uses `@mastra/libsql` too ([MastraFactory API](https://factory.mastra.ai/reference/mastra-factory-api)). Alternative: SQLite with `sqlite-vec`, the stack overseer uses.
2. **Embedding model** (proposed): a local model, because users with only a subscription have no embeddings API ([ADR 0009](0009-subscription-first-authentication.md)). overseer's `Xenova/multilingual-e5-small` suits prose in several languages; code probably needs a code-specific model. Choose after measuring candidates on kodrax's own documents and code.
3. **Code graph**: which languages come first, and whether the graph is built with tree-sitter or from language servers.
