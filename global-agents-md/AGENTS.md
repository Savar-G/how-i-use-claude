# Global Instructions

## Communication
- **Write all prose in ASD-STE100 Simplified Technical English.** Code, code comments, and commit messages are out of scope. One meaning per word. Active voice. One instruction per sentence. Approved vocabulary only, in its literal sense. Keep articles.
- **Lead with the answer.** Stay terse: answer only what I asked.

## Project Instruction Files
- **Every project's instruction file is `AGENTS.md`** - the one file every AI tool reads.
- A `CLAUDE.md` or `CLAUDE.local.md` in the same directory or above it hides `AGENTS.md` from Claude Code. When a project has a `CLAUDE.md`, move its content into `AGENTS.md`, then delete the `CLAUDE.md` or reduce it to the single line `@AGENTS.md`.

## Task-Specific Rules
- **Design:** before UI or UX work, read `~/.claude/docs/design.md`.
- **Parallel agents:** before you create a worktree or spawn an agent, read `~/.claude/docs/parallel-agents.md`.

## Long-Term Memory (Obsidian Vault)
The vault at `/Users/savargupta/Library/Mobile Documents/iCloud~md~obsidian/Documents/Obsidian Vault` (`<vault>` below) is your long-term memory between sessions - who I am, my projects, decisions, people, preferences. Inside the vault, its own instruction file governs. Outside it:
- **Preflight the vault from any directory.** Before you plan or act on a task, run the `ask-vault` preflight. Skip it only for: (a) one-line or single-file mechanical edits; (b) self-contained technical questions that do not touch my projects, people, decisions, or priorities; (c) later turns of a task where you already ran it. If you are unsure, run it.
- **Profile packet:** a session-start hook pushes it. Use it in place of `Memory.md` for basics. If it is missing, run `"<vault>/.claude/scripts/vault-profile"` once.
- **Privacy:** the vault holds sensitive material (work, finance, PII). Surface only what the task needs. Keep vault content inside the task that needs it, and keep confidential non-vault material out of the vault.
