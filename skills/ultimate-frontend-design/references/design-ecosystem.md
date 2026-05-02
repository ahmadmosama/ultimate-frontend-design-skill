# Design Ecosystem: External Skills, Plugins, and Libraries

Curated map of every external Claude Code skill, plugin, library, and tool that complements `ultimate-frontend-design`. Use this to know what to install alongside the skill, and when to route the user to a different tool.

## Companion Claude Code skills and plugins

### pbakaus/impeccable
The vocabulary the design rubric needed. Apache-2.0, 24k+ stars.

- Install: see [`impeccable-integration.md`](impeccable-integration.md) in this folder
- Why: 23 opinionated commands (`/impeccable craft`, `/impeccable critique`, `/impeccable polish`, etc.) plus explicit anti-patterns
- When to use: any task with a clear verb (audit, polish, harden, animate). Defer to ultimate-frontend-design for system-level scoring

### sjsyrek/design-council
11-agent peer-debate plugin for high-stakes design decisions.

- Install: `/plugin marketplace add sjsyrek/claude-plugins` then `/plugin install design-council@sjsyrek`
- Why: convenes 11 specialist agents (architecture, security, performance, UX, testing, docs) that message each other directly. Outputs a one-page decision log
- When to use: non-trivial trade-off decisions where peer-debate beats single-agent reasoning (architecture, schema migrations, API design)

### Anthropic frontend-design
Anthropic's official frontend skill, baseline for all CC design work.

- Install: ships with newer Claude Code releases via the official marketplace
- When to use: defer to it for the newest Anthropic-blessed patterns; ultimate-frontend-design extends it

### ibelick/ui-skills
Motion performance, accessibility audit, CSS techniques.

- Source: github.com/ibelick/ui-skills
- When to use: niche performance and a11y deep dives, motion-budget analysis

### nextlevelbuilder/ui-ux-pro-max
Searchable style and pattern database (already junctioned at `~/.claude/skills/ui-ux-pro-max`).

- When to use: quick lookup of named UX patterns

### wondelai/skills
12 design skills bundle.

- When to use: alternative phrasings; cross-reference with ultimate-frontend-design

### tw93/kami
Claude Code design skill targeted at resumes, decks, and portfolios.

- Source: github.com/tw93/kami
- When to use: artifact is a resume, slide deck, or personal portfolio (not a product UI)

### greensock/gsap-skills
GSAP animation patterns packaged as Claude Code and Cursor skills.

- Source: github.com/greensock/gsap-skills
- When to use: scroll-driven animations, timeline choreography, complex SVG morphs. Pairs with Section 19 (3D & immersive)

### Joshua Stevenson and Kyle Whitrow CC design skill marketplace
The skills now visible as `cro-methodology`, `design-everyday-things`, `design-sprint`, `hooked-ux`, `improve-retention`, `ios-hig-design`, `lean-ux`, `microinteractions`, `refactoring-ui`, `top-design`, `ux-heuristics`, `web-typography`.

- When to use: each one is a sharper specialist than ultimate-frontend-design for its niche. Route by verb:
  - "audit conversion" goes to `cro-methodology`
  - "fix usability" goes to `ux-heuristics`
  - "type pairing" goes to `web-typography`
  - "iOS native screens" goes to `ios-hig-design`
  - "premium agency feel" goes to `top-design`
  - "habit loops" goes to `hooked-ux`
  - "users dropping off" goes to `improve-retention`
  - "rapid prototype week" goes to `design-sprint`

### Already junctioned in `~/.claude/skills/`
These ship via `awesome-claude-skills` and are sibling tools, not replacements:

- `canvas-design`: visual art generation inside artifacts
- `theme-factory`: Tailwind and shadcn theme generation
- `brand-guidelines`: Anthropic brand constraints
- `artifacts-builder`: building polished artifacts
- `image-enhancer`: image quality polish
- `slack-gif-creator`: animated Slack reactions

## DESIGN.md scaffolds

### VoltAgent/awesome-claude-design
68 ready-made `DESIGN.md` files modeled on real sites (Linear, Vercel, Notion, etc.). 1.4k+ stars.

- Source: github.com/VoltAgent/awesome-claude-design
- Workflow: pick a DESIGN.md that matches the feel, drop into Claude Design (Stitch) or attach to Claude Code, get a scaffolded UI kit
- Pairs with: `claude-design-systems` (sibling skill in this same plugin)

## Component libraries

### Aceternity UI
200+ Tailwind and Framer Motion components, blocks, and templates. Marketed for AI/agentic startup landing pages.

- URL: ui.aceternity.com
- When to use: hero sections, decorative backgrounds, fast scaffolding

### Already covered in [`ecosystem-and-libraries.md`](ecosystem-and-libraries.md)
shadcn/ui, Magic UI, 21st.dev, Animate UI, Motion Primitives, Tailwind CSS, Spline, React Three Fiber.

## Animation resources

### animations.dev (Emil Kowalski, Linear)
Animation course that ships a `SKILL.md` for AI agents. Covers easing, spring animations, Framer Motion, Lottie, Mapbox in Remotion.

- URL: animations.dev
- When to use: deep dive on motion craft when stock easing is not enough

### Design Arena (designarena.ai)
Crowdsourced AI design benchmark comparing Claude Sonnet, GLM, GPT-5 across websites, games, UI, logos.

- When to use: pick which model variant to use for a given design task

## Notion and blog references queued for ingestion

Located in `SecondBrain/raw/2026-04-26-claude-code-notion-guides.md`. Read these when the user wants production studio workflows:

1. Claude Code for Designers (band-spike-a6d.notion.site)
2. Claude Code Product Visual Workflow (joeymulcahyguides)
3. 6 Claude Code Plugins (notion.so)
4. AI Agency OS (notion.so)
5. Claude Code Creative Studio Stack (timkoda)
6. The 10k Vibecode Blueprint
7. Build a 10,000 Website With One Line of Code

## Routing table (which tool for which job)

| User intent | Route to |
|-------------|----------|
| "review my UI, score it" | ultimate-frontend-design rubric |
| "polish a section, add motion, harden edges" | `/impeccable <verb>` from `impeccable-integration.md` |
| "set up a project-wide design contract" | `claude-design-systems` (sibling skill) plus VoltAgent DESIGN.md scaffolds |
| "high-stakes design or architecture trade-off" | `sjsyrek/design-council` |
| "build resume, deck, portfolio" | `tw93/kami` |
| "complex GSAP timeline" | `greensock/gsap-skills` plus Section 19 (3D & immersive) |
| "drop-in component for hero" | Aceternity UI |
| "pick which model to use" | Design Arena rankings |
| "fix conversion on a landing page" | `cro-methodology` |
| "iOS native UI" | `ios-hig-design` |
| "Awwwards-grade premium feel" | `top-design` |
| "type pairing question" | `web-typography` |
| "users not coming back" | `hooked-ux` plus `improve-retention` |
| "usability audit" | `ux-heuristics` |

## Personal Style Rules (Ahmad)

These overrides apply automatically when designing for `ahmadmosama@gmail.com`. They sit on top of the rubric. A 9/10 visual that violates them ships as 6/10.

### 1. Palette ban
Never default to navy, black, blue, or purple as the dominant text color. Use warmer or neutral grays for body text. Pick accent colors outside the blue-purple spectrum unless specifically requested.

Source: [feedback_no_dark_font_palette.md](file:///C:/Users/ahmad/.claude/memory/feedback_no_dark_font_palette.md).

### 2. UI copy tone
For any user-facing copy (button labels, error states, empty states, marketing prose, microcopy):

- lowercase starts on sentences and messages, unless proper noun or "I"
- no dashes of any type (em, en, double hyphen). Use commas, colons, periods, or parentheses
- no periods on short messages, only in multi-sentence blocks
- no formal openers (avoid "Hello", "Dear", and the words for "graciously" or "with appreciation" that managers tend to overuse)
- no sign-offs ("Thanks", "Best", "Cheers")
- "please" at the end, not the beginning
- contractions always (it's, don't, can't)
- greetings: "hey [name]," or "hi [name]," never formal
- short and direct, drop unnecessary articles and subjects
- numbered lists for multi-part requests
- max 1 emoji per message, only occasionally

Source: [feedback_writing_tone.md](file:///C:/Users/ahmad/.claude/memory/feedback_writing_tone.md).
