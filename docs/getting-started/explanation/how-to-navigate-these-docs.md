---
title: How to Navigate These Docs
domain: getting-started
category: explanation
tldr: Which section answers which kind of question, in one table.
order: 3
---

<Callout variant="tldr">
Every section follows the same shape — tutorial, how-to, reference,
explanation — so once you know *which section*, you already know where
inside it to look for a given kind of answer.
</Callout>

## "I want to..." → where to go

| If you're trying to... | Go to |
|---|---|
| Get Contextual running for the first time | `cli/tutorials/getting-started` |
| Look up an exact CLI flag or command | `cli/reference/{family}/` |
| Connect [tool] as my AI client | `integrations/reference/{client}` |
| Understand what a specific MCP tool does | `mcp/tools/reference/{tool}` |
| Understand why retrieval/graph/temporal returned what it did | `retrieval/`, `graph/`, `temporal/`, `nexus/` explanation pages |
| Fix something that's broken, and I have an error/symptom | `troubleshooting/` |
| Read logs, a `doctor` report, or trace data | `observability/` |
| Know what data leaves my machine | `trust-and-privacy/` |
| Know what a config key does | `configuration/reference/config-key-index` |
| Manage billing, login, or my account | `website/` (dashboard) or `account/` (CLI license) |

## The pattern inside every section

- **Tutorials** — learning-oriented, step-by-step, assumes nothing.
- **How-to** — task-oriented, terse, assumes you're mid-task and blocked.
- **Reference** — factual, dense, exhaustive — built to be scanned or
  copy-pasted, not read start to end.
- **Explanation** — the mental model: how something works and why it was
  built that way.
- **Troubleshooting** (its own section, not per-subsystem) — symptom
  first. Start from what you're seeing, not from which subsystem you
  suspect.

If a page opens with a `TL;DR` callout, that's the answer — the rest of
the page is depth for when you need it, not a prerequisite to reading it.
