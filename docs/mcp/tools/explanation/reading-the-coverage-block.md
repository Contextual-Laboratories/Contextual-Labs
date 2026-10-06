---
title: "Reading the coverage block — what was returned, and what wasn't"
domain: mcp-tools
category: explanation
tldr: "graph_impact, graph_traverse, graph_get_entity_callers and nexus_search all report completeness through one _meta.coverage object — returned, has_more, confidence, capped_by, and an honest unresolved_count — so a result can never claim to be both complete and truncated."
order: 3
---

<Callout variant="tldr">
Four tools — `graph_impact`, `graph_traverse`,
`graph_get_entity_callers`, and `nexus_search` — answer "how much of the
real answer am I looking at?" with a single `_meta.coverage` object.
Read it before treating an empty or short list as a finished answer.
</Callout>

## Why one object

These tools used to describe completeness through several separate
fields (`completeness`, `truncated`, `confirmed_count`) computed from
different counts. They could disagree — a response could say it was
statically complete and truncated at the same time. `coverage` replaces
all of them. Every field below is derived from the same set of facts, so
the combinations that used to contradict each other cannot be produced.

## The fields

```json
"coverage": {
  "returned": 12,
  "has_more": true,
  "confidence": "resolved_but_capped",
  "total_known": 31,
  "capped_by": ["limit"],
  "hint": "Raise the `limit` parameter to see more results."
}
```

- `returned` — how many items are in this response.
- `has_more` — `true` if anything was held back by a cap (see
  `capped_by`). `false` means nothing was removed *after* the search
  found it.
- `confidence` — one of three values:
  - `"resolved"` — nothing was capped, and results were found. The list
    reflects everything found under this call's own filters.
  - `"resolved_but_capped"` — real results were found, but something you
    didn't directly control removed some before you saw them. Never
    reported together with `has_more: false`.
  - `"unknown"` — nothing was returned and nothing was capped. This is
    **not** the same claim as "verified: nothing depends on this." It
    means the tool found nothing, and the graph declines to guess at calls
    it can't type-resolve. Confirm the entity itself exists (see
    `troubleshooting/entity-not-found`) before reading it as "safe."
- `total_known` — the best count actually available at that point. It is
  left out entirely when no honest total exists, rather than printing a
  number that only looks precise. It can itself be a lower bound if an
  internal ceiling was hit before ranking (see `hydration_cap` below).
- `capped_by` — present only when something was capped. Each entry names
  a mechanism:

  | Value | What happened | Does raising `limit` help? |
  |---|---|---|
  | `limit` | You asked for `limit` results and more matched. | Yes |
  | `hydration_cap` | The candidate pool exceeded an internal ceiling before ranking began. | No — narrow the query (more specific entity, predicate, or direction) |
  | `diversity_cap` | Results are capped per source file so one file can't crowd out the rest. | No — ask about that file directly |
  | `token_budget` | The response hit its shared token budget. | No — set `include_code=false` and/or `no_preview=true`, or narrow the query |

- `hint` — remediation text matched to the cause(s) in `capped_by`, not a
  generic "raise limit" for every case.
- `metadata_stripped` — present (`true`) when low-priority fields were
  dropped from nodes to fit the response budget. The field names are
  listed in `_meta.omitted_fields`. This is separate from `has_more`:
  the same items came back, just with less detail on each.
- `unresolved_count` — see below.

## `unresolved_count` — calls the graph saw but couldn't bind

`graph_impact` and `graph_get_entity_callers` add `unresolved_count`
when it is non-zero. When the resolver finds a call like
`entry.reject(...)` and can't determine what `entry` is (a loop variable
or an untyped parameter, say), it deliberately declines to guess a
target, since a wrong guess would add false dependents to the graph. It
records the call as unresolved instead. `unresolved_count` is the number
of those unresolved call sites that **could be calls into the entity you
asked about**.

A call site counts only when all three hold:

- the name being called is exactly this entity's name;
- the calling file is the same language as the entity's file; and
- the calling file is the entity's own file, or depends on it (it
  imports from it, directly or through the file-level dependency graph).

Name alone isn't enough. Common names like `get` or `append` would match
hundreds of unrelated calls, so the file-dependency check does the real
filtering. That also makes this a heuristic, not a bound: a call through
an object that never touches the entity's file isn't counted, and a
same-named method in a dependent file may be counted even though it's a
different method. Treat it as "N places that look like they might call
this," not as a confirmed number and not as a ceiling.

Because of that, it never changes `confidence`. An unresolved call is
neither a confirmed dependent nor proof that one exists, so
`confidence: "unknown"` with `unresolved_count: 3` means "no confirmed
dependents, but three call sites the graph couldn't resolve look like
candidates; read them before treating this as safe to change." If the
underlying query hit its internal row ceiling, `unresolved_count_capped`
is `true` and the count is a lower bound. Workspaces indexed before the
file-level dependency edges were added will under-report until their next
index.

## Tool-specific fields

- `graph_traverse` — `dependents_confidence` appears for
  `direction="backward"` and `"bi"`. It judges only the backward
  results, so a `"bi"` call's forward results can't make an empty
  backward result look resolved. Values: `"resolved"` (backward
  dependents found), `"unknown"` (none found), `"speculative_only"`
  (you passed `predicate="potential_call"`, so any results are
  low-confidence by construction).
- `graph_impact` — `speculative_supplement_count` appears when
  `speculative_callers` is non-empty (`change_type` `"rename"` or
  `"signature_change"`). It is a separate count on purpose:
  `speculative_callers` is its own list and is never mixed into
  `impacted_nodes`, so `confidence` still describes `impacted_nodes`
  honestly.

## The `_status` sentence

When `has_more` is `true`, the response also leads with a top-level
`_status` sentence such as "Showing 12 of 31 matches — Raise the limit
parameter to see more results." It carries the same facts as `coverage`
in a form that is hard to skim past. When nothing was capped, `_status`
is absent.

## `response_budget` and `omitted_fields`

Two further `_meta` fields explain *how* a response was fit under its
size budget:

- `response_budget` — appears when `include_code` is on, or when the
  budget excluded any item. It reports `budget_tokens`, `used_tokens`,
  `full_detail_count`, `preview_count`, and `excluded_by_budget`. Items
  are kept in rank order; when the next item no longer fits even as a
  preview, it and everything after it are left out, and
  `excluded_by_budget` says how many.
- `omitted_fields` — appears when node metadata was trimmed. Field groups
  are dropped in a fixed order, first group first, before any whole node
  is dropped. The order differs by tool to match what each tool is for:

  | Tool | Dropped first → last |
  |---|---|
  | `graph_impact`, `graph_traverse`, `graph_get_entity_callers`, `nexus_search`, `graph_find_path` | commit data, authorship, temporal, edges, degree, staleness |
  | `graph_at_time` — the point is history, so temporal fields are protected longest | staleness, degree, commit data, authorship, edges, temporal |
  | `graph_query` — edges are the comparison axis, so they're protected longest | commit data, staleness, authorship, temporal, degree, edges |

  The groups are: staleness (`staleness_score`, `is_stale`); degree
  (`out_degree`, `in_degree`); edges (`edges`, `co_change_count`);
  temporal (`valid_at`, `relative_time`, `change_count`,
  `change_velocity`); authorship (`author`); commit data (`commit_hash`).

`graph_find_path`, `graph_at_time`, and `graph_query` apply the same
metadata budget and report `omitted_fields` when they trim anything
(in `_meta` for the first two, at the top level of the response for
`graph_query`). `graph_find_path` and `graph_at_time` also report
`response_budget`. None of the three uses `coverage`; they keep their
own `truncated` flags.

## See also

- `mcp/tools/reference/graph_impact`, `mcp/tools/reference/graph_traverse`,
  `mcp/tools/reference/graph_get_entity_callers`,
  `mcp/tools/reference/nexus_search`.
- `graph/how-to/interpret-a-blast-radius-report`.
- `graph/explanation/confidence-tiers-and-resolution` — the per-edge
  confidence scale, which is a different thing from `coverage.confidence`.
- `troubleshooting/entity-not-found`.
