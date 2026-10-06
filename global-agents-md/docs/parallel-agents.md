# Parallel Agents

Savar runs several agents at once and needs to see what each one does. Rationale: `[[Parallel Agents with Git Worktrees]]` in the vault wiki.

## Before You Spawn
- **Board:** run `wtb` before you spawn another agent. It prints every worktree, its branch, its agent state, and its diff size.
- **Clean up:** check `git worktree list` for leftovers before you add more worktrees.

## Isolate
- **Isolate in a worktree.** Run a task that runs beside other work in its own git worktree. While another agent is live, keep a shared checkout on its current branch: never `git checkout` there.
- **One repo per worktree.** Savar's tasks are single-repo. The superrepo pattern (`origins/` + `worktrees/<task>/<repo>`) is for tasks that span several repos, so skip it.

## Make It Visible (cmux)
- **Give every worktree a visible home.** Open one cmux surface per worktree, then label it: `cmux new-split right`, `cmux rename-tab <task>`, `cmux workspace status set <status>`. Name every pane that runs an agent.
- **`status` is a fixed enum: `todo | working | needs-attention | review | done | auto | none`.** A task or worktree name there is an error, not a label (verified 2026-08-12). The status is workspace-scoped, and `new-split` puts the new surface in the *same* workspace, so it marks the shared workspace, not one pane.
- **Layout:** follow Chen's heuristic - workspaces across projects, tabs and splits within one project.
- **Report where, not just what.** When you start or finish worktree work, name the worktree path, the branch, and the cmux ref, so Savar can go to it.

## In Claude Code
- Isolate with `claude -w <task>`, or with the `EnterWorktree` tool mid-session. Use `-w "#1234"` to start from a PR.
- Worktrees land at `.claude/worktrees/<task>/` on branch `worktree-<task>`. `worktree.baseRef` is pinned to `fresh`, so each one starts from `origin/<default>`.
- Give subagents that write files in parallel `isolation: "worktree"`. Read-only subagents run without it.
- **Carry local config** with `.worktreeinclude` at the repo root (`.gitignore` syntax; only gitignored files are copied). List config files only: `node_modules/`, `.venv/`, and build output are slow to copy and hold absolute paths.
- `-p` runs never auto-remove their worktree. The `git worktree list` check above finds them.
