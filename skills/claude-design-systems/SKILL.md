---
name: claude-design-systems
description: 'Use Claude Code itself as a design system. Covers the DESIGN.md pattern (Google Stitch), Jens Heitmann three systems approach, Nano Banana 2 visual workflow, and the Joshua Stevenson and Kyle Whitrow CC design skills. Use whenever the user wants Claude Code to produce consistent, premium UI across an entire project, ship a website end to end with Claude, or set up a reusable design contract that every Claude session inherits.'
license: MIT
metadata:
  author: ahmad (curated from WhatsApp Feb to Apr 2026 intake)
  version: "1.0.0"
  source: "Ultimate Frontend Design Skill / WhatsApp design intake"
---

# Claude Design Systems

A skill that teaches Claude Code how to act as a senior design partner across a whole project, not just inside a single prompt. The big idea: front load a design contract (DESIGN.md), wire up a visual loop (Stitch, Nano Banana 2, MediaPipe sketch to UI), and let Claude reuse those constraints on every subsequent change.

## When to invoke

Use this skill when:

- The user is starting a new website, app, or marketing site and wants a single source of truth for visual style
- The user is bouncing between prompts and getting inconsistent UI from Claude Code
- The user wants to convert a Figma, Stitch, or screenshot into a working build with Claude
- The user mentions DESIGN.md, Stitch, Nano Banana, "design system for Claude Code", "make Claude design like a pro"
- The user wants to spin up an interactive prototype from a sketch, photo, or hand drawing

For pure visual hierarchy, color, motion, and rubric scoring, defer to `ultimate-frontend-design`. This skill sits one layer up and governs how Claude itself participates in the design pipeline.

## Three core systems

### 1. The DESIGN.md contract (Google Stitch concept)

A single markdown file at the repo root that does for design what README.md does for code: defines the visual contract every component must respect. Claude reads it on every turn and applies the rules to any UI it generates or refactors.

A minimal DESIGN.md should declare:

- Brand voice and one paragraph aesthetic statement
- Type system: families, scale, weights, leading
- Color tokens: full palette in OKLCH or hex, semantic mapping (text, surface, accent, danger)
- Spacing and radius scales
- Motion easing and duration tokens
- Component primitives (button variants, input states, card patterns)
- Anti patterns (what this brand never does)

See: [references/design-md-pattern.md](references/design-md-pattern.md)

### 2. Jens Heitmann three systems

Three layered systems that compound:

1. Visual reference system: a `design/inspiration/` folder of screenshots, palettes, and references Claude can read with the Read tool
2. Component memory: a living `components/README.md` that tracks every reusable component, its variants, and its usage site
3. Design QA loop: a checklist in CLAUDE.md that Claude runs against every UI commit (blur test, grayscale test, contrast, mobile, motion purpose, anti slop)

See: [references/jens-heitmann-three-systems.md](references/jens-heitmann-three-systems.md)

### 3. Nano Banana 2 visual workflow

Image first, code second. Generate or curate a high fidelity visual (Nano Banana 2, Stitch, Figma, screenshot of a site you love), drop it into the project as a reference image, then have Claude implement against the image with iterative visual diffs. Pairs with Chrome DevTools MCP and Playwright for take screenshot then compare loops.

See: [references/nano-banana-workflow.md](references/nano-banana-workflow.md)

## End to end workflow

For a new project from zero:

1. Write or generate DESIGN.md (use Stitch, Figma Make, or one of the existing presets in `Desktop/Claude/design-md-presets/`)
2. Drop hero references into `design/inspiration/` (5 to 10 screenshots, palette swatches, type specimens)
3. Add a Design QA section to CLAUDE.md pointing at DESIGN.md and the inspiration folder
4. Build the first screen end to end with Claude using the visual reference loop (Nano Banana 2 or screenshot)
5. After ship, extract reusable primitives into `components/` and update `components/README.md`
6. From this point on, every Claude session inherits the contract automatically

## Companion skills

| Need | Skill |
|------|-------|
| Score a finished UI 0 to 10 | ultimate-frontend-design |
| Pick a typeface or pair fonts | web-typography |
| Build award winning marketing site | top-design |
| Native iOS look and feel | ios-hig-design |
| Audit usability | ux-heuristics |
| Conversion focused redesign | cro-methodology |
| Pick a component library | ultimate-frontend-design / references/component-libraries-and-tools.md |

## References

- [design-md-pattern.md](references/design-md-pattern.md): Full DESIGN.md spec with template, Stitch origin, Joshua Stevenson workflow
- [jens-heitmann-three-systems.md](references/jens-heitmann-three-systems.md): Inspiration folder, component memory, QA loop in CLAUDE.md
- [nano-banana-workflow.md](references/nano-banana-workflow.md): Image to code visual loop with Nano Banana 2 and screenshot diffing
- [visual-prompt-workflows.md](references/visual-prompt-workflows.md): Sketch to UI (MediaPipe, Seb Intel free AI), Kyle Whitrow design skill, Stitch prompts
- [system-design-adjacent.md](references/system-design-adjacent.md): Meta design: Alex Wang 5 layer Claude Code repo architecture, system-design-academy. Why visual design depends on system design.
- [premium-build-playbooks.md](references/premium-build-playbooks.md): 5 Claude Code playbooks for $5K to $10K+ tier websites and creative studios (Tenfold Marc, Joey Mulcahy, Tim Koda, Vibecode Blueprint, Swishy.ai motion).

## Sources (WhatsApp Feb to Apr 2026)

- Jens Heitmann IG reel, 3/5/2026: 3 design systems for Claude Code
- Joshua Stevenson IG reel, 4/6/2026: Claude Code plus Google Stitch DESIGN.md (HIGH priority)
- GitHub Projects IG reel, 4/2/2026: Google Stitch DESIGN.md concept introduction
- Marc Cleroux IG, 3/14/2026: Claude Code plus Nano Banana 2 = $10K websites
- Kyle Whitrow IG reel, 3/15/2026: "Claude Code killed website designers" skill
- Seb Intel IG reel, 3/14/2026: Free AI for clean UI layouts plus prompts
- Notion page, 4/13/2026: Claude Code for Designers
