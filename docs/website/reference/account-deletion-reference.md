---
title: Account deletion reference
domain: website
category: reference
tldr: Deleting your account from /account's Danger Zone is immediate and permanent — typed DELETE confirmation, hard delete, no grace period, no undo.
order: 3
---

<Callout variant="tldr">
Account deletion is immediate and irreversible — there is no grace
period, no "restore within 30 days," no undo. Make sure this is what
you actually want before typing the confirmation.
</Callout>

## What happens

1. Go to `/account`'s Plan tab and find the Danger Zone.
2. Type `DELETE` exactly to confirm — this isn't a simple yes/no click.
3. Your account is hard-deleted immediately once the backend confirms.
4. You're signed out and redirected to the homepage.

<Callout variant="warning">
This is a genuinely permanent action, by explicit design — not a bug or
an oversight in an early version of this page. There is no support
path to reverse it after the fact.
</Callout>

## What this does not do

Deletion does not affect any repositories you've indexed locally —
`.contextual/` directories on your own machine are untouched. It also
does not automatically resolve billing: if you have an open dispute/
chargeback with your payment provider, deletion is held until that
resolves, the same way it would be for any account in that state.

## See also

- `website/reference/account-page-reference`.
- `cli/reference/general/uninstall` — for removing local state, separately.
