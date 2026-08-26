---
title: Graph schema reference
domain: graph
category: reference
tldr: "The graph lives in two tables — entities (nodes) and triples (edges) — plus the fixed 15 entity types and 18 predicates every row's entity_type/predicate field is drawn from."
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
| `scope` | string, nullable | Fully qualified path — what disambiguates two entities that share a plain `name`. |
| `content_hash` | binary, nullable | BLAKE3 of the entity's definition — used to detect real content changes vs. an untouched re-index. |
| `valid_at` / `invalid_at` | timestamp | Bitemporal valid-time range — when this version was true about your code. |
| `tx_at` / `expired_at` | timestamp | Bitemporal transaction-time range — when Contextual recorded it. |
| `metadata` | JSON, nullable | Extraction-specific extra data; shape varies by entity type. |

## `triples` — edges

| Field | Type | Notes |
|---|---|---|
| `id` | string | Edge identity hash. |
| `entity_id` | string | Subject — foreign key into `entities.id`. |
| `predicate` | string | One of the 18 predicates below. |
| `object_id` | string, nullable | Object — foreign key into `entities.id`, when the edge points at another entity. |
| `object_literal` | string, nullable | JSON payload for edges whose object is a literal value rather than another entity. |
| `weight` | float | Edge weight, `1.0` by default. |
| `confidence` | float, nullable | Drives the `confidence_tier` (`high`/`moderate`/`low`/`speculative`/`unknown`) every graph tool response surfaces — see `graph/explanation/confidence-tiers-and-resolution`. |
| `valid_at` / `invalid_at` / `tx_at` / `expired_at` | timestamp | Same bitemporal shape as `entities`. |
| `metadata` | JSON, nullable | Predicate-specific extra data. |

<Callout variant="note">
Bitemporal + append-only means a change to your code never overwrites
graph history — it adds a new version with its own `valid_at` range and
closes out the old one's `invalid_at`. This is what `graph_at_time`
reads to reconstruct what the graph looked like as of a past commit;
see `temporal/explanation/temporal-intelligence`.
</Callout>

## The 15 entity types

`function`, `class`, `method`, `module`, `package`, `file`, `import`,
`variable`, `constant`, `type`, `interface`, `enum`, `adr`, `commit`,
`author`

## The 18 predicates

`calls`, `instantiates`, `calls_polymorphic`, `potential_call`,
`imports`, `defines`, `references`, `inherits_from`, `implements`,
`tests`, `documented_by`, `mentions`, `supersedes`,
`supersedes_entity`, `motivated_by`, `authored_by`, `modified_in`,
`co_changes_with`

See `graph/explanation/entity-and-predicate-taxonomy` for what each one
means, not just the name.

## See also

- `graph/explanation/entity-and-predicate-taxonomy` — the same lists,
  explained.
- `graph/explanation/confidence-tiers-and-resolution` — what `confidence`
  drives in a query response.
- `indexing/reference/storage-schema-reference` — every other table in
  the same database, and the migration model these tables share.
