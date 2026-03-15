# Frontend Design Ecosystem & Component Libraries

## Decision Matrix

| Need | Library |
|------|---------|
| Base components (buttons, forms, dialogs) | shadcn/ui |
| Flashy landing page effects | Aceternity UI (200+ effects) or Magic UI (cleaner, ~50) |
| React component animations | Motion (formerly Framer Motion) |
| Scroll-driven narratives, pinning, timelines | GSAP + ScrollTrigger |
| Smooth scrolling | Lenis |
| Interactive 3D (no-code) | Spline (`@splinetool/react-spline`) |
| Programmatic 3D / custom shaders | React Three Fiber + Drei |
| Simple 3D product viewer / AR | `<model-viewer>` (Google) |
| AI/agent interfaces | 21st.dev |
| Design inspiration | Framer templates |

## Premium Landing Page Stack

```
shadcn/ui (base) + Tailwind CSS (styling)
  + Motion (component interactions, hover/tap/enter)
  + GSAP + ScrollTrigger (scroll-driven sections)
  + Lenis (smooth scroll)
  + Aceternity/Magic UI (hero effects, backgrounds)
  + Spline (interactive 3D elements, optional)
```

---

## shadcn/ui — Component Foundation

Source code registry (not npm package). You own the code. Built on Radix UI + Tailwind CSS.

**Setup:**
```bash
npx shadcn@latest init
npx shadcn@latest add button dialog sheet toast
```

**Key patterns:**
- 60+ base components: Button, Dialog, Sheet, Drawer, Popover, Command, DataTable, Calendar, Toast, Form, Tabs, Accordion, NavigationMenu
- Theming via CSS variables in `globals.css`, not props
- `cn()` utility (clsx + tailwind-merge) for conditional class merging
- Multiple style presets (Default, New York)
- Radix UI primitives = keyboard nav, ARIA, focus management out of the box

```tsx
import { Button } from "@/components/ui/button"
import { cn } from "@/lib/utils"

<Button variant="outline" className={cn("w-full", isActive && "bg-primary")}>
  Click me
</Button>
```

**When to use:** Foundation layer for any React/Next.js project. Start here, layer animation libraries on top. Use instead of Material UI or Ant Design when you want full styling control and smaller bundles.

---

## Aceternity UI — Dramatic Visual Effects

200+ copy-paste animated React components. Tailwind + Framer Motion. Visual impact for marketing sites.

**Installation:**
```bash
npx shadcn@latest add "https://ui.aceternity.com/r/hero-parallax"
```

**Best components by category:**

### Backgrounds
- Aurora Background — animated gradient aurora
- Background Beams — light beam traces
- Wavy Background — organic wave motion
- Vortex Background — swirling particle field
- Shooting Stars, Meteors, Sparkles

### Hero Sections
- Hero Parallax — 3D scroll with rotation
- Macbook Scroll — image emerges from screen on scroll
- Lamp Effect — radial light with text reveal

### Cards
- 3D Card Effect — perspective tilt on hover
- Infinite Moving Cards — auto-scrolling testimonials
- Evervault Card — encrypted text reveal effect
- Focus Cards — blur/focus on hover
- Expandable Cards — click to full detail

### Text Effects
- Text Generate Effect — character-by-character appearance
- Typewriter Effect — classic typing animation
- Flip Words — rotating word display
- Encrypted Text — decode animation

### Scroll Components
- Parallax Scroll — multi-layer depth
- Sticky Scroll Reveal — pinned content reveal
- Container Scroll Animation — 3D perspective scroll

### Interactive
- Following Pointer — cursor-tracking element
- Spotlight — hover spotlight effect
- Lens — magnification on hover
- Animated Tooltip

### 3D
- Globe — interactive 3D earth
- 3D Pin — location marker with depth
- 3D Marquee — rotating 3D text/elements

### Data Display
- Timeline — vertical timeline with animations
- World Map — animated connection lines

**When to use:** Landing pages, marketing sites, portfolios. NOT for internal dashboards (too flashy, adds bundle weight). More variety than Magic UI but less polished code.

---

## Magic UI — Polished Micro-Interactions

~50 animated components. Cleaner code quality than Aceternity. 20k+ GitHub stars.

**Installation:**
```bash
npx shadcn@latest add "https://magicui.design/r/marquee"
```

**Best components:**
- **Marquee** — infinite horizontal/vertical scroll (logos, testimonials)
- **Bento Grid** — animated grid layout for feature showcases
- **Animated Beam** — connection lines between elements (architecture diagrams)
- **Globe** — 3D interactive globe
- **Number Ticker** — counting animation
- **Dock** — macOS-style dock with magnification
- **Particles** — particle background effects
- **Blur Fade** — elements fade in with blur removal
- **Ripple** — click ripple effects
- **Shimmer Button** — gradient shimmer on hover
- **Border Beam** — animated border glow
- **Orbiting Circles** — elements orbiting a center point

**Magic UI vs Aceternity:**
- Magic UI: cleaner code, closer to shadcn patterns, better for SaaS/product sites
- Aceternity: more variety (200+ vs ~50), more dramatic effects, better for portfolios/creative sites
- Both use React + Tailwind + Framer Motion, both compatible with shadcn CLI

---

## Motion (formerly Framer Motion) — React Animation Engine

Most popular React animation library (31k+ GitHub stars). Declarative animations with hybrid JS + native browser engine for 120fps GPU-accelerated output.

**Installation:**
```bash
npm install motion
```

### Basic Animation
```tsx
import { motion } from "motion/react"

<motion.div
  initial={{ opacity: 0, y: 20 }}
  animate={{ opacity: 1, y: 0 }}
  transition={{ duration: 0.5 }}
/>
```

### Spring Physics
```tsx
// Defaults: stiffness: 100, damping: 10, mass: 1
<motion.div
  animate={{ x: 100 }}
  transition={{
    type: "spring",
    stiffness: 200,  // higher = snappier
    damping: 15,     // higher = less bounce
    mass: 1          // higher = more inertia
  }}
/>

// Bounce preset (0 = no bounce, 1 = max)
transition={{ type: "spring", bounce: 0.25 }}
```

**Spring presets by feel:**

| Feel | Stiffness | Damping | Use for |
|------|-----------|---------|---------|
| Snappy | 300 | 20 | Buttons, toggles |
| Gentle | 120 | 14 | Cards, modals |
| Bouncy | 200 | 10 | Playful elements |
| Heavy | 80 | 20 | Large panels |

### Variants (Orchestrated Stagger)
```tsx
const container = {
  hidden: { opacity: 0 },
  show: {
    opacity: 1,
    transition: { staggerChildren: 0.1 }
  }
}

const item = {
  hidden: { opacity: 0, y: 20 },
  show: { opacity: 1, y: 0 }
}

<motion.ul variants={container} initial="hidden" animate="show">
  {items.map(i => <motion.li key={i} variants={item} />)}
</motion.ul>
```

### Gesture Animations
```tsx
<motion.button
  whileHover={{ scale: 1.05 }}
  whileTap={{ scale: 0.95 }}
  whileDrag={{ opacity: 0.8 }}
  whileInView={{ opacity: 1 }}
  viewport={{ once: true, margin: "-100px" }}
/>
```

### Layout Animations
```tsx
// Auto-animate position/size changes
<motion.div layout layoutId="shared-element" />
```

### Exit Animations
```tsx
<AnimatePresence>
  {isVisible && (
    <motion.div
      initial={{ opacity: 0 }}
      animate={{ opacity: 1 }}
      exit={{ opacity: 0 }}
    />
  )}
</AnimatePresence>
```

### Vanilla JS (no React)
```js
import { animate } from "motion"
animate("#box", { x: 100 }, { duration: 0.5 })
```

**When to use:** Default choice for React animations. Replaces CSS transitions for interactive or physics-based motion. Aceternity and Magic UI are built ON TOP of Motion. Use GSAP instead for scroll-driven timelines or non-React projects.

---

## GSAP + ScrollTrigger — Scroll-Driven Narratives

Industry-standard animation library for complex timelines and scroll-driven sequences.

**Installation:**
```bash
npm install gsap   # ScrollTrigger included
```

### Basic Tween
```js
gsap.to(".box", { x: 200, duration: 1, ease: "power2.out" })
gsap.from(".box", { opacity: 0, y: 50 })
gsap.fromTo(".box", { x: 0 }, { x: 200 })
```

### Timeline (Sequenced)
```js
const tl = gsap.timeline({ defaults: { duration: 1, ease: "power2.out" } })

tl.to(".hero-title", { opacity: 1, y: 0 })
  .to(".hero-subtitle", { opacity: 1, y: 0 }, "-=0.5")  // overlap 0.5s
  .to(".hero-cta", { opacity: 1, y: 0 }, "<0.2")         // 0.2s after prev starts
```

**Position parameter shortcuts:**
- `"+=1"` — 1s gap after previous
- `"-=0.5"` — overlap 0.5s with previous
- `"<"` — same start as previous
- `"<0.2"` — 0.2s after previous starts
- `3` — absolute time at 3s

### ScrollTrigger
```js
gsap.to(".parallax-bg", {
  yPercent: -30,
  scrollTrigger: {
    trigger: ".hero-section",
    start: "top top",
    end: "bottom top",
    scrub: 1,           // smooth 1s catch-up to scroll
    pin: true,          // pin element during scroll range
    snap: 1 / 5,        // snap to 5 sections
    toggleActions: "play pause resume reverse"
  }
})
```

### Stagger
```js
gsap.from(".card", {
  opacity: 0,
  y: 50,
  stagger: { each: 0.15, from: "start" },
  scrollTrigger: { trigger: ".cards-grid", start: "top 80%" }
})
```

### Nested Timelines (Modular)
```js
function heroAnimation() {
  return gsap.timeline()
    .from(".hero-title", { opacity: 0, y: 30 })
    .from(".hero-desc", { opacity: 0, y: 20 }, "-=0.3")
}

const master = gsap.timeline()
master.add(heroAnimation())
      .add(featuresAnimation(), "+=0.5")
```

**GSAP vs Motion:**
- GSAP: complex scroll-driven sequences, pinning, timeline orchestration, non-React
- Motion: React-centric, gesture-driven, layout animations, simpler enter/exit
- They coexist well: Motion for component-level, GSAP for page-level scroll narratives

---

## Lenis — Smooth Scrolling

Lightweight (<4kb) smooth scroll library preserving native browser APIs. Used by GTA VI, Microsoft Design, Shopify.

**Installation:**
```bash
npm install lenis
```

### Basic Setup
```js
import Lenis from 'lenis'
import 'lenis/dist/lenis.css'

const lenis = new Lenis({
  lerp: 0.1,           // 0.05 = very smooth, 0.1 = default, 1 = instant
  duration: 1.2,
  smoothWheel: true,
  syncTouch: false,     // leave false for mobile (use native touch scroll)
  autoRaf: true,        // simplest setup
})
```

### GSAP ScrollTrigger Integration (Essential Pattern)
```js
const lenis = new Lenis()

lenis.on('scroll', ScrollTrigger.update)

gsap.ticker.add((time) => {
  lenis.raf(time * 1000)
})
gsap.ticker.lagSmoothing(0)
```

### Scroll to Element
```js
lenis.scrollTo('#section', {
  offset: -100,
  duration: 2,
  easing: (t) => Math.min(1, 1.001 - Math.pow(2, -10 * t))
})
```

### Prevent Smooth Scroll on Elements
```html
<div data-lenis-prevent>Scrolls natively (modals, dropdowns)</div>
```

**When to use:** Any site with GSAP ScrollTrigger. Portfolio sites, agency sites, editorial pages. NOT needed for SaaS dashboards or typical apps. Replaces Locomotive Scroll (deprecated) and GSAP ScrollSmoother (paid).

---

## 21st.dev — Component Registry + AI Agent UI

Y Combinator-backed platform with 500+ React UI components and an MCP server for AI agent integration.

**Component categories (with counts):**
- Buttons (130), Cards (79), Hero sections (73)
- Landing page: announcements, backgrounds, CTAs, features, footers, navigation, pricing, testimonials, video sections
- UI primitives: accordions, alerts, avatars, badges, calendars, carousels, checkboxes, dialogs, dropdowns, forms, inputs, menus, notifications, sidebars, tables, tabs, toasts, tooltips
- 31 animation/interaction hooks
- SVGL integration for professional brand logos

**AI integration:**
- MCP server: `npx @21st-dev/magic@latest` with API key
- Describe UI in natural language, generates components with all dependencies
- Follows shadcn CLI conventions

**When to use:** Browse `21st.dev/s/hero`, `21st.dev/s/card`, `21st.dev/s/component` for pre-built sections BEFORE designing from scratch. Also useful for AI agent interfaces (chat UI, streaming, session management).

---

## Shadcn Animation Ecosystem

Libraries that add animation to shadcn/ui components while preserving accessibility:

| Library | Components | Description |
|---------|-----------|-------------|
| **Animate UI** (animate-ui.com) | All Radix primitives | Animated versions of every shadcn component with Motion |
| **Motion Primitives** (motion-primitives.com) | Developer-focused | Follows shadcn conventions exactly |
| **SmoothUI** (smoothui.dev) | 50+ components | Drop-in shadcn compatible, Tailwind + Motion |
| **Indie UI** | 20+ components | Animated responsive components |

All follow shadcn's copy-paste philosophy (no external dependency). When a project already uses shadcn/ui, recommend Animate UI or Motion Primitives as the animation layer instead of building from scratch.

---

## Scroll Animation Named Patterns

From Framer templates and award-winning sites:

| Pattern | Description | Implementation |
|---------|-------------|----------------|
| **Card Stack Scroll** | Cards rotate forward toward viewer one by one | GSAP ScrollTrigger + 3D transforms |
| **Sticky Scroll Cards** | Pinned cards reveal benefits sequentially | GSAP `pin: true` + `snap` |
| **Horizontal Scroll** | Content scrolls sideways while page scrolls vertically | GSAP pin + `x` translate linked to scrollY |
| **Parallax Layers** | Multi-depth layers at different scroll speeds | Motion `useScroll` + `useTransform` |
| **Tracing Beam** | SVG beam follows scroll path, adjusts with speed | Aceternity `Tracing Beam` component |
| **Section Variant Switch** | Component variants change based on scroll position | Motion `useScroll` + variant switching |

**Framer's philosophy:** "Scroll animations should feel like the page is responding to the user, not performing a script."

---

## Signature Moment Recipes

Pre-built combinations for specific design goals:

### SaaS Landing Page Hero
```
Aurora Background (Aceternity) + Text Generate Effect (Aceternity) + Animated Beam (Magic UI)
```

### Portfolio/Agency Hero
```
Background Beams (Aceternity) + Oversized Typography (8-12vw) + Parallax Layers + Lenis smooth scroll
```

### Product Feature Showcase
```
Bento Grid (Magic UI) + Focus Cards (Aceternity) + Blur Fade entrance (Magic UI)
```

### Testimonials Section
```
Infinite Moving Cards (Aceternity) OR Marquee (Magic UI) + Number Ticker (Magic UI)
```

### Interactive Architecture Diagram
```
Animated Beam (Magic UI) + Orbiting Circles (Magic UI) + Globe (Magic UI)
```

### Dark Mode SaaS Dashboard
```
shadcn/ui base + Border Beam (Magic UI) + Dock (Magic UI) + Shimmer Button (Magic UI)
```

---

## Component Name Lookup

When you need a specific effect, find the right component:

| Need | Aceternity | Magic UI |
|------|-----------|----------|
| Hero background | Aurora Background, Background Beams, Spotlight, Lamp Effect | Particles, Warp Background, Animated Grid |
| Card hover effects | 3D Card Effect, Card Spotlight, Wobble Card, Glare Card | Neon Gradient Card, Cool Mode |
| Text animation | Text Generate, Typewriter, Flip Words, Encrypted Text | Animated Gradient Text, Aurora Text, Morphing Text, Typing Animation |
| Scroll reveal | Parallax Scroll, Sticky Scroll Reveal, Tracing Beam | Blur Fade, Progressive Blur |
| Testimonials/logos | Infinite Moving Cards | Marquee |
| Data visualization | Timeline, World Map | Globe, Dotted Map, Animated Beam, Number Ticker |
| Buttons | — | Shimmer Button, Pulsating Button, Rainbow Button, Shiny Button |
| Navigation | — | Dock (macOS-style) |
| Borders/glow | — | Border Beam, Orbiting Circles |
| Celebration | — | Confetti, Cool Mode |
| Code display | — | Terminal, Code Comparison, File Tree |

---

## Framer Template Design Trends (2025-2026)

- **Dark mode + neon:** deep black/charcoal with bright neon accents
- **Organic shapes:** blobs, smooth curves, natural textures for warmth
- **Micro-animations everywhere:** every interactive element has motion feedback
- **Immersive browsing:** Lenis smooth scroll, horizontal sections, cursor-aware animations
- **Scroll-triggered reveals:** elements appear on viewport entry, not all at once
- **Oversized typography:** 8-12vw headings with serif/display fonts
- **Bento grid layouts:** asymmetric grids for feature showcases
- **3D transforms:** card tilts on hover, perspective shifts, depth layers
- **Custom cursors:** magnetic buttons, cursor trails, context-aware changes

---

## Installation Quick Reference

```bash
# Foundation
npx shadcn@latest init && npx shadcn@latest add button dialog sheet toast

# Animation
npm install motion        # React animations (import from "motion/react")
npm install gsap          # Scroll-driven, timelines (ScrollTrigger included)
npm install lenis         # Smooth scrolling

# Effect Components (via shadcn CLI)
npx shadcn@latest add "https://ui.aceternity.com/r/[component]"
npx shadcn@latest add "https://magicui.design/r/[component]"

# 21st.dev (MCP integration)
npx @21st-dev/magic@latest

# Shadcn animation ecosystem
# Browse: animate-ui.com, motion-primitives.com, smoothui.dev
```
