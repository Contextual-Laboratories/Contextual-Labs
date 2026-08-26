---
title: Switch or add a second AI client
domain: integrations
category: how-to
tldr: Run contextual client --<new-client> from inside the workspace — adding a client never removes another one, so switching just means adding the new one and optionally rm-ing the old.
order: 1
---

<Callout variant="tldr">
Adding a client never removes another — run `contextual client
--<new-client>` to add one alongside whatever's already connected. To
actually switch (not just add), also run `contextual client rm
--<old-client>`.
</Callout>

```
contextual client --windsurf
```

<Terminal lines={[
  {command: "contextual client --windsurf"},
  {output: "Configured Windsurf for this workspace.", muted: true}
]} />

To remove the previous client's connection instead of leaving both
active:

```
contextual client rm --claude-code
```

## See also

- `cli/reference/client/client`, `cli/reference/client/client-rm`.
- `integrations/tutorials/connecting-an-ai-client`.
