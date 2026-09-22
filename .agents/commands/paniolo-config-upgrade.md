---
description: "Upgrade, migrate, or deduplicate an existing paniolo.config.json — legacy shape to repo-centric, repoDefaults/repoTypes factoring, connect a repo, register a wiki, or trace effective settings. Use when the user says /paniolo-config-upgrade, 'migrate paniolo config', 'dedupe these repo blocks', or 'where does this config value come from'."
allowed-tools: Bash(*), Read(*), Write(*), Edit(*), AskUserQuestion(*)
---

# /paniolo-config-upgrade — migrate and deduplicate paniolo.config.json

Upgrade an **existing** `paniolo.config.json`: move it to the repo-centric shape,
stop repeating the same policy in every `repos` entry, and register repos or
wikis. Prefer the CLI's own writes over hand edits — the CLI verifies semantic
equivalence and preserves comments; a hand migration proves nothing.

For creating a config from scratch, use `/paniolo-config-init` instead.

## Shapes and layering

Two config shapes exist:

- **Legacy** — `scan.targets`, `evolve.verification.targets`, `wiki.wikis`;
  targets inherit host scan policy.
- **Repo-centric** — `name` + `repos.<name>` + top-level `verify`, plus the
  sharing layers `repoDefaults` and `repoTypes`.

Mixing the two is an error — every paniolo command refuses a config carrying
both until one side is removed.

Effective values resolve in this order:

`repos.<name>` → `repoTypes.<type>` → `repoDefaults` → profile preset → builtin

- `repoDefaults` applies to every `repos` entry; never to the host.
- `repoTypes.<name>` applies to nodes declaring `"type": "<name>"` — one type
  per node; unknown names are a config error.
- `path`, `qmd`, `workspace`, and `wiki` membership stay on the node — identity
  never defaults. `"wiki": {}` is a legal membership marker that inherits
  `root`/`pagePrefixes`/`domains`/`allowUnknownWikis` field-by-field.
- Empty means unset: an absent field or empty list inherits; a node cannot
  clear an inherited list back to empty.
- `rules` merge per-key; the node's own entry always wins.

## Flow

### Step 1: Confirm the CLI is new enough

```bash
npx --yes @paniolo/cli --version
```

`repoDefaults`/`repoTypes` and `evolve config migrate` need a recent
`@paniolo/cli`. If the binary predates them, upgrade
(`npm i -g @paniolo/cli@latest`) — a config using the new layers carries a
`version` floor so an old binary refuses instead of silently ignoring them.

### Step 2: Inspect the current shape

```bash
npx @paniolo/cli evolve config graph --json
```

`sourceShape` is `legacy` or `repo-centric`. For "where did this value come
from", `evolve config effective --target <repo>` reports each effective setting
with its provenance (`node` / `type` / `defaults` / `preset` / `builtin` /
`cli`); omit `--target` for the host.

If the file doesn't exist or has no config to upgrade, say so and offer
`/paniolo-config-init` instead.

### Step 3: Migrate a legacy config

```bash
npx @paniolo/cli evolve config migrate            # preview only
npx @paniolo/cli evolve config migrate --apply    # writes after confirmation
```

- The preview lists each `repos.<name>` plus `repoDefaults`, `verify`, and
  `wiki` moves. Show it to the user before applying.
- The write is verified: the CLI reparses the result and refuses a file whose
  effective behavior differs. Comments and unrelated fields are preserved;
  `paniolo.config.json.pre-migration.bak` keeps the original.
- Fields identical across every migrated repo lift into `repoDefaults`
  automatically — including wiki fields shared by every wiki repo.
- A migration emitting `repoDefaults` also raises `version` to the build's
  schema version so older binaries fail loudly.

### Step 4: Deduplicate an already-repo-centric config

`migrate` reports "already migrated" on a repo-centric file, so dedupe by hand:

- A field repeated identically in **every** `repos` entry → `repoDefaults`;
  delete the per-node copies.
- A field shared by a **class** of repos (e.g. every wiki) →
  `repoTypes.<name>` plus `"type": "<name>"` on each member.
- Leave on the node: `path`, `qmd`, `workspace`, `wiki` membership, genuine
  per-repo overrides.
- Verify with `evolve config effective --target <name>` before and after —
  effective values must be identical; only provenance changes.

### Step 5: Add a repo or a wiki

- Connect a sibling repo: from inside that repo, run
  `npx @paniolo/cli evolve harness connect --harness <path-to-harness>`;
  `--apply` writes the harness-side `repos.<name>` entry (or `scan.targets` on
  a legacy config) after confirmation.
- Register a wiki: `npx @paniolo/cli wiki init` stamps the wiki directory and
  registers it in the config.

### Step 6: Confirm

Show the final diff of `paniolo.config.json`. Offer `evolve config graph` or a
`npx @paniolo/cli scan --format json .` run to confirm everything still loads.

## Guardrails

- Prefer the CLI writers (`migrate --apply`, `connect --apply`, `wiki init`) —
  they are format-preserving and verified; hand edits are not.
- Never mix legacy and repo-centric keys in one file; commands refuse the mix.
- Don't copy a type layer's values onto its member nodes — that recreates the
  duplication the layer exists to remove.
- `version` declares the minimum CLI the file needs — never lower it below what
  the file's features require, and never strip it from a migrated config.
