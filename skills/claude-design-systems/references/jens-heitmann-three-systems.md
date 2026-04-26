# Jens Heitmann's Three Design Systems for Claude Code

Source: Jens Heitmann IG reel, 3/5/2026 ("You probably haven't fully unlocked the design power of your Claude Code. Use these 3 systems to up level the design of your output").

The premise: most people use Claude for design with one prompt at a time and get one off output. Jens proposes three layered systems that compound across a project so every new screen inherits the discipline of the previous ones.

## System 1: Visual Reference System

A folder Claude can browse for inspiration before writing any UI.

```
project-root/
  design/
    inspiration/
      hero-references/
        stripe-pricing.png
        linear-changelog.png
        vercel-marketing.png
      type-specimens/
        space-grotesk-display.png
        instrument-serif-headlines.png
      palette-swatches/
        warm-monochrome-with-coral.png
        cool-grays-with-electric-blue.png
      motion-references/
        framer-page-transition.mp4
        figma-cursor-trail.gif
```

Rules:

- 5 to 10 references per screen type, never more (avoids contradictory signals)
- Each reference labeled with what to copy and what to ignore
- Update the folder when the visual direction shifts, do not let it accumulate sediment
- Always use Read tool on inspiration before generating, never assume from memory

CLAUDE.md must state: "Before generating any UI, Read at least one matching reference from `design/inspiration/`. If no relevant reference exists, ask the user to provide one or accept that the output may drift from house style."

## System 2: Component Memory

A `components/README.md` that is the live index of every reusable component, its variants, where it is used, and what tokens it consumes. Updated on every commit that adds or modifies a component.

```markdown
# Components

| Component | File | Variants | States | Used in |
|-----------|------|----------|--------|---------|
| Button | components/Button.tsx | primary, secondary, tertiary, destructive | default, hover, active, focus, disabled, loading | every page |
| StatCard | components/StatCard.tsx | up, down, neutral | default, loading, error | dashboard/, reports/ |
| EmptyState | components/EmptyState.tsx | search, no-data, error | default | tables/, list-views/ |

## Conventions

- All components consume tokens from `design/tokens.css`, never raw values
- All components handle the 8 states from DESIGN.md section 6
- All components ship with a Storybook entry or equivalent
- New variants must be added here in the same commit
```

Rules:

- README is the source of truth, code follows
- Before creating a new component, search README first to avoid duplication
- After each component change, update README in the same commit
- A periodic audit (weekly or on milestone) deletes stale entries

## System 3: Design QA Loop

A checklist Claude is required to run before claiming any UI work is done. Lives in CLAUDE.md so every session inherits it.

```markdown
## Design QA (run before saying done)

For every UI change, verify:

1. Tokens only: no raw colors, spacings, font sizes outside the design system
2. State coverage: default, hover, active, focus, disabled, loading, error, empty
3. Blur test: squint at the screen, primary action stands out, hierarchy reads
4. Grayscale test: temporarily strip color, layout still works
5. Contrast: body text 4.5:1, large text 3:1 minimum
6. Mobile: works at 375px, touch targets 44px minimum, safe areas respected
7. Motion: every animation has a reason, prefers-reduced-motion respected
8. Anti slop: screen does not look like a default AI output (see DESIGN.md anti patterns)
9. Inspiration tie back: this matches at least one reference in design/inspiration/

If any check fails, fix before reporting done.
```

Rules:

- The checklist is enforced by Claude on itself, not by the human
- Failures are surfaced explicitly ("Step 4 failed because the layout collapses without color, fixing now")
- Hooks can automate items 1, 5, 6 with linters and visual regression testing

## Why three and not one

Each system covers a different failure mode:

| System | Solves |
|--------|--------|
| Visual Reference | Aesthetic drift across screens |
| Component Memory | Re inventing primitives, inconsistent tokens |
| Design QA | Shipping incomplete states and slop |

Skip any one and the others cannot compensate. The reference folder cannot enforce contrast, the component memory cannot prevent default ease, the QA checklist cannot tell you what the brand looks like.

## Bootstrap order for an existing project

1. Add the QA checklist to CLAUDE.md (15 minutes)
2. Create `components/README.md` from a one shot scan of the existing components folder (30 minutes with Claude)
3. Spin up `design/inspiration/` and seed with 5 to 10 references the team agrees represent house style (1 hour)

After this, every new Claude session benefits without any extra prompting.

## Pairing with DESIGN.md

The three systems are complementary to DESIGN.md, not a replacement.

- DESIGN.md says what (tokens, primitives, anti patterns)
- Visual Reference says inspiration (what house style feels like)
- Component Memory says where (which files realize the system)
- Design QA says how to verify (the checklist)

Run all four together for the strongest setup.
