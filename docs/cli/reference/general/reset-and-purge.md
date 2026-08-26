---
title: "--reset and --purge"
domain: cli
category: reference
tldr: "contextual --reset rebuilds the current workspace in place (preserving decisions and graph history by default); contextual --purge deletes it entirely and deregisters it everywhere. Both are root-level flags on contextual itself, not subcommands."
order: 31
---

<Callout variant="tldr">
`contextual --reset` and `contextual --purge` are flags on the root
`contextual` command, not subcommands — there's no `contextual reset`
or `contextual purge`. `--reset` rebuilds the current workspace from
scratch but preserves recorded decisions and graph history by default.
`--purge` is the permanent, no-recovery option: it deletes the
workspace and deregisters it from every AI client, everywhere.
</Callout>

## Usage

```
contextual --reset [--decisions]
contextual --purge
```

Both operate on the workspace in your **current directory** — there's
no path argument. Both require an already-initialized workspace
(`contextual init` must have run here first) and both prompt you to
type the workspace's directory name back as confirmation before doing
anything.

## `--reset`

<Terminal lines={[
  {command: "contextual --reset"},
  {output: "This will reset the workspace 'api-core'.\nDecisions (ADRs) and graph history are preserved.\n\nType workspace name to confirm: api-core\nResetting workspace...\nWorkspace reset complete.", muted: true}
]} />

Rebuilds `.contextual/` from a clean slate — a fresh database, fresh
config — then re-registers it under the **same workspace ID** it had
before. By default, two things survive the reset and get copied into
the new workspace:

- **Decisions** — every ADR recorded via the graph/decision tools.
- **Graph temporal history** — the bitemporal entity/triple versions
  that `graph_at_time` reads, not just current-state entities.

Pass `--decisions` to wipe those too, for a genuinely from-scratch
reset:

```
contextual --reset --decisions
```

<Callout variant="note">
This is deliberately different from a plain `contextual index --force`,
which also never wipes decisions or graph temporal history — `--reset`
without `--decisions` and `index --force` preserve the exact same
things, for the exact same reason. `--reset --decisions` is the one
path that actually clears them.
</Callout>

### How the reset stays safe

Nothing is destroyed until the rebuild is proven to work. The old
`.contextual/` is renamed aside (a single atomic same-filesystem
rename, not deleted) *before* the new workspace is built. Only once the
new workspace — and, if decisions/history were being preserved, the
copied-over tables — are verified present does the renamed-aside old
directory actually get deleted. If anything fails at any point, the old
directory is renamed straight back into place and nothing is lost. This
means `--reset` always ends in exactly one of two states: "old data
gone, new workspace live" or "old data restored, nothing changed" —
never a half-migrated state in between.

## `--purge`

<Callout variant="warning">
`--purge` is permanent and much more thorough than `--reset`. It
deletes the workspace outright, deregisters it from the multi-workspace
registry, cleans this workspace's entry out of **every** locally
configured AI client (Claude Desktop, Cursor, and everything else you'd
normally remove one at a time with `contextual client rm`), and revokes
every access token issued for it. There is no `--decisions` equivalent
— nothing about this workspace survives.
</Callout>

<Terminal lines={[
  {command: "contextual --purge"},
  {output: "This will PERMANENTLY delete the workspace 'api-core' and\nremove it from every configured AI client.\n\nType workspace name to confirm: api-core\nDeleting workspace...\nWorkspace purged. Cleaned: Claude Code, Cursor.", muted: true}
]} />

What it does, in order:

1. Deletes `.contextual/` for this workspace.
2. Deregisters the workspace from the global registry (the same one
   `contextual workspace list` reads).
3. Deregisters it from the multi-workspace name registry
   (`workspaces.json`) — without this step a purged workspace would
   keep showing up in `workspace list` forever, with a blank ID and
   "never indexed."
4. Best-effort cleans this workspace's entry out of every AI client
   configured on this machine and revokes its scoped access tokens —
   the same logic `contextual client rm --<name>` uses, just run once
   per client automatically instead of by hand.

<Callout variant="note">
`--purge` deliberately does **not** stop the background MCP daemon,
even though it's a single shared process serving every registered
workspace. Restarting it would interrupt every *other* workspace's live
session for the ~20-50s model-warmup a respawn takes — pure collateral
damage for workspaces that have nothing to do with this purge. Once
`.contextual/` is gone, any daemon request still cached against this
workspace fails with a clear error instead of silently serving stale
data, and the daemon evicts it from memory on its own idle-eviction
sweep. If step 1's delete ever fails because the daemon still has files
open under `.contextual/`, the error message points at `contextual mcp
restart` as the fallback — not needed in the normal case.
</Callout>

## See also

- `cli/reference/general/init` — the command that creates what these
  two flags act on.
- `cli/how-to/force-a-full-re-index-after-a-large-refactor` — the
  much lighter-weight `index --force`, for when a reset is overkill.
- `cli/reference/client/client-rm` — remove one client at a time
  instead of every client at once.
