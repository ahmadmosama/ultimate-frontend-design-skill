# Layout & Responsive Design Reference

## 1. Grid Systems

### 12-Column Fluid Grid

```css
:root {
  --grid-columns: 12;
  --grid-gap: 1.5rem;
  --grid-margin: clamp(1rem, 5vw, 4rem);
}

.grid-container {
  display: grid;
  grid-template-columns: repeat(var(--grid-columns), 1fr);
  gap: var(--grid-gap);
  max-width: 90rem;
  margin-inline: var(--grid-margin);
}

.col-span-4 { grid-column: span 4; }
.col-span-6 { grid-column: span 6; }
.col-span-8 { grid-column: span 8; }
.col-span-full { grid-column: 1 / -1; }
```

### Auto-Fit Responsive Grid (No Media Queries)

```css
/* Cards that auto-wrap: min 280px each, fill available space */
.auto-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(min(280px, 100%), 1fr));
  gap: 1.5rem;
}

/* auto-fill: keeps empty tracks. auto-fit: collapses them. */
/* Use auto-fit for most card grids, auto-fill when you need consistent column widths. */
```

Tailwind: `grid grid-cols-[repeat(auto-fit,minmax(min(280px,100%),1fr))] gap-6`

### Sidebar + Main Content

```css
.sidebar-layout {
  display: grid;
  grid-template-columns: minmax(200px, 280px) 1fr;
  gap: 2rem;
  min-height: 100dvh;
}

/* Collapse on narrow screens */
@media (max-width: 768px) {
  .sidebar-layout { grid-template-columns: 1fr; }
}
```

Tailwind: `grid grid-cols-1 md:grid-cols-[minmax(200px,280px)_1fr] gap-8 min-h-dvh`

### Holy Grail Layout

```css
.holy-grail {
  display: grid;
  grid-template: "header header header" auto
                 "nav    main   aside" 1fr
                 "footer footer footer" auto
                 / 220px 1fr 220px;
  min-height: 100dvh;
}

header { grid-area: header; }
nav    { grid-area: nav; }
main   { grid-area: main; }
aside  { grid-area: aside; }
footer { grid-area: footer; }

@media (max-width: 768px) {
  .holy-grail {
    grid-template: "header" auto "main" 1fr "footer" auto / 1fr;
  }
  nav, aside { display: none; } /* move to overlay */
}
```

### Dashboard Grid

```css
.dashboard {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-auto-rows: minmax(180px, auto);
  gap: 1rem;
}

.widget-wide   { grid-column: span 2; }
.widget-tall   { grid-row: span 2; }
.widget-hero   { grid-column: span 4; grid-row: span 2; }

@media (max-width: 1024px) { .dashboard { grid-template-columns: repeat(2, 1fr); } }
@media (max-width: 640px)  { .dashboard { grid-template-columns: 1fr; } }
```

### Flexbox: Centering & Distribution

```css
/* Distribute nav items evenly, wrap on overflow */
.flex-bar {
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
  justify-content: space-between;
  align-items: center;
}

/* True center (vertical + horizontal) */
.flex-center { display: flex; align-items: center; justify-content: center; min-height: 100dvh; }
```

---

## 2. Breakpoint System

### Mobile-First Breakpoints

| Token | Min-width | Typical use |
|-------|-----------|-------------|
| `sm`  | 640px  | Large phone landscape, small adjustments |
| `md`  | 768px  | Tablet portrait, sidebar appears |
| `lg`  | 1024px | Tablet landscape / small laptop, full nav |
| `xl`  | 1280px | Desktop, multi-column content |
| `2xl` | 1536px | Large desktop, generous whitespace |

```css
/* Mobile-first: base styles are mobile, layer up */
.card-grid { grid-template-columns: 1fr; }

@media (min-width: 640px)  { .card-grid { grid-template-columns: repeat(2, 1fr); } }
@media (min-width: 1024px) { .card-grid { grid-template-columns: repeat(3, 1fr); } }
@media (min-width: 1280px) { .card-grid { grid-template-columns: repeat(4, 1fr); } }
```

Tailwind: `grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4`

### Container Queries (Component-Level Responsive)

```css
.card-wrapper {
  container-type: inline-size;
  container-name: card;
}

/* Respond to the wrapper's width, not the viewport */
@container card (min-width: 400px) {
  .card { flex-direction: row; }
  .card-image { width: 40%; }
}

@container card (min-width: 600px) {
  .card-title { font-size: 1.5rem; }
}
```

Tailwind (v3.4+): `@container/card (min-width: 400px)` via `@container` class on parent, then `@sm:flex-row` etc.

**When to use viewport vs. container queries:** viewport for page-level layout shifts (sidebar, nav). Container for reusable components (cards, widgets) that live in different-sized parents.

---

## 3. Container Widths

| Content type | Max width | Rationale |
|-------------|-----------|-----------|
| Prose / articles | `65ch` (~650px) | Optimal reading line length |
| Forms | `28rem` (448px) | Focused input, no scanning |
| Card grids | `72rem` (1152px) | 3-4 cards with comfortable gutters |
| Dashboard | `90rem` (1440px) | Dense but not stretched |
| Full-width hero | `100%` | Edge-to-edge impact |

```css
.prose-container  { max-width: 65ch; margin-inline: auto; padding-inline: 1.5rem; }
.form-container   { max-width: 28rem; margin-inline: auto; padding-inline: 1rem; }
.content-container { max-width: 72rem; margin-inline: auto; padding-inline: clamp(1rem, 5vw, 3rem); }
```

Tailwind: `max-w-prose mx-auto px-6` / `max-w-md mx-auto px-4` / `max-w-6xl mx-auto px-4 lg:px-12`

### Container Padding by Breakpoint

```css
.page-container {
  width: 100%;
  margin-inline: auto;
  padding-inline: 1rem;          /* mobile */
}
@media (min-width: 640px)  { .page-container { padding-inline: 1.5rem; } }
@media (min-width: 1024px) { .page-container { padding-inline: 2rem; } }
@media (min-width: 1280px) { .page-container { padding-inline: 3rem; } }
```

---

## 4. Responsive Patterns

### Stack to Horizontal

```css
.stack-to-row {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}
@media (min-width: 768px) { .stack-to-row { flex-direction: row; } }
```

Tailwind: `flex flex-col md:flex-row gap-4`

### Sidebar to Overlay

```css
.sidebar {
  position: fixed; inset-block: 0; left: 0;
  width: 280px; transform: translateX(-100%);
  transition: transform 0.3s ease;
  z-index: 40;
}
.sidebar.open { transform: translateX(0); }

@media (min-width: 1024px) {
  .sidebar { position: sticky; top: 0; transform: none; height: 100dvh; }
}
```

### Grid Column Reduction (3 -> 2 -> 1)

Tailwind: `grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-6`

### Show/Hide by Breakpoint

```css
.mobile-only  { display: block; }
.desktop-only { display: none; }

@media (min-width: 1024px) {
  .mobile-only  { display: none; }
  .desktop-only { display: block; }
}
```

Tailwind: `block lg:hidden` (mobile only) / `hidden lg:block` (desktop only)

### Responsive Spacing

```css
:root {
  --space-section: clamp(3rem, 8vw, 8rem);
  --space-block:   clamp(1.5rem, 4vw, 3rem);
  --space-element: clamp(0.75rem, 2vw, 1.5rem);
}

.section + .section { margin-top: var(--space-section); }
.block + .block     { margin-top: var(--space-block); }
```

Tailwind: `space-y-6 md:space-y-10 lg:space-y-16` on a parent flex-col.

---

## 5. Safe Areas

### iOS Safe Area Insets

```css
/* Handles notch, home indicator, status bar */
body {
  padding-top: env(safe-area-inset-top);
  padding-bottom: env(safe-area-inset-bottom);
  padding-left: env(safe-area-inset-left);
  padding-right: env(safe-area-inset-right);
}

/* Fixed bottom nav: account for home indicator */
.bottom-nav {
  position: fixed; bottom: 0; left: 0; right: 0;
  padding-bottom: calc(0.5rem + env(safe-area-inset-bottom));
  padding-left: env(safe-area-inset-left);
  padding-right: env(safe-area-inset-right);
}

/* REQUIRED: enable safe area env variables */
/* <meta name="viewport" content="viewport-fit=cover"> */
```

### Android System Bars

Android respects `env(safe-area-inset-*)` in modern Chrome. For older WebViews, use a fixed fallback:

```css
.bottom-bar {
  padding-bottom: calc(0.5rem + env(safe-area-inset-bottom, 0px));
}
```

### Notch Handling for Landscape

```css
@media (orientation: landscape) {
  .content {
    padding-left: calc(1rem + env(safe-area-inset-left));
    padding-right: calc(1rem + env(safe-area-inset-right));
  }
}
```

---

## 6. Compositional Techniques

### Overlapping Elements

```css
.overlap-stack {
  display: grid;
  grid-template: 1fr / 1fr;
}
.overlap-stack > * {
  grid-area: 1 / 1;
}
.overlap-text {
  align-self: end;
  padding: 2rem;
  background: linear-gradient(transparent, rgba(0,0,0,0.7));
  color: white;
}
```

### Bleeding Image (Break Out of Container)

```css
.full-bleed {
  width: 100vw;
  margin-left: calc(50% - 50vw);
}
```

### Offset / Asymmetric Grid

```css
.offset-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 2rem;
}
.offset-grid > :nth-child(even) {
  transform: translateY(3rem); /* staggered rhythm */
}
```

### Swiss / Modular Grid

Use a fine sub-grid (e.g., 6 or 12 columns) and let elements span different counts for controlled asymmetry. Best for editorial and portfolio layouts where visual tension is desirable.

```css
.swiss-grid {
  display: grid;
  grid-template-columns: repeat(6, 1fr);
  gap: 1rem;
}
.swiss-grid .feature { grid-column: 1 / 5; grid-row: 1 / 3; }
.swiss-grid .sidebar-item { grid-column: 5 / 7; }
```

---

## 7. Scroll Layouts

### Sticky Header

```css
.site-header {
  position: sticky;
  top: 0;
  z-index: 30;
  backdrop-filter: blur(12px);
  background: rgba(255, 255, 255, 0.85);
}
```

### Pinned Sidebar (Scrolls to a Point, Then Sticks)

```css
.sidebar {
  position: sticky;
  top: 5rem; /* below header */
  height: fit-content;
  max-height: calc(100dvh - 6rem);
  overflow-y: auto;
}
```

### Horizontal Scroll Section

```css
.h-scroll {
  display: flex;
  gap: 1rem;
  overflow-x: auto;
  scroll-snap-type: x mandatory;
  -webkit-overflow-scrolling: touch;
  padding-bottom: 0.5rem; /* room for scrollbar */
}
.h-scroll > * {
  flex: 0 0 min(300px, 80vw);
  scroll-snap-align: start;
}

/* Hide scrollbar visually (accessible scroll still works) */
.h-scroll { scrollbar-width: none; }
.h-scroll::-webkit-scrollbar { display: none; }
```

Tailwind: `flex gap-4 overflow-x-auto snap-x snap-mandatory` with children `snap-start shrink-0 w-[min(300px,80vw)]`

### Vertical Snap Scrolling (Full-Page Sections)

```css
.snap-container {
  height: 100dvh;
  overflow-y: auto;
  scroll-snap-type: y mandatory;
}
.snap-section {
  height: 100dvh;
  scroll-snap-align: start;
}
```

---

## 8. Spacing Rhythm

### Section vs. Inner Spacing

Keep a clear hierarchy: large gaps between page sections, moderate gaps between blocks within a section, small gaps between elements within a block.

```css
:root {
  --space-page-section: clamp(4rem, 10vw, 10rem);  /* between hero, features, testimonials */
  --space-inner-block:  clamp(1.5rem, 4vw, 3rem);   /* between heading + card grid */
  --space-element:      clamp(0.5rem, 1.5vw, 1rem);  /* between card title and description */
}
```

### Alternating Dense / Sparse

Alternate tight content blocks with generous whitespace sections to create breathing room and visual rhythm. Dense sections (card grids, data tables) feel active; sparse sections (CTAs, hero quotes) feel premium.

```css
.section-dense  { padding-block: var(--space-inner-block); }
.section-sparse { padding-block: var(--space-page-section); }
```

### Consistent Scale

Pick a base (e.g., 4px or 0.25rem) and stick to multiples: 4, 8, 12, 16, 24, 32, 48, 64, 96. Avoid arbitrary values. Tailwind's default spacing scale already follows this.

---

## 9. Common Layout Mistakes

| Mistake | Why it fails | Fix |
|---------|-------------|-----|
| Fixed pixel widths on containers | Breaks on any screen narrower than the value | Use `max-width` with percentage or `min()` |
| `100vh` for full-height | Doesn't account for mobile browser chrome | Use `100dvh` (dynamic viewport height) |
| Horizontal scroll from overflow | Element or padding exceeds viewport width | `overflow-x: hidden` on body, audit `100vw` usage (includes scrollbar) |
| Breakpoints based on devices | Device landscape changes yearly | Use content-driven breakpoints: when the layout breaks, add one |
| Hiding content with `display: none` for mobile | Content still loads, SEO may suffer | Consider restructuring over hiding. If hiding, use `content-visibility: hidden` for paint cost savings |
| Nested flex without `min-width: 0` | Flex children refuse to shrink below content size | Add `min-width: 0` (or `overflow: hidden`) to flex children |
| `gap` on older Safari flex | Not supported before Safari 14.1 | Use margin-based fallback or check your support matrix |
| Z-index wars | Competing `z-index` across unrelated stacking contexts | Define a z-index scale in custom properties: `--z-dropdown: 100; --z-overlay: 200; --z-modal: 300` |
| Forgetting safe areas | Content hidden behind notch or home indicator | Add `viewport-fit=cover` meta tag and `env(safe-area-inset-*)` padding |
| Text too wide on desktop | Lines exceed 80+ characters, hard to read | Cap prose containers at `65ch` max-width |
