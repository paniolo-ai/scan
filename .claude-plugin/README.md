# .claude-plugin

This directory packages paniolo-scan as a **Claude Code plugin** — the first remediation
adapter that turns the diagnostic CLI into a "scan and fix" experience inside an agent
session.

## Contents

| Path               | Role                                                                         |
| ------------------ | ---------------------------------------------------------------------------- |
| `plugin.json`      | Plugin manifest: name, version, description, license, homepage, repository.  |
| `marketplace.json` | Marketplace manifest so this repo is installable as a plugin (`source: ./`). |

The plugin's user-facing surface — the `/paniolo-scan` slash command — lives in
`commands/`, one level up, because Claude Code discovers commands
from the plugin root's `commands/` directory.

## Installing and registering the command

```bash
# Register the workflows repo as a marketplace, then install
/plugin marketplace add paniolo/paniolo-workflow
/plugin install paniolo-scan@paniolo
```

After installing, `/paniolo-scan` appears in the slash-command list.
