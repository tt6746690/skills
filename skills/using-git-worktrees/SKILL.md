---
name: using-git-worktrees
description: >-
  Use when starting feature work that needs isolation, before executing
  implementation plans, or whenever the user mentions branching, parallel work,
  or wanting a clean workspace. Creates isolated git worktrees with systematic
  directory selection and safety verification. Use this skill even if the user
  just says "let's work on this in a branch" or "set up a workspace for this."
---

# Using Git Worktrees

Git worktrees let you work on multiple branches simultaneously — each worktree is an independent checkout sharing the same `.git` directory. Agents can operate in isolated worktrees without disrupting the user's main workspace.

**Announce at start:** "I'm using the using-git-worktrees skill to set up an isolated workspace."

## Creation Flow

### 1. Plan Work and Confirm Worktree Details

Before creating anything:

1. **Brainstorm and plan the task** — survey the codebase, ask clarifying questions, discuss tradeoffs
2. **Include the worktree proposal in your planning response** — present the parent branch, new branch name, and worktree directory
3. **Wait for user confirmation** before proceeding

Present the worktree details like:

```
Git worktree:
  Parent branch: main
  New branch:    feature/<descriptive-name>
  Directory:     .worktrees/feature/<descriptive-name>
```

Once the worktree is created, do all work inside the worktree directory — not in the main worktree.

### 2. Verify `.worktrees/` is Gitignored

```bash
git check-ignore -q .worktrees/ 2>/dev/null
```

If NOT ignored, add `.worktrees/` to `.gitignore` and commit before proceeding.

### 3. Create Worktree

```bash
git worktree add .worktrees/<branch-name> -b <branch-name>
cd .worktrees/<branch-name>
```

If the branch already exists, either reuse it (`git worktree add .worktrees/<name> <existing-branch>`) or pick a different name. If the worktree directory already exists, ask the user whether to reuse or remove it.

### 4. Install Dependencies

Run the repo's dependency install command. Check `Makefile`, `README`, or project instructions (`copilot-instructions.md`, `CLAUDE.md`) for the standard install step. Common patterns:

- `make install`
- `uv sync --all-extras`
- `npm install`

Each worktree gets its own dependency environment for isolation.

### 5. Repo-Specific Post-Checkout Setup

Check project docs for repo-specific setup steps beyond dependency installation. Common examples:

- **Submodule initialization:** `git submodule update --init --recursive`
- **Symlinks:** Shared directories (storage, caches) that should point back to a shared location
- **Environment files:** `.env` or credentials that need copying

Apply all repo-specific steps before running baseline verification.

### 5a. Verify Repo-Specific Files are Gitignored

Verify that symlinks or generated files created during setup are gitignored. This prevents `git add -A` from accidentally staging them.

```bash
git check-ignore -q <symlink-or-generated-path> 2>/dev/null || echo "WARNING: not ignored"
```

If not ignored, use selective `git add <files>` instead of `git add -A` during commits.

### 6. Verify Clean Baseline

Run the project's validation command (check `Makefile` or project docs):

```bash
make all    # or whatever the project uses
```

- **If tests fail:** Report failures and ask whether to proceed or investigate.
- **If tests pass:** Report ready.

### 7. Report

```
Worktree ready at <full-path>
Branch: <branch-name> (from <parent-branch>)
Validation passing
Ready to work on <task-description>
```

## Cleanup / Teardown

When work in a worktree is complete, follow these steps in order.

### 1. Commit Changes

```bash
cd .worktrees/<branch-name>
git add -u && git commit -m "<message>"
```

Use `git add -u` (stages only tracked files). Never use `git add -A` or `git add .` in worktrees — they can stage symlinks or generated files created during post-checkout setup.

Optionally push to remote if collaboration or backup is needed:

```bash
git push -u origin <branch-name>
```

### 2. Pre-Merge Safety Check

Before merging, verify the branch doesn't track paths created during post-checkout setup (symlinks, generated files). Check project docs for the list of repo-specific paths.

```bash
git ls-tree -r --name-only <branch-name> | grep -q '^<path>$' && echo "DANGER" || echo "OK"
```

If a repo-specific path is tracked, remove it before merging:

```bash
git rm --cached <path>
git commit -m "fix: remove accidentally tracked <path>"
```

### 3. Merge into Parent Branch

Switch to the main worktree root (not the feature worktree) and merge:

```bash
cd <main-worktree-root>
git checkout <parent-branch>
git merge <branch-name>
```

If merge conflicts arise, follow the project's conflict resolution guidelines. General approach:

1. Simple conflicts (one side added, other didn't touch): resolve automatically
2. Ambiguous conflicts (both sides modified same region): present the hunks to the user and ask for a decision

### 4. Remove Worktree

Remove the worktree before deleting the branch. If the worktree has submodules, deinit them first:

```bash
# With submodules:
cd .worktrees/<branch-name>
git submodule deinit -f --all
cd <main-worktree-root>
git worktree remove --force .worktrees/<branch-name>

# Without submodules:
git worktree remove .worktrees/<branch-name>
```

### 5. Delete Branch

```bash
git branch -d <branch-name>
```

If the branch was pushed to remote:

```bash
git push origin --delete <branch-name>
```

## Quick Reference

| Situation | Action |
|-----------|--------|
| `.worktrees/` not ignored | Add to `.gitignore` and commit |
| Branch already exists | Reuse it or pick a different name |
| Worktree directory already exists | Ask user whether to reuse or remove |
| Tests fail during baseline | Report failures and ask user |
| Merge conflicts on cleanup | Follow project conflict resolution guidelines |
| Worktree has submodules | `git submodule deinit -f --all` then `git worktree remove --force` |
| Repo-specific symlinks not gitignored | Use `git add -u` instead of `git add -A` |
| Branch tracks repo-specific paths | Remove with `git rm --cached <path>` before merging |

## Common Mistakes

### Staging repo-specific symlinks

- **Problem:** `git add -A` stages symlinks or generated files created during post-checkout setup. Merging these can overwrite real directories with symlinks.
- **Fix:** Always use `git add -u`. Never use `git add -A` or `git add .` in worktrees.

### Merging without checking for tracked symlinks

- **Problem:** Branch accidentally tracks a symlink created during setup. Merge replaces the real directory with the symlink.
- **Fix:** Run pre-merge safety check (`git ls-tree -r --name-only <branch> | grep '<path>'`) before every merge.

### Skipping ignore verification

- **Problem:** Worktree contents or `.worktrees/` directory get tracked, polluting git status.
- **Fix:** Always run `git check-ignore` on `.worktrees/` and on any paths created during setup.

### Skipping user confirmation

- **Problem:** Wrong parent branch or branch name, wasted work.
- **Fix:** Present worktree details in the planning response. Wait for user to confirm.

### Skipping repo-specific setup

- **Problem:** Missing submodules, symlinks, or shared caches cause runtime failures that look like bugs.
- **Fix:** Always check project docs for post-checkout steps.

### Proceeding with failing tests

- **Problem:** Can't distinguish new bugs from pre-existing issues.
- **Fix:** Report failures, get explicit permission to proceed.

### Leaving orphan branches after cleanup

- **Problem:** Branch pollution in local and remote.
- **Fix:** Delete local branch after merge. Delete remote branch if it was pushed.

### Worktree removal fails after submodule deinit

- **Problem:** `git worktree remove` errors because `git submodule deinit` leaves metadata that git considers "dirty."
- **Fix:** Use `git worktree remove --force` after submodule deinit.

## Rules

**Never:**
- Create a worktree without verifying `.worktrees/` is gitignored
- Skip baseline validation
- Proceed with failing tests without asking
- Skip user confirmation of branch names
- Delete a worktree without merging first (unless user explicitly says to discard)
- Use `git add -A` or `git add .` in worktrees
- Merge without running pre-merge safety checks

**Always:**
- Present worktree details in planning response for user confirmation
- Verify `.worktrees/` is in `.gitignore`
- Run repo-specific post-checkout setup from project docs
- Verify repo-specific files (symlinks, generated paths) are gitignored after setup
- Run project validation before starting work
- Do all work inside the worktree directory, not the main worktree
- Use `git add -u` for staging
- Run pre-merge safety check before merging
- Remove worktree before deleting branch during cleanup
