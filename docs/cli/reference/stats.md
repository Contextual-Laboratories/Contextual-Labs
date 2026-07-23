---
title: "stats"
domain: cli
category: reference
tldr: "contextual stats shows index statistics (file/chunk/vector counts, languages, size); add --temporal for decision-record counts or --graph for entity/edge counts, or --all for everything."
order: 17
---

<Callout variant="tldr">
`contextual stats` reports on the current workspace's index: file count,
chunk count, vector count, languages seen, and index size on disk, by
default. Add `--temporal` and/or `--graph` for those specific subsystems,
or `--all` for everything at once.
</Callout>

## Usage

```
contextual stats [--temporal] [--graph] [--all]
```

- `--temporal`, `-t` — show decision-record counts (total, accepted,
  proposed) and when they were last created/accepted.
- `--graph`, `-g` — show entity count, edge count, predicate breakdown,
  and the language list seen in the graph.
- `--all`, `-a` — show standard, temporal, and graph stats together.

<Terminal lines={[
  {command: "contextual stats"},
  {output: "Files:        342\nChunks:       4,108\nVectors:      4,108\nLast indexed: 2026-07-22 09:41 UTC\nLanguages:    python, typescript\nIndex size:   61.4 MB", muted: true}
]} />

Requires the workspace to already be indexed — run `contextual index`
first if this is a brand-new workspace.

## See also

- `mcp-server/reference/get_stats` — the MCP-tool equivalent an AI
  client calls directly.
