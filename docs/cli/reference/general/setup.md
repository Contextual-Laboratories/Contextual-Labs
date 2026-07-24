---
title: "setup"
domain: cli
category: reference
tldr: "contextual setup runs one-time global post-install setup (home directory, global DB/models/logs, optional model download and AI-client skill install) — refuses to run as root/Administrator."
order: 23
---

<Callout variant="tldr">
`contextual setup` performs the one-time global setup after installing
the package: creates `~/.contextual/`, and offers to download the
embedding model and install AI-client skills right there. It refuses to
run elevated (root/sudo, or Administrator), since that would bootstrap
state under the wrong account entirely.
</Callout>

## Usage

```
contextual setup
```

No arguments or flags.

<Terminal lines={[
  {command: "contextual setup"},
  {output: "Setting up Contextual...\nDownload the embedding model now (~130MB)? [Y/n]: y\nAdd Contextual skills to your AI systems now? [Y/n]: y\nSetup complete.", muted: true}
]} />

Both prompts can be deferred — answering no just means running
`contextual fetch` and `contextual skill` yourself later.

<Callout variant="warning">
Don't run this with `sudo` or as Administrator. It writes to your own
user profile's home directory; running it elevated would set that state
up under the root/Administrator account instead, and your normal user's
`contextual` commands would never see it.
</Callout>

## See also

- `cli/reference/general/fetch`, `cli/reference/skill`.
- `cli/reference/general/uninstall` — the reverse of this command.
