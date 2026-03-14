# Accessibility Reference

## WCAG 2.2 AA Checklist

### Perceivable

| Requirement | Standard | Test Method | Common Failure |
|---|---|---|---|
| Text alternatives for images | 1.1.1 (A) | Check every `<img>` has `alt`; decorative images use `alt=""` | Missing alt, or alt says "image.png" |
| Captions for video | 1.2.2 (A) | Play video, verify synced captions exist | Auto-generated captions with no review |
| Audio descriptions for video | 1.2.5 (AA) | Check visual-only info is narrated | Key info shown on screen but never spoken |
| Content order is meaningful without CSS | 1.3.1 (A) | Disable CSS, read top-to-bottom | Visual order differs from DOM order |
| Sensory instructions don't rely on shape/color alone | 1.3.3 (A) | Search for "click the green button" type instructions | "Errors are shown in red" with no icon |
| Content works in both orientations | 1.3.4 (AA) | Rotate device/viewport | Layout breaks or content hidden in landscape |
| Input purpose is identified | 1.3.5 (AA) | Check `autocomplete` attr on personal data fields | Missing `autocomplete="email"` on email input |
| Contrast ratio >= 4.5:1 for text | 1.4.3 (AA) | Use contrast checker on all text | Light gray (#999) on white (#fff) = 2.8:1 |
| Contrast ratio >= 3:1 for large text (18px+ bold or 24px+) | 1.4.3 (AA) | Check headings and large UI text | Assumes large text doesn't need checking |
| Text resizes to 200% without loss | 1.4.4 (AA) | Zoom browser to 200% | Fixed height containers clip text |
| No images of text | 1.4.5 (AA) | Search for text baked into images | Logo text in PNG instead of styled HTML |
| Reflow at 320px width (no horizontal scroll) | 1.4.10 (AA) | Set viewport to 320px | Tables or code blocks cause horizontal scroll |
| Non-text contrast >= 3:1 for UI components | 1.4.11 (AA) | Check borders, icons, focus indicators | Input borders at 1px #ccc on white |
| Text spacing overridable without breaking layout | 1.4.12 (AA) | Apply 1.5x line-height, 2x letter-spacing | Fixed-height containers overflow |

### Operable

| Requirement | Standard | Test Method | Common Failure |
|---|---|---|---|
| All functionality available via keyboard | 2.1.1 (A) | Tab through entire page, operate every control | Custom dropdowns only work with mouse |
| No keyboard traps | 2.1.2 (A) | Tab into every component, verify you can Tab out | Modal with no close-on-Escape |
| Skip to main content link | 2.4.1 (A) | Press Tab on page load, check for skip link | No skip link, or link is present but broken |
| Page has descriptive title | 2.4.2 (A) | Check `<title>` in each route | All pages say "My App" |
| Focus order is logical | 2.4.3 (A) | Tab through page, check sequence | Modal focus jumps to footer |
| Link purpose clear from text | 2.4.4 (A) | Read link text alone | Multiple "Click here" or "Read more" links |
| Multiple ways to find pages | 2.4.5 (AA) | Check for nav, search, or sitemap | Single-path navigation only |
| Headings and labels are descriptive | 2.4.6 (AA) | Read all headings out of context | Heading says "Section 1" instead of content summary |
| Focus is visible | 2.4.7 (AA) | Tab through page, look for focus ring | `outline: none` with no replacement |
| Focus not obscured by sticky elements | 2.4.11 (AA) | Tab while sticky header/footer is present | Sticky nav covers focused element |
| Dragging has non-drag alternative | 2.5.7 (AA) | Find drag interactions, look for alternative | Sortable list with drag-only reorder |
| Target size >= 24x24px | 2.5.8 (AA) | Measure interactive targets | Inline text links in dense paragraphs (exempt) |

### Understandable

| Requirement | Standard | Test Method | Common Failure |
|---|---|---|---|
| Page language declared | 3.1.1 (A) | Check `<html lang="en">` | Missing `lang` attribute |
| Consistent navigation across pages | 3.2.3 (AA) | Compare nav on 3+ pages | Nav items reorder between pages |
| Consistent component identification | 3.2.4 (AA) | Check same function = same label | "Search" on one page, "Find" on another |
| Error identification in text | 3.3.1 (A) | Submit invalid form, check error messages | Red border only, no text message |
| Labels or instructions for inputs | 3.3.2 (A) | Check every input has a visible label | Placeholder-only labels that disappear |
| Error suggestions provided | 3.3.3 (AA) | Trigger errors, check for fix suggestions | "Invalid input" with no guidance |
| Error prevention for legal/financial | 3.3.4 (AA) | Check review/confirm step before submit | One-click purchase with no confirmation |

### Robust

| Requirement | Standard | Test Method | Common Failure |
|---|---|---|---|
| Valid HTML parsing | 4.1.1 (A) | Run HTML validator | Duplicate IDs, unclosed tags |
| Name, role, value for custom components | 4.1.2 (A) | Inspect with screen reader | Custom toggle has no `role="switch"` |
| Status messages use live regions | 4.1.3 (AA) | Trigger status change, listen with screen reader | "Item added to cart" not announced |

---

## Focus Management

### Focus-visible CSS pattern

```css
/* Remove default outline, restore for keyboard only */
*:focus { outline: none; }
*:focus-visible {
  outline: 2px solid #2563eb;
  outline-offset: 2px;
  border-radius: 2px;
}

/* Tailwind equivalent */
/* focus:outline-none focus-visible:ring-2 focus-visible:ring-blue-600 focus-visible:ring-offset-2 */
```

### Focus trap for modals

```js
function trapFocus(modal) {
  const focusable = modal.querySelectorAll(
    'a[href], button:not([disabled]), input:not([disabled]), select, textarea, [tabindex]:not([tabindex="-1"])'
  );
  const first = focusable[0];
  const last = focusable[focusable.length - 1];

  modal.addEventListener('keydown', (e) => {
    if (e.key !== 'Tab') return;
    if (e.shiftKey && document.activeElement === first) {
      e.preventDefault(); last.focus();
    } else if (!e.shiftKey && document.activeElement === last) {
      e.preventDefault(); first.focus();
    }
  });
  first.focus();
}
```

### Skip link

```html
<a href="#main" class="sr-only focus:not-sr-only focus:absolute focus:top-4 focus:left-4 focus:z-50 focus:bg-white focus:px-4 focus:py-2 focus:rounded focus:shadow-lg">
  Skip to main content
</a>
<!-- ... nav ... -->
<main id="main" tabindex="-1">...</main>
```

### Roving tabindex for composite widgets

```html
<!-- Only the active item has tabindex="0"; others have tabindex="-1" -->
<div role="tablist">
  <button role="tab" tabindex="0" aria-selected="true">Tab 1</button>
  <button role="tab" tabindex="-1" aria-selected="false">Tab 2</button>
  <button role="tab" tabindex="-1" aria-selected="false">Tab 3</button>
</div>
```

Arrow keys move `tabindex="0"` between items. Tab moves focus out of the group entirely.

---

## Screen Reader Support

### Semantic HTML elements (use these first, ARIA second)

| Need | Use | NOT |
|---|---|---|
| Navigation | `<nav>` | `<div role="navigation">` |
| Main content | `<main>` | `<div role="main">` |
| Page header | `<header>` | `<div class="header">` |
| Section heading | `<h1>`-`<h6>` | `<div class="title">` |
| Button | `<button>` | `<div onclick>` or `<a href="#">` |
| Link | `<a href="...">` | `<span onclick>` |
| List | `<ul>/<ol>` + `<li>` | `<div>` with bullet characters |
| Table | `<table>` + `<th scope>` | CSS grid pretending to be a table |
| Form field | `<label for="id">` + `<input id="id">` | Placeholder as label |

### Essential ARIA roles and properties

| Pattern | ARIA | When to use |
|---|---|---|
| Modal | `role="dialog"` + `aria-modal="true"` + `aria-labelledby` | Custom dialog/modal |
| Toggle | `role="switch"` + `aria-checked` | On/off toggle (not checkbox) |
| Tab panel | `role="tablist/tab/tabpanel"` + `aria-selected` + `aria-controls` | Custom tabs |
| Expandable | `aria-expanded="true/false"` | Accordion, dropdown, collapsible |
| Loading | `aria-busy="true"` on container | While content is loading |
| Current page | `aria-current="page"` on nav link | Active nav item |
| Error | `aria-invalid="true"` + `aria-describedby` pointing to error text | Form validation |
| Required | `aria-required="true"` (or HTML `required`) | Required form fields |
| Hidden | `aria-hidden="true"` | Decorative icons, backdrop |

### Live regions

```html
<!-- Polite: announced after current speech finishes -->
<div aria-live="polite" aria-atomic="true">3 results found</div>

<!-- Assertive: interrupts current speech (use sparingly) -->
<div role="alert">Payment failed. Please try again.</div>

<!-- Status: polite live region with role -->
<div role="status">File uploaded successfully</div>
```

### Screen reader testing

**VoiceOver (macOS/iOS):** Cmd+F5 to toggle. Use VO keys (Ctrl+Option) + arrows to navigate. Check: headings rotor (VO+U), landmarks, form labels, live regions.

**TalkBack (Android):** Settings > Accessibility > TalkBack. Swipe right to move forward. Double-tap to activate. Check: heading navigation (swipe up/down to change granularity).

---

## Keyboard Navigation

### Required patterns for common components

| Component | Keys | Behavior |
|---|---|---|
| Button | Enter, Space | Activate |
| Link | Enter | Navigate |
| Checkbox | Space | Toggle |
| Radio group | Arrow keys | Move selection |
| Select/dropdown | Arrow keys, Enter, Escape | Navigate options, select, close |
| Modal | Escape | Close |
| Tabs | Arrow keys | Switch tab |
| Menu | Arrow keys, Enter, Escape | Navigate, select, close |
| Accordion | Enter, Space | Toggle section |
| Slider | Arrow keys | Adjust value |

### Preventing keyboard traps

```js
// Always provide Escape as an exit mechanism
document.addEventListener('keydown', (e) => {
  if (e.key === 'Escape') {
    closeModal();
    restoreFocus(); // Return focus to the element that opened the modal
  }
});
```

---

## Color & Contrast

### Requirements by element type

| Element | Min ratio | Example pass | Example fail |
|---|---|---|---|
| Body text (<18px) | 4.5:1 | #595959 on #fff (7:1) | #999 on #fff (2.8:1) |
| Large text (>=18px bold or >=24px) | 3:1 | #767676 on #fff (4.5:1) | #aaa on #fff (2.3:1) |
| UI components (borders, icons) | 3:1 | #767676 border on #fff | #ccc border on #fff (1.6:1) |
| Focus indicator | 3:1 | #2563eb ring on #fff | #93c5fd ring on #fff (2.1:1) |
| Disabled elements | None (exempt) | Gray on white is fine | N/A |

### Safe text/background combos

| Background | Safe text colors | Unsafe |
|---|---|---|
| White (#fff) | #000, #1a1a1a, #333, #595959 | #999, #aaa, #ccc |
| Light gray (#f5f5f5) | #000, #1a1a1a, #333, #525252 | #888, #aaa |
| Dark (#1a1a1a) | #fff, #e5e5e5, #d4d4d4 | #737373, #666 |
| Blue (#2563eb) | #fff | #bfdbfe (light blue text on blue) |
| Red (#dc2626) | #fff | #000 passes but looks harsh |

### Tools

- **axe DevTools** (browser extension): automated scan
- **Chrome DevTools**: inspect element > color picker shows contrast ratio
- **WebAIM Contrast Checker**: webaim.org/resources/contrastchecker
- **Stark** (Figma plugin): design-time checking

---

## Touch Accessibility

- **Minimum target size:** 44x44px (WCAG) / 48x48dp (Material). The 24x24px WCAG 2.5.8 is the *absolute* minimum.
- **Spacing between targets:** at least 8px gap to prevent mis-taps.
- **Gesture alternatives:** every swipe, pinch, or multi-finger gesture needs a single-tap alternative (button).
- **One-handed reachability:** primary actions in the bottom 2/3 of mobile screens. Avoid critical actions in top corners.

```css
/* Minimum touch target */
.touch-target {
  min-width: 44px;
  min-height: 44px;
  display: inline-flex;
  align-items: center;
  justify-content: center;
}
```

---

## Content Accessibility

### Alt text patterns

| Image type | Alt text | Example |
|---|---|---|
| Informative | Describe the content/function | `alt="Bar chart showing Q3 revenue of $2.4M"` |
| Decorative | Empty alt | `alt=""` (NOT omitting alt entirely) |
| Functional (link/button) | Describe the action | `alt="Search"` on a magnifying glass icon |
| Complex (chart/diagram) | Short alt + long description nearby | `alt="Sales trend" + <details>` with full data |

### Heading hierarchy

- One `<h1>` per page (the page title).
- Never skip levels: h1 > h2 > h3, NOT h1 > h3.
- Headings should describe the section content, not be used for styling.

### Link text

| Bad | Good |
|---|---|
| "Click here" | "View your order history" |
| "Read more" | "Read more about accessibility testing" |
| "Link" | "WCAG 2.2 guidelines (opens in new tab)" |
| URL as text | Descriptive label with URL as href |

### Error messages

```html
<div class="space-y-1">
  <label for="email" class="block text-sm font-medium">Email</label>
  <input id="email" type="email" aria-invalid="true" aria-describedby="email-error"
         class="border-red-500 ..." />
  <p id="email-error" role="alert" class="text-sm text-red-600">
    Enter a valid email address, e.g. name@example.com
  </p>
</div>
```

### Language attributes

```html
<html lang="en">
<!-- For mixed-language content -->
<p>The French word <span lang="fr">bonjour</span> means hello.</p>
```

---

## Testing Checklist (5-minute audit)

### Automated (2 min)
1. Run axe DevTools or Lighthouse accessibility audit
2. Fix any critical/serious issues flagged

### Keyboard (1 min)
3. Tab through the entire page: can you reach and operate every control?
4. Is focus always visible? Does it follow a logical order?
5. Can you escape every modal/overlay with the Escape key?

### Visual (1 min)
6. Zoom to 200%: does content reflow without horizontal scrolling?
7. Check contrast on key text elements (use DevTools picker)
8. Disable images: does alt text convey the meaning?

### Screen reader (1 min)
9. Turn on VoiceOver/NVDA. Navigate by headings: does the structure make sense?
10. Tab to a form: are labels read correctly? Are errors announced?
