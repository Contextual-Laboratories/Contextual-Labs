---
title: Inspect graph state as of a past commit
domain: mcp-tools
category: how-to
tldr: Call graph_at_time(entity_id, timestamp) with a commit SHA or ISO timestamp to get a bitemporal snapshot instead of current state.
order: 4
---

<Callout variant="tldr">
`graph_at_time` answers "what did this look like at commit X" —
distinct from every other graph tool, which all describe current state.
</Callout>

```
graph_at_time(entity_id="src/auth.py:validate_token", timestamp="a91f0c2")
```

Accepts either a git commit SHA (7–40 hex characters) or a full ISO 8601
timestamp. Use this for historical audit questions, not as a substitute
for `graph_traverse` on current state.

## See also

- `mcp/tools/reference/graph_at_time`, `mcp/tools/reference/get_temporal_context`.
