---
name: adapter-sync
description: Focused mode for changing the scan → present → remediate flow and keeping all three harness surfaces (skill, slash command, Antigravity workflow) in lockstep.
---

# Adapter Sync Agent

Use this mode when the task changes the paniolo-scan flow or its user-facing trigger, so the change
lands consistently across every harness surface instead of drifting.

## Scope

The same flow lives in three places. A change to one almost always belongs in the others, adjusted
for that harness's tools:

| Surface | Path | Harness | Tooling notes |
| ------- | ---- | ------- | ------------- |
| Portable skill | [/.agents/skills/paniolo-scan/SKILL.md](/.agents/skills/paniolo-scan/SKILL.md) | Any skill-capable agent | Must stay self-contained and portable — no links to repo-local paths |
| Slash command | [/.agents/commands/paniolo-scan.md](/.agents/commands/paniolo-scan.md) | Claude Code | May use `Agent`, `AskUserQuestion`, `allowed-tools` |
| Workflow | [/.agents/workflows/paniolo-scan.md](/.agents/workflows/paniolo-scan.md) | Antigravity / Gemini | Uses `invoke_subagent` / `ask_question`; note Windows shell variants |

## Procedure

Follow [llm-wiki/wiki/authoring/harness-rules/rules.md](llm-wiki/wiki/authoring/harness-rules/rules.md). For each flow change:

1. Decide the canonical wording, then apply it to all three surfaces.
2. Preserve the goodwill framing and the `High + Medium` remediation default in each.
3. Never reimplement scanner rules, scores, or thresholds — read them from the JSON report.
4. Update the [README](/README.md) if the trigger, install command, or harness coverage changes.

## Done When

- All three surfaces describe the same scan → present → remediate → re-scan loop.
- `npx @paniolo/scan --format json .` on this repo stays clean of warnings.

## Boundaries

- The scanner is diagnostic and read-only; all writes are the user's, in their working tree.
- Do not rename the `/paniolo-scan` trigger or `npx skills add paniolo-ai/scan` install without
  updating every surface.
