# Nexus

What `nexus_search` adds on top of plain search: a semantic seed lookup
straight against the graph, a structural traversal outward from that
seed, and temporal context attached to every node it finds — one call
instead of three.

- `nexus/explanation/nexus-enrichment` — what `nexus_search` actually
  does, in order, and why it's a genuinely separate code path from
  `search` rather than a wrapper around it.
- `nexus/explanation/nexus-signal-sources` — the specific signals it
  blends (semantic, structural, temporal) and the one signal it
  deliberately does not (co-change).
- `nexus/how-to/read-a-nexus-search-result` — the shape of a
  `nexus_search` response and what each field on a returned node means.

For the tool's exact parameters, see
`mcp/tools/reference/nexus_search`, `mcp/tools/reference/graph_at_time`,
and `mcp/tools/reference/co_change_analysis` — the three Nexus-layer
MCP tools.
