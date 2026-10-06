---
title: "get_doctor"
domain: mcp-tools
category: reference
tldr: "get_doctor(workspace) — the MCP-tool equivalent of contextual doctor: a full diagnostic across models, index, retrieval, graph, database, MCP daemon, and git, returning overall status plus per-subsystem checks with suggestions."
order: 6
---

<Callout variant="tldr">
`get_doctor` is what an AI client should call when something isn't
working — search returning nothing, tools erroring, models not loaded —
rather than guessing at the cause. Returns an overall status
(`ok`/`degraded`/`critical`) plus a per-subsystem breakdown with
concrete suggestions.
</Callout>

## Parameters

- `workspace` (string, optional) — diagnostics still run without one.

## When to use it (and when not to)

Call it when something is visibly broken, or the user asks "is
Contextual healthy?" Skip it if you only want index statistics — use
`get_stats` instead, it's cheaper and more specific.

## The `file_health` subsystem check

Beyond the checks `contextual doctor` runs (see
`cli/reference/general/doctor`), `get_doctor` also reports a per-file
`file_health` signal, `degraded` when any indexed file hit one of two
distinct failure modes — worse one takes priority when both are
present:

- **`chunk_extraction_failed`** — a file with real, non-trivial content
  produced zero chunks. It has no indexed content at all: not
  searchable, not in the graph. Usually a chunker/parser gap for that
  file's language or syntax.
- **`symbol_extraction_failed`** — the file's content chunked and
  indexed fine (it's searchable), but symbol/entity extraction failed
  for it, so it contributes nothing to the dependency graph: no
  functions/classes, no call-graph edges, no entity-level search hits
  for anything defined in that file.

Both surface the affected file paths directly so you know exactly what
to inspect rather than just that "something" is degraded.

## Deep Index progress and background activity

The `index` check answers "do chunks exist," which stays true even while
Deep Index (embeddings) is still running with a small, growing chunk
count. It therefore carries three additive fields for the other
question, "has Deep Index finished":

- `deep_index_ready` (boolean) — `false` while Deep Index is running, or
  if the last run was canceled or crashed before it ever completed.
- `deep_index_percent_complete` (number or null) — roughly how far along
  it is.
- `deep_index_note` (string or null) — the plain-language explanation
  `search` also surfaces, such as that answers currently come from the
  fast keyword layer.

None of these changes the meaning of the check's existing status.

When the daemon is incrementally indexing a workspace in the background,
the response also includes a `daemon_activity` check listing
`active_indexing_workspaces`. It is absent when the daemon is idle.

## See also

- `cli/reference/general/doctor` — the CLI equivalent.
- `observability/how-to/interpreting-doctor-report`.
- `mcp/tools/reference/diagnose_issue` — correlates this with recent telemetry into ranked causes.
