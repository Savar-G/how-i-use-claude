# Global CLAUDE.md -- Annotated Walkthrough

This is a section-by-section breakdown of my global `CLAUDE.md` file. For each section, I cover what it does, why it matters, and what goes wrong without it.

---

## Persona

**The instruction:**
> You are an incredibly talented and experienced polyglot with decades of experience in software architecture, system design, development, UI & UX, copywriting, and more.

**What it does:** Sets the baseline identity and confidence level for every interaction. Claude adopts the mindset of a senior, multi-disciplinary engineer rather than a cautious assistant.

**Why it matters:** Without this, Claude tends to hedge. You get a lot of "I'm not sure, but..." and "You might want to consider..." instead of direct, confident answers. The persona raises the bar -- Claude acts like a peer who has seen these problems before, not an intern asking for permission. It also broadens the scope of what Claude considers within its competence. With this line, it won't shy away from giving opinions on architecture, UX, or copy because it sees those as part of its domain.

**What you'd see without it:** More hedging, more qualifiers, more deferring to you on decisions where Claude actually has good judgment. It would also be more likely to stay in a narrow "code monkey" lane instead of thinking holistically about the problem.

---

## Task Planning

**The instructions:**
> - Understand the architecture, identify files to modify, and come up with a Plan. Get the Plan approved before writing a single line of code.
> - When a task is too large or vague, push back -- guide the user to break it down into smaller subtasks.

**What it does:** Forces a planning phase before any implementation begins. Claude must present what it intends to do and wait for your approval.

**Why it matters:** This is one of the most important lines in the file. Without it, Claude will read your request, make assumptions about what you want, and immediately start writing code. Sometimes it guesses right. Often it doesn't. And when it guesses wrong, you've burned tokens and time on work that needs to be thrown away. The "get the Plan approved" instruction creates a checkpoint -- you see the plan, correct any misunderstandings, and only then does Claude start executing. The pushback instruction is equally important. If you give Claude a vague request like "refactor the auth system," it will try to do the whole thing in one shot without this guardrail. The instruction to push back means Claude will ask you to scope the work first, which leads to better outcomes for everyone.

**What you'd see without it:** Claude jumps straight into writing code. It modifies files you didn't want touched. It takes an approach you wouldn't have chosen. You end up doing more work reviewing and undoing than you would have spent just explaining the plan upfront.

---

## Implementation

**The instructions:**
> - Never do a "dummy" implementation. Just implement the thing.
> - When working with any external library you're not 100% sure about, look up the latest syntax via web search.
> - Commit early and often. Break large tasks into logical milestones and commit after each is confirmed.

**What it does:** Three rules that directly address Claude's most common implementation failures.

**Why it matters:**

The "never do a dummy implementation" rule is critical. Claude has a strong tendency to write placeholder code -- functions that return hardcoded values, components with `// TODO: implement` comments, API routes that just log the request and return 200. It will confidently present this as progress. Without this rule, you will frequently find yourself looking at code that compiles and runs but doesn't actually do anything. This instruction tells Claude to build the real thing or not build it at all.

The web search rule prevents hallucinated API calls. Claude's training data has a cutoff, and libraries change their APIs constantly. Without this rule, Claude will write code using function signatures and options that don't exist, or that existed two major versions ago. The code looks plausible, passes a casual review, and then fails at runtime with cryptic errors. Telling Claude to look it up when unsure short-circuits this failure mode.

The commit cadence rule keeps work recoverable. Without it, Claude tends to make all changes in one giant batch. If something goes wrong halfway through, you lose everything. Breaking work into committed milestones means you can always roll back to the last good state.

**What you'd see without it:** Stub implementations presented as complete work. API calls to functions that don't exist in the library version you're using. Monolithic changes that are hard to review and impossible to partially revert.

---

## Problem Solving

**The instruction:**
> - Figure out the root cause instead of throwing random things at the wall.

**What it does:** Tells Claude to diagnose before prescribing.

**Why it matters:** When Claude encounters a bug, its default behavior is to try the most obvious fix first. If that doesn't work, it tries another. And another. Each attempt modifies code, sometimes introducing new issues. This "throw spaghetti at the wall" approach can burn through a lot of iterations without actually solving the problem. The instruction to find the root cause means Claude will spend more time reading error messages, tracing execution paths, and understanding *why* something is broken before it tries to fix it. This almost always leads to a correct fix on the first attempt instead of a series of band-aids.

**What you'd see without it:** Claude applies a surface-level fix. The immediate error goes away, but the underlying issue remains. A week later, the same bug shows up in a different form, or the fix introduces a subtle regression that's harder to track down than the original problem.

---

## UI & UX

**The instructions:**
> - Make designs aesthetically pleasing, easy to use, and delightful. Pay attention to interaction patterns and micro-interactions.
> - Skill stacking: For ANY frontend/UI design work, always invoke BOTH `frontend-design` AND `ui-ux-pro-max` skills together.

**What it does:** Sets a quality bar for visual work and mandates a specific workflow for achieving it.

**Why it matters:** Claude's default UI output is functional but bland. It will build something that works, but it won't look like something a real designer touched. The first instruction raises the aesthetic bar. The second is a workflow optimization: by invoking both the `frontend-design` and `ui-ux-pro-max` skills together, you get a much better result than either produces alone. The frontend-design skill focuses on production-quality code and creative visual choices. The ui-ux-pro-max skill brings design system knowledge, typography pairings, color palettes, and interaction patterns. Together, they cover both the "looks good" and "works well" dimensions of UI work.

**What you'd see without it:** Generic-looking UIs with default spacing, safe color choices, and no personality. The kind of output that screams "an AI made this." The skill stacking pattern is the difference between a prototype and something that actually feels designed.

---

## Think Before Coding

**The instructions:**
> - State assumptions explicitly. If uncertain, ask.
> - If multiple interpretations exist, present them -- don't pick silently.
> - If a simpler approach exists, say so. Push back when warranted.

**What it does:** Gives Claude permission and instruction to slow down, surface ambiguity, and disagree with you.

**Why it matters:** The "push back when warranted" line is doing a lot of work here. By default, Claude is agreeable. If you ask for something that's overly complex, architecturally questionable, or just a bad idea, Claude's instinct is to comply. It will build the thing you asked for even if a better approach exists. This instruction changes that dynamic. Claude will tell you when there's a simpler way, when your approach has trade-offs you might not have considered, or when the requirements are ambiguous enough that it needs clarification before proceeding.

The "state assumptions explicitly" rule catches a different failure mode. Without it, Claude will make reasonable-sounding assumptions silently and build on top of them. You don't find out about these assumptions until you're reviewing the output and realize Claude interpreted your request differently than you intended.

**What you'd see without it:** Claude silently picks one interpretation of an ambiguous request and runs with it. It builds a complex solution when a simple one would do. It never tells you "actually, have you considered just doing X instead?" You miss out on Claude's genuine ability to see simpler paths because it defaults to doing exactly what you asked, even when that's not the best move.

---

## Goal-Driven Execution

**The instruction:**
> For multi-step tasks, state a brief plan with verification steps.

**What it does:** Requires Claude to structure its work as a series of steps, each with a concrete check that confirms the step was completed correctly.

**Why it matters:** This makes Claude's work auditable at every stage. Instead of getting a wall of changes at the end with "done!", you get a structured plan where each step has a verification check. If step 3 fails its check, you know exactly where things went wrong. It also forces Claude to think about how it will *know* each step succeeded, which catches a surprising number of issues at planning time rather than execution time.

The format is intentionally simple -- step, arrow, verification. It's lightweight enough that Claude will actually use it consistently, and structured enough that you can scan it quickly and catch problems early.

**What you'd see without it:** Claude describes work in prose paragraphs that are hard to scan. Verification is ad hoc or absent. When something goes wrong in a multi-step task, you have to dig through the full output to figure out which step failed. The structured format makes debugging the process itself much faster.
