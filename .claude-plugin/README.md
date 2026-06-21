# .claude-plugin

This directory packages paniolo-scan as a **Claude Code plugin** — the first remediation
adapter that turns the diagnostic CLI into a "scan and fix" experience inside an agent
session.

## Contents

| Path               | Role                                                                         |
| ------------------ | ---------------------------------------------------------------------------- |
| `plugin.json`      | Plugin manifest: name, version, description, author, license, homepage, repository, keywords, and the `skills`/`commands`/`agents` path fields below. |
| `marketplace.json` | Marketplace manifest so this repo is installable as a plugin (`source: ./`). |

This repo standardizes every AI-agent surface — skills, slash commands, and subagents — under
`.agents/` rather than plugin's own root-level defaults (`skills/`, `commands/`, `agents/`).
`plugin.json` repoints Claude Code there with the manifest's
[component path fields](https://code.claude.com/docs/en/plugins-reference#component-path-fields):

```json
"skills": ["./.agents/skills/"],
"commands": ["./.agents/commands/"],
"agents": ["./.agents/agents/"]
```

Because this plugin's marketplace `source` resolves to the marketplace root (`"./"`), the
`skills` field *replaces* the default `skills/` scan rather than adding to it, so there is no
duplicate skill tree. `commands` and `agents` always replace their defaults. The
`/paniolo-scan` and `/paniolo-config-init` slash commands live in
[`.agents/commands/`](/.agents/commands/); subagent modes live in [`.agents/agents/`](/.agents/agents/).

## Installing and registering the command

```bash
# Register this repo as a marketplace, then install
/plugin marketplace add paniolo-ai/scan
/plugin install paniolo-scan@paniolo-ai
```

After installing, `/paniolo-scan` appears in the slash-command list.
