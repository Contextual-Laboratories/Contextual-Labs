---
title: Find files/entities that change together for refactor planning
domain: mcp-tools
category: how-to
tldr: Call co_change_analysis(entity_id) to surface undeclared coupling — files that change together in history with no structural link explaining why.
order: 5
---

<Callout variant="tldr">
`co_change_analysis` finds coupling `graph_impact`/`graph_traverse`
structurally cannot see — entities that change together in commit
history despite no calls/imports connecting them.
</Callout>

```
co_change_analysis(entity_id="src/auth.py:validate_token")
```

<Terminal lines={[
  {command: "co_change_analysis(entity_id=\"src/auth.py:validate_token\")"},
  {output: "{\n  \"co_change_partners\": [\n    {\"entity\": \"src/session.py:Session\", \"declared\": false, \"strength\": 0.82}\n  ]\n}", muted: true}
]} />

The `declared: false` partners are the actually useful signal — real
historical coupling with no structural explanation, worth understanding
before you refactor either side in isolation.

## See also

- `mcp/tools/reference/co_change_analysis`, `mcp/tools/reference/graph_impact`.
