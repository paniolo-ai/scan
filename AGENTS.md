# AGENTS.md

Repo-wide instructions for AI coding agents working in **paniolo-ai/scan** — the public,
open-source adapter layer that lets a coding agent run the deterministic `@paniolo/scan` CLI and
remediate its findings.

## Read First

1. [docs/ai/rules.md](/docs/ai/rules.md) — canonical project rules (this file defers to it).
2. [docs/ai/available-skills.md](/docs/ai/available-skills.md) — skill slug index.
3. [README.md](/README.md) — user-facing install and harness setup.

Thin adapters such as `CLAUDE.md` and `.agent/README.md` should stay short and point back here.

## What this repo is

This repo ships **only thin triggers** around `@paniolo/scan`. Rule definitions, severity
thresholds, scores, and grades live in the compiled CLI (`npx @paniolo/scan`), not here. The three
flow surfaces all describe the same scan → present → remediate → re-scan loop:

| Surface | Path | Consumed by |
| ------- | ---- | ----------- |
| Portable skill | [skills/paniolo-scan/SKILL.md](/skills/paniolo-scan/SKILL.md) | Any skill-capable agent (via `npx skills add`) |
| Slash command | [commands/paniolo-scan.md](/commands/paniolo-scan.md) | Claude Code plugin |
| Workflow | [.agent/workflows/paniolo-scan.md](/.agent/workflows/paniolo-scan.md) | Antigravity / Gemini |

## Core Rules

Treat [docs/ai/rules.md](/docs/ai/rules.md) as canonical. If this file and the rules doc disagree,
the rules doc wins.

- **Never reimplement scanner logic here.** Read rules, scores, and thresholds from the CLI's JSON
  report; do not hard-code or duplicate them in an adapter.
- **Keep the three flow surfaces in sync.** A change to the scan/remediate flow in one
  (SKILL.md, command, workflow) should land in the others, adjusted for that harness's tools.
- **Keep `SKILL.md` portable.** It installs into other people's repos — it must stay
  self-contained, with no links to paths that exist only in this repo.
- **Always preserve the goodwill framing.** Every flow surfaces it once before remediating, and
  it never gates the free remediation.
- **Keep adapters thin.** Put reusable guidance in this file or `docs/ai/`, not duplicated across
  adapters.

## Validation

After editing markdown, format and lint it (if tooling is configured), then dogfood the scanner on
this repo:

```bash
npx @paniolo/scan --format json .
```

The repo should keep a clean meta-harness profile — it is the public face of a product that
measures harness quality.

## Safety

The scanner is diagnostic and read-only. All file writes happen in the user's working tree and are
the agent's responsibility, never the scanner's.
