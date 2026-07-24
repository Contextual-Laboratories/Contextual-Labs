---
title: "A Guided Tour: From a Vague Question to a Graph-Grounded Answer"
domain: getting-started
category: tutorial
tldr: Walk through what actually happens end to end when you ask an AI client a vague question about your codebase — from indexing, through hybrid retrieval, into the dependency graph, and out the other side as a cited, traceable answer.
order: 1
related:
  - indexing/explanation/how-indexing-works.md
  - retrieval/explanation/retrieval-pipeline.md
  - nexus/explanation/nexus-enrichment.md
  - graph/explanation/the-knowledge-graph.md
---

<Callout variant="tldr">
This walks through one realistic question end to end — not tool syntax,
but what actually happens underneath: how your codebase became
queryable in the first place, what runs when your AI client asks a
question on your behalf, and how the answer that comes back is
traceable to real code rather than a plausible-sounding guess.
</Callout>

This tutorial assumes you've already been through
`cli/tutorials/getting-started` — a workspace exists, it's indexed, and
an AI client is connected. What follows is what's actually happening
behind that one grounded-question moment.

## The question

Say you're new to a codebase and ask your AI client something genuinely
vague: *"How does authentication actually work in this project?"* Not a
function name, not a file path — the kind of question you'd ask a
teammate on day one.

## Step 1 — this question could only be answered because indexing already ran

Before this question meant anything to Contextual, `contextual index` had
already: chunked every file, extracted a dependency graph of entities and
relationships, walked git blame history, and embedded everything with a
local model — all six stages covered in
`indexing/explanation/how-indexing-works`. None of that happens at query
time; it's why an answer comes back in seconds instead of Contextual
re-reading your entire repository on the spot.

## Step 2 — your client picks a tool, not just a search box

Your AI client doesn't send your question as a literal search string
to one hardcoded tool. It has Contextual's MCP tools available and
decides which one fits — for a broad, conceptual question like this one,
that's typically `nexus_search` rather than plain `search`, precisely
because the question is about *how a subsystem works*, not *find one
specific thing*.

## Step 3 — semantic seeding, not keyword matching

`nexus_search` embeds "how does authentication actually work" with the
same local model used at index time, and matches that embedding directly
against the graph's own node vectors — finding the entities most
conceptually related to authentication, even if none of them literally
contain the word "authentication." This is the same underlying embedding
mechanism `search` uses for its dense-retrieval arm (see
`retrieval/explanation/retrieval-pipeline`), applied here directly against
graph nodes as seeds rather than chunk text.

## Step 4 — walking outward through real structure

From those seed nodes — probably something like a `login` function, an
`AuthMiddleware` class, an OAuth client module — the graph traversal
described in `graph/explanation/the-knowledge-graph` walks outward
through real `calls`, `imports`, and `inherits_from` edges. This is the
step a plain text search can't do: it's how the answer ends up covering
the actual call chain (a route handler that calls a login function that
calls into an OAuth client) instead of just the handful of files that
happen to mention "auth" in a comment.

## Step 5 — temporal context, attached automatically

Every node picked up along the way gets its temporal context attached —
who last touched it, how recently, and a staleness score (see
`temporal/explanation/temporal-intelligence`). If the authentication code
was substantially rewritten last month, that's part of what comes back,
not a separate question you'd have to think to ask.

## Step 6 — an answer with a trail back to real code

What your AI client receives isn't a paragraph asserting how
authentication works — it's real nodes with real code, real edges
between them, and real commit/staleness data. The AI client synthesizes
the explanation, but every claim in it traces back to an actual function,
an actual file, an actual commit — not a plausible-sounding guess about
what a typical auth system probably looks like. That traceability is the
entire point: a guess from a general-purpose model and an answer grounded
this way can read identically in prose, but only one of them survives you
asking "wait, where's that actually defined?"

## What would change with a narrower question

If instead you'd asked "what calls `validate_token`," your client would
likely reach for a graph tool directly (`graph_get_entity_callers`)
rather than `nexus_search`'s broader semantic-plus-structural sweep — see
`mcp/tools/how-to/choose-between-search-and-nexus-search` for that more
tactical decision. The mechanism underneath is the same knowledge graph either
way; only the entry point differs.
