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

## Structure (restructured 2026-07-25 — see `Teams/Operations/docs-domain-restructure-2026-07-25.md`)

17 top-level domains, each following Diátaxis internally
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
  tools/             all 22 MCP tools: reference, how-to, explanation, tutorials
indexing/            chunking, embedding, graph extraction, blame, sizing, language support
retrieval/           hybrid BM25+dense+trigram ranking, MMR diversity
graph/               entities, predicates, confidence tiers, CHA/RTA
temporal/            blame-enriched history, staleness scoring, co-change
nexus/               what nexus_search adds over search
models/              the embedding model stack
configuration/       config keys, precedence, what each one controls
observability/       logs, doctor output, OTel tracing (what's real vs. not-yet-surfaced)
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

## Page frontmatter

Every authored page (not the category `README.md` stubs) carries:

```yaml
---
title: string          # required. page H1 / <title>.
domain: getting-started | cli | mcp-server | mcp-tools | indexing
       | retrieval | graph | temporal | nexus | models | configuration
       | observability | troubleshooting | integrations
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
directly in this markdown with no per-file import. See
`Teams/Memory/docs-authoring-components-2026-07-23.md` for the full
component contract.

## Status

**Structural scaffold complete, 2026-07-25**: every domain/subdomain
above exists on disk with real frontmatter (title, domain, category,
tldr) on every page. Pages carried over from the prior 6-domain tree
(`cli/`, `mcp-server/`, `engine/`, `web-dashboard/`) are real, previously
-authored content, moved and re-pointed — not stubs. Net-new pages (the
six former-`engine/`-subsystem domains' extra depth, all 11
`integrations/reference/` pages, `observability/`'s OTel-specific pages,
`getting-started/`'s wayfinding page) are structural placeholders —
real title/domain/category/tldr, body content not yet written. See
`Teams/Operations/docs-domain-restructure-2026-07-25.md` for the full
before/after page manifest and what's pending Web's confirmation before
the real content-authoring pass begins.

---
Pipeline test: 2026-07-11T14:59:07Z
