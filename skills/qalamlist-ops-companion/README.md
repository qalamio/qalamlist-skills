# qalamlist-ops-companion

Agent skill that helps customers install prerequisites, run health checks, develop locally, and deploy [QalamList](https://qalamlist.qalamio.com/self-host) on Cloudflare.

## When it triggers

Use this skill when someone is working in a QalamList checkout and asks to:

- Deploy or publish to Cloudflare
- Install prerequisites / set up tools
- Run doctor / health checks
- Run QalamList locally
- Configure D1, Turnstile, secrets, or Wrangler
- Diagnose deploy errors

## Workflows

| Workflow | Purpose |
| --- | --- |
| `prerequisites` | Check and install Node, jq, mise, Wrangler |
| `doctor` | Read-only readiness report (`Ready` / `Needs action` / `Optional`) |
| `local` | `npm install`, `npm run dev`, tests |
| `deploy` | Guided production deploy with explicit approval gates |

## Safety model

- Read-only diagnostics run freely
- Installs, file edits, secrets, remote D1, and deploys require explicit approval
- Secrets are never printed
- Deploy failures route through the QalamList repo's `TROUBLESHOOTING.md` before improvising fixes

## Requirements

- A local QalamList repository checkout
- A coding agent with shell access (Claude Code, OpenCode, Cursor, Codex, etc.)

## Install

From the [qalamlist-skills](https://github.com/qalamio/qalamlist-skills) repo root README, or:

```bash
npx skills add qalamio/qalamlist-skills --skill qalamlist-ops-companion
```

## Evals

Behavioral eval prompts live in [`evals/evals.json`](./evals/evals.json).
