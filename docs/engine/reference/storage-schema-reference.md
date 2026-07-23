---
title: Storage Schema Reference
domain: engine
category: reference
tldr: Everything Contextual knows about your code lives in one LanceDB database per workspace — chunks, graph entities and edges, blame/commit history, decisions, and caches all as separate tables in the same store, plus a small global database tracking users and workspace registration.
order: 2
related:
  - engine/explanation/architecture-overview.md
  - engine/reference/data-privacy.md
  - engine/reference/logs-and-retention-reference.md
---

<Callout variant="tldr">
Two database roots, not one: a small global LanceDB at
`~/.contextual/lancedb` tracking users and registered workspaces, and a
full per-workspace LanceDB under `<repo>/.contextual/lancedb` holding
everything Contextual actually knows about that specific codebase.
</Callout>

<Callout variant="note">
Older internal documentation describes a fixed "13-table" schema — that
count is historical only. The schema has grown considerably since,
through forward-only migrations, and this page reflects the current
shape.
</Callout>

## Two database roots

- **Global** (`~/.contextual/lancedb`) — `users`, `workspaces`. Small,
  machine-wide, tracks account/workspace registration, not code content.
- **Per-workspace** (`<workspace_root>/.contextual/lancedb`) — everything
  else. One database per indexed repository.

## The per-workspace tables that back what you actually query

| Table | What it holds |
|---|---|
| `code_chunks` / `doc_chunks` | Tree-sitter-split code and documentation chunks, with embeddings — what `search` and BM25/trigram FTS query against. |
| `entities` / `triples` | Knowledge graph nodes and edges — see `engine/explanation/the-knowledge-graph`. |
| `commits` / `blame_cache` / `entity_commits` | Git commit metadata, cached `git blame` output, and per-entity commit history — see `engine/explanation/temporal-intelligence`. |
| `adrs` / `adr_embeddings` / `adr_pending_refs` | Architectural Decision Records and their embeddings, plus provisional entity references awaiting resolution. |
| `file_state` | Per-file indexing status (content hash, entity count, pipeline version) — what lets an unchanged file be skipped on the next incremental run. |
| `embedding_cache` / `query_cache` | Content-hash-keyed embedding reuse, and cached query results. |
| `audit_log` | The tool-call audit trail (which client called which tool, when, with what outcome) — see `engine/reference/logs-and-retention-reference` for its retention window, which is deliberately different from everything else in this list. |

A few additional tables (`terminal_sessions`, `queue`, `swarm`,
`task_force`, `reminders`) support internal/experimental features outside
the documented MCP tool surface and aren't covered in detail here.
Enterprise and Teams editions add their own organization/team-scoped
tables (members, permissions, audit trails) through a separate,
edition-gated migration path — not part of the Solo/local schema above.

## Migrations

Schema changes ship as forward-only, numbered migrations, applied in
order and tracked in a JSON version file at
`<workspace_db_path>/.migrations/applied.json` — so a workspace always
knows exactly which migrations it's had applied, and a fresh workspace
runs the full chain from scratch. A schema version bump
(`SCHEMA_VERSION`, currently `2.0.0`) signals a change significant enough
to require `contextual index --force` — most recently, the ONNX/fastembed
embedding migration, since old and new embeddings aren't compatible
within the same table.

## What this means for you

You never interact with this schema directly — it's entirely internal to
how Contextual stores what it learns about your repository. What's worth
knowing as a user: everything lives in one place per workspace (deleting
that workspace's `.contextual/` directory removes everything Contextual
knows about it), and different tables have very different retention
behavior — see `engine/reference/logs-and-retention-reference` for
exactly what expires and what doesn't.
