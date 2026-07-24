---
title: Language Support Matrix
domain: indexing
category: reference
tldr: Languages fall into three real tiers — full graph (entities plus calls, imports, inheritance, CHA/RTA), partial graph (entities only), and chunk-only (search but no graph entities) — check this before assuming graph_impact or graph_traverse will have anything to say about your language.
order: 3
related:
  - graph/explanation/the-knowledge-graph.md
  - indexing/explanation/how-indexing-works.md
---

<Callout variant="tldr">
Every language Contextual recognizes gets chunked and is searchable. Only
some get graph entities extracted, and only a subset of those get full
calls/imports/inheritance resolution feeding `graph_impact`,
`graph_traverse`, and CHA/RTA virtual-dispatch resolution. Check your
language's tier before assuming a graph tool will have anything to say
about it.
</Callout>

## The three tiers

**Full graph** — entities plus calls, imports, inheritance, and
constructor-binding resolution, feeding CHA/RTA virtual-dispatch analysis
(see `graph/explanation/the-knowledge-graph`):

Python, TypeScript, TSX, JavaScript, Go, Java, Rust, C#, Kotlin, Swift,
C++, PHP, Ruby.

**Partial graph** — entities are extracted (functions, classes, etc.
show up as graph nodes), but calls/imports/inheritance resolution is
limited or absent, so relationship-dependent tools like `graph_impact`
and `graph_traverse` will have little or nothing to traverse:

C, Protocol Buffers, HCL (Terraform), Groovy, Nim, Zig, V, Elm, OCaml,
Erlang, Julia, Objective-C, Solidity.

**Chunk-only** — fully searchable via `search` and `nexus_search`'s
semantic/keyword paths, but no graph entities at all — `graph_impact`,
`graph_traverse`, and similar tools have nothing to find for these files:

Scala, Dart, Lua, Perl, R, Haskell, Elixir, plus structured/config
formats: JSON, YAML, TOML, Dockerfile, Markdown, SQL, HTML, CSS, SCSS.

## What this means practically

If you're asking "what calls this function" or "what's the blast radius
of changing this" (`graph_impact`), that only works for full-graph
languages — for anything in the other two tiers, `search` and
`nexus_search`'s semantic matching are your real tools, since there's no
structural graph to traverse. If a graph tool returns an unexpectedly
empty result for a partial- or chunk-only-language file, that's expected
tier behavior, not a bug — see
`troubleshooting/entity-not-found` if you're unsure whether you're
looking at "no graph for this language" versus a genuine indexing issue.

## An unsupported file extension

If your language isn't listed above at all, it's not indexed — full stop,
not silently degraded to chunk-only. Chunking depends on tree-sitter
having a grammar wired in for that extension.
