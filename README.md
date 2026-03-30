# Shared Agent Skills

Reusable [Agent Skills](https://agentskills.io/) for VS Code Copilot, Codex, and Claude Code.

## Skills

| Skill | Description |
|-------|-------------|
| `using-git-worktrees` | Create isolated git worktrees for feature work |
| `codex-approval-rules-tuning` | Tune Codex approval behavior and allow-rules |

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

Register this repo as a plugin marketplace:

```
/plugin marketplace add tt6746690/skills
```

Then install the plugin:

```
/plugin install dev-workflow-skills@wpq-agent-skills
```

## Repo Structure

```
skills/
├── .claude-plugin/
│   └── marketplace.json        # Claude Code marketplace config
├── skills/
│   ├── using-git-worktrees/
│   │   ├── SKILL.md            # Skill instructions
│   │   └── agents/
│   │       └── openai.yaml     # Codex UI metadata
│   └── codex-approval-rules-tuning/
│       ├── SKILL.md
│       └── agents/
│           └── openai.yaml
└── README.md
```
