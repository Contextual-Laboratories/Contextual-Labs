---
title: How the Decision Record Feature Is Verified
domain: evaluation
category: reference
tldr: A separate 100-ADR fixture suite drives the real decision_* MCP tools through chain creation, supersession, and search, then verifies chain integrity and that decision_search never surfaces a superseded record.
related:
  - evaluation/explanation/how-we-evaluate-retrieval-quality.md
---

<Callout variant="tldr">
The retrieval-recall suite covers search and the dependency graph. A
separate, smaller eval — `eval/run_adr_eval.py` — exists specifically
to verify the architectural-decision-record (ADR) feature: chain
integrity, chronological ordering, and that a superseded decision
never surfaces from search.
</Callout>

## A different feature, a different eval

This suite doesn't touch any golden repo or `eval/repos/` — it creates
its own throwaway workspace, since `decision_create` only needs a
registered workspace, not an indexed one. It drives a fixture of 100
architectural decision records — 30 standalone plus 70 chained across
22 revision chains (4 five-revision chains, 4 four-revision, 6
three-revision, 8 two-revision) — through the real MCP
`decision_create`/`decision_update`/`decision_supersede` tools, exactly
as a real client would.

## Realistic timestamps, real tools

The real `decision_*` tools always timestamp a record at the moment
it's created — there's no parameter to backdate one. Since testing
chronological chain ordering meaningfully needs decisions spread
across a realistic historical range rather than clustered in the same
few-minute test run, the fixture's timestamps are backdated after
creation via a direct, low-level database write — the same write path
the decision-record service itself uses internally, just invoked
directly for this one setup step. Every ADR is still created,
superseded, and linked through the real MCP tools first; only its
`valid_at` timestamp is adjusted afterward, producing a realistic
spread from roughly a year old down to a few days old.

## What gets verified

Once the fixture is loaded, everything is re-checked through the real
MCP tools — never assumed from the setup step:

- Every one of the 100 ADRs is present via `decision_list`.
- Each chain's `status`, `supersedes_id`, and `superseded_by_id` are
  correct, and walking the chain forward and backward from any link
  lands on the right neighbors.
- Timestamps within a chain are chronologically non-decreasing.
- **The core invariant**: `decision_search` never surfaces a
  superseded ADR, regardless of how closely its text matches the
  query — a superseded decision should be discoverable through
  `decision_list`'s full history, never mistaken for the current
  answer via search.
