---
title: Entity and predicate taxonomy
domain: graph
category: explanation
tldr: "The graph uses a closed, fixed vocabulary — 15 entity types and 22 predicates — the same set across every supported language, rather than inventing new labels per language or per file."
order: 5
related:
  - graph/explanation/the-knowledge-graph.md
  - graph/reference/graph-schema-reference.md
---

<Callout variant="tldr">
Every entity and edge Contextual writes to the graph comes from one
fixed, closed set — 15 entity types, 22 predicates — the same
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

## The 22 predicates

Structural relationships, extracted directly from parsed code:

- `calls` — a direct, statically-resolved function/method call. Besides
  ordinary calls this covers `super().method()` (resolved through the
  caller's class's bases), a method called on a constructor's result
  (`Invoice(100).total()`), and — in Python — a decorator applied to a
  function or class: the decorated definition `calls` its decorator, tagged
  `via: "decorator"`, so changing a decorator shows everything it decorates
  in its blast radius. `@property`, `@staticmethod`, `@classmethod`,
  `@abstractmethod` and `@x.setter`-style accessors add no edge.
- `instantiates` — a constructor call (`Cls()` in Python, `NewFoo()` in
  Go, `::new()` in Rust, and each other language's equivalent). Carries
  a `via: "raise"` metadata field when the construction is a raised/thrown
  exception rather than plain object construction (`raise Cls()` in
  Python, `throw new Cls()` in Java/C#/TypeScript, and each other
  language's equivalent) — the edge is still `instantiates`, not a
  separate predicate, but this lets a query tell "this constructs an
  exception it's about to throw" apart from an ordinary object build.
- `references_type` — a method's declared parameter or return type,
  distinct from a runtime `calls`/`instantiates` edge: "this signature
  depends on that type" rather than "this executes that code."
- `references_field_type` — the same relationship for a class or
  struct's own declared field/property type, kept separate from
  `references_type` so a query can tell "this signature depends on
  that type" from "this stored state depends on that type."
- `calls_polymorphic` — a virtual-dispatch call resolved by CHA/RTA (see
  `graph/explanation/confidence-tiers-and-resolution`), kept distinct
  from `calls` so a query can tell "this is a literal call" from "this
  resolves to one of several possible overrides at runtime."
- `potential_call` — a speculative call the resolver couldn't confirm
  statically. Excluded from most results by default; surfaced
  explicitly (and clearly labeled) where it's directly relevant, such
  as `graph_impact`'s `speculative_callers` list for a rename or
  signature change.
- `unresolved_call` — a dotted call where the receiver's type couldn't
  be bound — a `self.attr.method()` whose attribute type is unknown, or a
  call through a loop variable or untyped parameter — so there's no
  candidate target to record at all, not even a speculative one. The
  resolver deliberately declines to guess here, since a wrong guess adds
  false dependents; the marker only makes the omission countable. It is
  recorded against the calling entity, and the reason is in
  `object_literal`. `object_id` is
  always `NULL` on these rows; the callee and the reason live in
  `object_literal` instead. Not reachable through `graph_traverse`/
  `graph_impact` (their in-memory graph requires a real `object_id` per
  edge) — `graph_query` surfaces it in the edges list for a matched
  entity (it can't be used as a `predicate=` filter in the query itself),
  so an entity with a call the resolver couldn't figure out shows that
  gap explicitly, rather than the dependency silently being absent.
- `imports`, `defines`, `references`, `inherits_from`, `implements`,
  `tests`, `documented_by`, `mentions`
- `file_depends_on` — a file-level roll-up: this file's own symbol-level
  dependency edges (calls, imports, inheritance and so on) into another
  file, collapsed to one edge per target file at the strongest
  confidence any underlying edge provides. It exists so questions asked
  about a whole file, such as `co_change_analysis`'s declared-versus-
  undeclared check, have an edge at file granularity to find. It is
  deliberately not part of the default caller set used by
  `graph_traverse`, `graph_impact` or `nexus_search`, so their results
  for file-level entities are unchanged.

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
