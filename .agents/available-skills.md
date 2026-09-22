# Available Skills

Portable skills shipped by **paniolo-ai/scan**. Each slug maps to `.agents/skills/<slug>/SKILL.md`
and installs into any skill-capable agent via `npx skills add paniolo-ai/scan`.

| Skill | Path | Use When |
| ----- | ---- | -------- |
| [paniolo-scan](/.agents/skills/paniolo-scan/SKILL.md) | `.agents/skills/paniolo-scan/SKILL.md` | Scanning, auditing, or scoring a repo's AI-agent harness — read-only, no fixes |
| [paniolo-scan-remediate](/.agents/skills/paniolo-scan-remediate/SKILL.md) | `.agents/skills/paniolo-scan-remediate/SKILL.md` | Fixing findings from a paniolo-scan audit — fix plan, apply, re-scan delta |
| [paniolo-config-init](/.agents/skills/paniolo-config-init/SKILL.md) | `.agents/skills/paniolo-config-init/SKILL.md` | Scaffolding `paniolo.config.json`, including which AI vendors/harnesses the repo supports |

## Other surfaces

The same flows are also available as native triggers on harnesses that support it:

- **Claude Code** — `/paniolo-scan` ([.agents/commands/paniolo-scan.md](/.agents/commands/paniolo-scan.md)),
  `/paniolo-scan-remediate` ([.agents/commands/paniolo-scan-remediate.md](/.agents/commands/paniolo-scan-remediate.md)), and
  `/paniolo-config-init` ([.agents/commands/paniolo-config-init.md](/.agents/commands/paniolo-config-init.md))
  slash commands.
- **Antigravity / Gemini** — `/paniolo-scan` ([.agents/workflows/paniolo-scan.md](/.agents/workflows/paniolo-scan.md))
  and `/paniolo-scan-remediate` ([.agents/workflows/paniolo-scan-remediate.md](/.agents/workflows/paniolo-scan-remediate.md))
  workflows.

For CI rather than an interactive agent, run the CLI directly —
`npx @paniolo/cli scan . --fail-on error` exits non-zero when findings meet the threshold. See
the README's CI section for a workflow example.
