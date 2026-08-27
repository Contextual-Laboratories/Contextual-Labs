---
title: What stays local
domain: trust-and-privacy
category: explanation
tldr: "The no-egress-by-default claim: what data never leaves your machine and why."
related:
  - trust-and-privacy/reference/data-privacy.md
  - getting-started/explanation/architecture-overview.md
  - models/explanation/embedding-model-stack.md
---

<Callout variant="tldr">
Contextual is built local-first, not "local-first except for the
expensive parts." Embedding, indexing, retrieval, and the graph all
run as local computation against a local database, using a local
model — by architecture, not by a setting you have to find and enable.
</Callout>

## Local-first is a design constraint, not a feature flag

A lot of code-intelligence tooling treats "send code to a hosted model"
as the default and "local mode" as an opt-in restriction with reduced
capability. Contextual is built the other way around: the embedding
model, the chunking and graph extraction, and every query path assume
no network round-trip from the start. There's no cloud tier of the
same product with a bigger model behind an API key — the local path
*is* the product, which is what makes "your code never leaves your
machine" a structural fact rather than a promise layered on top of an
architecture that could just as easily call out. For exactly what runs
where, see `trust-and-privacy/reference/data-privacy`.

## Why the file-discovery layer is conservative

Indexing walks your project directory to decide what to read, and two
choices there exist specifically to avoid pulling in things you didn't
mean to index:

- **Symlinks are never followed.** A symlink is a pointer, and
  following it means indexing whatever it points to — which might be
  outside your project entirely (`~/.ssh/`, a mounted secrets
  directory). Contextual treats a symlink as something to skip, not
  something to resolve and read through.
- **`.gitignore` is honored automatically**, not just
  `.contextualignore`. Most projects already encode "don't touch this"
  in `.gitignore` for things like `.env` files and credentials. Making
  Contextual respect that file too means a secret that's already kept
  out of version control doesn't end up in a searchable index just
  because nobody thought to also list it in a second ignore file.

## Why the daemon only trusts the local machine

The MCP daemon that answers search and graph queries binds to loopback
only and requires a bearer token on every request — a design aimed at
one specific threat model: another process or user on the *same*
machine, not a network attacker, since the daemon was never meant to
be reachable from elsewhere on your network in the first place. That's
a narrower promise than "encrypted and authenticated for any network,"
and it's deliberate — a local tool answering local queries about local
code doesn't need a network-facing security model, it needs a
local-process one. See `trust-and-privacy/reference/data-privacy` for
the platform-specific mechanics of how that boundary is enforced.

## Why this is worth stating explicitly

If your codebase can't be sent to a third party — proprietary,
regulated, under an NDA, or just something you'd rather not hand to an
external API — the usual response to a code-intelligence tool is "does
it have a local mode, and what does that mode give up?" Contextual's
answer is that there's nothing to give up: local isn't the restricted
path, it's the only path.
