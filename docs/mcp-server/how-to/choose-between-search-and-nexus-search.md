---
title: Choose between search and nexus_search
domain: mcp-server
category: how-to
tldr: Use search for ranked snippets with no graph context; use nexus_search when you want the full structural neighborhood (code, edges, authorship, temporal) in one call.
order: 1
---

<Callout variant="tldr">
`search` gives you ranked semantic snippets. `nexus_search` gives you
the same idea plus structural edges, authorship, and temporal data
around each result — pick based on whether you actually need that
richer bundle or would just be paying for tokens you won't use.
</Callout>

**Use `search` when**: you just need to find relevant code or docs and
don't yet know if you'll need graph context.

**Use `nexus_search` when**: you want the full structural neighborhood
around a query in one round trip, instead of chaining `search` →
`graph_traverse` → `get_temporal_context` yourself — or you already
have an entity ID/FQN and want the enriched bundle directly.

## See also

- `mcp-server/reference/search`, `mcp-server/reference/nexus_search`.
- `mcp-server/explanation/tool-taxonomy`.
