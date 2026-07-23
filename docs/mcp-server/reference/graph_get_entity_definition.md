---
title: "graph_get_entity_definition"
domain: mcp-server
category: reference
tldr: "graph_get_entity_definition(entity_id, workspace) — fetches source code, location, author, and commit metadata for one named entity by FQN or hash, without pulling the whole containing file."
order: 10
---

<Callout variant="tldr">
`graph_get_entity_definition` fetches one entity's full definition —
source, location, author, commit metadata — without reading its entire
containing file.
</Callout>

## Parameters

- `entity_id` (string, required) — entity hash or FQN to fetch.
- `workspace` (string, optional).

## When to use it (and when not to)

Call it once a search or traversal returned an `entity_id` and you need
its full definition. Skip it if you want the whole file (`get_file_
content`) or the entity is unknown yet (`search` first).

## See also

- `mcp-server/reference/get_file_content`, `mcp-server/reference/search`.
