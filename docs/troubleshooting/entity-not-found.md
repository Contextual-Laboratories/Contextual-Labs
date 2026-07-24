---
title: "\"Entity not found\" — what it actually means"
domain: troubleshooting
category: troubleshooting
tldr: "Entity not found" means the graph has no record of that symbol at all — it's different from a tool returning zero results for a symbol it does know about, and the two are never conflated.
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

**A real result with zero entries** — the entity was found, and the
graph is telling you accurately that nothing matched (no callers, no
dependents, no co-changed files). This is a real answer, not an error.

## What to do

1. If you just added or renamed the symbol, run
   `contextual index --incremental` and try again.
2. If the symbol has existed for a while and this is unexpected, run
   `contextual doctor` and check the **Database** line — an empty or
   stale index for this workspace is the next most common cause.
3. Double-check the exact fully-qualified name/path you're passing —
   graph lookups are exact-match on the resolved entity identifier, not
   a fuzzy search.
