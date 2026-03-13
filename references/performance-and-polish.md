# Performance & Visual Polish Reference

Complete reference for Core Web Vitals, font/image/CSS performance, rendering optimization, visual polish techniques, and advanced responsive patterns.

---

## 1. Core Web Vitals

### Thresholds (measured at 75th percentile of real user data)

| Metric | Good | Needs Improvement | Poor | What It Measures |
|--------|------|-------------------|------|------------------|
| **LCP** (Largest Contentful Paint) | < 2.5s | 2.5s - 4.0s | > 4.0s | Loading speed (largest visible element render time) |
| **INP** (Interaction to Next Paint) | < 200ms | 200ms - 500ms | > 500ms | Responsiveness (delay from user input to next frame) |
| **CLS** (Cumulative Layout Shift) | < 0.1 | 0.1 - 0.25 | > 0.25 | Visual stability (unexpected layout movement) |

All three must pass at the 75th percentile for a page to have an overall "good" score. Google uses these for ranking signals.

### How Design Decisions Affect Each Metric

**LCP killers:**
- Unoptimized hero images (largest element on most pages)
- Web fonts that block rendering (`font-display: block`)
- Render-blocking CSS/JS in `<head>`
- Lazy-loading the LCP image (never do this)
- Server response time > 600ms (TTFB)

**INP killers:**
- Long JavaScript tasks on main thread (> 50ms blocks input)
- Heavy event handlers on click/keydown
- Layout thrashing in interaction handlers (read-write-read DOM cycles)
- Large DOM trees (> 1,500 nodes increases style recalculation)

**CLS killers:**
- Images without `width`/`height` or `aspect-ratio`
- Web fonts that swap with different metrics (no `size-adjust`)
- Dynamically injected content above the fold (ads, banners, cookie notices)
- Late-loading CSS that shifts existing elements

---

## 2. Font Loading Optimization

### font-display Strategy Selection

| Value | Block Period | Swap Period | Best For |
|-------|-------------|-------------|----------|
| `swap` | ~100ms invisible | Infinite swap window | Body text (always readable, swaps when ready) |
| `optional` | ~100ms invisible | None (uses cached or skips) | Repeat visitors, perf-critical pages |
| `fallback` | ~100ms invisible | ~3s swap window | Heading fonts (short swap window) |
| `block` | Up to 3s invisible | Infinite | Icon fonts only (FOUT is worse than FOIT for icons) |

**Recommendation:** `swap` for body, `optional` for display/heading on performance-critical pages.

### Fallback Font Matching (Eliminate CLS from Font Swap)

Use `size-adjust`, `ascent-override`, `descent-override`, and `line-gap-override` to make the fallback font occupy identical space to the web font:

```css
/* Match Inter's metrics on Arial fallback */
@font-face {
  font-family: 'Inter Fallback';
  src: local('Arial');
  size-adjust: 107.06%;       /* scale fallback glyphs to match web font width */
  ascent-override: 90.49%;    /* match the height above baseline */
  descent-override: 22.56%;   /* match the depth below baseline */
  line-gap-override: 0%;      /* match the gap between lines */
}

body {
  font-family: 'Inter', 'Inter Fallback', system-ui, sans-serif;
}
```

**Finding override values:**
- Use [Fallback Font Generator](https://screenspan.net/fallback) or the Font Face Metrics Adjuster tool
- Next.js `@next/font` calculates these automatically
- Chrome DevTools > Rendering > "Compute font fallback metrics" experiment

### Variable Font Optimization

```css
/* Single file replaces 6+ static weight files */
@font-face {
  font-family: 'Inter';
  src: url('/fonts/Inter-Variable.woff2') format('woff2-variations');
  font-weight: 100 900;
  font-display: swap;
}
```

| Approach | File Size | HTTP Requests |
|----------|-----------|---------------|
| 6 static WOFF2 weights | 150-300KB | 6 |
| 1 variable WOFF2 | 80-150KB | 1 |

### Subsetting

Strip unused Unicode ranges to reduce file size by 50-80%:

```bash
# Using pyftsubset (fonttools)
pyftsubset Inter-Variable.woff2 \
  --output-file=Inter-Variable-subset.woff2 \
  --flavor=woff2 \
  --layout-features='kern,liga,calt' \
  --unicodes='U+0000-00FF,U+0131,U+0152-0153,U+02BB-02BC,U+2000-206F,U+2074,U+20AC,U+2122,U+2191,U+2193,U+2212,U+2215,U+FEFF,U+FFFD'
```

Or use `unicode-range` in CSS to load character sets on demand:

```css
/* Latin only */
@font-face {
  font-family: 'Inter';
  src: url('/fonts/Inter-latin.woff2') format('woff2');
  unicode-range: U+0000-00FF, U+0131, U+0152-0153, U+02BB-02BC, U+2000-206F;
}

/* Arabic (loaded only if Arabic characters appear on page) */
@font-face {
  font-family: 'Inter';
  src: url('/fonts/Inter-arabic.woff2') format('woff2');
  unicode-range: U+0600-06FF, U+200C-200E, U+2010-2011, U+204F, U+2E41, U+FB50-FDFF, U+FE80-FEFC;
}
```

### Preloading (Critical Font Only)

```html
<!-- Only preload the ONE font used above the fold -->
<link rel="preload" href="/fonts/Inter-Variable.woff2"
      as="font" type="font/woff2" crossorigin>

<!-- Preconnect to Google Fonts if using them -->
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
```

**Performance budget:** Total font payload under 200KB. Prefer WOFF2 exclusively (95%+ browser support, Brotli compression built in).

---

## 3. Image Optimization

### Format Selection

| Format | Best For | Compression | Browser Support |
|--------|----------|-------------|-----------------|
| **AVIF** | Photos, complex images | Best (50% smaller than JPEG) | 95%+ (2026) |
| **WebP** | Photos, graphics with alpha | Good (25-35% smaller than JPEG) | 97%+ |
| **JPEG** | Fallback for older browsers | Baseline | Universal |
| **PNG** | Graphics needing lossless transparency | Large files | Universal |
| **SVG** | Icons, logos, illustrations | Infinitely scalable, tiny | Universal |

### The `<picture>` Element (Format Negotiation + Art Direction)

```html
<!-- Format negotiation: browser picks best supported format -->
<picture>
  <source srcset="hero.avif" type="image/avif">
  <source srcset="hero.webp" type="image/webp">
  <img src="hero.jpg" alt="Hero image" width="1200" height="600"
       loading="eager" fetchpriority="high" decoding="async">
</picture>

<!-- Art direction: different crops for different viewports -->
<picture>
  <source media="(min-width: 1024px)"
          srcset="hero-wide.avif 1200w, hero-wide-2x.avif 2400w"
          sizes="100vw" type="image/avif">
  <source media="(min-width: 640px)"
          srcset="hero-medium.avif 800w" sizes="100vw" type="image/avif">
  <source srcset="hero-mobile.avif 400w" sizes="100vw" type="image/avif">
  <img src="hero-fallback.jpg" alt="Product showcase"
       width="400" height="400" loading="eager">
</picture>
```

### Responsive Images with `srcset` and `sizes`

```html
<!-- Resolution switching (same image, different sizes) -->
<img
  srcset="
    product-400.avif 400w,
    product-800.avif 800w,
    product-1200.avif 1200w,
    product-1600.avif 1600w"
  sizes="
    (min-width: 1280px) 33vw,
    (min-width: 768px) 50vw,
    100vw"
  src="product-800.avif"
  alt="Product photo"
  width="800" height="600"
  loading="lazy" decoding="async"
>
```

### Preventing CLS with Aspect Ratio

```css
/* Modern: CSS aspect-ratio */
img {
  aspect-ratio: 16 / 9;
  width: 100%;
  height: auto;
  object-fit: cover;
}

/* Always include width and height attributes on <img> */
/* The browser uses them to calculate aspect ratio before load */
```

```html
<img src="photo.avif" alt="..." width="800" height="450"
     style="aspect-ratio: 16/9; width: 100%; height: auto;">
```

### Lazy Loading

```html
<!-- Native lazy loading (below the fold only) -->
<img src="photo.avif" alt="..." loading="lazy" decoding="async"
     width="400" height="300">

<!-- NEVER lazy-load the LCP image -->
<img src="hero.avif" alt="..." loading="eager" fetchpriority="high"
     width="1200" height="600">
```

### Blur-Up / LQIP (Low Quality Image Placeholder)

```html
<!-- Inline a tiny base64 blur as background -->
<div class="img-wrapper" style="background-image: url('data:image/jpeg;base64,...'); background-size: cover;">
  <img src="full-image.avif" alt="..." loading="lazy"
       onload="this.parentElement.style.backgroundImage='none'"
       style="opacity: 0; transition: opacity 0.4s;"
       onload="this.style.opacity=1">
</div>
```

```css
/* CSS blur-up technique */
.img-wrapper {
  position: relative;
  overflow: hidden;
}
.img-wrapper::before {
  content: '';
  position: absolute;
  inset: 0;
  background: var(--lqip-bg);
  filter: blur(20px);
  transform: scale(1.1); /* prevent blur edges showing */
  transition: opacity 0.4s;
}
.img-wrapper.loaded::before {
  opacity: 0;
}
```

### Real-World Impact

A case study: converting a 480KB hero JPEG to AVIF at quality 45 (110KB) with WebP/JPEG fallback:
- LCP: 3.6s to 2.1s (41% improvement)
- CLS: 0.16 to 0.02 (fixed dimensions)
- Total image bytes: 2.8MB to 0.95MB (66% reduction)

---

## 4. CSS Performance

### `content-visibility: auto` (Rendering Skip for Off-Screen Content)

Tells the browser to skip layout, paint, and rendering for off-screen content until it scrolls into view. Can provide a 7x rendering performance boost on initial load.

```css
/* Apply to large sections that are below the fold */
.page-section {
  content-visibility: auto;
  contain-intrinsic-size: auto 500px; /* estimated height to prevent CLS */
}

/* For list items in long scrollable lists */
.list-item {
  content-visibility: auto;
  contain-intrinsic-size: auto 80px;
}
```

**Key rules:**
- `contain-intrinsic-size` MUST be set to prevent layout jumps (the browser needs estimated dimensions)
- Use `auto` keyword in `contain-intrinsic-size` so the browser remembers the real size after first render
- Don't apply to above-the-fold content (it's already visible)
- Works in all major browsers as of September 2025 (Baseline)

### CSS `contain` Property

Manually tell the browser which parts of the page are independent:

```css
/* Full containment: element doesn't affect layout outside its box */
.widget {
  contain: layout paint style;
}

/* Size containment: element's size doesn't depend on children */
.fixed-size-container {
  contain: size layout paint;
}

/* Inline-size containment: required for container queries */
.container-query-parent {
  container-type: inline-size; /* implicitly sets contain: inline-size layout style */
}
```

| Value | What It Isolates |
|-------|------------------|
| `layout` | Element's layout is independent of the rest of the page |
| `paint` | Element's descendants don't display outside its bounds |
| `style` | Counters and quotes scoping |
| `size` | Element can be sized without examining its descendants |
| `inline-size` | Only inline axis (width in horizontal writing), needed for container queries |

### `will-change` (GPU Layer Promotion)

```css
/* Correct: apply BEFORE animation, remove AFTER */
.about-to-animate {
  will-change: transform, opacity;
}

/* WRONG: never apply globally or permanently */
* { will-change: transform; } /* wastes GPU memory */
```

```js
// Best practice: apply via JS just before animation
el.style.willChange = 'transform, opacity';
el.addEventListener('transitionend', () => {
  el.style.willChange = 'auto';
}, { once: true });
```

**Rules:**
- Each `will-change` element gets its own GPU compositor layer
- Too many layers = increased memory usage and potential slowdown
- Only use for elements that will actually animate in the next frame
- Chrome may auto-promote elements with CSS animations (no `will-change` needed)

### Avoiding Layout Thrashing

Layout thrashing happens when JavaScript repeatedly reads and writes DOM properties in an interleaved pattern, forcing the browser to recalculate layout on every read:

```js
// BAD: forces layout recalculation on every iteration
items.forEach(item => {
  const height = item.offsetHeight;      // READ (forces layout)
  item.style.height = height + 10 + 'px'; // WRITE (invalidates layout)
});

// GOOD: batch reads, then batch writes
const heights = items.map(item => item.offsetHeight); // all READs
items.forEach((item, i) => {
  item.style.height = heights[i] + 10 + 'px';         // all WRITEs
});
```

**Properties that trigger layout when read:**
`offsetTop`, `offsetHeight`, `scrollTop`, `scrollHeight`, `clientTop`, `clientHeight`, `getComputedStyle()`, `getBoundingClientRect()`

### Composite-Only Animations

The rendering pipeline: **Style > Layout > Paint > Composite**

| Animation Property | Triggers | Performance |
|-------------------|----------|-------------|
| `transform` | Composite only | Best (GPU) |
| `opacity` | Composite only | Best (GPU) |
| `filter` | Paint + Composite | OK (being improved) |
| `background-color` | Paint + Composite | OK (Chrome auto-accelerating) |
| `clip-path` | Paint + Composite | OK (Chrome auto-accelerating) |
| `width`, `height` | Layout + Paint + Composite | Bad |
| `top`, `left`, `right`, `bottom` | Layout + Paint + Composite | Bad |
| `margin`, `padding` | Layout + Paint + Composite | Bad |
| `border-width` | Layout + Paint + Composite | Bad |
| `font-size` | Layout + Paint + Composite | Bad |

**Target:** 16.66ms per frame for 60fps. Only `transform` and `opacity` are guaranteed to stay within this budget.

```css
/* Use transform instead of position changes */
.slide-in {
  transform: translateX(-100%);
  transition: transform 0.3s cubic-bezier(0.16, 1, 0.3, 1);
}
.slide-in.active {
  transform: translateX(0);
}

/* Use transform: scale() instead of width/height changes */
.grow {
  transform: scale(0);
  transition: transform 0.3s cubic-bezier(0.16, 1, 0.3, 1);
}
.grow.active {
  transform: scale(1);
}

/* Animate height with grid-template-rows instead of height */
.collapsible {
  display: grid;
  grid-template-rows: 0fr;
  transition: grid-template-rows 0.3s cubic-bezier(0.16, 1, 0.3, 1);
}
.collapsible.open {
  grid-template-rows: 1fr;
}
.collapsible > div {
  overflow: hidden;
}
```

### `requestAnimationFrame` Best Practices

```js
// GOOD: use rAF for visual updates
function animateScroll() {
  const scrollY = window.scrollY;
  header.style.transform = `translateY(${Math.min(scrollY * 0.5, 100)}px)`;
  requestAnimationFrame(animateScroll);
}
requestAnimationFrame(animateScroll);

// BAD: using setInterval or setTimeout for animation
setInterval(() => {
  el.style.left = parseFloat(el.style.left) + 1 + 'px'; // jank
}, 16);

// GOOD: cancel when done
let rafId;
function loop() {
  /* ... */
  rafId = requestAnimationFrame(loop);
}
rafId = requestAnimationFrame(loop);
// Cleanup:
cancelAnimationFrame(rafId);
```

---

## 5. Bundle Impact of Design Choices

### Font File Sizes (Typical WOFF2)

| Font | Weight (WOFF2) | Notes |
|------|---------------|-------|
| Inter Variable | ~100KB | Full Latin, all weights |
| Inter Variable (subset) | ~40KB | Latin only, all weights |
| Inter 400+700 static | ~50KB (2 files) | Two HTTP requests |
| Roboto Variable | ~90KB | Full Latin |
| Font Awesome (full) | ~200KB+ | All 2,000+ icons |
| Material Icons | ~150KB | All icons |

### Icon Library Comparison

| Approach | File Size | Pros | Cons |
|----------|-----------|------|------|
| **SVG Sprites** | Depends on count (typically 10-50KB for 50 icons) | Tree-shakeable, crisp at any size, styleable with CSS, accessible | Requires build step for sprite sheet |
| **Individual Inline SVGs** | 0.5-2KB each | Best tree-shaking, full CSS control | Many DOM nodes if overused |
| **Icon Fonts** | 50-200KB (full set) | Simple to use, cached well | No tree-shaking, anti-aliasing issues, all-or-nothing download |
| **CSS-only Icons** | 0KB (pure CSS) | No HTTP request | Limited complexity, hard to maintain |

**2026 recommendation:** Individual inline SVGs with tree-shaking (e.g., `lucide-react`, `@heroicons/react`). Import only what you use. 78% of major websites now use SVG for at least some icons.

```tsx
// Tree-shaken SVG icons (only imported icons are bundled)
import { Search, Menu, X } from 'lucide-react';
// Each icon: ~0.5KB in bundle
```

### CSS Custom Properties vs Tailwind Classes

| Approach | Bundle Impact | Runtime Impact |
|----------|---------------|----------------|
| CSS Custom Properties | Minimal (just variable declarations) | Tiny overhead for `var()` resolution |
| Tailwind CSS (with purge) | 10-30KB (after tree-shaking) | Zero runtime, static classes |
| CSS-in-JS (styled-components) | 12KB+ runtime | Runtime style injection, SSR needed |
| Tailwind (without purge) | 3-4MB | Never ship unpurged Tailwind |

---

## 6. Subpixel Rendering & Retina Polish

### Why Shadows/Borders Look Different on Retina

Retina displays (2x, 3x pixel density) render fractional CSS values differently than 1x displays. A `1px` border is actually 2 or 3 physical pixels on retina, making it look thicker. Shadows with small offsets may appear blurred or misaligned when offset values don't align with device pixel boundaries.

### `box-shadow` vs `filter: drop-shadow()`

| Property | Behavior | Performance | Use When |
|----------|----------|-------------|----------|
| `box-shadow` | Applies to the element's rectangular box | CPU-rendered | Cards, buttons, containers with solid shapes |
| `filter: drop-shadow()` | Follows the element's alpha transparency (shape) | May be GPU-accelerated | Non-rectangular shapes, SVG icons, images with transparency |

```css
/* box-shadow: works on the rectangle */
.card { box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08); }

/* drop-shadow: follows the shape */
.svg-icon { filter: drop-shadow(0 2px 4px rgba(0, 0, 0, 0.15)); }

/* IMPORTANT: drop-shadow doesn't support spread or inset */
/* drop-shadow(offset-x offset-y blur color) — no spread parameter */
```

### Hairline Borders (True 1-Physical-Pixel Lines)

```css
/* Method 1: 0.5px border (works on retina Webkit/Blink) */
@media (-webkit-min-device-pixel-ratio: 2), (min-resolution: 2dppx) {
  .hairline-border {
    border-width: 0.5px;
  }
}

/* Method 2: border-image with gradient (works everywhere) */
.hairline-border {
  border: none;
  border-bottom: 1px solid transparent;
  border-image: linear-gradient(to right, rgba(0,0,0,0.12), rgba(0,0,0,0.12)) 1;
  /* On retina, this renders as a true 1-physical-pixel line */
}

/* Method 3: transform scale (most reliable cross-browser) */
.hairline::after {
  content: '';
  position: absolute;
  left: 0;
  right: 0;
  bottom: 0;
  height: 1px;
  background: rgba(0, 0, 0, 0.12);
  transform: scaleY(0.5);
  transform-origin: bottom;
}

@media (-webkit-min-device-pixel-ratio: 3), (min-resolution: 3dppx) {
  .hairline::after {
    transform: scaleY(0.333);
  }
}
```

### Subpixel Anti-Aliasing Issue with Shadows

Shadows with fractional offset values (e.g., `0.5px`) may render inconsistently across browsers. Stick to whole pixel values for shadow offsets, and use even numbers for blur radius:

```css
/* GOOD: whole pixel offsets */
.shadow { box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1); }

/* AVOID: fractional offsets */
.shadow { box-shadow: 0 1.5px 6px rgba(0, 0, 0, 0.1); }
```

---

## 7. Anti-Aliasing (`-webkit-font-smoothing`)

### When to Use `antialiased`

```css
/* Grayscale anti-aliasing (thinner, lighter text) */
.dark-bg-text {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale; /* Firefox 127 and below */
}
```

**As of Firefox 128+**, `-webkit-font-smoothing` is supported, so `-moz-osx-font-smoothing` is no longer strictly necessary.

**Use antialiased when:**
- Light text on dark backgrounds (subpixel rendering makes light-on-dark text look too heavy/bloated)
- Icon fonts (prevents color fringing on icons)
- Very thin font weights (< 300) that look too thick with subpixel rendering

**Do NOT use antialiased when:**
- Dark text on light backgrounds (subpixel rendering is superior here, clearer and more readable)
- Body text at normal weights (subpixel rendering increases effective resolution 3x)
- Accessibility-focused content (antialiased reduces contrast, harming readability)

**Key fact:** This property only affects macOS. It has no effect on Windows, Linux, iOS, or Android. It's a macOS-only setting because macOS is the only platform where browsers still use subpixel anti-aliasing.

```css
/* WRONG: applying globally */
* { -webkit-font-smoothing: antialiased; } /* reduces readability for body text */

/* RIGHT: applying only where needed */
.dark-section,
[data-theme="dark"] {
  -webkit-font-smoothing: antialiased;
}
```

---

## 8. Focus Indicators

### Custom Focus Ring (Works on All Backgrounds)

The double-outline technique ensures visibility on any background color:

```css
/* Two-layer focus ring: one dark, one light */
:focus-visible {
  outline: 2px solid #005fcc;         /* dark ring */
  outline-offset: 2px;
  box-shadow: 0 0 0 4px rgba(255, 255, 255, 0.8); /* light halo behind */
}

/* Alternative: outline + box-shadow stack */
:focus-visible {
  outline: 2px solid white;
  outline-offset: 2px;
  box-shadow: 0 0 0 6px #005fcc;
}
```

As long as the two layers have a 9:1+ contrast ratio with each other, at least one will always maintain sufficient contrast with any solid background.

### `:focus-visible` vs `:focus`

```css
/* :focus — triggers on ANY focus (mouse click, keyboard, programmatic) */
/* :focus-visible — triggers only when the browser determines focus should be visible */
/* (keyboard navigation, not mouse clicks) */

/* RECOMMENDED pattern: */
:focus-visible {
  outline: 2px solid var(--color-accent);
  outline-offset: 2px;
}

/* AVOID this older pattern: */
:focus { outline: none; }  /* removes focus for everyone */
:focus-visible { outline: 2px solid blue; } /* adds it back for keyboard */
/* Problem: some browsers may not support :focus-visible, leaving no focus indicator */

/* SAFE pattern with progressive enhancement: */
:focus {
  outline: 2px solid var(--color-accent);
  outline-offset: 2px;
}
:focus:not(:focus-visible) {
  outline: none;  /* remove for mouse users in supporting browsers */
}
```

### WCAG Requirements for Focus Indicators

- Minimum 2px thick (WCAG 2.4.7)
- At least 3:1 contrast ratio against the background
- At least 3:1 contrast against the unfocused state of the component
- Must not be obscured by sticky headers/footers (WCAG 2.4.11)
- Focus indicator area must be at least as large as a 2px thick perimeter around the component (WCAG 2.4.13, AAA)

---

## 9. Selection Styling

```css
/* Custom selection colors */
::selection {
  background: oklch(75% 0.15 250);  /* brand-tinted selection */
  color: #ffffff;
}

/* Prevent ugly blue highlights on interactive elements */
.interactive-element {
  -webkit-user-select: none;
  user-select: none;
}

/* But NEVER disable selection on text content */
.article-body {
  -webkit-user-select: text;
  user-select: text;
}

/* Tap highlight on mobile (remove default blue flash) */
.btn {
  -webkit-tap-highlight-color: transparent;
}
```

---

## 10. Smooth Color Transitions (OKLCH vs sRGB)

### The Problem with sRGB Interpolation

When transitioning between two colors in sRGB, the path goes through a "muddy middle" because sRGB is not perceptually uniform. A gradient from blue to yellow passes through desaturated gray in sRGB.

### OKLCH Interpolation (Recommended)

OKLCH is perceptually uniform: equal numeric changes produce equal visual changes. Gradients stay vibrant, transitions look natural.

```css
/* Gradient interpolation in OKLCH (vibrant, no gray middle) */
.gradient-vibrant {
  background: linear-gradient(in oklch, oklch(60% 0.25 250), oklch(85% 0.2 90));
}

/* Compare: same colors in sRGB (muddy gray in the middle) */
.gradient-muddy {
  background: linear-gradient(#2563eb, #eab308);
}

/* Hue interpolation direction */
.gradient-short {
  background: linear-gradient(in oklch shorter hue, oklch(60% 0.2 30), oklch(60% 0.2 330));
  /* Takes the short path around the hue wheel */
}
.gradient-long {
  background: linear-gradient(in oklch longer hue, oklch(60% 0.2 30), oklch(60% 0.2 330));
  /* Takes the long path (rainbow) */
}

/* Color transitions also benefit from OKLCH */
.btn {
  background: oklch(55% 0.2 250);
  transition: background 0.2s;
}
.btn:hover {
  background: oklch(48% 0.22 250);
  /* Darker + slightly more chromatic, perceptually smooth */
}
```

### `color-mix()` in OKLCH

```css
:root {
  --brand: oklch(55% 0.2 250);

  /* Generate shades programmatically */
  --brand-light: color-mix(in oklch, var(--brand) 30%, white);
  --brand-dark: color-mix(in oklch, var(--brand) 70%, black);
  --brand-muted: color-mix(in oklch, var(--brand) 50%, oklch(60% 0 0));

  /* Semi-transparent variants */
  --brand-10: color-mix(in oklch, var(--brand) 10%, transparent);
  --brand-20: color-mix(in oklch, var(--brand) 20%, transparent);
}
```

### Browser Support

OKLCH and `color-mix()` are Baseline as of late 2023, supported in Chrome 111+, Firefox 113+, Safari 16.4+. Over 95% global coverage in 2026.

---

## 11. Print Styles

```css
@media print {
  /* Hide non-essential UI */
  nav, footer, .sidebar, .fab, .toast,
  .cookie-banner, button, .no-print {
    display: none !important;
  }

  /* Reset backgrounds and colors for ink saving */
  body {
    background: white !important;
    color: black !important;
    font-size: 12pt;
    line-height: 1.5;
  }

  /* Show link URLs */
  a[href]::after {
    content: " (" attr(href) ")";
    font-size: 0.8em;
    color: #666;
  }
  /* Don't show URLs for internal/javascript links */
  a[href^="#"]::after,
  a[href^="javascript"]::after {
    content: none;
  }

  /* Page break control */
  h1, h2, h3 {
    page-break-after: avoid;
    break-after: avoid;
  }
  table, figure, img {
    page-break-inside: avoid;
    break-inside: avoid;
  }
  p {
    orphans: 3; /* min lines at bottom of page */
    widows: 3;  /* min lines at top of next page */
  }

  /* Force black borders (colored borders waste ink) */
  table, th, td {
    border: 1px solid #333 !important;
  }

  /* Full-width content */
  .container, .content {
    max-width: 100% !important;
    padding: 0 !important;
    margin: 0 !important;
  }
}
```

---

## 12. Reduced Motion

### Core Pattern

```css
@media (prefers-reduced-motion: reduce) {
  /* Option 1: eliminate all animations */
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

### Better Approach: Reduce, Don't Eliminate

```css
/* Default: full animation */
.card-enter {
  animation: slideUp 0.6s cubic-bezier(0.16, 1, 0.3, 1) forwards;
}

@keyframes slideUp {
  from { opacity: 0; transform: translateY(24px); }
  to   { opacity: 1; transform: translateY(0); }
}

/* Reduced motion: instant fade (no spatial movement) */
@media (prefers-reduced-motion: reduce) {
  .card-enter {
    animation: fadeOnly 0.2s ease forwards;
  }

  @keyframes fadeOnly {
    from { opacity: 0; }
    to   { opacity: 1; }
  }
}
```

### What to Reduce vs Keep

| Animation Type | Full Motion | Reduced Motion |
|----------------|------------|----------------|
| Slide/fly in | translateY + opacity | Opacity fade only |
| Parallax scrolling | Remove entirely | Static positioning |
| Auto-playing video/GIF | Normal playback | Paused with play button |
| Hover scale/rotate | Full transform | Color/opacity change only |
| Page transitions | Slide/wipe | Instant swap or fast crossfade |
| Loading spinners | Keep (essential) | Keep (essential) |
| Progress indicators | Keep (essential) | Keep (essential) |
| Focus ring transitions | Fast transition OK | Instant is fine |
| Scroll-triggered reveals | Slide + fade | Already visible, no animation |

### JavaScript Detection

```js
const prefersReducedMotion = window.matchMedia('(prefers-reduced-motion: reduce)');

if (prefersReducedMotion.matches) {
  // Disable GSAP timelines
  gsap.globalTimeline.timeScale(100);
  // Or skip animation setup entirely
}

// Listen for changes (user toggles setting)
prefersReducedMotion.addEventListener('change', (e) => {
  if (e.matches) {
    // User enabled reduced motion
  }
});
```

---

## 13. High Contrast & Forced Colors Mode

### `forced-colors` Media Query (Windows High Contrast Mode)

When forced colors mode is active, the browser overrides all colors with user-chosen system colors. Your custom colors, shadows, and backgrounds are replaced.

```css
@media (forced-colors: active) {
  /* Fix elements that lose their visual boundaries */
  .card {
    border: 1px solid ButtonText; /* visible border replaces shadow */
  }

  /* Fix custom checkboxes/toggles */
  .checkbox-custom {
    forced-color-adjust: none; /* opt out of color forcing for this element */
    /* Only use this when you're handling contrast yourself */
  }

  /* Ensure focus is visible */
  :focus-visible {
    outline: 2px solid Highlight;
  }
}
```

### System Color Keywords (for `forced-colors: active`)

| Keyword | Meaning |
|---------|---------|
| `Canvas` | Page background |
| `CanvasText` | Default text color |
| `LinkText` | Link color |
| `ButtonFace` | Button background |
| `ButtonText` | Button text |
| `Highlight` | Selection/focus background |
| `HighlightText` | Selection/focus text |
| `GrayText` | Disabled text |

### `prefers-contrast` Media Query

```css
/* User has requested higher contrast */
@media (prefers-contrast: more) {
  :root {
    --border: #000;
    --text-secondary: #333; /* darken secondary text */
  }
  .card {
    border: 2px solid var(--border); /* make borders more visible */
  }
}

/* User has requested lower contrast */
@media (prefers-contrast: less) {
  :root {
    --text-primary: #333;
    --surface: #f0f0f0;
  }
}
```

### Design Rules for Forced Colors

1. **Use semantic HTML.** The browser automatically applies system colors to semantic elements (`<button>`, `<a>`, `<input>`)
2. **Don't hide information in color alone.** Use borders, icons, text labels
3. **Invisible borders technique:** give elements a `transparent` border in normal mode, so when forced colors activates, the border becomes visible at the system color
4. **Test in Chrome DevTools:** Rendering panel > Emulate CSS media feature > `forced-colors: active`
5. **`-ms-high-contrast` is deprecated.** Use `forced-colors` instead (Edge 138+ removed the old one)

```css
/* Invisible border technique (progressive enhancement) */
.card {
  border: 1px solid transparent; /* invisible in normal mode */
  box-shadow: 0 2px 8px rgba(0,0,0,0.1); /* visual boundary */
}
/* In forced-colors mode: shadow is removed, but border becomes visible
   at ButtonText color automatically */
```

---

## 14. Cursor Design

```css
/* Interactive states */
.draggable       { cursor: grab; }
.draggable:active { cursor: grabbing; }
.zoomable        { cursor: zoom-in; }
.zoomable.zoomed { cursor: zoom-out; }
.resizable-col   { cursor: col-resize; }
.resizable-row   { cursor: row-resize; }
.text-content    { cursor: text; }
.disabled-action { cursor: not-allowed; }
.loading-action  { cursor: wait; }
.help-trigger    { cursor: help; }

/* Custom cursor */
.custom-cursor {
  cursor: url('/cursors/custom-pointer.svg') 12 12, auto;
  /* 12 12 = hotspot coordinates (where the click registers) */
}

/* Hide cursor for immersive experiences */
.fullscreen-video:fullscreen {
  cursor: none;
}

/* Crosshair for precision tools */
.canvas-tool { cursor: crosshair; }
```

---

## 15. Container Query Patterns

### Component-Level Breakpoints

```css
/* Define a containment context */
.card-container {
  container-type: inline-size;
  container-name: card;
}

/* Component adapts to its container, not the viewport */
.card {
  display: grid;
  gap: 1rem;
}

@container card (min-width: 400px) {
  .card {
    grid-template-columns: 200px 1fr;
  }
}

@container card (min-width: 600px) {
  .card {
    grid-template-columns: 250px 1fr;
    gap: 1.5rem;
  }
  .card-title {
    font-size: 1.5rem;
  }
}
```

### Container Query Units

```css
@container card (min-width: 300px) {
  .card-title {
    font-size: clamp(1rem, 5cqi, 2rem);
    /* cqi = 1% of the container's inline size */
    /* cqb = 1% of the container's block size */
    /* cqw/cqh = container width/height */
  }
  .card-body {
    padding: 3cqi;
  }
}
```

### When Container Queries vs Media Queries

| Use Case | Use |
|----------|-----|
| Page layout shifts (sidebar shows/hides) | Media queries |
| Navigation bar reconfiguration | Media queries |
| Reusable component in multiple contexts | Container queries |
| Card that lives in sidebar AND main area | Container queries |
| Dashboard widget at different sizes | Container queries |
| Typography scaling with viewport | Media queries (or `clamp()`) |
| Typography scaling with component | Container queries |

**Browser support:** 95%+ global coverage in 2026 (Chrome 105+, Firefox 110+, Safari 16+).

---

## 16. Fluid Everything with `clamp()`

### The Formula

```
clamp(minimum, preferred, maximum)

/* preferred value formula for viewport-based scaling: */
/* preferred = slope * viewport + intercept */
/* where: */
/*   slope = (max - min) / (maxViewport - minViewport) */
/*   intercept = min - slope * minViewport */
```

### Fluid Spacing System

```css
:root {
  /* Spacing that scales from 320px to 1440px viewport */
  --space-xs:  clamp(0.25rem, 0.2rem + 0.25vw, 0.5rem);    /* 4-8px */
  --space-sm:  clamp(0.5rem, 0.4rem + 0.5vw, 1rem);         /* 8-16px */
  --space-md:  clamp(1rem, 0.8rem + 1vw, 2rem);              /* 16-32px */
  --space-lg:  clamp(1.5rem, 1.2rem + 1.5vw, 3rem);          /* 24-48px */
  --space-xl:  clamp(2rem, 1.5rem + 2.5vw, 5rem);            /* 32-80px */
  --space-2xl: clamp(3rem, 2rem + 5vw, 8rem);                /* 48-128px */

  /* Fluid padding */
  --page-padding: clamp(1rem, 0.5rem + 2.5vw, 3rem);        /* 16-48px */
}

/* Usage */
.section {
  padding-block: var(--space-xl);
  padding-inline: var(--page-padding);
}
.card {
  padding: var(--space-md);
  gap: var(--space-sm);
}
```

### Fluid Font Size

```css
:root {
  --text-base: clamp(1rem, 0.93rem + 0.34vw, 1.19rem);     /* 16-19px */
  --text-lg:   clamp(1.25rem, 1.1rem + 0.61vw, 1.58rem);    /* 20-25px */
  --text-xl:   clamp(1.56rem, 1.31rem + 1.03vw, 2.11rem);   /* 25-34px */
  --text-2xl:  clamp(2rem, 1.5rem + 2.5vw, 3.5rem);         /* 32-56px */
}
```

### Fluid Gap in Grids

```css
.card-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(min(280px, 100%), 1fr));
  gap: clamp(1rem, 0.5rem + 2vw, 2rem);
}
```

---

## 17. Logical Properties (RTL/LTR Support)

### Physical to Logical Mapping

| Physical | Logical | Notes |
|----------|---------|-------|
| `margin-left` | `margin-inline-start` | Start of reading direction |
| `margin-right` | `margin-inline-end` | End of reading direction |
| `margin-top` | `margin-block-start` | Start of block flow |
| `margin-bottom` | `margin-block-end` | End of block flow |
| `padding-left/right` | `padding-inline` | Shorthand for both inline sides |
| `padding-top/bottom` | `padding-block` | Shorthand for both block sides |
| `width` | `inline-size` | Size along the inline axis |
| `height` | `block-size` | Size along the block axis |
| `min-width` | `min-inline-size` | |
| `max-height` | `max-block-size` | |
| `top` | `inset-block-start` | |
| `right` | `inset-inline-end` | |
| `left` | `inset-inline-start` | |
| `border-left` | `border-inline-start` | |
| `border-radius: 8px 0 0 8px` | `border-start-start-radius: 8px; border-end-start-radius: 8px` | Per-corner |
| `text-align: left` | `text-align: start` | |
| `float: left` | `float: inline-start` | |

### Practical Usage

```css
/* Logical properties auto-flip for RTL */
.card {
  padding-inline: 1.5rem;    /* left+right in LTR, right+left in RTL */
  padding-block: 1rem;       /* top+bottom regardless of direction */
  margin-inline-end: 1rem;   /* right margin in LTR, left margin in RTL */
  border-inline-start: 3px solid var(--accent); /* left border in LTR, right in RTL */
}

.sidebar {
  inline-size: 280px;        /* width in horizontal writing mode */
  inset-inline-start: 0;     /* left: 0 in LTR, right: 0 in RTL */
}

/* The shorthand versions */
.element {
  margin-inline: 1rem 2rem;  /* start: 1rem, end: 2rem */
  padding-block: 0.5rem;     /* top and bottom both 0.5rem */
  inset: 0;                  /* all four sides (already logical) */
}
```

### Setting Up RTL

```html
<html lang="ar" dir="rtl">
<!-- dir="rtl" triggers all logical properties to flip -->
```

```css
/* Detect direction in CSS */
:dir(rtl) {
  /* RTL-specific overrides if needed */
}
```

**Tailwind v4:** logical properties are becoming the default. `ms-4` = `margin-inline-start`, `ps-4` = `padding-inline-start`.

---

## 18. Dynamic Viewport Units

### The Mobile Address Bar Problem

`100vh` on mobile always equals the **largest** viewport (with browser chrome hidden). This causes content to be cut off behind the address bar when it's visible.

### The Three Unit Types

| Unit | Equals | When to Use |
|------|--------|-------------|
| `svh` (Small Viewport Height) | Viewport when all browser UI is **showing** (smallest possible) | Fixed-height elements that must always be fully visible |
| `lvh` (Large Viewport Height) | Viewport when all browser UI is **hidden** (largest possible) | Same as old `100vh` behavior |
| `dvh` (Dynamic Viewport Height) | Adjusts live as browser UI shows/hides | Full-screen hero sections, mobile-first layouts |

```css
/* Hero section: fills the visible viewport dynamically */
.hero {
  min-height: 100dvh;
}

/* Sticky footer layout */
.page-wrapper {
  min-height: 100dvh;
  display: grid;
  grid-template-rows: auto 1fr auto;
}

/* Full-screen modal */
.modal-overlay {
  height: 100dvh; /* or 100svh for safety */
  position: fixed;
  inset: 0;
}

/* Fallback for older browsers */
.hero {
  min-height: 100vh;       /* fallback */
  min-height: 100dvh;      /* modern override */
}
```

### Performance Warning

`dvh` updates dynamically as the user scrolls and the browser chrome appears/disappears. This can cause content to resize during scroll, which may:
- Trigger layout recalculations (affects INP)
- Cause visual jitter

**For scroll-sensitive elements:** use `svh` (stable, always the smallest viewport) instead of `dvh`.

### Corresponding Width/Inline Units

| Height | Width | Inline | Block |
|--------|-------|--------|-------|
| `svh` | `svw` | `svi` | `svb` |
| `lvh` | `lvw` | `lvi` | `lvb` |
| `dvh` | `dvw` | `dvi` | `dvb` |

Also: `svmin`/`svmax`, `lvmin`/`lvmax`, `dvmin`/`dvmax` (corresponding to `vmin`/`vmax`).

**Browser support:** all major browsers since early 2023, 95%+ global coverage.
