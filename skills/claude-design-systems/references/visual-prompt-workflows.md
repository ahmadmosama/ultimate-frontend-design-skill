# Visual Prompt Workflows

Sketch to UI, photo to UI, and prompt sharing patterns for getting AI to design under direction. Sources from WhatsApp Feb to Apr 2026 intake.

## 1. Sketch and gesture to UI (Julia, Product Designer)

Source: Julia IG reel, 3/30/2026 ("no one believes I built this in 1 evening, been wanting to try MediaPipe for a while finally did").

The workflow uses MediaPipe (Google's hand and body tracking model) to capture real time hand gestures, then maps them onto an interactive UI. The shipped artifact is impressive in its own right, but the underlying pattern is reusable: any sensor stream (hand position, eye gaze, pose) can drive UI state, not just clicks and taps.

When to apply:

- Demo and portfolio pieces where novelty matters
- Accessibility prototypes (hands free control)
- Spatial UIs and AR overlays
- Interactive installations and trade show booths

How to apply with Claude Code:

1. Define the gesture vocabulary upfront ("pinch closes the modal, swipe right advances the carousel")
2. Generate a MediaPipe wrapper that emits typed events
3. Bind those events into the existing component event handlers
4. Add a fallback for users without camera access (always)

## 2. Free AI for clean UI layouts plus prompts (Seb Intel)

Source: Seb Intel IG reel, 3/14/2026 ("Comment Design and I'll send you the link. Free AI that creates clean UI layouts and gives you the exact prompts to recreate").

The product (link gated by IG comment) generates UI layouts and exposes the underlying prompts that produced them. The teachable pattern: ask generators to emit not just the design but the prompt that produced it, so you can iterate, fork, and reuse.

How to apply universally:

- After every Stitch or Nano Banana generation, ask the model to also output the system prompt it would have used to make this design from scratch
- Save the prompts in `design/prompts/` next to the generated visuals
- Reuse and remix prompts instead of starting from blank every time

## 3. Kyle Whitrow Claude Code design skill

Source: Kyle Whitrow IG reel, 3/15/2026 ("Claude Code just killed website designers. Comment design to get the skill sent to you.").

The skill (link gated by IG comment) is specifically a SKILL.md that gives Claude opinionated design defaults plus example outputs. The teachable pattern: a packaged design skill is portable across projects and survives context resets.

How to apply:

- Put your house design rules in a SKILL.md file (this skill is one example)
- Reference DESIGN.md, the inspiration folder, and the QA checklist from inside the skill
- Keep the skill versioned in git so changes are reviewable

This entire `claude-design-systems` skill bundle plus the existing `ultimate-frontend-design` skill is exactly that pattern, applied to Ahmad's setup.

## 4. Visual prompt iteration with Stitch

Stitch supports back and forth refinement. The loop:

```
1. Initial prompt with brand and constraints (60 to 80 words)
2. Generate 4 variants
3. For the strongest variant: ask Stitch to generate 4 more variations on it
4. After each round, paste the chosen variant back as a reference image with a delta prompt
   ("same composition, but more editorial type, more white space, drop the gradient")
5. Stop when blur test passes and brand feels owned
```

Save every accepted variant into `design/inspiration/` even if it is not the final pick. They serve as the brand library for the next screen.

## 5. Sharing and reusing prompts

Prompts are the new design templates. Treat them like code:

- Version them in git, not in your screenshots folder
- Comment them with intent ("this prompt is for hero sections on data heavy products")
- Maintain a `design/prompts/README.md` that indexes them
- Fork prompts when starting a new project, do not start from blank

## Pattern summary

| Source | Pattern |
|--------|---------|
| Julia, MediaPipe | Sensors as input, not just clicks |
| Seb Intel | Output the prompt with the design |
| Kyle Whitrow | Package house style as a SKILL.md |
| Stitch loop | Iterate by passing chosen variant back as reference |
| Universal | Version prompts like code |

The throughline: design output is no longer a one shot artifact. It is a loop with reusable inputs (prompts), reusable references (inspiration folder), and reusable rules (DESIGN.md and SKILL.md).
