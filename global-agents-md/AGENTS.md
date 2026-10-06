# Global Instructions

- **Write prose in ASD-STE100 Simplified Technical English:** approved words in their approved meaning, active voice, one instruction per sentence, articles kept. Code, code comments, and commit messages are exempt.
- **Lead with the answer.** Answer only what I asked.
- **Vault preflight:** before you plan or act, run the `ask-vault` preflight. Skip it only for mechanical edits, general technical questions, and later turns of one task. If you are unsure, run it.
- **Vault privacy:** vault content is sensitive. Use it only inside the task that needs it.
- **Project instructions live in `AGENTS.md`.** When you create or migrate a project, move its `CLAUDE.md` content into `AGENTS.md`, then delete `CLAUDE.md` or reduce it to `@AGENTS.md`.
- **Code, commits, branches, worktrees:** first read `~/.claude/CODING_STANDARDS.md`.
- **Parallel agents, cmux panes:** first read `~/.claude/docs/parallel-agents.md`.
- **Design:** route all UI and UX work through the `design` skill.
