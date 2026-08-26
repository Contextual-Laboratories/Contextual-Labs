---
title: "client list"
domain: cli
category: reference
tldr: "contextual client list [--<name>] [--all] [--expiry] shows registered clients, their access level, and which workspaces they're scoped to — defaulting to just the current workspace when run inside one."
order: 29
---

<Callout variant="tldr">
`contextual client list` prints registered clients, their access level,
and which workspaces each one is allowed to reach.
</Callout>

## Usage

```
contextual client list [--all] [--claude] [--claude-code] [--chatgpt]
                        [--perplexity] [--gemini-cli] [--cursor]
                        [--copilot] [--windsurf] [--codex]
                        [--antigravity] [--cline] [--expiry]
```

- `--all` — show every client × workspace registered on this device,
  ignoring the current-directory scoping described below.
- A single client flag (e.g. `--cursor`) — show only that one client,
  across every workspace it's connected to. Implies `--all`'s scope (no
  need to combine the two).
- `--expiry` — also show each token's expiry timestamp. Tokens renew
  automatically while actively used, so this is mainly a debugging aid,
  not something you need for normal use.

<Terminal lines={[
  {command: "contextual client list"},
  {output: "name          access   workspaces\nclaude-code   full     api-core\ncursor        full     api-core", muted: true}
]} />

## Scope: what "current workspace" means here

With no flags at all, `client list` scopes itself to whatever workspace
you're standing in:

- Inside a `contextual init`-ed workspace, it shows only that
  workspace's clients.
- Outside one, it falls back to showing every client on the device (the
  same as `--all`) and prints a note explaining why, rather than
  silently returning an empty list.

Passing any client flag or `--all` bypasses this scoping outright — a
client flag always means "this client, every workspace it's connected
to," never "this client, current workspace only."

<Callout variant="note">
The system-internal token used by git hooks and the embed-routing layer
is deliberately excluded from this list — it isn't a client an AI tool
connects as, so listing it here would be noise, not useful.
</Callout>

## See also

- `cli/reference/client/client-config` — where access levels are actually set.
- `mcp/server/explanation/mcp-client-access-control`.
