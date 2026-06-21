# AGENTS.md

Repo-wide instructions for AI coding agents working in **paniolo-ai/scan** — the public,
open-source adapter layer that lets a coding agent run the deterministic `@paniolo/scan` CLI and
remediate its findings.

## Read First

1. [llm-wiki/wiki/authoring/harness-rules/rules.md](llm-wiki/wiki/authoring/harness-rules/rules.md) — canonical project rules (this file defers to it).
2. [llm-wiki/wiki/authoring/harness-ai-system/available-skills.md](llm-wiki/wiki/authoring/harness-ai-system/available-skills.md) — skill slug index.
3. [README.md](/README.md) — user-facing install and harness setup.

Thin adapters such as `CLAUDE.md` and `.agents/README.md` should stay short and point back here.

## What this repo is

This repo ships **only thin triggers** around `@paniolo/scan`. Rule definitions, severity
thresholds, scores, and grades live in the compiled CLI (`npx @paniolo/scan`), not here. It exists
to expose two public distribution channels: the **portable skill** (installed via `npx skills add`,
the [skills.sh](https://skills.sh/paniolo-ai/scan) listing) and the **GitHub Action** (a CI gate).

The three **flow surfaces** all describe the same interactive scan → present → remediate → re-scan
loop:

| Surface | Path | Consumed by |
| ------- | ---- | ----------- |
| Portable skill | [.agents/skills/paniolo-scan/SKILL.md](/.agents/skills/paniolo-scan/SKILL.md) | Any skill-capable agent (via `npx skills add`) |
| Slash command | [.agents/commands/paniolo-scan.md](/.agents/commands/paniolo-scan.md) | Claude Code plugin |
| Workflow | [.agents/workflows/paniolo-scan.md](/.agents/workflows/paniolo-scan.md) | Antigravity / Gemini |

The **GitHub Action** is a separate, non-interactive surface — it runs the diagnostic only (no
remediation) as a CI gate that fails the build on findings at or above a threshold:

| Surface | Path | Consumed by |
| ------- | ---- | ----------- |
| Composite action | [action.yml](/action.yml) | GitHub Actions CI (`uses: paniolo-ai/scan@<tag>`) |
| Action self-test | [.github/workflows/action-self-test.yml](/.github/workflows/action-self-test.yml) | Proves `action.yml` runs end-to-end on a real runner |

## Core Rules

Treat [llm-wiki/wiki/authoring/harness-rules/rules.md](llm-wiki/wiki/authoring/harness-rules/rules.md) as canonical. If this file and the rules doc disagree,
the rules doc wins.

- **Never reimplement scanner logic here.** Read rules, scores, and thresholds from the CLI's JSON
  report; do not hard-code or duplicate them in an adapter.
- **Keep the three flow surfaces in sync.** A change to the scan/remediate flow in one
  (SKILL.md, command, workflow) should land in the others, adjusted for that harness's tools.
- **Keep the GitHub Action in sync with the CLI contract.** When the CLI's flags or severity
  vocabulary change, update [action.yml](/action.yml) inputs, the README's Action section, and the
  [self-test workflow](/.github/workflows/action-self-test.yml) together.
- **Keep `SKILL.md` portable.** It installs into other people's repos — it must stay
  self-contained, with no links to paths that exist only in this repo.
- **Always preserve the goodwill framing.** Every flow surfaces it once before remediating, and
  it never gates the free remediation.
- **Keep adapters thin.** Put reusable guidance in this file or `.agents/`, not duplicated across
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
