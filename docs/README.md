# docs/Public

This is the **only** part of `Contextual/docs/` that ever leaves this
repo. Everything else in `docs/` is private, internal engineering
documentation and stays here.

## What this is

The authoring source for Contextual's public documentation. Written here,
in the same PR as the code change that made a doc update necessary.
Synced automatically to the public `Contextual-Labs` repo on every merge
to main that touches this folder — never edited by hand over there, and
never touched directly by anyone outside this repo.

`Contextual-Labs` is the published, publicly-browsable copy and also
where GitHub Issues lives as the real customer support/bug-report
channel. `Contextual-Web`'s marketing site renders the final docs website
by pulling from `Contextual-Labs` at build time. One-way flow: author
here → publish on Labs → render on Web.

## Structure

Mirrors `Contextual-Labs`'s own structure 1:1 (surface × Diataxis
category), so the sync is a plain recursive file copy with no path
remapping:

```
cli/            {tutorials, how-to, reference, explanation}/
mcp-server/     {tutorials, how-to, reference, explanation}/
web-dashboard/  {tutorials, how-to, reference, explanation}/
concepts/       cross-cutting explanation (architecture, graph layer, temporal)
account/        licensing, trial, device management
changelog/       release notes (intended to be live-pulled eventually)
```

URL scheme on the rendered site: 1:1 with this folder structure —
`/docs/cli/reference/login` maps directly to
`cli/reference/login.md` here.

## What's not here yet

Structure only, no real content — content authoring is a separate,
later pass (see `Teams/TODO.md`). This phase is infrastructure: the sync
mechanism, the URL scheme, the taxonomy shape.
