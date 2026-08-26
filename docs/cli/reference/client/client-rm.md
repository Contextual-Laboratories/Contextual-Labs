---
title: "client rm"
domain: cli
category: reference
tldr: "contextual client rm --<name> removes this workspace's MCP entry from that client's config — UI-based clients like ChatGPT and Perplexity have nothing local to remove."
order: 28
---

<Callout variant="tldr">
`contextual client rm --<name>` removes the current workspace's entry
from that client's config file. Same flag set as `contextual client`.
</Callout>

## Usage

```
contextual client rm --claude | --claude-code | --chatgpt | --perplexity
                      | --gemini-cli | --cursor | --copilot | --windsurf
                      | --codex | --antigravity | --cline
```

Same 11 client flags as `contextual client` (no `--dry-run` here).
Exactly one is required per call — omitting all of them, or passing
more than one, is rejected with an error rather than doing nothing or
guessing. `--chatgpt` and `--perplexity` are UI-based clients — there's
nothing local for either of them to remove, so those two only revoke
that client's access token (see `cli/reference/client/client-config`)
and report "nothing local to remove" rather than pretending to delete a
file.

<Terminal lines={[
  {command: "contextual client rm --cursor"},
  {output: "Removed Cursor MCP entry and .cursor/rules/contextual.mdc.", muted: true}
]} />

If nothing was configured for that client in this workspace to begin
with, `client rm` says so plainly instead of erroring — it's always
safe to run against a client you're not sure is connected.

<Callout variant="note">
This is scoped to the current workspace only — running `contextual
client rm --cursor` from `api-core` never touches Cursor's connection to
a different registered workspace like `docs-site`. To disconnect a
client everywhere, run it once per workspace, or use `contextual
--purge` to tear down one workspace (and every client's connection to
it) entirely.
</Callout>

## See also

- `cli/reference/client/client`, `cli/reference/client/client-list`.
