---
title: Move your license to a new machine
domain: cli
category: how-to
tldr: On the old machine run contextual deactivate to free the device slot, then contextual login on the new one — a Solo license is single-device.
order: 7
---

<Callout variant="tldr">
Solo licenses are single-device. To move machines: `contextual
deactivate` on the old one first, then `contextual login` on the new
one.
</Callout>

**On the old machine:**

```
contextual deactivate
```

<Terminal lines={[
  {command: "contextual deactivate"},
  {output: "Deactivating will revoke your license on THIS device.\nContinue? [y/N]: y\nDevice deactivated.\nYour Solo license is now free to activate on another machine.", muted: true}
]} />

**On the new machine:**

```
contextual login
```

<Callout variant="warning">
Don't just run `contextual logout` and assume the device slot is freed
— `logout` only clears local credentials on that machine, it does not
release the license slot. `deactivate` is the step that actually frees
it.
</Callout>

## See also

- `cli/reference/deactivate`, `cli/reference/login`, `cli/reference/logout`.
