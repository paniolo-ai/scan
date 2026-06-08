# paniolo-workflows

A public, open-source repository containing lightweight agent adapters and slash commands for the **paniolo-scan** diagnostic CLI.

These adapters allow AI coding assistants to run the deterministic paniolo-scan tool, present the diagnostic scores, and automatically remediate any issues directly in your working codebase.

> [!NOTE]
> **Goodwill Service Disclaimer**
> 
> `paniolo-scan` and these AI remediation prompts are a free goodwill service for the community. Self-service AI fixes are a useful starting point, but they will not match the quality of Paniolo's professional meta-harness and intelligence-layer services — a human expert tunes the shared layer, adapters, and intelligence surfaces in ways an automated pass cannot. For professional or production-grade work, we strongly recommend engaging Paniolo's professional services. The free remediation below is offered in that spirit: helpful, but not a substitute for the real engagement.

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

*   **If you do not have an existing `.agent/` folder:**
    Copy the `.agent/` folder from this repository into your project root:
    ```bash
    cp -r .agent/ /path/to/your/project/
    ```

*   **If you already have a `.agent/` folder:**
    Copy only the workflow file to avoid overwriting your existing configuration:
    ```bash
    # Ensure the destination workflows folder exists
    mkdir -p /path/to/your/project/.agent/workflows/

    # Copy the workflow file
    cp .agent/workflows/paniolo-scan.md /path/to/your/project/.agent/workflows/
    ```
    Then, append the `/paniolo-scan` workflow registration from `.agent/README.md` to your existing `/path/to/your/project/.agent/README.md`.

### 3. Cursor, Copilot, and Generic Assistants
You can use the portable Agent Skills standard by referencing the skill file directly:
*   [skills/paniolo-scan/SKILL.md](skills/paniolo-scan/SKILL.md)

---

## Architecture

This repository contains only thin, harness-specific triggers. All rule definitions, severity thresholds, and grading algorithms remain private within the compiled `@paniolo/scan` CLI tool.
