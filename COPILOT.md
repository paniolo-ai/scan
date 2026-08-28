# GitHub Copilot — Paniolo Scan

Keep this file short. Reusable guidance belongs in [AGENTS.md](/AGENTS.md) and
[.agents/](/.agents/).

## Shared Context

- Read [AGENTS.md](/AGENTS.md) for what this repo is and how to work in it.
- Treat [[sharp-shooter-wiki:rules]] as the canonical source of project rules.
- This repo ships thin adapters only. Never reimplement `paniolo scan` rules or
  thresholds here.

## Copilot-Specific Setup

Copilot discovers the `paniolo-scan` skill through harness qmd search.

1. Open the
   [`paniolo-harness.code-workspace`](../harness/paniolo-harness.code-workspace).
   It indexes Scan alongside the configured sibling repositories.

2. Search for the skill and relevant documentation:

   `pnpm -C ../harness run qmd -- search "scan audit harness"`

3. Run `npx @paniolo/cli scan --format json .` to validate changes.

## Agent Routing

Load the matching `.agents/agents/*.agent.md` mode when the task fits:

| Task                                                      | Agent                                                                         |
| --------------------------------------------------------- | ----------------------------------------------------------------------------- |
| Change the scan/remediate flow or trigger across surfaces | [.agents/agents/adapter-sync.agent.md](/.agents/agents/adapter-sync.agent.md) |

For anything else, use [AGENTS.md](/AGENTS.md) and [[sharp-shooter-wiki:rules]].

## Cross-reference

- **Harness Copilot guide** → [paniolo-harness/COPILOT.md](../harness/COPILOT.md)
- **Shared harness guidance** → See harness [AGENTS.md](../harness/AGENTS.md)
