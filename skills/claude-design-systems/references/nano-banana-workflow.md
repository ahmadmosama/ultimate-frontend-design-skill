# Nano Banana 2 Visual Workflow

Source: Marc Cleroux YouTube reel, 3/14/2026 ("Claude Code + Nano Banana 2 = Insane $10,000 Websites").

## What Nano Banana 2 is

Nano Banana 2 is a high quality text to image and image to image model tuned for UI mockups, marketing visuals, and product photography. It produces designs that look closer to a senior designer's output than the typical AI mood board, which makes it usable as a real design source rather than just inspiration.

## The premise

Asking Claude to design something nice is asking it to do two jobs at once: invent an aesthetic and write the implementation. The Nano Banana workflow splits these. A specialized image model invents the visual, then Claude implements against the locked image. Output quality jumps because Claude is no longer guessing what good looks like.

## The loop

```
1. Goal in plain language ("hero section for a fintech, dense data feel, warm monochrome")
2. Generate 3 to 6 variants in Nano Banana 2
3. Pick the strongest, save it to design/inspiration/hero.png
4. Open Claude Code, point at hero.png, ask for HTML/CSS implementation
5. Claude builds, screenshots own output, compares to hero.png
6. Iterate on diffs (spacing, type weight, color drift) until match is close
7. Extract tokens used into DESIGN.md
8. Move to next screen
```

## Why screenshot diffing matters

LLMs cannot see their own output by default. Without a visual feedback loop, Claude is implementing blind. Wire up either:

- Chrome DevTools MCP (`browser_take_screenshot`)
- Playwright MCP (`browser_take_screenshot`)
- Local headless screenshot via `puppeteer` or `playwright` script

After every meaningful change, Claude should screenshot the rendered page, place it next to the reference, and call out specific deltas in plain English ("CTA padding looks 4px too tight, type tracking on the hero is too loose, accent color reads more orange than coral").

## Prompt template for Nano Banana 2

```
Design: [type of screen, e.g. "hero section for a desktop landing page"]

Brand: [one paragraph, concrete adjectives, two reference brands]

Constraints:
- Must include: [hero text, primary CTA, secondary CTA, supporting visual]
- Must avoid: [purple to blue gradients, centered hero with one button, default sans serif]
- Aspect ratio: 16:9 desktop hero, 9:16 mobile
- Contrast: WCAG AA on all body copy

Output: 4 variants exploring composition, color, type system, motion suggestion
```

## Pairing with DESIGN.md

If DESIGN.md already exists, paste its key tokens (palette, type, anti patterns) into the Nano Banana prompt so generated visuals stay on system. If DESIGN.md does not exist, generate visuals first, extract tokens from the chosen visual, then write DESIGN.md from those tokens.

## Pairing with the Ultimate Frontend Design rubric

After implementation, score the output against `ultimate-frontend-design/SKILL.md` (visual hierarchy, typography, spacing, color, motion, accessibility, mobile). Anything under 8 means another iteration. Use the rubric as the stopping criterion, not "looks good to me."

## When not to use this loop

- Internal admin tools and dashboards (functional clarity beats visual differentiation)
- Bug fixes and incremental tweaks to existing screens (overkill, just edit)
- Prototypes meant to be thrown away in 24 hours

Use the loop when the screen will be public facing, when first impression matters, or when the brand needs a moment someone would screenshot.

## Adjacent generators

If Nano Banana 2 is unavailable or pricing changes, the same loop works with:

- Google Stitch (text to UI, free tier)
- Figma Make
- Midjourney v7 with `--style raw --ar 16:9`
- ChatGPT image gen (lower fidelity but free)
- Local diffusion models for batch exploration

The generator is interchangeable. The loop (image first, code second, visual diff) is the moat.
