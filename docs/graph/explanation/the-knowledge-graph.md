---
title: The Knowledge Graph
domain: graph
category: explanation
tldr: Contextual extracts your code into a typed graph — 15 entity types, 18 relationship types, each edge carrying a confidence score instead of a flat yes/no — including virtual-dispatch calls that a plain AST parse can't see.
order: 4
related:
  - indexing/explanation/how-indexing-works.md
  - troubleshooting/entity-not-found.md
---

<Callout variant="tldr">
Every function, class, import, and other symbol in your codebase becomes
a typed node; every relationship between them (calls, imports, inherits,
...) becomes a typed, confidence-scored edge. The graph also resolves
calls a plain AST parse can't — virtual dispatch through inheritance,
cross-file attribute chains, constructor bindings — instead of only
literal same-file calls.
</Callout>

## Entities and relationships are closed, typed vocabularies

Contextual doesn't invent ad-hoc relationship labels per language — every
extractor across every supported language emits entities and edges from
the same fixed set:

**15 entity types**: function, class, method, module, package, file,
import, variable, constant, type, interface, enum, plus three that aren't
code at all — adr, commit, author (so a decision record or a git commit
is a first-class graph node, not a side table).

**18 predicate (relationship) types**, including `calls`, `imports`,
`inherits_from`, `implements`, `tests`, `references`, `documented_by`,
`modified_in`, `authored_by`, and `co_changes_with` (derived from git
history, not structural analysis — see
`temporal/explanation/temporal-intelligence`). Two are worth calling out
specifically: `instantiates` (a constructor call — `Cls()` in Python,
`NewFoo()` in Go, `::new()` in Rust) and `calls_polymorphic` (a virtual
dispatch call resolved by the CHA/RTA engine below, kept as a distinct
predicate from a direct `calls` edge so a graph query can tell "this is a
literal call" from "this is a call that resolves to one of several
possible overrides at runtime").

## Every edge carries a confidence score, not a boolean

Static resolution of "what does this call actually point to" is
inherently uncertain in a dynamic-enough language — the same function
name might exist in five files. Rather than guessing and asserting a
single answer, or refusing to answer at all, Contextual attaches a
confidence tier to every resolved edge, roughly from "unambiguous" down
to "plausible but not certain," and callers pass a confidence floor to
graph queries to control how speculative a result they're willing to
accept. Treat the specific tier boundaries as illustrative, not a fixed
public spec — they've already been retuned once as resolution heuristics
improved, and will likely move again.

The lowest tier is intentionally excluded by default: a graph query with
no explicit threshold won't surface the most speculative, lowest-
confidence guesses unless you ask for them.

## Resolving what a plain parser can't

A handful of resolution strategies run beyond "match the literal call
site to a same-file definition":

- **Import-aware resolution** — a call is matched against what the
  calling file actually imports, so a same-named function in an
  unrelated, unimported file doesn't get credited as the target.
- **Cross-file inheritance** — if a subclass's base class isn't defined
  in the same file, the resolver looks it up workspace-wide and merges
  the parent's attribute bindings in, so `self.parent_attr.method()`
  still resolves correctly across file boundaries.
- **CHA/RTA virtual dispatch** — a dedicated pass implements Class
  Hierarchy Analysis (which overrides *could* this call reach, based on
  the class hierarchy) filtered by Rapid Type Analysis (which of those
  classes are ever actually instantiated somewhere in your codebase).
  This is what makes an interface-typed call in an OOP codebase resolve
  to its real implementations instead of stopping at the interface
  definition. The same mechanism also covers Go's structural interfaces
  (duck-typed `implements`, inferred rather than declared) as a distinct
  case, since Go has no explicit `implements` keyword to parse.
- **Constructor-bound type inference** — tracking what type a variable
  was constructed as, so a later method call through that variable can
  resolve to the right class even without an explicit type annotation.
  Language coverage for this specific mechanism isn't uniform — it's
  deepest for Python, TypeScript/JavaScript, Go, Rust, Java, C#, Kotlin,
  and Swift; other full-graph languages still get calls/imports/
  inheritance resolution without this particular refinement.

## Extraction runs in two passes, not one

A single left-to-right parse of your codebase would drop forward
references — a function defined in `a.py` that's called from `b.py`,
where `b.py` happens to get processed first. A full index avoids this by
running extraction in two passes: everything gets registered first, then
relationships are resolved against the complete, fully-built registry —
so processing order never determines whether a real reference gets
missed. The file-watcher-driven incremental path is a narrower case: it
resolves each saved file against the registry immediately, which is safe
there specifically because a full index has already seeded that registry
before the watcher ever starts — it isn't redoing two-pass resolution
from scratch on every save.

<Callout variant="note">
If you save several files that reference each other in the same instant
(e.g. an editor's "save all"), the concurrent file-watcher tasks
processing them coordinate with each other before resolving — so a
same-wave cross-file reference isn't lost to whichever file's extraction
happened to finish first.
</Callout>

## What this graph actually answers

The graph is what backs `graph_traverse`, `graph_impact`,
`graph_get_entity_callers`, `graph_get_entity_definition`,
`graph_find_path`, and `graph_query` — see `mcp/tools/reference/` for
each tool's exact parameters. `nexus/explanation/nexus-enrichment`
covers how the graph gets combined with semantic search and temporal
data into a single richer answer via `nexus_search`.
