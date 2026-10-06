---
title: Graph schema reference
domain: graph
category: reference
tldr: "The graph lives in two tables — entities (nodes) and triples (edges) — plus the fixed 15 entity types and 22 predicates every row's entity_type/predicate field is drawn from."
related:
  - graph/explanation/entity-and-predicate-taxonomy.md
  - indexing/reference/storage-schema-reference.md
---

<Callout variant="tldr">
Two tables back the whole graph: `entities` (nodes) and `triples`
(edges), both bitemporal and append-only — a row is never updated or
deleted in place, only superseded by a newer version. Both live inside
the same per-workspace LanceDB database as everything else Contextual
stores; see `indexing/reference/storage-schema-reference` for the full
table list.
</Callout>

## `entities` — nodes

| Field | Type | Notes |
|---|---|---|
| `id` | string | BLAKE3 hash of the entity's fully-qualified name. |
| `name` | string | The entity's own name (not fully qualified). |
| `entity_type` | string | One of the 15 types below. |
| `scope` | string, nullable | Fully qualified path — what disambiguates two entities that share a plain `name`. Always `path/to/file.ext:Symbol`, with forward slashes on every operating system. |
| `content_hash` | binary, nullable | BLAKE3 of the entity's definition — used to detect real content changes vs. an untouched re-index. |
| `valid_at` / `invalid_at` | timestamp | Bitemporal valid-time range — when this version was true about your code. |
| `tx_at` / `expired_at` | timestamp | Bitemporal transaction-time range — when Contextual recorded it. |
| `metadata` | JSON, nullable | Extraction-specific extra data; shape varies by entity type. |

## `triples` — edges

| Field | Type | Notes |
|---|---|---|
| `id` | string | Edge identity hash. |
| `entity_id` | string | Subject — foreign key into `entities.id`. |
| `predicate` | string | One of the 22 predicates below. |
| `object_id` | string, nullable | Object — foreign key into `entities.id`, when the edge points at another entity. |
| `object_literal` | string, nullable | JSON payload for edges whose object is a literal value rather than another entity. |
| `weight` | float | Edge weight, `1.0` by default. |
| `confidence` | float, nullable | Drives the `confidence_tier` (`high`/`moderate`/`low`/`speculative`/`unknown`) every graph tool response surfaces — see `graph/explanation/confidence-tiers-and-resolution`. |
| `valid_at` / `invalid_at` / `tx_at` / `expired_at` | timestamp | Same bitemporal shape as `entities`. |
| `metadata` | JSON, nullable | Predicate-specific extra data. |

<Callout variant="note">
Bitemporal + append-only means a change to your code never overwrites
graph history in place — it adds a new version with its own `valid_at`
range and closes out the old one's `invalid_at`. This is what
`graph_at_time` reads to reconstruct what the graph looked like as of a
past commit; see `temporal/explanation/temporal-intelligence`.

Invalidated versions aren't kept forever, though: a background sweep
hard-deletes them once they've been closed out for longer than
`bitemporal_history_retention_days` (90 days by default) — see
`configuration/reference/configuration-reference`. `graph_at_time`
queries inside that window are unaffected either way; a timestamp older
than the retention window may no longer resolve to a real snapshot.
</Callout>

## The 15 entity types

`function`, `class`, `method`, `module`, `package`, `file`, `import`,
`variable`, `constant`, `type`, `interface`, `enum`, `adr`, `commit`,
`author`

## The 22 predicates

`calls`, `instantiates`, `references_type`, `references_field_type`,
`calls_polymorphic`, `potential_call`,
`unresolved_call`, `imports`, `defines`, `references`, `inherits_from`,
`implements`, `tests`, `documented_by`, `mentions`, `supersedes`,
`supersedes_entity`, `motivated_by`, `authored_by`, `modified_in`,
`co_changes_with`, `file_depends_on`

<Callout variant="note">
`unresolved_call` rows have `object_id: NULL` — there's no candidate
target entity to point at, by definition. They're invisible to
`graph_traverse`/`graph_impact`'s in-memory graph (which requires a real
`object_id` per edge). `graph_query` surfaces them in the edges list it
returns for a matched entity, but its `query` WHERE-clause filters
entities only — you can't filter directly on `predicate` there, so find
the entity first (e.g. `entity_type = 'method'`) and look for
`unresolved_call` among its returned edges.
</Callout>

See `graph/explanation/entity-and-predicate-taxonomy` for what each one
means, not just the name.

## See also

- `graph/explanation/entity-and-predicate-taxonomy` — the same lists,
  explained.
- `graph/explanation/confidence-tiers-and-resolution` — what `confidence`
  drives in a query response.
- `indexing/reference/storage-schema-reference` — every other table in
  the same database, and the migration model these tables share.

## How a file path becomes an identity

An entity's `id` is the hash of its `scope`, so the way the path part of a scope is spelled
decides the id. Contextual spells it one way everywhere:

- **Forward slashes**, on Windows too, relative to the workspace root, with no leading `./`.
  This is also what git reports, so history and co-change data attach to the same entities.
- **Unicode NFC.** macOS and Linux can store the same accented file name in different forms;
  both end up with one identity.
- **Case is preserved.** `Pkg/Api.py` and `pkg/api.py` are different spellings of one file on
  Windows and by default on macOS. A lookup that misses exactly is retried ignoring case and
  resolves when exactly one entity matches; if several do (a case-sensitive directory on NTFS),
  it does not guess.
- **Links are not indexed.** Symbolic links and Windows directory junctions are skipped, so a
  link can neither cause a loop nor pull in files from outside the workspace.

You can pass either separator, or either Unicode form, to any tool; the identity does not change.
