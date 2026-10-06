# Design

Rationale and sources: `[[Design Workflow for Claude Code]]` in the vault wiki.

## Route through `/design`
Route all design work through `/design`. It sequences the design skills and enforces the gates.

| I say | Run |
|---|---|
| "design/redesign X", "build me a page", "show me options" | `/design brief <what>` → `explore` → `build` → `review` |
| "here are some references" / a FigJam board URL | `/design refs <url\|paths>` |
| **"how do we make this 10x better?"** | `/design 10x [target]` |
| a targeted fix on something already built | `/impeccable <verb>` by name is fine |

## References
- **Reference intake is the highest-leverage step.** I keep reference screens and annotations in FigJam. Read the board through the Figma MCP: `get_screenshot` on the `/board/` URL, `get_figjam` for my stickies.
- My annotations are the constraint. The screenshots only show the context.

## 10x
- **`10x` diagnoses first.** It classifies the gap as concept, taste, delight, or craft, and gives refinement verbs to craft gaps only. Gradient and animation on a taste problem make it worse.

## Quality Bar
- Aim for polished, delightful design, with attention to interaction patterns and micro-interactions.

## Components and Handoff
- Use the shadcn/ui MCP for real components and the Figma MCP for design-to-code.
