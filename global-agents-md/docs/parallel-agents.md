# Parallel Agents

Savar runs several agents at once and needs to see what each one does. Worktree rules live in `~/.claude/CODING_STANDARDS.md`.

- **Board:** run `wtb` before you spawn another agent. It prints every worktree, its branch, its agent state, and its diff size.
- **Name every pane that runs an agent:** `cmux rename-tab <task>`.
- **`cmux workspace status set` takes a fixed enum: `todo | working | needs-attention | review | done | auto | none`.** A task or worktree name there is an error, not a label (verified 2026-08-12). The status is workspace-scoped, and `new-split` puts the new surface in the same workspace, so the status marks the shared workspace, not one pane.
