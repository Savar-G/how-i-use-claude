# Global Instructions

## Obsidian as long-term memory

Savar's persistent memory is the Obsidian vault at:

`/Users/savargupta/Library/Mobile Documents/iCloud~md~obsidian/Documents/Obsidian Vault`

A profile packet (identity, goals, style, and every project `Now.md` verified in the last 14 days) is pushed at session start by the SessionStart hook (`.claude/scripts/vault-profile --hook`). Treat it as known context. If it did not arrive, run `"/Users/savargupta/Library/Mobile Documents/iCloud~md~obsidian/Documents/Obsidian Vault/.claude/scripts/vault-profile"` once.

For substantive work involving Savar's projects, people, preferences, decisions, or priorities:

1. Silently run:

   `"/Users/savargupta/Library/Mobile Documents/iCloud~md~obsidian/Documents/Obsidian Vault/.claude/scripts/vault-brief" "<the user's request>"`

2. Open only the recommended sources needed to act. Size-gate large notes.
3. Query live trackers, CRM, email, calendar, or `.base` files directly before asserting volatile state.
4. Keep healthy retrieval invisible. Surface only stale, conflicting, live-only, or missing context.
5. Persist durable corrections, decisions, changed project state, tasks, and explicit lasting preferences through the `vault-capture` skill.
6. After a vault write, return one compact receipt naming what changed.

Do not make Savar say "check my vault," name a file, or repeat context that is discoverable. Never leak sensitive vault content into unrelated code, commits, messages, or external systems.
