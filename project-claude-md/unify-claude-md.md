# Example: Unify Project CLAUDE.md

This is the CLAUDE.md used in the Unify project -- a React Native / Expo social app backed by Supabase.

```markdown
# Project: Unify

React Native / Expo project.

## Skills

- Always apply `vercel-react-native-skills` when working on React Native components, screens, navigation, animations, lists, or UI/performance code.
- **Frontend design:** Always invoke BOTH `frontend-design` AND `ui-ux-pro-max` skills together when changing any UI.

## Commits

When committing, only stage files relevant to the change. Skip unrelated modifications (e.g. package-lock.json, build.gradle) unless they're part of the feature.

## Backend

- Supabase edge functions are in `unify-front-end/supabase/functions/`
- Moderator notification emails go to `contact@unifysocial.ca` via Resend API
- Legal documents are hosted on Notion -- URLs are in `unify-front-end/utils/legalUrls.ts`

## Skill routing

When the user's request matches an available skill, ALWAYS invoke it using the Skill
tool as your FIRST action. Do NOT answer directly, do NOT use other tools first.
The skill has specialized workflows that produce better results than ad-hoc answers.

Key routing rules:
- Product ideas, "is this worth building", brainstorming -> invoke office-hours
- Bugs, errors, "why is this broken", 500 errors -> invoke investigate
- Ship, deploy, push, create PR -> invoke ship
- QA, test the site, find bugs -> invoke qa
- Code review, check my diff -> invoke review
- Update docs after shipping -> invoke document-release
- Weekly retro -> invoke retro
- Design system, brand -> invoke design-consultation
- Visual audit, design polish -> invoke design-review
- Architecture review -> invoke plan-eng-review
- Save progress, checkpoint, resume -> invoke checkpoint
- Code quality, health check -> invoke health
```

## Notable patterns

**Skill routing.** The bulk of this CLAUDE.md is a routing table that maps user intents to specific skills. This is deliberate. Without it, Claude might try to answer a debugging question directly instead of invoking the `investigate` skill, which has a structured four-phase workflow that produces better results. The routing table acts as a dispatch layer -- Claude reads it at the start of every conversation and knows which skill to reach for before doing anything else.

**Commit hygiene.** The one-liner about staging only relevant files solves a real problem. React Native projects generate a lot of churn in lockfiles, Gradle configs, and Xcode project files. Without this instruction, Claude will happily stage everything it touched, creating noisy commits that mix a feature change with unrelated build config updates. This keeps the git history clean.

**Backend context.** The backend section gives Claude just enough to be useful -- where edge functions live, which email service is used, where legal URLs are configured -- without dumping the entire architecture. Claude does not need to know your database schema or every API endpoint. It needs to know where to look when a task touches those areas.

**Skill stacking.** The `frontend-design` + `ui-ux-pro-max` pattern appears here just as it does in the global CLAUDE.md. This is intentional redundancy. The global file ensures it applies everywhere; the project file reinforces it for a project where UI quality is especially important. If someone clones the repo without the global config, the project-level instruction still kicks in.
