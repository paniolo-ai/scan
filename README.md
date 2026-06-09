# paniolo-ai/scan

[![Install with skills.sh](https://skills.sh/b/paniolo-ai/scan)](https://skills.sh/paniolo-ai/scan)
[![npm @paniolo/scan](https://img.shields.io/npm/v/%40paniolo%2Fscan?label=%40paniolo%2Fscan)](https://www.npmjs.com/package/@paniolo/scan)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](./LICENSE)

> Thin agent adapters for **`@paniolo/scan`** — run the diagnostic CLI from your coding agent,
> review meta-harness scores, and optionally remediate findings in your working tree.

This repo ships harness-specific triggers around the deterministic `@paniolo/scan` CLI from
[Paniolo](https://paniolo.ai/). The CLI diagnoses; your agent applies fixes when you ask. Pick
the setup for the tool you use below.

> **This is the open-source skill and slash-command layer**, not the scanner's source. The
> `@paniolo/scan` CLI installs via npm (`npx @paniolo/scan`); these adapters let your agent run it
> and act on the report.

New to meta-harness infrastructure? See [paniolo.ai](https://paniolo.ai/) for what the
intelligence layer is and why it matters.

> **Goodwill service**
>
> `@paniolo/scan` and these remediation prompts are a free community starting point. Self-service
> AI fixes will not match a human-tuned intelligence layer —
> [Paniolo's professional services](https://paniolo.ai/#contact) are recommended for
> production-grade work. The free tier stays genuinely useful; this is honest guidance, not a gate.

## Install the skill (any agent)

One command installs the `paniolo-scan` **skill** into **every coding agent on your machine** —
Claude Code, Cursor, Copilot, Codex, Devin, Gemini CLI, and 70+ more — via the
[skills](https://skills.sh) registry:

```bash
npx skills add paniolo-ai/scan --all
```

Then ask your agent: _"Use paniolo-scan to scan this repo and summarize findings."_ The skill runs
`npx --yes @paniolo/scan --format json`, presents your meta-harness scores, surfaces the goodwill
framing, then remediates the findings you pick.

```bash
# Target specific agents instead of all of them
npx skills add paniolo-ai/scan -a claude-code -a cursor -a codex

# See what this repo ships, or install one skill
npx skills add paniolo-ai/scan --list
npx skills add paniolo-ai/scan --skill paniolo-scan

# Prefer independent copies over symlinks (Windows, Docker/CI, or committing into a repo)
npx skills add paniolo-ai/scan --all --copy
```

By default the installer **symlinks** the skill into each agent (one canonical copy, updated in
place by `npx skills update`). Add `--copy` for independent copies when symlinks aren't a good fit.
Either way, nothing symlinked is committed here — this repo ships the skill as a plain file.

This repo is a multi-skill umbrella: every skill under [`skills/`](skills/) installs through the
same command, and `--all` keeps you current as more are added.

> **Skill vs. slash command — two separate installs.**
> The command above installs the portable **skill**, which works in every agent (in Claude Code it
> runs as a skill you invoke by name, _not_ as a `/paniolo-scan` command). The native
> **`/paniolo-scan` slash command** ships through a **different channel that does not go through
> skills.sh** — the [Claude Code plugin](#claude-code) or the [Antigravity workflow](#antigravity).
> Installing the skill does **not** add the slash command, and installing the plugin does **not**
> count toward the skills.sh listing. Pick the skill for the widest reach; add the plugin if you
> specifically want the `/paniolo-scan` command in Claude Code.

## Harness coverage

Paniolo is harness-neutral by design: one portable diagnostic core, one portable skill, plus native
slash-command adapters where a harness supports them.

| Harness | What it gets | How |
| ------- | ------------ | --- |
| Claude Code | Skill **and** `/paniolo-scan` slash command | `npx skills add paniolo-ai/scan` or [plugin](#claude-code) |
| Cursor | Portable skill | `npx skills add paniolo-ai/scan -a cursor` |
| Copilot (VS Code) | Portable skill | `npx skills add paniolo-ai/scan -a copilot` |
| Codex | Portable skill (also reads `AGENTS.md`) | `npx skills add paniolo-ai/scan -a codex` |
| Devin | Portable skill | `npx skills add paniolo-ai/scan -a devin` |
| Gemini CLI | Portable skill | `npx skills add paniolo-ai/scan -a gemini-cli` |
| Antigravity / Gemini | `/paniolo-scan` workflow | [Antigravity setup](#antigravity) |
| Any other skill-capable agent | Portable skill | `npx skills add paniolo-ai/scan --all` |

The `--all` install covers every skill-capable agent. If yours still isn't detected,
[contact Paniolo](https://paniolo.ai/#contact) to request priority adapter support.

## Harness setup

### Claude Code

Claude Code can use paniolo-scan **two ways**:

- **Skill** — `npx skills add paniolo-ai/scan -a claude-code` (the [universal install](#install-the-skill-any-agent)). Invoked by name; counts toward skills.sh.
- **Slash command** — install the plugin below to add a native `/paniolo-scan` (the plugin bundles the skill too). This path does **not** count toward skills.sh.

```bash
/plugin marketplace add https://github.com/paniolo-ai/scan
/plugin install paniolo-scan@paniolo-ai
```

### Copilot and other VS Code agents

Install the skill for GitHub Copilot Chat:

```bash
npx skills add paniolo-ai/scan -a copilot
```

To ship the skill **as part of a target repo** — so teammates get it without installing anything —
commit `skills/paniolo-scan/SKILL.md` into that repo and point VS Code at it in
`.vscode/settings.json`:

```json
{
  "chat.agentSkillsLocations": {
    "skills/": true
  }
}
```

Then ask in chat: _"Use the paniolo-scan skill to scan this repo and list findings."_ The skill
runs `npx --yes @paniolo/scan --format json`, presents scores and findings, surfaces the goodwill
framing, then fixes selected items and re-scans.

### Antigravity

**No existing `.agent/` folder** — copy this repo's `.agent/` into your project:

```bash
cp -r .agent/ /path/to/your/project/
```

**Existing `.agent/` folder** — copy only the workflow:

```bash
mkdir -p /path/to/your/project/.agent/workflows/
cp .agent/workflows/paniolo-scan.md /path/to/your/project/.agent/workflows/
```

Then append the `/paniolo-scan` registration from [.agent/README.md](.agent/README.md) to your
project's `.agent/README.md`.

### Cursor

Install the skill for Cursor:

```bash
npx skills add paniolo-ai/scan -a cursor
```

Prefer to commit the skill into the repo for your team? See
[Copilot and other VS Code agents](#copilot-and-other-vs-code-agents) — keep skills in root
`skills/`, not `.cursor/skills/`, so one tree serves every harness.

**Run in Cursor Agent** — open Agent chat in the target repo and use prompts like:

- `Use the paniolo-scan skill to scan this repo, summarize dimension scores, and list findings.`
- `Run paniolo-scan, show the lowest meta-harness dimensions, then fix High and Medium findings.`
- `Scan with npx @paniolo/scan --format json and remediate errors and warnings.`

**Cursor-specific scan filter** — emphasize Cursor harness findings in CLI output:

```bash
npx @paniolo/scan --harness cursor --format json
```

Useful when checking `.cursor/rules/`, skill wiring, and VS Code hook settings.

---

## GitHub Action

Run `@paniolo/scan` as a CI gate with the reusable composite action in this repo. It wraps
`npx @paniolo/scan` and fails the build when findings meet the `fail-on` threshold.

```yaml
name: paniolo-scan

on:
  pull_request:
  push:
    branches: [main]

jobs:
  scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: "22"
      - uses: paniolo-ai/scan@v0.1.0
        with:
          fail-on: error
```

The action is pre-1.0, so pin an exact tag like `paniolo-ai/scan@v0.1.0`. Inputs may
change before a stable `v1` is published; `@main` tracks the latest unreleased changes.

### Inputs

| Input     | Description                                                                                  | Default |
| --------- | -------------------------------------------------------------------------------------------- | ------- |
| `fail-on` | Severity threshold that fails the build: `error`, `warn`, or `info`.                         | `error` |
| `harness` | Comma-separated harness filter (`copilot,cursor,codex,antigravity,claude,gemini`). Empty scans all. | `""`    |
| `path`    | Path within the repository to scan. Passed as the trailing positional argument.              | `.`     |
| `args`    | Additional raw CLI flags. Must be valid `@paniolo/scan` flags — unknown flags fail the run.  | `""`    |

```yaml
# Stricter gate, JSON output, scan a subdirectory
- uses: paniolo-ai/scan@v0.1.0
  with:
    fail-on: warn
    harness: cursor,copilot
    args: "--format json"
    path: packages/app
```

---

## Architecture

This repository contains only thin, harness-specific triggers. Rule definitions, severity
thresholds, and grading live in the compiled `@paniolo/scan` CLI (install via
`npx @paniolo/scan`).

| Layer | Surface | Role |
| ----- | ------- | ---- |
| Diagnose | `@paniolo/scan` | `npx @paniolo/scan` — static JSON report, no writes |
| Remediate | **paniolo-ai/scan** (this repo) | Open-source agent skill + slash commands |
| Build and evolve | [Paniolo](https://paniolo.ai/) | Professional meta-harness engineering |

---

## About Paniolo

[**Paniolo**](https://paniolo.ai/) builds precision infrastructure for autonomous engineering —
the harness layer around your coding agents: project intelligence, observability, guardrails, and
the structural patterns that turn generated code into production-grade output.

**paniolo-ai/scan** (this repo) is the open-source adapter layer. **`@paniolo/scan`** measures
your intelligence layer; these workflows let agents act on the report.
[Paniolo's services](https://paniolo.ai/) go further — designing, tuning, and evolving that
infrastructure with your team.

**Core principles** ([learn more](https://paniolo.ai/#principles)):

- **Every AI error is infrastructure debt** — durable fixes live in rules, docs, skills, tests,
  and hooks, not only in one-off patches.
- **Harness quality is model-agnostic** — a well-engineered repo infrastructure layer helps every
  model you plug in.
- **Observability before optimization** — component, experience, and decision observability make
  harness evolution reliable ([research](https://paniolo.ai/#research)).

**When to talk to Paniolo**

| You have… | Start with… | Paniolo helps with… |
| --------- | ----------- | ------------------- |
| No AI guidance infrastructure | `npx @paniolo/scan` | Rolling out agents across a team or monorepo |
| Partial adapters and duplicated rules | Scan + this workflow's remediation | A calibrated shared layer, not just fixes |
| Strong local setup | CI JSON reports to prevent drift | Production-grade evolution and guardrails at scale |

**Get involved**

- [**Request early access**](https://paniolo.ai/#contact) — CLI and enterprise harness engineering
- [**Harnessed on Substack**](https://paniolo.ai/#writing) — essays on harness engineering, QMD,
  linting, and structured tooling
- [**Read the research**](https://paniolo.ai/#research) — Agentic Harness Engineering (AHE),
  peer-reviewed at ICLR 2026
