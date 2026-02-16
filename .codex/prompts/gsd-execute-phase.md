---
description: Execute all plans in a phase with wave-based parallelization
argument-hint: "<phase-number> [--gaps-only]"
---

## Objective
Execute all plans in a phase with wave-based parallelization

## Compatibility
- Use .codex/skills/get-shit-done-codex semantics.
- Treat upstream workflow as the source of truth.
- Replace user-specific paths with workspace-relative paths (.claude/..., .planning/...).
- Use one of these engine paths (prefer local, fallback global):
  node .claude/get-shit-done/bin/gsd-tools.js ...
  node ~/.claude/get-shit-done/bin/gsd-tools.js ...
- If `.js` is unavailable, use the same path with `.cjs`.
- Run engine commands through PowerShell.
- Do not set `node <path> ...` as one string variable and invoke `& $var`; run direct `node <path> ...` or `& node <path> ...`.
- Parse JSON with ConvertFrom-Json; parse key/value output when workflow uses KEY=value raw mode.
- No jq / bash-only constructs.

## Subagent lifecycle (required)

- Translate each upstream `Task(...)` into `spawn_agent` -> `wait` -> `close_agent`.
- Spawn only when the upstream workflow defines an agent role.
- Use `.claude/agents/gsd-*.md` as role context for each spawned agent.
- Do not advance workflow steps until wait and close complete.

## Input contract
- The command argument is authoritative.
- If user runs `gsd-execute-phase 28`, execute phase `28`. Do not auto-discover a different phase.
- Parse flags only from the same argument string (`--gaps-only`, `--auto`).
- If phase argument is missing or invalid, stop and ask for a valid phase number.

## Execution
1. Parse "<phase-number> [--gaps-only] [--auto]" from the command argument string.
2. Treat parsed phase as the execution target and keep it unchanged for all downstream steps.
3. Run init with the parsed phase:
node <gsd-tools-path> init execute-phase <phase> --raw
4. If `--gaps-only` is present, apply gap-closure filtering exactly as defined in the upstream workflow.
5. If `--auto` is present, preserve auto-advance behavior from the upstream workflow.
6. Load .claude/get-shit-done/workflows/execute-phase.md and execute it step-by-step.
7. Translate each Task(...) in workflow into:
   - spawn_agent with the matching role file context from .claude/agents/.
   - wait for each spawned agent and apply returned output before moving forward.
8. Preserve all gates and routing from upstream workflow.
9. Preserve commit behavior using
node <gsd-tools-path> commit "message" --files ....
10. If commit preflight fails (no git / no commit flag), proceed in read-only mode and report clearly.

## Completion output
- Summarize key artifacts created/updated.
- Next recommended command: `gsd-verify-work <phase>` (Codex) / `/gsd:verify-work <phase>` (Claude)
- Never recommend internal `node ... gsd-tools ...` commands to the user.
