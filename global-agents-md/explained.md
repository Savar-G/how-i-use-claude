# Global AGENTS.md - Layout and Install

This folder holds the instruction files that load in every project, for Claude Code and for Codex. The design rule is "rebuild from zero": a line stays always-loaded only if it changes agent behaviour on most turns, compared with the model default plus the installed skills, hooks, and settings.

## Files

| File | What it holds | Who reads it | When |
|---|---|---|---|
| `AGENTS.md` | Eight rules: STE prose, lead with the answer, vault preflight, vault privacy, project `AGENTS.md`, and three pointers | Claude Code (through `CLAUDE.md`) and Codex | Every turn |
| `CLAUDE.md` | One line: `@AGENTS.md` | Claude Code | Every turn |
| `CODING_STANDARDS.md` | Worktree isolation, one repo per worktree, cleanup, `.worktreeinclude`, report-where, Claude Code worktree commands | Both | When a pointer fires |
| `docs/parallel-agents.md` | The `wtb` board, pane naming, the `cmux` status enum | Both | When a pointer fires |

## Why AGENTS.md is canonical

`AGENTS.md` is the file that every AI tool reads. Codex reads `~/.codex/AGENTS.md`, so a symlink gives Codex the same file. At user scope, Claude Code reads only `~/.claude/CLAUDE.md`. That file is therefore the one-line import `@AGENTS.md`. Each rule lives in one file, and a change is a one-place edit.

## When each pointer fires

A pointer line in `AGENTS.md` names a file and the condition that makes the agent read it. The agent loads the file only on that condition, so the always-loaded part stays small.

- **"Branches and worktrees"** - before the agent creates a branch or a worktree, it reads `~/.claude/CODING_STANDARDS.md`.
- **"Parallel agents, cmux panes"** - before the agent spawns another agent or opens a `cmux` pane, it reads `~/.claude/docs/parallel-agents.md`.
- **"Design"** - for UI or UX work, the agent uses the `design` skill. The `design` and `impeccable` skills both claim design requests, and this line selects the entry point.
- **"Vault preflight"** - before the agent plans or acts, it runs the `ask-vault` skill. The line adds the skip list and the "if you are unsure, run it" rule.

## What is not here, and why

These files do not repeat what the environment already holds:

- **Skills** (`design`, `ask-vault`, `vault-capture`, `cmux`) carry their own descriptions and steps.
- **The SessionStart hook** pushes the vault profile packet, with its own header.
- **Settings** hold `worktree.baseRef`.

A line that copies one of these is a cache. It goes stale when the source changes. The commit history of this folder lists each deleted rule and the reason.

## Install

```bash
mkdir -p ~/.claude/docs ~/.codex
cp AGENTS.md CLAUDE.md CODING_STANDARDS.md ~/.claude/
cp docs/*.md ~/.claude/docs/
ln -sf ~/.claude/AGENTS.md ~/.codex/AGENTS.md
```

Run the commands from this folder. The pointers use absolute `~/.claude/...` paths, so the files must be at these locations.
