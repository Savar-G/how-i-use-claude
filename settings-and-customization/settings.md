# Claude Code Settings Configuration

Claude Code reads its settings from `~/.claude/settings.json` (global) or `.claude/settings.json` (per-project). Below is a reference configuration with explanations.

## Full Example

```json
{
  "env": {
    "CLAUDE_CODE_NO_FLICKER": "1"
  },
  "model": "opus",
  "statusLine": {
    "type": "command",
    "command": "bash /path/to/statusline-command.sh"
  },
  "enabledPlugins": {
    "frontend-design@claude-plugins-official": true,
    "superpowers@claude-plugins-official": true,
    "typescript-lsp@claude-plugins-official": true,
    "github@claude-plugins-official": true
  },
  "effortLevel": "high",
  "skipDangerousModePermissionPrompt": true
}
```

## Setting-by-Setting Breakdown

### `env.CLAUDE_CODE_NO_FLICKER`

```json
"env": {
  "CLAUDE_CODE_NO_FLICKER": "1"
}
```

Sets environment variables for the Claude Code process. `CLAUDE_CODE_NO_FLICKER` reduces terminal flickering during streaming output. By default, Claude Code refreshes the terminal rapidly as tokens arrive, which can cause visible flicker on some terminals. Setting this to `"1"` switches to a calmer rendering mode.

**When to use it:** If you notice the terminal flickering or jumping while Claude is generating a response, especially on slower terminals or over SSH.

---

### `model`

```json
"model": "opus"
```

Overrides the default model. By default, Claude Code uses Sonnet (the fastest model in the Claude family). Setting this to `"opus"` uses Claude Opus instead -- the most capable model, which produces more thorough, nuanced, and accurate responses at the cost of higher latency and token usage.

**When to use it:** When you want the best possible output quality and are willing to wait longer and use more tokens. Particularly useful for complex architectural decisions, subtle bugs, and tasks requiring deep reasoning.

---

### `statusLine`

```json
"statusLine": {
  "type": "command",
  "command": "bash /path/to/statusline-command.sh"
}
```

Configures a custom status line at the bottom of the Claude Code interface. When `type` is `"command"`, Claude Code pipes session metadata as JSON to the specified script's stdin on each render tick. The script's stdout becomes the status line content.

The JSON piped to stdin includes fields like:
- `model.display_name` -- the active model
- `context_window.used_percentage` -- how full the context window is
- `cwd` -- current working directory
- `rate_limits.five_hour.used_percentage` -- rate limit consumption

See [custom-statusline.md](./custom-statusline.md) for a full example script.

**When to use it:** When you want at-a-glance visibility into model, context usage, rate limits, or any other session metadata while working.

---

### `enabledPlugins`

```json
"enabledPlugins": {
  "frontend-design@claude-plugins-official": true,
  "superpowers@claude-plugins-official": true,
  "typescript-lsp@claude-plugins-official": true,
  "github@claude-plugins-official": true
}
```

Enables official Claude Code plugins. Each plugin adds domain-specific capabilities:

| Plugin | What it does |
|--------|-------------|
| `frontend-design` | Generates higher-quality UI code. Applies design principles, avoids generic "AI slop" aesthetics, and produces production-grade HTML/CSS/React output. |
| `superpowers` | Advanced workflow skills: test-driven development, implementation planning, systematic debugging, code review, parallel agent dispatch, and more. |
| `typescript-lsp` | Connects Claude to the TypeScript language server. Gives type-aware code intelligence -- real type errors, go-to-definition, and completions instead of guessing from text. |
| `github` | GitHub PR and issue workflows. Create PRs, review diffs, manage issues, and interact with CI checks directly from Claude Code. |

**When to use them:** Enable all four if you do full-stack TypeScript development with GitHub. Cherry-pick if you only need a subset.

---

### `effortLevel`

```json
"effortLevel": "high"
```

Controls how hard Claude thinks before responding. At `"high"`, Claude spends more time reasoning, considers more edge cases, and produces more thorough responses. Lower effort levels (`"low"`, `"medium"`) trade depth for speed.

**When to use it:** Set to `"high"` for complex codebases where you want Claude to catch subtle issues and think through implications. Drop to `"medium"` or `"low"` for quick one-off questions where speed matters more than depth.

---

### `skipDangerousModePermissionPrompt`

```json
"skipDangerousModePermissionPrompt": true
```

Skips the confirmation dialog that appears when entering dangerous mode (the mode that allows Claude to run arbitrary shell commands without per-command approval). Normally, Claude Code shows a warning prompt each time you enable this. Setting this to `true` suppresses that prompt.

**When to use it:** If you are a power user who routinely works in dangerous mode and finds the confirmation redundant. Do not enable this if you share your machine with others or are new to Claude Code -- the prompt exists to prevent accidental execution of destructive commands.
