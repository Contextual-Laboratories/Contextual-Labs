# Contextual

[![Docs Sync](https://github.com/Contextual-Laboratory/Contextual-Labs/actions/workflows/dispatch-to-web.yml/badge.svg)](https://github.com/Contextual-Laboratory/Contextual-Labs/actions/workflows/dispatch-to-web.yml)
[![License](https://img.shields.io/badge/license-All%20Rights%20Reserved-blue.svg)](LICENSE)
[![Latest Release](https://img.shields.io/github/v/release/Contextual-Laboratory/Contextual-Labs?include_prereleases)](https://github.com/Contextual-Laboratory/Contextual-Labs/releases)

Temporal, semantic code intelligence for your codebase — a local-first CLI
and MCP server that gives coding agents grounded, timestamped, searchable
context instead of guesses.

This repository is the public home of Contextual: documentation, and the
place to report bugs or request features. It does not contain the engine's
source code.

- **Docs:** [contextuallabs.dev/docs](https://contextuallabs.dev/docs) *(or see [`docs/`](docs/) in this repo)*
- **Install:** `pip install contextual-engine` — see [Getting Started](docs/cli/tutorials/)
- **Found a bug or a docs error?** [Open an issue](../../issues/new/choose)
- **Security vulnerability?** See [`SECURITY.md`](SECURITY.md) — please don't file a public issue

## What is Contextual?

Contextual indexes a codebase locally and exposes it through an MCP server
and CLI, giving AI coding agents structural call resolution, class-hierarchy
awareness, and temporal git-history-aware retrieval — grounded answers about
what a codebase actually does, not what a model assumes it does.

## Documentation

Docs are organized by product surface, each following the
[Diátaxis](https://diataxis.fr/) framework (tutorials, how-to guides,
reference, explanation):

- [`docs/cli/`](docs/cli/) — the Python CLI
- [`docs/mcp-server/`](docs/mcp-server/) — the MCP server and its tools
- [`docs/web-dashboard/`](docs/web-dashboard/) — the web dashboard
- [`docs/concepts/`](docs/concepts/) — cross-cutting architecture/concepts
- [`docs/account/`](docs/account/) — licensing, trial, device management
- [`docs/changelog/`](docs/changelog/) — release notes

This repository is a published mirror, not the authoring source — technical
docs are written once upstream (in `Contextual/docs/Public/`) and synced
here automatically on every merge. See each folder's `README.md` for what
belongs where.

## Support

- **Docs feedback, product bugs, feature requests:** [GitHub Issues](../../issues) —
  pick the template that matches.
- **Everything else:** team@contextuallabs.dev

## License

All rights reserved — this repository's content is publicly viewable, not
publicly licensed. No commercial use, copying, or redistribution is
permitted. See [`LICENSE`](LICENSE). The Contextual engine (CLI/MCP
server) is licensed separately, under BUSL 1.1 — see the `Contextual`
repository.
