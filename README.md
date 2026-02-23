# Get Shit Done (GSD) - Codex Fork

This repository packages the upstream **get-shit-done (GSD)** workflow for **OpenAI Codex**.
It installs Codex prompt commands plus the supporting GSD reference files used by the workflows and agents.

This fork is **Codex-first**, but keeps **Claude-compatible** assets alongside for workflow parity.

## Requirements

- Node.js >= 16.7 (for `npx`)
- OpenAI Codex (to run `/prompts:...` commands)

## Install

### Local install (recommended)

Run from your project root:

```sh
npx gsd-codex-cli@latest --path .
```

This installs into your project:
- `./.codex/` (prompts, skills, agent configs)
- `./.claude/` (GSD workflow engine + Claude agent definitions for compatibility)

It also registers GSD agent roles by appending missing `[agents.gsd-*]` blocks to:
- local: `./.codex/config.toml`

### Global install (optional)

Makes the prompts available across projects by installing to your home directory:

```sh
npx gsd-codex-cli@latest --global
```

Home directory locations:
- macOS/Linux: `~/.codex/` and `~/.claude/`
- Windows: `%USERPROFILE%\\.codex\\` and `%USERPROFILE%\\.claude\\`

It also appends missing `[agents.gsd-*]` blocks to:
- global: `~/.codex/config.toml`

### Verify

In Codex:
- Run `/prompts:gsd-help` to see available commands
- If anything looks off, run `/prompts:gsd-doctor` (read-only diagnostics)

## Quick start

Recommended flow in Codex:

1. `/prompts:gsd-new-project` (initializes `.planning/`)
2. `/prompts:gsd-discuss-phase` (scope the next phase)
3. `/prompts:gsd-plan-phase`
4. `/prompts:gsd-execute-phase`
5. `/prompts:gsd-verify-work`

GSD stores project state in `./.planning/` so the workflow is compatible across tools.

## What gets installed

The installer copies these directories into the target location:

- `./.codex/prompts/` - Codex prompt commands (`/prompts:gsd-*`)
- `./.codex/skills/` - supporting skill files
- `./.codex/agents/` - per-role agent config files
- `./.claude/get-shit-done/` - workflow engine + references (compat)
- `./.claude/agents/` - Claude agent definitions (compat)

Notes:
- Re-running the installer overwrites the installed files with the latest versions.
- Role registration in `config.toml` is append-only for missing sections.

## Update / Upgrade

Re-run the installer (overwrites installed prompt/skill/agent files):

```sh
npx gsd-codex-cli@latest --path .
# add --global if you also want the home install updated
```

Or, inside Codex:
- `/prompts:gsd-update` (best-effort version check + guided update)

## Uninstall

If you use `.codex/` for other tools, remove only the GSD-related files.

Local (project) uninstall:
- delete `./.claude/get-shit-done/`
- delete `./.codex/skills/get-shit-done-codex/`
- delete `./.codex/prompts/gsd-*.md`
- delete `./.codex/agents/gsd-*.toml`
- delete `./.codex/gsd/`

Global uninstall:
- remove the same paths under your home directory (`~/.codex`, `~/.claude`)

Optional cleanup:
- remove `[agents.gsd-*]` blocks from the relevant `config.toml` if you no longer want GSD role registrations.

## Troubleshooting

### Prompts not showing / "unknown command"

If `/prompts:gsd-help` does not autocomplete or is not recognized:

1. Confirm the prompt files exist:
   - local: `./.codex/prompts/gsd-help.md`
   - global: `~/.codex/prompts/gsd-help.md`
2. Restart Codex / reopen the project (some clients cache prompt lists).
3. Run `/prompts:gsd-doctor` to diagnose versions, engine presence, role registration, and `.planning/`.
4. If you only installed with `--global`, try a local install in the project:
   ```sh
   npx gsd-codex-cli@latest --path .
   ```

### `npx` not found

Install Node.js (includes `npm`/`npx`), then retry the install command.

### Install overwrote something

The installer copies directories with overwrite semantics.
If you maintain local customizations in `.codex/` or `.claude/`, keep them outside the `gsd-*` files/directories (or commit/back up before updating).

## CLI options

```text
--path <target-dir>   Install into this directory (defaults to current directory)
--global              Also install to your home directory
--help, -h            Show help
```

## Development

```sh
git clone https://github.com/redstar1337/get-shit-done-codex.git
cd get-shit-done-codex
npm install

npm test
npm run build:hooks

node bin/install-codex.js --path .
```

Note: `npm test` uses `node --test` (Node 18+ recommended for contributors).

## Notes

- This package is published as `gsd-codex-cli`. Use `npx gsd-codex-cli@latest` for installs.
- The npm binary names `get-shit-done-codex` and `get-shit-done-cc` are still provided for compatibility.

## Upstream

GSD originated in the upstream repository by TACHES. This fork keeps the core workflow and adds Codex-native prompts and packaging.

Upstream: https://github.com/gsd-build/get-shit-done

## License

MIT. See `LICENSE`.
