# Design Psychology Principles Reference

Actionable laws and thresholds for frontend design. Every principle includes the rule, the number, and how to implement it.

---

## 1. Gestalt Principles Applied to UI

**Principle:** The brain groups visual elements based on proximity, similarity, closure, continuity, figure-ground, and common region, not by reading labels.

### Proximity
**Rule:** Elements spaced closer together are perceived as a group. Inner spacing must be noticeably less than outer spacing (at minimum 2:1 ratio).

**Thresholds:**
- Related items: 4-8px gap (within a group)
- Unrelated groups: 16-32px gap (between groups)
- Section separation: 48-96px
- Ratio: space between groups should be at least 2x the space within groups

**CSS/Tailwind:**
```css
/* Related items within a group */
.form-group > * + * { margin-top: 4px; }     /* gap-1 */

/* Between groups */
.form-section > * + * { margin-top: 24px; }  /* gap-6 */

/* Tailwind: related fields tight, groups spaced */
<div class="space-y-1">  <!-- within group: 4px -->
  <label>Email</label>
  <input />
  <p class="text-sm text-gray-500">Helper text</p>
</div>
<!-- gap between groups -->
<div class="mt-6">  <!-- between groups: 24px -->
  <label>Password</label>
  ...
</div>
```

**Common violation:** Equal spacing everywhere (16px between label and input AND between field groups). Users cannot distinguish which label belongs to which field.

### Similarity
**Rule:** Elements that share visual properties (color, shape, size, weight) are perceived as related. Use consistent styling for same-function elements.

**Thresholds:**
- All clickable elements should share at least 2 visual properties (color + shape, or color + weight)
- All status indicators should use the same shape (dots, badges) with color as the differentiator
- Deviate on exactly ONE property to signal a different function

**CSS/Tailwind:**
```css
/* All navigation links share properties */
.nav-link {
  font-size: 14px;
  font-weight: 500;
  color: var(--color-text-secondary);
  padding: 8px 12px;
  border-radius: 6px;
}

/* Active state deviates on ONE property: color */
.nav-link--active {
  color: var(--color-text-primary);
  background: var(--color-surface-raised);
}
```

**Common violation:** CTA buttons and destructive buttons with the same shape, size, and weight but different colors, while text links look completely different from buttons that do the same thing.

### Closure
**Rule:** The brain completes incomplete shapes. Use partial visibility to signal "more content exists" (carousel peek, truncated lists).

**Thresholds:**
- Show 10-30% of the next item to signal scrollability
- Minimum peek: 40-60px of the next card/item visible
- Fade gradients: 40-80px width

**CSS/Tailwind:**
```css
/* Carousel peek: show partial next item */
.carousel {
  overflow-x: auto;
  scroll-snap-type: x mandatory;
  padding-right: 48px; /* space for peek */
}

.carousel-item {
  scroll-snap-align: start;
  min-width: calc(100% - 60px); /* 60px peek of next */
}

/* Fade edge to hint at more content */
.scroll-fade::after {
  content: '';
  position: absolute;
  right: 0;
  top: 0;
  bottom: 0;
  width: 60px;
  background: linear-gradient(to right, transparent, var(--color-surface));
  pointer-events: none;
}
```

**Common violation:** Hard cut-off at the container edge with no visual hint that more items exist. Users don't know to scroll.

### Continuity
**Rule:** Eyes follow lines, curves, and aligned edges. Align elements along a shared axis or path.

**Thresholds:**
- Maximum 2-3 alignment axes per section
- Misaligned elements by even 1-2px feel "off" (use grid/flexbox, never manual positioning)
- Vertical rhythm: all text baselines should fall on a 4px grid

**CSS/Tailwind:**
```css
/* Strict grid alignment */
.content-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
  gap: 24px;
  align-items: start; /* shared top edge */
}

/* Baseline alignment in flex rows */
.stat-row {
  display: flex;
  align-items: baseline; /* numbers and labels share baseline */
  gap: 16px;
}
```

**Common violation:** Dashboard cards with different internal padding, causing content edges to misalign across columns.

### Figure-Ground
**Rule:** Users must instantly distinguish interactive foreground from passive background. Create clear layering.

**Thresholds:**
- Overlay backdrop: rgba(0,0,0,0.4) to rgba(0,0,0,0.6) (40-60% opacity)
- Modal/dropdown must be at least 2 elevation levels above the trigger
- Background desaturation: reduce to 30-50% opacity or apply blur (8-16px)

**CSS/Tailwind:**
```css
/* Modal overlay: clear figure-ground separation */
.overlay {
  background: rgba(0, 0, 0, 0.5);
  backdrop-filter: blur(4px);
}

.modal {
  background: var(--color-surface);
  box-shadow: var(--shadow-xl);
  border-radius: var(--radius-xl);
}

/* Tailwind */
<div class="fixed inset-0 bg-black/50 backdrop-blur-sm">
  <div class="bg-white shadow-xl rounded-2xl">...</div>
</div>
```

**Common violation:** Dropdown menus with no shadow or border over content, making it unclear what is foreground and what is background.

### Common Region
**Rule:** Elements within a shared boundary (border, background, container) are perceived as a group even without proximity.

**Thresholds:**
- Background shift: at least 2-3% lightness difference from surroundings
- Border: 1px at minimum, gray-200 or lighter
- Padding inside region: at least 12-16px
- Don't nest regions more than 2 levels deep (cards within cards within cards)

**CSS/Tailwind:**
```css
/* Group via background region */
.setting-group {
  background: var(--color-surface-raised);
  border-radius: var(--radius-lg);
  padding: 16px;
}

/* Group via border region */
.field-group {
  border: 1px solid var(--color-border);
  border-radius: var(--radius-md);
  padding: 16px;
}

/* Tailwind */
<div class="bg-gray-50 rounded-lg p-4 space-y-3">
  <!-- grouped items -->
</div>
```

**Common violation:** Putting every element in its own card. Over-containment destroys the grouping signal because everything is equally contained.

---

## 2. Fitts's Law

**Principle:** The time to reach a target is a function of the distance to and size of the target.

**Formula:**
```
MT = a + b * log2(2D / W)

MT = movement time (ms)
D  = distance from cursor/finger to target center
W  = width of target along the axis of movement
a, b = empirical constants (device-dependent)
```

**Key insight:** Doubling the target size reduces acquisition time by a constant amount. Halving the distance has the same effect. But size has diminishing returns past ~72px.

**Specific thresholds:**

| Context | Minimum Size | Recommended |
|---------|-------------|-------------|
| Web buttons (mouse) | 32x32px | 40-48px height |
| Touch targets (mobile) | 44x44px (iOS) / 48x48dp (Android) | 48x48px |
| WCAG 2.5.8 AA minimum | 24x24px | 44x44px |
| Spacing between targets | 8px minimum | 12px |
| Primary CTA | 48px height | 48-56px height, 160px+ width |

**CSS/React/Tailwind:**
```css
/* Minimum touch target with visual padding */
.button {
  min-height: 44px;
  min-width: 44px;
  padding: 12px 24px;
}

/* Icon button: small visual, large hit area */
.icon-button {
  width: 24px;        /* visual size */
  height: 24px;
  padding: 12px;      /* expands hit area to 48x48 */
  margin: -12px;      /* pulls padding back */
}

/* Invisible hit area expansion */
.small-target::before {
  content: '';
  position: absolute;
  inset: -8px;        /* expands by 8px in all directions */
}

/* Tailwind */
<button class="min-h-[44px] min-w-[44px] px-6 py-3">
  Primary Action
</button>

/* Icon button */
<button class="p-3 -m-3">  <!-- 48px hit area on 24px icon -->
  <Icon class="size-6" />
</button>
```

**Navigation placement rules (derived from Fitts's Law):**
- **Screen edges and corners are infinitely deep** (cursor stops at edge). Place frequent actions near edges on desktop.
- **Pie/radial menus** beat linear menus: equal distance to all options.
- **Primary CTAs** should be in the thumb zone on mobile (bottom 40% of screen).
- **Destructive actions** should be far from primary actions (distance = protection).
- **Dropdown items** closest to the trigger should be the most used options.

**Common violations:**
- Tiny "X" close buttons (12x12px) in the corner of modals.
- Action links styled as small text with no padding around them.
- Mobile FABs placed at the top of the screen, outside the thumb zone.

---

## 3. Hick's Law

**Principle:** Decision time increases logarithmically with the number of choices.

**Formula:**
```
RT = a + b * log2(n)

RT = reaction time (ms)
n  = number of equally probable choices
a  = base reaction time (~200ms)
b  = empirical constant (~150ms per bit of information)
```

**Example:** 2 choices = ~350ms. 4 choices = ~500ms. 8 choices = ~650ms. 16 choices = ~800ms.

**Specific thresholds:**

| Context | Maximum Options | Strategy |
|---------|----------------|----------|
| Primary navigation | 5-7 items | Group rest under "More" |
| Dropdown/select menus | 10-15 visible | Add search at 10+ items |
| Action buttons per screen | 1 primary + 2-3 secondary | One CTA per section |
| Form fields per step | 3-5 fields | Multi-step wizard at 6+ |
| Pricing tiers | 3 (occasionally 4) | Highlight recommended |
| Settings categories | 5-7 top-level groups | Nested hierarchy for detail |
| Dashboard widgets | 4-6 per view | Tabs or filters for more |

**CSS/React/Tailwind:**
```tsx
/* Progressive disclosure: show less, reveal on demand */

// BAD: 20 settings all visible
<SettingsPage>
  {allSettings.map(s => <Setting key={s.id} {...s} />)}
</SettingsPage>

// GOOD: grouped, collapsed, searchable
<SettingsPage>
  <SearchInput placeholder="Search settings..." />
  <SettingsGroup title="Account" defaultOpen>
    <Setting ... />  {/* 3-5 items */}
  </SettingsGroup>
  <SettingsGroup title="Notifications" defaultOpen={false}>
    <Setting ... />
  </SettingsGroup>
</SettingsPage>
```

```tsx
/* Multi-step form (reduces per-step choices) */
const steps = [
  { title: 'Account', fields: ['email', 'password'] },        // 2 choices
  { title: 'Profile', fields: ['name', 'avatar'] },           // 2 choices
  { title: 'Preferences', fields: ['language', 'timezone'] }, // 2 choices
];
// Instead of 6 fields on one screen = slower decisions
```

**Important nuance:** Hick's Law applies to DECISIONS, not recognition. A navigation bar with 10 items doesn't require holding them in memory (they're visible). The cost is in scanning and deciding, which is still logarithmic but less severe for familiar layouts. The law is most impactful for unfamiliar choices (first-time users, complex forms).

**Common violations:**
- Settings pages with 30+ toggles on one screen.
- Form pages with 10+ fields and no grouping or progression.
- Action menus with 15+ items and no categorization.

---

## 4. Miller's Law

**Principle:** Working memory holds approximately 7 (plus or minus 2) chunks of information at a time.

**The rule:** 7 +/- 2 items in SHORT-TERM MEMORY, not on screen. Items visible on screen don't need to be memorized. The real application is chunking: group information into digestible units.

**Specific thresholds:**

| Context | Chunk Size | Why |
|---------|-----------|-----|
| Phone numbers | 3-4 digits per group | `(555) 867-5309` not `5558675309` |
| Credit card numbers | 4 digits per group | `4242 4242 4242 4242` |
| Navigation groups | 5-7 items per group | Before needing subgroups |
| Dashboard cards | 5-7 KPIs visible | Before needing tabs/scrolling |
| Steps in a wizard | 3-5 steps | User holds the sequence mentally |
| Breadcrumb depth | 3-4 levels | Beyond this, context is lost |
| Tags/filters shown | 5-7 visible | "Show more" for rest |

**CSS/React/Tailwind:**
```tsx
/* Chunking: break long lists into scannable groups */

// BAD: flat list of 20 items
<ul>
  {items.map(item => <li>{item}</li>)}
</ul>

// GOOD: chunked into groups of 5-7
<div class="space-y-8">
  <section>
    <h3 class="text-sm font-medium text-gray-500 uppercase tracking-wide mb-3">
      Recently Used
    </h3>
    <ul class="space-y-1">{recentItems.slice(0, 5).map(...)}</ul>
  </section>
  <section>
    <h3 class="text-sm ...">All Items</h3>
    <ul class="space-y-1">{allItems.map(...)}</ul>
  </section>
</div>
```

```css
/* Input chunking with CSS */
.phone-input {
  letter-spacing: 0.5em;   /* visual spacing for number groups */
}

/* Or use separate inputs per chunk */
.cc-fields {
  display: flex;
  gap: 8px;
}
.cc-fields input {
  width: 4ch;               /* exactly 4 digits wide */
  text-align: center;
}
```

**Common violations:**
- A single unbroken list of 20+ navigation items.
- Dashboard with 15 KPI cards and no grouping or tabs.
- Showing a 16-digit number without spacing.

---

## 5. Jakob's Law

**Principle:** Users spend most of their time on OTHER sites. They expect your site to work the same way as all the others they know.

**The rule:** Don't innovate on standard patterns. Users have pre-built mental models. Match them or face a learning cost.

**Specific conventions users expect:**

| Element | Expected Pattern | Deviation Cost |
|---------|-----------------|----------------|
| Logo | Top-left, links to home | Users get lost |
| Search | Top-right or top-center, magnifying glass icon | Users can't find search |
| Shopping cart | Top-right, bag/cart icon with count | Lost sales |
| Navigation | Top horizontal (desktop) or bottom tab bar (mobile) | Confused navigation |
| Login/signup | Top-right corner | Users scan there first |
| Footer | Contact, legal, sitemap links | Accessibility/legal risk |
| Back button | Top-left (mobile), browser back (web) | Trapped users |
| Form submit | Bottom-right of form | Users miss the button |
| Hamburger menu | Top-left or top-right (mobile only) | Desktop: low discoverability |
| Notifications | Bell icon, top-right area | Users miss updates |
| Dark mode toggle | Settings or header area | Users can't find it |
| "Powered by" / attribution | Bottom of page, small text | N/A |

**CSS/React/Tailwind:**
```tsx
/* Standard e-commerce header layout (Jakob's Law compliant) */
<header class="flex items-center justify-between px-6 py-4">
  {/* Logo: top-left, links home */}
  <a href="/" class="flex items-center gap-2">
    <Logo />
    <span class="font-semibold">Brand</span>
  </a>

  {/* Navigation: center */}
  <nav class="hidden md:flex items-center gap-6">
    <a href="/products">Products</a>
    <a href="/about">About</a>
  </nav>

  {/* Actions: top-right */}
  <div class="flex items-center gap-4">
    <SearchButton />       {/* magnifying glass */}
    <UserMenu />           {/* avatar or person icon */}
    <CartButton count={3} /> {/* bag icon with badge */}
  </div>
</header>
```

**When to deviate:** Only deviate from conventions when:
1. User testing proves the new pattern is superior
2. The deviation provides clear, measurable benefit
3. You provide a transition period (show old + new)

**Common violations:**
- Hiding navigation behind a hamburger menu on desktop.
- Placing the logo top-right or bottom.
- Custom scroll behavior that breaks browser back/forward.
- Non-standard form controls that look like content instead of inputs.

---

## 6. Doherty Threshold

**Principle:** Productivity soars when system response time is under 400ms. Above that, users mentally disengage.

**The thresholds:**

| Response Time | Perception | Required Action |
|--------------|------------|-----------------|
| 0-100ms | Instant | No feedback needed |
| 100-400ms | Slight delay | Show subtle state change (button press) |
| 400ms-1s | Noticeable | Show spinner or progress indicator |
| 1-5s | Slow | Show skeleton screen + progress bar |
| 5-10s | Very slow | Show progress percentage + estimated time |
| 10s+ | Unacceptable | Background processing + notification when done |

**CSS/React/Tailwind:**
```tsx
/* Optimistic UI: update immediately, sync in background */
function LikeButton({ postId }) {
  const [liked, setLiked] = useState(false);

  const handleClick = () => {
    setLiked(true);  // instant visual feedback (<100ms)
    api.likePost(postId).catch(() => {
      setLiked(false); // revert on failure
      toast.error("Couldn't save. Try again.");
    });
  };

  return <button onClick={handleClick} />;
}
```

```css
/* Skeleton screen (show within 400ms of loading) */
.skeleton {
  background: linear-gradient(
    90deg,
    var(--color-surface-raised) 25%,
    rgba(0,0,0,0.04) 50%,
    var(--color-surface-raised) 75%
  );
  background-size: 200% 100%;
  animation: skeleton-shimmer 1.5s ease-in-out infinite;
  border-radius: var(--radius-md);
}

@keyframes skeleton-shimmer {
  0% { background-position: 200% 0; }
  100% { background-position: -200% 0; }
}
```

```tsx
/* Tailwind skeleton */
<div class="animate-pulse space-y-4">
  <div class="h-4 bg-gray-200 rounded w-3/4"></div>
  <div class="h-4 bg-gray-200 rounded w-1/2"></div>
  <div class="h-32 bg-gray-200 rounded"></div>
</div>
```

```tsx
/* Progressive loading: skeleton -> content */
function DataCard({ data, isLoading }) {
  if (isLoading) {
    return (
      <div class="p-6 rounded-xl border animate-pulse">
        <div class="h-6 bg-gray-200 rounded w-1/3 mb-4" />
        <div class="h-10 bg-gray-200 rounded w-1/2 mb-2" />
        <div class="h-4 bg-gray-200 rounded w-2/3" />
      </div>
    );
  }

  return (
    <div class="p-6 rounded-xl border">
      <h3>{data.title}</h3>
      <p class="text-3xl font-bold">{data.value}</p>
      <p class="text-gray-500">{data.description}</p>
    </div>
  );
}
```

**Implementation rules:**
- Button press feedback: < 50ms (use CSS :active state, no JS delay)
- Navigation transitions: < 300ms
- Search results: < 400ms (use debounce 200-300ms on input, then fetch)
- Page loads: show skeleton within 200ms, never show blank white screen
- Long operations: show determinate progress bar (indeterminate only if you truly don't know)

**Common violations:**
- Blank white screen while data loads (no skeleton).
- Spinner that blocks the entire page instead of the loading section.
- No feedback on button click (user clicks again, triggers duplicate action).
- Full page reload for operations that could use optimistic updates.

---

## 7. Von Restorff Effect (Isolation Effect)

**Principle:** When multiple similar items are presented, the one that differs from the rest is most likely to be remembered.

**The rule:** Make exactly ONE element stand out per visual group. If everything stands out, nothing does.

**Specific techniques:**

| Technique | Application | CSS Property |
|-----------|-------------|-------------|
| Color isolation | CTA among neutral buttons | Different `background-color` |
| Size isolation | Featured pricing tier | `transform: scale(1.05)` + extra padding |
| Elevation isolation | Promoted card | Higher shadow + slight `translateY(-4px)` |
| Border isolation | Recommended plan | Colored border 2-3px |
| Badge/label | "Most Popular" tag | Positioned badge on highlighted item |
| Position break | CTA outside the grid pattern | Different grid placement |

**CSS/React/Tailwind:**
```tsx
/* Pricing table with Von Restorff highlight */
<div class="grid grid-cols-3 gap-6 items-start">
  {/* Standard tier */}
  <div class="p-6 rounded-xl border border-gray-200">
    <h3 class="text-lg font-semibold">Basic</h3>
    <p class="text-3xl font-bold mt-2">$9</p>
    <button class="mt-6 w-full py-3 rounded-lg border border-gray-300
                    text-gray-700 hover:bg-gray-50">
      Get Started
    </button>
  </div>

  {/* HIGHLIGHTED tier (Von Restorff: differs on 3+ properties) */}
  <div class="p-6 rounded-xl border-2 border-blue-500 bg-blue-50/30
              shadow-lg relative -mt-2 pb-8">
    <span class="absolute -top-3 left-1/2 -translate-x-1/2
                 bg-blue-500 text-white text-xs font-medium
                 px-3 py-1 rounded-full">
      Most Popular
    </span>
    <h3 class="text-lg font-semibold">Pro</h3>
    <p class="text-3xl font-bold mt-2">$29</p>
    <button class="mt-6 w-full py-3 rounded-lg bg-blue-500
                    text-white hover:bg-blue-600 shadow-sm">
      Get Started
    </button>
  </div>

  {/* Standard tier */}
  <div class="p-6 rounded-xl border border-gray-200">
    <h3 class="text-lg font-semibold">Enterprise</h3>
    <p class="text-3xl font-bold mt-2">$99</p>
    <button class="mt-6 w-full py-3 rounded-lg border border-gray-300
                    text-gray-700 hover:bg-gray-50">
      Contact Sales
    </button>
  </div>
</div>
```

```css
/* CTA isolation: one filled button among ghost buttons */
.action-bar button {
  padding: 8px 16px;
  border-radius: 8px;
  background: transparent;
  color: var(--color-text-secondary);
}

/* The ONE standout */
.action-bar button.primary {
  background: var(--color-accent);
  color: white;
  box-shadow: var(--shadow-sm);
}
```

**Ratio rule:** For the effect to work, the "standouts" must be outnumbered by the "consistents" by at least 3:1. One highlighted item among 3+ plain items. Never highlight 2 out of 3 pricing tiers.

**Common violations:**
- Two "featured" pricing tiers (dilutes the signal).
- Every button on the page is a filled primary button.
- Multiple competing accent colors on the same screen.
- Banner + badge + colored border + shadow all on the same element (over-differentiation looks broken, not special).

---

## 8. Peak-End Rule

**Principle:** People judge an experience based on how they felt at the most intense moment (the peak) and at the end, not the average of the entire experience.

**The rule:** Design two moments deliberately: the peak (your "wow" moment) and the end (the last thing users see/feel).

**Specific applications:**

| Touchpoint | Peak Moment | End Moment |
|-----------|-------------|------------|
| Onboarding | "Aha" when user sees first value | Completion celebration |
| Checkout | Smooth payment (no friction) | Confirmation + next steps |
| Form completion | Smart autofill saving time | Success message + what's next |
| Search | Instant, relevant results | Easy next action on results |
| Error recovery | Problem clearly explained | Successful resolution |
| App session | Core value delivered | Graceful close/save state |

**CSS/React/Tailwind:**
```tsx
/* Success celebration at the end of a flow */
function OrderConfirmation({ order }) {
  return (
    <div class="text-center py-16 px-6">
      {/* Peak: visual celebration */}
      <div class="mx-auto w-16 h-16 bg-green-100 rounded-full
                  flex items-center justify-center mb-6
                  animate-[scale-in_0.5s_ease-out]">
        <CheckIcon class="w-8 h-8 text-green-600" />
      </div>

      <h1 class="text-2xl font-bold mb-2">Order confirmed!</h1>
      <p class="text-gray-500 mb-8">
        Order #{order.id} will arrive by {order.eta}
      </p>

      {/* End: clear next steps (don't leave user stranded) */}
      <div class="space-y-3 max-w-xs mx-auto">
        <a href={`/orders/${order.id}`}
           class="block w-full py-3 bg-gray-900 text-white rounded-lg">
          Track Order
        </a>
        <a href="/"
           class="block w-full py-3 border border-gray-300 rounded-lg">
          Continue Shopping
        </a>
      </div>
    </div>
  );
}
```

```css
/* Scale-in animation for celebration moment */
@keyframes scale-in {
  0% { transform: scale(0); opacity: 0; }
  50% { transform: scale(1.2); }
  100% { transform: scale(1); opacity: 1; }
}
```

```tsx
/* Onboarding "aha" moment: show personalized result */
function OnboardingComplete({ userData }) {
  return (
    <div class="space-y-6">
      {/* Peak: personalized preview */}
      <div class="rounded-2xl overflow-hidden shadow-xl">
        <PersonalizedDashboardPreview data={userData} />
      </div>

      <h2 class="text-xl font-bold">
        Your workspace is ready, {userData.name}!
      </h2>

      {/* End: single clear action */}
      <button class="w-full py-4 bg-blue-600 text-white rounded-xl
                      text-lg font-medium">
        Go to Dashboard
      </button>
    </div>
  );
}
```

**Design rules:**
- Every flow needs a deliberate ending. Never drop users on a blank page after completing an action.
- Success screens should include: confirmation of what happened, a next action, and a way to go "home."
- Avoid ending on an error. If the last step fails, provide recovery AND a graceful fallback.
- Onboarding should deliver value within the first 2-3 screens (the "aha" peak).

**Common violations:**
- Checkout ending with a plain "Thank you" text and no next steps.
- Sign-up flow ending on a blank dashboard with no data and no guidance.
- Error message as the last thing a user sees before they leave.
- Multi-step wizard with an anticlimactic finish (same style as intermediate steps).

---

## 9. Zeigarnik Effect

**Principle:** People remember incomplete tasks better than completed ones. Unfinished activities create mental tension that drives completion.

**The rule:** Show progress to create positive tension. Users who see they're 60-80% done are strongly motivated to finish. Starting users at 0% has lower engagement than starting at 10-20% (the "endowed progress" effect).

**Specific thresholds:**

| Technique | Optimal Range | Research Basis |
|-----------|--------------|----------------|
| Progress bar start | 10-20% pre-filled | Endowed progress effect (Nunes & Dreze, 2006) |
| Profile completion | Show percentage prominently | LinkedIn effect: 2x completion rate |
| Step indicators | Show total steps + current | "Step 2 of 4" outperforms just "Step 2" |
| Streak counters | Show consecutive completions | Duolingo: streak anxiety drives retention |
| Task lists | Show checked vs unchecked | Visible progress = 1.5-2x completion rate |

**CSS/React/Tailwind:**
```tsx
/* Profile completion bar (Zeigarnik + endowed progress) */
function ProfileCompletion({ steps }) {
  const completed = steps.filter(s => s.done).length;
  const total = steps.length;
  const percent = Math.round((completed / total) * 100);

  return (
    <div class="p-4 rounded-xl border border-gray-200">
      <div class="flex items-center justify-between mb-2">
        <span class="text-sm font-medium">Profile completion</span>
        <span class="text-sm font-bold text-blue-600">{percent}%</span>
      </div>

      {/* Progress bar */}
      <div class="h-2 bg-gray-100 rounded-full overflow-hidden">
        <div
          class="h-full bg-blue-500 rounded-full transition-all duration-500"
          style={{ width: `${percent}%` }}
        />
      </div>

      {/* Incomplete tasks (Zeigarnik: these nag at the user) */}
      <ul class="mt-3 space-y-2">
        {steps.filter(s => !s.done).map(step => (
          <li class="flex items-center gap-2 text-sm text-gray-600">
            <div class="w-4 h-4 rounded-full border-2 border-gray-300" />
            {step.label}
          </li>
        ))}
      </ul>
    </div>
  );
}
```

```tsx
/* Multi-step form with visible progress */
function StepIndicator({ current, total }) {
  return (
    <div class="flex items-center gap-2">
      {Array.from({ length: total }, (_, i) => (
        <div class={`h-1.5 flex-1 rounded-full transition-colors ${
          i < current ? 'bg-blue-500' :
          i === current ? 'bg-blue-300' :
          'bg-gray-200'
        }`} />
      ))}
    </div>
  );
}

{/* Usage */}
<div class="space-y-6">
  <div class="flex items-center justify-between">
    <h2>Create your account</h2>
    <span class="text-sm text-gray-500">Step {current + 1} of {total}</span>
  </div>
  <StepIndicator current={1} total={4} />
  {/* form fields */}
</div>
```

**Ethical boundaries:**
- Don't fake progress (showing 73% when the user has done nothing meaningful).
- Don't use the effect to guilt users into providing optional data ("Your profile is incomplete!").
- Show clear benefit for each step, not just the percentage.

**Common violations:**
- Multi-step form with no progress indicator (user doesn't know how much is left).
- Profile page that demands 100% completion for features that work fine without it.
- Progress bar that resets or jumps backward (breaks the tension contract).

---

## 10. Serial Position Effect

**Principle:** Items at the beginning (primacy) and end (recency) of a list are remembered best. Items in the middle are most forgotten.

**The rule:** Place the most important items first and last. Hide the least important items in the middle.

**Specific applications:**

| Context | First Position | Last Position | Middle |
|---------|---------------|---------------|--------|
| Bottom nav bar | Home (most used) | Profile/Settings | Secondary features |
| Feature lists | Strongest differentiator | Social proof/CTA | Supporting features |
| Pricing features | Most valued features | Unique selling point | Standard features |
| Navigation menu | Most visited page | Key CTA (Contact/Signup) | Alphabetical or by importance |
| Onboarding slides | Value proposition | CTA to start | Feature details |
| Dropdown options | Most common choice | "Other" or "Custom" | Alphabetical or by frequency |

**CSS/React/Tailwind:**
```tsx
/* Bottom navigation: key items at edges */
<nav class="fixed bottom-0 inset-x-0 flex items-center justify-around
            bg-white border-t py-2 pb-safe">
  {/* FIRST: Home (primacy - most remembered) */}
  <NavItem icon={<HomeIcon />} label="Home" active />

  {/* MIDDLE: Less critical (forgotten zone) */}
  <NavItem icon={<SearchIcon />} label="Search" />
  <NavItem icon={<HeartIcon />} label="Saved" />
  <NavItem icon={<InboxIcon />} label="Messages" />

  {/* LAST: Profile (recency - second most remembered) */}
  <NavItem icon={<UserIcon />} label="Profile" />
</nav>
```

```tsx
/* Feature comparison list: best features at start and end */
<ul class="space-y-3">
  {/* FIRST: strongest selling point */}
  <li class="flex items-center gap-3 font-medium">
    <CheckCircle class="text-green-500" /> Unlimited projects
  </li>

  {/* MIDDLE: supporting features (less memorable) */}
  <li class="flex items-center gap-3 text-gray-600">
    <CheckCircle class="text-green-500" /> Email support
  </li>
  <li class="flex items-center gap-3 text-gray-600">
    <CheckCircle class="text-green-500" /> API access
  </li>
  <li class="flex items-center gap-3 text-gray-600">
    <CheckCircle class="text-green-500" /> Custom domains
  </li>

  {/* LAST: clincher / unique value */}
  <li class="flex items-center gap-3 font-medium">
    <CheckCircle class="text-green-500" /> Priority 24/7 support
  </li>
</ul>
```

**Design enhancement for middle items:**
- If middle items are still important, use visual differentiation (icons, color, grouping) to counteract the serial position effect.
- Group middle items under sub-headers to create new "first" positions within each group.

**Common violations:**
- Alphabetically sorted navigation with "About" first and "Pricing" in the middle.
- Most important feature buried in the middle of a long feature list.
- CTA button placed in the middle of a page section instead of at the start or end.

---

## 11. Cognitive Load Theory

**Principle:** Working memory has limited capacity. UI should minimize extraneous load (irrelevant processing), manage intrinsic load (task complexity), and maximize germane load (meaningful learning).

**The three types and how to control them:**

| Load Type | Definition | Design Strategy |
|-----------|-----------|----------------|
| **Intrinsic** | Inherent complexity of the task | Break complex tasks into steps, provide smart defaults |
| **Extraneous** | Unnecessary processing from poor design | Remove clutter, simplify layout, use conventions |
| **Germane** | Useful processing that builds understanding | Clear mental models, good feedback, progressive disclosure |

**Progressive disclosure pattern (key technique):**

```
Level 1: Show only what's needed NOW (3-5 items)
Level 2: Reveal next tier on interaction (click/expand)
Level 3: Deep detail available on demand (link/modal)
```

**Specific thresholds:**

| Metric | Threshold | Action |
|--------|-----------|--------|
| Visible actions per screen | Max 5-7 | Group or hide the rest |
| Decisions before primary task | 0-1 | Don't ask "preferences" before core action |
| New concepts introduced at once | 1-2 | Teach one thing at a time |
| Competing visual weights | Max 3 levels | Primary, secondary, tertiary hierarchy |
| Form fields without grouping | Max 3-4 | Add section headers at 5+ |
| Simultaneous notifications | Max 1 | Queue, don't stack |

**CSS/React/Tailwind:**
```tsx
/* Progressive disclosure: details on demand */
function SettingRow({ title, description, children }) {
  const [expanded, setExpanded] = useState(false);

  return (
    <div class="border-b border-gray-100 py-4">
      {/* Level 1: just the essentials */}
      <button
        onClick={() => setExpanded(!expanded)}
        class="flex items-center justify-between w-full text-left"
      >
        <div>
          <h4 class="font-medium">{title}</h4>
          <p class="text-sm text-gray-500">{description}</p>
        </div>
        <ChevronIcon class={`transition-transform ${
          expanded ? 'rotate-180' : ''
        }`} />
      </button>

      {/* Level 2: details on demand */}
      {expanded && (
        <div class="mt-4 pl-0 space-y-3">
          {children}
        </div>
      )}
    </div>
  );
}
```

```tsx
/* Smart defaults to reduce decisions */
<Select defaultValue="usd">
  <option value="usd">USD - US Dollar</option>  {/* pre-selected */}
  <option value="eur">EUR - Euro</option>
  <option value="gbp">GBP - British Pound</option>
</Select>

/* Conditional fields: only show when relevant */
{paymentMethod === 'card' && (
  <div class="space-y-4 animate-in fade-in">
    <input placeholder="Card number" />
    <div class="grid grid-cols-2 gap-4">
      <input placeholder="MM/YY" />
      <input placeholder="CVC" />
    </div>
  </div>
)}
```

**Cognitive load reduction checklist:**
- [ ] Can any fields be removed? (do you really need "Middle Name"?)
- [ ] Can any fields be pre-filled? (geolocation for country, browser for timezone)
- [ ] Can any choices be deferred? (don't ask for preferences during signup)
- [ ] Is there visual clutter that doesn't help the task? (decorative elements, redundant labels)
- [ ] Does the user need to remember anything from a previous screen? (if yes, show it again)

**Common violations:**
- Requiring users to fill out a preferences survey before seeing the product.
- Showing advanced options alongside basic options without progressive disclosure.
- Dashboard with 20 metrics, no grouping, all the same visual weight.

---

## 12. F-Pattern and Z-Pattern Scanning

**Principle:** Eyes scan pages in predictable patterns. F-pattern for text-heavy pages. Z-pattern for visual/minimal pages.

### F-Pattern

**When to use:** Text-heavy pages: blogs, news, documentation, search results, email inboxes.

**The pattern:**
```
[████████████████████]  ← first horizontal scan (full width)
[██████████          ]  ← second horizontal scan (shorter)
[██                  ]  ← vertical scan down left edge
[██                  ]
[█                   ]
```

**Design rules:**
- Put the most important content in the first 2 lines
- Front-load paragraphs (key info first, details after)
- Left-align text (users scan the left edge)
- Use headings, bold text, and bullet points to create "scan stops" on the left
- First 2 words of every heading matter most

**CSS/Tailwind:**
```tsx
/* F-pattern optimized article layout */
<article class="max-w-prose mx-auto">
  {/* First horizontal scan: headline */}
  <h1 class="text-3xl font-bold mb-4">
    Most Important Words First in Headlines
  </h1>

  {/* Second horizontal scan: summary */}
  <p class="text-lg text-gray-600 mb-8 font-medium">
    Key takeaway goes here. Users read this line fully.
  </p>

  {/* Left edge scan: use visual anchors */}
  <h2 class="text-xl font-semibold mt-8 mb-3">Section heading</h2>
  <ul class="space-y-2 list-disc list-inside">
    <li><strong>Key point first</strong>, then explanation</li>
    <li><strong>Another key point</strong>, then detail</li>
  </ul>
</article>
```

### Z-Pattern

**When to use:** Visual pages with minimal text: landing pages, hero sections, login screens, splash pages.

**The pattern:**
```
[1 ─────────────── 2]  ← top-left to top-right
[  \                 ]
[    \               ]  ← diagonal scan
[      \             ]
[3 ─────────────── 4]  ← bottom-left to bottom-right
```

**Design rules:**
- **Position 1 (top-left):** Logo/brand
- **Position 2 (top-right):** CTA or navigation
- **Diagonal:** Hero image or visual element
- **Position 3 (bottom-left):** Supporting info or secondary headline
- **Position 4 (bottom-right):** Primary CTA button

**CSS/Tailwind:**
```tsx
/* Z-pattern landing page hero */
<section class="min-h-screen flex flex-col">
  {/* Top bar: logo (pos 1) to CTA (pos 2) */}
  <header class="flex items-center justify-between px-8 py-6">
    <Logo />                           {/* Position 1 */}
    <button class="bg-blue-600 text-white px-6 py-2 rounded-lg">
      Sign Up                          {/* Position 2 */}
    </button>
  </header>

  {/* Center diagonal: hero visual */}
  <div class="flex-1 flex items-center justify-center px-8">
    <div class="max-w-5xl w-full grid md:grid-cols-2 gap-12 items-center">
      <div>                            {/* Position 3 area */}
        <h1 class="text-5xl font-bold mb-6">
          Headline with value proposition
        </h1>
        <p class="text-xl text-gray-600 mb-8">
          Supporting description text
        </p>
        <button class="bg-blue-600 text-white px-8 py-4 rounded-xl
                        text-lg font-medium">
          Get Started Free             {/* Position 4: primary CTA */}
        </button>
      </div>
      <div>
        <img src="/hero.png" class="rounded-2xl shadow-xl" />
      </div>
    </div>
  </div>
</section>
```

**Common violations (both patterns):**
- Key information placed in the bottom-center (dead zone for both patterns).
- CTA buried in the middle of a text-heavy page.
- Right-aligned body text on a text-heavy page (fights F-pattern).
- Landing page with no clear Z-flow (elements scattered without directional intention).

---

## 13. Weber's Law

**Principle:** The just-noticeable difference (JND) between two stimuli is proportional to the magnitude of the original stimulus. Bigger things need bigger changes to be noticed.

**Formula:**
```
ΔS / S = k (constant)

ΔS = change in stimulus (size, brightness, spacing)
S  = original stimulus value
k  = Weber fraction (depends on modality)
```

**Specific Weber fractions for UI-relevant modalities:**

| Modality | Weber Fraction (k) | Meaning |
|----------|-------------------|---------|
| Line length | ~0.03 (3%) | A 100px element needs 3px+ change to notice |
| Brightness/luminance | ~0.08 (8%) | Color needs 8% lightness shift to look different |
| Weight/heaviness | ~0.02-0.05 (2-5%) | Font weight changes need ~5% difference |
| Sound loudness | ~0.05 (5%) | Volume change of 5% to notice |
| Size (area) | ~0.04 (4%) | A 200px box needs 8px+ size change |
| Temporal (duration) | ~0.1 (10%) | A 1s animation needs 100ms+ change |

**UI design implications:**

| Current Value | Minimum Noticeable Change | Example |
|--------------|--------------------------|---------|
| 12px font | +1px (to 13px) | Barely noticeable |
| 16px font | +1px minimum, +2px comfortable | 16 to 18 feels like a step |
| 48px heading | +3px minimum, +4px comfortable | 48 to 52 is a noticeable step |
| 100px spacing | +5px minimum | Users notice 100 to 105 |
| 400px container | +16px minimum | 400 to 416 is perceptible |
| 8px padding | +1px barely, +2px clearly | This is why 4px spacing scales work |

**CSS/Tailwind (applying Weber's Law to spacing scales):**
```css
/* Why the 4/8/12/16/24/32/48/64 scale works:
   Each step is a 25-50% increase, well above the ~3-4% JND.
   This ensures every step on the scale is VISIBLY different. */

/* BAD: increments too small to notice */
.spacing-bad {
  /* 10px, 11px, 12px — users can't distinguish these */
}

/* GOOD: each step clearly different */
.spacing-good {
  /* 8px → 12px (50% increase) → 16px (33%) → 24px (50%) → 32px (33%) */
}
```

```css
/* Color system: ensure adjacent shades are distinguishable */
/* Each step should shift lightness by at least 8-10% */
:root {
  --gray-50:  oklch(98% 0.01 250);   /* ↓ ~8% lightness per step */
  --gray-100: oklch(92% 0.01 250);
  --gray-200: oklch(85% 0.01 250);
  --gray-300: oklch(77% 0.02 250);
  --gray-400: oklch(65% 0.02 250);
  --gray-500: oklch(55% 0.02 250);
  --gray-600: oklch(45% 0.02 250);
  --gray-700: oklch(35% 0.02 250);
  --gray-800: oklch(25% 0.02 250);
  --gray-900: oklch(15% 0.02 250);
}
```

**Application to redesigns (why gradual changes work):**
- Changing a price from $10 to $10.50 (5% increase) is near the JND and may be ignored.
- Changing a price from $10 to $12 (20% increase) is well above JND and will be noticed.
- This is why SaaS companies make small iterative price changes (under the JND) rather than dramatic ones.
- Similarly, redesigns should change elements by less than 20% at a time to avoid "everything changed" shock.

**Common violations:**
- Spacing scale with 1px increments (10, 11, 12, 13) that are perceptually identical.
- Color palette with adjacent shades that look the same on screen (less than 5% lightness difference).
- "Before/after" redesign where the change is so subtle nobody notices the improvement.

---

## 14. Aesthetic-Usability Effect

**Principle:** Users perceive aesthetically pleasing interfaces as more usable, even when they objectively aren't. Beautiful design creates a halo effect that increases tolerance for minor friction.

**The research (1995, Kurosu & Kashimura, Hitachi):** 252 participants rated ATM interfaces. The correlation between aesthetic appeal and PERCEIVED ease of use was significantly stronger than the correlation between aesthetics and ACTUAL ease of use. Users rated pretty interfaces as more usable even when task performance was the same.

**The thresholds and rules:**

| Finding | Implication |
|---------|------------|
| Users tolerate 1-2 minor usability issues in beautiful interfaces | Polish buys you forgiveness for small friction |
| Users abandon ugly interfaces 2x faster on errors | First impressions matter disproportionately |
| Attractive interfaces increase perceived credibility | E-commerce: design quality = trust signal |
| The effect has limits: major usability failures aren't forgiven | Aesthetics can't fix broken flows |
| Beautiful + functional > beautiful + broken > ugly + functional | Prioritize: function first, then polish |

**Implementation priority:**

```
1. Fix all major usability issues first (the effect won't save you)
2. Then invest in visual polish for maximum ROI:
   a. Typography: consistent scale, good line height, proper tracking
   b. Spacing: consistent scale, generous whitespace
   c. Color: cohesive palette, proper contrast, saturated grays
   d. Depth: subtle shadows, not flat and not overdone
   e. Micro-interactions: hover states, transitions, feedback
   f. Border radius: consistent, rounded-but-not-bubbly
```

**CSS/Tailwind (the "polish pass" that activates the aesthetic-usability effect):**
```css
/* BEFORE: functional but ugly */
.card-before {
  border: 1px solid #ccc;
  padding: 16px;
  margin: 8px;
  background: #fff;
}

/* AFTER: same function, but polished */
.card-after {
  border: 1px solid rgba(0, 0, 0, 0.06);
  padding: 24px;
  margin: 8px;
  background: #fafaf9;
  border-radius: 12px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.04), 0 1px 2px rgba(0, 0, 0, 0.02);
  transition: box-shadow 0.2s cubic-bezier(0.25, 1, 0.5, 1),
              transform 0.2s cubic-bezier(0.25, 1, 0.5, 1);
}

.card-after:hover {
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08), 0 2px 4px rgba(0, 0, 0, 0.04);
  transform: translateY(-1px);
}
```

```tsx
/* Tailwind polish comparison */

{/* Before: functional */}
<div class="border p-4 m-2 bg-white">
  <h3 class="text-lg font-bold">Title</h3>
  <p>Description</p>
  <button class="bg-blue-500 text-white px-4 py-2 mt-4">Action</button>
</div>

{/* After: polished (same structure, better aesthetics) */}
<div class="border border-gray-100 p-6 bg-white rounded-xl shadow-sm
            hover:shadow-md transition-all duration-200">
  <h3 class="text-lg font-semibold tracking-tight">Title</h3>
  <p class="text-gray-500 mt-1 leading-relaxed">Description</p>
  <button class="bg-gray-900 text-white px-5 py-2.5 rounded-lg mt-5
                  text-sm font-medium hover:bg-gray-800
                  transition-colors duration-150">
    Action
  </button>
</div>
```

**The aesthetic checklist (minimum polish pass):**
- [ ] No pure #000000 or #ffffff (use near-black and near-white)
- [ ] Grays have subtle saturation (blue-tinted or warm-tinted)
- [ ] Consistent border-radius on all elements
- [ ] At least one subtle shadow level on elevated elements
- [ ] Hover/active states on all interactive elements
- [ ] Transitions on state changes (150-250ms, custom easing)
- [ ] Consistent spacing scale (no arbitrary pixel values)
- [ ] Typography scale with at least 3:1 contrast between heading and body sizes
- [ ] Adequate whitespace (when in doubt, add more)

**Common violations:**
- Investing weeks in animation while basic typography and spacing are inconsistent.
- Using "we'll polish it later" as an excuse for ugly prototypes that ship to users.
- Over-polishing low-traffic admin screens while the public-facing checkout looks default.
- Confusing "minimal" with "unfinished" (minimal is intentionally restrained, unfinished is just incomplete).

---

## Quick Reference Matrix

| Law | Core Number | Primary Application |
|-----|-------------|-------------------|
| Gestalt Proximity | 2:1 inner:outer spacing ratio | Grouping related elements |
| Fitts's Law | 44px minimum touch, 48dp Android | Button/target sizing |
| Hick's Law | RT = a + b*log2(n); max 5-7 options | Menu/form simplification |
| Miller's Law | 7 +/- 2 chunks in working memory | Chunking content |
| Jakob's Law | Match existing conventions | Layout and navigation patterns |
| Doherty Threshold | 400ms maximum response time | Loading states and feedback |
| Von Restorff | 1 standout per 3+ consistents | CTA and pricing highlighting |
| Peak-End Rule | Design peak + ending deliberately | Onboarding and checkout flows |
| Zeigarnik Effect | Start at 10-20% for endowed progress | Progress bars and completion |
| Serial Position | First and last positions remembered | Navigation and list ordering |
| Cognitive Load | Max 5-7 visible actions per screen | Progressive disclosure |
| F-Pattern | Text-heavy pages, left-edge scanning | Articles, search results |
| Z-Pattern | Visual pages, diagonal flow | Landing pages, hero sections |
| Weber's Law | 3-8% change needed to notice | Spacing/color scales |
| Aesthetic-Usability | Beautiful = perceived as more usable | Visual polish priority |

---

## Sources

- Fitts, P. M. (1954). The information capacity of the human motor system. Journal of Experimental Psychology.
- Hick, W. E. (1952). On the rate of gain of information. Quarterly Journal of Experimental Psychology.
- Miller, G. A. (1956). The magical number seven, plus or minus two. Psychological Review.
- Nielsen, J. (2000). Jakob's Law of Internet User Experience. Nielsen Norman Group.
- Doherty, W. J., & Thadani, A. J. (1982). The economic value of rapid response time. IBM Systems Journal.
- Von Restorff, H. (1933). Analyse von Vorgangen im Spurenfeld. Psychologische Forschung.
- Kahneman, D. (1999). Peak-End Rule. Objective happiness.
- Zeigarnik, B. (1927). On finished and unfinished tasks. Psychologische Forschung.
- Murdock, B. B. (1962). The serial position effect of free recall. Journal of Experimental Psychology.
- Sweller, J. (1988). Cognitive load during problem solving. Cognitive Science.
- Weber, E. H. (1834). De Pulsu, Resorptione, Auditu et Tactu.
- Kurosu, M., & Kashimura, K. (1995). Apparent usability vs. inherent usability. CHI '95.
- WCAG 2.2 (W3C). Success Criteria 2.5.5, 2.5.8. Target Size.
- Laws of UX (lawsofux.com). Jon Yablonski.
- Nielsen Norman Group (nngroup.com). F-Shaped Pattern, Gestalt Proximity.
