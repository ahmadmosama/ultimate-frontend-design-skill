# Animation & Motion Reference

Complete reference for easing, page transitions, scroll animations, micro-interactions, mobile motion, haptics, and performance.

---

## 1. Easing Functions

### CSS Custom Properties

```css
:root {
  /* --- Out (decelerate — most UI motion) --- */
  --ease-out-quad:    cubic-bezier(0.25, 0.46, 0.45, 0.94);
  --ease-out-cubic:   cubic-bezier(0.215, 0.61, 0.355, 1);
  --ease-out-quart:   cubic-bezier(0.165, 0.84, 0.44, 1);
  --ease-out-quint:   cubic-bezier(0.23, 1, 0.32, 1);
  --ease-out-expo:    cubic-bezier(0.16, 1, 0.3, 1);
  --ease-out-back:    cubic-bezier(0.34, 1.56, 0.64, 1);

  /* --- In (accelerate — exits, removals) --- */
  --ease-in-quad:     cubic-bezier(0.55, 0.085, 0.68, 0.53);
  --ease-in-cubic:    cubic-bezier(0.55, 0.055, 0.675, 0.19);
  --ease-in-expo:     cubic-bezier(0.95, 0.05, 0.795, 0.035);

  /* --- In-Out (symmetric — looping, modal open/close) --- */
  --ease-in-out-quad: cubic-bezier(0.455, 0.03, 0.515, 0.955);
  --ease-in-out-expo: cubic-bezier(0.87, 0, 0.13, 1);

  /* --- Elastic / Spring (playful UI, toasts, badges) --- */
  --ease-elastic:     cubic-bezier(0.68, -0.55, 0.265, 1.55);

  /* --- Standard tokens --- */
  --duration-fast:    120ms;
  --duration-normal:  240ms;
  --duration-slow:    400ms;
  --duration-page:    600ms;
}
```

### GSAP Equivalents

| CSS var              | GSAP ease           |
|----------------------|---------------------|
| `--ease-out-expo`    | `"expo.out"`        |
| `--ease-out-quart`   | `"quart.out"`       |
| `--ease-out-back`    | `"back.out(1.7)"`   |
| `--ease-in-out-expo` | `"expo.inOut"`      |
| `--ease-elastic`     | `"elastic.out(1, 0.5)"` |

### Framer Motion Spring Configs

```tsx
const springs = {
  snappy:  { type: "spring", stiffness: 500, damping: 30 },
  smooth:  { type: "spring", stiffness: 200, damping: 24 },
  bouncy:  { type: "spring", stiffness: 400, damping: 15 },
  gentle:  { type: "spring", stiffness: 120, damping: 20 },
  default: { type: "spring", stiffness: 260, damping: 20 },
};
```

### When to Use Each

| Context | Easing | Why |
|---------|--------|-----|
| Element entering | `out-expo` / `out-quart` | Fast start, gentle land |
| Element exiting | `in-expo` | Accelerates away, feels intentional |
| Modal open/close | `in-out-expo` | Symmetric, smooth |
| Playful UI (badge, toast pop) | `out-back` or spring `bouncy` | Slight overshoot feels alive |
| Hover / micro-interaction | `out-cubic` | Subtle, not distracting |
| Scroll-linked | `linear` or none | Tied to scroll position directly |

---

## 2. Page Load Sequences

### CSS-Only Staggered Reveal

```css
.reveal-item {
  opacity: 0;
  transform: translateY(24px);
  animation: revealUp 0.6s var(--ease-out-expo) forwards;
}

.reveal-item:nth-child(1) { animation-delay: 0ms; }
.reveal-item:nth-child(2) { animation-delay: 80ms; }
.reveal-item:nth-child(3) { animation-delay: 160ms; }
.reveal-item:nth-child(4) { animation-delay: 240ms; }

@keyframes revealUp {
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
```

### GSAP Stagger

```js
gsap.from(".reveal-item", {
  y: 40,
  opacity: 0,
  duration: 0.8,
  ease: "expo.out",
  stagger: 0.08,
});
```

### Split Text Animation

```js
// Using GSAP SplitText or manual split
const text = document.querySelector(".hero-title");
text.innerHTML = text.textContent
  .split("")
  .map((ch) => `<span class="char">${ch === " " ? "&nbsp;" : ch}</span>`)
  .join("");

gsap.from(".char", {
  y: "110%",
  opacity: 0,
  duration: 0.6,
  ease: "expo.out",
  stagger: 0.025,
});
```

### Choreography Timeline (typical hero section)

```js
const tl = gsap.timeline({ defaults: { ease: "expo.out" } });

tl.from(".hero-tag",      { y: 20, opacity: 0, duration: 0.5 })
  .from(".hero-title",    { y: 30, opacity: 0, duration: 0.7 }, "-=0.3")
  .from(".hero-subtitle", { y: 20, opacity: 0, duration: 0.5 }, "-=0.4")
  .from(".hero-cta",      { y: 20, opacity: 0, duration: 0.5 }, "-=0.3")
  .from(".hero-image",    { scale: 0.95, opacity: 0, duration: 0.8 }, "-=0.5");
```

Order rule: top-to-bottom, large elements last, overlap by ~60%.

---

## 3. Scroll-Triggered Animations

### Vanilla JS — Intersection Observer

```js
const observer = new IntersectionObserver(
  (entries) => {
    entries.forEach((entry) => {
      if (entry.isIntersecting) {
        entry.target.classList.add("is-visible");
        observer.unobserve(entry.target);
      }
    });
  },
  { threshold: 0.15, rootMargin: "0px 0px -60px 0px" }
);

document.querySelectorAll("[data-animate]").forEach((el) => observer.observe(el));
```

```css
[data-animate] {
  opacity: 0;
  transform: translateY(32px);
  transition: opacity 0.6s var(--ease-out-expo), transform 0.6s var(--ease-out-expo);
}
[data-animate].is-visible {
  opacity: 1;
  transform: translateY(0);
}
```

### GSAP ScrollTrigger — Fade Up

```js
gsap.utils.toArray(".fade-up").forEach((el) => {
  gsap.from(el, {
    y: 60,
    opacity: 0,
    duration: 0.8,
    ease: "expo.out",
    scrollTrigger: {
      trigger: el,
      start: "top 85%",
      toggleActions: "play none none none",
    },
  });
});
```

### Parallax

```js
gsap.to(".parallax-bg", {
  yPercent: -20,
  ease: "none",
  scrollTrigger: {
    trigger: ".parallax-section",
    start: "top bottom",
    end: "bottom top",
    scrub: true,
  },
});
```

### Horizontal Scroll Section

```js
const track = document.querySelector(".horizontal-track");
const panels = gsap.utils.toArray(".horizontal-track .panel");

gsap.to(panels, {
  xPercent: -100 * (panels.length - 1),
  ease: "none",
  scrollTrigger: {
    trigger: track,
    pin: true,
    scrub: 1,
    end: () => `+=${track.scrollWidth - window.innerWidth}`,
  },
});
```

### Pinned Section with Progress

```js
gsap.to(".progress-bar", {
  scaleX: 1,
  ease: "none",
  scrollTrigger: {
    trigger: ".pinned-section",
    pin: true,
    start: "top top",
    end: "+=200%",
    scrub: true,
  },
});
```

### Lenis Smooth Scroll Setup

```js
import Lenis from "@studio-freight/lenis";

const lenis = new Lenis({ lerp: 0.1, smoothWheel: true });

function raf(time) {
  lenis.raf(time);
  requestAnimationFrame(raf);
}
requestAnimationFrame(raf);

// Sync with GSAP ScrollTrigger
lenis.on("scroll", ScrollTrigger.update);
gsap.ticker.add((time) => lenis.raf(time * 1000));
gsap.ticker.lagSmoothing(0);
```

---

## 4. Micro-Interactions

### Button — Magnetic Hover

```js
document.querySelectorAll(".btn-magnetic").forEach((btn) => {
  btn.addEventListener("mousemove", (e) => {
    const rect = btn.getBoundingClientRect();
    const x = e.clientX - rect.left - rect.width / 2;
    const y = e.clientY - rect.top - rect.height / 2;
    gsap.to(btn, { x: x * 0.3, y: y * 0.3, duration: 0.3, ease: "power2.out" });
  });
  btn.addEventListener("mouseleave", () => {
    gsap.to(btn, { x: 0, y: 0, duration: 0.5, ease: "elastic.out(1, 0.4)" });
  });
});
```

### Button — Text Slide on Hover

```css
.btn-slide {
  overflow: hidden;
  position: relative;
}
.btn-slide span {
  display: block;
  transition: transform 0.4s var(--ease-out-expo);
}
.btn-slide span:nth-child(2) {
  position: absolute;
  inset: 0;
  display: grid;
  place-items: center;
  transform: translateY(100%);
}
.btn-slide:hover span:first-child { transform: translateY(-100%); }
.btn-slide:hover span:nth-child(2) { transform: translateY(0); }
```

### Button — Background Fill

```css
.btn-fill {
  position: relative;
  overflow: hidden;
  z-index: 1;
}
.btn-fill::before {
  content: "";
  position: absolute;
  inset: 0;
  background: var(--color-primary);
  transform: scaleX(0);
  transform-origin: right;
  transition: transform 0.4s var(--ease-out-expo);
  z-index: -1;
}
.btn-fill:hover::before {
  transform: scaleX(1);
  transform-origin: left;
}
```

### Link Underline Animation

```css
.link-underline {
  position: relative;
  text-decoration: none;
}
.link-underline::after {
  content: "";
  position: absolute;
  bottom: -2px;
  left: 0;
  width: 100%;
  height: 1.5px;
  background: currentColor;
  transform: scaleX(0);
  transform-origin: right;
  transition: transform 0.35s var(--ease-out-cubic);
}
.link-underline:hover::after {
  transform: scaleX(1);
  transform-origin: left;
}
```

### Card — Tilt on Hover

```js
document.querySelectorAll(".card-tilt").forEach((card) => {
  card.addEventListener("mousemove", (e) => {
    const rect = card.getBoundingClientRect();
    const x = ((e.clientX - rect.left) / rect.width - 0.5) * 12;
    const y = ((e.clientY - rect.top) / rect.height - 0.5) * -12;
    card.style.transform = `perspective(600px) rotateY(${x}deg) rotateX(${y}deg)`;
  });
  card.addEventListener("mouseleave", () => {
    card.style.transition = "transform 0.5s var(--ease-out-expo)";
    card.style.transform = "perspective(600px) rotateY(0) rotateX(0)";
  });
});
```

### Card — Lift on Hover

```css
.card-lift {
  transition: transform 0.35s var(--ease-out-expo), box-shadow 0.35s var(--ease-out-expo);
}
.card-lift:hover {
  transform: translateY(-6px);
  box-shadow: 0 12px 32px rgba(0, 0, 0, 0.1);
}
```

### Toggle Animation

```css
.toggle-track {
  width: 44px; height: 24px;
  border-radius: 12px;
  background: var(--color-muted);
  transition: background 0.25s var(--ease-out-cubic);
}
.toggle-track[aria-checked="true"] { background: var(--color-primary); }
.toggle-thumb {
  width: 20px; height: 20px;
  border-radius: 50%;
  background: white;
  transition: transform 0.3s var(--ease-out-back);
}
.toggle-track[aria-checked="true"] .toggle-thumb {
  transform: translateX(20px);
}
```

### Skeleton Loading

```css
.skeleton {
  background: linear-gradient(90deg, #e0e0e0 25%, #f0f0f0 50%, #e0e0e0 75%);
  background-size: 200% 100%;
  animation: shimmer 1.5s infinite ease-in-out;
  border-radius: 4px;
}
@keyframes shimmer {
  0%   { background-position: 200% 0; }
  100% { background-position: -200% 0; }
}
```

### Spinner

```css
.spinner {
  width: 24px; height: 24px;
  border: 2.5px solid var(--color-muted);
  border-top-color: var(--color-primary);
  border-radius: 50%;
  animation: spin 0.7s linear infinite;
}
@keyframes spin { to { transform: rotate(360deg); } }
```

---

## 5. Page Transitions

### Barba.js — Fade Transition

```js
import barba from "@barba/core";

barba.init({
  transitions: [
    {
      leave({ current }) {
        return gsap.to(current.container, { opacity: 0, duration: 0.4, ease: "expo.in" });
      },
      enter({ next }) {
        return gsap.from(next.container, { opacity: 0, duration: 0.5, ease: "expo.out" });
      },
    },
  ],
});
```

### Barba.js — Wipe Transition

```js
barba.init({
  transitions: [
    {
      leave() {
        return gsap.to(".transition-cover", {
          scaleY: 1,
          transformOrigin: "bottom",
          duration: 0.5,
          ease: "expo.inOut",
        });
      },
      enter() {
        return gsap.to(".transition-cover", {
          scaleY: 0,
          transformOrigin: "top",
          duration: 0.5,
          ease: "expo.inOut",
          delay: 0.1,
        });
      },
    },
  ],
});
```

### View Transitions API (Chrome 111+)

```js
document.querySelectorAll("a[data-transition]").forEach((link) => {
  link.addEventListener("click", (e) => {
    e.preventDefault();
    document.startViewTransition(() => {
      // swap DOM content here
      return fetch(link.href)
        .then((r) => r.text())
        .then((html) => {
          document.querySelector("main").innerHTML =
            new DOMParser().parseFromString(html, "text/html").querySelector("main").innerHTML;
        });
    });
  });
});
```

```css
::view-transition-old(root) {
  animation: fade-out 0.3s ease-in forwards;
}
::view-transition-new(root) {
  animation: fade-in 0.4s ease-out;
}
```

### Next.js + Framer Motion Route Transition

```tsx
// app/template.tsx (App Router)
"use client";
import { motion } from "framer-motion";

export default function Template({ children }: { children: React.ReactNode }) {
  return (
    <motion.div
      initial={{ opacity: 0, y: 12 }}
      animate={{ opacity: 1, y: 0 }}
      exit={{ opacity: 0, y: -8 }}
      transition={{ duration: 0.35, ease: [0.16, 1, 0.3, 1] }}
    >
      {children}
    </motion.div>
  );
}
```

---

## 6. Mobile Animation

### iOS Spring Parameters

```swift
// UIKit
UIView.animate(withDuration: 0.5, delay: 0,
  usingSpringWithDamping: 0.7, initialSpringVelocity: 0.4,
  options: .curveEaseOut, animations: { view.transform = .identity })

// SwiftUI
.animation(.spring(response: 0.45, dampingFraction: 0.7, blendDuration: 0), value: isActive)
// interactiveSpring for gestures:
.animation(.interactiveSpring(response: 0.3, dampingFraction: 0.8, blendDuration: 0.1), value: offset)
```

### Android Material Motion

```kotlin
// Container Transform (list item → detail)
val transform = MaterialContainerTransform().apply {
    duration = 350L
    interpolator = FastOutSlowInInterpolator()
    fadeMode = MaterialContainerTransform.FADE_MODE_THROUGH
}

// Shared Axis (forward/backward navigation)
val sharedAxis = MaterialSharedAxis(MaterialSharedAxis.X, true).apply {
    duration = 300L
}

// Fade Through (unrelated content swap)
val fadeThrough = MaterialFadeThrough().apply {
    duration = 250L
}
```

### React Native Animated

```tsx
const fadeAnim = useRef(new Animated.Value(0)).current;

Animated.spring(fadeAnim, {
  toValue: 1,
  tension: 50,
  friction: 7,
  useNativeDriver: true,
}).start();

// In JSX:
<Animated.View style={{ opacity: fadeAnim, transform: [{ translateY: fadeAnim.interpolate({
  inputRange: [0, 1], outputRange: [20, 0],
}) }] }} />
```

### React Native Reanimated (gesture-driven)

```tsx
import { useSharedValue, useAnimatedStyle, withSpring } from "react-native-reanimated";
import { Gesture, GestureDetector } from "react-native-gesture-handler";

const translateX = useSharedValue(0);

const pan = Gesture.Pan()
  .onUpdate((e) => { translateX.value = e.translationX; })
  .onEnd(() => { translateX.value = withSpring(0, { damping: 15, stiffness: 150 }); });

const animStyle = useAnimatedStyle(() => ({
  transform: [{ translateX: translateX.value }],
}));
```

---

## 7. Haptic Feedback

### iOS — UIImpactFeedbackGenerator

| Type | Feel | Use |
|------|------|-----|
| `.light` | Gentle tap | Toggle, selection change |
| `.medium` | Firm tap | Action confirm, snap point |
| `.heavy` | Strong thud | Destructive action, impact |
| `.soft` | Muted thump | Background state change |
| `.rigid` | Sharp click | Precise selection, slider tick |

```swift
let generator = UIImpactFeedbackGenerator(style: .medium)
generator.impactOccurred()

// Notification feedback
let notif = UINotificationFeedbackGenerator()
notif.notificationOccurred(.success) // .success | .warning | .error

// Selection feedback (scrolling through picker)
let selection = UISelectionFeedbackGenerator()
selection.selectionChanged()
```

### Android — HapticFeedbackConstants

```kotlin
view.performHapticFeedback(HapticFeedbackConstants.CONFIRM)      // success
view.performHapticFeedback(HapticFeedbackConstants.REJECT)        // error
view.performHapticFeedback(HapticFeedbackConstants.TOGGLE_ON)     // toggle
view.performHapticFeedback(HapticFeedbackConstants.LONG_PRESS)    // long press menu
view.performHapticFeedback(HapticFeedbackConstants.CONTEXT_CLICK) // selection
```

### When to Trigger

| Action | iOS | Android |
|--------|-----|---------|
| Toggle on/off | `.light` | `TOGGLE_ON/OFF` |
| Destructive confirm | `.heavy` + `.warning` | `REJECT` |
| Success (payment, save) | `notif .success` | `CONFIRM` |
| Error (validation fail) | `notif .error` | `REJECT` |
| Long-press menu | `.medium` | `LONG_PRESS` |
| Slider tick / snap | `selection` | `CONTEXT_CLICK` |
| Pull-to-refresh threshold | `.medium` | `CONFIRM` |

---

## 8. Performance

### GPU-Accelerated Properties Only

Only animate `transform` and `opacity`. Everything else (width, height, top, left, margin, padding, background-color, border-radius) causes layout or paint and cannot hit 60fps reliably.

```css
/* GOOD */
.animate { transition: transform 0.3s, opacity 0.3s; }

/* BAD — triggers layout */
.animate { transition: width 0.3s, left 0.3s; }
```

### will-change

```css
/* Apply BEFORE animation starts, remove after */
.will-animate { will-change: transform, opacity; }

/* Never apply globally — it reserves GPU memory per element */
/* Apply via JS just before animation, remove in transitionend */
```

```js
el.style.willChange = "transform, opacity";
el.addEventListener("transitionend", () => {
  el.style.willChange = "auto";
}, { once: true });
```

### prefers-reduced-motion

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

```js
const prefersReduced = window.matchMedia("(prefers-reduced-motion: reduce)").matches;

if (prefersReduced) {
  gsap.globalTimeline.timeScale(100); // effectively instant
}
```

### Lazy Animation Loading

```js
// Only register ScrollTrigger animations when section is near viewport
const lazyObserver = new IntersectionObserver(
  (entries) => {
    entries.forEach((entry) => {
      if (entry.isIntersecting) {
        initSectionAnimations(entry.target);
        lazyObserver.unobserve(entry.target);
      }
    });
  },
  { rootMargin: "200px" }
);

document.querySelectorAll("[data-lazy-animate]").forEach((el) => lazyObserver.observe(el));
```

### 60fps Checklist

1. Animate only `transform` and `opacity`
2. Use `will-change` sparingly and remove after
3. Avoid animating more than ~20 elements simultaneously
4. Use `requestAnimationFrame` for JS animations, never `setInterval`
5. Debounce scroll/resize handlers; prefer passive listeners
6. Test on mid-range devices, not just M-series MacBooks

---

## 9. Timing Reference Table

| Action | Duration | Easing | Example |
|--------|----------|--------|---------|
| Button hover (bg/color) | 150-200ms | `out-cubic` | Fill color change |
| Button press (scale) | 100ms | `out-quart` | Scale to 0.97 |
| Tooltip show | 150ms | `out-expo` | Fade + translate up 4px |
| Tooltip hide | 100ms | `in-quad` | Fade out |
| Dropdown open | 200-250ms | `out-expo` | Scale Y from 0.95, fade in |
| Dropdown close | 150ms | `in-expo` | Scale Y to 0.95, fade out |
| Modal open | 300-400ms | `out-expo` | Scale from 0.95, fade overlay |
| Modal close | 200-250ms | `in-expo` | Scale to 0.95, fade out |
| Toast enter | 350ms | `out-back` | Slide up + slight overshoot |
| Toast exit | 250ms | `in-expo` | Slide down, fade |
| Page transition out | 300-400ms | `in-expo` | Fade or wipe |
| Page transition in | 400-600ms | `out-expo` | Fade or wipe |
| Scroll reveal | 600-800ms | `out-expo` | Translate Y 40px + fade |
| Stagger gap | 60-100ms | — | Between sibling items |
| Toggle switch | 250-300ms | `out-back` | Thumb slide with overshoot |
| Skeleton shimmer | 1500ms | `in-out-quad` | Infinite loop gradient |
| Spinner rotation | 700ms | `linear` | Infinite rotate |
| Accordion expand | 300ms | `out-expo` | Height auto + fade content |
| Tab switch content | 200ms | `out-cubic` | Cross-fade |
| Card hover lift | 300ms | `out-expo` | TranslateY -6px + shadow |
| Notification badge pop | 400ms | spring `bouncy` | Scale from 0 with overshoot |
| Progress bar fill | 400-600ms | `out-quart` | Width transition |
| Drawer slide in | 350ms | `out-expo` | TranslateX from off-screen |
| Drawer slide out | 250ms | `in-expo` | TranslateX to off-screen |
| Input focus ring | 150ms | `out-cubic` | Opacity + box-shadow |
| Checkbox check | 200ms | `out-back` | Scale from 0 with bounce |
| Menu item hover | 120ms | `out-quad` | Background color |
| Image zoom (lightbox) | 400ms | `out-expo` | Scale from thumbnail |
