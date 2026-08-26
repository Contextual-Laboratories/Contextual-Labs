---
title: Entity and predicate taxonomy
domain: graph
category: explanation
tldr: "The graph uses a closed, fixed vocabulary — 15 entity types and 18 predicates — the same set across every supported language, rather than inventing new labels per language or per file."
order: 5
related:
  - graph/explanation/the-knowledge-graph.md
  - graph/reference/graph-schema-reference.md
---

<Callout variant="tldr">
Every entity and edge Contextual writes to the graph comes from one
fixed, closed set — 15 entity types, 18 predicates — the same
vocabulary regardless of which language a file is written in. Nothing
in the graph uses an ad-hoc, language-specific label.
</Callout>

## Why a closed vocabulary

A graph query like "what calls this function" only stays useful if
"calls" means the same thing everywhere — across Python, Go, Rust,
every supported language. Contextual's extractors normalize every
language's own AST vocabulary down to this shared set at extraction
time, rather than exposing each language's native grammar terms
directly. A query never needs to know whether it's asking about a
Python `class` or a Rust `struct` — both become the same `class`
entity type.

## The 15 entity types

Twelve are code constructs:

`function`, `class`, `method`, `module`, `package`, `file`, `import`,
`variable`, `constant`, `type`, `interface`, `enum`

Three aren't code at all — a decision record, a git commit, and a
commit's author are first-class graph nodes too, not a separate side
table:

`adr`, `commit`, `author`

This is what lets a single `graph_traverse` or `graph_query` call
follow a relationship from a function straight to the commit that last
touched it, or to the architectural decision that motivated it, without
switching to a different tool or data source.

## The 18 predicates

Structural relationships, extracted directly from parsed code:

- `calls` — a direct, statically-resolved function/method call.
- `instantiates` — a constructor call (`Cls()` in Python, `NewFoo()` in
  Go, `::new()` in Rust, and each other language's equivalent).
- `calls_polymorphic` — a virtual-dispatch call resolved by CHA/RTA (see
  `graph/explanation/confidence-tiers-and-resolution`), kept distinct
  from `calls` so a query can tell "this is a literal call" from "this
  resolves to one of several possible overrides at runtime."
- `potential_call` — a speculative call the resolver couldn't confirm
  statically. Excluded from most results by default; surfaced
  explicitly (and clearly labeled) where it's directly relevant, such
  as `graph_impact`'s `speculative_callers` list for a rename.
- `imports`, `defines`, `references`, `inherits_from`, `implements`,
  `tests`, `documented_by`, `mentions`

Decision-record relationships:

- `supersedes` — one ADR superseding another.
- `supersedes_entity` — an entity's own rename/refactor lineage, kept
  distinct from `supersedes` so `graph_traverse`/`graph_query` never mix
  ADR history with refactor history on the same predicate.
- `motivated_by` — an entity linked to the decision that motivated it.

Temporal/history relationships:

- `authored_by`, `modified_in` — commit and blame attribution, see
  `temporal/explanation/temporal-intelligence`.
- `co_changes_with` — derived from git history rather than structural
  analysis: two entities that tend to change together across commits,
  even with no direct code reference between them. See
  `temporal/explanation/temporal-intelligence` for how that's computed.

## See also

- `graph/explanation/the-knowledge-graph` — how these get resolved
  during extraction, including cross-file and virtual-dispatch cases.
- `graph/reference/graph-schema-reference` — the same taxonomy as a
  lookup table, alongside the underlying storage fields.
