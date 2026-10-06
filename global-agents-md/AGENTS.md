# Global Instructions

## Communication
- **Always write in ASD-STE100 Simplified Technical English.** One word = one meaning. Active voice. One instruction per sentence. Approved vocabulary only - no idiom, metaphor, slang, or invented verbs. Keep articles.
- Lead with the answer. Terse and direct - no preamble, no over-explaining what I didn't ask.

## Code
- **Code:** Before you write code or a commit message, read `~/.claude/CODING_STANDARDS.md`.

## Project Instruction Files
- **Every project's instruction file is `AGENTS.md`** - the one file every AI tool reads.
- A `CLAUDE.md` or `CLAUDE.local.md` in the same directory or above it hides `AGENTS.md` from Claude. When a project has a `CLAUDE.md`, move its content into `AGENTS.md`, then delete the `CLAUDE.md` or reduce it to the single line `@AGENTS.md`.

## UI & UX
**All design work goes through `/design`.** It sequences the tools below and enforces the gates; do not hand-assemble a design workflow out of the individual skills. Rationale + sources: `[[Design Workflow for Claude Code]]` in the vault wiki.

| I say | Run |
|---|---|
| "design/redesign X", "build me a page", "show me options" | `/design brief <what>` → `explore` → `build` → `review` |
| "here are some references" / a FigJam board URL | `/design refs <url\|paths>` |
| **"how do we make this 10x better?"** | `/design 10x [target]` |
| a targeted fix on something already built | `/impeccable <verb>` by name is fine |

- **Reference intake is the highest-leverage step.** I keep reference screens + annotations in FigJam; read the board via the Figma MCP (`get_screenshot` on the `/board/` URL, `get_figjam` for my stickies). My annotations are the constraint, not the screenshots.
- **`10x` never means polish.** It classifies the gap as concept / taste / delight / craft and only craft gaps get refinement verbs. Adding gradient and animation to a taste problem makes it worse.
- Aim for polished, delightful design - attention to interaction patterns and micro-interactions.

## Parallel Work (Git Worktrees + cmux)
Savar runs several agents at once and needs to see what each one does.

- **Isolate, do not branch-switch.** For a task that runs beside other work, use `claude -w <task>` (or the `EnterWorktree` tool mid-session). Never `git checkout` in a shared checkout while another agent is live. Worktrees land at `.claude/worktrees/<task>/` on branch `worktree-<task>`; `worktree.baseRef` is pinned to `fresh`, so each one starts from `origin/<default>`. Use `-w "#1234"` to start from a PR.
- **Give every worktree a visible home.** One cmux surface per worktree, then label it: `cmux new-split right`, `cmux rename-tab <task>`, `cmux workspace status set <status>`. **`status` is a fixed enum - `todo | working | needs-attention | review | done | auto | none`.** A task or worktree name there is an error, not a label (verified 2026-08-12). Note it is workspace-scoped, and `new-split` puts the new surface in the *same* workspace, so it marks the shared workspace rather than one pane. Follow Chen's heuristic - **workspaces across projects, tabs/splits within one project**. Never leave an agent running in an unnamed pane.
- **Report where, not just what.** When you start or finish worktree work, name the worktree path, the branch, and the cmux ref, so Savar can jump to it.
- **Subagents that write files in parallel** get `isolation: "worktree"`. Read-only agents do not.
- **Carry local config** via `.worktreeinclude` at the repo root (`.gitignore` syntax; only gitignored files are copied). Never list `node_modules/`, `.venv/`, or build output - they are slow to copy and hold absolute paths.
- **Clean up.** `-p` runs never auto-remove their worktree. Check `git worktree list` for leftovers before adding more.
- **Board:** `wtb` prints every worktree, its branch, its agent state, and its diff size. Run it before spawning another agent.
- Do **not** build a superrepo (`origins/` + `worktrees/<task>/<repo>`). That pattern is for tasks spanning several repos; Savar's tasks are single-repo. Rationale: `[[Parallel Agents with Git Worktrees]]` in the vault wiki.

## Long-Term Memory (Obsidian Vault)
The vault at `/Users/savargupta/Library/Mobile Documents/iCloud~md~obsidian/Documents/Obsidian Vault` is Claude's **long-term memory between sessions** - who I am, my projects, decisions, people, preferences. Inside the vault its own `CLAUDE.md` governs (full read/write protocol). Outside it:
- **A profile packet is pushed at session start** (SessionStart hook runs `.claude/scripts/vault-profile --hook`; static identity/goals/style from `Memory.md` plus every `Now.md` verified in the last 14 days). Treat it as already-known context: do not re-read `Memory.md` for basics, and still run `vault-brief` for anything project-specific. If the packet is missing, run `"/Users/savargupta/Library/Mobile Documents/iCloud~md~obsidian/Documents/Obsidian Vault/.claude/scripts/vault-profile"` once.
- **Preflight the vault by default, from any directory.** Run `"/Users/savargupta/Library/Mobile Documents/iCloud~md~obsidian/Documents/Obsidian Vault/.claude/scripts/vault-brief" "<my request>"` at the start of a task, before you plan or act. Skip it only for: (a) one-line or single-file mechanical edits; (b) self-contained technical questions that do not touch my projects, people, decisions, or priorities; (c) later turns of a task where you already ran it. If you are unsure, run it. Use the bounded packet silently. Never make me say "check my vault", name a file, or repeat context that is discoverable.
- **Direct questions:** "check my vault" / "ask my vault" / "what do my notes say" runs the global `ask-vault` skill and answers with vault citations.
- **Write back durable findings:** use `vault-capture` for direct corrections, decisions, open actions, changed project state, and explicit lasting preferences. Route to the authoritative note and return one compact receipt that names what changed.
- **Live state:** query live trackers, CRM, email, calendar, or `.base` files directly before you assert volatile state.
- **Visibility:** healthy retrieval is invisible. Surface stale state, canonical conflicts, an unqueried live system, or a genuine vault gap.
- **Access:** use the absolute `vault-brief` command first, then token-safe reads of only the recommended notes.
- **Privacy** - the vault holds sensitive material (work, finance, PII). Surface only what the task needs; never leak it into unrelated contexts, and don't write confidential non-vault material in.
