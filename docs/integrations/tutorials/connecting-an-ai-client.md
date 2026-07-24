---
title: Connecting an AI Client
domain: integrations
category: tutorial
tldr: Run contextual client --<name> from inside a workspace to wire that client's MCP config — repeat per client, and per workspace if you use more than one.
order: 2
---

<Callout variant="tldr">
`contextual client --<name>` writes (or updates) the MCP entry for the
current workspace into that client's own config. You'll do this once
per client, and once per workspace you want that client connected to.
</Callout>

This tutorial walks through connecting a second AI client to a
workspace you've already run `contextual init`/`contextual index`
against — the step covered briefly in `cli/tutorials/getting-started`.

## 1. Confirm which clients are supported

```
contextual client --help
```

As of this writing, the supported flags are `--claude` (Claude
Desktop), `--claude-code`, `--chatgpt`, `--perplexity`, `--gemini-cli`,
`--cursor`, `--copilot`, `--windsurf`, `--codex`, `--antigravity`, and
`--cline`. Run `--help` yourself rather than trusting a hardcoded list
here — this surface grows.

## 2. Run it from inside the workspace

```
contextual client --cursor
```

<Terminal lines={[
  {command: "contextual client --cursor"},
  {output: "Configured Cursor for this workspace.", muted: true}
]} />

The key detail: this command is workspace-scoped. Run it from inside
the repository you want that client connected to — not from your home
directory, and not expecting it to apply globally.

## 3. Connect more than one client to the same workspace

You can pass multiple flags in one call, or run the command again with
a different flag — both work:

```
contextual client --cursor --claude-code
```

## 4. Verify

```
contextual client list
```

<Terminal lines={[
  {command: "contextual client list"},
  {output: "NAME          ACCESS   WORKSPACES\ncursor        full     api-core\nclaude-code   full     api-core", muted: true}
]} />

## If a client isn't picking up the connection

Some clients (VS Code/Copilot, for example) need a window reload after
their config file changes. If a restart/reload doesn't fix it, check
`contextual mcp status` — see
`troubleshooting/workspace-not-connecting` if the daemon itself
isn't the problem.

## See also

- `cli/reference/client/client`, `cli/reference/client/client/client-rm`, `cli/reference/client/client/client-list`.
- `integrations/how-to/switch-or-add-a-second-ai-client`.
