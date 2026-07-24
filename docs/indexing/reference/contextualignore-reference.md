---
title: .contextualignore Reference
domain: indexing
category: reference
tldr: .contextualignore uses exact .gitignore (gitwildmatch) syntax and is additive with .gitignore — a path excluded by either file is excluded from indexing, and .gitignore is honored specifically so secrets like .env files never get indexed even without a .contextualignore entry.
order: 5
related:
  - indexing/how-to/exclude-files-with-contextualignore.md
  - trust-and-privacy/reference/data-privacy.md
---

<Callout variant="tldr">
Exact `.gitignore` syntax, via the same `pathspec` library `.gitignore`
parsers use. `.contextualignore` and `.gitignore` are both checked, and
either one matching excludes a file — there's no precedence override
between them, just two independent deny-lists.
</Callout>

## Syntax

Standard gitignore-style patterns (`gitwildmatch` matching):

- One pattern per line; `#` starts a comment; blank lines are skipped.
- A trailing `/` matches directories only.
- `*` and `**` work as in `.gitignore`.
- `!pattern` negates a preceding match, same as `.gitignore`'s own
  in-file negation.

## Precedence: additive, not overriding

A file is excluded from indexing if it matches a pattern in
**either** `.contextualignore` or `.gitignore` — `.contextualignore` is
checked first, then `.gitignore`, but both are real deny-lists and
neither overrides the other. There's no mechanism to "un-ignore" in
`.contextualignore` something `.gitignore` excludes, or vice versa,
beyond each file's own in-file negation patterns.

<Callout variant="note">
`.gitignore` is deliberately honored, not just `.contextualignore` —
specifically so gitignored artifacts and secrets (`.env` files, build
output, vendored dependencies) never end up in the searchable index just
because you didn't also think to add them to `.contextualignore`. If a
path is safe to keep out of git, it's safe to keep out of the index by
the same rule, with zero extra configuration.
</Callout>

## Default patterns

Contextual ships default exclusions covering common noise: dependency
directories (`node_modules/`, `.venv/`, `venv/`, `vendor/`, `.bundle/`),
build output (`dist/`, `build/`, `target/`, `out/`, `bin/`, `obj/`,
`*.egg-info/`), minified/generated files (`*.min.js`, `*.min.css`,
`*_pb2.py`, `*.g.dart`, `*.freezed.dart`, `*.g.go`, `*.d.ts`), database
files (`*.db`, `*.sqlite`, `*.sqlite3`), lockfiles (`package-lock.json`,
`yarn.lock`, `pnpm-lock.yaml`, `poetry.lock`, `Cargo.lock`), large media
and binary files (images, video, audio, archives, compiled binaries), and
common IDE/log directories. You don't need to redeclare any of these
yourself — write `.contextualignore` for exclusions beyond this default
set.

## What's excluded regardless of any ignore file

Symlinks are never followed during file discovery, full stop — this
isn't configurable via `.contextualignore` because it's a security
boundary, not a noise-reduction filter: a symlink inside a repository
could point outside the project root (`~/.ssh/id_rsa`, `/etc/`), and
following it would read and potentially index file content that was
never meant to be part of this codebase. See
`trust-and-privacy/reference/data-privacy` for the fuller picture of what does and
doesn't get read during indexing.

## Location and scope

One `.contextualignore` file at your repository root, applied to that
workspace only — there's no global or user-wide ignore file layered on
top.
