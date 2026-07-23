---
title: "get_file_content"
domain: mcp-server
category: reference
tldr: "get_file_content(file_path|fqn, start_line, end_line, workspace) — reads real source text; always prefer a line range or fqn over a full-file read (a 30-line function costs ~120 tokens vs. ~4,000 for a 1,000-line file)."
order: 3
---

<Callout variant="tldr">
`get_file_content` reads actual source text. Always prefer
`start_line`/`end_line` (or `fqn`) over a full-file read — every entity
returned by `search`, `graph_traverse`, and the other graph tools
already carries line numbers for exactly this reason.
</Callout>

## Parameters

- `file_path` (string, optional) — repo-relative path. Required unless
  `fqn` is given.
- `fqn` (string, optional) — fully-qualified symbol name (e.g.
  `path/file.py:ClassName.method`); resolves file and line range
  automatically.
- `start_line` / `end_line` (integer, optional) — 1-based inclusive
  range. Omit either to read from the top / to the end.
- `workspace` (string, optional).

## Call patterns, cheapest first

1. `get_file_content(file_path, start_line, end_line)` — fetch one
   function/class.
2. `get_file_content(fqn="path/file.py:ClassName.method")` — resolve by
   FQN, auto-scoped.
3. `get_file_content(file_path)` — full file, only when you actually
   need the whole thing.

## When to use it (and when not to)

Call it when you need actual source to answer a question, verify logic,
or generate a patch. Skip it if you don't yet know which file (call
`search` first), or you only need a signature/location
(`graph_get_entity_definition` is cheaper).

## See also

- `mcp-server/reference/search`, `mcp-server/reference/graph_get_entity_definition`.
