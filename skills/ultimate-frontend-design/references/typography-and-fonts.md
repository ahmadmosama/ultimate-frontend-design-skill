# Typography & Fonts Reference

## 1. Font Pairing Strategies

### Serif + Sans-Serif

| Project Type | Heading (Serif) | Body (Sans) | Why |
|---|---|---|---|
| Editorial / Blog | Playfair Display | Source Sans 3 | High contrast signals long-form authority |
| Legal / Finance | Libre Baskerville | Inter | Classical trust cues with modern readability |
| Luxury E-commerce | Cormorant Garamond | Montserrat | Elegant display with neutral commerce body |
| Portfolio | DM Serif Display | DM Sans | Same superfamily, guaranteed harmony |

### Sans + Sans

| Project Type | Heading | Body | Why |
|---|---|---|---|
| SaaS Dashboard | Inter (700) | Inter (400) | Single family reduces load, wide glyph set |
| Developer Tool | JetBrains Mono | Inter | Mono heading signals technical, Inter for prose |
| Mobile App | SF Pro Display / Roboto | SF Pro Text / Roboto | Platform-native, no font loading cost |
| Startup Landing | Cabinet Grotesk | General Sans | Geometric display + humanist body avoids monotony |

### Display + Body

| Project Type | Display | Body | Why |
|---|---|---|---|
| Creative Agency | Clash Display | Satoshi | Sharp geometric display with friendly body |
| E-commerce Hero | Bebas Neue | Lato | Condensed all-caps hero with wide readable body |
| Event / Conference | Syne | Outfit | Quirky display with clean geometric body |

**Rule of thumb:** pair fonts that share x-height but differ in personality. Never pair two fonts that look almost-but-not-quite the same.

---

## 2. Fluid Type Scale System

### Conservative Scale (ratio 1.2 — Minor Third)

Best for: dashboards, SaaS, documentation, mobile-first.

```css
:root {
  --fs-xs:   clamp(0.694rem, 0.660rem + 0.17vw, 0.800rem);   /* 11-13px */
  --fs-sm:   clamp(0.833rem, 0.791rem + 0.21vw, 0.960rem);   /* 13-15px */
  --fs-base: clamp(1.000rem, 0.950rem + 0.25vw, 1.150rem);   /* 16-18px */
  --fs-md:   clamp(1.200rem, 1.140rem + 0.30vw, 1.383rem);   /* 19-22px */
  --fs-lg:   clamp(1.440rem, 1.368rem + 0.36vw, 1.660rem);   /* 23-27px */
  --fs-xl:   clamp(1.728rem, 1.642rem + 0.43vw, 1.991rem);   /* 28-32px */
  --fs-2xl:  clamp(2.074rem, 1.970rem + 0.52vw, 2.389rem);   /* 33-38px */
  --fs-3xl:  clamp(2.488rem, 2.364rem + 0.62vw, 2.867rem);   /* 40-46px */
}
```

### Dramatic Scale (ratio 1.333 — Perfect Fourth)

Best for: editorial, marketing, landing pages.

```css
:root {
  --fs-xs:   clamp(0.563rem, 0.520rem + 0.21vw, 0.702rem);   /* 9-11px  */
  --fs-sm:   clamp(0.750rem, 0.700rem + 0.25vw, 0.937rem);   /* 12-15px */
  --fs-base: clamp(1.000rem, 0.933rem + 0.33vw, 1.250rem);   /* 16-20px */
  --fs-md:   clamp(1.333rem, 1.244rem + 0.44vw, 1.666rem);   /* 21-27px */
  --fs-lg:   clamp(1.777rem, 1.659rem + 0.59vw, 2.221rem);   /* 28-36px */
  --fs-xl:   clamp(2.369rem, 2.212rem + 0.79vw, 2.961rem);   /* 38-47px */
  --fs-2xl:  clamp(3.157rem, 2.947rem + 1.05vw, 3.946rem);   /* 51-63px */
  --fs-3xl:  clamp(4.209rem, 3.928rem + 1.40vw, 5.261rem);   /* 67-84px */
}
```

### Tailwind Config Equivalent

```js
// tailwind.config.js
module.exports = {
  theme: {
    fontSize: {
      xs:   ['clamp(0.694rem, 0.660rem + 0.17vw, 0.800rem)', { lineHeight: '1.5' }],
      sm:   ['clamp(0.833rem, 0.791rem + 0.21vw, 0.960rem)', { lineHeight: '1.5' }],
      base: ['clamp(1.000rem, 0.950rem + 0.25vw, 1.150rem)', { lineHeight: '1.6' }],
      md:   ['clamp(1.200rem, 1.140rem + 0.30vw, 1.383rem)', { lineHeight: '1.4' }],
      lg:   ['clamp(1.440rem, 1.368rem + 0.36vw, 1.660rem)', { lineHeight: '1.3' }],
      xl:   ['clamp(1.728rem, 1.642rem + 0.43vw, 1.991rem)', { lineHeight: '1.2' }],
      '2xl': ['clamp(2.074rem, 1.970rem + 0.52vw, 2.389rem)', { lineHeight: '1.15' }],
      '3xl': ['clamp(2.488rem, 2.364rem + 0.62vw, 2.867rem)', { lineHeight: '1.1' }],
    },
  },
};
```

---

## 3. Tracking (Letter-Spacing) Rules

```css
:root {
  --tracking-tighter: -0.04em;  /* hero text, 48px+ */
  --tracking-tight:   -0.02em;  /* display headings, 32-48px */
  --tracking-heading:  -0.01em; /* h2-h3, 24-32px */
  --tracking-normal:   0;       /* body text */
  --tracking-wide:     0.02em;  /* captions, labels */
  --tracking-wider:    0.05em;  /* all-caps buttons, overlines */
  --tracking-widest:   0.10em;  /* all-caps section labels */
  --tracking-mono:    -0.02em;  /* monospace (compensate for wider glyphs) */
}
```

**Key principle:** large text needs negative tracking (optical tightening). Small all-caps text needs positive tracking (legibility). Body text at 0.

---

## 4. Line Height Rules

| Context | line-height | Why |
|---|---|---|
| Hero / Display (48px+) | 1.0 - 1.1 | Large text has built-in spacing, tight feels intentional |
| Heading (24-48px) | 1.15 - 1.25 | Moderate: readable without looking loose |
| Subheading (18-24px) | 1.3 - 1.4 | Transition zone between display and body |
| Body paragraph | 1.5 - 1.65 | Optimal saccade return sweep for 45-75 char lines |
| Body (long-form, 600+ words) | 1.6 - 1.75 | Extra breathing room reduces fatigue |
| Caption / Small text (<14px) | 1.4 - 1.5 | Smaller text needs proportionally more leading |
| UI labels / Buttons | 1.0 - 1.2 | Single line, vertically centered with padding |
| Code blocks | 1.5 - 1.6 | Matches body, avoids cramped appearance |
| Lists | 1.4 - 1.5 | Slightly tighter than body, items are short |

**Rule:** as font size increases, line-height decreases. Never use a unitless value above 2.0 or below 1.0.

---

## 5. Variable Fonts

### Basic @font-face Setup

```css
@font-face {
  font-family: 'Inter';
  src: url('/fonts/Inter-Variable.woff2') format('woff2-variations');
  font-weight: 100 900;        /* full weight range */
  font-style: normal;
  font-display: swap;
  font-variation-settings: 'opsz' 16; /* optical size default */
}
```

### Weight Animation

```css
.nav-link {
  font-variation-settings: 'wght' 400;
  transition: font-variation-settings 200ms ease;
}
.nav-link:hover {
  font-variation-settings: 'wght' 600;
}
```

This avoids layout shift that happens with toggling `font-weight` between separate static files.

### Optical Sizing

```css
/* Let the browser auto-adjust optical size to font-size */
h1 { font-optical-sizing: auto; font-size: var(--fs-3xl); }

/* Or control manually */
.caption {
  font-size: 12px;
  font-variation-settings: 'opsz' 12;  /* opens counters, thickens strokes */
}
.display {
  font-size: 72px;
  font-variation-settings: 'opsz' 72;  /* tightens details, refines contrast */
}
```

### Performance Benefits

| Approach | Total Weight | HTTP Requests |
|---|---|---|
| 6 static weights (woff2) | ~150-300KB | 6 |
| 1 variable font (woff2) | ~80-150KB | 1 |

Variable fonts also eliminate FOUT between weight changes and enable smooth hover/state transitions.

---

## 6. Font Loading Strategy

### HTML Head Pattern (Critical Path)

```html
<head>
  <!-- Preconnect to font origin (if external) -->
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>

  <!-- Preload the primary body font only -->
  <link rel="preload" href="/fonts/Inter-Variable.woff2" as="font"
        type="font/woff2" crossorigin>

  <!-- Inline critical @font-face (avoids render-blocking CSS request) -->
  <style>
    @font-face {
      font-family: 'Inter';
      src: url('/fonts/Inter-Variable.woff2') format('woff2-variations');
      font-weight: 100 900;
      font-display: swap;
    }
  </style>

  <!-- Defer non-critical display font -->
  <link rel="stylesheet" href="/css/fonts-display.css" media="print"
        onload="this.media='all'">
</head>
```

### font-display Values

| Value | Behavior | Use When |
|---|---|---|
| `swap` | Shows fallback immediately, swaps when loaded | Body text (always readable) |
| `optional` | Shows fallback, uses custom only if already cached | Repeat visitors, perf-critical |
| `fallback` | 100ms invisible, then fallback, short swap window | Heading fonts |
| `block` | Up to 3s invisible, then fallback | Icon fonts only |

### Performance Budget

- **Target:** under 200KB total font payload
- **Subset:** strip unused Unicode ranges with `unicode-range` or `pyftsubset`
- **Format:** woff2 only (brotli compression, 95%+ browser support)
- **Count:** max 2 font families, 1 variable file each

### Fallback Stack with Metrics Override

```css
/* Reduce CLS by matching fallback metrics to custom font */
@font-face {
  font-family: 'Inter Fallback';
  src: local('Arial');
  ascent-override: 90.49%;
  descent-override: 22.56%;
  line-gap-override: 0%;
  size-adjust: 107.06%;
}

body {
  font-family: 'Inter', 'Inter Fallback', system-ui, sans-serif;
}
```

---

## 7. Cross-Platform Typography

### Comparison Table

| Property | Web (CSS) | iOS (SwiftUI / UIKit) | Android (Compose / XML) |
|---|---|---|---|
| Base unit | `rem` (1rem = 16px default) | `pt` (1pt = 1/72 inch) | `sp` (scales with user pref) |
| System font | `system-ui` | SF Pro (Text < 20pt, Display >= 20pt) | Roboto |
| Scale system | Custom with clamp() | Dynamic Type (11 semantic sizes) | Material Type Scale (15 roles) |
| Min body size | 16px | 17pt (Body style) | 14sp (bodyMedium) |
| Scaling | Media queries + clamp() | Automatic with Dynamic Type | Automatic with sp unit |
| Bold weight | `font-weight: 700` | `.bold()` / `UIFont.Weight.bold` | `FontWeight.Bold` (700) |
| Line height | `line-height: 1.5` | Automatic per style | Automatic per style |

### iOS Dynamic Type Semantic Styles

| Style | Default Size (pt) | Weight | Use For |
|---|---|---|---|
| largeTitle | 34 | Regular | Screen titles |
| title1 | 28 | Regular | Section headers |
| title2 | 22 | Regular | Subsection headers |
| title3 | 20 | Regular | Tertiary headers |
| headline | 17 | Semibold | Inline emphasis |
| body | 17 | Regular | Primary content |
| callout | 16 | Regular | Secondary content |
| subheadline | 15 | Regular | Below headlines |
| footnote | 13 | Regular | Tertiary info |
| caption1 | 12 | Regular | Labels |
| caption2 | 11 | Regular | Fine print |

### Android Material Type Scale (M3)

| Role | Default Size (sp) | Weight | Tracking |
|---|---|---|---|
| displayLarge | 57 | 400 | -0.25 |
| displayMedium | 45 | 400 | 0 |
| displaySmall | 36 | 400 | 0 |
| headlineLarge | 32 | 400 | 0 |
| headlineMedium | 28 | 400 | 0 |
| headlineSmall | 24 | 400 | 0 |
| titleLarge | 22 | 400 | 0 |
| titleMedium | 16 | 500 | 0.15 |
| titleSmall | 14 | 500 | 0.1 |
| bodyLarge | 16 | 400 | 0.5 |
| bodyMedium | 14 | 400 | 0.25 |
| bodySmall | 12 | 400 | 0.4 |
| labelLarge | 14 | 500 | 0.1 |
| labelMedium | 12 | 500 | 0.5 |
| labelSmall | 11 | 500 | 0.5 |

---

## 8. Reading Mechanics

### Why 45-75 Characters Per Line

The eye reads in **saccades** (quick jumps of 7-9 characters) and **fixations** (pauses where you absorb ~4 chars on each side). At 75+ chars per line, the return sweep (left eye movement to the next line) becomes inaccurate, causing readers to lose their place or re-read lines. Under 45 chars, too-frequent line breaks disrupt rhythm.

```css
/* Enforce optimal measure */
.prose {
  max-width: 65ch;       /* ~65 characters, adapts to font */
  /* NOT max-width: 700px — px doesn't scale with font size */
}
```

### Why Text Width > Font Size

Increasing font size without constraining line width makes text harder to read, not easier. A 20px font on a 1400px container produces 100+ char lines. Always set `max-width` in `ch` units.

### Optimal Settings Summary

| Metric | Value | Measured From |
|---|---|---|
| Characters per line | 45-75 (ideal: 65) | Decades of readability research (Bringhurst) |
| Line height for body | 1.5-1.6 | Eye tracking studies on return sweep accuracy |
| Paragraph spacing | 0.75em-1em | Enough to signal break without creating rivers |
| Contrast ratio (body) | >= 4.5:1 (WCAG AA) | W3C accessibility guidelines |
| Contrast ratio (large text) | >= 3:1 (WCAG AA) | Large = 18pt+ or 14pt bold+ |
| Minimum body size (screen) | 16px | Below this, pinch-to-zoom rates spike on mobile |

---

## 9. Common Typography Mistakes

| Mistake | Why It Fails | Fix |
|---|---|---|
| Body text below 16px on mobile | Forces pinch-to-zoom, increases bounce rate | `font-size: clamp(1rem, ...)`, never below 16px |
| Same line-height for all sizes | Headings look loose, captions look cramped | Scale line-height inversely with font-size |
| Using px for font-size | Breaks user zoom preferences and accessibility | Use `rem` for font-size, `em` for related spacing |
| Loading 4+ font weights as static files | 400KB+ payload, blocks render | Use 1 variable font file or limit to 2 weights |
| Setting line width in px | Measure changes when user adjusts font size | Use `ch` unit: `max-width: 65ch` |
| Centered body text | Ragged left edge ruins return sweep, unreadable past 3 lines | Center only headings and short labels |
| Positive letter-spacing on large headings | Looks airy and amateur at 48px+ | Use negative tracking: `letter-spacing: -0.02em` |
| All-caps without extra tracking | Letters collide, words become illegible blobs | Add `letter-spacing: 0.05em` minimum for uppercase |
| Relying on `font-weight: bold` only | Some fonts have no bold, browser faux-bolds look bad | Load the actual bold weight or use a variable font |
| Grey body text (#999 on #fff) | Fails WCAG contrast, fatiguing to read | Minimum #595959 on white for AA compliance |
| System font stack without metrics fallback | Large CLS when custom font loads | Use `size-adjust`, `ascent-override` on fallback |
| Orphans and widows in headings | Single word on last line looks broken | `text-wrap: balance` (supported in modern browsers) |
| Mixing too many font families | Visual noise, no hierarchy signal | Max 2 families. Use weight/size for hierarchy instead |
| Using `text-justify` without hyphens | Creates rivers of whitespace between words | Add `hyphens: auto` with `text-align: justify`, or avoid justify |
| Not testing with long words (German, Finnish) | Layouts break with overflow | Add `overflow-wrap: break-word` on all text containers |
