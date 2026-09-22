# Available Skills

The portable skills ship from the **[paniolo-ai/skills](https://github.com/paniolo-ai/skills)**
registry (listed at <https://skills.sh/paniolo-ai/skills>) and install into any skill-capable
agent via `npx skills add paniolo-ai/skills --skill <name>`. This repo vendors them into
`.agents/skills/external/` (tracked by `skills-lock.json`) so the Claude Code plugin ships them;
their source of truth is the wiki page per skill in `paniolo-ai/sharp-shooter-wiki`.

| Skill | Vendored path | Use When |
| ----- | ------------- | -------- |
| [paniolo-scan](/.agents/skills/external/paniolo-scan/SKILL.md) | `.agents/skills/external/paniolo-scan/SKILL.md` | Scanning, auditing, or scoring a repo's AI-agent harness - read-only, no fixes |
| [paniolo-scan-remediate](/.agents/skills/external/paniolo-scan-remediate/SKILL.md) | `.agents/skills/external/paniolo-scan-remediate/SKILL.md` | Fixing findings from a paniolo-scan audit - fix plan, apply, re-scan delta |
| [paniolo-config-init](/.agents/skills/external/paniolo-config-init/SKILL.md) | `.agents/skills/external/paniolo-config-init/SKILL.md` | Scaffolding `paniolo.config.json`, including which AI vendors/harnesses the repo supports |
| [paniolo-config-upgrade](/.agents/skills/external/paniolo-config-upgrade/SKILL.md) | `.agents/skills/external/paniolo-config-upgrade/SKILL.md` | Migrating or deduplicating an existing `paniolo.config.json` into `repoDefaults`/`repoTypes` |

## Other surfaces

The same flows are also available as native triggers on harnesses that support it:

- **Claude Code** - `/paniolo-scan` ([.agents/commands/paniolo-scan.md](/.agents/commands/paniolo-scan.md)),
  `/paniolo-scan-remediate` ([.agents/commands/paniolo-scan-remediate.md](/.agents/commands/paniolo-scan-remediate.md)),
  `/paniolo-config-init` ([.agents/commands/paniolo-config-init.md](/.agents/commands/paniolo-config-init.md)), and
  `/paniolo-config-upgrade` ([.agents/commands/paniolo-config-upgrade.md](/.agents/commands/paniolo-config-upgrade.md))
  slash commands.
- **Antigravity / Gemini** - `/paniolo-scan` ([.agents/workflows/paniolo-scan.md](/.agents/workflows/paniolo-scan.md))
  and `/paniolo-scan-remediate` ([.agents/workflows/paniolo-scan-remediate.md](/.agents/workflows/paniolo-scan-remediate.md))
  workflows.

For CI rather than an interactive agent, run the CLI directly -
`npx @paniolo/cli scan . --fail-on error` exits non-zero when findings meet the threshold. See
the README's CI section for a workflow example.
