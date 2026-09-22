---
name: adapter-sync
description: Focused mode for changing the audit/remediate flow and keeping each stage's harness surfaces (skill, slash command, Antigravity workflow) in lockstep.
---

# Adapter Sync Agent

Use this mode when the task changes the paniolo-scan flow or its user-facing triggers, so the change
lands consistently across every harness surface instead of drifting.

## Scope

The flow is split into two stages — audit (scan → present → offer) and remediate
(plan → fix → re-scan) — and each stage lives on three surfaces. A change to one almost always
belongs in the same stage's other surfaces, adjusted for that harness's tools:

| Stage | Surface | Path | Tooling notes |
| ----- | ------- | ---- | ------------- |
| Audit | Portable skill | [/.agents/skills/external/paniolo-scan/SKILL.md](/.agents/skills/external/paniolo-scan/SKILL.md) | Vendored copy — edit the `paniolo-scan` page in `paniolo-ai/sharp-shooter-wiki`, re-bundle, `paniolo skills update` here; never edit the copy |
| Audit | Slash command | [/.agents/commands/paniolo-scan.md](/.agents/commands/paniolo-scan.md) | May use `Agent`, `AskUserQuestion`, `allowed-tools` |
| Audit | Workflow | [/.agents/workflows/paniolo-scan.md](/.agents/workflows/paniolo-scan.md) | Uses `invoke_subagent` / `ask_question`; note Windows shell variants |
| Remediate | Portable skill | [/.agents/skills/external/paniolo-scan-remediate/SKILL.md](/.agents/skills/external/paniolo-scan-remediate/SKILL.md) | Vendored copy — same wiki → bundle → update pipeline as the audit skill |
| Remediate | Slash command | [/.agents/commands/paniolo-scan-remediate.md](/.agents/commands/paniolo-scan-remediate.md) | Same tooling as the audit command |
| Remediate | Workflow | [/.agents/workflows/paniolo-scan-remediate.md](/.agents/workflows/paniolo-scan-remediate.md) | Same tooling as the audit workflow |

## Procedure

Follow [AGENTS.md](/AGENTS.md). For each flow change:

1. Decide the canonical wording, then apply it to all of that stage's surfaces.
2. Preserve the goodwill framing and the `High + Medium` remediation default in each surface
   that reaches them.
3. Never reimplement scanner rules, scores, or thresholds — read them from the JSON report.
4. Update the [README](/README.md) if the trigger, install command, or harness coverage changes.

## Done When

- Each stage's surfaces describe the same flow: audit = scan → present → offer; remediate =
  plan → fix → re-scan.
- `npx @paniolo/cli scan --format json .` on this repo stays clean of warnings.

## Boundaries

- The scanner is diagnostic and read-only; all writes are the user's, in their working tree.
- Do not rename the `/paniolo-scan` trigger or `npx skills add paniolo-ai/skills` install without
  updating every surface.
