# paniolo-ai/scan — moved

This repository's contents moved to [**paniolo-ai/skills**](https://github.com/paniolo-ai/skills),
the single public registry for all Paniolo skills and agent adapters.

| What you wanted | Where it lives now |
| --------------- | ------------------ |
| `npx skills add` skills (`paniolo-scan`, `paniolo-scan-remediate`, `paniolo-config-init`, `paniolo-config-upgrade`) | `npx skills add paniolo-ai/skills` |
| Claude Code plugin (`/paniolo-scan` and friends) | `/plugin marketplace add paniolo-ai/skills`, then `/plugin install paniolo-scan@paniolo-ai` |
| Antigravity workflows | [`paniolo-ai/skills` `.agents/workflows/`](https://github.com/paniolo-ai/skills/tree/main/.agents/workflows) |

This repo is archived and kept only as a redirect. The scanner itself was never here —
it has always been the `@paniolo/cli` npm package (`npx @paniolo/cli scan .`).
