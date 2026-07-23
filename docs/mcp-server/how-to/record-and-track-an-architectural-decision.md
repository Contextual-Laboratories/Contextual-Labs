---
title: Record and track an architectural decision
domain: mcp-server
category: how-to
tldr: decision_create writes a proposed ADR, decision_accept ratifies it, decision_supersede links a replacement to an old one — check decision_list first to avoid duplicates.
order: 3
---

<Callout variant="tldr">
The decision lifecycle is create → accept, with `decision_supersede`
available later if a decision gets replaced. Check `decision_list`
before creating a new one, in case it already exists.
</Callout>

```
decision_list(status="proposed")
decision_create(title="Use LanceDB for all storage", context="...", decision="...", consequences="...")
decision_accept(adr_id="adr_...")
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

## See also

- `mcp-server/reference/decision_create`, `mcp-server/reference/decision_accept`,
  `mcp-server/reference/decision_supersede`, `mcp-server/reference/decision_list`.
