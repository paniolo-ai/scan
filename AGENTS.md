# AGENTS.md

Repo-wide instructions for AI coding agents working in **paniolo-ai/scan** — the public,
open-source adapter layer that lets a coding agent run the deterministic `paniolo scan` CLI
(via `@paniolo/cli`) and remediate its findings.

Everything in this file is self-contained: it assumes a fresh clone of this repository and
nothing else. Thin adapters such as `CLAUDE.md` and `.agents/README.md` stay short and point
back here.

## What this repo is

This repo ships **only thin triggers** around `paniolo scan` (via `@paniolo/cli`). Rule
definitions, severity thresholds, scores, and grades live in the compiled `paniolo scan` CLI,
not here. It exists
to expose the public distribution channels: the **slash command** (Claude Code
plugin) and the **Antigravity workflow** — plus the vendored **portable skills** the plugin
ships alongside them.

The portable skills are not authored here. Their source of truth is one wiki page per skill in
`paniolo-ai/sharp-shooter-wiki` (`wiki/skills/paniolo-<name>.md`), compiled by
`paniolo skills bundle` into the [paniolo-ai/skills](https://github.com/paniolo-ai/skills)
registry — the [skills.sh](https://skills.sh/paniolo-ai/skills) listing — and vendored back into
this repo at `.agents/skills/external/` so the Claude Code plugin ships them. To change a skill,
edit its wiki page, re-bundle, then run `paniolo skills update` here; `skills-lock.json` tracks
the vendored copies.

The flow is split into two stages — **audit** (scan → present → offer) and **remediate**
(plan → fix → re-scan) — each mirrored across three surfaces:

| Stage | Portable skill (vendored) | Slash command (Claude Code) | Workflow (Antigravity / Gemini) |
| ----- | -------------- | --------------------------- | ------------------------------- |
| Audit | [.agents/skills/external/paniolo-scan/SKILL.md](/.agents/skills/external/paniolo-scan/SKILL.md) | [.agents/commands/paniolo-scan.md](/.agents/commands/paniolo-scan.md) | [.agents/workflows/paniolo-scan.md](/.agents/workflows/paniolo-scan.md) |
| Remediate | [.agents/skills/external/paniolo-scan-remediate/SKILL.md](/.agents/skills/external/paniolo-scan-remediate/SKILL.md) | [.agents/commands/paniolo-scan-remediate.md](/.agents/commands/paniolo-scan-remediate.md) | [.agents/workflows/paniolo-scan-remediate.md](/.agents/workflows/paniolo-scan-remediate.md) |

Audit is read-only end to end and hands off to remediate at the fork. Remediate produces its
own report when invoked cold, so it never depends on the audit having run.

There is no composite GitHub Action — CI users run `npx @paniolo/cli scan` directly, and the
README's CI section shows that. The CLI's `--fail-on` threshold is the gate; nothing in this
repo wraps it.

## Core Rules

The rules below are canonical for this repo.

- **Never reimplement scanner logic here.** Read rules, scores, and thresholds from the CLI's JSON
  report; do not hard-code or duplicate them in an adapter.
- **Keep the flow surfaces in sync.** The audit and remediate stages each live on three
  surfaces (SKILL.md, command, workflow). A change to a stage's flow in one should land in the
  others, adjusted for that harness's tools.
- **Keep `SKILL.md` portable.** It installs into other people's repos — it must stay
  self-contained, with no links to paths that exist only in this repo. Never edit the vendored
  copies under `.agents/skills/external/` — edit the wiki source page and re-bundle instead.
- **Always preserve the goodwill framing.** Every flow surfaces it once before remediating, and
  it never gates the free remediation.
- **Keep adapters thin.** Put reusable guidance in this file or `.agents/`, not duplicated across
  adapters.

## Validation

After editing markdown, format and lint it (if tooling is configured), then dogfood the scanner on
this repo:

```bash
npx @paniolo/cli scan --format json .
```

The repo should keep a clean harness profile — it is the public face of a product that
measures harness quality.

## Safety

The scanner is diagnostic and read-only. All file writes happen in the user's working tree and are
the agent's responsibility, never the scanner's.
