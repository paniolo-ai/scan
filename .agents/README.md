# .agents

Shared home for every AI-agent surface this repo ships, instead of scattering them across
root-level `skills/`, `commands/`, `agents/`, and `.agent/` directories.

## Contents

- [`skills/`](skills/) — portable skills (`<slug>/SKILL.md`), installable into any
  skill-capable agent via `npx skills add paniolo-ai/scan`. Also the Claude Code plugin's
  skill component, repointed here via [`plugin.json`](/.claude-plugin/plugin.json)'s `skills`
  field.
- [`commands/`](commands/) — Claude Code plugin slash commands (`/paniolo-scan`,
  `/paniolo-config-init`), repointed here via `plugin.json`'s `commands` field.
- [`agents/`](agents/) — Claude Code plugin subagent modes (e.g.
  [`adapter-sync.agent.md`](agents/adapter-sync.agent.md)), repointed here via `plugin.json`'s
  `agents` field.
- [`workflows/`](workflows/) — Antigravity-specific slash-command workflows (see below).
  Antigravity has no equivalent manifest remapping, so its workflow files live under their own
  vendor-specific name rather than `commands/`.

## Antigravity workflows

Antigravity does **not** support native automated hooks (unlike Claude Code, Copilot, and
Cursor). It uses manual slash-command workflows instead.

- [`workflows/paniolo-scan.md`](workflows/paniolo-scan.md) — the `/paniolo-scan` slash command.
  Scan this repo's AI-agent harness with the deterministic paniolo-scan tool, then remediate
  findings.
