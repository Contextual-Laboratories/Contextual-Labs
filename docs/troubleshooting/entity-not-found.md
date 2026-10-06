---
title: "\"Entity not found\" — what it actually means"
domain: troubleshooting
category: troubleshooting
tldr: "\"Entity not found\" means the graph has no record of that symbol at all — it's different from a tool returning zero results for a symbol it does know about, and the two are never conflated."
order: 1
---

<Callout variant="tldr">
Graph tools (`graph_traverse`, `graph_impact`, `co_change_analysis`, and
others) deliberately distinguish "this entity doesn't exist in the
index" from "this entity exists, and genuinely has zero related
results" — they are never allowed to look the same. If you see an
explicit "entity not found" message, the graph has no row for that
symbol at all; it isn't quietly telling you "nothing depends on this."
</Callout>

## Why this distinction exists

Before running a graph query, Contextual checks whether the entity you
asked about actually has a live row in the index. Without that check, a
query like "what depends on this function" and a query about a function
the index has never heard of would return the identical empty answer —
which is dangerous specifically when you're checking blast radius before
a rename or delete. Reading "0 impacted" as "confirmed safe to delete"
is a real, bad outcome if what actually happened is "the tool doesn't
know this function exists," not "this function has no dependents."

## The two things you might actually be seeing

**"Entity not found in index"** — the symbol genuinely isn't in the
graph. Most commonly this means one of:
- A typo in the fully-qualified name or path you gave the tool.
- The file containing this symbol hasn't been indexed yet — check
  `.contextualignore` isn't excluding it (see
  `indexing/reference/contextualignore-reference`).
- The symbol was added or renamed very recently and the index hasn't
  caught up — Contextual specifically checks the current working tree
  for the symbol before reporting a flat "not found," so if it's a
  brand-new or freshly-renamed symbol, re-run `contextual index
  --incremental` first before assuming something's broken.

**"Entity excluded from index"** (`ENTITY_EXCLUDED_FROM_INDEX`) — the
file exists on disk but `.contextualignore` deliberately excludes it, a
lockfile for example. It is not a typo and nothing is stale; the file
was never meant to be indexed. Change the ignore rules if you do want
it indexed (see `indexing/reference/contextualignore-reference`).

**"Not in the index, but found in the working tree"**
(`ENTITY_NOT_FOUND_BUT_IN_WORKING_TREE`) — a plain-text search of the
symbol's own file found a match, so this is *not* a confirmed absence.
The index is most likely behind a recent edit; run `contextual index
--incremental` and retry.

**A real result with zero entries** — the entity was found, and the
graph is telling you accurately that nothing matched (no callers, no
dependents, no co-changed files). This is a real answer, not an error.

## What to do

1. If you just added or renamed the symbol, run
   `contextual index --incremental` and try again.
2. If the symbol has existed for a while and this is unexpected, run
   `contextual doctor` and check the **Database** line — an empty or
   stale index for this workspace is the next most common cause.
3. Double-check the name or path you're passing. Graph tools accept a
   raw entity hash, a fully-qualified name (`path/to/file.py:Class.method`),
   a repo-relative file path, or a bare symbol name. Resolution is exact,
   not fuzzy: a bare name must match an entity's name exactly, and when
   several entities share it the most-referenced one is chosen, so use
   the fully-qualified name when you need a specific one. Misspelling
   only produces a miss, never a near-match.
