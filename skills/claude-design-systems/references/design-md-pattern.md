# DESIGN.md Pattern (Google Stitch concept)

## Origin

Google Stitch introduced the DESIGN.md concept publicly in early April 2026 (per Github Projects IG reel, 4/2/2026). The premise: every project already has a README.md that tells humans and LLMs how the code works, so it should also have a DESIGN.md that does the same for the visual system. A single plain markdown file at the root of the repo, containing the entire design language in a form an LLM can hold in context.

Joshua Stevenson (IG reel, 4/6/2026) demonstrated the end to end Claude Code plus Stitch loop and elevated it to the point where the analysis flagged it as the highest priority design upgrade in the WhatsApp intake.

## Why it works

LLMs are stateless across sessions and notoriously inconsistent across prompts on visual choices. A DESIGN.md file converts the unstable house style stored in the model's head into a deterministic external reference. Every Claude turn can re read it, so the Nth screen looks like the 1st.

It also has a side effect: it forces the human to commit to design decisions before code is written, which is the same discipline Refactoring UI teaches.

## Required sections

A working DESIGN.md should cover all eight of the following. Anything missing will cause Claude to invent inconsistent answers in that area.

1. Aesthetic statement
2. Type system
3. Color tokens
4. Spacing and radius
5. Motion
6. Component primitives
7. Layout and density
8. Anti patterns

## Template

```markdown
# DESIGN.md

## 1. Aesthetic statement

One paragraph. Concrete adjectives, no marketing language. Reference at least two real brands or sites whose visual language is close to the target. Example: "Editorial calm. Pen and paper warmth. Closer to Stripe and Linear than to Vercel or Apple. Generous whitespace, restrained motion, monochrome with one signature accent."

## 2. Type system

- Display family: [name, source, weight range, license]
- Body family: [name, source, weight range, license]
- Scale (px): 12, 14, 16, 18, 20, 24, 30, 36, 48, 60, 72
- Line height: hero 0.95, headings 1.15, body 1.6
- Tracking: hero -0.04em, body 0, caps +0.1em
- Max measure: 65ch body, 50ch mobile

## 3. Color tokens

Use OKLCH for the source of truth. Provide hex fallback in comments.

```css
--color-bg:        oklch(98% 0.005 90);
--color-surface:   oklch(100% 0 0);
--color-text:      oklch(20% 0.01 250);
--color-text-mute: oklch(45% 0.01 250);
--color-border:    oklch(90% 0.005 90);
--color-accent:    oklch(60% 0.18 30);
--color-danger:    oklch(55% 0.20 25);
--color-success:   oklch(60% 0.15 150);
```

Dark mode: invert lightness, desaturate accents 10 to 15 percent, never use pure black or pure white.

## 4. Spacing and radius

- Spacing scale (px): 4, 8, 12, 16, 24, 32, 48, 64, 96
- Radius scale (px): 0, 4, 8, 12, 16, 24, 9999
- Default container: max-w 1200px, gutters 24px desktop, 16px mobile
- Section rhythm: alternate dense (24 to 32px internal) with breathing (96 to 128px between)

## 5. Motion

- Easing: ease-out-expo `cubic-bezier(0.16, 1, 0.3, 1)` is the default
- Duration tokens: 80ms (micro), 200ms (state change), 400ms (reveal), 700ms (page)
- Animate transform and opacity only
- Stagger children at 60ms when revealing groups
- Respect prefers-reduced-motion

## 6. Component primitives

For each: variants, states (default, hover, active, focus, disabled, loading, error), and exact tokens used.

- Button: primary (filled accent), secondary (outline), tertiary (text), destructive
- Input: text, email, password, number, textarea, select, toggle, checkbox, radio
- Card: flat, raised, interactive
- Modal, drawer, popover, toast, tooltip
- Nav: top bar, side rail, mobile tab bar
- Data: table row, list row, key value strip, stat card

## 7. Layout and density

- Grid: 12 col desktop, 6 col tablet, 4 col mobile
- Density modes: comfortable (default) and compact (tables, dashboards)
- Where to bleed images vs. inset them
- Sticky behavior rules (header, sidebars, CTAs)

## 8. Anti patterns

What this brand never does. Examples:

- Never purple to blue gradients
- Never centered hero with subtitle and one button
- Never Inter as the display face
- Never pure black or white
- Never default ease or linear
- Never more than two type families
```

## How Claude consumes it

When DESIGN.md exists at repo root, every Claude session that touches UI should:

1. Read DESIGN.md at the start of the turn (Read tool, not Grep)
2. Read CLAUDE.md to confirm the project's design QA rules
3. Read any `design/inspiration/*` images relevant to the screen being built
4. Implement the change strictly within the declared tokens and components
5. Before claiming done, run the Design QA checklist from CLAUDE.md

If a request contradicts DESIGN.md, Claude must surface the contradiction before writing code.

## Joshua Stevenson workflow (4/6/2026)

The IG reel walks through this exact loop:

1. Open Google Stitch
2. Generate a design system spec from a single prompt (brand, vibe, references)
3. Export the system as DESIGN.md
4. Drop DESIGN.md into the repo root
5. Open Claude Code, ask it to scaffold the homepage referencing DESIGN.md
6. Iterate on individual screens, each time pointing Claude back to DESIGN.md
7. Output: an entire site that looks like one product, not a Frankenstein of LLM moods

The major quality jump comes from step 1 to 4: instead of asking Claude to design something nice, you give it a deterministic spec.

## Existing presets (already on disk)

`Desktop/Claude/design-md-presets/` contains three production grade DESIGN.md files Ahmad already saved:

- `linear.app/DESIGN.md`
- `stripe/DESIGN.md`
- `vercel/DESIGN.md`

Use these as starting templates. Copy one, swap brand name and accent color, and you have a working contract in 5 minutes.
