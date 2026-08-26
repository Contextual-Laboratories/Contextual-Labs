---
title: "Tool taxonomy — the five groups, and when to reach for which"
domain: mcp-tools
category: explanation
tldr: 24 tools across five groups — Search & Retrieval, System, Temporal & Decisions, Knowledge Graph, and Co-change — each answering a genuinely different kind of question.
order: 1
---

<Callout variant="tldr">
Contextual's 24 MCP tools split into five groups by the kind of
question they answer, not by which subsystem implements them. Reaching
for the right group first saves a chain of unnecessary calls.
</Callout>

## Search & Retrieval (5)

`search`, `nexus_search`, `get_file_content`, `get_repo_structure`,
`get_git_diff` — "find and read." Start here for any question about
what code exists, what it says, or what recently changed.

## System (4)

`get_doctor`, `get_stats`, `get_telemetry`, `diagnose_issue` — "is
Contextual itself healthy." Reach for these when something seems
broken or you want to know how fresh/large the index is or how it's
been performing, not when you're asking about the codebase's own
content.

## Temporal & Decisions (6)

`decision_create`, `decision_update`, `decision_supersede`,
`decision_list`, `decision_search`, `get_temporal_context` —
architectural decision records and per-entity history (blame, commits,
change velocity).

## Knowledge Graph (8)

`graph_traverse`, `graph_get_entity_callers`,
`graph_get_entity_definition`, `graph_find_path`, `graph_impact`,
`graph_stats`, `graph_query`, `graph_at_time` — structural relationships
(calls, imports, inheritance) between named entities, at current or
historical points in time.

## Co-change (1)

`co_change_analysis` — the one tool that answers a question the
structural graph literally cannot: what changes together in practice,
regardless of whether anything in the code explains why.

## Rule of thumb

If you don't have an entity ID yet, start in Search & Retrieval. If you
have one and want relationships, go to Knowledge Graph. If the question
is "why" or "who decided," go to Temporal & Decisions. If something
seems broken, go to System first, not last.

## See also

- `mcp/tools/explanation/why-grounded-retrieval-beats-guessing`.
