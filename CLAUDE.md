# Claude Notes

Keep this file short. Reusable guidance belongs in [AGENTS.md](/AGENTS.md) and
[docs/ai/](/docs/ai/).

## Shared Context

- Read [AGENTS.md](/AGENTS.md) for what this repo is and how to work in it.
- Treat [docs/ai/rules.md](/docs/ai/rules.md) as the canonical source of project rules.
- This repo ships thin adapters only — never reimplement `@paniolo/scan` rules or thresholds here.

## Agent Routing

Load the matching `agents/*.agent.md` mode when the task fits:

| Task | Agent |
| ---- | ----- |
| Change the scan/remediate flow or trigger across surfaces | [agents/adapter-sync.agent.md](/agents/adapter-sync.agent.md) |

For anything else, use [AGENTS.md](/AGENTS.md) and [docs/ai/rules.md](/docs/ai/rules.md).

## Validation

After editing markdown, dogfood the scanner on this repo:

```bash
npx @paniolo/scan --format json .
```
