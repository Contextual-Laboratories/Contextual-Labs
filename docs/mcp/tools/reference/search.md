---
title: "search"
domain: mcp-tools
category: reference
tldr: "search(query, intent, filters, limit, compact, workspace) — the default first call for any code/docs question, returning ranked chunks plus related graph nodes."
order: 1
---

<Callout variant="tldr">
`search` is the tool an AI client should reach for before answering any
question about this codebase's logic, structure, behavior, or history —
instead of guessing from training data. Returns ranked code/doc chunks,
related graph nodes, and freshness info.
</Callout>

## Parameters

- `query` (string, required) — natural-language question or code/symbol
  name.
- `intent` (`"code"` | `"docs"` | `"mixed"`, default `"mixed"`) —
  restrict to code chunks, doc chunks, or both.
- `filters` (object, optional) — post-filter by `language`, `file_path`
  (prefix match), or `symbol_type`.
- `limit` (integer, 1–100, default 10) — max ranked results.
- `compact` (boolean, default false) — return a pipe-separated compact
  text format instead of JSON.
- `workspace` (string, optional) — workspace name or path; omitted
  means auto-resolve.

## When to use it (and when not to)

Call it when a question references "the codebase," "our code," or a
symbol/file not already visible in context. Skip it if you already have
the exact file content from a prior `get_file_content` call, or the
question is about an unrelated codebase.

<Terminal lines={[
  {command: "search(query=\"how does license validation work\", intent=\"code\")"},
  {output: "{\n  \"results\": [ ... 8 ranked chunks ... ],\n  \"related_nodes\": [ ... ],\n  \"latency_ms\": 84\n}", muted: true}
]} />

## See also

- `mcp/tools/reference/nexus_search` — the richer, graph-enriched
  version of this same idea.
- `mcp/tools/how-to/choose-between-search-and-nexus-search`.
