---
title: Site Map
domain: getting-started
category: explanation
tldr: A map of every section of contextuallabs.dev and these docs — what lives where, and why.
order: 2
---

<Callout variant="tldr">
This page is the plain-text index behind the illustrated site map you see
above: every top-level docs section, what it covers, and the parts of
contextuallabs.dev that sit outside the docs entirely (marketing, pricing,
your account).
</Callout>

## The public site, outside these docs

- **Marketing pages** (home, pricing, changelog) — what Contextual is, what
  it costs, what shipped recently. Not part of the docs tree.
- **`website/`** — the logged-in account dashboard: billing, login/auth,
  device management, plan management. Covers the *website*, not the CLI's
  own license/trial mechanics.
- **`account/`** — the CLI-side license: activation, trial state, the
  node-locked model. If you're asking "why does `contextual doctor` say my
  license is invalid," this is the section, not `website/`.

## The docs tree

**Start here**
- `getting-started/` (this section) — the architecture overview, a guided
  tour from a first question to a grounded answer, and this map.

**Using Contextual**
- `cli/` — all 30 commands, grouped by family (general, config, client,
  mcp, workspace, skill).
- `mcp/server/` — the background daemon: lifecycle, hot-reload, client
  access control.
- `mcp/tools/` — all 22 tools your AI client can call, one page each.
- `integrations/` — connecting, switching, and removing each of the 11
  supported AI clients.

**How it works underneath**
- `indexing/` — chunking, embedding, graph extraction, blame, sizing,
  language support.
- `retrieval/` — hybrid ranking (BM25 + dense + trigram), MMR diversity.
- `graph/` — the knowledge graph: entities, predicates, confidence tiers.
- `temporal/` — blame-enriched history, staleness scoring, co-change.
- `nexus/` — what `nexus_search` adds on top of plain `search`.
- `models/` — the embedding model stack: what runs, where, and why.
- `configuration/` — every config key, precedence, what it controls.

**When something needs attention**
- `observability/` — logs, the `doctor` report, what's traced via OTel.
- `troubleshooting/` — symptom-indexed: start from what you're seeing.
- `trust-and-privacy/` — what stays local, how licensing/auth works,
  security disclosure scope.

**Reference-only**
- `changelog/` — release notes, live-pulled, not hand-authored.

<Callout variant="note">
The illustrated version of this map is a custom, branded component — this
page is its content source and the fallback for readers (and crawlers)
that don't render it.
</Callout>
