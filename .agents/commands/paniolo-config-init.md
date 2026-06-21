---
description: "Scaffold paniolo.config.json by asking which AI vendors/harnesses this repo supports. Use when the user says /paniolo-config-init, 'init paniolo config', 'set up paniolo.config.json', or 'which AI vendors should this repo support'."
allowed-tools: Bash(*), Read(*), Write(*), AskUserQuestion(*)
---

# /paniolo-config-init — scaffold paniolo.config.json

Generate a starter `paniolo.config.json` for this repo. The scanner only **reads** this
file — it never writes it — so the file the user ends up with is whatever you write here.

## Flow

### Step 1: Check for an existing config

Look for `paniolo.config.json` at the repo root. If it already exists, read it and show
the current `harnesses` value (if any) before continuing — don't silently overwrite a
file the user or a teammate already tuned.

### Step 2: Run deterministic discovery

Don't Glob for adapter markers or skill directories yourself — call the scanner's
deterministic discovery mode so detection matches what `paniolo-scan` actually uses for
scoring:

```bash
npx --yes @paniolo/scan --print-discovery .
```

This skips the full rule scan and prints JSON only, shaped like:

```json
{
  "harnesses": ["claude", "cursor"],
  "skillsDirs": {
    "patterns": ["..."],
    "resolved": [{ "displayPath": "...", "absolutePath": "..." }]
  }
}
```

Read the command's own stdout — there's no need to redirect to a file.

- `harnesses` — a hint for Step 3, not a decision. Always confirm with the user rather than
  silently locking in what's detected.
- `skillsDirs.resolved` — directories discovery actually found on disk (already accounting
  for any `chat.agentSkillsLocations` wiring or built-in defaults). Carry this into Step 4:
  - **Non-empty**: the scanner will find these same directories at scan time with no config
    needed. Don't write `options.inventory.skillsDirs` unless the user wants to lock the set
    in (e.g. a monorepo with several `packages/*/skills` roots they want recorded explicitly
    rather than re-discovered every run).
  - **Empty**: discovery found no skills directory at all — built-in defaults
    (`.agents/skills`, `skills`, etc.) and any workspace wiring all came up empty. Ask the
    user where skill/guidance markdown lives in this repo, and write that path into
    `options.inventory.skillsDirs` in Step 4 so the scanner has somewhere to look.

### Step 3: Ask which AI vendors/harnesses to support

Use `AskUserQuestion` (multi-select) with the six harnesses the scanner understands,
pre-noting which ones Step 2 detected:

- Claude Code (`claude`)
- Cursor (`cursor`)
- GitHub Copilot (`copilot`)
- Codex (`codex`)
- Gemini CLI / Antigravity (`gemini`)
- Antigravity workflows (`antigravity`)

If the user picks none (or explicitly says "all" / "no preference"), leave `harnesses`
empty — an empty list means "no declared scope," and every harness-specific rule still
runs, same as omitting `--harness` on the CLI.

### Step 4: Resolve the skills directory, if needed

Only act here when Step 2's `skillsDirs.resolved` was **empty** — the common case (a
non-empty `resolved`) needs no config and nothing to ask. When it's empty, ask the user
where skill/guidance markdown lives in this repo (a glob pattern is fine, e.g.
`"packages/*/skills"`), and carry their answer into Step 5 as `options.inventory.skillsDirs`.

### Step 5: Write the config

Write `paniolo.config.json` at the repo root with the selected vendors. Keep it minimal —
this config grows with the rule set, so don't pre-populate `rules` with every rule ID:

```json
{
  "$schema": "https://paniolo.ai/schemas/paniolo.config.v1.json",
  "extends": ["@paniolo/scan/presets/meta-harness.json"],
  "harnesses": ["claude", "cursor"],
  "rules": {},
  "options": {}
}
```

- `harnesses` — exactly what the user picked in Step 3, lowercase, matching the CLI's
  `--harness` flag and the `harnesses` field from `--print-discovery`/the scan report.
  Omit the key entirely (rather than writing `[]`) when the user chose "all".
- `extends` — default to `@paniolo/scan/presets/meta-harness.json` unless the user names
  a different preset (`paniolo-reference`, `scan-legacy`, `guidance-strict`,
  `boundary-minimal`).
- `options.inventory.skillsDirs` — only present when Step 4 ran (discovery found nothing).
  An array of repo-relative directories or glob patterns, e.g. `["packages/*/skills"]`.
  Omit `options.inventory` entirely otherwise — don't write a `skillsDirs` the scanner
  would have resolved to the same thing on its own.

### Step 6: Confirm and offer a scan

Show the written file's contents. Offer to run `npx --yes @paniolo/scan --format json .`
(see the `paniolo-scan` skill/command) to confirm the new config loads and to get a
baseline score.

## Guardrails

- Never invent a `harnesses` value the user didn't pick or explicitly confirm from a
  detected default.
- Never invent a `skillsDirs` value — only write one the user named in Step 4, and only
  when discovery's `resolved` list was empty.
- Don't write `rules`, `guidance`, or `boundary` overrides unless the user asks for them
  — this command scaffolds, it doesn't tune.
- If a `paniolo.config.json` already exists, never overwrite it without showing the
  current contents and getting explicit confirmation.
