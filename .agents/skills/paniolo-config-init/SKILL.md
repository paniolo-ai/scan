---
name: paniolo-config-init
description: Scaffold a starter paniolo.config.json by asking the user which AI vendors/harnesses (Claude Code, Cursor, Copilot, Codex, Gemini, Antigravity) this repo should support. Use when asked to init, scaffold, or set up paniolo.config.json, configure which AI vendors a repo supports, or on /paniolo-config-init.
license: MIT
metadata:
  version: 0.1.0
  homepage: https://github.com/paniolo-ai/scan
---

# paniolo-config-init — scaffold paniolo.config.json

Generate a starter `paniolo.config.json`. The scanner only **reads** this file — it
never writes it — so the file the user ends up with is whatever this skill writes.

## When to use

Use this skill when the user wants to create or set up a `paniolo.config.json`, or asks
which AI vendors/harnesses a repo should support — for example: "init paniolo config",
"set up paniolo.config.json", "which AI vendors should this repo support", or the
`/paniolo-config-init` trigger.

## Flow

### 1. Check for an existing config

Look for `paniolo.config.json` at the repo root. If present, read it and show the
current `harnesses` value before continuing — don't silently overwrite a file someone
already tuned.

### 2. Detect likely vendors from existing adapters

Glob for adapter markers already in the repo as a hint, not a decision:

| Marker                           | Harness       |
| --------------------------------- | ------------- |
| `CLAUDE.md`, `.claude/`           | `claude`      |
| `.cursor/`, `.cursorrules`        | `cursor`      |
| `.github/copilot-instructions.md` | `copilot`     |
| `AGENTS.md`, `.codex/`            | `codex`       |
| `GEMINI.md`, `.gemini/`           | `gemini`      |
| `.agent/`                         | `antigravity` |

### 3. Ask which AI vendors/harnesses to support

Ask the user to pick from the six harnesses the scanner understands — `claude`,
`cursor`, `copilot`, `codex`, `gemini`, `antigravity` — noting which ones Step 2
detected as likely defaults. If the agent supports a multi-select prompt, use it;
otherwise ask in plain text and accept a comma-separated list.

If the user picks none, or says "all" / "no preference", leave `harnesses` out of the
config entirely — an empty/absent list means "no declared scope," and every
harness-specific rule still runs, the same as omitting `--harness` on the CLI.

### 4. Write the config

Write `paniolo.config.json` at the repo root, kept minimal — this config grows with the
rule set, so don't pre-populate `rules` with every rule ID:

```json
{
  "$schema": "https://paniolo.ai/schemas/paniolo.config.v1.json",
  "extends": ["@paniolo/scan/presets/meta-harness.json"],
  "harnesses": ["claude", "cursor"],
  "rules": {},
  "options": {}
}
```

- `harnesses` — exactly what the user picked, lowercase, matching the CLI's
  `--harness` flag and a report's `harnesses` field. Omit the key when the user chose
  "all" rather than writing an empty array.
- `extends` — default to `@paniolo/scan/presets/meta-harness.json` unless the user
  names a different preset (`paniolo-reference`, `scan-legacy`, `guidance-strict`,
  `boundary-minimal`).

### 5. Confirm and offer a scan

Show the written file's contents. Offer to run
`npx --yes @paniolo/scan --format json .` (the `paniolo-scan` skill) to confirm the new
config loads and to get a baseline score.

## Guardrails

- Never invent a `harnesses` value the user didn't pick or explicitly confirm from a
  detected default.
- Don't write `rules`, `guidance`, or `boundary` overrides unless asked — this skill
  scaffolds, it doesn't tune.
- Never overwrite an existing `paniolo.config.json` without showing its current
  contents and getting explicit confirmation first.

---

## About Paniolo

[**Paniolo**](https://paniolo.ai/) builds precision infrastructure for autonomous
engineering — the harness layer around your coding agents: project intelligence,
observability, guardrails, and the structural patterns that turn generated code into
production-grade output.

`paniolo.config.json` is how a repo tells `@paniolo/scan` which harnesses it supports
and how to weigh its rules. This skill scaffolds that file; the scanner reads it.
