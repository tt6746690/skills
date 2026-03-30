---
name: codex-approval-rules-tuning
description: Use this skill when refining Codex approval behavior, sandbox settings, or allow-rules. It inspects local Codex config, rules, and session history to find recurring approval friction, separate rule issues from sandbox/cache issues, and propose generalizable improvements instead of one-off exceptions.
---

# Codex Approval Rules Tuning

## Overview

Use this skill to tune Codex so routine repo work flows with fewer interruptions while meaningful actions still pause for review. The skill is most useful when the user says approvals feel noisy, wants better defaults in `config.toml`, or wants `default.rules` to reflect actual trust boundaries.

## Workflow

### 1. Inspect the current setup

Read:

- `~/.codex/config.toml`
- `~/.codex/rules/default.rules`

Capture:

- `approval_policy`
- `sandbox_mode`
- network settings
- existing allow-rules
- any stale exact-command exceptions

### 2. Inspect real approval history

Use local Codex artifacts to understand actual friction instead of guessing:

- `~/.codex/session_index.jsonl`
- `~/.codex/sessions/`
- `~/.codex/archived_sessions/`

Look for recurring patterns such as:

- repeated approvals for `git status`, `git diff`, `uv run pytest`, `curl`
- repeated failures tied to worktrees or Git lock files
- repeated failures tied to tool caches such as `~/.cache/uv`
- brittle one-off rules added for exact commit messages, exact paths, or exact URLs

### 3. Separate the root cause

Do not treat every prompt as a rules problem.

- If a command is low-risk and repeatedly useful, it may need an allow-rule.
- If a command fails because it writes outside writable roots, it is mainly a sandbox problem.
- If a tool fails because it writes to a cache in the home directory, it is mainly a cache-path or sandbox problem.

Call this distinction out explicitly in the recommendation.

### 4. Propose policy-shaped changes

Prefer rules that encode trust categories, not past incidents.

Good broad categories:

- read-only repo inspection
- low-risk staging
- normal repo QA commands
- repo-local module entrypoints

Keep meaningful review points unless the user explicitly wants otherwise:

- `git commit`
- `git merge`
- `git push`
- rebases or destructive file operations

### 5. Use repo conventions to generalize rules

If the user wants `uv` rules, inspect the repo `Makefile` or standard scripts first. Use those commands to shape allow-rules around the actual development workflow instead of inventing patterns.

### 6. Present changes before editing

Default output should be:

- what is causing friction now
- which issues are rules issues versus sandbox/cache issues
- concrete rules to add, remove, or broaden
- recommended `config.toml` changes
- any cautions about over-broad trust

Prefer a proposed rule draft before applying changes, unless the user directly asked for edits.

## Heuristics

- Favor `approval_policy = "on-request"` when the user wants fewer prompts but still wants commits and merges gated.
- Favor `sandbox_mode = "workspace-write"` when repo-local editing is fine but global machine access should stay constrained.
- Prefer broad, understandable rules such as `["git", "status"]` or `["uv", "run", "pytest"]`.
- Remove stale exact-message, exact-path, and exact-URL rules when they no longer reflect policy.
- Mention clearly when better rules will not solve the real issue.

## Output Expectations

Give the user a concise recommendation set they can review quickly:

- a short diagnosis
- a concrete add/remove/modify list
- a draft cleaned-up rule set when useful
- a note about whether a new Codex session is needed for changes to take effect
