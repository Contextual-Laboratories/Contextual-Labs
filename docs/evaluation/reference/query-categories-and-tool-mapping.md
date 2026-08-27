---
title: Query Categories & Tool Mapping
domain: evaluation
category: reference
tldr: The 11 query categories in the eval suite and which MCP tool each one exercises, from plain semantic search through graph traversal, impact analysis, co-change, and time-travel queries.
related:
  - evaluation/explanation/how-we-evaluate-retrieval-quality.md
  - mcp/tools/README.md
---

<Callout variant="tldr">
1,083 queries across 15 repos split into 11 categories, each one
mapped to the single real MCP tool it exercises — so a category's
recall number is really a measurement of that specific tool, not the
suite as a whole.
</Callout>

## Category → tool mapping

| Category | Count | MCP tool exercised |
|---|---|---|
| `graph_traverse` | 167 | `graph_traverse` |
| `graph_impact` | 157 | `graph_impact` |
| `graph_at_time` | 157 | `graph_at_time` |
| `co_change` | 144 | `co_change_analysis` |
| `temporal` | 92 | `search` |
| `cross_file` | 91 | `nexus_search` |
| `graph_traversal` | 89 | `nexus_search` |
| `symbol_lookup` | 88 | `search` |
| `semantic_search` | 86 | `search` |
| `repo_structure` | 6 | `get_repo_structure` |
| `file_content` | 6 | `get_file_content` |

**Total: 1,083 queries.** `temporal` queries are phrased as
time-relative natural language ("what changed most recently in the
auth module") and answered through `search` — they test whether the
right file is findable when a query is phrased temporally, not the
correctness of any temporal metadata itself. That's a deliberately
different, narrower question than what `graph_at_time` tests (below).

## What each category actually tests

- **`semantic_search`** / **`symbol_lookup`** — plain natural-language
  or symbol-name queries against `search`, Contextual's core hybrid
  retrieval path.
- **`temporal`** — time-phrased natural-language queries, also
  answered through `search`.
- **`cross_file`** / **`graph_traversal`** — queries that need to
  follow a relationship across files, answered through `nexus_search`,
  which blends semantic and graph-derived signal.
- **`graph_traverse`** — explicit forward/backward dependency walks
  from a named entity, at a specified depth, through `graph_traverse`.
- **`graph_impact`** — "what breaks if I change/delete/rename this
  entity," through `graph_impact`.
- **`graph_at_time`** — the same kind of graph query, but resolved as
  of a specific historical commit, through `graph_at_time`.
- **`co_change`** — "what else tends to change alongside this entity,"
  through `co_change_analysis`, Contextual's git-history coupling
  signal.
- **`repo_structure`** — directory-tree queries through
  `get_repo_structure`.
- **`file_content`** — direct file/range retrieval through
  `get_file_content`.

## Two category groups, for partial runs

For fast local iteration (see
`evaluation/how-to/run-the-eval-suite-yourself`), these 11 categories
also collapse into two groups:

- **Semantic** — `semantic_search`, `symbol_lookup`, `temporal`,
  `repo_structure`, `file_content` — everything answerable from an
  L1-only (chunk/embedding) index.
- **Graph** — `cross_file`, `graph_traversal`, `graph_traverse`,
  `graph_impact`, `co_change`, `graph_at_time` — everything that needs
  the dependency graph and/or co-change data built.

Every category belongs to exactly one group — a category that isn't
claimed by either would silently vanish from both partial-run modes,
so the suite asserts this partition holds at import time rather than
letting it drift unnoticed.
