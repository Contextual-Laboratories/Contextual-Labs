---
title: MCP Client Access Control
domain: engine
category: explanation
tldr: Which tools an AI client can call is governed entirely by clients.json access policies (full/read/custom/none) — a separate mechanism from license validity, which only controls whether the daemon starts at all.
order: 10
related:
  - engine/explanation/mcp-daemon-lifecycle-and-hot-reload.md
  - engine/explanation/architecture-overview.md
---

<Callout variant="tldr">
Per-client tool access (which of Contextual's MCP tools a given AI
client can call) is controlled entirely by local access policies in
`clients.json` — not by your license state. License validity is checked
once, at daemon startup, and decides whether the daemon runs at all; it
has no effect on what a running, already-started daemon exposes to which
client.
</Callout>

## The access-tier mechanism

Every client connected to your daemon — your terminal via the local
stdio shim, or an AI client like Claude Code, Cursor, or Windsurf — has a
policy recorded in `clients.json`. Each policy has an access level:

- **`full`** — every tool available.
- **`read`** — only read-only tools: `search`, `nexus_search`,
  `get_stats`, `get_file_content`, `get_repo_structure`, the read-side
  temporal tools (`decision_list`, `decision_search`,
  `get_temporal_context`, `co_change_analysis`), every graph tool (all of
  which are read-only by nature), plus `get_git_diff` and `get_doctor`.
- **`custom`** — only an explicit, named list of allowed tools.
- **`none`** — nothing. Every tool call is denied.

A locally-auto-provisioned `local` client is created with `full` access
on every daemon start — this is what the CLI's own stdio shim uses, by
design, so running Contextual against your own machine has zero
additional setup friction. Any other client (a remote AI tool, a second
machine) needs an explicit entry in `clients.json` with its own access
level.

This check happens per tool call, on an already-running daemon — it's the
mechanism `contextual client` commands manage, and it hot-reloads on
`SIGHUP` without a daemon restart (see
`engine/explanation/mcp-daemon-lifecycle-and-hot-reload`).

## What this has nothing to do with: your license

Separately, at daemon startup, Contextual validates your license
(offline, against a locally cached signed license file) and refuses to
start at all if it's invalid and outside any grace period. This is a
one-time, startup-only check — it decides whether the daemon boots, full
stop. It does not run per tool call, and it has no field or connection
anywhere in the access-policy mechanism above. A client with `full`
access on a daemon that started successfully can call every tool
regardless of anything about your license beyond the fact that it was
valid (or in grace) at the moment the daemon last started.

<Callout variant="note">
These are two independent systems solving two different problems: license
validity gates whether the daemon exists at all; `clients.json` access
policies gate what an existing, running daemon will do for a specific
caller. Don't read "the license gate is live" as "license state
determines which tools I can use" — those are different questions with
different answers.
</Callout>

## Managing access levels

`contextual client` (add, remove, list, config) is the CLI surface for
managing entries in `clients.json` — see `cli/reference/client.md` and
its subcommands for the exact flags. Changes take effect on the running
daemon without a restart.
