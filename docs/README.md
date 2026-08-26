# docs/Public

This is the **only** part of `Contextual/docs/` that ever leaves this
repo. Everything else in `docs/` is private, internal engineering
documentation and stays here.

## What this is

The authoring source for Contextual's public documentation. Written here,
in the same PR as the code change that made a doc update necessary.
On every push to `main` that touches this folder, the whole tree syncs
automatically to `Contextual-Web`, which builds and serves the public
docs website and also mirrors this folder on to `Contextual-Labs`
(receive-only there) — never edited by hand in either downstream repo.

`Contextual-Labs` is the published, publicly-browsable copy and also
where GitHub Issues lives as the real customer support/bug-report
channel.

## Structure

18 top-level domains, each following Diátaxis internally
(`tutorials/how-to/reference/explanation`, plus two cross-cutting,
non-Diátaxis sections below). Replaces the prior 6-domain shape
(`cli/`, `mcp-server/`, `engine/`, `account/`, `web-dashboard/`,
`changelog/`) — `engine/` and `mcp-server/` are fully dissolved,
`web-dashboard/` is renamed, nothing else from the old tree survives
under its old name.

```
getting-started/    site map, architecture overview, guided tour, wayfinding
cli/                 reference/{general,config,client,mcp,workspace,skill}/, + tutorials, how-to, explanation
mcp/
  server/            the daemon: lifecycle, hot-reload, access control
  tools/             all 24 MCP tools: reference, how-to, explanation, tutorials
indexing/            chunking, embedding, graph extraction, blame, sizing, language support
retrieval/           hybrid BM25+dense+trigram ranking, MMR diversity
graph/               entities, predicates, confidence tiers, CHA/RTA
temporal/            blame-enriched history, staleness scoring, co-change
nexus/               what nexus_search adds over search
models/              the embedding model stack
configuration/       config keys, precedence, what each one controls
observability/       logs, doctor output, OTel tracing (what's real vs. not-yet-surfaced)
evaluation/          golden repos, query suite, metrics, hardware profiles behind any accuracy claim
troubleshooting/      cross-cutting, symptom-indexed — NOT nested under any one subsystem
integrations/        one reference page per supported AI client (11), + connect/switch/remove
trust-and-privacy/   what stays local, license/auth model, security disclosure scope
account/             CLI-side license/trial/activation (node-locked model)
website/             account dashboard: billing, login/auth, device management
changelog/           release notes (intended to be live-pulled eventually)
```

`troubleshooting/` and `observability/` are deliberately **not** nested
inside any single subsystem domain, even though they're engine-adjacent —
a user hitting "MCP daemon not responding" doesn't know in advance
whether that's an indexing, retrieval, or graph problem, so both stay
cross-cutting and symptom/log-indexed rather than fragmented per
subsystem (mirrors Stripe's unified error-code reference).

`mcp/server/` and `mcp/tools/` share the `mcp/` URL prefix but are two
distinct `domain` values in frontmatter (`mcp-server`, `mcp-tools`) —
the one deliberate exception to "domain value == top-level folder name"
in this tree. `cli/reference/` nests one level deeper by command family
(`general`, `config`, `client`, `mcp`, `workspace`, `skill`) — still a
single `cli` domain, just an extra path segment under `reference/` only.

URL scheme is otherwise still 1:1 with this folder structure —
`/docs/cli/reference/general/login` maps directly to
`cli/reference/general/login.md` here.

## meta.json

Root-level `meta.json` carries a single `docsVersion` field — the engine
version this docs tree has actually been verified against, not
necessarily whatever's newest on PyPI (PyPI can run ahead of doc
verification). Bump it by hand whenever a content-authoring pass is
checked against a release. Web's `fetch-docs-content.mjs` stages the
whole tree already (see `generate-docs-pages.mjs`'s `.md`-only filter —
this file is skipped by the page walk, so it's safe as a sibling to the
domain folders), so no separate sync step is needed for it to reach
`Contextual-Labs`.

## Page frontmatter

Every authored page (not the category `README.md` stubs) carries:

```yaml
---
title: string          # required. page H1 / <title>.
domain: getting-started | cli | mcp-server | mcp-tools | indexing
       | retrieval | graph | temporal | nexus | models | configuration
       | observability | evaluation | troubleshooting | integrations
       | trust-and-privacy | account | website | changelog
category: tutorial | how-to | reference | explanation
          # troubleshooting has no sub-categories, its domain folder holds
          # pages directly.
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
directly in this markdown with no per-file import.

## Status

Every domain/subdomain exists on disk with real frontmatter (title,
domain, category, tldr) on every page. Most pages carry real, verified
content; a page still awaiting its content-authoring pass is marked
inline with a "structural placeholder" note rather than silently
shipping an empty page. `evaluation/` is held back deliberately until
every golden repo has a verified benchmark baseline — see
`eval/baselines/CHANGELOG.md` and `eval/manifest.yml` for current
coverage before writing real content into that domain.
