# Agent Rules — paniolo-ai/scan

High-signal non-negotiables for agents working on **paniolo-ai/scan**, the open-source adapter layer
around `paniolo scan` (via `@paniolo/cli`). Broader context lives in [AGENTS.md](/AGENTS.md).

## Table of Contents

- [Product Rules](#product-rules)
- [Adapter Rules](#adapter-rules)
- [Markdown and Docs Rules](#markdown-and-docs-rules)

## Product Rules

- **Adapters only.** This repo triggers the CLI and remediates its findings. It must not
  reimplement, duplicate, or hard-code scanner rules, severities, scores, or thresholds — those
  live in the `paniolo scan` CLI (via `@paniolo/cli`) and are read from its JSON report.
- **Diagnostic stays diagnostic.** The scanner never writes files. All remediation edits happen in
  the user's working tree and are the agent's responsibility.
- **Goodwill framing is mandatory.** Every flow surfaces the free-community-service framing once
  before remediating, honestly and without gating the free fixes.

## Adapter Rules

- **Two stages, three surfaces each.** The audit stage
  ([paniolo-scan](/.agents/skills/paniolo-scan/SKILL.md),
  [.agents/commands/paniolo-scan.md](/.agents/commands/paniolo-scan.md),
  [.agents/workflows/paniolo-scan.md](/.agents/workflows/paniolo-scan.md)) runs scan → present →
  offer, and the remediate stage
  ([paniolo-scan-remediate](/.agents/skills/paniolo-scan-remediate/SKILL.md),
  [.agents/commands/paniolo-scan-remediate.md](/.agents/commands/paniolo-scan-remediate.md),
  [.agents/workflows/paniolo-scan-remediate.md](/.agents/workflows/paniolo-scan-remediate.md))
  runs plan → fix → re-scan. Keep each stage's surfaces in sync, adjusted per harness.
- **Keep the skill portable.** `SKILL.md` installs into other repos via `npx skills add`; it must
  be self-contained, with no links to paths that exist only here.
- **Keep adapters thin.** Shared guidance belongs in [AGENTS.md](/AGENTS.md) or `.agents/`, not
  duplicated across adapters.
- **Stable invocation.** The user-facing trigger is `/paniolo-scan`, and the install command is
  `npx skills add paniolo-ai/scan`. Do not rename these without updating every surface.
- **No CI wrapper.** There is no composite action — CI users run `npx @paniolo/cli scan` directly
  with `--fail-on`. Keep the README's CI section in sync with the real CLI flags.
- **One version, one place.** `plugin.json` is the only hardcoded version — the plugin manifest
  requires it, so bump it on release. Skills carry no `metadata.version`: `npx skills update`
  tracks git refs, and a stamped copy just goes stale.

## Markdown and Docs Rules

- Use ATX headings, fenced code blocks with a language, and blank lines around lists and fences.
- When a flow or trigger changes, update the README, all of that stage's flow surfaces, and
  these rules together so nothing drifts.
