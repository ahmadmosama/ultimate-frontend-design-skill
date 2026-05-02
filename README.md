# Ultimate Frontend Design Skill

A complete, opinionated design framework for [Claude Code](https://claude.com/claude-code) that transforms any UI task into professional-grade output.

Built from **Refactoring UI, Apple HIG, Material Design 3, WCAG 2.2, Nielsen's heuristics, Don Norman's design principles, Gestalt psychology, cognitive load theory**, and the craft standards of award-winning agencies.

> **v2.2.0 (2026-05-03).** Added a curated `design-ecosystem.md` reference (impeccable, design-council, kami, gsap-skills, awesome-claude-design, animations.dev, Aceternity, Design Arena, Joshua Stevenson and Kyle Whitrow CC marketplace), a full `impeccable-integration.md` guide (23 commands, 7 reference files, anti-patterns, install steps), and a Personal Style Rules section in SKILL.md (palette ban + UI copy tone for ahmadmosama@gmail.com).
>
> **v2.1.0 (2026-04-26).** Added `claude-design-systems` sibling skill (DESIGN.md contract, Jens Heitmann three systems, Nano Banana 2 workflow, premium build playbooks). Added Section 19 (3D & immersive web). Plugin now ships **two skills** in one install.

## Plugin Contents

This plugin installs **two complementary skills**:

| Skill | Invoke | Use For |
|-------|--------|---------|
| `ultimate-frontend-design` | `/ultimate-frontend-design` | Per-screen UI work: building, reviewing, scoring 0-10 |
| `claude-design-systems` | `/claude-design-systems` | Project-wide: set a DESIGN.md contract Claude reuses every turn |

Both are auto-installed when you install the plugin (Option 1 below) or can be copied individually (Options 2-3).

## What It Does

Use on **any** UI task: building screens, reviewing designs, fixing layouts, choosing colors, adding animation, improving usability, or designing components.

**20 core sections + 15 deep-dive reference files** covering:

- Visual hierarchy, typography, spacing
- Color systems, depth, shadows
- Motion and animation
- Usability and accessibility (WCAG 2.2)
- Mobile-native patterns (iOS & Android)
- Components and state management
- Design psychology
- Modern CSS (2024-2026)
- Performance and polish
- Ecosystem libraries and tools

## Scoring

Every output is scored **0-10** with a weighted rubric across:

| Category | Weight |
|----------|--------|
| Visual Hierarchy | 20% |
| Typography & Spacing | 15% |
| Color & Theming | 15% |
| Layout & Responsiveness | 15% |
| Interaction & Motion | 10% |
| Accessibility | 10% |
| Polish & Craft | 10% |
| Innovation | 5% |

**Target: 8.0+** on every output. Below 7.0 triggers automatic revision.

## Installation

### Option 1: Plugin (recommended)

Install as a Claude Code plugin in 2 commands. Run these inside a Claude Code session:

```
/plugin marketplace add ahmadmosama/ultimate-frontend-design-skill
/plugin install ultimate-frontend-design@ahmadmosama-ultimate-frontend-design-skill
```

Once installed, use it with `/ultimate-frontend-design` in any project.

### Option 2: Manual (per-project)

Clone and copy into your project's `.claude/skills/` directory:

```bash
git clone https://github.com/ahmadmosama/ultimate-frontend-design-skill.git
cp -r ultimate-frontend-design-skill/skills/ultimate-frontend-design .claude/skills/
```

Then `/ultimate-frontend-design` is available in that project.

### Option 3: Manual (global)

Install globally so it's available in every project on your machine:

```bash
git clone https://github.com/ahmadmosama/ultimate-frontend-design-skill.git
cp -r ultimate-frontend-design-skill/skills/ultimate-frontend-design ~/.claude/skills/
```

## Usage

Once installed, invoke the skill in any Claude Code session:

```
/ultimate-frontend-design
```

Then describe your UI task. The skill will guide Claude through building, reviewing, or improving your frontend with professional-grade design principles.

## Project Structure

```
.claude-plugin/
  plugin.json              # Plugin manifest
skills/
  ultimate-frontend-design/
    SKILL.md               # Main skill (19 core sections)
    references/
      typography-and-fonts.md
      color-and-theming.md
      layout-and-responsive.md
      components-and-states.md
      animation-and-motion.md
      accessibility.md
      mobile-patterns.md
      design-psychology.md
      advanced-patterns.md
      ecosystem-and-libraries.md
      cutting-edge-css-2024-2026.md
      performance-and-polish.md
```

## Reference Files

| File | Contents |
|------|----------|
| `typography-and-fonts.md` | Type scales, font pairings, fluid typography |
| `color-and-theming.md` | Palette generation, dark mode, semantic tokens |
| `layout-and-responsive.md` | Grid systems, breakpoints, container queries |
| `components-and-states.md` | Component patterns, states, variants |
| `animation-and-motion.md` | Easing curves, transitions, scroll animations |
| `accessibility.md` | WCAG 2.2 compliance, ARIA, focus management |
| `mobile-patterns.md` | iOS HIG, Material Design 3, gestures |
| `design-psychology.md` | Cognitive load, Fitts's law, persuasion patterns |
| `advanced-patterns.md` | Complex layouts, data-heavy UIs, dashboards |
| `ecosystem-and-libraries.md` | Tailwind, shadcn/ui, Framer Motion, etc. |
| `cutting-edge-css-2024-2026.md` | View transitions, anchor positioning, scroll-driven animations |
| `performance-and-polish.md` | Core Web Vitals, image optimization, perceived performance |

## License

MIT
