---
title: Record and track an architectural decision
domain: mcp-tools
category: how-to
tldr: decision_create writes a proposed ADR, decision_update(action="accept") ratifies it, decision_supersede links a replacement to an old one, decision_update(action="deprecate"/"reinstate") retires or brings back a decision — check decision_list first to avoid duplicates.
order: 3
---

<Callout variant="tldr">
The decision lifecycle is create → accept, with `decision_supersede`
available later if a decision gets replaced, or `decision_update` with
`action="deprecate"` to retire it with nothing replacing it (and
`action="reinstate"` to undo that). Check `decision_list` before
creating a new one, in case it already exists.
</Callout>

```
decision_list(status="proposed")
decision_create(title="Use LanceDB for all storage", context="...", decision="...", consequences="...")
decision_update(adr_id="adr_...", action="accept")
```

<Terminal lines={[
  {command: "decision_create(title=\"Use LanceDB for all storage\", context=\"...\", decision=\"...\", consequences=\"...\")"},
  {output: "{\n  \"adr_id\": \"adr_7f3a...\",\n  \"status\": \"proposed\"\n}", muted: true}
]} />

If a decision is later replaced, don't just create a new unrelated
record — link them:

```
decision_supersede(old_adr_id="adr_old...", new_adr_id="adr_new...", reason="...")
```

If a decision is retired outright with nothing replacing it (e.g. the
feature it was about got removed), deprecate it instead — it drops out
of `decision_search` but still shows up in `decision_list`:

```
decision_update(adr_id="adr_...", action="deprecate")
```

## See also

- `mcp/tools/reference/decision_create`, `mcp/tools/reference/decision_update`,
  `mcp/tools/reference/decision_supersede`, `mcp/tools/reference/decision_list`.
