---
title: "uninstall"
domain: cli
category: reference
tldr: "contextual uninstall [--purge] [--yes] removes global Contextual state (~/.contextual/) from this machine, requiring you to type the machine's hostname to confirm — it never touches per-project .contextual/ directories."
order: 24
---

<Callout variant="tldr">
`contextual uninstall` removes Contextual's global state from this
machine — config, license/session credentials, the global index, daemon
files — after you type your machine's hostname to confirm. It preserves
downloaded model weights unless you pass `--purge`, and it never touches
any individual repository's `.contextual/` directory.
</Callout>

## Usage

```
contextual uninstall [--purge] [--yes]
```

- `--purge` — also remove downloaded ONNX model weights (large; you'd
  need `contextual fetch` again afterward).
- `--yes`, `-y` — skip the confirmation prompt.

<Terminal lines={[
  {command: "contextual uninstall"},
  {output: "This will remove Contextual state (config, license, index cache) from this machine.\nYour indexed project repositories are not touched.\n\nType this machine's name (marys-macbook-pro) to confirm: marys-macbook-pro\nStopping the running daemon...\nGlobal state removed.", muted: true}
]} />

## What this does and doesn't remove

**Removed**: the running daemon (stopped first), global config,
session/license credentials, the global index, daemon lock/log/socket
files, and the workspace registry.

**Preserved by default**: downloaded embedding model weights under
`~/.contextual/models/` — hundreds of MB, slow to re-fetch. Pass
`--purge` for a full wipe including these.

**Never touched**: any individual repository's `.contextual/` directory.
Those are listed at the end for manual cleanup if you want them gone
too.

<Callout variant="note">
This only removes Contextual's state, not the package itself. To remove
the package, run `pip uninstall contextual-engine` (or the `uv`/`pipx`
equivalent) separately.
</Callout>

## See also

- `cli/reference/setup` — the reverse of this command.
