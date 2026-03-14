# Cutting-Edge CSS & Frontend Techniques (2024-2026)

> Comprehensive reference for modern CSS features that have reached or are approaching Baseline status.
> Each section includes: what it does, browser support, complete code example, and what it replaces.

---

## 1. CSS View Transitions API

**What it does:** Animates between DOM states (SPA) or page navigations (MPA) with crossfade, slide, or custom transitions, without JavaScript animation libraries.

**Browser support (2026):**
| Feature | Chrome | Firefox | Safari | Edge |
|---------|--------|---------|--------|------|
| Same-document (SPA) | 111+ | 133+ | 18+ | 111+ |
| Cross-document (MPA) | 126+ | In dev | 18.2+ | 126+ |

**SPA example (same-document):**
```js
// Trigger a view transition when updating DOM
document.startViewTransition(() => {
  // Update the DOM here
  document.querySelector('.content').innerHTML = newContent;
});
```

```css
/* Default crossfade is automatic. Customize: */
::view-transition-old(root) {
  animation: 300ms ease-out both fade-out, 300ms ease-out both slide-to-left;
}
::view-transition-new(root) {
  animation: 300ms ease-in both fade-in, 300ms ease-in both slide-from-right;
}

@keyframes fade-out { to { opacity: 0; } }
@keyframes fade-in { from { opacity: 0; } }
@keyframes slide-to-left { to { transform: translateX(-100%); } }
@keyframes slide-from-right { from { transform: translateX(100%); } }
```

**MPA example (cross-document):**
```css
/* Add to BOTH pages (source and destination) */
@view-transition {
  navigation: auto;
}

/* Name elements that should animate between pages */
.hero-image {
  view-transition-name: hero;
}

.page-title {
  view-transition-name: title;
}

/* Customize the transition */
::view-transition-old(hero) {
  animation: 400ms cubic-bezier(0.16, 1, 0.3, 1) both fade-and-scale-out;
}
::view-transition-new(hero) {
  animation: 400ms cubic-bezier(0.16, 1, 0.3, 1) both fade-and-scale-in;
}

@keyframes fade-and-scale-out {
  to { opacity: 0; transform: scale(0.95); }
}
@keyframes fade-and-scale-in {
  from { opacity: 0; transform: scale(1.05); }
}
```

**Replaces:** JavaScript-heavy page transition libraries (Barba.js, Swup), manual FLIP animations, SPA router animation wrappers. Previously required capturing screenshots, calculating positions, and running JS animation frames.

---

## 2. CSS Scroll-Driven Animations

**What it does:** Ties CSS animation progress to scroll position (`scroll()`) or element visibility (`view()`), replacing JavaScript scroll listeners entirely.

**Browser support (2026):**
| Feature | Chrome | Firefox | Safari | Edge |
|---------|--------|---------|--------|------|
| `animation-timeline: scroll()` | 115+ | Not yet | 18+ | 115+ |
| `animation-timeline: view()` | 115+ | Not yet | 18+ | 115+ |
| `animation-range` | 115+ | Not yet | 18+ | 115+ |

Polyfill available: `scroll-timeline-polyfill` (github.com/flackr/scroll-timeline).

**Scroll progress example (progress bar):**
```css
/* Progress bar that fills as user scrolls the page */
.progress-bar {
  position: fixed;
  top: 0;
  left: 0;
  height: 4px;
  background: var(--color-accent);
  transform-origin: left;
  /* Tie animation to scroll position of nearest scrollable ancestor */
  animation: grow-progress linear;
  animation-timeline: scroll();
}

@keyframes grow-progress {
  from { transform: scaleX(0); }
  to   { transform: scaleX(1); }
}
```

**View progress example (reveal on scroll):**
```css
/* Elements fade in and slide up as they enter the viewport */
.reveal-on-scroll {
  animation: reveal linear both;
  animation-timeline: view();
  animation-range: entry 0% entry 100%;
}

@keyframes reveal {
  from {
    opacity: 0;
    transform: translateY(60px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
```

**Parallax example:**
```css
.parallax-bg {
  animation: parallax linear;
  animation-timeline: scroll();
}

@keyframes parallax {
  from { transform: translateY(-20%); }
  to   { transform: translateY(20%); }
}
```

**Named scroll timeline (for non-ancestor scrollers):**
```css
.scroller {
  overflow-y: scroll;
  scroll-timeline-name: --my-scroller;
  scroll-timeline-axis: y;
}

.animated-child {
  animation: slide-in linear both;
  animation-timeline: --my-scroller;
}
```

**`animation-range` values:**
```css
/* Control when the animation starts and ends within the view timeline */
animation-range: entry 0% entry 100%;    /* during entry into viewport */
animation-range: exit 0% exit 100%;      /* during exit from viewport */
animation-range: cover 0% cover 100%;    /* entire time element is in view */
animation-range: contain 0% contain 50%; /* first half of full containment */
```

**Replaces:** Intersection Observer + JS scroll listeners, GSAP ScrollTrigger for simple scroll effects, AOS (Animate On Scroll) library, custom requestAnimationFrame scroll handlers. Note: GSAP ScrollTrigger still superior for complex pinning, timeline scrubbing, and snap behaviors.

---

## 3. CSS Anchor Positioning

**What it does:** Positions elements relative to other elements ("anchors") declaratively in CSS, with automatic fallback repositioning when space runs out. Replaces Floating UI / Popper.js.

**Browser support (2026):**
| Feature | Chrome | Firefox | Safari | Edge |
|---------|--------|---------|--------|------|
| Core anchor positioning | 125+ | 145+ (flag) | 26+ | 125+ |
| `position-area` | 125+ | 145+ | 26+ | 125+ |
| `@position-try` | 125+ | 145+ | 26+ | 125+ |

Baseline compatible as of late 2025 / early 2026 with Firefox 146+.

**Complete tooltip example:**
```html
<button class="anchor-btn" id="my-anchor">Hover me</button>
<div class="tooltip" popover>Tooltip content here</div>
```

```css
/* 1. Define the anchor */
.anchor-btn {
  anchor-name: --my-anchor;
}

/* 2. Position the tooltip relative to the anchor */
.tooltip {
  /* Absolutely positioned element */
  position: fixed;
  position-anchor: --my-anchor;

  /* Place it centered above the anchor */
  position-area: top center;

  /* Margin from anchor */
  margin-bottom: 8px;

  /* Fallback positions if no space */
  position-try-fallbacks: --below, --left, --right;

  /* Styling */
  background: var(--color-surface-raised);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-md);
  padding: 8px 12px;
  box-shadow: var(--shadow-lg);
  max-width: 250px;
}

/* 3. Define fallback positions */
@position-try --below {
  position-area: bottom center;
  margin-top: 8px;
  margin-bottom: unset;
}

@position-try --left {
  position-area: left center;
  margin-right: 8px;
  margin-bottom: unset;
}

@position-try --right {
  position-area: right center;
  margin-left: 8px;
  margin-bottom: unset;
}
```

**Dropdown menu example:**
```css
.menu-trigger {
  anchor-name: --menu-trigger;
}

.dropdown-menu {
  position: fixed;
  position-anchor: --menu-trigger;
  position-area: bottom span-right;
  margin-top: 4px;
  position-try-fallbacks: flip-block;  /* built-in: flip to top if no space below */
  position-try-order: most-height;     /* prefer the position with more available height */
}
```

**`position-area` grid (conceptual):**
```
top-left      |  top center     |  top-right
left center   |  center         |  right center
bottom-left   |  bottom center  |  bottom-right
```
Use `span-left`, `span-right`, `span-all` for spanning across grid areas.

**Replaces:** Popper.js, Floating UI, all custom JS tooltip/dropdown positioning logic, manual `getBoundingClientRect()` calculations, scroll-aware repositioning code, resize observers for position updates.

---

## 4. CSS `@starting-style`

**What it does:** Defines the initial style for elements entering the DOM or transitioning from `display: none`, enabling pure-CSS entry animations without JavaScript class toggling.

**Browser support (2026):**
| Feature | Chrome | Firefox | Safari | Edge |
|---------|--------|---------|--------|------|
| `@starting-style` | 117+ | 129+ | 17.5+ | 117+ |
| `transition-behavior: allow-discrete` | 117+ | 129+ | 17.5+ | 117+ |

Baseline Newly Available. Firefox does not yet support animating *from* `display: none` (only *to*).

**Modal entry/exit animation:**
```css
dialog {
  /* Final (visible) state */
  opacity: 1;
  transform: translateY(0) scale(1);

  /* Transition properties including display */
  transition:
    opacity 300ms cubic-bezier(0.16, 1, 0.3, 1),
    transform 300ms cubic-bezier(0.16, 1, 0.3, 1),
    overlay 300ms cubic-bezier(0.16, 1, 0.3, 1) allow-discrete,
    display 300ms cubic-bezier(0.16, 1, 0.3, 1) allow-discrete;
}

/* Exit state (when dialog is closed / display becomes none) */
dialog:not([open]) {
  opacity: 0;
  transform: translateY(16px) scale(0.97);
}

/* Entry state (starting point when dialog first opens) */
@starting-style {
  dialog[open] {
    opacity: 0;
    transform: translateY(16px) scale(0.97);
  }
}

/* Also animate the backdrop */
dialog::backdrop {
  background: rgba(0, 0, 0, 0.5);
  opacity: 1;
  transition:
    opacity 300ms ease,
    overlay 300ms ease allow-discrete,
    display 300ms ease allow-discrete;
}

dialog:not([open])::backdrop {
  opacity: 0;
}

@starting-style {
  dialog[open]::backdrop {
    opacity: 0;
  }
}
```

**Popover entry animation:**
```css
[popover] {
  opacity: 1;
  transform: scale(1);
  transition:
    opacity 200ms ease,
    transform 200ms cubic-bezier(0.16, 1, 0.3, 1),
    overlay 200ms allow-discrete,
    display 200ms allow-discrete;
}

[popover]:not(:popover-open) {
  opacity: 0;
  transform: scale(0.95);
}

@starting-style {
  [popover]:popover-open {
    opacity: 0;
    transform: scale(0.95);
  }
}
```

**Toast notification sliding in:**
```css
.toast {
  display: none;
  opacity: 1;
  transform: translateX(0);
  transition:
    opacity 250ms ease,
    transform 250ms cubic-bezier(0.16, 1, 0.3, 1),
    display 250ms allow-discrete;
}

.toast.visible {
  display: flex;
}

@starting-style {
  .toast.visible {
    opacity: 0;
    transform: translateX(100%);
  }
}
```

**Key requirements:**
1. `@starting-style` defines the "from" state when the element first renders
2. `transition-behavior: allow-discrete` (or `allow-discrete` in the shorthand) is required for `display` to participate in the transition
3. `overlay` transition is needed for top-layer elements (dialog, popover) to stay visible during exit

**Replaces:** JavaScript class toggling for entry animations, `setTimeout` hacks to trigger reflow before adding animation classes, CSS animation libraries for simple show/hide, React Transition Group / Vue `<Transition>` for basic cases.

---

## 5. CSS `@layer` (Cascade Layers)

**What it does:** Organizes CSS into named layers with explicit priority ordering, making specificity predictable and eliminating `!important` hacks.

**Browser support (2026):**
| Feature | Chrome | Firefox | Safari | Edge |
|---------|--------|---------|--------|------|
| `@layer` | 99+ | 97+ | 15.4+ | 99+ |

Fully supported everywhere. Baseline since March 2022.

**Design system layer architecture:**
```css
/* Declare layer order: first = lowest priority, last = highest */
@layer reset, base, tokens, components, utilities, overrides;

/* Reset layer: lowest priority */
@layer reset {
  *, *::before, *::after {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
  }
}

/* Base layer: element defaults */
@layer base {
  body {
    font-family: var(--font-body);
    color: var(--color-text-primary);
    background: var(--color-surface);
    line-height: 1.5;
  }

  a { color: var(--color-accent); }
  img { max-width: 100%; display: block; }
}

/* Tokens layer: design tokens and custom properties */
@layer tokens {
  :root {
    --font-body: 'Inter', sans-serif;
    --color-text-primary: #0a0a0a;
    --color-surface: #fafaf9;
    --color-accent: oklch(55% 0.2 250);
    --spacing-md: 16px;
    --radius-md: 8px;
  }
}

/* Components layer */
@layer components {
  .btn {
    padding: var(--spacing-sm) var(--spacing-md);
    border-radius: var(--radius-md);
    font-weight: 500;
    cursor: pointer;
  }

  .card {
    padding: var(--spacing-lg);
    border-radius: var(--radius-lg);
    background: var(--color-surface-raised);
  }
}

/* Utilities layer: high priority, simple overrides */
@layer utilities {
  .sr-only { position: absolute; width: 1px; height: 1px; overflow: hidden; clip: rect(0,0,0,0); }
  .text-center { text-align: center; }
  .hidden { display: none; }
}

/* Overrides layer: highest priority, project-specific overrides */
@layer overrides {
  /* Customer-specific or page-specific overrides go here */
}
```

**Nested layers for component libraries:**
```css
@layer components {
  @layer base, variants, states;

  @layer base {
    .btn { /* base button */ }
  }

  @layer variants {
    .btn--primary { /* filled */ }
    .btn--secondary { /* outlined */ }
  }

  @layer states {
    .btn:hover { /* hover */ }
    .btn:disabled { /* disabled */ }
  }
}
```

**Importing third-party CSS into a low-priority layer:**
```css
/* Third-party styles get lowest priority, easy to override */
@import url('normalize.css') layer(reset);
@import url('some-library.css') layer(vendor);
```

**Key rules:**
- Unlayered styles always beat layered styles (highest priority)
- Within layers, later-declared layers win
- `!important` reverses layer order (important in first layer beats important in last layer)
- Nested layers are scoped: `components.base` is separate from `utilities.base`

**Replaces:** BEM naming conventions for specificity management, `!important` chains, overly specific selectors to override library styles, CSS Modules specificity isolation (partially), manual selector ordering.

---

## 6. CSS `@container` Queries (Advanced)

**What it does:** Styles elements based on their container's size (not the viewport), enabling truly reusable components. Advanced features include container query units, style queries, and named containers.

**Browser support (2026):**
| Feature | Chrome | Firefox | Safari | Edge |
|---------|--------|---------|--------|------|
| Size queries (`@container`) | 105+ | 110+ | 16+ | 105+ |
| Container query units (cqw, cqh) | 105+ | 110+ | 16+ | 105+ |
| Style queries (`style()`) | 111+ | Not yet | Not yet | 111+ |
| Named containers | 105+ | 110+ | 16+ | 105+ |

**Named containers with size queries:**
```css
/* Define a named container */
.card-grid {
  container-name: card-grid;
  container-type: inline-size;
  /* shorthand: container: card-grid / inline-size; */
}

.sidebar {
  container: sidebar / inline-size;
}

/* Query a specific named container */
@container card-grid (min-width: 600px) {
  .card {
    display: grid;
    grid-template-columns: 200px 1fr;
    gap: 16px;
  }
}

@container card-grid (min-width: 900px) {
  .card {
    grid-template-columns: 250px 1fr auto;
  }
}

@container sidebar (max-width: 250px) {
  .nav-label {
    display: none; /* hide labels, show icons only */
  }
}
```

**Container query units:**
```css
.card-grid {
  container-type: inline-size;
}

.card-title {
  /* Font size scales with container, not viewport */
  font-size: clamp(1rem, 4cqi, 2rem);
  /* cqi = 1% of container inline size */
  /* cqw = 1% of container width */
  /* cqh = 1% of container height */
  /* cqb = 1% of container block size */
  /* cqmin = smaller of cqi/cqb */
  /* cqmax = larger of cqi/cqb */
}

.card-spacing {
  padding: 4cqi;     /* padding scales with container */
  gap: 2cqi;
}
```

**Style queries (Chrome/Edge only as of 2026):**
```css
/* Query custom property values on the container */
.card-wrapper {
  container-type: normal; /* no size containment needed for style queries */
  --variant: default;
}

.card-wrapper.featured {
  --variant: featured;
}

/* Style the card based on the container's custom property */
@container style(--variant: featured) {
  .card {
    background: var(--color-accent-subtle);
    border: 2px solid var(--color-accent);
  }

  .card-title {
    font-size: 1.5rem;
    color: var(--color-accent);
  }
}

/* Theme-aware components */
.theme-region {
  --density: comfortable;
}

@container style(--density: compact) {
  .list-item {
    padding: 4px 8px;
    font-size: 0.875rem;
  }
}

@container style(--density: comfortable) {
  .list-item {
    padding: 12px 16px;
    font-size: 1rem;
  }
}
```

**Responsive component pattern (card that adapts to any container):**
```css
.responsive-container {
  container: card-host / inline-size;
}

/* Small: stacked layout */
@container card-host (max-width: 299px) {
  .product-card {
    display: flex;
    flex-direction: column;
  }
  .product-card img {
    aspect-ratio: 16/9;
    width: 100%;
  }
}

/* Medium: side-by-side */
@container card-host (300px <= width <= 599px) {
  .product-card {
    display: grid;
    grid-template-columns: 120px 1fr;
    gap: 12px;
  }
}

/* Large: expanded with actions visible */
@container card-host (min-width: 600px) {
  .product-card {
    display: grid;
    grid-template-columns: 200px 1fr auto;
    align-items: center;
    gap: 24px;
  }
  .product-actions {
    display: flex;
    gap: 8px;
  }
}
```

**Replaces:** Media queries for component-level responsiveness, JavaScript ResizeObserver for layout switching, element query polyfills, manual breakpoint classes passed as props in component frameworks.

---

## 7. CSS `color-mix()`

**What it does:** Mixes two colors together in a specified color space, enabling dynamic tints, shades, and palette generation without Sass or JS.

**Browser support (2026):**
| Feature | Chrome | Firefox | Safari | Edge |
|---------|--------|---------|--------|------|
| `color-mix()` | 111+ | 113+ | 16.2+ | 111+ |

Baseline since May 2023. ~95% global support.

**Generating tints and shades from a single brand color:**
```css
:root {
  --brand: oklch(55% 0.2 250);

  /* Tints (mix with white) */
  --brand-50:  color-mix(in oklch, var(--brand) 5%, white);
  --brand-100: color-mix(in oklch, var(--brand) 10%, white);
  --brand-200: color-mix(in oklch, var(--brand) 20%, white);
  --brand-300: color-mix(in oklch, var(--brand) 40%, white);
  --brand-400: color-mix(in oklch, var(--brand) 70%, white);

  /* Base */
  --brand-500: var(--brand);

  /* Shades (mix with black) */
  --brand-600: color-mix(in oklch, var(--brand) 85%, black);
  --brand-700: color-mix(in oklch, var(--brand) 70%, black);
  --brand-800: color-mix(in oklch, var(--brand) 50%, black);
  --brand-900: color-mix(in oklch, var(--brand) 30%, black);
}
```

**Transparent variants (for hover states, backgrounds):**
```css
:root {
  --accent: #3b82f6;

  /* Semi-transparent versions */
  --accent-hover-bg: color-mix(in srgb, var(--accent) 10%, transparent);
  --accent-active-bg: color-mix(in srgb, var(--accent) 20%, transparent);
  --accent-border: color-mix(in srgb, var(--accent) 30%, transparent);
}

.btn-ghost:hover {
  background: var(--accent-hover-bg);
}
```

**Dynamic hover darkening:**
```css
.btn-primary {
  background: var(--brand-500);
}

.btn-primary:hover {
  /* 20% darker on hover */
  background: color-mix(in oklch, var(--brand-500) 80%, black);
}

.btn-primary:active {
  /* 30% darker on press */
  background: color-mix(in oklch, var(--brand-500) 70%, black);
}
```

**Color space recommendation:**
- `oklch` or `oklab`: perceptually uniform, best for palette generation. Blue doesn't shift hue when darkened.
- `srgb`: predictable, slightly less uniform but widely understood.
- `hsl` / `hwb`: can produce hue shifts and overly vibrant results when mixing. Avoid for palette generation.

**Replaces:** Sass `lighten()` / `darken()` / `mix()`, PostCSS color functions, JS-based color manipulation libraries (chroma.js, polished), manual hex color calculations, maintaining dozens of hand-picked color variants.

---

## 8. CSS `light-dark()`

**What it does:** Returns one of two color values based on the user's or page's color scheme preference, without media queries.

**Browser support (2026):**
| Feature | Chrome | Firefox | Safari | Edge |
|---------|--------|---------|--------|------|
| `light-dark()` | 123+ | 120+ | 17.5+ | 123+ |

Baseline Newly Available. ~87% global support.

**Complete dark mode system:**
```css
/* Required: declare color-scheme on root */
:root {
  color-scheme: light dark;

  /* All tokens use light-dark() -- single source of truth */
  --color-text-primary: light-dark(#0a0a0a, #fafaf9);
  --color-text-secondary: light-dark(
    rgba(10, 10, 10, 0.6),
    rgba(250, 250, 249, 0.6)
  );
  --color-text-tertiary: light-dark(
    rgba(10, 10, 10, 0.4),
    rgba(250, 250, 249, 0.4)
  );

  --color-surface: light-dark(#fafaf9, #0a0a0a);
  --color-surface-raised: light-dark(#ffffff, #141414);
  --color-surface-sunken: light-dark(#f0f0ef, #050505);

  --color-border: light-dark(
    rgba(10, 10, 10, 0.1),
    rgba(250, 250, 249, 0.1)
  );
  --color-border-strong: light-dark(
    rgba(10, 10, 10, 0.2),
    rgba(250, 250, 249, 0.15)
  );

  --shadow-md: light-dark(
    0 4px 6px rgba(0, 0, 0, 0.05),
    0 4px 6px rgba(0, 0, 0, 0.3)
  );
}

/* Use normally -- automatically adapts */
body {
  color: var(--color-text-primary);
  background: var(--color-surface);
}

.card {
  background: var(--color-surface-raised);
  border: 1px solid var(--color-border);
  box-shadow: var(--shadow-md);
}
```

**Combined with `color-mix()` for adaptive accent colors:**
```css
:root {
  color-scheme: light dark;

  --brand: oklch(55% 0.2 250);

  /* Slightly desaturate accent in dark mode */
  --color-accent: light-dark(
    var(--brand),
    color-mix(in oklch, var(--brand) 85%, gray)
  );

  --color-accent-bg: light-dark(
    color-mix(in oklch, var(--brand) 10%, white),
    color-mix(in oklch, var(--brand) 15%, black)
  );
}
```

**Force a specific scheme for a section:**
```css
/* This section is always dark regardless of user preference */
.dark-hero {
  color-scheme: dark;
  background: var(--color-surface);  /* will use dark value */
  color: var(--color-text-primary);  /* will use dark value */
}
```

**Replaces:** `prefers-color-scheme` media queries with duplicated custom properties, `[data-theme="dark"]` attribute selectors, JavaScript theme toggle class management, maintaining two separate sets of CSS variables.

---

## 9. CSS `text-wrap: balance` and `text-wrap: pretty`

**What it does:** `balance` equalizes line lengths in short text blocks (headings). `pretty` avoids orphans (single words on the last line) in paragraphs.

**Browser support (2026):**
| Feature | Chrome | Firefox | Safari | Edge |
|---------|--------|---------|--------|------|
| `text-wrap: balance` | 114+ | 121+ | 17.5+ | 114+ |
| `text-wrap: pretty` | 117+ | Not yet | 26+ | 117+ |
| `text-wrap: stable` | 130+ | 121+ | 17.5+ | 130+ |

Progressive enhancement: unsupported browsers fall back to normal wrapping with no visual breakage.

**Usage:**
```css
/* Balance: for headings and short text (6 lines max in Chrome, 10 in Firefox) */
h1, h2, h3, h4, h5, h6,
blockquote,
.card-title,
.hero-text {
  text-wrap: balance;
}

/* Pretty: for body text to avoid orphaned last words */
p,
li,
.body-text {
  text-wrap: pretty;
}

/* Stable: prevents text reflow when contenteditable content changes */
[contenteditable] {
  text-wrap: stable;
}
```

**Before and after comparison:**
```
/* Without text-wrap: balance */
"The Quick Guide to Building
Modern Web Applications"

/* With text-wrap: balance */
"The Quick Guide to
Building Modern Web
Applications"
→ Lines are roughly equal length
```

```
/* Without text-wrap: pretty */
"This paragraph has a lot of great content that wraps nicely except for the last
word."

/* With text-wrap: pretty */
"This paragraph has a lot of great content that
wraps nicely except for the last word."
→ Last line avoids single orphaned word
```

**Modern CSS reset inclusion:**
```css
/* Add to your reset */
h1, h2, h3, h4, h5, h6 { text-wrap: balance; }
p { text-wrap: pretty; }
```

**Limitations:**
- `balance` only works on blocks with 6 or fewer lines (Chrome) or 10 (Firefox). Longer text falls back to normal.
- `balance` is computationally expensive; don't apply to long-form content.
- `pretty` may adjust line breaks throughout the paragraph, not just the last line.

**Replaces:** JavaScript text-balancing libraries (Adobe's `text-balancer`, React Wrap Balancer), manual `<br>` tags for controlling line breaks, `&nbsp;` hacks to prevent orphans, CSS `widows` / `orphans` properties (which only work in paged media).

---

## 10. CSS Subgrid

**What it does:** Allows nested grid items to inherit and align with their parent grid's track definitions, enabling consistent alignment across deeply nested components.

**Browser support (2026):**
| Feature | Chrome | Firefox | Safari | Edge |
|---------|--------|---------|--------|------|
| `subgrid` | 117+ | 71+ | 16+ | 117+ |

97%+ global coverage. Safe for production.

**Card layout with aligned sections:**
```css
/* Parent grid defines the row tracks */
.card-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
  gap: 24px;
}

/* Each card uses subgrid to align its internal rows with siblings */
.card {
  display: grid;
  /* Inherit row tracks from parent -- card spans 4 implicit rows */
  grid-row: span 4;
  grid-template-rows: subgrid;
  gap: 0;
  border-radius: var(--radius-lg);
  overflow: hidden;
  background: var(--color-surface-raised);
}

/* Card children automatically align across all cards */
.card-image   { /* row 1 */ }
.card-title   { padding: 16px 16px 0; /* row 2 */ }
.card-body    { padding: 0 16px; /* row 3 */ }
.card-actions { padding: 16px; align-self: end; /* row 4 */ }
```

**Form layout with aligned labels and inputs:**
```css
.form {
  display: grid;
  grid-template-columns: auto 1fr;
  gap: 16px 24px;
}

.form-section {
  display: grid;
  grid-column: 1 / -1;        /* span both columns */
  grid-template-columns: subgrid; /* inherit parent's columns */
  gap: 12px 24px;
}

.form-section label {
  grid-column: 1;  /* all labels in column 1 */
  text-align: right;
}

.form-section input {
  grid-column: 2;  /* all inputs in column 2, perfectly aligned */
}
```

**Dashboard with aligned metric cards:**
```css
.dashboard {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-template-rows: auto auto auto; /* label, value, chart */
  gap: 16px;
}

.metric-card {
  display: grid;
  grid-row: 1 / -1;           /* span all 3 rows */
  grid-template-rows: subgrid; /* align label/value/chart across cards */
  padding: 16px;
  background: var(--color-surface-raised);
  border-radius: var(--radius-lg);
}

/* All labels, all values, and all charts align perfectly across cards */
.metric-label { font-size: 0.875rem; color: var(--color-text-secondary); }
.metric-value { font-size: 2rem; font-weight: 700; }
.metric-chart { /* sparkline or mini chart */ }
```

**Replaces:** Duplicating `grid-template-rows` / `grid-template-columns` in every nested component, JavaScript-based height equalization (matchHeight), flexbox hacks with invisible spacer elements, fixed heights on card sections.

---

## 11. CSS `@scope`

**What it does:** Scopes CSS rules to a specific DOM subtree with optional lower boundaries, preventing style leakage without Shadow DOM or BEM naming.

**Browser support (2026):**
| Feature | Chrome | Firefox | Safari | Edge |
|---------|--------|---------|--------|------|
| `@scope` | 118+ | 146+ | 17.4+ | 118+ |

Baseline compatible as of late 2025 / early 2026.

**Basic scoping with upper and lower bounds:**
```css
/* Styles apply inside .card but stop at .card-footer */
@scope (.card) to (.card-footer) {
  p {
    color: var(--color-text-secondary);
    line-height: 1.6;
  }

  a {
    color: var(--color-accent);
    text-decoration: underline;
  }

  /* These styles do NOT leak into .card-footer */
}

/* Card footer has its own scope */
@scope (.card-footer) {
  p {
    font-size: 0.875rem;
    color: var(--color-text-tertiary);
  }

  a {
    color: inherit;
    text-decoration: none;
  }
}
```

**Component isolation (replaces BEM):**
```css
/* Instead of .nav__link, .nav__icon, .nav__label */
@scope (.nav) {
  a {
    display: flex;
    align-items: center;
    gap: 8px;
    padding: 8px 16px;
    color: var(--color-text-secondary);
    text-decoration: none;
    border-radius: var(--radius-md);
  }

  a:hover {
    background: var(--color-surface-raised);
    color: var(--color-text-primary);
  }

  /* :scope refers to the scoping root (.nav) */
  :scope {
    display: flex;
    flex-direction: column;
    gap: 4px;
  }
}
```

**Inline `<style>` scoping (no selector needed):**
```html
<div class="widget">
  <style>
    /* Automatically scoped to .widget (the parent element) */
    @scope {
      p { color: blue; }     /* Only blue inside this .widget */
      a { font-weight: bold; } /* Only bold inside this .widget */
    }
  </style>
  <p>This is blue.</p>
  <a href="#">This is bold.</a>
</div>
<p>This is NOT blue.</p>
```

**Theme regions:**
```css
@scope ([data-theme="marketing"]) to ([data-theme]) {
  h1 { font-family: 'Clash Display', sans-serif; font-size: 4rem; }
  .btn { border-radius: 9999px; }
}

@scope ([data-theme="app"]) to ([data-theme]) {
  h1 { font-family: 'Inter', sans-serif; font-size: 1.5rem; }
  .btn { border-radius: 8px; }
}
```

**Proximity-based specificity:**
When two `@scope` rules match the same element, the one whose scope root is closer in the DOM tree wins. This is unique to `@scope` and cannot be replicated with other CSS features.

**Replaces:** BEM naming conventions (`.block__element--modifier`), CSS Modules (build-tool-based scoping), Shadow DOM (when full encapsulation is overkill), deeply nested selectors for specificity, component-scoped `<style>` in frameworks (Vue/Svelte).

---

## 12. Popover API

**What it does:** Native HTML attribute for creating dismissible popup elements (tooltips, menus, dialogs) with built-in light dismiss, focus management, and top-layer rendering, no JavaScript required.

**Browser support (2026):**
| Feature | Chrome | Firefox | Safari | Edge |
|---------|--------|---------|--------|------|
| `popover` attribute | 114+ | 125+ | 17+ | 114+ |
| `popovertarget` | 114+ | 125+ | 17+ | 114+ |
| `popovertargetaction` | 114+ | 125+ | 17+ | 114+ |

Baseline Newly Available since January 2025. Works in all modern browsers.

**Basic popover (zero JavaScript):**
```html
<!-- The trigger button -->
<button popovertarget="my-popover">Open Menu</button>

<!-- The popover content (hidden by default) -->
<div id="my-popover" popover>
  <nav>
    <a href="/dashboard">Dashboard</a>
    <a href="/settings">Settings</a>
    <a href="/logout">Log out</a>
  </nav>
</div>
```

```css
/* Style the popover */
[popover] {
  padding: 16px;
  border: 1px solid var(--color-border);
  border-radius: var(--radius-lg);
  box-shadow: var(--shadow-xl);
  background: var(--color-surface-raised);
  max-width: 300px;

  /* Remove default browser styles */
  margin: 0;
  overflow: visible;
}

/* Style the backdrop (auto popovers get one) */
[popover]::backdrop {
  background: transparent; /* or dim: rgba(0,0,0,0.1) */
}
```

**Popover with explicit show/hide/toggle:**
```html
<button popovertarget="info" popovertargetaction="show">Show</button>
<button popovertarget="info" popovertargetaction="hide">Hide</button>
<button popovertarget="info" popovertargetaction="toggle">Toggle</button>

<div id="info" popover>Popover content</div>
```

**Manual popover (no light dismiss):**
```html
<!-- Must be explicitly closed; clicking outside does NOT dismiss -->
<button popovertarget="persistent-menu">Settings</button>

<div id="persistent-menu" popover="manual">
  <h3>Settings</h3>
  <form>...</form>
  <button popovertarget="persistent-menu" popovertargetaction="hide">Close</button>
</div>
```

**Popover with entry animation (combine with @starting-style):**
```css
[popover] {
  opacity: 1;
  transform: translateY(0);
  transition:
    opacity 200ms ease,
    transform 200ms cubic-bezier(0.16, 1, 0.3, 1),
    overlay 200ms allow-discrete,
    display 200ms allow-discrete;
}

[popover]:not(:popover-open) {
  opacity: 0;
  transform: translateY(-8px);
}

@starting-style {
  [popover]:popover-open {
    opacity: 0;
    transform: translateY(-8px);
  }
}
```

**Popover + Anchor Positioning (the ultimate combo):**
```html
<button id="trigger" popovertarget="tooltip">Hover info</button>
<div id="tooltip" popover="hint" anchor="trigger">
  Helpful tooltip text
</div>
```

```css
#trigger {
  anchor-name: --trigger;
}

#tooltip {
  position: fixed;
  position-anchor: --trigger;
  position-area: top center;
  margin-bottom: 8px;
  position-try-fallbacks: flip-block;
}
```

**Popover types summary:**
| Type | Light dismiss | Closes other popovers | Use case |
|------|--------------|----------------------|----------|
| `popover` / `popover="auto"` | Yes (click outside, Esc) | Yes | Menus, dropdowns |
| `popover="manual"` | No | No | Persistent panels, toasts |
| `popover="hint"` (future) | Yes | No (only other hints) | Tooltips |

**Replaces:** Custom modal/dropdown JavaScript, click-outside detection libraries, focus trap libraries for simple popups, z-index stacking context management, `event.stopPropagation()` hacks for nested menus.

---

## 13. `<dialog>` Element

**What it does:** Native HTML element for modal and non-modal dialogs with built-in backdrop, focus trapping, `Esc` to close, and `inert` behavior on the rest of the page.

**Browser support (2026):**
| Feature | Chrome | Firefox | Safari | Edge |
|---------|--------|---------|--------|------|
| `<dialog>` | 37+ | 98+ | 15.4+ | 79+ |
| `::backdrop` | 37+ | 98+ | 15.4+ | 79+ |
| `showModal()` / `close()` | 37+ | 98+ | 15.4+ | 79+ |
| `inert` (auto on modal) | 102+ | 112+ | 15.5+ | 102+ |

Fully supported. Baseline since March 2022.

**Complete modal pattern:**
```html
<button onclick="document.getElementById('confirm-dialog').showModal()">
  Delete Account
</button>

<dialog id="confirm-dialog">
  <form method="dialog">
    <h2>Delete your account?</h2>
    <p>This action cannot be undone. All your data will be permanently removed.</p>

    <div class="dialog-actions">
      <button value="cancel" formmethod="dialog">Cancel</button>
      <button value="confirm" class="btn-destructive">Delete Account</button>
    </div>
  </form>
</dialog>
```

```css
dialog {
  border: none;
  border-radius: var(--radius-xl);
  padding: 32px;
  max-width: 480px;
  width: 90vw;
  box-shadow: var(--shadow-xl);
  background: var(--color-surface-raised);
  color: var(--color-text-primary);
}

/* Backdrop styling */
dialog::backdrop {
  background: rgba(0, 0, 0, 0.5);
  backdrop-filter: blur(4px);
}

/* Animation with @starting-style */
dialog[open] {
  opacity: 1;
  transform: scale(1);
  transition:
    opacity 300ms cubic-bezier(0.16, 1, 0.3, 1),
    transform 300ms cubic-bezier(0.16, 1, 0.3, 1),
    overlay 300ms allow-discrete,
    display 300ms allow-discrete;
}

dialog:not([open]) {
  opacity: 0;
  transform: scale(0.95);
}

@starting-style {
  dialog[open] {
    opacity: 0;
    transform: scale(0.95);
  }
}

dialog[open]::backdrop {
  opacity: 1;
  transition: opacity 300ms ease, overlay 300ms allow-discrete, display 300ms allow-discrete;
}

dialog:not([open])::backdrop {
  opacity: 0;
}

@starting-style {
  dialog[open]::backdrop {
    opacity: 0;
  }
}

.dialog-actions {
  display: flex;
  gap: 12px;
  justify-content: flex-end;
  margin-top: 24px;
}
```

**JavaScript for handling the result:**
```js
const dialog = document.getElementById('confirm-dialog');

dialog.addEventListener('close', () => {
  if (dialog.returnValue === 'confirm') {
    // User clicked "Delete Account"
    deleteAccount();
  }
  // "cancel" or Esc: do nothing
});
```

**Non-modal dialog (no backdrop, no inert):**
```js
// Non-modal: user can still interact with the page
dialog.show();    // non-modal
dialog.showModal(); // modal (backdrop, inert, focus trap)
```

**Built-in behaviors of `showModal()`:**
1. Renders dialog in the top layer (above all other content)
2. Adds `::backdrop` pseudo-element
3. Makes everything else `inert` (not interactive, not focusable)
4. Traps focus within the dialog (tabbing cycles inside)
5. `Esc` key closes the dialog
6. Returns focus to the triggering element on close

**Replaces:** React modal libraries (react-modal, Radix Dialog for simple cases), custom backdrop overlays with z-index management, manual focus trap implementations, `inert` attribute management, `aria-modal` + `role="dialog"` boilerplate.

---

## 14. CSS `:has()` Selector

**What it does:** Selects a parent element based on its children or descendants, enabling conditional styling, form validation feedback, and layout changes without JavaScript.

**Browser support (2026):**
| Feature | Chrome | Firefox | Safari | Edge |
|---------|--------|---------|--------|------|
| `:has()` | 105+ | 121+ | 15.4+ | 105+ |

95%+ global support. Fully Baseline.

**Parent selection:**
```css
/* Style a card differently when it contains an image */
.card:has(img) {
  display: grid;
  grid-template-columns: 200px 1fr;
}

.card:not(:has(img)) {
  border-top: 3px solid var(--color-accent);
}

/* Style a list item when it has a nested submenu */
.nav-item:has(.submenu) > a::after {
  content: '>';
  margin-left: auto;
}
```

**Form validation styling (no JavaScript):**
```css
/* Highlight the form group when its input is invalid */
.form-group:has(input:invalid:not(:placeholder-shown)) {
  --field-color: var(--color-error);
}

.form-group:has(input:valid:not(:placeholder-shown)) {
  --field-color: var(--color-success);
}

.form-group:has(input:focus-visible) {
  --field-color: var(--color-accent);
}

/* Apply the color to border and label */
.form-group label {
  color: var(--field-color, var(--color-text-secondary));
}

.form-group input {
  border-color: var(--field-color, var(--color-border));
}

/* Show error message only when input is invalid and not empty */
.form-group .error-message {
  display: none;
}

.form-group:has(input:invalid:not(:placeholder-shown)) .error-message {
  display: block;
  color: var(--color-error);
  font-size: 0.875rem;
  margin-top: 4px;
}
```

**Conditional layouts:**
```css
/* Change page layout based on presence of sidebar */
.page:has(.sidebar) {
  display: grid;
  grid-template-columns: 280px 1fr;
}

.page:not(:has(.sidebar)) {
  display: block;
  max-width: 768px;
  margin-inline: auto;
}

/* Show placeholder when a list is empty */
.list:not(:has(.list-item)) .empty-state {
  display: flex;
}

.list:has(.list-item) .empty-state {
  display: none;
}
```

**Quantity queries (style based on number of children):**
```css
/* Grid layout when there are 4+ items */
.tag-list:has(:nth-child(4)) {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
}

/* Stack layout when few items */
.tag-list:not(:has(:nth-child(4))) {
  display: flex;
  flex-direction: column;
  gap: 4px;
}
```

**Interactive state propagation:**
```css
/* Highlight table row when any cell's checkbox is checked */
tr:has(input[type="checkbox"]:checked) {
  background: var(--color-accent-bg);
}

/* Style the entire form when any required field is empty */
form:has(:required:invalid) .submit-btn {
  opacity: 0.5;
  pointer-events: none;
}

/* Dark mode toggle without JavaScript */
html:has(#dark-mode-toggle:checked) {
  color-scheme: dark;
}
```

**Sibling and combinator patterns:**
```css
/* Select an element when its NEXT sibling matches */
h2:has(+ p) {
  margin-bottom: 8px;  /* tighter spacing when followed by paragraph */
}

h2:has(+ ul, + ol) {
  margin-bottom: 12px; /* slightly more before lists */
}

/* Feature card: if image comes first, add different padding */
.feature:has(> img:first-child) {
  padding-top: 0;
}
```

**Replaces:** JavaScript parent class toggling, jQuery `.parent()` / `.closest()` selectors, React state-driven conditional classes on parent elements, CSS-in-JS conditional styling based on children, manual DOM traversal for validation feedback.

---

## 15. View Transition Classes (Advanced View Transitions)

**What it does:** Names individual elements for independent transition animations and provides pseudo-elements (`::view-transition-old()`, `::view-transition-new()`) for fine-grained control over how each named element transitions.

**Browser support (2026):**
Same as View Transitions API (section 1):
| Feature | Chrome | Firefox | Safari | Edge |
|---------|--------|---------|--------|------|
| `view-transition-name` | 111+ | 133+ | 18+ | 111+ |
| `::view-transition-old/new()` | 111+ | 133+ | 18+ | 111+ |
| `view-transition-class` | 125+ | In dev | 18.2+ | 125+ |

**Named element transitions (shared element animation):**
```css
/* Give unique names to elements that should animate independently */
.product-image {
  view-transition-name: product-hero;
}

.product-title {
  view-transition-name: product-title;
}

.product-price {
  view-transition-name: product-price;
}

/* The browser auto-generates these pseudo-element pairs for each named element: */
/* ::view-transition-old(product-hero) — snapshot of the old state */
/* ::view-transition-new(product-hero) — snapshot of the new state */

/* Customize animation for the hero image */
::view-transition-old(product-hero) {
  animation: 400ms cubic-bezier(0.16, 1, 0.3, 1) both scale-down;
}
::view-transition-new(product-hero) {
  animation: 400ms cubic-bezier(0.16, 1, 0.3, 1) both scale-up;
}

/* Title cross-fades (default behavior) */
::view-transition-old(product-title),
::view-transition-new(product-title) {
  animation-duration: 250ms;
}

/* Price slides */
::view-transition-old(product-price) {
  animation: 200ms ease-out slide-out-up;
}
::view-transition-new(product-price) {
  animation: 300ms cubic-bezier(0.16, 1, 0.3, 1) slide-in-up;
}

@keyframes scale-down { to { transform: scale(0.8); opacity: 0; } }
@keyframes scale-up { from { transform: scale(1.2); opacity: 0; } }
@keyframes slide-out-up { to { transform: translateY(-20px); opacity: 0; } }
@keyframes slide-in-up { from { transform: translateY(20px); opacity: 0; } }
```

**View transition classes (grouping animations):**
```css
/* Instead of writing rules for each named element, group them */
.card {
  view-transition-name: card-1; /* each must be unique */
  view-transition-class: card;  /* but can share a class */
}

/* Style all elements with the "card" transition class */
::view-transition-group(*.card) {
  animation-duration: 300ms;
  animation-timing-function: cubic-bezier(0.16, 1, 0.3, 1);
}
```

**Dynamic view-transition-name for lists:**
```css
/* In list→detail transitions, dynamically assign names */
.product-card {
  view-transition-name: none; /* default: no transition */
}

/* Only the clicked card gets a name (set via JS or :has) */
.product-card.transitioning {
  view-transition-name: product-hero;
}
```

```js
// SPA: list-to-detail transition
card.addEventListener('click', (e) => {
  // Name only the clicked card
  card.style.viewTransitionName = 'product-hero';

  document.startViewTransition(() => {
    // Navigate to detail view
    renderDetailPage(card.dataset.productId);

    // The detail page's hero image also has view-transition-name: product-hero
    // Browser auto-animates between the two positions
  });
});
```

**Pseudo-element tree structure:**
```
::view-transition                          /* root container */
  ::view-transition-group(name)            /* contains old + new */
    ::view-transition-image-pair(name)     /* crossfade container */
      ::view-transition-old(name)          /* screenshot of old state */
      ::view-transition-new(name)          /* screenshot of new state */
```

**Direction-aware transitions (MPA):**
```css
/* Different animations based on navigation direction */
@view-transition {
  navigation: auto;
  types: slide-forward, slide-back;
}

/* Forward navigation: slide left */
@media (view-transition-type: slide-forward) {
  ::view-transition-old(root) {
    animation: 300ms ease slide-to-left;
  }
  ::view-transition-new(root) {
    animation: 300ms ease slide-from-right;
  }
}

/* Back navigation: slide right */
@media (view-transition-type: slide-back) {
  ::view-transition-old(root) {
    animation: 300ms ease slide-to-right;
  }
  ::view-transition-new(root) {
    animation: 300ms ease slide-from-left;
  }
}
```

**Important rules:**
- Every `view-transition-name` must be unique on the page at any given time
- Use `view-transition-name: none` to opt elements out
- Named elements on both old and new states auto-animate position, size, and opacity
- The pseudo-elements are snapshots (rasterized images), not live DOM

**Replaces:** FLIP animation libraries (flip.js), shared element transition plugins, manual position/size interpolation between views, React/Vue transition group components for complex choreography, native mobile shared element transitions (partially).

---

## Quick Integration Guide

### Modern CSS Reset (2026)
```css
@layer reset, base, tokens, components, utilities;

@layer reset {
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
  html { color-scheme: light dark; text-size-adjust: none; }
  body { min-height: 100dvh; -webkit-font-smoothing: antialiased; }
  img, picture, video, canvas, svg { display: block; max-width: 100%; }
  input, button, textarea, select { font: inherit; }
  h1, h2, h3, h4, h5, h6 { text-wrap: balance; }
  p { text-wrap: pretty; }
}
```

### Feature Detection
```css
/* Use @supports for progressive enhancement */
@supports (animation-timeline: scroll()) {
  .scroll-animated { /* scroll-driven animation styles */ }
}

@supports (anchor-name: --x) {
  .tooltip { /* anchor positioning styles */ }
}

@supports selector(:has(*)) {
  /* :has-based styles */
}
```

### Combination Patterns
The real power comes from combining these features:

1. **Popover + Anchor Positioning + @starting-style** = fully native tooltips/dropdowns with positioning and animation, zero JS
2. **:has() + @container** = components that adapt based on both their content AND their container size
3. **View Transitions + @starting-style** = animated page transitions with animated entry/exit for modals/popovers
4. **@layer + @scope** = complete CSS architecture without BEM, CSS Modules, or CSS-in-JS
5. **light-dark() + color-mix()** = entire theme system in pure CSS, single source of truth
6. **Subgrid + @container** = components with internal alignment that also adapt to their container

---

## Sources

- [View Transition API | MDN](https://developer.mozilla.org/en-US/docs/Web/API/View_Transition_API)
- [Using the View Transition API | MDN](https://developer.mozilla.org/en-US/docs/Web/API/View_Transition_API/Using)
- [Smooth transitions with the View Transition API | Chrome Developers](https://developer.chrome.com/docs/web-platform/view-transitions)
- [CSS scroll-driven animations | MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/Guides/Scroll-driven_animations)
- [CSS Scroll-Driven Animations Guide | design.dev](https://design.dev/guides/scroll-timeline/)
- [CSS anchor positioning | MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/Guides/Anchor_positioning)
- [Anchor Positioning Updates for Fall 2025 | OddBird](https://www.oddbird.net/2025/10/13/anchor-position-area-update/)
- [CSS Anchor Positioning Guide | CSS-Tricks](https://css-tricks.com/css-anchor-positioning-guide/)
- [@starting-style | MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/At-rules/@starting-style)
- [Four new CSS features for smooth entry and exit animations | Chrome Developers](https://developer.chrome.com/blog/entry-exit-animations)
- [Transitioning Top-Layer Entries And The Display Property In CSS | Smashing Magazine](https://www.smashingmagazine.com/2025/01/transitioning-top-layer-entries-display-property-css/)
- [@layer | MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/At-rules/@layer)
- [Organizing Design System Component Patterns With CSS Cascade Layers | CSS-Tricks](https://css-tricks.com/organizing-design-system-component-patterns-with-css-cascade-layers/)
- [CSS container queries | MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/Guides/Containment/Container_queries)
- [Container queries in 2026 | LogRocket](https://blog.logrocket.com/container-queries-2026/)
- [color-mix() | MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Values/color_value/color-mix)
- [light-dark() | MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Values/color_value/light-dark)
- [text-wrap-style | MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Properties/text-wrap-style)
- [When to use CSS text-wrap: balance vs. text-wrap: pretty | LogRocket](https://blog.logrocket.com/css-text-wrap-balance-vs-text-wrap-pretty/)
- [Subgrid | MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/Guides/Grid_layout/Subgrid)
- [CSS subgrid | web.dev](https://web.dev/articles/css-subgrid)
- [@scope | MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/At-rules/@scope)
- [How to @scope CSS Now That It's Baseline | Frontend Masters](https://frontendmasters.com/blog/how-to-scope-css-now-that-its-baseline/)
- [CSS @scope: An Alternative To Naming Conventions | Smashing Magazine](https://www.smashingmagazine.com/2026/02/css-scope-alternative-naming-conventions/)
- [Popover API | MDN](https://developer.mozilla.org/en-US/docs/Web/API/Popover_API)
- [Popover API lands in Baseline | web.dev](https://web.dev/blog/popover-api)
- [dialog element | MDN](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/dialog)
- [:has() | MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Selectors/:has)
- [CSS :has() Selector Deep Dive | DEV](https://dev.to/pockit_tools/css-has-selector-deep-dive-the-parent-selector-that-changes-everything-p77)
- [CSS Anchor Positioning | Can I Use](https://caniuse.com/css-anchor-positioning)
- [CSS Container Query Units | Can I Use](https://caniuse.com/css-container-query-units)
