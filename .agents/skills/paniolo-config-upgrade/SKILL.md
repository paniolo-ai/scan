---
name: paniolo-config-upgrade
description: >
  Upgrade, migrate, or deduplicate an existing paniolo.config.json — convert the
  legacy product-siloed shape (scan.targets, evolve.verification.targets,
  wiki.wikis) to the repo-centric shape, factor repeated per-repo policy into
  repoDefaults or named repoTypes layers, connect a sibling repo, or trace where
  an effective setting came from. Writes config files after confirmation. Use
  when asked to migrate, modernize, dedupe, or restructure an existing
  paniolo.config.json, or on /paniolo-config-upgrade. Do not use to create a
  config from scratch — that is paniolo-config-init.
license: MIT
metadata:
  homepage: https://github.com/paniolo-ai/scan
---

# paniolo-config-upgrade — migrate and deduplicate paniolo.config.json

Upgrade an **existing** `paniolo.config.json`: move it to the repo-centric shape,
stop repeating the same policy in every `repos` entry, and register repos or
wikis. Prefer the CLI's own writes over hand edits — the CLI verifies semantic
equivalence and preserves comments; a hand migration proves nothing.

## When to use

- "migrate paniolo config", "dedupe these repo blocks", "every repo repeats the
  same settings", "add this repo to the harness", "register this wiki", "where
  does this setting come from", "why isn't this config value applying".
- Not for creating a config from scratch — that's `paniolo-config-init`.

## Shapes and layering

Two config shapes exist:

- **Legacy** — policy lives under `scan.targets`, `evolve.verification.targets`,
  and `wiki.wikis`, and targets inherit host scan policy.
- **Repo-centric** — `name` plus a `repos.<name>` map and top-level `verify`,
  with two opt-in sharing layers: `repoDefaults` and `repoTypes`.

Mixing the two is an error — every paniolo command refuses a config carrying
both until one side is removed.

Within the repo-centric shape, an effective value resolves in this order:

`repos.<name>` → `repoTypes.<type>` → `repoDefaults` → profile preset → builtin

- `repoDefaults` — policy every sibling shares. Applies to all `repos` entries;
  never to the host's own top-level settings.
- `repoTypes.<name>` — policy for a _class_ of repos; a node opts in with
  `"type": "<name>"`. One type per node; an unknown type name is a config error.
- Node — always owns `path`, `qmd`, and `workspace` (identity and membership
  never come from a layer), plus whatever it genuinely overrides.
- `wiki` — the node's `wiki` key _is_ the membership marker; `"wiki": {}` is
  legal and inherits `root`, `pagePrefixes`, `domains`, and `allowUnknownWikis`
  field-by-field from its type or `repoDefaults`. A layer's `wiki` section can
  fill fields but can never mark a repo a wiki.
- Empty means unset — an absent field or empty list inherits; a node cannot
  explicitly "clear" an inherited list back to empty.
- `rules` merge per-key across the layers; a node's own entry always wins, so
  `"rule-id": "off"` on the node overrides the layer's severity.

## Flow

### 1. Confirm the CLI is new enough

```bash
npx --yes @paniolo/cli --version
```

`repoDefaults`, `repoTypes`, and `evolve config migrate` need a recent
`@paniolo/cli`. Upgrade first (`npm i -g @paniolo/cli@latest`) if the binary
predates them — a config using the new layers carries a `version` floor, so an
old binary refuses rather than silently ignoring the layer.

### 2. Inspect the current shape

```bash
npx @paniolo/cli evolve config graph --json
```

`sourceShape` is `legacy` or `repo-centric`. To answer "where did this value
come from", `evolve config effective --target <repo>` reports each effective
setting plus its provenance (`node` / `type` / `defaults` / `preset` /
`builtin` / `cli`); omit `--target` for the host.

### 3. Migrate a legacy config

```bash
npx @paniolo/cli evolve config migrate            # preview only, writes nothing
npx @paniolo/cli evolve config migrate --apply    # writes after confirmation
```

- The preview lists each `repos.<name>` it creates plus `repoDefaults`, `verify`,
  and `wiki` moves.
- The write is verified: the CLI reparses the migrated file and refuses to
  produce one whose effective behavior differs. Comments and unrelated fields
  are preserved; `paniolo.config.json.pre-migration.bak` keeps the original.
- Fields identical across every migrated repo lift into `repoDefaults`
  automatically — including wiki fields shared by every wiki repo.
- A migration that emits `repoDefaults` also raises `version` to the build's
  schema version so older binaries fail loudly instead of dropping the layer.

### 4. Deduplicate an already-repo-centric config

`migrate` reports "already migrated" on a repo-centric file, so dedupe by hand:

- A field repeated identically in **every** `repos` entry → move it to
  `repoDefaults`, delete the per-node copies.
- A field shared by a **class** of repos (e.g. every wiki) →
  `repoTypes.<name>`, plus `"type": "<name>"` on each member node.
- Leave on the node: `path`, `qmd`, `workspace`, `wiki` membership, and genuine
  per-repo overrides.
- Verify with `evolve config effective --target <name>` before and after — the
  effective values should be identical; only the provenance changes.

### 5. Add a repo or a wiki

- Connect a sibling repo: from inside that repo, run
  `npx @paniolo/cli evolve harness connect --harness <path-to-harness>`; `--apply`
  writes the harness-side `repos.<name>` entry (or `scan.targets` on a legacy
  config) after confirmation.
- Register a wiki: `npx @paniolo/cli wiki init` stamps the wiki directory and
  registers it in the config.

## Do not

- Do not use this for a repo with no `paniolo.config.json` yet — scaffold one with
  `paniolo-config-init`.
- Do not hand-edit a legacy config into repo-centric shape — run
  `evolve config migrate`, which verifies equivalence; hand edits can silently drop policy.
- Do not put `path`, `qmd`, or `workspace` in `repoDefaults`/`repoTypes` — identity and
  membership live on the node.
- Do not rely on `repoDefaults`/`repoTypes` while the installed `@paniolo/cli` predates
  them — check `--version` first; the `version` field exists to fail loudly on old builds.

## Guardrails

- Prefer the CLI writers (`migrate --apply`, `connect --apply`, `wiki init`) —
  they are format-preserving and verified; hand edits are not.
- Never mix legacy and repo-centric keys in one file; commands refuse the mix.
- Don't copy a type layer's values onto its member nodes — that's the
  duplication the layer exists to remove; the node only needs overrides.
- `version` declares the minimum CLI the file needs — don't lower it below what
  the file's features require, and don't strip it from a migrated config.

## References

- Field-by-field config reference: <https://paniolo.ai/pages/config.html>
- JSON schema: <https://paniolo.ai/schemas/paniolo.config.v2.json>
- CLI: `npx @paniolo/cli evolve config --help`

---

## About Paniolo

[**Paniolo**](https://paniolo.ai/) builds precision infrastructure for autonomous
engineering — the harness layer around your coding agents: project intelligence,
observability, guardrails, and the structural patterns that turn generated code into
production-grade output.

`paniolo.config.json` is how a repo tells `paniolo scan` (via `@paniolo/cli`) which
harnesses it supports and how to weigh its rules. This skill upgrades and deduplicates
that file; the scanner reads it.
