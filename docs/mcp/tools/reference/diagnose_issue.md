---
title: "diagnose_issue"
domain: mcp-tools
category: reference
tldr: "diagnose_issue(symptom, since, workspace) — correlates a live get_doctor snapshot with recent get_telemetry activity into one ranked list of likely causes; the single-call starting point for \"something is wrong, help me figure out why\"."
order: 24
---

<Callout variant="tldr">
`diagnose_issue` is the one-call version of "run get_doctor and
get_telemetry, then figure out which of it actually matters" — it does
that correlation for you and hands back a ranked list of candidate
causes with concrete next steps.
</Callout>

## Parameters

- `symptom` (string, optional) — plain-language description of what's wrong, e.g. `"search is slow"`.
- `since` (string, default `"24h"`) — how far back to look for corroborating evidence.
- `workspace` (string, optional).

Returns `candidates` (ranked list of `{source, status, signal,
suggestion, error_code}`), `doctor_overall`, and `telemetry_summary`.
Does not search the web or change anything — it hands back a
structured diagnosis for the calling agent to act on.

`error_code` is always populated on every candidate: a real Contextual
error code when known, or the literal string `"uncoded (real error,
not yet mapped to a Contextual error code)"` when it isn't — the
latter still means a genuine problem was found, not that this tool
malfunctioned. Prefer each candidate's `suggestion` field (and run its
suggested command) over searching for the `error_code` itself, since
Contextual's docs aren't guaranteed to cover every code yet.

## When to use it (and when not to)

Call it when a problem is reported (or noticed) and you want one call
that gathers both current health and recent evidence, ranked by
severity. Skip it if you already know which subsystem is broken and
just want its live detail — call `get_doctor` directly — or you want
raw activity data with no interpretation — use `get_telemetry`.

## See also

- `mcp/tools/reference/get_doctor`, `mcp/tools/reference/get_telemetry`.
- `observability/how-to/interpreting-doctor-report`.
