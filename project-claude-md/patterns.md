# How to write a good project CLAUDE.md

A project CLAUDE.md lives at the root of your repository. Claude reads it at the start of every conversation, so it shapes every interaction. Here is what makes one effective.

## Start with one line of context

Tell Claude what it is looking at. The project type and tech stack, nothing more.

```
# Project: Acme

Next.js 14 app with Postgres (Drizzle ORM) and Tailwind.
```

This single line prevents a surprising number of wrong assumptions. Without it, Claude may guess the framework, use outdated patterns, or reach for the wrong package manager.

## Include common commands

Claude needs to run your dev server, build, test, and lint. If these differ from the defaults, spell them out.

```
## Commands

- Dev: `pnpm dev`
- Build: `pnpm build`
- Test: `pnpm vitest`
- Lint: `pnpm lint --fix`
- Type check: `pnpm tsc --noEmit`
```

Do not assume Claude will read your package.json scripts. It might, but the CLAUDE.md is faster and more reliable.

## Document architecture at a high level

Describe the key directories and how data flows through the system. Stay at the level of "what lives where" rather than documenting every file.

```
## Architecture

- `src/app/` -- Next.js app router pages and layouts
- `src/server/` -- tRPC routers and server-side logic
- `src/db/` -- Drizzle schema and migrations
- `src/lib/` -- Shared utilities, auth helpers, API clients

Data flow: client -> tRPC router -> service layer -> Drizzle -> Postgres
```

This gives Claude a mental map. When you say "add a new API endpoint," it knows to look in `src/server/`, not fumble around the codebase.

## Add conventions that are not obvious from the code

Every project has rules that live in people's heads. Put them in the CLAUDE.md so Claude follows them too.

- Naming conventions (e.g., "all tRPC routers use camelCase, all DB columns use snake_case")
- Error handling patterns (e.g., "throw AppError with a user-facing message, never raw Error")
- File organization rules (e.g., "one component per file, colocate tests next to source")
- Things Claude should never do (e.g., "do not modify the auth middleware without discussion")

These are the instructions that prevent the most rework.

## Include skill routing if you use skills

If you have skills or plugins installed, add a routing table so Claude always picks the right one. Without this, Claude may try to answer directly when a specialized skill would produce better results.

```
## Skill routing

- Bugs, errors, debugging -> invoke investigate
- Ship, deploy, create PR -> invoke ship
- Code review -> invoke review
- Design work -> invoke frontend-design AND ui-ux-pro-max together
```

The routing table is especially valuable for skills with overlapping triggers. Being explicit removes ambiguity.

## Keep it under ~60 lines

Claude reads the CLAUDE.md on every conversation start. A 200-line document wastes context and buries the important bits. If you find yourself writing that much, you probably need to move some of it into code comments or a separate architecture doc that Claude can read on demand.

Aim for roughly 40-60 lines. Enough to orient Claude, not enough to overwhelm it.

## Update it as the project evolves

A stale CLAUDE.md is worse than no CLAUDE.md. If you add a new service, change the test runner, or adopt a new convention, update the file. Treat it like you treat your .env.example -- a living document that stays in sync with reality.

A good habit: whenever you finish a major feature or refactor, skim the CLAUDE.md and ask whether anything needs to change.

## A quick checklist

- [ ] Project type and tech stack in one line
- [ ] Common commands (dev, build, test, lint)
- [ ] High-level architecture (key directories, data flow)
- [ ] Conventions not obvious from the code
- [ ] Skill routing (if applicable)
- [ ] Under 60 lines
- [ ] Reviewed after last major change
