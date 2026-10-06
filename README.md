# How I Use Claude Code

A living document of how I actually use Claude Code day-to-day -- the configurations, workflows, tips, and tricks that make it work for me.

This isn't a tutorial or a best-practices guide. It's a real, opinionated setup that I iterate on constantly. Some of it might be useful to you, some of it might not. Take what works, ignore what doesn't.

---

## Table of Contents

1. [Global AGENTS.md](#global-agentsmd)
2. [Project CLAUDE.md Examples](#project-claudemd-examples)
3. [MCP Servers](#mcp-servers)
4. [Settings and Customization](#settings-and-customization)
5. [Obsidian Second Brain](#obsidian-second-brain)
6. [Tips and Tricks](#tips-and-tricks)

---

## Global AGENTS.md

The global `AGENTS.md` lives at `~/.claude/AGENTS.md` and applies across every project. Claude Code loads it through a one-line `~/.claude/CLAUDE.md` import (`@AGENTS.md`). Codex reads it through a `~/.codex/AGENTS.md` symlink. One file serves both tools.

Mine is eight lines, and each line changes behaviour compared with the model default:

- **Communication** -- write prose in ASD-STE100 Simplified Technical English, and lead with the answer
- **Memory** -- run the Obsidian vault preflight before planning, and keep vault content inside the task that needs it
- **Project files** -- every project's instruction file is `AGENTS.md`
- **Pointers** -- `CODING_STANDARDS.md` for code, commits, branches, and worktrees; `docs/parallel-agents.md` for parallel agents in cmux; the `design` skill for UI and UX work

The key insight: a short global `AGENTS.md` saves you from repeating yourself in every conversation, and it costs context on every turn. Keep a line only if it changes behaviour. Put task-specific rules in files that load only when a pointer fires. See [`global-agents-md/explained.md`](global-agents-md/explained.md) for the layout and the install steps.

## Project CLAUDE.md Examples

Beyond the global config, each project gets its own `CLAUDE.md` tailored to its specific architecture, conventions, and constraints. I have examples from two projects:

- **Unify** -- a React Native / Expo social app with skill routing, commit hygiene rules, and backend context
- **HealthOS** -- a multi-agent health tracking system with 4 specialized CLAUDE.md files (running coach, strength tracker, Oura recovery analyst, dashboard), each with its own data files, output formats, and coaching rules

The project-level file is where you encode the knowledge that a new team member would need their first week. Claude reads it every session, so it never "forgets" your project's conventions. HealthOS takes this further -- each sub-agent has its own CLAUDE.md, creating a multi-agent architecture where each domain expert stays focused.

## MCP Servers

I have 14+ MCP (Model Context Protocol) servers connected, which dramatically extends what Claude Code can do. MCP servers give Claude access to external tools and data sources -- everything from browsing the web to interacting with databases to managing files in ways the base tool can't.

The full list of servers and what each one does is documented here.

## Settings and Customization

Beyond `CLAUDE.md`, there's a lot you can do with:

- **settings.json** -- fine-tuning Claude Code's behavior at the application level
- **Plugins** -- extending functionality with custom plugins
- **Custom statusline** -- because small quality-of-life details matter when you're in the terminal all day

## Obsidian Second Brain

*Coming soon.*

I'm working on a workflow that connects Claude Code with Obsidian as a personal knowledge system. The idea is to use Claude not just for coding but as a thinking partner that has access to my notes, references, and past decisions. More on this as I figure it out.

## Tips and Tricks

A collection of patterns I've found effective:

- **Skill stacking** -- combining multiple skills in a single prompt to get better output (e.g., invoking both frontend design and UX skills together for UI work)
- **Effective prompting** -- how to frame requests so Claude gives you what you actually want
- **When to push back** -- Claude works best when you treat it as a collaborator, not a command executor. Tell it to challenge your assumptions.
- **Breaking down large tasks** -- if the task is too big or vague, don't let Claude barrel through it. Force decomposition first.

---

## Why share this?

Claude Code is genuinely powerful, but most people barely scratch the surface. They use it like a fancy autocomplete instead of the configurable, extensible development partner it can be.

The official docs are fine, but they don't show you what a real setup looks like in practice. Seeing someone's actual configurations -- the global instructions, the project-specific rules, the MCP servers, the workflow patterns -- is worth more than reading about what's theoretically possible.

That's what this repo is. Not a polished product, just a working setup from someone who uses this tool every day and keeps tweaking it.

If something here helps you get more out of Claude Code, that's the whole point.

---

*Maintained by [Savar Gupta](https://github.com/savargupta). This is a living document -- it changes as my workflow evolves.*
