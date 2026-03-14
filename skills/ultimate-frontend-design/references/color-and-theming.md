# Color & Theming Reference

## 1. Palette Generation from a Single Brand Color

Start with one brand hex, convert to HSL, then generate 9 shades by adjusting H, S, and L.

**Rules:**
- **Lighter shades:** increase L, decrease S slightly, shift hue toward 60 (yellow/warm)
- **Darker shades:** decrease L, increase S slightly, shift hue toward 0 or 240 (red/blue)
- **50 shade** = near-white tint, **900 shade** = near-black shade
- The brand color itself sits at 500 or 600

```js
// Generate 9-shade palette from a single HSL base
function generatePalette(baseHue, baseSat, baseLight) {
  const shades = [50, 100, 200, 300, 400, 500, 600, 700, 800, 900];
  return shades.map((shade, i) => {
    const t = i / (shades.length - 1); // 0..1 from lightest to darkest
    const l = 97 - t * 87;             // 97% down to 10%
    const s = baseSat + (t - 0.5) * 16; // slightly less sat at extremes
    const h = baseHue + (0.5 - t) * 4;  // subtle hue shift
    return { shade, h: Math.round(h), s: Math.round(s), l: Math.round(l) };
  });
}

// Example: brand blue #2563EB → hsl(217, 83%, 53%)
const palette = generatePalette(217, 83, 53);
// Result:
// 50:  hsl(219, 75%, 97%)  → #eff4ff
// 100: hsl(219, 77%, 87%)  → #c9dbfe
// 200: hsl(218, 79%, 77%)  → #93b4fd
// 300: hsl(218, 81%, 67%)  → #608dfc
// 400: hsl(217, 82%, 57%)  → #3b6ff6
// 500: hsl(217, 83%, 53%)  → #2563eb  ← brand
// 600: hsl(216, 85%, 43%)  → #1a4fc4
// 700: hsl(216, 87%, 33%)  → #153e9a
// 800: hsl(215, 89%, 23%)  → #0f2d73
// 900: hsl(215, 91%, 10%)  → #071a42
```

```css
:root {
  --brand-50:  #eff4ff;
  --brand-100: #c9dbfe;
  --brand-200: #93b4fd;
  --brand-300: #608dfc;
  --brand-400: #3b6ff6;
  --brand-500: #2563eb;
  --brand-600: #1a4fc4;
  --brand-700: #153e9a;
  --brand-800: #0f2d73;
  --brand-900: #071a42;
}
```

## 2. Gray Palettes (Always Add Subtle Saturation)

Pure gray (#808080) looks dead on screen. Always tint your neutrals.

### Cool Gray (blue tint, for tech/SaaS/fintech)

| Shade | Hex       | HSL              |
|-------|-----------|------------------|
| 50    | `#f8fafc` | hsl(210, 40%, 98%) |
| 100   | `#f1f5f9` | hsl(210, 40%, 96%) |
| 200   | `#e2e8f0` | hsl(214, 32%, 91%) |
| 300   | `#cbd5e1` | hsl(213, 27%, 84%) |
| 400   | `#94a3b8` | hsl(215, 20%, 65%) |
| 500   | `#64748b` | hsl(215, 16%, 47%) |
| 600   | `#475569` | hsl(215, 19%, 35%) |
| 700   | `#334155` | hsl(215, 25%, 27%) |
| 800   | `#1e293b` | hsl(217, 33%, 17%) |
| 900   | `#0f172a` | hsl(222, 47%, 11%) |

### Warm Gray (amber tint, for organic/lifestyle/food brands)

| Shade | Hex       | HSL              |
|-------|-----------|------------------|
| 50    | `#fafaf9` | hsl(40, 10%, 98%)  |
| 100   | `#f5f5f4` | hsl(40, 8%, 96%)   |
| 200   | `#e7e5e4` | hsl(30, 6%, 90%)   |
| 300   | `#d6d3d1` | hsl(24, 6%, 83%)   |
| 400   | `#a8a29e` | hsl(24, 5%, 64%)   |
| 500   | `#78716c` | hsl(25, 5%, 45%)   |
| 600   | `#57534e` | hsl(26, 6%, 33%)   |
| 700   | `#44403c` | hsl(28, 8%, 25%)   |
| 800   | `#292524` | hsl(20, 10%, 15%)  |
| 900   | `#1c1917` | hsl(24, 14%, 10%)  |

## 3. Semantic Token System

Full CSS custom property system for light and dark mode.

```css
/* ── Light Mode (default) ── */
:root {
  /* Text */
  --text-primary:    #0f172a;   /* headings, body */
  --text-secondary:  #475569;   /* descriptions, labels */
  --text-tertiary:   #94a3b8;   /* placeholders, disabled */
  --text-inverse:    #ffffff;   /* text on dark backgrounds */

  /* Surfaces */
  --surface:         #ffffff;   /* default background */
  --surface-raised:  #ffffff;   /* cards, modals (use shadow for elevation) */
  --surface-sunken:  #f1f5f9;   /* inset areas, sidebar, table stripes */
  --surface-overlay: rgba(0, 0, 0, 0.5); /* modal backdrop */

  /* Borders */
  --border:          #e2e8f0;   /* default dividers */
  --border-strong:   #cbd5e1;   /* emphasized borders */
  --border-focus:    #2563eb;   /* focus rings */

  /* Accent (brand) */
  --accent:          #2563eb;
  --accent-hover:    #1d4ed8;
  --accent-subtle:   #eff4ff;   /* accent backgrounds */

  /* Status */
  --success:         #16a34a;
  --success-subtle:  #f0fdf4;
  --warning:         #d97706;
  --warning-subtle:  #fffbeb;
  --error:           #dc2626;
  --error-subtle:    #fef2f2;
  --info:            #0284c7;
  --info-subtle:     #f0f9ff;
}

/* ── Dark Mode ── */
@media (prefers-color-scheme: dark) {
  :root {
    --text-primary:    #f1f5f9;
    --text-secondary:  #94a3b8;
    --text-tertiary:   #475569;
    --text-inverse:    #0f172a;

    --surface:         #0f172a;
    --surface-raised:  #1e293b;
    --surface-sunken:  #020617;
    --surface-overlay: rgba(0, 0, 0, 0.7);

    --border:          #1e293b;
    --border-strong:   #334155;
    --border-focus:    #3b82f6;

    --accent:          #3b82f6;
    --accent-hover:    #60a5fa;
    --accent-subtle:   #172554;

    --success:         #22c55e;
    --success-subtle:  #052e16;
    --warning:         #f59e0b;
    --warning-subtle:  #422006;
    --error:           #ef4444;
    --error-subtle:    #450a0a;
    --info:            #38bdf8;
    --info-subtle:     #082f49;
  }
}

/* ── Class-based override (for toggle) ── */
[data-theme="dark"] {
  --text-primary:    #f1f5f9;
  --text-secondary:  #94a3b8;
  /* ... same dark values as above */
}
```

## 4. Dark Mode Implementation

### Core Rules

1. **Dark grays, not black.** Background should be `#0f172a` or `#111827`, never `#000000`.
2. **Elevate with lighter surfaces.** Higher elevation = lighter shade (opposite of light mode shadow).
3. **Desaturate accent colors.** Shift brand saturation down 10-15% so colors don't vibrate on dark bg.
4. **Reduce shadow intensity.** Shadows are barely visible on dark; use border or lighter surface instead.
5. **White text at 87% opacity** for primary, 60% for secondary, 38% for disabled.

### Tailwind dark: Pattern

```html
<div class="bg-white dark:bg-slate-900
            text-slate-900 dark:text-slate-100
            border-slate-200 dark:border-slate-700
            shadow-lg dark:shadow-none dark:ring-1 dark:ring-slate-800">
  <h2 class="text-slate-900 dark:text-white">Title</h2>
  <p class="text-slate-600 dark:text-slate-400">Description</p>
</div>
```

### Theme Toggle with System Detection

```js
function getThemePreference() {
  const stored = localStorage.getItem('theme');
  if (stored) return stored;
  return window.matchMedia('(prefers-color-scheme: dark)').matches ? 'dark' : 'light';
}

function applyTheme(theme) {
  document.documentElement.setAttribute('data-theme', theme);
  document.documentElement.classList.toggle('dark', theme === 'dark');
}

// Init
applyTheme(getThemePreference());

// Toggle
function toggleTheme() {
  const next = document.documentElement.getAttribute('data-theme') === 'dark' ? 'light' : 'dark';
  localStorage.setItem('theme', next);
  applyTheme(next);
}

// Listen for OS changes (only when user hasn't manually chosen)
window.matchMedia('(prefers-color-scheme: dark)').addEventListener('change', (e) => {
  if (!localStorage.getItem('theme')) {
    applyTheme(e.matches ? 'dark' : 'light');
  }
});
```

### CSS color-scheme

```css
:root { color-scheme: light dark; }
/* Tells browser to style native controls (scrollbars, inputs) to match */
```

## 5. Platform Color Systems

### iOS Semantic Colors (UIKit)

| UIColor Name           | Light Hex  | Dark Hex   | Usage               |
|------------------------|-----------|-----------|----------------------|
| `.label`               | `#000000` | `#ffffff` | Primary text         |
| `.secondaryLabel`      | `#3c3c43` (60%) | `#ebebf5` (60%) | Secondary text |
| `.tertiaryLabel`       | `#3c3c43` (30%) | `#ebebf5` (30%) | Tertiary text  |
| `.systemBackground`    | `#ffffff` | `#000000` | Root background      |
| `.secondarySystemBackground` | `#f2f2f7` | `#1c1c1e` | Grouped bg    |
| `.tertiarySystemBackground`  | `#ffffff` | `#2c2c2e` | Elevated bg   |
| `.separator`           | `#3c3c43` (29%) | `#545458` (65%) | Thin lines   |
| `.systemBlue`          | `#007aff` | `#0a84ff` | Links, actions       |
| `.systemGreen`         | `#34c759` | `#30d158` | Success              |
| `.systemRed`           | `#ff3b30` | `#ff453a` | Destructive          |

### Material 3 Tonal Palette (13 tones)

From any source color, Material generates tones at these lightness levels:

| Tone | Lightness | Typical Use (Light) | Typical Use (Dark) |
|------|-----------|--------------------|--------------------|
| 0    | 0%        | -                  | -                  |
| 10   | 10%       | On-primary         | Primary container  |
| 20   | 20%       | -                  | Primary            |
| 30   | 30%       | -                  | On-primary-container |
| 40   | 40%       | Primary            | -                  |
| 50   | 50%       | -                  | -                  |
| 60   | 60%       | -                  | -                  |
| 70   | 70%       | -                  | -                  |
| 80   | 80%       | Primary container  | On-primary         |
| 90   | 90%       | On-primary-container | -                |
| 95   | 95%       | -                  | -                  |
| 99   | 99%       | Background         | -                  |
| 100  | 100%      | On-primary         | -                  |

## 6. Accessible Color

### Contrast Requirements (WCAG 2.1)

| Level | Normal Text (< 18pt) | Large Text (>= 18pt or 14pt bold) | UI Components |
|-------|----------------------|-----------------------------------|---------------|
| AA    | 4.5:1                | 3:1                               | 3:1           |
| AAA   | 7:1                  | 4.5:1                             | 4.5:1         |

### Safe Text-on-Background Combinations

| Background     | Safe Text Colors                | Ratio  |
|----------------|--------------------------------|--------|
| White `#fff`   | `#171717` (15.7), `#404040` (9.7), `#525252` (7.4) | AA/AAA |
| `#f8fafc` (50) | `#334155` (7.5), `#475569` (5.8) | AA     |
| `#0f172a` (900)| `#f1f5f9` (14.5), `#94a3b8` (6.3), `#cbd5e1` (9.8) | AA/AAA |
| Brand blue `#2563eb` | `#ffffff` (4.6), `#eff4ff` (4.3) | AA (large text safe) |
| `#dc2626` (red)| `#ffffff` (4.6)                | AA     |

### Common Failures and Fixes

- **Gray text on white:** `#9ca3af` on `#fff` = 2.9:1. Fix: darken to `#6b7280` (5.4:1).
- **Brand on brand-subtle:** `#2563eb` on `#eff4ff` = 4.3:1. Fix: darken accent to `#1d4ed8` (5.5:1).
- **Colored status text:** `#f59e0b` (yellow) on `#fff` = 1.9:1. Fix: use `#92400e` (brown) for text, keep yellow for icons/badges.
- **Placeholder text:** ensure at least 3:1 contrast (not a WCAG requirement for AA but improves usability).

### Checking Tools

- **Browser:** Chrome DevTools (inspect element, contrast indicator in color picker)
- **Design:** Figma Stark plugin, Contrast app (macOS)
- **CLI:** `npx wcag-contrast #2563eb #ffffff` (programmatic check)
- **Bulk audit:** axe DevTools extension scans entire page

## 7. Color Psychology Quick Reference

### By Color

| Color  | Associations             | Best For                        | Caution                     |
|--------|--------------------------|----------------------------------|-----------------------------|
| Blue   | Trust, stability, calm   | Finance, tech, healthcare        | Can feel cold or corporate  |
| Green  | Growth, health, nature   | Eco, health, finance (profit)    | Avoid neon green (cheap)    |
| Red    | Urgency, energy, danger  | CTAs, sales, food, entertainment | Overuse = anxiety           |
| Orange | Friendly, energetic      | E-commerce CTAs, youth brands    | Can feel unserious          |
| Purple | Premium, creative        | Luxury, beauty, SaaS             | Dark purple = hard to read  |
| Yellow | Optimism, attention      | Warnings, highlights, accents    | Low contrast on white       |
| Black  | Luxury, sophistication   | Fashion, premium products        | Large areas = heavy         |
| White  | Clean, minimal           | Tech, healthcare, portfolios     | Too much = sterile          |

### By Industry

| Industry  | Primary Palette          | Accent                    | Avoid               |
|-----------|--------------------------|---------------------------|----------------------|
| Fintech   | Blue 600-700, cool grays | Green (success), teal     | Red as primary       |
| Health    | Blue, teal, soft green   | Warm white surfaces       | Aggressive reds      |
| E-commerce| Neutral base             | Orange/red CTAs           | Too many accent colors |
| SaaS      | Purple or blue primary   | Vibrant accent for CTAs   | Dull, unsaturated palette |
| Food      | Warm reds, oranges, greens| Cream/warm backgrounds   | Cold blues           |
