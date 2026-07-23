---
title: "deactivate"
domain: cli
category: reference
tldr: "contextual deactivate frees this device's license slot (after a confirmation prompt) so you can activate on a different machine — Solo is a 1-device license."
order: 6
---

<Callout variant="tldr">
`contextual deactivate` revokes your license on this specific device,
after you confirm, so it can be activated on another one. Solo licenses
are single-device — this is the correct way to move machines.
</Callout>

## Usage

```
contextual deactivate
```

No arguments or flags — you'll be asked to confirm before anything
happens, since this immediately revokes the license on the device you
run it from.

<Terminal lines={[
  {command: "contextual deactivate"},
  {output: "Deactivating will revoke your license on THIS device.\nYou will need to run  contextual login  on the new device to re-activate.\n\nContinue? [y/N]: y\nDevice deactivated.\nYour Solo license is now free to activate on another machine.", muted: true}
]} />

## See also

- `cli/how-to/move-your-license-to-a-new-machine` — the full workflow.
- `cli/reference/login`.
