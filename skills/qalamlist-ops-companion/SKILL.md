---
name: qalamlist-ops-companion
description: Use when working in the QalamList repo and the user asks to deploy, install prerequisites, check setup, run locally, configure Cloudflare, D1, Turnstile, secrets, Wrangler, diagnose deploy errors, or get QalamList running. Use for phrases like "deploy this", "doctor", "prerequisites", "publish", "setup", or "run locally".
license: MIT
compatibility: Designed for coding agents with shell access (Claude Code, OpenCode, Cursor, Codex, and similar). Expects a local QalamList checkout.
metadata:
  author: qalamio
  version: "1.0.0"
  homepage: https://github.com/qalamio/qalamlist-skills
---

# QalamList Ops Companion

## Core Principle

Help QalamList customers get running without surprising them. Read-only checks are fine; installs, file edits, Cloudflare mutations, remote D1 commands, secrets, and deploys need explicit approval.

This is a customer-facing skill. Explain required tools and Cloudflare resources in one clear sentence or short paragraph before asking to act.

## First Move

1. Confirm the current directory is the QalamList repo by checking for `package.json`, `wrangler.jsonc.example`, `deploy.sh`, and `src/index.ts`.
2. Route the request to one workflow: `prerequisites`, `doctor`, `local`, or `deploy`.
3. If the request is ambiguous, choose the safest workflow:
   - "check", "why failing", "doctor", "validate" -> `doctor`
   - "install", "requirements", "setup tools" -> `prerequisites`
   - "run locally", "dev server", "test locally" -> `local`
   - "deploy", "publish", "production", "Cloudflare" -> `deploy`

## Approval Gate

Before running any command that installs software, edits files, writes secrets, creates Cloudflare resources, applies any remote migration, or deploys, state:

- What is missing or changing.
- Why QalamList needs it.
- The exact command.
- Whether it affects only the local machine or Cloudflare/production.

Then ask for approval and wait. If the tool environment has an approval mechanism, use it. If not, ask in chat.

Read-only diagnostics do not need approval unless they may expose secrets. Never print secret values.

## Troubleshooting Rule

If a Wrangler, Cloudflare, D1, secret, Turnstile, `deploy.sh`, or deployment command fails:

1. Stop the current workflow.
2. Read `TROUBLESHOOTING.md` in the QalamList repo root.
3. Match the error to a troubleshooting section when possible.
4. Tell the customer which section applies and the next safe command or manual action.
5. If no section matches, say that clearly, then continue with read-only diagnosis before proposing a fix.

Do not improvise destructive fixes before checking `TROUBLESHOOTING.md`.

## Workflow: prerequisites

Use for installing or checking required tools.

Check:

- OS and shell: macOS, Linux, Windows native, or WSL.
- Node.js, npm, and `npx`.
- `jq`.
- Wrangler through `npx wrangler --version`.
- Cloudflare auth through `npx wrangler whoami` when the customer is preparing to deploy.

Install strategy:

1. Prefer `mise` for language/runtime components.
2. If `mise` is missing, offer to install `mise` first.
3. If `mise` is not appropriate, use existing version managers in this order when present: `asdf`, `nvm`, `fnm`, `volta`.
4. Fall back to OS package managers: Homebrew, `apt`, `dnf`, `yum`, `pacman`, `zypper`, or `winget`.
5. Give manual instructions only when automation is unavailable or unsafe.

Windows:

- Prefer WSL for deployment because `deploy.sh` and common Cloudflare tooling are Unix-oriented.
- Native Windows may be used for read-only checks and local guidance, but steer production deployment through WSL when available.

Do not install global Wrangler by default. Prefer `npx wrangler` because QalamList already declares Wrangler in `package.json`.

Required explanations:

- Node.js: QalamList uses npm scripts and Wrangler is run through `npx`.
- `jq`: the deployment helper reads JSON from Wrangler and updates production config safely.
- `mise`: it gives a repeatable way to install and switch runtime tools without relying first on OS packages.
- Wrangler: it is Cloudflare's CLI for Workers, D1, secrets, auth, and deploys.

## Workflow: doctor

Use for read-only health checks and diagnosis.

Check and report in three groups: `Ready`, `Needs action`, and `Optional`.

Read-only checks:

- Expected repo files exist: `package.json`, `deploy.sh`, `wrangler.jsonc.example`, `TROUBLESHOOTING.md`.
- Git state when relevant, without reverting or cleaning anything.
- Node/npm/`npx` availability.
- `jq` availability.
- `npx wrangler --version`.
- `npx wrangler whoami`.
- Presence and basic shape of `wrangler.production.jsonc` if deployment has been attempted.
- D1 binding name is `DB` and database name is `qalamlist-db`.
- Production database ID is not `PLACEHOLDER` when `wrangler.production.jsonc` exists.
- Required Turnstile secret names are planned or present: `TURNSTILE_SITE_KEY`, `TURNSTILE_SECRET_KEY`.
- Optional email secrets are planned or present when the customer wants email: `RESEND_API_KEY`, `MAILCHANNELS_API_KEY`, `EMAIL_FROM`.

If a check command errors, apply the Troubleshooting Rule before proposing fixes.

## Workflow: local

Use when the customer wants to run or test QalamList locally.

Explain that local development uses repo scripts, so project dependencies may be needed.

Allowed after approval:

- `npm install` only for local/dev workflows when dependencies are missing.
- `npm run dev` to start the local server.
- `npm test` to run Vitest tests.
- `npm run test:e2e` to run Playwright E2E tests.
- Playwright browser installation when E2E tests require it.

Avoid remote Cloudflare changes in this workflow. If the customer asks to publish or configure production, switch to `deploy`.

Local D1 note:

- Schema is managed by `wrangler d1 migrations`. Run `npm run db:migrate` to apply migrations locally.
- To create a new migration: `npm run db:new` (runs `wrangler d1 migrations create qalamlist-db <name>`).
- To list pending: `npm run db:list`.
- Never edit `migrations/0001_baseline.sql`. Add a new numbered migration via `npm run db:new`.

## Workflow: deploy

Use when the customer asks to deploy, publish, prepare production, configure Cloudflare, set production secrets, create D1, or apply production migrations.

Deploy readiness:

- Ask whether this is the customer's first QalamList deploy. If yes, include the post-deploy setup step before finishing the deploy workflow.
- Run prerequisite checks for Node/npm/`npx`, `jq`, Wrangler, and Cloudflare auth.
- Explain D1: Cloudflare's SQLite database where QalamList stores users, waitlists, and signups.
- Explain Turnstile: bot protection for public signup forms.
- Explain `JWT_SECRET`: signs user sessions; changing it logs users out.
- Explain optional email providers only when the customer wants email.

Deployment paths:

- Recommended guided path: offer `./deploy.sh`, but never run it unless the customer chooses it.
- Manual path: guide the underlying Wrangler commands one at a time.

Production-impacting actions require explicit approval:

- `npx wrangler login`
- D1 database creation
- `npm run db:migrate:remote` (applies migrations to production D1)
- `npx wrangler secret put ...`
- `npx wrangler deploy ...`
- `./deploy.sh`

Migration rule:

- Never edit `migrations/0001_baseline.sql`.
- Create new migrations via `npm run db:new`.
- Apply to production: `npm run db:migrate:remote` (runs `wrangler d1 migrations apply qalamlist-db --remote --config wrangler.production.jsonc`).
- Apply locally: `npm run db:migrate`.
- List pending: `npm run db:list` (checks against production config).
- For a fresh production database, `npm run db:migrate:remote` applies all pending migrations in order. Wrangler tracks applied migrations in the `d1_migrations` table automatically — no manual tracking needed.

Secrets:

- Required for Turnstile: `TURNSTILE_SITE_KEY`, `TURNSTILE_SECRET_KEY`.
- Required session secret: `JWT_SECRET`. `deploy.sh` may generate it in production config if absent.
- Optional email: `RESEND_API_KEY`, `MAILCHANNELS_API_KEY`, `EMAIL_FROM`, or Cloudflare Email binding.
- Never echo entered secret values back to the customer.

If deployment fails, apply the Troubleshooting Rule before trying fixes.

Post-deploy setup:

- For a first deploy, tell the customer to open `https://<their-domain>/setup` after the Worker is deployed and reachable.
- Explain that `/setup` creates the first admin account. It is only available while the users table is empty; after an admin exists, it redirects to `/login`.
- Ask for the customer's production domain or Worker URL if it is not known, then give the exact setup URL.
- Do not ask for or handle the admin password in chat. Tell the customer to enter it directly in the `/setup` page.

## Command Templates

Use the exact command only after adapting it to the detected OS and after approval when needed.

```bash
npx wrangler --version
npx wrangler whoami
```

```bash
./deploy.sh
```

```bash
npm run db:migrate:remote
npm run db:list
npm run db:new
```

```bash
npx wrangler secret put TURNSTILE_SITE_KEY --config wrangler.production.jsonc
npx wrangler secret put TURNSTILE_SECRET_KEY --config wrangler.production.jsonc
```

## Response Shape

Keep responses short and operational:

1. Current workflow.
2. What you checked or found.
3. One-sentence explanation for each missing prerequisite or secret.
4. Exact command proposed next, with local vs production impact.
5. Approval question when required.

## Common Mistakes

| Mistake | Correction |
| --- | --- |
| Running `deploy.sh` immediately | Offer it and wait for customer approval. |
| Treating `npm install` as a deployment prerequisite | Use it for local/dev workflows only unless a chosen helper script needs local dependencies. |
| Installing global Wrangler first | Prefer `npx wrangler`. |
| Ignoring Windows environment | Recommend WSL for production deployment. |
| Fixing Cloudflare errors from memory | Read `TROUBLESHOOTING.md` first. |
| Editing `migrations/0001_baseline.sql` | Create a new migration via `npm run db:new`. |
| Running migrations one by one for fresh setup | Use `npm run db:migrate:remote` — Wrangler applies all pending in order. |
| Ending first deploy after `wrangler deploy` | Ask whether this is the first deploy and direct the customer to `https://<domain>/setup`. |
| Printing secrets | Confirm names only; never echo values. |

## Red Flags

Stop and ask before continuing if you are about to:

- Run a production command because "the user probably wants it".
- Run `./deploy.sh` without a direct yes.
- Run `npm run db:migrate:remote` without a direct yes.
- Write or overwrite `wrangler.production.jsonc`.
- Set or replace `JWT_SECRET`.
- Delete Cloudflare resources.
- Tell the customer a deploy issue is fixed without rerunning or verifying the failed command.
