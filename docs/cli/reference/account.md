---
title: "account"
domain: cli
category: reference
tldr: "contextual account shows your email, tier, license status (active/trial/grace period), trial end date, and when the license was last verified."
order: 7
---

<Callout variant="tldr">
`contextual account` prints your current account status: email, tier,
whether you're on a trial or in a grace period, and how recently your
license was last verified. Read-only — it doesn't change anything.
</Callout>

## Usage

```
contextual account
```

No arguments or flags. Requires being logged in — run `contextual login`
first if not.

<Terminal lines={[
  {command: "contextual account"},
  {output: "you@example.com\nTier: Solo\nStatus: Trial · 12 day(s) remaining\nTrial ends: March 4, 2026\nLast verified: 1 hour ago", muted: true}
]} />

## Status values you might see

- **Active** — a normal, current license, outside any trial window.
- **Trial · N day(s) remaining** — inside the trial period.
- **Grace period — N day(s) remaining** — the trial or billing period
  has ended and you're in the grace window before enforcement.
- **Inactive (reason)** — the license check failed; the reason string
  is included rather than a bare "inactive."

## See also

- `cli/reference/login`, `cli/reference/deactivate`.
