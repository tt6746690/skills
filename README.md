# Shared Agent Skills

Reusable [Agent Skills](https://agentskills.io/) for VS Code Copilot, Codex, and Claude Code.

## Skills

| Skill | Description |
|-------|-------------|
| `plan-iteration` | Iterate on complex multi-file implementation plans |
| `using-git-worktrees` | Create isolated git worktrees for feature work |
| `codex-approval-rules-tuning` | Tune Codex approval behavior and allow-rules |
| `paper-review` | Read, critique, and write a peer review of an academic paper |

## Setup

### VS Code Copilot

Add to VS Code user settings (`Cmd+Shift+P` → "Preferences: Open User Settings (JSON)"):

```json
{
  "chat.agentSkillsLocations": {
    "~/github/skills/skills": true
  }
}
```

Skills are discovered automatically in any workspace.

### Codex

Use the built-in `skill-installer` to install from this repo:

```
/skill install from repo tt6746690/skills path skills/using-git-worktrees
/skill install from repo tt6746690/skills path skills/codex-approval-rules-tuning
```

Restart Codex after installing to pick up new skills.

### Claude Code

#### From GitHub

Register this repo as a plugin marketplace:

```
/plugin marketplace add tt6746690/skills
```

#### From a local clone

If you have this repo cloned locally (e.g. `~/github/skills`):

```
/plugin marketplace add ~/github/skills
```

Then install the bundled plugin — this makes **all** skills available at once:

```
/plugin install wpq-skills@wpq-skills
/reload-plugins
```

#### Troubleshooting

**`Plugin "wpq-skills" not found in marketplace "wpq-skills"`**

The marketplace catalog is cached. `/plugin marketplace add` is a no-op if the
marketplace was already registered (even from an older `marketplace.json`), so
it keeps serving the stale catalog. Refresh it:

```
/plugin marketplace update wpq-skills
/plugin install wpq-skills@wpq-skills
```

If `update` still serves stale data, force a clean re-register:

```
/plugin marketplace remove wpq-skills
/plugin marketplace add ~/github/skills
/plugin install wpq-skills@wpq-skills
```

Always run `/plugin marketplace update wpq-skills` after editing
`.claude-plugin/marketplace.json` or adding a new skill.

## Repo Structure

```
skills/
├── .claude-plugin/
│   └── marketplace.json        # Claude Code marketplace config (one bundled plugin)
├── skills/                     # Source of truth for all skills
│   ├── plan-iteration/
│   │   └── SKILL.md
│   ├── using-git-worktrees/
│   │   ├── SKILL.md            # Skill instructions
│   │   └── agents/
│   │       └── openai.yaml     # Codex UI metadata
│   ├── codex-approval-rules-tuning/
│   │   ├── SKILL.md
│   │   └── agents/
│   │       └── openai.yaml
│   └── paper-review/
│       └── SKILL.md
└── README.md
```

> `.claude/` is gitignored. Skills are authored only under `skills/`; for local
> Claude Code dev, install the plugin via `/plugin marketplace add ~/github/skills`
> rather than symlinking into `.claude/skills/`.
