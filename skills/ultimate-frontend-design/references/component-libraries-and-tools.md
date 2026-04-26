# Component Libraries and Tools

Curated external libraries, generators, and benchmarks worth pulling into a project. All entries vetted from WhatsApp intake (Feb to Apr 2026) plus existing skill knowledge.

## Component libraries

### Aceternity UI
- URL: https://ui.aceternity.com/
- Source: WhatsApp 3/21/2026 (Design/UI category)
- Stack: React, Next.js, Tailwind CSS, Framer Motion
- 200+ copy paste components: hero sections, sliders, 3D effects, animated backgrounds
- Best for: marketing sites, portfolios, product launches that want signature motion without writing it from scratch
- Caveat: heavy on dramatic animation, can feel same-y if used wholesale. Pick 2 or 3 components, not 20.

### 21st.dev
- URL: https://21st.dev/
- Source: WhatsApp 3/11/2026 (AI/General)
- AI Agent Registry plus UI Components plus Developer Tools for the agentic internet
- Magic MCP available (`mcp__magic__21st_magic_component_builder`)
- Best for: agent first products, dashboards for AI workflows, tools that need an existing component vocabulary
- Use the Magic MCP to spawn components by prompt rather than copy paste

### Open Session (OS) Link Portal
- URL: https://opensesh.github.io/OS_our-links/
- Source: WhatsApp 3/21/2026 and 4/13/2026 (AI/General)
- Curated link portal: design systems, AI advice, insights from Fortune 500 design veterans
- Free resources, templates, and tools for designers and PMs
- Best for: research before kicking off a brand or major redesign

## Visual generators

### Google Stitch
- Concept: introduced DESIGN.md (4/2/2026)
- Generates UI mockups from prompt, exports DESIGN.md spec
- Free tier available
- See: `claude-design-systems/references/design-md-pattern.md`

### Nano Banana 2
- See: `claude-design-systems/references/nano-banana-workflow.md`
- High fidelity image generator tuned for marketing visuals and UI mockups
- Strongest pairing with Claude Code for premium website builds

### Design Arena
- URL: https://designarena.ai/
- Source: WhatsApp 3/21/2026 (Design/UI)
- Largest global crowdsourced benchmark for design
- Submit a design, get voted on against others
- Best for: sanity checking your output against the field before shipping

## Motion and effects

### Unicorn Studio
- URL: https://www.unicorn.studio/
- Source: WhatsApp 3/21/2026 (Design/UI)
- No code WebGL effects, motion, and interactivity
- Export effects as embeddable assets
- Best for: hero sections that need ambient motion, landing pages, interactive backgrounds without writing GLSL
- Pairs with: Aceternity for static composition + Unicorn for ambient motion

### HTML breaking free from the DOM (Marc Kaz pattern)
- Source: Marc Kaz IG reel, 4/11/2026 (Design/UI)
- Concept: shaders and WebGL elements composited above DOM, blending creative direction with semantic HTML
- Use case: type that animates in 3D space, image sequences with shader effects, hover states that exit the box model
- Caveat: heavy performance budget. Always measure with the Performance MCP before shipping.

## Skeleton and loading patterns

### Boneyard (auto generated skeleton screens)
- Source: Github Awesome IG reel, 4/4/2026 (Design/UI)
- Premise: skeleton loaders break every time the real layout changes
- Boneyard auto generates skeletons from the live UI structure, eliminating drift
- Pattern is reusable even without the specific tool: snapshot the DOM after first render and emit skeleton CSS, do not maintain a parallel UI

## Free AI for layouts

### Seb Intel free AI layout generator
- Source: Seb Intel IG reel, 3/14/2026 (Design/UI)
- Generates clean UI layouts and exposes the underlying prompts
- Link gated by IG comment "Design"
- See: `claude-design-systems/references/visual-prompt-workflows.md` for the prompt sharing pattern

## When to reach for which

| Need | First choice | Second choice |
|------|--------------|---------------|
| Marketing hero with motion | Aceternity UI + Unicorn Studio | Custom Framer Motion |
| Dashboard or admin tool | 21st.dev components | Tailwind UI |
| AI mockup before code | Google Stitch (DESIGN.md export) | Nano Banana 2 |
| Premium website end to end | Nano Banana 2 + Claude Code loop | Stitch + Claude Code loop |
| Skeleton loaders | Boneyard pattern | Manual `animate-pulse` shells |
| Validate finished design | Design Arena vote | Internal review against rubric |
| Research and inspiration | Open Session Link Portal | Awwwards, Mobbin |

## Anti pattern

Do not adopt all of these at once. Each library has a flavor. Stacking three component libraries makes the site look like a moodboard. Pick one for primitives, one for hero motion, one for ambient effects. Stop there.
