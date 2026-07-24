---
title: Run blast-radius analysis before a rename or delete
domain: mcp-tools
category: how-to
tldr: Call graph_impact(entity_id, change_type) before deleting, renaming, or changing a signature — check that the entity was actually found before trusting a "0 impacted" result.
order: 2
---

<Callout variant="tldr">
`graph_impact` is the mandatory pre-flight check before deleting,
renaming, or changing a signature. Match `change_type` to what you're
actually about to do — it changes which edges and depth are used.
</Callout>

```
graph_impact(entity_id="src/auth.py:validate_token", change_type="delete")
```

<Terminal lines={[
  {command: "graph_impact(entity_id=\"src/auth.py:validate_token\", change_type=\"delete\")"},
  {output: "{\n  \"impacted\": [ ... 4 entities ... ],\n  \"total_impacted\": 4,\n  \"_meta\": {\"_resolution_summary\": {\"high\": 4, \"speculative\": 0}}\n}", muted: true}
]} />

<Callout variant="warning">
Before trusting a "0 impacted" result as "safe to delete," make sure the
entity was actually found — see
`troubleshooting/entity-not-found` for how "not found" and
"found, zero dependents" are deliberately kept distinguishable.
</Callout>

For a rename specifically, also check the `speculative_callers` field —
those are low-confidence matches that structural resolution couldn't
confirm, and they're worth a manual look before you proceed.

## See also

- `mcp/tools/reference/graph_impact`, `mcp/tools/reference/graph_traverse`.
