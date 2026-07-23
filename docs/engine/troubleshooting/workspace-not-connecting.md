---
title: "Workspace won't connect to the daemon"
domain: engine
category: troubleshooting
tldr: This almost always means the workspace isn't registered, its name is ambiguous with another, or the daemon itself isn't running — three distinct, distinguishable causes, not one generic connection failure.
order: 2
---

<Callout variant="tldr">
"Workspace not connecting" is never one error — it's one of three
specific, distinguishable causes: no workspaces registered at all, the
one you meant is ambiguous with another, or the daemon backing all of
them isn't actually running. Each has its own message and its own fix.
</Callout>

## Cause 1 — no workspaces are registered yet

If you haven't run `contextual init` (or added an existing workspace) at
all, there is nothing for an AI client to connect to. The fix is direct:

<Terminal lines={[
  {command: "contextual workspace add <path>"},
  {output: "Workspace registered.", muted: true}
]} />

## Cause 2 — the workspace name is ambiguous

If you have more than one registered workspace and a tool call doesn't
specify which one, Contextual won't guess — it lists every registered
workspace and asks you to be specific rather than silently picking one.
This is deliberate: silently picking the wrong workspace and answering a
question about the wrong repository is worse than asking once.

**Fix**: specify the workspace explicitly in the tool call, or run
`contextual workspace list` to see the exact names you can use.

## Cause 3 — the name doesn't match any registered workspace

If you specify a workspace hint that doesn't match anything registered,
you'll get an explicit list of what *is* registered rather than a flat
"not found." Check for a typo, or confirm you actually ran
`contextual workspace add` for the one you meant.

## Cause 4 — the workspace is registered, but the daemon isn't running

This is a different failure mode from the three above: the workspace
itself is fine, but nothing is listening for the MCP server to talk to.

<Terminal lines={[
  {command: "contextual mcp status"},
  {output: "Daemon: not running", muted: true},
  {command: "contextual mcp start"},
  {output: "Daemon started (pid 41213, port 8420).", muted: true}
]} />

<Callout variant="warning">
Don't kill daemon processes by hand (`kill -9`, Activity Monitor/Task
Manager) as a first move — a daemon that's genuinely stuck is rare
compared to a client simply not knowing it needs to reconnect. Use
`contextual mcp restart` first; it's the same effect without leaving a
stale lockfile behind.
</Callout>

## If none of the above explains it

Run `contextual doctor` and check the **Daemon & Locks** and **MCP
Integration** lines specifically — see
`engine/observability/interpreting-doctor-report` for what each one
means. If those both pass and the client still can't connect, the issue
is more likely in the client's own MCP configuration than in Contextual
itself — re-run `contextual client --<your-client>` to rewrite it fresh.
