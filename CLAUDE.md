# Claude Notes

Keep this file short. Reusable guidance belongs in [AGENTS.md](/AGENTS.md) and
[.agents/](/.agents/).

## Shared Context

- Read [AGENTS.md](/AGENTS.md) for what this repo is and how to work in it.
- Treat [[sharp-shooter-wiki:rules]] as the canonical source of project rules.
- This repo ships thin adapters only — never reimplement `@paniolo/scan` rules or thresholds here.

## Agent Routing

Load the matching `.agents/agents/*.agent.md` mode when the task fits:

| Task | Agent |
| ---- | ----- |
| Change the scan/remediate flow or trigger across surfaces | [.agents/agents/adapter-sync.agent.md](/.agents/agents/adapter-sync.agent.md) |

For anything else, use [AGENTS.md](/AGENTS.md) and [[sharp-shooter-wiki:rules]].

## Validation

After editing markdown, dogfood the scanner on this repo:

```bash
npx @paniolo/scan --format json .
```
