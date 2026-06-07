---
name: using-git-worktrees
description: >-
  Use when starting feature work that needs isolation, before executing
  implementation plans, or whenever the user mentions branching, parallel work,
  or wanting a clean workspace. Creates isolated git worktrees with systematic
  setup and safety checks. Use this skill even if the user just says "let's work
  on this in a branch" or "set up a workspace for this."
---

# Using Git Worktrees

A git worktree is a separate checkout that shares the repo's history, so you can work on a branch in isolation without disturbing the main checkout.

**Announce at start:** "I'm using the using-git-worktrees skill to set up an isolated workspace."

## Setup

**1. Name and create.** Pick a short branch name that describes the work. If you have nothing to go on, make up a memorable three-word name (e.g. `brave-amber-otter`). Don't ask the user to name it. Branch from `main` by default, unless the user says otherwise.

```bash
git worktree add .worktrees/<name> -b <name> main
cd .worktrees/<name>
```

Make sure `.worktrees/` is gitignored — add and commit it if not. If that name is already taken, reuse it or pick another.

**2. Prepare the workspace.** Install dependencies and run any project-specific setup. Check the project's docs (`AGENTS.md`, `CLAUDE.md`, `README`, `Makefile`) for the commands — don't assume. If setup creates a symlink or points at data outside the repo, confirm that path is gitignored so it can never be staged.

## Working in the worktree

- Do all your work and commits inside the worktree, never the main checkout. Before committing, make sure you're in the worktree.
- Stage deliberately with `git add -u` or specific paths, not `git add -A` — which can stage setup-created symlinks or generated files.

## Finishing

1. **Commit** your changes in the worktree.
2. **Validate.** Run the project's checks in the worktree — this is the gate before your change reaches the main branch.
3. **Merge from the main checkout** (not the worktree): switch there, check out the parent branch, and merge your branch in. Resolve simple conflicts yourself; for ambiguous ones (both sides changed the same lines), show the conflict and ask.
4. **Tear down.** Remove the worktree, then delete the branch. If it has submodules, deinitialize them first.
