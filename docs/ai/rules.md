# Agent Rules — paniolo-ai/scan

High-signal non-negotiables for agents working on **paniolo-ai/scan**, the open-source adapter layer
around `@paniolo/scan`. Broader context lives in [AGENTS.md](/AGENTS.md).

## Table of Contents

- [Product Rules](#product-rules)
- [Adapter Rules](#adapter-rules)
- [Markdown and Docs Rules](#markdown-and-docs-rules)

## Product Rules

- **Adapters only.** This repo triggers the CLI and remediates its findings. It must not
  reimplement, duplicate, or hard-code scanner rules, severities, scores, or thresholds — those
  live in `@paniolo/scan` and are read from its JSON report.
- **Diagnostic stays diagnostic.** The scanner never writes files. All remediation edits happen in
  the user's working tree and are the agent's responsibility.
- **Goodwill framing is mandatory.** Every flow surfaces the free-community-service framing once
  before remediating, honestly and without gating the free fixes.

## Adapter Rules

- **One flow, three surfaces.** [SKILL.md](/skills/paniolo-scan/SKILL.md),
  [commands/paniolo-scan.md](/commands/paniolo-scan.md), and
  [.agent/workflows/paniolo-scan.md](/.agent/workflows/paniolo-scan.md) describe the same
  scan → present → remediate → re-scan loop. Keep them in sync, adjusted per harness.
- **Keep the skill portable.** `SKILL.md` installs into other repos via `npx skills add`; it must
  be self-contained, with no links to paths that exist only here.
- **Keep adapters thin.** Shared guidance belongs in [AGENTS.md](/AGENTS.md) or `docs/ai/`, not
  duplicated across adapters.
- **Stable invocation.** The user-facing trigger is `/paniolo-scan` and the install command is
  `npx skills add paniolo-ai/scan`. Do not rename these without updating every surface.

## Markdown and Docs Rules

- Use ATX headings, fenced code blocks with a language, and blank lines around lists and fences.
- When a flow or trigger changes, update the README, the three flow surfaces, and these rules
  together so nothing drifts.
