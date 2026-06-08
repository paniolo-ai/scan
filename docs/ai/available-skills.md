# Available Skills

Portable skills shipped by **paniolo-ai/scan**. Each slug maps to `skills/<slug>/SKILL.md` and
installs into any skill-capable agent via `npx skills add paniolo-ai/scan`.

| Skill | Path | Use When |
| ----- | ---- | -------- |
| [paniolo-scan](/skills/paniolo-scan/SKILL.md) | `skills/paniolo-scan/SKILL.md` | Scanning, auditing, or optimizing a repo's AI-agent harness, then remediating findings |

## Other surfaces

The same flow is also available as a native trigger on harnesses that support it:

- **Claude Code** — `/paniolo-scan` slash command ([commands/paniolo-scan.md](/commands/paniolo-scan.md)).
- **Antigravity / Gemini** — `/paniolo-scan` workflow ([.agent/workflows/paniolo-scan.md](/.agent/workflows/paniolo-scan.md)).
