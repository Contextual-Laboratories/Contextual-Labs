---
title: "activity"
domain: cli
category: reference
tldr: "contextual activity [--since 7d] shows recent MCP tool-call usage for this workspace — call counts, error rate, latency, busiest and priciest (by token) tools, plus a fixed 90-day daily heatmap."
order: 6
---

<Callout variant="tldr">
`contextual activity` summarizes how your AI client has actually been
using Contextual on this workspace: how many tool calls, how many
failed, latency, which tools got used most, and (separately) which
tools cost the most output tokens. It's read-only and requires the
workspace to already be indexed.
</Callout>

## Usage

```
contextual activity [--since VALUE]
```

- `--since VALUE` — time window for the stats panel, as a number plus
  `h` or `d` (e.g. `24h`, `7d`, `30d`). Defaults to `7d`.

<Terminal lines={[
  {command: "contextual activity"},
  {output: "  activity\n\n  window            last 7d\n  tool calls        184\n  errors            2\n  avg latency       91ms\n  p95 latency       340ms\n  busiest tools     search(62)  nexus_search(41)  graph_impact(23)  get_stats(18)  co_change_analysis(11)\n  total tokens      48,206\n  avg tokens/call   262\n  priciest tools    nexus_search(19,340)  search(14,880)  graph_impact(8,102)", muted: true}
]} />

## Where this data comes from

Every MCP tool call your AI client makes is already recorded in
`audit_log` — the same SOC2-style tool-call trail the daemon writes on
every request, entirely local to this workspace's database.
`contextual activity` is the first command that reads it back; nothing
new is being tracked to support this command; it's a client-side view
onto data that already existed.

<Callout variant="note">
Only real tool calls count. Protocol/session bookkeeping requests
(`initialize`, `tools/list`, and similar JSON-RPC plumbing that isn't
one of Contextual's 24 MCP tools) are filtered out, so this reflects
actual usage — not raw request volume.
</Callout>

## The two different windows

`--since` only scopes the stats panel at the top (call counts, latency,
busiest/priciest tools). The daily-activity heatmap underneath it is
always a fixed, independent 90-day rolling window, regardless of
`--since` — it's shown even when your `--since` window itself has zero
calls in it, since it answers a different question ("when have I used
this workspace") than the panel above it does ("how has it performed
recently").

## Token figures can be partial on older data

`avg tokens/call` and `priciest tools` are computed only from rows that
have a recorded response-token count. Rows written before response-size
tracking was added don't have one and are excluded from these two
figures rather than counted as zero — so a workspace with a long
history mixing old and new rows won't have its per-call average
understated by rows that were never measured. Call counts, error rate,
and latency are unaffected — those are recorded on every row.

## Empty states

- No `audit_log` table yet (nothing indexed, or no tool calls made
  through the daemon yet) — prints a zero-call panel, not an error.
- A workspace with calls outside the `--since` window but inside the
  90-day heatmap window still shows the heatmap; the panel above it
  just reads `tool calls  0`.

## See also

- `cli/reference/general/stats` — index contents (files, chunks,
  entities), not usage.
- `observability/` — where audit-log retention and what else gets
  logged locally are covered in full.
