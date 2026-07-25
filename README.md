# QalamList Skills

Agent Skills for [QalamList](https://qalamlist.qalamio.com/) — reusable instructions that help AI coding agents set up, diagnose, and deploy QalamList safely.

Skills follow the open [Agent Skills](https://agentskills.io/) format (`SKILL.md` + optional resources).

## Available skills

| Skill | Description |
| --- | --- |
| [`qalamlist-ops-companion`](./skills/qalamlist-ops-companion) | Guided prerequisites, doctor checks, local dev, and Cloudflare deploy for QalamList. Approval-gated for installs, secrets, remote D1, and production deploys. |

### qalamlist-ops-companion

Helps customers get QalamList running without surprising them.

**Use when** the user is in a QalamList repo and says things like:

- "deploy this" / "publish" / "set up Cloudflare"
- "doctor" / "why is deploy failing"
- "install prerequisites" / "what do I need"
- "run locally" / "start the dev server"

**Workflows:** `prerequisites` → `doctor` → `local` → `deploy`

**Safety:** read-only checks are free; installs, secrets, remote migrations, and deploys need an explicit yes.

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
   - "Help me deploy QalamList"
   - "Run doctor on this install"
   - "Set up prerequisites on this machine"
   - "Run it locally"
3. The agent should load `qalamlist-ops-companion` and follow its approval gates.

You can also name the skill explicitly: "Use qalamlist-ops-companion to deploy."

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

## Related

- [QalamList](https://qalamlist.qalamio.com/) — the product this skill operates on
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
