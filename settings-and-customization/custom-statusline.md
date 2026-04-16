# Custom Statusline Script

A shell script that renders a rich status line for Claude Code, showing model name, context window usage, git branch, working directory, and rate limit consumption -- all color-coded.

## How It Works

Claude Code pipes a JSON object to the script's stdin on every render tick. The script parses that JSON, builds a formatted string with ANSI colors, and prints it to stdout. Claude Code displays the result at the bottom of the terminal.

## The Script

```bash
#!/bin/sh
input=$(cat)

model=$(echo "$input" | jq -r '.model.display_name // "Claude"')
used=$(echo "$input" | jq -r '.context_window.used_percentage // empty')
cwd=$(echo "$input" | jq -r '.cwd // .workspace.current_dir // ""')
five_pct=$(echo "$input" | jq -r '.rate_limits.five_hour.used_percentage // empty')

# Shorten cwd: replace $HOME with ~, then show last 2 path components
home="$HOME"
short_cwd="${cwd#$home}"
if [ "$short_cwd" != "$cwd" ]; then
  short_cwd="~$short_cwd"
fi
components=$(echo "$short_cwd" | tr '/' '\n' | grep -c '.')
if [ "$components" -gt 3 ]; then
  short_cwd="…/$(echo "$short_cwd" | rev | cut -d'/' -f1-2 | rev)"
fi

# Get git branch from cwd
git_branch=""
if [ -n "$cwd" ] && [ -d "$cwd" ]; then
  git_branch=$(git -C "$cwd" --no-optional-locks symbolic-ref --short HEAD 2>/dev/null)
fi

# Build context window usage bar
if [ -n "$used" ]; then
  used_int=$(printf "%.0f" "$used")
  filled=$(( used_int / 10 ))
  empty=$(( 10 - filled ))
  bar=""
  i=0
  while [ $i -lt $filled ]; do bar="${bar}█"; i=$(( i + 1 )); done
  i=0
  while [ $i -lt $empty ]; do bar="${bar}░"; i=$(( i + 1 )); done
  ctx_part=$(printf "[%s] %d%%" "$bar" "$used_int")
else
  ctx_part=""
fi

# Build rate limit part
if [ -n "$five_pct" ]; then
  five_int=$(printf "%.0f" "$five_pct")
  rate_part=$(printf "5h:%d%%" "$five_int")
else
  rate_part=""
fi

# Assemble with ANSI colors
out=""
if [ -n "$short_cwd" ]; then
  out=$(printf "\033[2m%s\033[0m" "$short_cwd")
fi
if [ -n "$git_branch" ]; then
  out=$(printf "%s  \033[0;35m branch: %s\033[0m" "$out" "$git_branch")
fi
out=$(printf "%s  \033[0;36m%s\033[0m" "$out" "$model")
if [ -n "$ctx_part" ]; then
  out=$(printf "%s  %s" "$out" "$ctx_part")
fi
if [ -n "$rate_part" ]; then
  out=$(printf "%s  \033[0;33m%s\033[0m" "$out" "$rate_part")
fi
printf "%s" "$out"
```

## Section-by-Section Explanation

### 1. Reading the Input

```bash
input=$(cat)
```

The script reads all of stdin into a variable. Claude Code sends a single JSON object containing session metadata each time the status line refreshes.

### 2. Extracting Fields with jq

```bash
model=$(echo "$input" | jq -r '.model.display_name // "Claude"')
used=$(echo "$input" | jq -r '.context_window.used_percentage // empty')
cwd=$(echo "$input" | jq -r '.cwd // .workspace.current_dir // ""')
five_pct=$(echo "$input" | jq -r '.rate_limits.five_hour.used_percentage // empty')
```

Four values are pulled from the JSON:

| Field | What it is |
|-------|-----------|
| `model.display_name` | The name of the active model (e.g., "Claude Opus 4"). Falls back to `"Claude"` if missing. |
| `context_window.used_percentage` | How much of the context window has been consumed (0-100). |
| `cwd` | The current working directory, with a fallback to `workspace.current_dir`. |
| `rate_limits.five_hour.used_percentage` | Percentage of the 5-hour rate limit bucket consumed. |

The `// empty` syntax tells jq to output nothing (rather than `null`) when a field is absent, which makes the downstream conditionals cleaner.

### 3. Shortening the Working Directory

```bash
home="$HOME"
short_cwd="${cwd#$home}"
if [ "$short_cwd" != "$cwd" ]; then
  short_cwd="~$short_cwd"
fi
components=$(echo "$short_cwd" | tr '/' '\n' | grep -c '.')
if [ "$components" -gt 3 ]; then
  short_cwd="…/$(echo "$short_cwd" | rev | cut -d'/' -f1-2 | rev)"
fi
```

Long paths waste status bar space. This section:
1. Replaces the `$HOME` prefix with `~`.
2. Counts the number of path components.
3. If there are more than 3 components, truncates to the last 2 and prepends `.../`.

Result: `/Users/you/projects/my-app/src/components` becomes `.../src/components`.

### 4. Getting the Git Branch

```bash
git_branch=""
if [ -n "$cwd" ] && [ -d "$cwd" ]; then
  git_branch=$(git -C "$cwd" --no-optional-locks symbolic-ref --short HEAD 2>/dev/null)
fi
```

Runs `git symbolic-ref` in the working directory to get the current branch name. The `--no-optional-locks` flag prevents git from acquiring locks that could interfere with other git operations running concurrently. Errors are suppressed -- if the directory is not a git repo, `git_branch` stays empty and is omitted from the output.

### 5. Building the Context Window Progress Bar

```bash
if [ -n "$used" ]; then
  used_int=$(printf "%.0f" "$used")
  filled=$(( used_int / 10 ))
  empty=$(( 10 - filled ))
  bar=""
  i=0
  while [ $i -lt $filled ]; do bar="${bar}█"; i=$(( i + 1 )); done
  i=0
  while [ $i -lt $empty ]; do bar="${bar}░"; i=$(( i + 1 )); done
  ctx_part=$(printf "[%s] %d%%" "$bar" "$used_int")
fi
```

Converts the usage percentage into a 10-segment visual bar. Each segment represents 10%. At 40% usage, the output looks like:

```
[████░░░░░░] 40%
```

This gives you an immediate visual sense of how much context remains without reading a number.

### 6. Rate Limit Display

```bash
if [ -n "$five_pct" ]; then
  five_int=$(printf "%.0f" "$five_pct")
  rate_part=$(printf "5h:%d%%" "$five_int")
fi
```

Formats the 5-hour rate limit usage as a simple percentage, e.g., `5h:23%`. Only shown when the data is available.

### 7. Assembling the Output with ANSI Colors

```bash
out=""
if [ -n "$short_cwd" ]; then
  out=$(printf "\033[2m%s\033[0m" "$short_cwd")           # dim white
fi
if [ -n "$git_branch" ]; then
  out=$(printf "%s  \033[0;35m branch: %s\033[0m" "$out" "$git_branch")  # magenta
fi
out=$(printf "%s  \033[0;36m%s\033[0m" "$out" "$model")   # cyan
if [ -n "$ctx_part" ]; then
  out=$(printf "%s  %s" "$out" "$ctx_part")                # default
fi
if [ -n "$rate_part" ]; then
  out=$(printf "%s  \033[0;33m%s\033[0m" "$out" "$rate_part")  # yellow
fi
printf "%s" "$out"
```

Each piece is color-coded with ANSI escape sequences:

| Element | Color | Why |
|---------|-------|-----|
| Working directory | Dim white | Present but not distracting -- it is context, not a call to action. |
| Git branch | Magenta | Stands out from the path so you can spot it quickly. |
| Model name | Cyan | Always visible -- you should always know which model is active. |
| Context bar | Default | The bar shape itself carries the information; color is not needed. |
| Rate limit | Yellow | Yellow signals "pay attention" -- rate limits are a resource you can run out of. |

## Example Output

A typical status line looks like this (colors not shown in markdown):

```
~/projects/my-app  branch: feature/auth  Claude Opus 4  [████░░░░░░] 40%  5h:23%
```

## Why This Is Useful

Two things kill your productivity in Claude Code: running out of context and hitting rate limits. Both are invisible by default -- you only find out when something breaks.

**Context window awareness.** The progress bar tells you how full the context window is. When it hits 70-80%, you know it is time to start a new conversation or summarize before Claude starts forgetting earlier context. Without this, you keep going until responses degrade and you waste time debugging "why Claude forgot X."

**Rate limit awareness.** The `5h:` percentage shows how much of your rate limit budget is consumed. If you are at 80% with 3 hours left in the window, you can pace yourself -- batch questions, reduce back-and-forth, or switch to a lighter model. Without this, you hit the wall mid-task and have to wait.

**Model confirmation.** Seeing the model name prevents the "wait, which model am I using?" question. Useful when you switch between Opus and Sonnet depending on the task.

## Setup

1. Save the script to a file (e.g., `~/.claude/statusline.sh`).
2. Make it executable: `chmod +x ~/.claude/statusline.sh`.
3. Ensure `jq` is installed (`brew install jq` on macOS, `apt install jq` on Debian/Ubuntu).
4. Add the statusLine config to your `settings.json`:

```json
{
  "statusLine": {
    "type": "command",
    "command": "bash ~/.claude/statusline.sh"
  }
}
```
