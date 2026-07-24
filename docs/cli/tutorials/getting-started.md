---
title: Getting Started
domain: cli
category: tutorial
tldr: Install Contextual, initialize your repository, run your first index, then ask a grounded question through an AI client in under ten minutes.
order: 1
---

<Callout variant="tldr">
Install Contextual, run `contextual init` and `contextual index` inside your
repository, connect one AI client, then ask it a question about your own
code. That's the entire loop — everything else in these docs is depth on
top of it.
</Callout>

This tutorial takes you from a fresh install to a grounded, cited answer
from your own AI client about your own codebase. It assumes a repository
you already have checked out locally and one of the supported AI clients
installed (Claude Desktop, Claude Code, Cursor, Windsurf, and others —
see `client add` in the how-to section for the full list).

## 1. Install

```
uv tool install contextual-engine
```

<Terminal lines={[
  {command: "uv tool install contextual-engine"},
  {output: "Resolved 42 packages in 1.1s\nInstalled 1 executable: contextual", muted: true}
]} />

If you don't use `uv`, `pipx` works the same way:

```
pipx install contextual-engine
```

Either way, you get one command on your `PATH`: `contextual`.

<Callout variant="note">
The package on PyPI is named `contextual-engine`; the command you actually
type is `contextual`. If a script or doc ever shows `contextual-engine
index` instead of `contextual index`, that's wrong — the entry point is
just `contextual`.
</Callout>

## 2. Initialize your repository

From the root of the repository you want Contextual to understand:

```
contextual init
```

<Terminal lines={[
  {command: "contextual init"},
  {output: "Creating .contextual/ workspace...\nWorkspace ready.", muted: true}
]} />

This creates a `.contextual/` directory in your repository — the on-disk
home for this workspace's database, config, and logs. It does not touch
any of your own source files, and it adds the right entries to your
`.gitignore` automatically so the workspace itself never gets committed.

<Callout variant="warning">
Don't `rm -rf .contextual/` to "start over." If something about the
workspace looks broken, use `contextual doctor` to see what's actually
wrong first — most problems are a stale index or a config mismatch, not
something a full wipe is the right fix for. See the Troubleshooting
section in `engine/` for what each `doctor` line actually means.
</Callout>

## 3. Run your first index

```
contextual index
```

This is the step that actually reads your code: walking git history for
blame/temporal data, extracting the dependency graph, then chunking
files and computing embeddings. On a first run over a real repository
this can take a while — it's proportional to repository size, and it
runs entirely on CPU (no GPU dependency, nothing leaves your machine).

<Callout variant="note">
Why this takes as long as it does: indexing is several sequential stages
(blame history, then graph extraction, then chunking and embedding — in
that order, not chunk-then-embed-first as you might expect), not one
fast pass, and the embedding step runs a local model with no network
calls. A large monorepo's first index is the slowest thing you'll do
with Contextual — every run after that can be incremental (`contextual
index --incremental`, covered in the how-to section) and only touches
what git says changed.
</Callout>

## 4. Connect an AI client

```
contextual client --claude-code
```

<Terminal lines={[
  {command: "contextual client --claude-code"},
  {output: "Configured Claude Code for this workspace.", muted: true}
]} />

Swap `--claude-code` for whichever client you actually use — `--cursor`,
`--windsurf`, `--claude` (Claude Desktop), `--copilot`, `--gemini-cli`,
`--codex`, `--chatgpt`, `--perplexity`, `--antigravity`, or `--cline` are
all real, supported flags. Run `contextual client --help` to see the
current list from your installed version, since this list can grow.

## 5. Ask a grounded question

Open your AI client and ask it something specific about your own
repository — not "what does this codebase do" in the abstract, but a real
question a teammate might ask you: "what calls this function," "what
would break if I renamed this," "why does this file keep changing
together with that one." Your client now has Contextual's MCP tools
available and will reach for them automatically when a question needs
real, current information about your code rather than a guess.

That's the full loop. From here:
- `cli/how-to/` covers specific tasks (adding a second client, forcing a
  full re-index, running the daemon in the background).
- `mcp/tools/tutorials/` covers the same first-question moment from the
  tool side, in more depth.
- `indexing/explanation/`, `retrieval/explanation/`, and
  `graph/explanation/` cover how indexing, retrieval, and the knowledge
  graph actually work under the hood.
