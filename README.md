# QalamList Skills

Agent Skills for **[QalamList](https://qalamlist.qalamio.com/self-host)** — the self-hosted waitlist software for pre-launch signups on Cloudflare Workers.

These skills teach AI coding agents how to install prerequisites, run doctor checks, develop locally, and deploy QalamList safely (Cloudflare D1, Turnstile, Wrangler secrets, and production deploys).

Skills follow the open [Agent Skills](https://agentskills.io/) format (`SKILL.md` + optional resources).

## About QalamList

[QalamList](https://qalamlist.qalamio.com/self-host) is a **self-hosted waitlist manager** for founders launching products:

- **Pre-launch waitlists** — capture interested visitors before launch day
- **Buy once, own forever** — one-time purchase waitlist software, no monthly SaaS fees
- **Self-host on Cloudflare** — Workers + D1 SQLite, your account, your data
- **UTM attribution** — first-touch and last-touch signup tracking
- **Embeddable waitlist widget** — drop into your existing landing page
- **REST API & webhooks** — connect to your tools without vendor lock-in
- **CSV export** — export waitlist signups anytime
- **Bot protection** — Cloudflare Turnstile on public signup forms
- **Source code included** — customize branding, fields, and workflows

Prefer hosted? See [QalamList Cloud](https://qalamlist.qalamio.com/cloud). Full docs: [QalamList manual](https://qalamlist.qalamio.com/manual).

**Keywords:** self-hosted waitlist, pre-launch waitlist software, waitlist manager, Cloudflare Workers waitlist, open waitlist tool, Waitlister alternative, GetWaitlist alternative, no subscription waitlist, own your data waitlist, UTM waitlist attribution, embeddable waitlist form, founder waitlist.

## Available skills

| Skill | Description |
| --- | --- |
| [`qalamlist-ops-companion`](./skills/qalamlist-ops-companion) | Guided prerequisites, doctor checks, local dev, and Cloudflare deploy for self-hosted QalamList. Approval-gated for installs, secrets, remote D1 migrations, and production deploys. |

### qalamlist-ops-companion

Helps customers get **self-hosted QalamList** running on Cloudflare without surprising them.

**Use when** the user is in a QalamList checkout and says things like:

- "deploy this" / "publish" / "set up Cloudflare Workers"
- "doctor" / "why is deploy failing" / "D1 migration error"
- "install prerequisites" / "what do I need for Wrangler"
- "run locally" / "start the waitlist dev server"
- "configure Turnstile" / "set JWT_SECRET" / "apply remote migrations"

**Workflows:** `prerequisites` → `doctor` → `local` → `deploy`

**Safety:** read-only checks are free; installs, secrets, remote D1 migrations, and deploys need an explicit yes.

## Install

### Option A — `npx skills` (recommended)

Install into your coding agent(s):

```bash
# One skill
npx skills add qalamio/qalamlist-skills --skill qalamlist-ops-companion

# All skills from this repo
npx skills add qalamio/qalamlist-skills --skill '*'
```

Useful flags:

```bash
npx skills add qalamio/qalamlist-skills -g          # global (user-level)
npx skills add qalamio/qalamlist-skills -y          # skip prompts
npx skills add qalamio/qalamlist-skills -l          # list skills only
```

### Option B — Claude Code plugin marketplace

```text
/plugin marketplace add qalamio/qalamlist-skills
```

Then browse and install the **qalamlist-skills** plugin, or:

```text
/plugin install qalamlist-skills@qalamlist-skills
```

### Option C — Manual copy / symlink

Clone this repo, then link the skill into a path your agent discovers:

```bash
git clone https://github.com/qalamio/qalamlist-skills.git
cd qalamlist-skills

# Examples (pick the path your agent uses):
mkdir -p ~/.agents/skills
ln -s "$PWD/skills/qalamlist-ops-companion" ~/.agents/skills/qalamlist-ops-companion

mkdir -p ~/.claude/skills
ln -s "$PWD/skills/qalamlist-ops-companion" ~/.claude/skills/qalamlist-ops-companion

mkdir -p ~/.config/opencode/skills
ln -s "$PWD/skills/qalamlist-ops-companion" ~/.config/opencode/skills/qalamlist-ops-companion
```

Project-local install (inside a QalamList checkout):

```bash
mkdir -p .agents/skills
ln -s /path/to/qalamlist-skills/skills/qalamlist-ops-companion .agents/skills/qalamlist-ops-companion
```

### Agent discovery paths

| Agent | Typical skill locations |
| --- | --- |
| OpenCode | `.opencode/skills/`, `.agents/skills/`, `.claude/skills/`, `~/.config/opencode/skills/`, `~/.agents/skills/`, `~/.claude/skills/` |
| Claude Code | `.claude/skills/`, `~/.claude/skills/`, plugins via marketplace |
| Cursor | `.cursor/skills/`, `.agents/skills/`, project/user skill dirs |
| Codex | `~/.agents/skills/`, project `.agents/skills/` |

Each skill is a folder with a `SKILL.md` whose `name` matches the folder name.

## Usage

1. Open a **QalamList** repository checkout in your agent.
2. Ask naturally, for example:
   - "Help me deploy self-hosted QalamList to Cloudflare"
   - "Run doctor on this waitlist install"
   - "Set up prerequisites (Node, jq, Wrangler) on this machine"
   - "Run the waitlist app locally"
   - "Apply D1 migrations and configure Turnstile secrets"
3. The agent should load `qalamlist-ops-companion` and follow its approval gates.

You can also name the skill explicitly: "Use qalamlist-ops-companion to deploy."

## What the ops skill helps with

| Task | Examples |
| --- | --- |
| Prerequisites | Node.js, npm, `jq`, mise, Wrangler via `npx` |
| Doctor / health check | Cloudflare auth, `wrangler.production.jsonc`, D1 binding `DB` / `qalamlist-db` |
| Local development | `npm install`, `npm run dev`, Vitest, Playwright e2e |
| Production deploy | `./deploy.sh`, remote D1 migrations, Turnstile + `JWT_SECRET` |
| Post-deploy | First admin at `https://<your-domain>/setup` |

## Repository layout

```text
qalamlist-skills/
├── README.md
├── LICENSE
├── .claude-plugin/
│   └── marketplace.json      # Claude Code marketplace entry
├── skills.sh.json            # skills.sh grouping metadata
└── skills/
    └── qalamlist-ops-companion/
        ├── SKILL.md          # Agent instructions (required)
        ├── README.md         # Human-facing skill docs
        └── evals/
            └── evals.json    # Behavioral eval prompts
```

## Links

- [QalamList self-host](https://qalamlist.qalamio.com/self-host) — buy once, self-hosted waitlist on Cloudflare
- [QalamList Cloud](https://qalamlist.qalamio.com/cloud) — hosted waitlist option
- [QalamList manual](https://qalamlist.qalamio.com/manual) — setup, analytics, webhooks, API
- [QalamList support](https://qalamlist.qalamio.com/support)
- [Agent Skills specification](https://agentskills.io/specification)
- [skills.sh](https://skills.sh) — skill discovery index

## Contributing

1. Add a new folder under `skills/<skill-name>/` with a valid `SKILL.md`.
2. `name` in frontmatter must match the folder name (`^[a-z0-9]+(-[a-z0-9]+)*$`).
3. Keep `description` specific enough for reliable triggering (what + when).
4. Update this README's skill table and `.claude-plugin/marketplace.json`.
5. Prefer eval prompts under `evals/evals.json` for behavioral skills.

## License

MIT — see [LICENSE](./LICENSE).
