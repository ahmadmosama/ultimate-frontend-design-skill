# Impeccable Integration

`pbakaus/impeccable` is the recommended companion to `ultimate-frontend-design`. It adds an opinionated command surface (`/impeccable <verb>`), 7 deep reference files, and an explicit anti-patterns library.

- License: Apache-2.0
- Source: github.com/pbakaus/impeccable
- Stars: 24k+
- Tagline: "The design language that makes your AI harness better at design."

## Install

### Global (recommended for ahmad)

```bash
git clone https://github.com/pbakaus/impeccable.git
cp -r impeccable/dist/claude-code/.claude/* ~/.claude/
```

### Per-project

```bash
cp -r impeccable/dist/claude-code/.claude your-project/
```

### One-line via skills.sh

```bash
npx skills add https://github.com/pbakaus/impeccable
```

### Visit impeccable.style for prepackaged ZIP bundles

The website at impeccable.style serves ready-to-use bundles for Cursor, Claude Code, and Gemini.

## The 23 commands

All accessed through `/impeccable <verb>`. Use `/impeccable pin <command>` to create a top-level shortcut (for example `pin audit` creates `/audit`).

| Command | What it does | When to invoke |
|---------|--------------|----------------|
| `/impeccable craft` | Full shape-then-build flow with visual iteration | Building a fresh feature from a brief |
| `/impeccable teach` | One-time setup: PRODUCT.md and DESIGN.md | Project onboarding, before any UI work |
| `/impeccable document` | Generate DESIGN.md from existing project code | Inheriting a codebase, capturing current state |
| `/impeccable extract` | Pull components and tokens into the design system | Consolidating drifted styles |
| `/impeccable shape` | Plan UX/UI before writing code | Ambiguous brief, discovery phase |
| `/impeccable critique` | UX review: hierarchy, clarity, emotional resonance | Sanity check before review |
| `/impeccable audit` | Technical quality (a11y, performance, responsive) | Pre-ship gate |
| `/impeccable polish` | Final pass, design system alignment, shipping readiness | Last 10% before ship |
| `/impeccable bolder` | Amplify boring designs | Output feels flat |
| `/impeccable quieter` | Tone down overly bold designs | Output feels chaotic |
| `/impeccable distill` | Strip to essence | Page is too dense |
| `/impeccable harden` | Error handling, i18n, text overflow, edge cases | Pre-prod, internationalization |
| `/impeccable onboard` | First-run flows, empty states, activation | New product, low activation rate |
| `/impeccable animate` | Add purposeful motion | Static design needs life |
| `/impeccable colorize` | Introduce strategic color | Grayscale design ready for color |
| `/impeccable typeset` | Fix font choices, hierarchy, sizing | Type system feels weak |
| `/impeccable layout` | Fix layout, spacing, visual rhythm | Composition feels off |
| `/impeccable delight` | Add moments of joy | Functional but bland |
| `/impeccable overdrive` | Add technically extraordinary effects | Hero, portfolio, award-show pieces |
| `/impeccable clarify` | Improve unclear UX copy | Buttons, errors, empty states unclear |
| `/impeccable adapt` | Adapt for different devices | Responsive overhaul |
| `/impeccable optimize` | Performance improvements | Slow LCP, CLS, INP |
| `/impeccable live` | Visual variant mode in the browser | Side-by-side iteration on real markup |

## The 7 reference files (impeccable's own)

| File | Coverage |
|------|----------|
| typography | Type systems, font pairing, modular scales, OpenType |
| color-and-contrast | OKLCH, tinted neutrals, dark mode, accessibility |
| spatial-design | Spacing systems, grids, visual hierarchy |
| motion-design | Easing curves, staggering, reduced motion |
| interaction-design | Forms, focus states, loading patterns |
| responsive-design | Mobile-first, fluid design, container queries |
| ux-writing | Button labels, error messages, empty states |

These overlap with ultimate-frontend-design's references but with sharper anti-pattern guidance. Use both in tandem.

## Anti-patterns (impeccable's curated bans)

These are HARD rules. Treat as rubric deductions:

- **No overused fonts:** no Arial, no Helvetica default, no Inter (yes, Inter is overused now)
- **No gray text on colored backgrounds:** always re-tint based on the surface
- **No pure black or pure gray:** always tint with a hint of brand or warm hue
- **No card-in-card nesting:** flatten or use a single elevation level per region
- **No bounce or elastic easing:** feels dated. Use custom cubic-beziers or springs with tight settings

These compose with ahmad's personal palette ban (no navy, black, blue, purple as dominant text). When both rules apply, take the stricter one.

## Routing rule

When the user asks for a design verb that maps to an impeccable command, prefer the impeccable command. When the user asks for a system-level review, score, or rubric, defer to ultimate-frontend-design.

Examples:

- "polish this checkout" goes to `/impeccable polish checkout`
- "review my UI and score it" goes to ultimate-frontend-design rubric
- "harden this form" goes to `/impeccable harden`
- "make this page feel more premium" goes to ultimate-frontend-design plus `/impeccable bolder`
- "add motion" goes to `/impeccable animate`
- "design system from scratch" goes to `/impeccable teach` then ultimate-frontend-design for scoring

## Why include impeccable in the ultimate skill

1. Vocabulary: 23 verbs make user intent unambiguous
2. Anti-patterns: ultimate-frontend-design teaches what to do, impeccable teaches what to avoid. Complementary
3. Anthropic-inspired: impeccable explicitly extends Anthropic's `frontend-design` skill, same spirit as this plugin
4. Active: 24k+ stars, ongoing development, maintained by Paul Bakaus (former Google Stadia DX lead)
