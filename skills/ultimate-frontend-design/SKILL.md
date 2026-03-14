---
name: ultimate-frontend-design
description: 'Complete frontend design system for web and mobile apps. Use on ANY UI task: building screens, reviewing designs, fixing layouts, choosing colors, adding animation, improving usability, or designing components. Covers visual hierarchy, typography, spacing, color systems, depth, motion, usability, accessibility, mobile-native patterns (iOS/Android), components, ecosystem libraries, forms, data display, design psychology, modern CSS, performance, and interaction design. Scores every output 0-10 with weighted rubric. Backed by 12 reference files with 8,000+ lines of patterns, code, and techniques.'
license: MIT
metadata:
  author: synthesized
  version: "2.0.0"
---

# Ultimate Frontend Design System

A complete, opinionated design framework for building professional web and mobile interfaces. 19 core sections + 12 deep-dive reference files. Synthesized from Refactoring UI, Apple HIG, Material Design 3, WCAG 2.2, Nielsen's heuristics, Don Norman's design principles, Gestalt psychology, cognitive load theory, and the craft standards of award-winning agencies (Locomotive, Studio Freight, AREA 17, Active Theory).

## Core Principle

**Systems over taste. Constraints over creativity.** Great UI comes from constrained scales (spacing, type, color, shadows, radii), not artistic intuition. Design in grayscale first to force hierarchy through spacing, contrast, and typography. Add color last. Start with too much white space, then remove. Every pixel must be intentional.

**The foundation:** Professional interfaces are built on core pillars: visual hierarchy, typography, spacing, color, depth, motion, usability, accessibility, mobile-native patterns, and design psychology. Master the system so you can break it with intention.

## Scoring

**Goal: 10/10.** Rate every UI output using this weighted rubric. Always provide the current score and specific improvements needed to reach 10/10.

### Category Scoring (Each 0-10)

**VISUAL HIERARCHY (Weight: 20%)**
| Score | Criteria |
|-------|----------|
| 0-3 | Everything same size/weight/color, no clear reading order |
| 4-6 | Some differentiation, but labels compete with data, buttons don't pop |
| 7-8 | Clear three-level hierarchy (primary/secondary/tertiary), blur test passes |
| 9-10 | Hierarchy communicates even when squinting, deliberate de-emphasis creates powerful emphasis |

**TYPOGRAPHY (Weight: 20%)**
| Score | Criteria |
|-------|----------|
| 0-3 | System fonts, uniform scale, default tracking and line-height |
| 4-6 | Decent font choice, some scale contrast, acceptable line-heights |
| 7-8 | Strong scale contrast (5:1+), proper line-heights per context, max 2 families |
| 9-10 | Typography IS the design, dramatic scale (10:1), fluid sizing, optical alignment |

**SPACING & LAYOUT (Weight: 15%)**
| Score | Criteria |
|-------|----------|
| 0-3 | Arbitrary values, cramped, no grouping, full-width everything |
| 4-6 | Some consistency, basic grouping, constrained widths |
| 7-8 | Strict scale (4/8/16/24/32/48/64), clear grouping, breathing room |
| 9-10 | Spacing creates rhythm, density variation between sections, composition has tension |

**COLOR & THEMING (Weight: 15%)**
| Score | Criteria |
|-------|----------|
| 0-3 | Random colors, pure black/white, no system, contrast failures |
| 4-6 | Cohesive palette, acceptable contrast, basic dark mode |
| 7-8 | Systematic shades (50-900), saturated grays, semantic tokens, proper dark mode |
| 9-10 | Colors feel owned by the project, contextual shifts, atmosphere you can feel |

**MOTION & INTERACTION (Weight: 10%)**
| Score | Criteria |
|-------|----------|
| 0-3 | No animation or default ease/linear, jarring transitions |
| 4-6 | Basic transitions, some hover states |
| 7-8 | Custom easing, choreographed reveals, meaningful feedback under 100ms |
| 9-10 | Motion tells stories, orchestrated sequences, every animation answers "why does this move?" |

**ACCESSIBILITY & USABILITY (Weight: 10%)**
| Score | Criteria |
|-------|----------|
| 0-3 | No focus states, contrast failures, no keyboard nav, tiny tap targets |
| 4-6 | Basic contrast, some focus states, adequate tap targets |
| 7-8 | WCAG AA compliant, keyboard navigable, proper ARIA, 44px+ touch targets |
| 9-10 | Accessibility enhances design, reduced-motion respected, screen reader optimized |

**MOBILE & RESPONSIVE (Weight: 10%)**
| Score | Criteria |
|-------|----------|
| 0-3 | Desktop-only, broken on mobile, no safe area handling |
| 4-6 | Basic responsiveness, adequate touch targets |
| 7-8 | Mobile-first, platform-appropriate patterns, proper safe areas, thumb-zone aware |
| 9-10 | Feels native on each platform, gestures feel right, adaptive not just responsive |

### Quick Score Formula
```
Total = (Hierarchy x 0.20) + (Typography x 0.20) + (Spacing x 0.15) + (Color x 0.15) + (Motion x 0.10) + (Accessibility x 0.10) + (Mobile x 0.10)
```

---

## 1. Visual Hierarchy

**Core concept:** Not everything can be important. Create hierarchy through three levers: size, weight, and color. Combine levers, don't multiply.

**Why it works:** When every element competes for attention, nothing stands out. Deliberate de-emphasis of secondary content makes primary content powerful by contrast. The blur test: squint at your design -- if the important elements don't stand out, hierarchy has failed.

**Key rules:**
- Primary text = large OR bold OR dark, not all three. Save "all three" for the single most important element
- Labels are secondary -- form labels, table headers, metadata labels support the data, not compete with it
- De-emphasize labels: smaller, lighter, or uppercase-small
- Button hierarchy: primary (filled), secondary (outlined or muted), tertiary (text only)
- Semantic color does not equal visual weight -- a muted red button often works better than screaming danger for routine actions
- Three text color levels: `gray-900` (primary), `gray-600` (secondary), `gray-400` (tertiary)

**Application by context:**

| Context | Hierarchy Technique | Example |
|---------|---------------------|---------|
| Form fields | De-emphasize labels, emphasize values | Small uppercase label above large value text |
| Navigation | Active bold/dark, inactive lighter | Active in gray-900, inactive in gray-500 |
| Cards | Title large, metadata small and light | Title 20px bold, date 12px gray-400 |
| Dashboards | Key metric large, context small | "$42,300" large, "vs last month" small |
| Tables | De-emphasize headers, emphasize cell data | Headers uppercase small gray, data normal weight |
| Mobile lists | Primary line prominent, secondary line muted | Name in 16px dark, timestamp in 13px gray |

**Ethical boundary:** Don't use hierarchy tricks to hide important information like pricing, terms, or cancellation options.

---

## 2. Typography

**Core concept:** Use a modular type scale, constrain line heights by context, limit to two font families maximum. Typography is architecture, not decoration.

**The system:**

| Property | Scale / Values |
|----------|---------------|
| **Size scale** | 12, 14, 16, 18, 20, 24, 30, 36, 48, 60, 72px (1.25 ratio) |
| **Line heights** | Hero: 0.9-1.0 / Headings: 1.0-1.25 / Body: 1.5-1.75 / Mobile body: 1.4-1.6 |
| **Tracking** | Hero: -0.04em / Display: -0.03em / Headings: -0.02em / Body: 0 / All-caps: +0.1em |
| **Weights** | Normal (400) / Medium (500) / Semibold (600) / Bold (700). Never below 400 for body |
| **Max width** | Body text: 45-75 characters (`max-w-prose` ~65ch). Mobile: 35-50 chars |
| **Font families** | Max 2: one for headings, one for body (or one family with weight variation) |
| **Minimum body** | 16px web / 17px iOS / 14sp Android. Never smaller for readable content |

**Fluid typography (modern approach):**
```css
/* Use clamp() for responsive type */
--text-sm: clamp(0.8rem, 0.17vw + 0.76rem, 0.89rem);    /* 12.8-14.2px */
--text-base: clamp(1rem, 0.34vw + 0.91rem, 1.19rem);     /* 16-19px */
--text-lg: clamp(1.25rem, 0.61vw + 1.1rem, 1.58rem);     /* 20-25px */
--text-xl: clamp(1.56rem, 1.03vw + 1.31rem, 2.11rem);    /* 25-34px */
--text-2xl: clamp(1.95rem, 1.66vw + 1.54rem, 2.81rem);   /* 31-45px */
--text-3xl: clamp(2.44rem, 2.59vw + 1.79rem, 3.75rem);   /* 39-60px */
```

**Font selection tiers:**
- **Premium web:** Space Grotesk, Instrument Serif, Fraunces, Satoshi, General Sans, Cabinet Grotesk, Clash Display, Geist
- **Reliable web:** Inter, DM Sans, Plus Jakarta Sans, Manrope, Source Sans 3
- **iOS native:** SF Pro (system), SF Pro Rounded, New York
- **Android native:** Roboto (system), Google Sans, Noto Sans
- **Performance budget:** Under 200KB total web font payload. Use WOFF2, subset to needed glyphs

**Ethical boundary:** Never use tiny type sizes to hide terms, conditions, or fees.

See: [references/typography-and-fonts.md](references/typography-and-fonts.md)

---

## 3. Spacing & Layout

**Core concept:** Use a constrained spacing scale, not arbitrary values. Spacing defines relationships -- closer elements are more related.

**The spacing scale:**
```
4px  (xs)    — icon-label gap, inline elements
8px  (sm)    — tight coupling, related items within a group
12px (md)    — form field internal padding
16px (base)  — standard padding, related group separation
24px (lg)    — section internal spacing, card padding
32px (xl)    — subsection separation
48px (2xl)   — major section separation
64px (3xl)   — page section separation
96px (4xl)   — hero/major landmark spacing
```

**Layout principles:**
- Start with too much white space, then remove. You'll almost never remove enough
- Spacing between groups > spacing within groups (Gestalt proximity)
- Full-width is almost never right for content. Constrain: `max-w-prose` for text, `max-w-md` for forms, `max-w-6xl` for page containers
- Left-align text by default. Center only: short headlines, hero sections, single-action CTAs, empty states
- Forms should max out at 300-500px width
- Cards don't need to contain everything -- let images bleed to edges

**Responsive breakpoints:**
```
sm:  640px   — large phones landscape
md:  768px   — tablets portrait
lg:  1024px  — tablets landscape / small laptops
xl:  1280px  — standard desktop
2xl: 1536px  — large screens
```

**Mobile-specific spacing:**
- Safe area insets: always respect `env(safe-area-inset-*)` on notched devices
- iOS: status bar 59pt, nav bar 44pt, tab bar 49pt, home indicator 34pt
- Android: status bar 24dp, app bar 56dp, bottom nav 80dp
- Standard margins: 16-20pt on mobile, scaling up to 24-32px on larger screens
- Thumb zone: critical actions in bottom 40% of screen on mobile

**Ethical boundary:** Don't use spacing to bury important UI elements like unsubscribe buttons or privacy controls.

See: [references/layout-and-responsive.md](references/layout-and-responsive.md)

---

## 4. Color System

**Core concept:** Build a systematic palette with 5-9 shades per color. Add subtle saturation to grays. Design in grayscale first.

**The color system:**

| Element | Rule |
|---------|------|
| **Shades per hue** | 9 shades: 50 (near-white) through 900 (near-black) |
| **Grays** | Never pure gray. Add subtle saturation: cool UI = blue tint (`#64748b`), warm UI = yellow/brown tint (`#78716c`) |
| **Black/White** | Never pure `#000000` or `#ffffff`. Use `#0a0a0a` and `#fafaf9` |
| **Text hierarchy** | Primary: gray-900 / Secondary: gray-600 / Tertiary: gray-400 |
| **Contrast ratios** | Body text: 4.5:1 minimum / Large text (18px+): 3:1 / Interactive elements: 3:1 |
| **Borders** | Subtle: gray-200 / Strong: gray-300 |
| **Accent usage** | Single accent color used sparingly (CTAs, links, key moments). Never everywhere |

**Modern palette generation (OKLCH):**
OKLCH is the recommended color space for programmatic palette generation (perceptually uniform, unlike HSL):
```css
--brand-500: oklch(55% 0.2 250);    /* base */
--brand-100: oklch(95% 0.04 250);   /* lightest */
--brand-900: oklch(25% 0.1 250);    /* darkest */
```
Fix chroma + rotate hue for harmonious palettes. Shift lightness for tonal scales.

**HSL fallback rules:**
- Lighter variants: higher lightness, lower saturation, hue shift toward 60 degrees
- Darker variants: lower lightness, higher saturation, hue shift toward 0/240 degrees

**Dark mode rules:**
- Surfaces: dark grays (`#0a0a0a`, `#141414`, `#1f1f1f`), not pure black
- Elevate with lighter surfaces, not shadows (invert the depth model)
- Desaturate accent colors by 10-15%
- Text: `#fafaf9` primary, `rgba(250,250,249,0.6)` secondary, `rgba(250,250,249,0.4)` tertiary
- Always test both themes. System preference detection via `prefers-color-scheme`

**Semantic tokens (design token approach):**
```css
:root {
  --color-text-primary: #0a0a0a;
  --color-text-secondary: rgba(10,10,10,0.6);
  --color-text-tertiary: rgba(10,10,10,0.4);
  --color-surface: #fafaf9;
  --color-surface-raised: #ffffff;
  --color-border: rgba(10,10,10,0.1);
  --color-accent: /* project-specific */;
}

[data-theme="dark"] {
  --color-text-primary: #fafaf9;
  --color-text-secondary: rgba(250,250,249,0.6);
  --color-text-tertiary: rgba(250,250,249,0.4);
  --color-surface: #0a0a0a;
  --color-surface-raised: #141414;
  --color-border: rgba(250,250,249,0.1);
}
```

**Mobile platform colors:**
- iOS: use semantic system colors (`UIColor.label`, `UIColor.secondaryLabel`, `UIColor.systemBackground`) that auto-adapt to light/dark
- Android/Material 3: use dynamic color from wallpaper, tonal palette system with 13 tonal values per hue

**Ethical boundary:** Don't use color alone to convey information. Always pair with text, icons, or patterns for accessibility.

See: [references/color-and-theming.md](references/color-and-theming.md)

---

## 5. Depth & Shadows

**Core concept:** Use a shadow scale to convey elevation. Small shadows for slightly raised elements, large shadows for floating elements.

**Shadow scale:**
```css
--shadow-xs:  0 1px 2px rgba(0,0,0,0.04);                               /* subtle lift */
--shadow-sm:  0 1px 3px rgba(0,0,0,0.06), 0 1px 2px rgba(0,0,0,0.04);  /* buttons */
--shadow-md:  0 4px 6px rgba(0,0,0,0.05), 0 2px 4px rgba(0,0,0,0.04);  /* cards */
--shadow-lg:  0 10px 15px rgba(0,0,0,0.06), 0 4px 6px rgba(0,0,0,0.04); /* dropdowns */
--shadow-xl:  0 20px 25px rgba(0,0,0,0.08), 0 8px 10px rgba(0,0,0,0.04);/* modals */
--shadow-2xl: 0 25px 50px rgba(0,0,0,0.15);                              /* hero cards */
```

**Elevation mapping:**
| Level | Element | Shadow | Z-index |
|-------|---------|--------|---------|
| 0 | Page background | none | 0 |
| 1 | Cards, sections | sm-md | 1 |
| 2 | Sticky header, raised cards | md | 10 |
| 3 | Dropdowns, tooltips | lg | 20 |
| 4 | Modals, dialogs | xl | 30 |
| 5 | Toasts, popovers | xl-2xl | 40 |
| 6 | Overlays | none (dim) | 50 |

**Border radius scale:**
```css
--radius-sm: 4px;    /* small elements: tags, badges */
--radius-md: 8px;    /* default: buttons, inputs */
--radius-lg: 12px;   /* cards, containers */
--radius-xl: 16px;   /* large cards, modals */
--radius-2xl: 24px;  /* prominent containers */
--radius-full: 9999px; /* pills, avatars */
```

**Depth without shadows:** lighter top border + darker bottom border, subtle gradient backgrounds, overlapping elements with offset, background color differentiation.

**Dark mode depth:** invert the model. Lighter surfaces = higher elevation. Reduce shadow intensity, use border-top highlights instead.

---

## 6. Motion & Animation

**Core concept:** Every animation must answer "why does this move?" Motion is not polish applied at the end. Purpose over decoration, custom curves always, orchestration over isolation.

**Timing scale:**
```
50-100ms   — micro-feedback (button press, toggle, checkbox)
150-250ms  — simple transitions (hover states, color changes, small reveals)
250-400ms  — medium transitions (panel slides, card expansions, page sections)
400-700ms  — complex transitions (page transitions, modal entries)
800-1200ms — dramatic reveals (hero animations, scroll sequences)
```

**Custom easing (mandatory -- default ease/linear banned):**
```css
--ease-out-expo: cubic-bezier(0.16, 1, 0.3, 1);       /* most common: reveals, enters */
--ease-out-quart: cubic-bezier(0.25, 1, 0.5, 1);      /* slightly faster settle */
--ease-in-out-expo: cubic-bezier(0.87, 0, 0.13, 1);   /* symmetric: toggles, morphs */
--ease-out-back: cubic-bezier(0.34, 1.56, 0.64, 1);   /* bouncy: playful moments */
```

**Spring physics (more natural than cubic-bezier):**
Springs adapt duration to distance: short moves are fast, long moves are slower. Use for interactive/gesture-driven animation.
- Stiffness: higher = snappier (100-500 typical)
- Damping: higher = less bounce (10-40 typical)
- Mass: higher = more sluggish (1 is standard)
- Libraries: Framer Motion (`transition: { type: "spring", stiffness: 300, damping: 24 }`), React Spring, Motion One

**Animation rules:**
- Only animate `transform` and `opacity` for 60fps. Never animate width, height, top, left, margin, padding
- Stagger children by 60-100ms for choreographed reveals
- Page load sequence: structure (0-200ms) -> hero title (200-600ms) -> subtitle (400-800ms) -> nav (600-900ms) -> supporting (800-1200ms)
- Scroll-triggered: elements reveal as they enter viewport, not all at once
- Feedback under 100ms feels instant. 100-1000ms is noticeable (show change). Over 1000ms needs progress indicator
- Always respect `prefers-reduced-motion: reduce`

**Mobile-specific motion:**
- iOS: spring animations (damping 0.7-0.9, response 0.3-0.5s). Gesture-driven with interruptible transitions
- Android/Material: emphasized easing with 400ms standard duration. Container transform for shared element transitions
- Touch feedback: immediate visual response on press (< 50ms). Scale down slightly (0.97-0.98) on press
- Haptics: iOS UIImpactFeedbackGenerator (light/medium/heavy). Android performHapticFeedback. Use on: toggles, destructive confirms, success moments

**Ethical boundary:** Motion must never block interaction or cause motion sickness. Always respect `prefers-reduced-motion`. Content must be accessible without animation.

See: [references/animation-and-motion.md](references/animation-and-motion.md)

---

## 7. Usability & Interaction Design

**Core concept:** Don't make users think. Every screen should be self-evident. Usability is not a phase -- it's embedded in every design decision.

**Nielsen's 10 Heuristics (applied):**

| Heuristic | What to check | Fix pattern |
|-----------|---------------|-------------|
| **System status** | Is the user always informed what's happening? | Loading states, progress bars, success/error feedback |
| **Real-world match** | Does terminology match the user's mental model? | Use domain language, not technical jargon |
| **User control** | Can users undo, go back, cancel? | Undo actions, back buttons, cancel flows |
| **Consistency** | Same action = same result everywhere? | Design system tokens, component library |
| **Error prevention** | Are errors prevented before they happen? | Inline validation, confirmation dialogs, constraints |
| **Recognition > recall** | Can users see their options? | Visible navigation, autocomplete, recent items |
| **Flexibility** | Can beginners and experts both succeed? | Progressive disclosure, keyboard shortcuts |
| **Minimalism** | Does every element earn its place? | Remove competing elements, reduce cognitive load |
| **Error recovery** | Do error messages explain the fix? | "Password must include a number" not "Invalid input" |
| **Help** | Is help available in context? | Tooltips, inline hints, contextual help |

**The trunk test (navigation check):** Can a user arriving on any page answer: What site is this? What page am I on? What are the major sections? What are my options at this level? Where am I in the hierarchy?

**Form usability rules:**
- One column layouts convert better than multi-column
- Label above input (not floating labels for critical forms)
- Inline validation on blur, not on each keystroke
- Smart defaults (pre-fill what you can)
- Autofocus the first field
- Group related fields, separate unrelated ones
- Submit button text = the action ("Create account", not "Submit")

**Affordance principles (Don Norman):**
- Discoverability: can users figure out what actions are possible?
- Signifiers: do visual cues indicate where to act? (buttons look pressable, links look clickable)
- Feedback: does the system respond to every action within 100ms?
- Constraints: does the design prevent errors? (disabled buttons, character limits, type restrictions)
- Mapping: does the control layout match the thing being controlled?

**Severity rating for issues:**
- **0 - Cosmetic:** noticed but doesn't affect use
- **1 - Minor:** slight delay, workaround exists
- **2 - Major:** significant delay, user may fail
- **3 - Critical:** task failure, user abandonment
- **4 - Catastrophic:** data loss, security risk, legal exposure

---

## 8. Accessibility

**Core concept:** Accessibility is not a feature toggle. It's a design constraint from day one. WCAG 2.2 AA is the minimum standard.

**Non-negotiable requirements:**

| Requirement | Standard | How to check |
|------------|----------|-------------|
| **Color contrast** | 4.5:1 normal text, 3:1 large text (18px+), 3:1 UI components | WebAIM contrast checker |
| **Touch targets** | 44x44px minimum (iOS HIG + WCAG). 48x48dp Android | Measure with dev tools |
| **Focus indicators** | Visible focus ring on all interactive elements. 2px+ solid, 3:1 contrast | Tab through the page |
| **Keyboard navigation** | All functionality reachable by keyboard. Logical tab order | Unplug the mouse |
| **Screen readers** | Semantic HTML, ARIA labels where needed, alt text on images | Test with VoiceOver/TalkBack |
| **Reduced motion** | Respect `prefers-reduced-motion`. Provide alternative to animation | Check media query |
| **Zoom** | Content readable at 200% zoom without horizontal scroll | Ctrl+/- test |
| **Color independence** | Never use color alone for information. Pair with text/icons | Grayscale screenshot test |

**Focus state design:**
```css
/* Beautiful AND accessible */
:focus-visible {
  outline: 2px solid var(--color-accent);
  outline-offset: 2px;
  border-radius: var(--radius-sm);
}

/* Remove outline only on mouse interaction */
:focus:not(:focus-visible) {
  outline: none;
}
```

**ARIA essentials (use sparingly -- semantic HTML first):**
- `aria-label` for icon-only buttons
- `aria-live="polite"` for dynamic content updates
- `aria-expanded` for collapsibles/dropdowns
- `role="alert"` for error messages
- `aria-hidden="true"` for decorative elements

See: [references/accessibility.md](references/accessibility.md)

---

## 9. Mobile-Native Patterns

**Core concept:** Mobile design is not "make the desktop version smaller." Each platform has distinct patterns. Respect platform conventions or users will fight the interface.

### iOS Patterns
- **Navigation:** Tab bar (2-5 items) at bottom. No hamburger menus. Large titles that collapse on scroll
- **Touch targets:** 44pt minimum. 8pt minimum spacing between targets
- **Typography:** SF Pro system font, Dynamic Type support (let users scale text). Semantic styles: `.largeTitle`, `.headline`, `.body`, `.caption`
- **Gestures:** swipe-back for navigation, pull-to-refresh, long-press for context menus
- **Haptics:** light impact on toggle, medium on confirm, notification on success/error
- **Safe areas:** always inset from notch, Dynamic Island, home indicator
- **Dark mode:** use semantic system colors that auto-adapt

### Android / Material 3 Patterns
- **Navigation:** bottom navigation bar (3-5 items), navigation drawer for secondary, top app bar with contextual actions
- **Touch targets:** 48dp minimum, 8dp spacing
- **Typography:** Roboto or Google Sans, Material type scale (display/headline/title/body/label)
- **Gestures:** predictive back, edge swipe nav, pull-to-refresh
- **Dynamic color:** Material You extracts palette from wallpaper. Design with tonal palette (13 tones per hue)
- **Elevation:** surfaces at dp levels 0-5, shadows + tonal fill combined
- **Shape:** rounded corners with shape scale (extra-small 4dp to extra-large 28dp)

### Cross-Platform (React Native / Flutter)
- Respect platform conventions: iOS push/pop navigation, Android back button
- Platform-specific components where they differ (date picker, action sheet vs. dialog)
- Shared design tokens for spacing, color, type -- platform-specific components
- Test on real devices. Simulators lie about performance and feel
- Bottom sheet over modal for secondary actions on mobile

**Application by screen type:**

| Screen | Pattern | Key rules |
|--------|---------|-----------|
| **Lists** | Swipe actions, pull-to-refresh, section headers | Consistent row heights, loading skeletons |
| **Forms** | Single column, keyboard-aware scroll, smart autofill | Large touch targets, clear error placement |
| **Navigation** | Tab bar + stack navigation | Max 3-4 levels deep, always show back |
| **Detail views** | Scrollable content, sticky header/CTA | Content in thumb zone, floating action |
| **Onboarding** | Progressive disclosure, 3-5 steps max | Skip option, progress indicator, clear value prop |
| **Settings** | Grouped list, toggle switches | Platform-native toggles, immediate feedback |

See: [references/mobile-patterns.md](references/mobile-patterns.md)

---

## 10. Components & Patterns

**Core concept:** Components are the building blocks of consistent interfaces. Every component must handle all states: default, hover, active, focus, disabled, loading, error, empty.

**State checklist (every interactive component):**
```
[ ] Default     — resting state
[ ] Hover       — mouse over (web) / highlight (mobile)
[ ] Active      — being pressed
[ ] Focus       — keyboard focused (focus-visible)
[ ] Disabled    — not interactive (reduced opacity, no pointer events)
[ ] Loading     — waiting for data/action (skeleton or spinner)
[ ] Error       — invalid or failed state
[ ] Empty       — no data to display (illustration + CTA)
[ ] Selected    — current/active item in a group
```

**Button system:**
| Type | Use | Style |
|------|-----|-------|
| Primary | Main CTA, one per section | Filled, accent color, shadow-sm |
| Secondary | Alternative actions | Outlined or muted fill |
| Tertiary | Low-emphasis actions | Text only, no background |
| Destructive | Delete, remove | Red tint, requires confirmation |
| Ghost | Toolbar actions, icon buttons | Transparent, hover shows background |

**Input system:**
- Height: 40-44px web, 44pt iOS, 48dp Android
- Padding: 12-16px horizontal
- Border: 1px gray-300, focus: 2px accent
- Label: above input, not floating (for critical forms)
- Helper text: below input, gray-500
- Error: red border + red helper text, icon optional
- Never rely on placeholder text as the label

**Card patterns:**
- Padding: 16-24px (scale with screen size)
- Border-radius: 8-16px
- Shadow: sm-md (or border on flat designs)
- Image: `object-fit: cover` with fixed `aspect-ratio`
- Always handle the empty/loading/error state

---

## 11. Component Libraries & Ecosystem

> **Full reference:** `references/ecosystem-and-libraries.md` — complete API patterns, code snippets, and installation guides for every library below.

### Stack Selection

| Need | Library |
|------|---------|
| Base components | **shadcn/ui** — source-code registry on Radix UI + Tailwind. You own the code. |
| Hero effects, backgrounds | **Aceternity UI** (200+ dramatic effects) or **Magic UI** (~50, cleaner code) |
| React animations | **Motion** (formerly Framer Motion) — springs, gestures, layout animations |
| Scroll-driven sequences | **GSAP + ScrollTrigger** — timelines, pinning, scrub |
| Smooth scrolling | **Lenis** (<4kb, preserves native APIs) |
| AI agent UIs | **21st.dev** — chat interfaces, streaming, session management |

### Premium Landing Page Stack
```
shadcn/ui (base) + Tailwind CSS (styling)
  + Motion (component interactions)
  + GSAP + ScrollTrigger (scroll narratives)
  + Lenis (smooth scroll)
  + Aceternity or Magic UI (hero effects)
```

### Quick Rules
- **Start with shadcn/ui** for any React/Next.js project, layer effects on top
- **Motion for component-level** (hover, tap, enter/exit), **GSAP for page-level** (scroll, timelines)
- **Aceternity for portfolios/creative sites**, **Magic UI for SaaS/product sites**
- **Lenis + GSAP ScrollTrigger** pair perfectly; always integrate them together
- Motion spring defaults: stiffness 100, damping 10. Snappy buttons: 300/20. Gentle modals: 120/14.
- All Aceternity and Magic UI components install via `npx shadcn@latest add "https://..."`

### Current Design Trends (from Framer templates)
- Dark mode dominant with accent gradients
- Oversized typography (8-12vw headings)
- Bento grid layouts for feature showcases
- Scroll-triggered section reveals with pinning
- Split-text and character-by-character animations
- Custom cursors and magnetic button effects
- Horizontal scroll sections for project showcases

---

## 12. Anti-Slop: Avoiding Generic AI Aesthetics

AI models default to the statistical center of their training data ("distributional convergence"). Without intentional constraints, every AI-generated UI converges on the same look: Inter font, purple-to-blue gradients, white backgrounds, centered everything, rounded cards with shadows. This section exists to break that pattern.

**The anti-AI test:** If someone said "AI made this," would they believe you immediately? If yes, that's the problem.

**Banned defaults (unless deliberately chosen with justification):**
- Inter, Roboto, Arial, or system-ui as the primary display font
- Purple-to-blue gradient hero backgrounds
- Glassmorphism everywhere, gradient text for "impact"
- Dark mode with glowing neon accents as a no-thought default
- Centered everything with uniform spacing
- Generic stock photo hero images
- `ease` or `linear` CSS easing. Bounce/elastic easing ("dated and tacky")
- Pure `#000000` and `#ffffff`
- Default browser scroll behavior on brand/marketing sites
- Rounded rectangle cards with identical shadows in a uniform grid
- Nesting cards inside cards. Not everything needs a container. Spacing creates grouping naturally
- Hero metrics template (big number, small label, gradient accent)
- Monospace font as lazy "developer" vibes
- "Get Started" / "Submit" / "OK" / "Yes/No" as button labels
- Gray placeholder illustrations
- Glow effects as primary affordances
- Heavy `rgba()`/`hsla()` usage (design smell: means your palette is incomplete)

**Forced aesthetic commitment:** Before writing any UI code, declare:
```
AESTHETIC: [specific direction, not "clean and modern"]
TYPOGRAPHY: [specific font choice with reasoning]
SIGNATURE MOMENT: [what will someone screenshot?]
COLOR STRATEGY: [monochromatic tension / bold signature / contextual / minimal]
```

**Anti-pattern checklist (run on every output):**
| Check | If true, fix it |
|-------|-----------------|
| Could this be any brand's website? | Add project-specific personality: owned color, distinctive type, unique layout rhythm |
| Are all cards the same size in a grid? | Vary sizes, feature one, use asymmetric layout |
| Is the hero a centered headline + subtitle + button? | Try offset layout, bleed imagery, dramatic type scale, or unconventional composition |
| Is every section the same spacing? | Create rhythm: dense sections followed by breathing room |
| Are hover states just opacity or color changes? | Add transform, custom cursor behavior, or reveal animations |
| Does the color palette look like a Tailwind preset? | Develop owned colors, consider monochromatic with single accent |

---

## 13. UX Writing & Microcopy

**Core concept:** Every word in a UI is a design decision. Bad microcopy creates confusion even in well-designed interfaces. Good microcopy is invisible.

**Button labels -- always verb + object:**
- "Create account" not "Submit"
- "Save changes" not "OK"
- "Delete 5 items" not "Yes" (show counts)
- "Delete" = permanent, "Remove" = recoverable. Pick one convention and be consistent

**Error messages must answer three questions:**
1. What happened? ("Your payment was declined")
2. Why? ("The card on file has expired")
3. How to fix it? ("Update your payment method to continue")
- Never use "Invalid input," "Error occurred," or generic messages
- Never use humor for errors. Frustration + jokes = rage

**Empty states are onboarding moments, not dead ends:**
- Show what this screen will look like with data
- Provide a clear CTA to add the first item
- Explain the value ("Track your orders here once you make a purchase")

**Internationalization planning:**
- Plan for 30% text expansion (German, Finnish are longer)
- Never hardcode text in components
- Right-to-left layout support: use logical CSS properties (`margin-inline-start` not `margin-left`)

---

## 14. Optical Adjustments & Polish

**The squint test:** Blur your eyes. If you can't identify the hierarchy, it's broken. Everything looks same-weight when blurred = hierarchy problem.

**Optical corrections:**
- Text at `margin-left: 0` looks indented vs adjacent elements. Optical alignment > mathematical alignment
- Play/triangle icons need to shift right ~2px to look centered in circles
- Touch target expansion via `::before` pseudo-element (invisible, larger hit area)
- Use `text-balance` for headings, `text-pretty` for body paragraphs (modern CSS)
- Use `tabular-nums` for data tables and numbers that change (prices, stats, timers)
- Use `size-*` for square elements instead of `w-*` + `h-*` in Tailwind

**Dark mode typography adjustment:** Increase line-height by 0.05-0.1 for light text on dark backgrounds (light-on-dark is harder to read).

**Animation polish:**
- Exit animations should be 75% of enter duration (faster out than in)
- Use `grid-template-rows: 0fr` to `1fr` for height animations instead of animating `height`
- Limit blur to <= 8px, never animate blur continuously, never on large surfaces

**Input method detection (beyond breakpoints):**
```css
@media (pointer: coarse) { /* touch device: enlarge targets */ }
@media (hover: none) { /* no hover: don't rely on hover states */ }
```
Use these instead of just screen width to detect mobile interaction.

**Viewport height:** Use `h-dvh` (dynamic viewport height) not `h-screen` / `100vh` on mobile. `100vh` doesn't account for browser chrome.

---

## 15. Performance & Rendering

**Core concept:** Design decisions have direct performance consequences. Every font, image, animation, and layout choice affects Core Web Vitals. Performance is not a post-launch optimization; it's a design constraint.

**Core Web Vitals targets (75th percentile):**
- **LCP** (loading): < 2.5s. Hero images and fonts are the biggest culprits
- **INP** (responsiveness): < 200ms. Long JS tasks and layout thrashing kill this
- **CLS** (stability): < 0.1. Missing image dimensions and font swaps cause shifts

**Quick performance rules:**
- Only animate `transform` and `opacity` for 60fps (16.66ms/frame budget)
- Never lazy-load the LCP image. Use `loading="eager"` and `fetchpriority="high"`
- Set `width` and `height` (or `aspect-ratio`) on every `<img>` to prevent CLS
- Use `font-display: swap` for body text, match fallback metrics with `size-adjust`/`ascent-override`
- Total font budget: under 200KB (use variable fonts + subsetting)
- Use `content-visibility: auto` on below-fold sections (7x rendering boost)
- Apply `will-change` only just before animation, remove after
- Prefer AVIF > WebP > JPEG. Use `<picture>` for format negotiation
- Batch DOM reads before writes to avoid layout thrashing
- Use `svh` for stable sizing, `dvh` for dynamic full-screen layouts. Never `100vh` on mobile
- Use logical properties (`margin-inline`, `padding-block`) for automatic RTL support
- Use `clamp()` for fluid spacing/type instead of media query breakpoints
- Container queries for component-level responsive, media queries for page-level layout
- Respect `prefers-reduced-motion`: replace spatial animation with opacity fades, keep essential indicators
- Test with `forced-colors: active` for Windows High Contrast Mode
- Use `::selection` colors that match your brand. Use `-webkit-font-smoothing: antialiased` only on dark backgrounds

See: [references/performance-and-polish.md](references/performance-and-polish.md)

---

## 16. Design Psychology & Cognitive Laws

**Core concept:** Design decisions are grounded in how the human brain processes visual information. These laws provide the "why" behind every spacing rule, hierarchy technique, and layout decision in this system.

**Key laws and their design implications:**

| Law | Rule | Design Application |
|-----|------|-------------------|
| **Fitts's Law** | T = a + b x log2(1 + D/W) | Make targets large and close. CTAs full-width on mobile. Confirmations near triggers |
| **Hick's Law** | Decision time = b x log2(n+1) | Max 5-7 nav items. Progressive disclosure. One primary CTA per section |
| **Miller's Law** | Working memory: 7 +/- 2 items | Max 5-7 nav items, 4-6 dashboard KPIs, chunk phone numbers and card numbers |
| **Jakob's Law** | Users expect your site to work like others | Logo top-left, search top-right, cart top-right, X to close modals |
| **Doherty Threshold** | Productivity at < 400ms response | Skeleton loading after 200ms, progress bar after 1s, background process after 10s |
| **Von Restorff** | Different items stand out | Highlight recommended pricing plan, primary CTA distinct from all others |
| **Peak-End Rule** | Experience judged by peak + ending | Make first interaction delightful, make confirmation/success page memorable |
| **Zeigarnik Effect** | Incomplete tasks create tension | Progress bars for multi-step flows, "complete your profile" prompts |
| **Serial Position** | First and last items remembered most | Put key nav items first and last in tab bar |
| **Cognitive Load** | Minimize extraneous load | Progressive disclosure, recognition over recall, consistent patterns, defaults |
| **Weber's Law** | JND is proportional (~10%) | Spacing steps must differ by 20%+. Font sizes at least 20% apart. 4px differences are imperceptible |
| **Aesthetic-Usability** | Beautiful = perceived as more usable | Visual polish directly affects perceived usability. Invest in polish after interaction design is solid |

**Scanning patterns:**
- **F-pattern:** content-heavy pages (articles, feeds). Front-load sentences, bold keywords on left edge
- **Z-pattern:** minimal pages (landing, marketing). Logo top-left, CTA top-right, main CTA bottom-right
- **Gestalt proximity:** spacing within groups < spacing between groups (minimum 2:1 ratio)
- **Gestalt similarity:** same visual treatment = same function. Break similarity to draw attention

See: [references/design-psychology.md](references/design-psychology.md)

---

## 17. Forms & Data Patterns

**Core concept:** Forms are where users do real work. Bad form design loses users, bad data display hides insights. Both need systematic treatment.

**Form quick rules:**
- One column layout (converts better than multi-column)
- Validate on blur, not on keystroke (less aggressive)
- Label above input, never floating labels for critical forms
- Submit button = specific verb ("Create account", not "Submit")
- Break long forms into 3-5 step wizards (Hick's Law)
- Always set `autocomplete` attributes (browser autofill is free UX)
- Use `inputmode` to show the right mobile keyboard (numeric, tel, email, url)

**Data display quick rules:**
- Skeleton loading after 200ms delay (skip for fast loads)
- Skeletons must match content shape (content-shaped, not generic rectangles)
- Empty states: illustration + explanation + CTA (never blank white space)
- Optimistic UI for actions that succeed >99% of the time (likes, bookmarks)
- Toast auto-dismiss: 3-5s success, 5-7s info, persistent for errors
- Use `tabular-nums` for all numeric data (prices, stats, timers)
- Use `Intl.NumberFormat` for compact notation (1.2K, 3.4M), currency, percentages
- Tables: sticky headers, horizontal scroll on mobile, zebra striping, row hover

**Error message formula:** What happened + Why + How to fix.
"Your payment was declined. The card on file has expired. Update your payment method to continue."

See: [references/advanced-patterns.md](references/advanced-patterns.md)

---

## 18. Modern CSS Techniques

**Core concept:** Modern CSS (2024-2026) eliminates the need for many JavaScript patterns. Native CSS now handles view transitions, scroll-driven animations, anchor positioning, popovers, and container queries.

**Must-know modern CSS:**

| Feature | What It Replaces | Use When |
|---------|-----------------|----------|
| **View Transitions API** | JS page transition libraries | SPA/MPA page transitions, shared element animations |
| **Scroll-Driven Animations** | JS scroll handlers, IntersectionObserver | Parallax, progress indicators, reveal-on-scroll |
| **`@starting-style`** | JS for animating from display:none | Modal/dialog entry animations, toast appearances |
| **Anchor Positioning** | JS tooltip/popover positioning | Tooltips, dropdowns, popovers tied to trigger elements |
| **Popover API** | JS modal/dropdown logic | Light-dismiss menus, tooltips (native, no z-index wars) |
| **`<dialog>`** | JS modal with focus trapping | Modals with native `::backdrop`, `inert`, focus management |
| **`:has()` selector** | JS parent selection, class toggling | Form validation styling, conditional layouts |
| **Container queries** | Media queries for components | Component-level responsive design (cards, widgets) |
| **`color-mix()`** | Sass/JS color functions | Tints/shades/alpha without preprocessors |
| **`light-dark()`** | `prefers-color-scheme` media query | Inline dark mode colors |
| **CSS `@layer`** | `!important` battles, specificity hacks | Managing cascade in design systems |
| **Subgrid** | JS grid alignment, nested flexbox hacks | Aligning nested grid children to parent tracks |
| **`@scope`** | BEM, CSS Modules, Shadow DOM | Component-scoped styles without build tools |

**Key code patterns:**
```css
/* Scroll-driven animation (no JS) */
@keyframes reveal { from { opacity: 0; transform: translateY(20px); } }
.card {
  animation: reveal linear both;
  animation-timeline: view();
  animation-range: entry 0% entry 100%;
}

/* Animate from display:none */
dialog { transition: opacity 0.3s, display 0.3s allow-discrete; }
@starting-style { dialog[open] { opacity: 0; } }

/* Container queries */
.card-container { container-type: inline-size; }
@container (min-width: 400px) { .card { flex-direction: row; } }

/* Parent selection */
.form-group:has(:invalid) { border-color: var(--color-destructive); }
```

See: [references/cutting-edge-css-2024-2026.md](references/cutting-edge-css-2024-2026.md)

---

## 19. Mandatory Pre-Design Context

**Before writing any UI code, gather this context (ask if not provided):**

| Question | Why it matters |
|----------|---------------|
| Who is the target user? | Senior exec vs college student = completely different visual weight |
| What is the core action? | Every screen has ONE primary thing the user should do |
| What platform(s)? | Web-only, iOS-native, cross-platform all require different patterns |
| What's the brand tone? | Playful vs corporate vs luxury changes every design decision |
| Is this a product app or marketing site? | Product = usability first. Marketing = impact first |
| What existing design system? | Don't reinvent if shadcn/Tailwind/Material is already in use |

**If confidence is low on any answer, STOP and ask.** Bad assumptions create bad designs.

---

## Design Process

### 1. Grayscale First
Design the entire layout in grayscale. If the hierarchy doesn't work without color, color won't save it.

### 2. Mobile First
Start with the smallest screen. Scale up, don't shrink down. Mobile forces you to prioritize.

### 3. Content First
Real content (or realistic placeholders) first, layout second. Lorem ipsum hides hierarchy problems.

### 4. States Before Polish
Design all states (empty, loading, error, success) before adding animations or micro-interactions.

### 5. Signature Moment
Every project should have at least one moment worth screenshotting. The thing someone would share. Design this first if it's a brand/marketing site.

---

## Common Mistakes

| Mistake | Why It Fails | Fix |
|---------|-------------|-----|
| Arbitrary spacing values (13px, 17px) | Inconsistency, visual noise | Stick to the 4/8/16/24/32/48/64 scale |
| Everything centered | No tension, no energy, boring | Left-align by default, center only short headlines and CTAs |
| Using `ease` or `linear` easing | Mechanical, lifeless motion | Use expo out `(0.16, 1, 0.3, 1)` or quart out `(0.25, 1, 0.5, 1)` |
| Pure `#000000` and `#ffffff` | Harsh, lifeless, digital feel | Use `#0a0a0a` and `#fafaf9` (slightly warm) |
| Same font size for everything | No hierarchy, nothing stands out | Push scale contrast to 5:1+ between display and body |
| Hamburger menu on mobile | Hides navigation, low discoverability | Tab bar with 3-5 items visible |
| No loading/empty/error states | App feels broken when data is absent | Design every state before shipping |
| Animating width/height/margin | 60fps failure, jank | Only animate `transform` and `opacity` |
| `placeholder` as label | Disappears on focus, fails accessibility | Label above input, placeholder as example |
| Same design for iOS and Android | Feels foreign on both platforms | Respect platform conventions |
| Tiny tap targets on mobile | Frustrating misses, rage taps | 44pt minimum, 8pt spacing between targets |
| No focus states | Keyboard users are locked out | Visible `focus-visible` on all interactive elements |
| Over-engineering first pass | Delays shipping, premature optimization | Nail the hierarchy, spacing, and states first. Polish later |

---

## Quick Diagnostic

Audit any UI:

| Question | If No | Action |
|----------|-------|--------|
| Does hierarchy read when squinting (blur test)? | Elements competing | Increase contrast between primary/secondary/tertiary |
| Does it work in grayscale? | Relying on color for hierarchy | Strengthen size/weight/spacing hierarchy |
| Is there enough white space? | Probably too dense | Increase spacing, especially between groups |
| Does spacing follow the scale? | Arbitrary values | Use 4/8/16/24/32/48/64 only |
| Is text width constrained? | Long lines cause fatigue | `max-w-prose` (~65ch) for text blocks |
| Do colors have sufficient contrast? | Accessibility failure | Test with WCAG checker, use gray-700+ on white |
| Are all interactive elements 44px+ touch? | Mobile frustration | Increase tap targets, add padding |
| Can you tab through the entire UI? | Keyboard users blocked | Add `tabindex`, logical order, focus-visible |
| Do all components handle loading/error/empty? | App feels broken at edges | Design every state |
| Does it look right on both light and dark? | Broken in one mode | Use semantic color tokens, test both |
| Is motion purposeful (not decorative)? | Animation for animation's sake | Remove or justify every moving element |
| Would it feel native on mobile? | Wrong platform patterns | Respect iOS/Android conventions |
| Could someone identify this as AI-generated? | Generic aesthetics | Run Anti-Slop checklist (Section 12) |
| Do forms have proper autocomplete and inputmode? | Mobile UX suffers | Add autocomplete tokens and inputmode attributes |
| Are images sized with aspect-ratio/width/height? | CLS failures | Prevent layout shift with explicit dimensions |
| Would a first-time user know what to do? | Cognitive overload | Apply Hick's Law: fewer choices, progressive disclosure |

---

## Reference Files

### Core Design (Sections 1-10)
- [**typography-and-fonts.md**](references/typography-and-fonts.md): Font pairing, fluid type scales, tracking, variable fonts, font loading, cross-platform type
- [**layout-and-responsive.md**](references/layout-and-responsive.md): Grid systems, breakpoints, mobile-first, safe areas, container queries, responsive patterns
- [**color-and-theming.md**](references/color-and-theming.md): OKLCH palette generation, dark mode, semantic tokens, platform-specific color, dynamic color
- [**animation-and-motion.md**](references/animation-and-motion.md): Easing functions, scroll animations, page transitions, micro-interactions, GSAP/Motion/CSS
- [**mobile-patterns.md**](references/mobile-patterns.md): iOS HIG, Material 3, React Native/Flutter, gesture design, haptics
- [**accessibility.md**](references/accessibility.md): WCAG 2.2 AA checklist, ARIA patterns, focus management, screen readers, keyboard nav
- [**components-and-states.md**](references/components-and-states.md): Component state matrix (12x9), button/input/card/modal/toast patterns

### Extended Design (Sections 11-18)
- [**ecosystem-and-libraries.md**](references/ecosystem-and-libraries.md): shadcn/ui, Aceternity UI, Magic UI, Motion, GSAP, Lenis, 21st.dev, signature moment recipes, component name lookups
- [**design-psychology.md**](references/design-psychology.md): Gestalt principles, Fitts's/Hick's/Miller's/Jakob's Law, Doherty Threshold, Von Restorff, Peak-End Rule, Zeigarnik, cognitive load, F/Z-pattern scanning, Weber's Law
- [**advanced-patterns.md**](references/advanced-patterns.md): Multi-step forms, inline validation, smart defaults, autofill, tables, skeleton loading, empty states, optimistic UI, toasts, progress indicators, error handling, onboarding
- [**cutting-edge-css-2024-2026.md**](references/cutting-edge-css-2024-2026.md): View Transitions, Scroll-Driven Animations, Anchor Positioning, @starting-style, Popover API, dialog, :has(), @layer, @scope, subgrid, color-mix(), light-dark()
- [**performance-and-polish.md**](references/performance-and-polish.md): Core Web Vitals, font/image optimization, CSS performance, animation performance, subpixel rendering, focus indicators, reduced motion, high contrast, fluid clamp(), logical properties, viewport units

---

## Sources

**Books & standards:** Refactoring UI (Wathan & Schoger), The Design of Everyday Things (Don Norman), Don't Make Me Think (Krug), On Web Typography (Santa Maria), Hooked (Nir Eyal), Laws of UX (Yablonski), Apple Human Interface Guidelines, Material Design 3 (Google), WCAG 2.2, Nielsen's 10 Usability Heuristics.

**Community skills:** Anthropic frontend-design, pbakaus/impeccable (anti-slop, UX writing, optical adjustments), ibelick/ui-skills (motion performance, accessibility audit, CSS techniques), nextlevelbuilder/ui-ux-pro-max (searchable style/pattern database), wondelai/skills (12 design skills).

**Libraries & ecosystem:** shadcn/ui, Aceternity UI, Magic UI, Motion (Framer Motion), GSAP, Lenis, 21st.dev, Animate UI, Motion Primitives, Tailwind CSS.

**Agency craft:** Locomotive, Studio Freight, AREA 17, Active Theory, Hello Monday. Framer template trends (5,800+ templates analyzed).
