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
contextual login [--device-code] [--license-key KEY]
```

- `--device-code` — use the device-code flow instead of a local browser
  redirect. Shows a code to enter at a URL from any browser, including
  one on a different machine. `login` also switches to this flow
  automatically, with no flag needed, when it detects a headless
  environment (an SSH session, Linux with no `DISPLAY`/`WAYLAND_DISPLAY`,
  or non-interactive stdout).
- `--license-key KEY` — skip the account/session layer entirely and
  activate directly with a raw Cryptolens key (falls back to the
  `CONTEXTUAL_CI_LICENSE_KEY` environment variable if the flag is
  omitted). For headless/CI use only — it writes just the local license
  activation file, no session or email is stored, and none of the
  browser flow below runs at all.

<Terminal lines={[
  {command: "contextual login"},
  {output: "Opening browser for authentication...\nLogged in as you@example.com (Solo, trial ends March 4, 2026).", muted: true}
]} />

If you're already logged in, `login` tells you so and exits rather than
silently re-authenticating — run `contextual logout` first if you
actually want to switch accounts. (If your session is still valid but
the local license activation somehow fell out of sync with it, `login`
repairs that silently instead of erroring — see "Repairing a split
state" below.)

## What actually happens in the browser

`login` (without `--device-code` or `--license-key`) is OAuth 2.0
Authorization Code + PKCE (RFC 7636), a "public client" flow — no
client secret is used or ever could be, and the CLI never sees your
password:

1. The CLI generates a PKCE verifier/challenge and a CSRF state token,
   then binds a one-off HTTP listener to `127.0.0.1` on an
   OS-assigned port.
2. It opens your default browser at Contextual's `/oauth/authorize`
   endpoint. **There is no Contextual-branded page here** — if you're
   not already signed in, this is an immediate redirect straight to
   Clerk's hosted sign-in UI (`accounts.contextuallabs.dev`). Nothing
   of Contextual's own renders before that.
3. After you sign in, the browser is redirected straight to the CLI's
   local loopback listener with an authorization code — the very last
   thing you see in the tab is a plain, unstyled "Signed in to
   Contextual. You can close this tab and return to the terminal."
   page. That page is rendered by the CLI process itself, on your own
   machine, not fetched from contextuallabs.dev.
4. The CLI exchanges that code (plus the original PKCE verifier) for
   your session — this exchange is a direct CLI→server HTTP call your
   browser is never involved in.

<Callout variant="note">
If Clerk considers your browser session not "fresh" enough for this
action (its own reverification policy, not something Contextual
configures per-request), `/oauth/authorize` detours through a
Contextual-styled step-up page at `/device/authorize` that silently
runs Clerk's reverification modal and bounces straight back — this is
not a separate flow you need to do anything different for, just a
possible extra hop you might notice.
</Callout>

### `--device-code`

For a machine with no local browser at all (SSH, containers, CI
without `--license-key`), the CLI instead requests an RFC 8628 Device
Authorization Grant and prints a URL + short code to enter on *any*
other device's browser:

<Terminal lines={[
  {command: "contextual login --device-code"},
  {output: "To sign in, visit:\n\n    https://contextuallabs.dev/device\n\nAnd enter this code if prompted:  WXYZ-1234", muted: true}
]} />

The `/device` page you land on is a real, Contextual-branded page (not
Clerk-hosted) with the code prefilled and editable. Once you're signed
in, it shows a phishing-mitigation confirmation — "Confirming a CLI
sign-in requested from `<IP address>`, `<relative time>`. If that isn't
you, don't confirm — close this tab instead." — gated behind a
Turnstile challenge, and finishes with "Device linked. You can go back
to your terminal — the CLI login will complete automatically."

<Callout variant="note">
That confirmation page deliberately does **not** show which account or
tier is about to be linked before you confirm — only the request's
origin IP and how long ago it was made, as the phishing/mismatch
signal. Meanwhile the CLI is polling the token endpoint in the
background every few seconds until you confirm or the code expires.
</Callout>

### Repairing a split state

If your account session is valid but the local `.skm` license
activation somehow isn't (e.g. it was cleared by hand, or a previous
run was interrupted), running `login` again — with no flags — detects
this and re-activates the device using the existing session, without
sending you through the browser again. You'll see "License repaired."
instead of the normal login success message.

### If sign-up succeeded but there's no active subscription

A brand-new account that hasn't started a paid plan yet gets a `403
no_active_license` response at the token-exchange step, in both the
browser and device-code flows — this happens entirely between the CLI
and the server; nothing about it is shown in the browser tab itself.
`login` surfaces it as a plain error pointing at
`contextuallabs.dev/pricing`.

## See also

- `cli/reference/general/logout`, `cli/reference/general/account`.
- `indexing/reference/platform-support` for the Windows-specific loopback
  notes on this flow.
