---
name: tree-me
description: Use this skill when to create a git worktree, checkout a branch in isolation, work on a PR in a separate workspace, manage git worktrees, or when the user mentions "tree-me". Provides guidance on using the tree-me CLI tool for isolated git worktree workspaces.
version: 1.0.0
---

# tree-me — Git Worktree Management

`tree-me` manages git worktrees organized at `$WORKTREE_ROOT/<repo>/<branch>` (default: `~/dev/worktrees`).

## Commands

| Command | Description |
|---|---|
| `tree-me create <branch> [base]` | Create new branch in a worktree (base defaults to main/master) |
| `tree-me checkout <branch>` | Checkout existing branch in a new worktree |
| `tree-me pr <number\|url>` | Checkout a GitHub PR in a worktree (uses `gh`) |
| `tree-me list` | List all worktrees |
| `tree-me remove <branch>` | Remove a worktree |
| `tree-me prune` | Remove stale worktree admin files |

## Key Behaviors

- **Idempotent**: If a worktree for the branch already exists, prints its path instead of erroring.
- **Auto-cd**: If the user has `source <(tree-me shellenv)` in their shell config, the shell auto-`cd`s into the new worktree after `create`/`checkout`/`pr`.
- **PR branches**: `tree-me pr 123` creates a local branch named `pr-123` and checks it out.
- **`co` and `rm` and `ls`** are aliases for `checkout`, `remove`, and `list`.

## Worktree Path Convention

```
~/dev/worktrees/<repo-name>/<branch-name>/
```

Override root with `WORKTREE_ROOT` env var.

## Common Workflows

**Start isolated work on a new feature:**
```bash
tree-me create my-feature        # branches from main
tree-me create my-feature develop # branches from develop
```

**Review a PR without disturbing current work:**
```bash
tree-me pr 456
```

**Clean up after merging:**
```bash
tree-me remove my-feature
```

## When Helping the User

- Prefer `tree-me create` for new work, `tree-me checkout` for existing branches.
- After creating a worktree, remind the user to `cd` into it (or that auto-cd handles it if shellenv is sourced).
- Use `tree-me list` to check existing worktrees before creating a duplicate.
- `tree-me prune` is safe to run when worktrees have been deleted manually.
