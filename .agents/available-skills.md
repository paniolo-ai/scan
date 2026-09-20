# Available Skills

Portable skills shipped by **paniolo-ai/scan**. Each slug maps to `.agents/skills/<slug>/SKILL.md`
and installs into any skill-capable agent via `npx skills add paniolo-ai/scan`.

| Skill | Path | Use When |
| ----- | ---- | -------- |
| [paniolo-scan](/.agents/skills/paniolo-scan/SKILL.md) | `.agents/skills/paniolo-scan/SKILL.md` | Scanning, auditing, or optimizing a repo's AI-agent harness, then remediating findings |
| [paniolo-config-init](/.agents/skills/paniolo-config-init/SKILL.md) | `.agents/skills/paniolo-config-init/SKILL.md` | Scaffolding `paniolo.config.json`, including which AI vendors/harnesses the repo supports |

## Other surfaces

The same flows are also available as native triggers on harnesses that support it:

- **Claude Code** — `/paniolo-scan` ([.agents/commands/paniolo-scan.md](/.agents/commands/paniolo-scan.md)) and
  `/paniolo-config-init` ([.agents/commands/paniolo-config-init.md](/.agents/commands/paniolo-config-init.md))
  slash commands.
- **Antigravity / Gemini** — `/paniolo-scan` workflow ([.agents/workflows/paniolo-scan.md](/.agents/workflows/paniolo-scan.md)).

For CI rather than an interactive agent, run the CLI directly —
`npx @paniolo/cli scan . --fail-on error` exits non-zero when findings meet the threshold. See
the README's CI section for a workflow example.
