# Coding Standards

The single home for how code, commits, branches, worktrees, and pull requests are made.

## Worktrees
- **Isolate in a worktree.** Run a task that runs beside other work in its own git worktree. While another agent is live, keep a shared checkout on its current branch: never `git checkout` there.
- **One repo per worktree.** Savar's tasks are single-repo.
- **Clean up:** check `git worktree list` for leftovers before you add a worktree. `claude -p` runs never auto-remove their worktree.
- **Carry local config** with `.worktreeinclude` at the repo root (`.gitignore` syntax; only gitignored files are copied). List config files only: `node_modules/`, `.venv/`, and build output are slow to copy and hold absolute paths.
- **Report where, not just what.** When you start or finish worktree work, name the worktree path, the branch, and the cmux ref, so Savar can go to it.

## In Claude Code
- Isolate with `claude -w <task>`, or with the `EnterWorktree` tool mid-session. `-w "#1234"` starts from a PR.
- Give subagents that write files in parallel `isolation: "worktree"`. Read-only subagents run without it.
