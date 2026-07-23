---
title: "Your first grounded query"
domain: mcp-server
category: tutorial
tldr: Ask your AI client a real question about your own code — it should call search automatically and answer from what the tool actually returns, not from a guess.
order: 2
---

<Callout variant="tldr">
Once connected, ask your AI client something specific about your own
repository. It should reach for the `search` tool automatically and
ground its answer in what that tool actually returns — not in a guess
about what code "usually" looks like.
</Callout>

## A worked example

Ask something a teammate might ask you, not a generic question: "what
does the `login` command actually do if I'm already authenticated?"

Behind the scenes, a well-behaved AI client:

1. Recognizes this needs real information about your specific code, not
   something answerable from training data.
2. Calls `search(query="login command already authenticated behavior")`.
3. Gets back ranked chunks — in this case, the real `login` function
   and its early-return-with-a-warning behavior.
4. Answers using that actual code, ideally citing the file and line.

## How to tell if it's actually grounded

A grounded answer references something specific enough that you could
go look it up yourself — a function name, a file path, a real behavior.
A guess sounds confident but generic. If an answer feels generic for a
question this specific, that's a signal the client didn't actually call
a tool — check `mcp-server/how-to/debug-a-tool-returning-zero-results`
if you suspect the call happened but came back empty.

## Going deeper than one search call

For anything involving "what depends on this" or "what changes
together with this," a single `search` call isn't enough — see
`mcp-server/explanation/tool-taxonomy` for when to reach for the graph
and temporal tools instead.

## See also

- `mcp-server/reference/search`.
- `mcp-server/explanation/why-grounded-retrieval-beats-guessing`.
