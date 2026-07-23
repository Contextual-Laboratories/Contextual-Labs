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
engine/         {tutorials, how-to, reference, explanation,
                 troubleshooting, observability}/
account/        licensing, trial, device management
changelog/       release notes (intended to be live-pulled eventually)
```

`engine/` replaced the earlier `concepts/` placeholder — full Diátaxis
coverage for indexing/retrieval/graph/temporal internals, not just a
handful of explanation pages, plus two sub-clusters that aren't general
Diátaxis categories: `troubleshooting/` (symptom-indexed lookups) and
`observability/` (logs, telemetry, `doctor` output).

URL scheme on the rendered site: 1:1 with this folder structure —
`/docs/cli/reference/login` maps directly to
`cli/reference/login.md` here.

## Page frontmatter

Every authored page (not the category `README.md` stubs) carries:

```yaml
---
title: string          # required. page H1 / <title>.
domain: cli | mcp-server | engine | account | web-dashboard | changelog
category: tutorial | how-to | reference | explanation
          | troubleshooting | observability
          # troubleshooting/observability are engine/-only.
tldr: string           # required. 1-2 plain-text sentences, no markdown.
                       # feeds llms.txt entries + the search index; also
                       # restated by hand as the page's opening
                       # <Callout variant="tldr"> in the body.
order: integer         # optional. sort weight within a category.
related: string[]      # optional. relative paths for "see also" links.
---
```

`<Callout variant="tldr" | "note" | "warning">` and
`<Terminal lines={[...]} />` are both real, live MDX components — usable
directly in this markdown with no per-file import. See
`Teams/Memory/docs-authoring-components-2026-07-23.md` for the full
component contract.

## Status

Content authoring is underway (see `Teams/Operations/docs-content-
buildout-joint-mission.md` for the tracked plan) — a first real slice
exists in `cli/tutorials/` and `engine/`; most pages across all domains
are still structure-only stubs, filled in over subsequent passes.

---
Pipeline test: 2026-07-11T14:59:07Z
