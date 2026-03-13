# Components & States Reference

## Component State Matrix

| Component | Default | Hover | Active | Focus | Disabled | Loading | Error | Selected | Empty |
|---|---|---|---|---|---|---|---|---|---|
| **Button** | Solid bg, text | Darker bg | Scale 0.98 | Ring 2px | 50% opacity, no pointer | Spinner + "Loading..." | Red bg (destructive) | N/A | N/A |
| **Input** | Border gray-300 | Border gray-400 | Border primary | Ring primary | Gray bg, muted text | N/A | Border red-500, red helper text | N/A | Placeholder shown |
| **Select** | Border gray-300, chevron | Border gray-400 | Dropdown open | Ring primary | Gray bg, no chevron interaction | Spinner in chevron slot | Border red-500 | Option highlighted | "Select an option" |
| **Checkbox** | Empty box, border | Border darker | Check animates in | Ring on box | Muted box, no pointer | N/A | Red border, error text below | Checkmark visible | N/A |
| **Radio** | Empty circle | Border darker | Dot animates in | Ring on circle | Muted, no pointer | N/A | Red border | Filled dot | N/A |
| **Toggle** | Track gray, knob left | Track slightly darker | Knob slides | Ring on track | Muted, no pointer | N/A | Red track | Track primary, knob right | N/A |
| **Card** | Shadow-sm, border | Shadow-md, slight lift | Scale 0.99 | Ring 2px | Muted content, no pointer | Skeleton pulse | Red left border or banner | Highlighted border/bg | Dashed border, CTA |
| **Modal** | Centered, backdrop | N/A | N/A | Trapped inside | N/A | Action button loading | Error banner inside | N/A | Empty state illustration |
| **Toast** | Slide in, colored left border | N/A | N/A | Close button focusable | N/A | Progress bar | Red accent | N/A | N/A |
| **Dropdown** | Hidden | Item bg gray-100 | Item bg gray-200 | Ring on trigger | Muted trigger | Spinner in menu | N/A | Item bg primary-50 | "No options" |
| **Tabs** | Underline none | Text darker | N/A | Ring on tab | Muted text | Skeleton in panel | Error in panel | Bold + underline primary | N/A |
| **Accordion** | Chevron right | Bg gray-50 | Chevron rotates | Ring on header | Muted, no expand | Skeleton in panel | Red header accent | Panel visible, chevron down | "No content" |

---

## Button Patterns

### Variants

```html
<!-- Primary -->
<button class="bg-blue-600 text-white hover:bg-blue-700 active:scale-[0.98] focus-visible:ring-2 focus-visible:ring-blue-600 focus-visible:ring-offset-2 rounded-lg font-medium transition-all">
  Save changes
</button>

<!-- Secondary -->
<button class="bg-white text-gray-900 border border-gray-300 hover:bg-gray-50 active:scale-[0.98] focus-visible:ring-2 focus-visible:ring-gray-400 focus-visible:ring-offset-2 rounded-lg font-medium transition-all">
  Cancel
</button>

<!-- Tertiary / Ghost -->
<button class="text-blue-600 hover:bg-blue-50 active:bg-blue-100 focus-visible:ring-2 focus-visible:ring-blue-600 rounded-lg font-medium transition-all">
  Learn more
</button>

<!-- Destructive -->
<button class="bg-red-600 text-white hover:bg-red-700 active:scale-[0.98] focus-visible:ring-2 focus-visible:ring-red-600 focus-visible:ring-offset-2 rounded-lg font-medium transition-all">
  Delete account
</button>
```

### Sizes

```html
<!-- sm: 32px height -->
<button class="px-3 py-1.5 text-sm rounded-md ...">Small</button>

<!-- md: 40px height (default) -->
<button class="px-4 py-2 text-sm rounded-lg ...">Medium</button>

<!-- lg: 48px height -->
<button class="px-6 py-3 text-base rounded-lg ...">Large</button>
```

### Icon button

```html
<button class="p-2 rounded-lg hover:bg-gray-100 focus-visible:ring-2" aria-label="Close menu">
  <svg class="w-5 h-5" ...><!-- icon --></svg>
</button>
```

### Loading button

```html
<button class="inline-flex items-center gap-2 px-4 py-2 bg-blue-600 text-white rounded-lg opacity-80 cursor-not-allowed" disabled>
  <svg class="w-4 h-4 animate-spin" viewBox="0 0 24 24" fill="none">
    <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"/>
    <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8v4a4 4 0 00-4 4H4z"/>
  </svg>
  Saving...
</button>
```

### Button group

```html
<div class="inline-flex rounded-lg border border-gray-300 divide-x divide-gray-300" role="group">
  <button class="px-4 py-2 text-sm hover:bg-gray-50 rounded-l-lg">Left</button>
  <button class="px-4 py-2 text-sm hover:bg-gray-50 bg-gray-100 font-medium">Center</button>
  <button class="px-4 py-2 text-sm hover:bg-gray-50 rounded-r-lg">Right</button>
</div>
```

---

## Input Patterns

### Text input with label, helper, and error

```html
<!-- Default state -->
<div class="space-y-1.5">
  <label for="name" class="block text-sm font-medium text-gray-700">Full name</label>
  <input id="name" type="text"
    class="w-full px-3 py-2 border border-gray-300 rounded-lg text-sm
           hover:border-gray-400
           focus:outline-none focus:ring-2 focus:ring-blue-600 focus:border-transparent
           placeholder:text-gray-400"
    placeholder="John Doe" />
  <p class="text-xs text-gray-500">As it appears on your ID</p>
</div>

<!-- Error state -->
<div class="space-y-1.5">
  <label for="email" class="block text-sm font-medium text-gray-700">Email</label>
  <input id="email" type="email" aria-invalid="true" aria-describedby="email-err"
    class="w-full px-3 py-2 border-2 border-red-500 rounded-lg text-sm
           focus:outline-none focus:ring-2 focus:ring-red-500 focus:border-transparent" />
  <p id="email-err" class="text-xs text-red-600">Please enter a valid email address</p>
</div>

<!-- Disabled state -->
<input disabled class="w-full px-3 py-2 border border-gray-200 rounded-lg text-sm bg-gray-50 text-gray-400 cursor-not-allowed" />
```

### Password with toggle

```html
<div class="relative">
  <input id="password" type="password"
    class="w-full px-3 py-2 pr-10 border border-gray-300 rounded-lg text-sm focus:ring-2 focus:ring-blue-600" />
  <button type="button" onclick="togglePassword()" aria-label="Toggle password visibility"
    class="absolute right-3 top-1/2 -translate-y-1/2 text-gray-400 hover:text-gray-600">
    <svg class="w-4 h-4"><!-- eye icon --></svg>
  </button>
</div>
```

### Search input

```html
<div class="relative">
  <svg class="absolute left-3 top-1/2 -translate-y-1/2 w-4 h-4 text-gray-400"><!-- search icon --></svg>
  <input type="search" placeholder="Search..."
    class="w-full pl-9 pr-3 py-2 border border-gray-300 rounded-lg text-sm focus:ring-2 focus:ring-blue-600" />
</div>
```

### Textarea with character count

```html
<div class="space-y-1.5">
  <label for="bio" class="block text-sm font-medium text-gray-700">Bio</label>
  <textarea id="bio" rows="4" maxlength="280"
    class="w-full px-3 py-2 border border-gray-300 rounded-lg text-sm resize-none focus:ring-2 focus:ring-blue-600"></textarea>
  <p class="text-xs text-gray-500 text-right"><span id="count">0</span>/280</p>
</div>
```

---

## Card Patterns

### Basic card

```html
<div class="bg-white border border-gray-200 rounded-xl p-6 shadow-sm">
  <h3 class="text-lg font-semibold text-gray-900">Card Title</h3>
  <p class="mt-2 text-sm text-gray-600">Card description goes here with supporting details.</p>
  <div class="mt-4 flex gap-3">
    <button class="px-4 py-2 bg-blue-600 text-white text-sm rounded-lg">Action</button>
    <button class="px-4 py-2 text-sm text-gray-600 hover:text-gray-900">Cancel</button>
  </div>
</div>
```

### Image card with aspect ratio

```html
<div class="bg-white border border-gray-200 rounded-xl overflow-hidden shadow-sm">
  <div class="aspect-video bg-gray-100">
    <img src="..." alt="Description" class="w-full h-full object-cover" />
  </div>
  <div class="p-5">
    <p class="text-xs text-gray-500 uppercase tracking-wide">Category</p>
    <h3 class="mt-1 text-lg font-semibold text-gray-900">Card Title</h3>
    <p class="mt-2 text-sm text-gray-600 line-clamp-2">Description text truncated to two lines maximum.</p>
  </div>
</div>
```

### Horizontal card

```html
<div class="flex bg-white border border-gray-200 rounded-xl overflow-hidden shadow-sm">
  <div class="w-48 shrink-0 bg-gray-100">
    <img src="..." alt="Description" class="w-full h-full object-cover" />
  </div>
  <div class="p-5 flex flex-col justify-center">
    <h3 class="text-lg font-semibold text-gray-900">Title</h3>
    <p class="mt-1 text-sm text-gray-600">Description</p>
  </div>
</div>
```

### Interactive card (hover lift)

```html
<a href="/details" class="block bg-white border border-gray-200 rounded-xl p-6 shadow-sm
  hover:shadow-md hover:-translate-y-0.5
  active:shadow-sm active:translate-y-0
  focus-visible:ring-2 focus-visible:ring-blue-600 focus-visible:ring-offset-2
  transition-all duration-200 cursor-pointer">
  <h3 class="font-semibold text-gray-900">Clickable Card</h3>
  <p class="mt-2 text-sm text-gray-600">Entire card is a link.</p>
</a>
```

### Skeleton card (loading)

```html
<div class="bg-white border border-gray-200 rounded-xl overflow-hidden shadow-sm animate-pulse">
  <div class="aspect-video bg-gray-200"></div>
  <div class="p-5 space-y-3">
    <div class="h-3 w-20 bg-gray-200 rounded"></div>
    <div class="h-5 w-3/4 bg-gray-200 rounded"></div>
    <div class="h-3 w-full bg-gray-200 rounded"></div>
    <div class="h-3 w-2/3 bg-gray-200 rounded"></div>
  </div>
</div>
```

---

## Modal / Dialog

```html
<!-- Backdrop -->
<div class="fixed inset-0 z-50 flex items-center justify-center">
  <div class="fixed inset-0 bg-black/50 backdrop-blur-sm" aria-hidden="true" onclick="closeModal()"></div>

  <!-- Dialog -->
  <div role="dialog" aria-modal="true" aria-labelledby="modal-title"
    class="relative z-10 bg-white rounded-2xl shadow-xl w-full max-w-md mx-4 p-6
           animate-in fade-in zoom-in-95 duration-200">
    <h2 id="modal-title" class="text-lg font-semibold text-gray-900">Modal Title</h2>
    <p class="mt-2 text-sm text-gray-600">Are you sure you want to proceed?</p>
    <div class="mt-6 flex justify-end gap-3">
      <button onclick="closeModal()" class="px-4 py-2 text-sm text-gray-700 hover:bg-gray-100 rounded-lg">Cancel</button>
      <button class="px-4 py-2 text-sm bg-blue-600 text-white rounded-lg hover:bg-blue-700">Confirm</button>
    </div>
    <button onclick="closeModal()" class="absolute top-4 right-4 text-gray-400 hover:text-gray-600" aria-label="Close">
      <svg class="w-5 h-5"><!-- X icon --></svg>
    </button>
  </div>
</div>
```

**Required behaviors:**
- Focus trap: tab cycles within modal (see accessibility.md for trap code)
- Escape key closes the modal
- Backdrop click closes the modal
- Scroll lock: `document.body.style.overflow = 'hidden'` on open
- Restore focus to trigger element on close
- Animation: scale from 95% + fade in (150-200ms ease-out)

---

## Toast / Notification

```html
<!-- Toast container: fixed position -->
<div class="fixed top-4 right-4 z-50 flex flex-col gap-3 w-80">
  <!-- Success toast -->
  <div role="status" class="flex items-start gap-3 bg-white border border-gray-200 rounded-xl p-4 shadow-lg
    animate-in slide-in-from-right duration-300">
    <div class="shrink-0 w-5 h-5 text-green-500"><!-- check-circle icon --></div>
    <div class="flex-1 min-w-0">
      <p class="text-sm font-medium text-gray-900">Changes saved</p>
      <p class="text-xs text-gray-500 mt-0.5">Your profile has been updated.</p>
    </div>
    <button class="shrink-0 text-gray-400 hover:text-gray-600" aria-label="Dismiss">
      <svg class="w-4 h-4"><!-- X icon --></svg>
    </button>
  </div>

  <!-- Error toast -->
  <div role="alert" class="flex items-start gap-3 bg-white border-l-4 border-red-500 rounded-xl p-4 shadow-lg">
    <div class="shrink-0 w-5 h-5 text-red-500"><!-- alert icon --></div>
    <div class="flex-1">
      <p class="text-sm font-medium text-gray-900">Upload failed</p>
      <p class="text-xs text-gray-500 mt-0.5">File exceeds 10MB limit.</p>
      <button class="mt-2 text-xs font-medium text-red-600 hover:text-red-700">Retry</button>
    </div>
  </div>
</div>
```

**Timing and behavior:**
- Auto-dismiss: 5s for success/info, persistent for errors
- Stacking: newest on top, max 3 visible, older ones fade out
- Position: top-right on desktop, bottom-center on mobile
- Types: success (green), error (red), warning (amber), info (blue)
- Use `role="status"` for info/success, `role="alert"` for errors

---

## Navigation

### Desktop navbar

```html
<nav class="sticky top-0 z-40 bg-white/80 backdrop-blur-md border-b border-gray-200">
  <div class="max-w-7xl mx-auto px-6 flex items-center justify-between h-16">
    <a href="/" class="text-lg font-bold text-gray-900">Logo</a>
    <div class="flex items-center gap-1">
      <a href="/features" class="px-3 py-2 text-sm text-gray-600 hover:text-gray-900 hover:bg-gray-100 rounded-lg">Features</a>
      <a href="/pricing" aria-current="page" class="px-3 py-2 text-sm text-gray-900 font-medium bg-gray-100 rounded-lg">Pricing</a>
      <a href="/docs" class="px-3 py-2 text-sm text-gray-600 hover:text-gray-900 hover:bg-gray-100 rounded-lg">Docs</a>
    </div>
    <button class="px-4 py-2 text-sm bg-blue-600 text-white rounded-lg hover:bg-blue-700">Sign up</button>
  </div>
</nav>
```

### Mobile tab bar

```html
<nav class="fixed bottom-0 inset-x-0 z-40 bg-white border-t border-gray-200 pb-safe">
  <div class="flex items-center justify-around h-16">
    <a href="/" class="flex flex-col items-center gap-1 text-blue-600" aria-current="page">
      <svg class="w-5 h-5"><!-- home icon --></svg>
      <span class="text-[10px] font-medium">Home</span>
    </a>
    <a href="/search" class="flex flex-col items-center gap-1 text-gray-400 hover:text-gray-600">
      <svg class="w-5 h-5"><!-- search icon --></svg>
      <span class="text-[10px]">Search</span>
    </a>
    <a href="/profile" class="flex flex-col items-center gap-1 text-gray-400 hover:text-gray-600">
      <svg class="w-5 h-5"><!-- user icon --></svg>
      <span class="text-[10px]">Profile</span>
    </a>
  </div>
</nav>
```

### Breadcrumbs

```html
<nav aria-label="Breadcrumb">
  <ol class="flex items-center gap-1.5 text-sm">
    <li><a href="/" class="text-gray-500 hover:text-gray-700">Home</a></li>
    <li class="text-gray-300">/</li>
    <li><a href="/products" class="text-gray-500 hover:text-gray-700">Products</a></li>
    <li class="text-gray-300">/</li>
    <li><span class="text-gray-900 font-medium" aria-current="page">Widget Pro</span></li>
  </ol>
</nav>
```

---

## Data Display

### Sortable table with sticky header

```html
<div class="overflow-auto rounded-xl border border-gray-200">
  <table class="w-full text-sm">
    <thead class="sticky top-0 bg-gray-50 border-b border-gray-200">
      <tr>
        <th class="text-left px-4 py-3 font-medium text-gray-600">
          <button class="inline-flex items-center gap-1 hover:text-gray-900">
            Name <svg class="w-3 h-3"><!-- sort icon --></svg>
          </button>
        </th>
        <th class="text-left px-4 py-3 font-medium text-gray-600">Email</th>
        <th class="text-right px-4 py-3 font-medium text-gray-600">Amount</th>
      </tr>
    </thead>
    <tbody class="divide-y divide-gray-100">
      <tr class="hover:bg-gray-50 transition-colors">
        <td class="px-4 py-3 text-gray-900 font-medium">Alice Johnson</td>
        <td class="px-4 py-3 text-gray-600">alice@example.com</td>
        <td class="px-4 py-3 text-gray-900 text-right tabular-nums">$1,200.00</td>
      </tr>
    </tbody>
  </table>
</div>
```

### List with actions

```html
<ul class="divide-y divide-gray-100" role="list">
  <li class="flex items-center justify-between py-4 px-4 hover:bg-gray-50 rounded-lg">
    <div class="flex items-center gap-3">
      <div class="w-10 h-10 rounded-full bg-blue-100 flex items-center justify-center text-blue-600 font-medium">AJ</div>
      <div>
        <p class="text-sm font-medium text-gray-900">Alice Johnson</p>
        <p class="text-xs text-gray-500">alice@example.com</p>
      </div>
    </div>
    <button class="p-2 text-gray-400 hover:text-gray-600 hover:bg-gray-100 rounded-lg" aria-label="More options">
      <svg class="w-4 h-4"><!-- dots icon --></svg>
    </button>
  </li>
</ul>
```

### Empty state

```html
<div class="flex flex-col items-center justify-center py-16 px-6 text-center">
  <div class="w-16 h-16 bg-gray-100 rounded-full flex items-center justify-center mb-4">
    <svg class="w-8 h-8 text-gray-400"><!-- inbox icon --></svg>
  </div>
  <h3 class="text-base font-semibold text-gray-900">No results found</h3>
  <p class="mt-1 text-sm text-gray-500 max-w-sm">Try adjusting your search or filters to find what you're looking for.</p>
  <button class="mt-4 px-4 py-2 text-sm bg-blue-600 text-white rounded-lg hover:bg-blue-700">Clear filters</button>
</div>
```

### Skeleton loading (table)

```html
<div class="animate-pulse">
  <div class="h-10 bg-gray-100 rounded-t-xl"></div>
  <div class="space-y-px">
    <div class="flex gap-4 px-4 py-3">
      <div class="h-4 w-32 bg-gray-200 rounded"></div>
      <div class="h-4 w-48 bg-gray-200 rounded"></div>
      <div class="h-4 w-20 bg-gray-200 rounded ml-auto"></div>
    </div>
    <!-- repeat 4-5 rows -->
  </div>
</div>
```

---

## Form Layout

### Single column form

```html
<form class="max-w-md mx-auto space-y-6">
  <div class="space-y-1.5">
    <label class="block text-sm font-medium text-gray-700">Name</label>
    <input class="w-full px-3 py-2 border border-gray-300 rounded-lg text-sm focus:ring-2 focus:ring-blue-600" />
  </div>
  <div class="space-y-1.5">
    <label class="block text-sm font-medium text-gray-700">Email</label>
    <input type="email" class="w-full px-3 py-2 border border-gray-300 rounded-lg text-sm focus:ring-2 focus:ring-blue-600" />
  </div>
  <button type="submit" class="w-full py-2.5 bg-blue-600 text-white rounded-lg text-sm font-medium hover:bg-blue-700">
    Submit
  </button>
</form>
```

### Section grouping

```html
<form class="max-w-lg mx-auto space-y-8">
  <fieldset class="space-y-4">
    <legend class="text-base font-semibold text-gray-900">Personal Information</legend>
    <p class="text-sm text-gray-500">This information is used for your profile.</p>
    <div class="grid grid-cols-2 gap-4">
      <div class="space-y-1.5">
        <label class="block text-sm font-medium text-gray-700">First name</label>
        <input class="w-full px-3 py-2 border border-gray-300 rounded-lg text-sm" />
      </div>
      <div class="space-y-1.5">
        <label class="block text-sm font-medium text-gray-700">Last name</label>
        <input class="w-full px-3 py-2 border border-gray-300 rounded-lg text-sm" />
      </div>
    </div>
  </fieldset>

  <div class="border-t border-gray-200"></div>

  <fieldset class="space-y-4">
    <legend class="text-base font-semibold text-gray-900">Address</legend>
    <!-- address fields -->
  </fieldset>
</form>
```

### Multi-step with progress indicator

```html
<!-- Step indicator -->
<div class="flex items-center justify-center gap-2 mb-8">
  <!-- Completed step -->
  <div class="flex items-center gap-2">
    <div class="w-8 h-8 rounded-full bg-blue-600 text-white flex items-center justify-center text-sm font-medium">1</div>
    <span class="text-sm font-medium text-gray-900 hidden sm:inline">Account</span>
  </div>
  <div class="w-12 h-0.5 bg-blue-600"></div>
  <!-- Current step -->
  <div class="flex items-center gap-2">
    <div class="w-8 h-8 rounded-full border-2 border-blue-600 text-blue-600 flex items-center justify-center text-sm font-medium">2</div>
    <span class="text-sm font-medium text-blue-600 hidden sm:inline">Profile</span>
  </div>
  <div class="w-12 h-0.5 bg-gray-200"></div>
  <!-- Future step -->
  <div class="flex items-center gap-2">
    <div class="w-8 h-8 rounded-full border-2 border-gray-300 text-gray-400 flex items-center justify-center text-sm font-medium">3</div>
    <span class="text-sm text-gray-400 hidden sm:inline">Review</span>
  </div>
</div>

<!-- Step content -->
<div class="max-w-md mx-auto space-y-6">
  <!-- form fields for current step -->
</div>

<!-- Step navigation -->
<div class="max-w-md mx-auto flex justify-between mt-8">
  <button class="px-4 py-2 text-sm text-gray-600 hover:text-gray-900">Back</button>
  <button class="px-6 py-2 text-sm bg-blue-600 text-white rounded-lg hover:bg-blue-700">Continue</button>
</div>
```

### Progressive disclosure

```html
<form class="max-w-md mx-auto space-y-6">
  <div class="space-y-1.5">
    <label class="block text-sm font-medium text-gray-700">Shipping method</label>
    <select onchange="toggleAddress(this.value)" class="w-full px-3 py-2 border border-gray-300 rounded-lg text-sm">
      <option value="digital">Digital delivery</option>
      <option value="physical">Physical shipping</option>
    </select>
  </div>

  <!-- Shown only when "physical" is selected -->
  <div id="address-section" class="space-y-4 hidden animate-in fade-in slide-in-from-top-2 duration-200">
    <div class="space-y-1.5">
      <label class="block text-sm font-medium text-gray-700">Street address</label>
      <input class="w-full px-3 py-2 border border-gray-300 rounded-lg text-sm" />
    </div>
    <div class="grid grid-cols-2 gap-4">
      <div class="space-y-1.5">
        <label class="block text-sm font-medium text-gray-700">City</label>
        <input class="w-full px-3 py-2 border border-gray-300 rounded-lg text-sm" />
      </div>
      <div class="space-y-1.5">
        <label class="block text-sm font-medium text-gray-700">ZIP code</label>
        <input class="w-full px-3 py-2 border border-gray-300 rounded-lg text-sm" />
      </div>
    </div>
  </div>
</form>
```
