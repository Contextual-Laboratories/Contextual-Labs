# CLI

All 30 commands, grouped by family, plus 3 root-level flags
(`--version`, `--reset`, `--purge`) that live on `contextual` itself
rather than any subcommand.

| Family | Commands | What it's for |
|---|---|---|
| **general** | `init`, `index`, `activity`, `login`, `logout`, `account`, `doctor`, `fetch`, `setup`, `uninstall`, `hooks-install`, `hooks-uninstall`, `stats`, `version` | Core lifecycle: create a workspace, index it, authenticate, check health. Start here. |
| **client** | `client`, `client rm`, `client list`, `client config` | Wire (or remove) the MCP connection between one repository and one AI client. |
| **skill** | `skill`, `skill rm` | Install (or remove) the device-wide Agent Skill file each AI client reads for tool-usage guidance — separate from `client`, which wires the connection itself. |
| **config** | `config`, `config reset` | Open or reset the workspace/global `config.toml`. |
| **mcp** | `mcp status`, `mcp start`, `mcp stop`, `mcp restart`, `mcp logs` | Manage the background MCP daemon process directly. |
| **workspace** | `workspace list`, `workspace add`, `workspace rm` | Register and manage more than one repository in the multi-workspace registry. |

Root-level flags (`contextual --version` / `--reset` / `--purge`) are
not under any family above because they attach to `contextual` itself,
not to a subcommand — `--reset` rebuilds the current workspace in place
(preserving decisions/graph history by default), `--purge` deletes it
and deregisters it everywhere. See
`cli/reference/general/reset-and-purge` for both in full.
