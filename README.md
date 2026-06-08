# paniolo-workflows

A public, open-source repository containing lightweight agent adapters and slash commands for the **paniolo-scan** diagnostic CLI.

These adapters allow AI coding assistants to run the deterministic paniolo-scan tool, present the diagnostic scores, and automatically remediate any issues directly in your working codebase.

## Harness Setup

### 1. Claude Code
To add `/paniolo-scan` as a plugin in Claude Code:

```bash
# Add this directory as a local marketplace, then install the plugin
/plugin marketplace add https://github.com/paniolo/paniolo-workflows
/plugin install paniolo-scan@paniolo
```

### 2. Antigravity
To add `/paniolo-scan` as a workflow in Antigravity:

Copy the `.agent/` folder from this repository into the root of your codebase:
```bash
cp -r .agent/ /path/to/your/project/
```

### 3. Cursor, Copilot, and Generic Assistants
You can use the portable Agent Skills standard by referencing the skill file directly:
*   [skills/paniolo-scan/SKILL.md](skills/paniolo-scan/SKILL.md)

---

## Architecture

This repository contains only thin, harness-specific triggers. All rule definitions, severity thresholds, and grading algorithms remain private within the compiled `@paniolo/scan` CLI tool.
