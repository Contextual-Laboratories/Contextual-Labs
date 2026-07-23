---
title: "login"
domain: cli
category: reference
tldr: "contextual login authenticates via OAuth in your browser (or --device-code for headless/SSH machines) and registers this device against your Solo license."
order: 4
---

<Callout variant="tldr">
`contextual login` opens your browser for OAuth authentication and
registers this device. Use `--device-code` on a headless or SSH-only
machine where a browser can't open locally.
</Callout>

## Usage

```
contextual login [--device-code]
```

- `--device-code` — use the device-code flow instead of a local browser
  redirect. Shows a code to enter at a URL from any browser, including
  one on a different machine.

<Terminal lines={[
  {command: "contextual login"},
  {output: "Opening browser for authentication...\nLogged in as you@example.com (Solo, trial ends March 4, 2026).", muted: true}
]} />

If you're already logged in, `login` tells you so and exits rather than
silently re-authenticating — run `contextual logout` first if you
actually want to switch accounts.

## See also

- `cli/reference/logout`, `cli/reference/deactivate`, `cli/reference/account`.
- `engine/reference/platform-support` for the Windows-specific loopback
  notes on this flow.
