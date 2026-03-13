# Advanced Frontend Design Patterns

> Forms, data display, loading states, error handling, and onboarding.
> All patterns include React/Tailwind/CSS code. Researched 2024-2026.

---

## 1. Multi-Step Form Design

### Architecture: Wizard Pattern with React Hook Form + Zod

```tsx
// Step schema validation — each step gets its own Zod schema
import { z } from 'zod';

const stepSchemas = {
  account: z.object({
    email: z.string().email('Enter a valid email'),
    password: z.string().min(8, 'Minimum 8 characters'),
  }),
  profile: z.object({
    name: z.string().min(2),
    company: z.string().optional(),
  }),
  review: z.object({}), // no validation on review step
};

type StepKey = keyof typeof stepSchemas;
```

### Form Context Provider (shares state across steps)

```tsx
import { createContext, useContext, useState } from 'react';

interface WizardState {
  currentStep: number;
  data: Record<string, any>;
  setStep: (step: number) => void;
  updateData: (stepData: Record<string, any>) => void;
}

const WizardContext = createContext<WizardState | null>(null);

export function WizardProvider({ children }: { children: React.ReactNode }) {
  const [currentStep, setStep] = useState(0);
  const [data, setData] = useState({});

  const updateData = (stepData: Record<string, any>) => {
    setData(prev => ({ ...prev, ...stepData }));
  };

  return (
    <WizardContext.Provider value={{ currentStep, data, setStep, updateData }}>
      {children}
    </WizardContext.Provider>
  );
}

export const useWizard = () => useContext(WizardContext)!;
```

### Step Component with Per-Step Validation

```tsx
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';

function StepForm({ schema, onNext, onBack, children }: StepFormProps) {
  const { data } = useWizard();
  const form = useForm({
    resolver: zodResolver(schema),
    defaultValues: data, // pre-fill from accumulated data
    mode: 'onBlur',      // validate on blur, not on every keystroke
    reValidateMode: 'onChange', // once error shown, clear on fix
  });

  const handleNext = form.handleSubmit((values) => {
    onNext(values);
  });

  return (
    <form onSubmit={handleNext} noValidate>
      {children(form)}
      <div className="flex justify-between mt-8">
        {onBack && (
          <button type="button" onClick={onBack}
            className="px-4 py-2 text-sm text-gray-600 hover:text-gray-900">
            Back
          </button>
        )}
        <button type="submit"
          className="px-6 py-2.5 text-sm bg-blue-600 text-white rounded-lg hover:bg-blue-700 ml-auto">
          Continue
        </button>
      </div>
    </form>
  );
}
```

### Progress Indicator with States

```html
<!-- Connected step indicator: completed / active / upcoming -->
<nav aria-label="Form progress" class="mb-8">
  <ol class="flex items-center justify-center">
    <!-- Completed step -->
    <li class="flex items-center">
      <div class="flex items-center gap-2">
        <div class="w-8 h-8 rounded-full bg-blue-600 text-white flex items-center justify-center">
          <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.5" d="M5 13l4 4L19 7"/>
          </svg>
        </div>
        <span class="text-sm font-medium text-gray-900 hidden sm:inline">Account</span>
      </div>
      <div class="w-16 h-0.5 bg-blue-600 mx-3"></div>
    </li>

    <!-- Active step -->
    <li class="flex items-center">
      <div class="flex items-center gap-2">
        <div class="w-8 h-8 rounded-full border-2 border-blue-600 bg-blue-50 text-blue-600
                    flex items-center justify-center text-sm font-semibold">2</div>
        <span class="text-sm font-semibold text-blue-600 hidden sm:inline">Profile</span>
      </div>
      <div class="w-16 h-0.5 bg-gray-200 mx-3"></div>
    </li>

    <!-- Upcoming step -->
    <li class="flex items-center">
      <div class="flex items-center gap-2">
        <div class="w-8 h-8 rounded-full border-2 border-gray-300 text-gray-400
                    flex items-center justify-center text-sm">3</div>
        <span class="text-sm text-gray-400 hidden sm:inline">Review</span>
      </div>
    </li>
  </ol>
</nav>
```

### localStorage Persistence (prevent data loss)

```tsx
const STORAGE_KEY = 'wizard-draft';

function usePersistedWizard() {
  const [data, setData] = useState(() => {
    const saved = localStorage.getItem(STORAGE_KEY);
    return saved ? JSON.parse(saved) : {};
  });

  const updateData = (stepData: Record<string, any>) => {
    setData((prev: Record<string, any>) => {
      const next = { ...prev, ...stepData };
      localStorage.setItem(STORAGE_KEY, JSON.stringify(next));
      return next;
    });
  };

  const clearDraft = () => localStorage.removeItem(STORAGE_KEY);

  return { data, updateData, clearDraft };
}
```

### Key rules
- Validate per step, not all at once at the end
- Allow back navigation without losing entered data
- Show completed steps as clickable (let users jump back)
- Persist to localStorage so refresh doesn't lose progress
- Mobile: stack step labels below circles, show only numbers on very small screens
- Disable forward jumping (can't skip to step 3 without completing step 2)

---

## 2. Inline Validation

### The Reward-Early, Punish-Late Pattern

The best validation timing is: **validate on blur first, then switch to onChange after the first error**.

```tsx
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';

function SmartValidationForm() {
  const form = useForm({
    resolver: zodResolver(schema),
    mode: 'onBlur',           // first validation: when user leaves field
    reValidateMode: 'onChange', // after error shown: re-validate on every change
  });

  return (
    <form onSubmit={form.handleSubmit(onSubmit)}>
      <div className="space-y-1.5">
        <label htmlFor="email" className="block text-sm font-medium text-gray-700">
          Email
        </label>
        <input
          id="email"
          type="email"
          aria-invalid={!!form.formState.errors.email}
          aria-describedby={form.formState.errors.email ? 'email-error' : 'email-hint'}
          className={`w-full px-3 py-2 rounded-lg text-sm transition-colors
            ${form.formState.errors.email
              ? 'border-2 border-red-500 focus:ring-red-500'
              : form.formState.dirtyFields.email && !form.formState.errors.email
                ? 'border-2 border-green-500 focus:ring-green-500'
                : 'border border-gray-300 focus:ring-blue-600'
            } focus:outline-none focus:ring-2`}
          {...form.register('email')}
        />
        {/* Error message */}
        {form.formState.errors.email && (
          <p id="email-error" className="text-xs text-red-600 flex items-center gap-1" role="alert">
            <svg className="w-3.5 h-3.5 shrink-0" fill="currentColor" viewBox="0 0 20 20">
              <path fillRule="evenodd" d="M18 10a8 8 0 11-16 0 8 8 0 0116 0zm-7 4a1 1 0 11-2 0 1 1 0 012 0zm-1-9a1 1 0 00-1 1v4a1 1 0 102 0V6a1 1 0 00-1-1z" clipRule="evenodd"/>
            </svg>
            {form.formState.errors.email.message}
          </p>
        )}
        {/* Success indicator */}
        {!form.formState.errors.email && form.formState.dirtyFields.email && (
          <p className="text-xs text-green-600 flex items-center gap-1">
            <svg className="w-3.5 h-3.5" fill="currentColor" viewBox="0 0 20 20">
              <path fillRule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm3.707-9.293a1 1 0 00-1.414-1.414L9 10.586 7.707 9.293a1 1 0 00-1.414 1.414l2 2a1 1 0 001.414 0l4-4z" clipRule="evenodd"/>
            </svg>
            Looks good
          </p>
        )}
        {/* Default hint */}
        {!form.formState.errors.email && !form.formState.dirtyFields.email && (
          <p id="email-hint" className="text-xs text-gray-500">We'll send a verification link</p>
        )}
      </div>
    </form>
  );
}
```

### Accessible Error Summary (on submit failure)

```html
<!-- Show at top of form when submission fails -->
<div role="alert" aria-live="assertive"
  class="bg-red-50 border border-red-200 rounded-lg p-4 mb-6">
  <h3 class="text-sm font-medium text-red-800">Please fix 3 errors to continue</h3>
  <ul class="mt-2 text-sm text-red-700 list-disc list-inside space-y-1">
    <li><a href="#email" class="underline hover:text-red-900">Email is required</a></li>
    <li><a href="#password" class="underline hover:text-red-900">Password must be 8+ characters</a></li>
    <li><a href="#terms" class="underline hover:text-red-900">You must accept the terms</a></li>
  </ul>
</div>
```

### Key rules
- Never validate empty required fields until submit attempt
- Error messages below the field, aligned left with the input
- Show success (green check) only after a field was invalid and is now fixed
- Link error summary items to the fields (click to focus)
- Use `aria-invalid="true"` + `aria-describedby` pointing to error message
- Use `role="alert"` on error messages so screen readers announce them
- Don't disable submit buttons. Let users submit and show what's wrong

---

## 3. Smart Defaults

### Country Auto-Detection from IP

```tsx
import { useEffect, useState } from 'react';

function useGeoDefaults() {
  const [defaults, setDefaults] = useState({
    country: '',
    currency: '',
    timezone: '',
    locale: '',
  });

  useEffect(() => {
    // Browser timezone detection (no API needed)
    const timezone = Intl.DateTimeFormat().resolvedOptions().timeZone;
    const locale = navigator.language || 'en-US';

    // IP-based geolocation (free tier)
    fetch('https://ipapi.co/json/')
      .then(r => r.json())
      .then(data => {
        setDefaults({
          country: data.country_code || '',
          currency: data.currency || 'USD',
          timezone,
          locale,
        });
      })
      .catch(() => {
        // Fallback: derive country from timezone
        const countryFromTZ = timezoneToCountry(timezone);
        setDefaults({ country: countryFromTZ, currency: 'USD', timezone, locale });
      });
  }, []);

  return defaults;
}
```

### Input Format Auto-Detection

```tsx
function SmartPhoneInput({ value, onChange }: InputProps) {
  const { country } = useGeoDefaults();

  // Auto-detect format from first digits
  const detectFormat = (input: string) => {
    const digits = input.replace(/\D/g, '');
    if (digits.startsWith('1') && digits.length <= 11) return 'US';
    if (digits.startsWith('971')) return 'UAE';
    if (digits.startsWith('966')) return 'KSA';
    return country; // fall back to geo-detected country
  };

  return (
    <div className="flex gap-2">
      <select
        value={detectFormat(value)}
        className="w-24 px-2 py-2 border border-gray-300 rounded-lg text-sm"
        onChange={(e) => { /* update country code prefix */ }}
      >
        <option value="US">+1</option>
        <option value="UAE">+971</option>
        <option value="KSA">+966</option>
      </select>
      <input
        type="tel"
        value={value}
        onChange={onChange}
        className="flex-1 px-3 py-2 border border-gray-300 rounded-lg text-sm"
        autoComplete="tel"
      />
    </div>
  );
}
```

### Previously Entered Values (remember user)

```tsx
function useRememberedDefaults(formKey: string) {
  const getDefaults = () => {
    try {
      const saved = localStorage.getItem(`form-defaults:${formKey}`);
      return saved ? JSON.parse(saved) : {};
    } catch {
      return {};
    }
  };

  const saveDefaults = (data: Record<string, any>) => {
    // Only save non-sensitive fields
    const { password, creditCard, cvv, ...safe } = data;
    localStorage.setItem(`form-defaults:${formKey}`, JSON.stringify(safe));
  };

  return { getDefaults, saveDefaults };
}
```

### Key rules
- Always allow users to override auto-detected values
- Show detected values as pre-filled, not locked
- Never auto-fill sensitive fields (password, payment info)
- For returning users: remember shipping address, preferred payment method, locale
- Use browser `Intl` APIs for timezone and locale before hitting external APIs

---

## 4. Input Masking

### Phone Number with react-imask

```tsx
import { IMaskInput } from 'react-imask';

function PhoneInput({ value, onChange }: InputProps) {
  return (
    <IMaskInput
      mask="+{1} (000) 000-0000"
      value={value}
      onAccept={(value) => onChange(value)}
      placeholder="+1 (555) 123-4567"
      className="w-full px-3 py-2 border border-gray-300 rounded-lg text-sm
                 focus:outline-none focus:ring-2 focus:ring-blue-600"
      inputMode="tel"
      autoComplete="tel"
    />
  );
}
```

### Credit Card with Auto-Detection

```tsx
import { IMaskInput } from 'react-imask';

function CreditCardInput() {
  const [cardType, setCardType] = useState('');

  const detectCardType = (value: string) => {
    const digits = value.replace(/\s/g, '');
    if (/^4/.test(digits)) return 'visa';
    if (/^5[1-5]/.test(digits)) return 'mastercard';
    if (/^3[47]/.test(digits)) return 'amex';
    if (/^6(?:011|5)/.test(digits)) return 'discover';
    return '';
  };

  return (
    <div className="relative">
      <IMaskInput
        mask="0000 0000 0000 0000"
        onAccept={(value) => setCardType(detectCardType(value))}
        placeholder="4242 4242 4242 4242"
        className="w-full pl-3 pr-12 py-2 border border-gray-300 rounded-lg text-sm
                   tracking-wider font-mono"
        inputMode="numeric"
        autoComplete="cc-number"
      />
      {cardType && (
        <div className="absolute right-3 top-1/2 -translate-y-1/2">
          <img src={`/icons/cards/${cardType}.svg`} alt={cardType} className="h-6" />
        </div>
      )}
    </div>
  );
}
```

### Date Input with Validation

```tsx
<IMaskInput
  mask="00/00/0000"
  placeholder="MM/DD/YYYY"
  inputMode="numeric"
  autoComplete="bday"
  className="w-full px-3 py-2 border border-gray-300 rounded-lg text-sm"
/>
```

### Native Alternative: `inputMode` + Formatting (no library)

```tsx
function NativeMaskedInput({ format, value, onChange }: MaskedProps) {
  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const raw = e.target.value.replace(/\D/g, '');
    let formatted = '';

    // Phone format: (XXX) XXX-XXXX
    if (format === 'phone') {
      if (raw.length > 0) formatted += '(' + raw.slice(0, 3);
      if (raw.length >= 3) formatted += ') ' + raw.slice(3, 6);
      if (raw.length >= 6) formatted += '-' + raw.slice(6, 10);
    }

    onChange(formatted || raw);
  };

  return <input value={value} onChange={handleChange} inputMode="numeric" />;
}
```

### Key rules
- Use `inputMode="numeric"` or `inputMode="tel"` to show number keyboard on mobile
- Always show the mask pattern as placeholder or visual guide
- Libraries: `react-imask` (most flexible), `react-number-format` (currency/numbers)
- Mask as you type, don't wait for blur
- Send unmasked value to the server, display masked value to the user

---

## 5. Autofill-Friendly Forms

### Correct `autocomplete` Values

```html
<!-- Login form -->
<form>
  <input type="email" autocomplete="username" name="email" />
  <input type="password" autocomplete="current-password" name="password" />
</form>

<!-- Registration form -->
<form>
  <input type="text" autocomplete="given-name" name="firstName" />
  <input type="text" autocomplete="family-name" name="lastName" />
  <input type="email" autocomplete="email" name="email" />
  <input type="tel" autocomplete="tel" name="phone" />
  <input type="password" autocomplete="new-password" name="password" />
</form>

<!-- Shipping address -->
<form>
  <input type="text" autocomplete="shipping street-address" name="address" />
  <input type="text" autocomplete="shipping address-level2" name="city" />
  <input type="text" autocomplete="shipping address-level1" name="state" />
  <input type="text" autocomplete="shipping postal-code" name="zip" />
  <input type="text" autocomplete="shipping country-name" name="country" />
</form>

<!-- Credit card -->
<form>
  <input type="text" autocomplete="cc-name" name="cardName" />
  <input type="text" autocomplete="cc-number" inputmode="numeric" name="cardNumber" />
  <input type="text" autocomplete="cc-exp" name="expiry" />
  <input type="text" autocomplete="cc-csc" inputmode="numeric" name="cvv" />
</form>
```

### Passkey Integration with Conditional UI

```html
<!-- Username field with passkey autofill support -->
<input
  type="text"
  id="username"
  autocomplete="username webauthn"
  name="username"
  class="w-full px-3 py-2 border border-gray-300 rounded-lg"
/>
```

```tsx
// Passkey conditional UI — shows passkeys in autofill dropdown
async function initPasskeyAutofill() {
  // Feature detection
  if (!window.PublicKeyCredential ||
      !PublicKeyCredential.isConditionalMediationAvailable) {
    return;
  }

  const available = await PublicKeyCredential.isConditionalMediationAvailable();
  if (!available) return;

  try {
    const credential = await navigator.credentials.get({
      mediation: 'conditional', // shows in autofill dropdown
      publicKey: {
        challenge: new Uint8Array(/* from server */),
        rpId: window.location.hostname,
        allowCredentials: [], // empty = discoverable credentials
        userVerification: 'preferred',
      },
    });
    // credential obtained — send to server for verification
    await verifyPasskey(credential);
  } catch (err) {
    // User dismissed or no passkey available — fall back to password
  }
}
```

### Key rules
- Always use `name` attributes that match standard names (email, password, firstName)
- Use correct `autocomplete` values. Each input gets one specific token
- Never use `autocomplete="off"` on login forms (browsers ignore it anyway)
- Group billing vs shipping with `autocomplete="billing ..."` and `autocomplete="shipping ..."`
- For passkeys: add `webauthn` token to username field autocomplete
- Test with browser autofill: Chrome DevTools > Elements > right-click input > "Autofill form"

---

## 6. Accessible Form Patterns

### Complete Accessible Input Pattern

```html
<div class="space-y-1.5" role="group">
  <label for="username" class="block text-sm font-medium text-gray-700">
    Username
    <span class="text-red-500 ml-0.5" aria-hidden="true">*</span>
    <span class="sr-only">(required)</span>
  </label>
  <input
    id="username"
    type="text"
    required
    aria-required="true"
    aria-invalid="true"
    aria-describedby="username-error username-hint"
    class="w-full px-3 py-2 border-2 border-red-500 rounded-lg text-sm
           focus:outline-none focus:ring-2 focus:ring-red-500"
  />
  <p id="username-hint" class="text-xs text-gray-500">
    3-20 characters, letters and numbers only
  </p>
  <p id="username-error" role="alert" class="text-xs text-red-600">
    Username must be at least 3 characters
  </p>
</div>
```

### Fieldset/Legend for Grouped Inputs

```html
<fieldset class="space-y-3">
  <legend class="text-sm font-medium text-gray-700">
    Notification preferences
  </legend>
  <div class="space-y-2">
    <label class="flex items-center gap-3 cursor-pointer">
      <input type="checkbox" name="notifications" value="email"
        class="w-4 h-4 rounded border-gray-300 text-blue-600
               focus:ring-2 focus:ring-blue-600 focus:ring-offset-2" />
      <span class="text-sm text-gray-700">Email notifications</span>
    </label>
    <label class="flex items-center gap-3 cursor-pointer">
      <input type="checkbox" name="notifications" value="sms"
        class="w-4 h-4 rounded border-gray-300 text-blue-600
               focus:ring-2 focus:ring-blue-600 focus:ring-offset-2" />
      <span class="text-sm text-gray-700">SMS notifications</span>
    </label>
  </div>
</fieldset>
```

### Live Region for Dynamic Validation

```tsx
function LiveValidationField({ name, validate }: FieldProps) {
  const [message, setMessage] = useState('');
  const [status, setStatus] = useState<'idle' | 'error' | 'success'>('idle');

  const handleBlur = (e: React.FocusEvent<HTMLInputElement>) => {
    const result = validate(e.target.value);
    setStatus(result.valid ? 'success' : 'error');
    setMessage(result.message);
  };

  return (
    <div>
      <input onBlur={handleBlur} aria-invalid={status === 'error'} />
      {/* polite: announced at next pause. assertive: announced immediately */}
      <div aria-live="polite" aria-atomic="true" className="min-h-[20px]">
        {status === 'error' && (
          <p className="text-xs text-red-600">{message}</p>
        )}
        {status === 'success' && (
          <p className="text-xs text-green-600">{message}</p>
        )}
      </div>
    </div>
  );
}
```

### Key rules
- Every input needs a visible `<label>` with `for` pointing to input `id`
- Use `fieldset` + `legend` for radio groups, checkbox groups, address sections
- `aria-describedby` can reference multiple IDs: `aria-describedby="hint error"`
- Required fields: visible indicator + `aria-required="true"` + native `required`
- Error messages: `role="alert"` OR `aria-live="polite"` container
- Don't use `placeholder` as the only label. It disappears on focus
- Focus management: on submit error, focus the first invalid field

---

## 7. Table Design

### Responsive Table with Card View on Mobile

```html
<!-- Desktop: standard table. Mobile: stacked cards -->
<div class="overflow-x-auto">
  <table class="w-full text-sm hidden md:table">
    <thead class="sticky top-0 z-10 bg-gray-50/95 backdrop-blur-sm border-b border-gray-200">
      <tr>
        <th class="text-left px-4 py-3 font-medium text-gray-500 text-xs uppercase tracking-wider">
          <button class="inline-flex items-center gap-1.5 hover:text-gray-900 transition-colors"
                  aria-sort="ascending">
            Name
            <svg class="w-3.5 h-3.5 text-blue-600" fill="currentColor" viewBox="0 0 20 20">
              <path d="M5 10l5-5 5 5H5z"/>
            </svg>
          </button>
        </th>
        <th class="text-left px-4 py-3 font-medium text-gray-500 text-xs uppercase tracking-wider">
          Status
        </th>
        <th class="text-right px-4 py-3 font-medium text-gray-500 text-xs uppercase tracking-wider">
          Amount
        </th>
        <th class="w-12 px-4 py-3">
          <span class="sr-only">Actions</span>
        </th>
      </tr>
    </thead>
    <tbody class="divide-y divide-gray-100">
      <tr class="hover:bg-gray-50/50 transition-colors">
        <td class="px-4 py-3.5">
          <div class="flex items-center gap-3">
            <div class="w-8 h-8 rounded-full bg-blue-100 flex items-center justify-center
                        text-blue-700 text-xs font-medium">AJ</div>
            <div>
              <p class="font-medium text-gray-900">Alice Johnson</p>
              <p class="text-xs text-gray-500">alice@example.com</p>
            </div>
          </div>
        </td>
        <td class="px-4 py-3.5">
          <span class="inline-flex items-center gap-1 px-2 py-0.5 rounded-full text-xs font-medium
                       bg-green-50 text-green-700 ring-1 ring-inset ring-green-600/20">
            <span class="w-1.5 h-1.5 rounded-full bg-green-500"></span>
            Active
          </span>
        </td>
        <td class="px-4 py-3.5 text-right tabular-nums text-gray-900 font-medium">$1,234.56</td>
        <td class="px-4 py-3.5 text-right">
          <button class="p-1.5 rounded-lg text-gray-400 hover:text-gray-600 hover:bg-gray-100"
                  aria-label="More options for Alice Johnson">
            <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 20 20">
              <path d="M10 6a2 2 0 110-4 2 2 0 010 4zM10 12a2 2 0 110-4 2 2 0 010 4zM10 18a2 2 0 110-4 2 2 0 010 4z"/>
            </svg>
          </button>
        </td>
      </tr>
    </tbody>
  </table>

  <!-- Mobile card view -->
  <div class="md:hidden space-y-3">
    <div class="bg-white border border-gray-200 rounded-xl p-4">
      <div class="flex items-center justify-between">
        <div class="flex items-center gap-3">
          <div class="w-10 h-10 rounded-full bg-blue-100 flex items-center justify-center
                      text-blue-700 text-sm font-medium">AJ</div>
          <div>
            <p class="font-medium text-gray-900">Alice Johnson</p>
            <p class="text-xs text-gray-500">alice@example.com</p>
          </div>
        </div>
        <span class="inline-flex items-center gap-1 px-2 py-0.5 rounded-full text-xs font-medium
                     bg-green-50 text-green-700">Active</span>
      </div>
      <div class="mt-3 pt-3 border-t border-gray-100 flex items-center justify-between">
        <span class="text-sm text-gray-500">Amount</span>
        <span class="text-sm font-semibold text-gray-900 tabular-nums">$1,234.56</span>
      </div>
    </div>
  </div>
</div>
```

### Horizontal Scroll Indicator

```css
/* Show gradient fade on edges when content overflows */
.table-scroll-container {
  position: relative;
}
.table-scroll-container::after {
  content: '';
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  width: 48px;
  background: linear-gradient(to right, transparent, white);
  pointer-events: none;
  opacity: 1;
  transition: opacity 0.2s;
}
/* Hide when scrolled to the end */
.table-scroll-container.scrolled-end::after {
  opacity: 0;
}
```

### Sorting Indicators

```tsx
type SortDirection = 'asc' | 'desc' | null;

function SortableHeader({ label, active, direction, onSort }: SortHeaderProps) {
  return (
    <th className="text-left px-4 py-3">
      <button
        onClick={onSort}
        className={`inline-flex items-center gap-1.5 text-xs uppercase tracking-wider font-medium
          ${active ? 'text-gray-900' : 'text-gray-500 hover:text-gray-700'} transition-colors`}
        aria-sort={active ? (direction === 'asc' ? 'ascending' : 'descending') : 'none'}
      >
        {label}
        <span className="flex flex-col">
          <svg className={`w-3 h-2 ${active && direction === 'asc' ? 'text-blue-600' : 'text-gray-300'}`}
               viewBox="0 0 12 8" fill="currentColor">
            <path d="M6 0l6 8H0z"/>
          </svg>
          <svg className={`w-3 h-2 ${active && direction === 'desc' ? 'text-blue-600' : 'text-gray-300'}`}
               viewBox="0 0 12 8" fill="currentColor">
            <path d="M6 8L0 0h12z"/>
          </svg>
        </span>
      </button>
    </th>
  );
}
```

---

## 8. Skeleton Loading

### Delayed Skeleton (show after 200ms)

```tsx
function useDelayedLoading(isLoading: boolean, delayMs = 200) {
  const [showSkeleton, setShowSkeleton] = useState(false);

  useEffect(() => {
    if (isLoading) {
      const timer = setTimeout(() => setShowSkeleton(true), delayMs);
      return () => clearTimeout(timer);
    }
    setShowSkeleton(false);
  }, [isLoading, delayMs]);

  return showSkeleton;
}

// Usage
function DataList() {
  const { data, isLoading } = useQuery(['items'], fetchItems);
  const showSkeleton = useDelayedLoading(isLoading);

  if (showSkeleton) return <ListSkeleton />;
  if (!isLoading && data) return <List items={data} />;
  return null; // brief flash: show nothing for <200ms loads
}
```

### Custom Shimmer Animation (beyond animate-pulse)

```html
<!-- Tailwind config extension for shimmer -->
<style>
  @keyframes shimmer {
    0% { background-position: -200% 0; }
    100% { background-position: 200% 0; }
  }
  .animate-shimmer {
    background: linear-gradient(
      90deg,
      rgb(229 231 235) 0%,      /* gray-200 */
      rgb(243 244 246) 40%,      /* gray-100 */
      rgb(229 231 235) 80%       /* gray-200 */
    );
    background-size: 200% 100%;
    animation: shimmer 1.5s ease-in-out infinite;
  }
</style>

<!-- Content-shaped skeleton for a user profile -->
<div class="flex items-center gap-4 p-4" aria-busy="true" aria-label="Loading profile">
  <!-- Avatar skeleton (circle) -->
  <div class="w-12 h-12 rounded-full animate-shimmer shrink-0"></div>
  <div class="flex-1 space-y-2">
    <!-- Name skeleton -->
    <div class="h-4 w-32 rounded animate-shimmer"></div>
    <!-- Email skeleton (slightly narrower) -->
    <div class="h-3 w-48 rounded animate-shimmer"></div>
  </div>
</div>

<!-- Content-shaped skeleton for a stats dashboard -->
<div class="grid grid-cols-3 gap-4" aria-busy="true">
  <div class="bg-white rounded-xl p-5 space-y-3">
    <div class="h-3 w-20 rounded animate-shimmer"></div>
    <div class="h-8 w-24 rounded animate-shimmer"></div>
    <div class="h-2.5 w-16 rounded animate-shimmer"></div>
  </div>
  <!-- repeat for other stat cards -->
</div>
```

### Skeleton to Content Transition

```tsx
function SkeletonTransition({ isLoading, skeleton, children }: TransitionProps) {
  return (
    <div className="relative">
      {/* Skeleton layer */}
      <div className={`transition-opacity duration-300 ${isLoading ? 'opacity-100' : 'opacity-0 pointer-events-none absolute inset-0'}`}>
        {skeleton}
      </div>
      {/* Content layer */}
      <div className={`transition-opacity duration-300 ${isLoading ? 'opacity-0' : 'opacity-100'}`}>
        {children}
      </div>
    </div>
  );
}
```

### Key rules
- Match skeleton shapes to actual content layout (don't use generic blocks)
- Text skeletons: vary widths (100%, 75%, 60%) to mimic real text
- Add `aria-busy="true"` to skeleton containers
- Show skeleton after 200ms delay (avoid flicker on fast loads)
- Never use spinners for content areas. Spinners are for buttons and small actions
- Skeleton rows: show 3-5 rows, not the full expected count

---

## 9. Empty States

### Three Types with Distinct Patterns

```html
<!-- TYPE 1: First-Run (onboarding moment) -->
<div class="flex flex-col items-center justify-center py-20 px-6 text-center max-w-md mx-auto">
  <div class="w-20 h-20 bg-blue-50 rounded-2xl flex items-center justify-center mb-6">
    <svg class="w-10 h-10 text-blue-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5"
            d="M12 6v6m0 0v6m0-6h6m-6 0H6"/>
    </svg>
  </div>
  <h3 class="text-lg font-semibold text-gray-900">Create your first project</h3>
  <p class="mt-2 text-sm text-gray-500 leading-relaxed">
    Projects help you organize your work. Start by creating one and inviting your team.
  </p>
  <button class="mt-6 px-5 py-2.5 bg-blue-600 text-white text-sm font-medium rounded-lg
                 hover:bg-blue-700 transition-colors">
    New project
  </button>
  <a href="/docs/getting-started" class="mt-3 text-sm text-blue-600 hover:text-blue-700">
    Learn how projects work
  </a>
</div>

<!-- TYPE 2: No Results (search/filter) -->
<div class="flex flex-col items-center justify-center py-16 px-6 text-center">
  <div class="w-16 h-16 bg-gray-100 rounded-full flex items-center justify-center mb-4">
    <svg class="w-7 h-7 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5"
            d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"/>
    </svg>
  </div>
  <h3 class="text-base font-semibold text-gray-900">No results for "acme widgets"</h3>
  <p class="mt-1 text-sm text-gray-500">Try a different search term or adjust your filters.</p>
  <div class="mt-4 flex gap-3">
    <button class="px-4 py-2 text-sm text-gray-700 border border-gray-300 rounded-lg
                   hover:bg-gray-50">Clear filters</button>
    <button class="px-4 py-2 text-sm text-blue-600 hover:text-blue-700">Browse all</button>
  </div>
</div>

<!-- TYPE 3: Error State -->
<div class="flex flex-col items-center justify-center py-16 px-6 text-center">
  <div class="w-16 h-16 bg-red-50 rounded-full flex items-center justify-center mb-4">
    <svg class="w-7 h-7 text-red-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5"
            d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-2.997L13.732 4.003c-.77-1.333-2.694-1.333-3.464 0L3.34 16.003C2.57 17.337 3.532 19 5.072 19z"/>
    </svg>
  </div>
  <h3 class="text-base font-semibold text-gray-900">Failed to load projects</h3>
  <p class="mt-1 text-sm text-gray-500">Check your connection and try again.</p>
  <button class="mt-4 px-4 py-2 text-sm bg-gray-900 text-white rounded-lg hover:bg-gray-800">
    Retry
  </button>
</div>
```

### Key rules
- First-run empty states: emphasize value proposition, single primary CTA
- No-results: suggest alternatives (clear filters, browse all, check spelling)
- Error empty states: explain what happened + retry button
- Illustration formula: icon in a soft-colored circle (not full illustrations unless brand supports it)
- One CTA maximum (optionally a secondary text link)
- Never show a completely blank screen

---

## 10. Infinite Scroll vs Pagination

### When to Use Each

| Pattern | Best for | Avoid when |
|---------|----------|------------|
| **Pagination** | Data tables, search results, admin panels, anything users need to bookmark/share a specific page | Browsing/discovery feeds |
| **Infinite scroll** | Social feeds, image galleries, activity logs, discovery experiences | Data users need to find specific items, anything with a footer |
| **Load more button** | Compromise: user-initiated, less jarring than infinite scroll. Good for product listings | Very long lists (1000+ items) |

### Infinite Scroll with TanStack Virtual

```tsx
import { useVirtualizer } from '@tanstack/react-virtual';
import { useInfiniteQuery } from '@tanstack/react-query';

function VirtualizedInfiniteList() {
  const { data, fetchNextPage, hasNextPage, isFetchingNextPage } = useInfiniteQuery({
    queryKey: ['items'],
    queryFn: ({ pageParam = 0 }) => fetchItems({ offset: pageParam, limit: 50 }),
    getNextPageParam: (lastPage, pages) =>
      lastPage.hasMore ? pages.length * 50 : undefined,
  });

  const allItems = data?.pages.flatMap(p => p.items) ?? [];
  const parentRef = useRef<HTMLDivElement>(null);

  const virtualizer = useVirtualizer({
    count: hasNextPage ? allItems.length + 1 : allItems.length,
    getScrollElement: () => parentRef.current,
    estimateSize: () => 72,      // estimated row height
    overscan: 5,                  // render 5 extra items above/below viewport
  });

  // Trigger fetch when approaching the end
  useEffect(() => {
    const lastItem = virtualizer.getVirtualItems().at(-1);
    if (!lastItem) return;
    if (lastItem.index >= allItems.length - 1 && hasNextPage && !isFetchingNextPage) {
      fetchNextPage();
    }
  }, [virtualizer.getVirtualItems(), hasNextPage, isFetchingNextPage]);

  return (
    <div ref={parentRef} className="h-[600px] overflow-auto">
      <div className="relative" style={{ height: `${virtualizer.getTotalSize()}px` }}>
        {virtualizer.getVirtualItems().map(virtualRow => (
          <div
            key={virtualRow.key}
            className="absolute top-0 left-0 w-full"
            style={{ height: `${virtualRow.size}px`, transform: `translateY(${virtualRow.start}px)` }}
          >
            {virtualRow.index < allItems.length ? (
              <ItemRow item={allItems[virtualRow.index]} />
            ) : (
              <div className="flex items-center justify-center h-full text-sm text-gray-500">
                Loading more...
              </div>
            )}
          </div>
        ))}
      </div>
    </div>
  );
}
```

### Load More Button (middle ground)

```html
<div class="text-center py-6">
  <button class="px-6 py-2.5 text-sm font-medium text-gray-700 bg-white border border-gray-300
                 rounded-lg hover:bg-gray-50 transition-colors">
    Load more results
    <span class="text-gray-400 ml-1">(showing 50 of 234)</span>
  </button>
</div>
```

---

## 11. Data Density Modes

### Density Toggle Component

```tsx
type Density = 'compact' | 'comfortable' | 'spacious';

const densityConfig: Record<Density, { py: string; text: string; gap: string }> = {
  compact:     { py: 'py-1.5', text: 'text-xs',  gap: 'gap-2' },
  comfortable: { py: 'py-3',   text: 'text-sm',  gap: 'gap-3' },
  spacious:    { py: 'py-4',   text: 'text-base', gap: 'gap-4' },
};

function DensityToggle({ value, onChange }: { value: Density; onChange: (d: Density) => void }) {
  const options: { value: Density; icon: string; label: string }[] = [
    { value: 'compact', icon: '|||', label: 'Compact' },
    { value: 'comfortable', icon: '| | |', label: 'Comfortable' },
    { value: 'spacious', icon: '|  |  |', label: 'Spacious' },
  ];

  return (
    <div class="inline-flex rounded-lg border border-gray-200 p-0.5 bg-gray-50">
      {options.map(opt => (
        <button
          key={opt.value}
          onClick={() => onChange(opt.value)}
          className={`px-3 py-1.5 text-xs rounded-md transition-colors
            ${value === opt.value
              ? 'bg-white shadow-sm text-gray-900 font-medium'
              : 'text-gray-500 hover:text-gray-700'}`}
          aria-label={opt.label}
          aria-pressed={value === opt.value}
        >
          {opt.label}
        </button>
      ))}
    </div>
  );
}
```

### Applying Density to Table

```tsx
function DenseTable({ density, items }: { density: Density; items: Item[] }) {
  const config = densityConfig[density];

  return (
    <table className="w-full">
      <tbody className="divide-y divide-gray-100">
        {items.map(item => (
          <tr key={item.id} className={`${config.py} hover:bg-gray-50`}>
            <td className={`px-4 ${config.py} ${config.text}`}>{item.name}</td>
            <td className={`px-4 ${config.py} ${config.text} text-gray-500`}>{item.email}</td>
            <td className={`px-4 ${config.py} ${config.text} text-right tabular-nums`}>
              {item.amount}
            </td>
          </tr>
        ))}
      </tbody>
    </table>
  );
}
```

---

## 12. Number Formatting

### Complete Formatting Utilities

```tsx
// All use native Intl API — no library needed

// Compact: 1.2K, 3.4M, 1.2B
function formatCompact(n: number, locale = 'en-US'): string {
  return new Intl.NumberFormat(locale, {
    notation: 'compact',
    compactDisplay: 'short',
    maximumFractionDigits: 1,
  }).format(n);
}
// formatCompact(1234)       → "1.2K"
// formatCompact(1234567)    → "1.2M"
// formatCompact(1234567890) → "1.2B"

// Currency
function formatCurrency(n: number, currency = 'USD', locale = 'en-US'): string {
  return new Intl.NumberFormat(locale, {
    style: 'currency',
    currency,
    minimumFractionDigits: 0,
    maximumFractionDigits: 2,
  }).format(n);
}
// formatCurrency(1234.5)           → "$1,234.50"
// formatCurrency(1234.5, 'SAR', 'ar-SA') → "١٬٢٣٤٫٥٠ ر.س."

// Percentage
function formatPercent(n: number, locale = 'en-US'): string {
  return new Intl.NumberFormat(locale, {
    style: 'percent',
    minimumFractionDigits: 0,
    maximumFractionDigits: 1,
  }).format(n);
}
// formatPercent(0.1234) → "12.3%"

// Relative time: "2 hours ago", "in 3 days"
function formatRelativeTime(date: Date, locale = 'en-US'): string {
  const rtf = new Intl.RelativeTimeFormat(locale, { numeric: 'auto' });
  const diff = date.getTime() - Date.now();
  const seconds = Math.round(diff / 1000);
  const minutes = Math.round(seconds / 60);
  const hours = Math.round(minutes / 60);
  const days = Math.round(hours / 24);

  if (Math.abs(seconds) < 60) return rtf.format(seconds, 'second');
  if (Math.abs(minutes) < 60) return rtf.format(minutes, 'minute');
  if (Math.abs(hours) < 24) return rtf.format(hours, 'hour');
  if (Math.abs(days) < 30) return rtf.format(days, 'day');
  return new Intl.DateTimeFormat(locale, { dateStyle: 'medium' }).format(date);
}
// formatRelativeTime(new Date(Date.now() - 7200000)) → "2 hours ago"
// formatRelativeTime(new Date(Date.now() + 86400000)) → "tomorrow"

// Ordinal: 1st, 2nd, 3rd, 4th
function formatOrdinal(n: number, locale = 'en-US'): string {
  const pr = new Intl.PluralRules(locale, { type: 'ordinal' });
  const suffixes: Record<string, string> = {
    one: 'st', two: 'nd', few: 'rd', other: 'th',
  };
  return `${n}${suffixes[pr.select(n)]}`;
}
```

### Display Pattern: Tabular Numbers

```html
<!-- Always use tabular-nums for numbers that change or align in columns -->
<span class="tabular-nums font-medium text-gray-900">$12,345.67</span>
<span class="tabular-nums text-sm text-gray-500">+12.3%</span>

<!-- Dashboard metric card -->
<div class="p-5">
  <p class="text-xs font-medium text-gray-500 uppercase tracking-wider">Revenue</p>
  <p class="mt-1 text-2xl font-semibold text-gray-900 tabular-nums">
    $42,389
  </p>
  <p class="mt-1 text-xs text-green-600 tabular-nums">
    +14.2% vs last month
  </p>
</div>
```

---

## 13. Optimistic UI

### React 19 useOptimistic Pattern

```tsx
import { useOptimistic, useTransition } from 'react';

function TodoList({ initialTodos }: { initialTodos: Todo[] }) {
  const [todos, setTodos] = useState(initialTodos);
  const [optimisticTodos, addOptimistic] = useOptimistic(
    todos,
    (currentTodos, newTodo: Todo) => [...currentTodos, { ...newTodo, pending: true }]
  );
  const [isPending, startTransition] = useTransition();

  async function addTodo(text: string) {
    const tempTodo: Todo = {
      id: `temp-${Date.now()}`,
      text,
      completed: false,
      pending: true,
    };

    startTransition(async () => {
      addOptimistic(tempTodo); // show immediately

      try {
        const savedTodo = await api.createTodo(text);
        setTodos(prev => [...prev, savedTodo]); // replace with server data
      } catch (error) {
        // useOptimistic auto-reverts on error (value resets to `todos`)
        toast.error('Failed to add todo. Please try again.');
      }
    });
  }

  return (
    <ul>
      {optimisticTodos.map(todo => (
        <li key={todo.id}
            className={todo.pending ? 'opacity-60' : ''}>
          {todo.text}
          {todo.pending && <span className="ml-2 text-xs text-gray-400">Saving...</span>}
        </li>
      ))}
    </ul>
  );
}
```

### TanStack Query Optimistic Update

```tsx
import { useMutation, useQueryClient } from '@tanstack/react-query';

function useToggleTodo() {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: (todoId: string) => api.toggleTodo(todoId),
    onMutate: async (todoId) => {
      // Cancel outgoing refetches
      await queryClient.cancelQueries({ queryKey: ['todos'] });

      // Snapshot previous value
      const previousTodos = queryClient.getQueryData<Todo[]>(['todos']);

      // Optimistically update
      queryClient.setQueryData<Todo[]>(['todos'], old =>
        old?.map(t => t.id === todoId ? { ...t, completed: !t.completed } : t)
      );

      return { previousTodos }; // return context for rollback
    },
    onError: (_err, _todoId, context) => {
      // Rollback on error
      queryClient.setQueryData(['todos'], context?.previousTodos);
      toast.error('Failed to update. Reverted.');
    },
    onSettled: () => {
      // Always refetch to ensure consistency
      queryClient.invalidateQueries({ queryKey: ['todos'] });
    },
  });
}
```

### Key rules
- Show pending state visually (reduced opacity, "Saving..." label)
- Always snapshot previous state before optimistic update
- On error: revert to snapshot + show non-intrusive error (toast, not modal)
- After success: refetch from server to ensure consistency
- Don't use optimistic UI for irreversible actions (delete, payments)
- Keep optimistic updater functions pure and fast

---

## 14. Progressive Loading

### Blur-Up Image Loading (LQIP)

```tsx
function ProgressiveImage({ src, alt, className }: ProgressiveImageProps) {
  const [loaded, setLoaded] = useState(false);
  const [currentSrc, setCurrentSrc] = useState('');

  useEffect(() => {
    // Generate tiny inline placeholder (or use a pre-generated thumb URL)
    const thumbSrc = src.replace(/\.(\w+)$/, '-thumb.$1'); // e.g., photo-thumb.jpg (20px wide)
    setCurrentSrc(thumbSrc);

    const img = new Image();
    img.onload = () => {
      setCurrentSrc(src);
      setLoaded(true);
    };
    img.src = src;
  }, [src]);

  return (
    <img
      src={currentSrc}
      alt={alt}
      className={`${className} transition-all duration-500 ease-out
        ${loaded ? 'blur-0 scale-100' : 'blur-lg scale-105'}`}
    />
  );
}
```

### BlurHash Placeholder

```tsx
import { Blurhash } from 'react-blurhash';

function BlurHashImage({ hash, src, alt, width, height }: BlurHashImageProps) {
  const [loaded, setLoaded] = useState(false);

  return (
    <div className="relative overflow-hidden" style={{ aspectRatio: `${width}/${height}` }}>
      {/* Blurhash placeholder */}
      {!loaded && (
        <Blurhash
          hash={hash} // e.g. "LEHV6nWB2yk8pyo0adR*.7kCMdnj"
          width="100%"
          height="100%"
          resolutionX={32}
          resolutionY={32}
          punch={1}
          className="absolute inset-0"
        />
      )}
      {/* Full image */}
      <img
        src={src}
        alt={alt}
        onLoad={() => setLoaded(true)}
        className={`w-full h-full object-cover transition-opacity duration-500
          ${loaded ? 'opacity-100' : 'opacity-0'}`}
      />
    </div>
  );
}
```

### Skeleton to Content Transition

```css
/* Smooth transition from skeleton to content */
@keyframes content-reveal {
  from {
    opacity: 0;
    transform: translateY(4px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.content-loaded {
  animation: content-reveal 0.3s cubic-bezier(0.16, 1, 0.3, 1) forwards;
}
```

---

## 15. Toast / Notification Design

### Complete Toast System

```tsx
type ToastType = 'success' | 'error' | 'warning' | 'info';

interface Toast {
  id: string;
  type: ToastType;
  title: string;
  description?: string;
  action?: { label: string; onClick: () => void };
  duration?: number; // ms. 0 = persistent
}

const toastConfig: Record<ToastType, { icon: string; accent: string; role: string }> = {
  success: { icon: 'check-circle', accent: 'text-green-500', role: 'status' },
  error:   { icon: 'x-circle',     accent: 'text-red-500',   role: 'alert' },
  warning: { icon: 'alert-triangle', accent: 'text-amber-500', role: 'alert' },
  info:    { icon: 'info',         accent: 'text-blue-500',  role: 'status' },
};

const defaultDurations: Record<ToastType, number> = {
  success: 5000,
  info: 5000,
  warning: 8000,
  error: 0, // persistent — user must dismiss
};
```

### Toast Container with Position Awareness

```html
<!-- Desktop: top-right. Mobile: bottom-center -->
<div
  class="fixed z-50 flex flex-col gap-2 pointer-events-none
         bottom-4 left-1/2 -translate-x-1/2 w-[calc(100%-2rem)] max-w-sm
         md:bottom-auto md:top-4 md:right-4 md:left-auto md:translate-x-0"
  aria-live="polite"
>
  <!-- Individual toasts get pointer-events-auto -->
</div>
```

### Toast with Action Button and Progress Bar

```html
<div role="alert"
  class="pointer-events-auto bg-white border border-gray-200 rounded-xl shadow-lg
         overflow-hidden animate-in slide-in-from-bottom-2 md:slide-in-from-right-2
         duration-300 ease-out">
  <!-- Progress bar (auto-dismiss timer) -->
  <div class="h-0.5 bg-blue-500 animate-shrink origin-left"
       style="animation-duration: 5000ms"></div>

  <div class="flex items-start gap-3 p-4">
    <div class="shrink-0 w-5 h-5 text-green-500 mt-0.5">
      <!-- check-circle icon -->
    </div>
    <div class="flex-1 min-w-0">
      <p class="text-sm font-medium text-gray-900">Invoice sent</p>
      <p class="text-xs text-gray-500 mt-0.5">Invoice #1234 sent to alice@example.com</p>
      <button class="mt-2 text-xs font-medium text-blue-600 hover:text-blue-700">
        Undo
      </button>
    </div>
    <button class="shrink-0 p-1 rounded text-gray-400 hover:text-gray-600 hover:bg-gray-100"
            aria-label="Dismiss">
      <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/>
      </svg>
    </button>
  </div>
</div>
```

```css
/* Progress bar animation for auto-dismiss */
@keyframes shrink {
  from { transform: scaleX(1); }
  to { transform: scaleX(0); }
}
.animate-shrink {
  animation: shrink linear forwards;
}
```

### Key rules
- Auto-dismiss: 5s success/info, 8s warning, persistent for errors
- Max 3 visible toasts. Older ones slide out
- Desktop: top-right. Mobile: bottom-center (above tab bar)
- Swipe to dismiss on mobile
- Action buttons: "Undo", "Retry", "View". Max one action per toast
- Use `role="status"` for informational, `role="alert"` for errors/warnings
- Pause auto-dismiss timer on hover

---

## 16. Progress Indicators

### Determinate vs Indeterminate

```html
<!-- Determinate: known progress (file upload, multi-step process) -->
<div class="space-y-2">
  <div class="flex justify-between text-sm">
    <span class="font-medium text-gray-700">Uploading...</span>
    <span class="tabular-nums text-gray-500">67%</span>
  </div>
  <div class="h-2 bg-gray-100 rounded-full overflow-hidden">
    <div class="h-full bg-blue-600 rounded-full transition-all duration-300 ease-out"
         style="width: 67%"
         role="progressbar" aria-valuenow="67" aria-valuemin="0" aria-valuemax="100">
    </div>
  </div>
</div>

<!-- Indeterminate: unknown duration (processing, loading) -->
<div class="h-1 bg-gray-100 rounded-full overflow-hidden" role="progressbar">
  <div class="h-full w-1/3 bg-blue-600 rounded-full animate-indeterminate"></div>
</div>

<style>
@keyframes indeterminate {
  0% { transform: translateX(-100%); }
  100% { transform: translateX(400%); }
}
.animate-indeterminate {
  animation: indeterminate 1.5s cubic-bezier(0.65, 0, 0.35, 1) infinite;
}
</style>
```

### Perceived Speed: Start Fast, Slow Down

```tsx
// Progress that feels faster than it is
function usePerceivedProgress(isLoading: boolean) {
  const [progress, setProgress] = useState(0);

  useEffect(() => {
    if (!isLoading) {
      setProgress(100);
      return;
    }

    setProgress(0);
    const intervals = [
      { target: 30, duration: 500 },   // fast start: 0-30% in 500ms
      { target: 60, duration: 1500 },   // medium: 30-60% in 1.5s
      { target: 80, duration: 3000 },   // slow: 60-80% in 3s
      { target: 90, duration: 5000 },   // crawl: 80-90% in 5s
      // never reach 100% until actually done
    ];

    let i = 0;
    const advance = () => {
      if (i >= intervals.length) return;
      const { target, duration } = intervals[i];
      setProgress(target);
      i++;
      setTimeout(advance, duration);
    };
    advance();
  }, [isLoading]);

  return progress;
}
```

### NProgress-Style Top Bar

```html
<!-- Fixed bar at top of page -->
<div class="fixed top-0 left-0 right-0 z-[100] h-0.5">
  <div class="h-full bg-blue-600 transition-all duration-300 ease-out shadow-[0_0_8px_rgba(59,130,246,0.5)]"
       style="width: 67%">
  </div>
</div>
```

---

## 17. First-Run Experience

### Progressive Disclosure Onboarding

```tsx
function OnboardingChecklist() {
  const [steps, setSteps] = useState([
    { id: 'profile', label: 'Complete your profile', done: false, href: '/settings' },
    { id: 'team', label: 'Invite a team member', done: false, href: '/team/invite' },
    { id: 'project', label: 'Create your first project', done: false, href: '/projects/new' },
    { id: 'integration', label: 'Connect an integration', done: false, href: '/integrations' },
  ]);

  const completedCount = steps.filter(s => s.done).length;
  const progress = (completedCount / steps.length) * 100;

  return (
    <div className="bg-white border border-gray-200 rounded-xl p-5 max-w-sm">
      <div className="flex items-center justify-between mb-4">
        <h3 className="text-sm font-semibold text-gray-900">Getting started</h3>
        <span className="text-xs text-gray-500 tabular-nums">
          {completedCount}/{steps.length}
        </span>
      </div>

      {/* Progress bar */}
      <div className="h-1.5 bg-gray-100 rounded-full mb-4 overflow-hidden">
        <div className="h-full bg-blue-600 rounded-full transition-all duration-500 ease-out"
             style={{ width: `${progress}%` }} />
      </div>

      {/* Steps */}
      <ul className="space-y-1">
        {steps.map(step => (
          <li key={step.id}>
            <a href={step.href}
               className={`flex items-center gap-3 px-3 py-2 rounded-lg text-sm transition-colors
                 ${step.done
                   ? 'text-gray-400 line-through'
                   : 'text-gray-700 hover:bg-gray-50'}`}>
              {step.done ? (
                <svg className="w-4 h-4 text-green-500 shrink-0" fill="currentColor" viewBox="0 0 20 20">
                  <path fillRule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm3.707-9.293a1 1 0 00-1.414-1.414L9 10.586 7.707 9.293a1 1 0 00-1.414 1.414l2 2a1 1 0 001.414 0l4-4z"/>
                </svg>
              ) : (
                <div className="w-4 h-4 rounded-full border-2 border-gray-300 shrink-0" />
              )}
              {step.label}
            </a>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

### Empty State as Onboarding

```html
<!-- Instead of a blank dashboard, show what it will become -->
<div class="relative">
  <!-- Faded preview of what the dashboard looks like with data -->
  <div class="opacity-[0.08] pointer-events-none select-none" aria-hidden="true">
    <!-- Render mock dashboard content here -->
    <div class="grid grid-cols-3 gap-4 p-6">
      <div class="bg-gray-200 rounded-xl p-5 h-24"></div>
      <div class="bg-gray-200 rounded-xl p-5 h-24"></div>
      <div class="bg-gray-200 rounded-xl p-5 h-24"></div>
    </div>
  </div>

  <!-- Overlay CTA -->
  <div class="absolute inset-0 flex items-center justify-center">
    <div class="text-center p-8 bg-white rounded-2xl shadow-xl max-w-sm">
      <h2 class="text-lg font-semibold text-gray-900">Your dashboard awaits</h2>
      <p class="mt-2 text-sm text-gray-500">
        Create your first project to see analytics, team activity, and progress here.
      </p>
      <button class="mt-4 px-5 py-2.5 bg-blue-600 text-white text-sm font-medium rounded-lg">
        Create project
      </button>
    </div>
  </div>
</div>
```

---

## 18. Tooltips and Coachmarks

### Spotlight Tour with driver.js Pattern

```tsx
// Lightweight tour using CSS clip-path spotlight
function useProductTour() {
  const [step, setStep] = useState(-1); // -1 = not started
  const steps = [
    {
      target: '#sidebar-projects',
      title: 'Your projects',
      description: 'All your projects live here. Click to switch between them.',
      position: 'right' as const,
    },
    {
      target: '#header-search',
      title: 'Quick search',
      description: 'Press Cmd+K to search anything: projects, tasks, team members.',
      position: 'bottom' as const,
    },
    {
      target: '#btn-new-task',
      title: 'Create tasks',
      description: 'Start adding tasks to track your work.',
      position: 'left' as const,
    },
  ];

  return {
    isActive: step >= 0,
    currentStep: steps[step],
    stepIndex: step,
    totalSteps: steps.length,
    start: () => setStep(0),
    next: () => setStep(s => Math.min(s + 1, steps.length - 1)),
    prev: () => setStep(s => Math.max(s - 1, 0)),
    skip: () => {
      setStep(-1);
      localStorage.setItem('tour-completed', 'true');
    },
    finish: () => {
      setStep(-1);
      localStorage.setItem('tour-completed', 'true');
    },
  };
}
```

### Coachmark Tooltip Component

```html
<!-- Tooltip positioned next to highlighted element -->
<div class="absolute z-50 w-72 bg-gray-900 text-white rounded-xl p-4 shadow-2xl"
     role="dialog" aria-label="Tour step 2 of 3">
  <!-- Arrow (positioned based on placement) -->
  <div class="absolute -left-2 top-6 w-0 h-0
              border-t-8 border-t-transparent
              border-r-8 border-r-gray-900
              border-b-8 border-b-transparent"></div>

  <p class="text-sm font-medium">Quick search</p>
  <p class="mt-1 text-xs text-gray-300 leading-relaxed">
    Press Cmd+K to search anything: projects, tasks, team members.
  </p>
  <div class="mt-3 flex items-center justify-between">
    <span class="text-xs text-gray-400">2 of 3</span>
    <div class="flex gap-2">
      <button class="px-3 py-1.5 text-xs text-gray-400 hover:text-white">Skip</button>
      <button class="px-3 py-1.5 text-xs bg-white text-gray-900 rounded-md font-medium
                     hover:bg-gray-100">Next</button>
    </div>
  </div>
</div>

<!-- Backdrop with spotlight cutout -->
<div class="fixed inset-0 z-40 bg-black/50 transition-opacity duration-300"
     style="clip-path: polygon(
       0% 0%, 0% 100%, 100% 100%, 100% 0%,
       /* cutout for highlighted element (x, y, width, height) */
       calc(var(--spotlight-x) - 8px) calc(var(--spotlight-y) - 8px),
       calc(var(--spotlight-x) - 8px) calc(var(--spotlight-y) + var(--spotlight-h) + 8px),
       calc(var(--spotlight-x) + var(--spotlight-w) + 8px) calc(var(--spotlight-y) + var(--spotlight-h) + 8px),
       calc(var(--spotlight-x) + var(--spotlight-w) + 8px) calc(var(--spotlight-y) - 8px)
     )">
</div>
```

### Key rules
- Max 3-5 steps per tour. Users skip long tours
- Always offer "Skip" on every step
- Don't auto-start tours. Trigger from a "Take a tour" button or after first login
- Persist completion in localStorage or user preferences
- Use CSS `clip-path` for spotlight (GPU-accelerated, works in dark mode)
- Position tooltips with Floating UI for smart edge detection

---

## 19. Contextual Help

### Inline Expandable Hint

```html
<div class="space-y-1.5">
  <div class="flex items-center gap-1.5">
    <label for="api-key" class="text-sm font-medium text-gray-700">API Key</label>
    <button type="button"
            aria-expanded="false"
            aria-controls="api-key-help"
            onclick="toggleHelp(this)"
            class="w-4 h-4 rounded-full bg-gray-200 text-gray-500 text-[10px] font-bold
                   hover:bg-gray-300 flex items-center justify-center"
            aria-label="What is an API key?">
      ?
    </button>
  </div>

  <!-- Expandable help text -->
  <div id="api-key-help" class="hidden">
    <div class="bg-blue-50 border border-blue-100 rounded-lg p-3 text-xs text-blue-800 leading-relaxed">
      <p>Your API key is found in <strong>Settings > Integrations > API</strong>.</p>
      <p class="mt-1">It starts with <code class="bg-blue-100 px-1 rounded">sk_live_</code> for production.</p>
      <a href="/docs/api-keys" class="mt-2 inline-block text-blue-600 hover:text-blue-700 font-medium">
        Learn more about API keys
      </a>
    </div>
  </div>

  <input id="api-key" type="text"
    class="w-full px-3 py-2 border border-gray-300 rounded-lg text-sm font-mono" />
</div>
```

### Info Tooltip (hover/focus)

```html
<div class="relative inline-flex items-center gap-1">
  <span class="text-sm text-gray-700">Processing fee</span>
  <div class="group relative">
    <button class="w-4 h-4 rounded-full text-gray-400 hover:text-gray-600"
            aria-label="About processing fees">
      <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 20 20">
        <path fill-rule="evenodd" d="M18 10a8 8 0 11-16 0 8 8 0 0116 0zm-7-4a1 1 0 11-2 0 1 1 0 012 0zM9 9a1 1 0 000 2v3a1 1 0 001 1h1a1 1 0 100-2v-3a1 1 0 00-1-1H9z" clip-rule="evenodd"/>
      </svg>
    </button>
    <div role="tooltip"
         class="invisible group-hover:visible group-focus-within:visible
                absolute bottom-full left-1/2 -translate-x-1/2 mb-2
                w-56 p-3 bg-gray-900 text-white text-xs rounded-lg shadow-lg
                opacity-0 group-hover:opacity-100 group-focus-within:opacity-100
                transition-opacity duration-150">
      A 2.9% + $0.30 fee is applied to each transaction to cover payment processing costs.
      <div class="absolute top-full left-1/2 -translate-x-1/2 w-0 h-0
                  border-l-4 border-l-transparent border-r-4 border-r-transparent
                  border-t-4 border-t-gray-900"></div>
    </div>
  </div>
</div>
```

---

## 20. Error Page Design

### 404 Page

```html
<div class="min-h-[60vh] flex items-center justify-center px-6">
  <div class="text-center max-w-md">
    <p class="text-7xl font-bold text-gray-200 tabular-nums">404</p>
    <h1 class="mt-4 text-xl font-semibold text-gray-900">Page not found</h1>
    <p class="mt-2 text-sm text-gray-500 leading-relaxed">
      The page you're looking for doesn't exist or has been moved.
    </p>
    <div class="mt-6 flex flex-col sm:flex-row gap-3 justify-center">
      <a href="/"
         class="px-5 py-2.5 bg-gray-900 text-white text-sm font-medium rounded-lg hover:bg-gray-800">
        Go home
      </a>
      <button onclick="history.back()"
              class="px-5 py-2.5 text-sm font-medium text-gray-700 border border-gray-300
                     rounded-lg hover:bg-gray-50">
        Go back
      </button>
    </div>
  </div>
</div>
```

### 500 Error with Retry

```html
<div class="min-h-[60vh] flex items-center justify-center px-6">
  <div class="text-center max-w-md">
    <div class="w-16 h-16 bg-red-50 rounded-full flex items-center justify-center mx-auto">
      <svg class="w-8 h-8 text-red-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5"
              d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-2.997L13.732 4.003c-.77-1.333-2.694-1.333-3.464 0L3.34 16.003C2.57 17.337 3.532 19 5.072 19z"/>
      </svg>
    </div>
    <h1 class="mt-4 text-xl font-semibold text-gray-900">Something went wrong</h1>
    <p class="mt-2 text-sm text-gray-500">
      We're having trouble loading this page. This is usually temporary.
    </p>
    <div class="mt-6 flex flex-col gap-3 items-center">
      <button onclick="location.reload()"
              class="px-5 py-2.5 bg-gray-900 text-white text-sm font-medium rounded-lg hover:bg-gray-800
                     inline-flex items-center gap-2">
        <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"/>
        </svg>
        Try again
      </button>
      <a href="mailto:support@example.com" class="text-sm text-gray-500 hover:text-gray-700">
        Contact support
      </a>
    </div>
  </div>
</div>
```

### Offline Page

```html
<div class="min-h-[60vh] flex items-center justify-center px-6">
  <div class="text-center max-w-md">
    <div class="w-16 h-16 bg-gray-100 rounded-full flex items-center justify-center mx-auto">
      <svg class="w-8 h-8 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5"
              d="M18.364 5.636a9 9 0 010 12.728m-3.536-3.536a4 4 0 010-5.656M6 18L18 6"/>
      </svg>
    </div>
    <h1 class="mt-4 text-xl font-semibold text-gray-900">You're offline</h1>
    <p class="mt-2 text-sm text-gray-500">
      Check your internet connection and try again.
    </p>
    <button onclick="location.reload()"
            class="mt-6 px-5 py-2.5 bg-gray-900 text-white text-sm font-medium rounded-lg hover:bg-gray-800">
      Retry connection
    </button>
  </div>
</div>
```

---

## 21. Inline Error Recovery

### Retry with Exponential Backoff

```tsx
function useRetryFetch<T>(fetcher: () => Promise<T>, maxRetries = 3) {
  const [data, setData] = useState<T | null>(null);
  const [error, setError] = useState<string | null>(null);
  const [isLoading, setIsLoading] = useState(true);
  const [retryCount, setRetryCount] = useState(0);

  const execute = useCallback(async () => {
    setIsLoading(true);
    setError(null);

    try {
      const result = await fetcher();
      setData(result);
      setRetryCount(0);
    } catch (err) {
      setError(err instanceof Error ? err.message : 'Something went wrong');
    } finally {
      setIsLoading(false);
    }
  }, [fetcher]);

  const retry = useCallback(() => {
    if (retryCount < maxRetries) {
      setRetryCount(c => c + 1);
      // Exponential backoff: 1s, 2s, 4s
      const delay = Math.pow(2, retryCount) * 1000;
      setTimeout(execute, delay);
    }
  }, [retryCount, maxRetries, execute]);

  useEffect(() => { execute(); }, [execute]);

  return { data, error, isLoading, retry, retryCount, maxRetries };
}
```

### Inline Error with Fallback Content

```tsx
function DataSection() {
  const { data, error, isLoading, retry } = useRetryFetch(fetchDashboardData);

  if (error) {
    return (
      <div className="bg-red-50 border border-red-100 rounded-xl p-5">
        <div className="flex items-start gap-3">
          <svg className="w-5 h-5 text-red-400 mt-0.5 shrink-0" fill="currentColor" viewBox="0 0 20 20">
            <path fillRule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zM8.707 7.293a1 1 0 00-1.414 1.414L8.586 10l-1.293 1.293a1 1 0 101.414 1.414L10 11.414l1.293 1.293a1 1 0 001.414-1.414L11.414 10l1.293-1.293a1 1 0 00-1.414-1.414L10 8.586 8.707 7.293z"/>
          </svg>
          <div className="flex-1">
            <p className="text-sm font-medium text-red-800">
              Couldn't load dashboard data
            </p>
            <p className="mt-1 text-xs text-red-600">{error}</p>
            <button onClick={retry}
                    className="mt-3 text-xs font-medium text-red-700 hover:text-red-800
                               underline underline-offset-2">
              Try again
            </button>
          </div>
        </div>
      </div>
    );
  }

  return <Dashboard data={data} />;
}
```

### React Error Boundary

```tsx
import { Component, type ReactNode } from 'react';

interface ErrorBoundaryState {
  hasError: boolean;
  error: Error | null;
}

class ErrorBoundary extends Component<
  { children: ReactNode; fallback?: ReactNode },
  ErrorBoundaryState
> {
  state: ErrorBoundaryState = { hasError: false, error: null };

  static getDerivedStateFromError(error: Error) {
    return { hasError: true, error };
  }

  render() {
    if (this.state.hasError) {
      return this.props.fallback || (
        <div className="p-6 text-center">
          <p className="text-sm text-gray-600">This section encountered an error.</p>
          <button
            onClick={() => this.setState({ hasError: false, error: null })}
            className="mt-3 text-sm text-blue-600 hover:text-blue-700 font-medium"
          >
            Try again
          </button>
        </div>
      );
    }
    return this.props.children;
  }
}
```

---

## 22. Network-Aware UI

### Online/Offline Hook

```tsx
function useNetworkStatus() {
  const [isOnline, setIsOnline] = useState(navigator.onLine);
  const [wasOffline, setWasOffline] = useState(false);

  useEffect(() => {
    const handleOnline = () => {
      setIsOnline(true);
      setWasOffline(true);
      // Auto-hide "back online" banner after 3s
      setTimeout(() => setWasOffline(false), 3000);
    };
    const handleOffline = () => {
      setIsOnline(false);
    };

    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);

  return { isOnline, wasOffline };
}
```

### Network Status Banner

```tsx
function NetworkBanner() {
  const { isOnline, wasOffline } = useNetworkStatus();

  if (!isOnline) {
    return (
      <div className="fixed top-0 inset-x-0 z-[100] bg-gray-900 text-white px-4 py-2.5
                      flex items-center justify-center gap-2 text-sm"
           role="alert">
        <svg className="w-4 h-4 text-amber-400" fill="currentColor" viewBox="0 0 20 20">
          <path fillRule="evenodd" d="M8.257 3.099c.765-1.36 2.722-1.36 3.486 0l5.58 9.92c.75 1.334-.213 2.98-1.742 2.98H4.42c-1.53 0-2.493-1.646-1.743-2.98l5.58-9.92zM11 13a1 1 0 11-2 0 1 1 0 012 0zm-1-8a1 1 0 00-1 1v3a1 1 0 002 0V6a1 1 0 00-1-1z"/>
        </svg>
        You're offline. Changes will sync when you reconnect.
      </div>
    );
  }

  if (wasOffline) {
    return (
      <div className="fixed top-0 inset-x-0 z-[100] bg-green-600 text-white px-4 py-2
                      flex items-center justify-center gap-2 text-sm animate-in slide-in-from-top duration-300">
        <svg className="w-4 h-4" fill="currentColor" viewBox="0 0 20 20">
          <path fillRule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm3.707-9.293a1 1 0 00-1.414-1.414L9 10.586 7.707 9.293a1 1 0 00-1.414 1.414l2 2a1 1 0 001.414 0l4-4z"/>
        </svg>
        Back online. Syncing changes...
      </div>
    );
  }

  return null;
}
```

### Optimistic Queue for Offline Actions

```tsx
interface QueuedAction {
  id: string;
  type: string;
  payload: any;
  timestamp: number;
  retries: number;
}

function useOfflineQueue() {
  const [queue, setQueue] = useState<QueuedAction[]>(() => {
    const saved = localStorage.getItem('offline-queue');
    return saved ? JSON.parse(saved) : [];
  });

  // Add action to queue
  const enqueue = (type: string, payload: any) => {
    const action: QueuedAction = {
      id: crypto.randomUUID(),
      type,
      payload,
      timestamp: Date.now(),
      retries: 0,
    };
    setQueue(prev => {
      const next = [...prev, action];
      localStorage.setItem('offline-queue', JSON.stringify(next));
      return next;
    });
  };

  // Process queue when online
  const processQueue = async () => {
    const pending = [...queue];
    for (const action of pending) {
      try {
        await processAction(action);
        setQueue(prev => {
          const next = prev.filter(a => a.id !== action.id);
          localStorage.setItem('offline-queue', JSON.stringify(next));
          return next;
        });
      } catch {
        // Leave in queue for retry
        setQueue(prev =>
          prev.map(a => a.id === action.id ? { ...a, retries: a.retries + 1 } : a)
        );
      }
    }
  };

  // Auto-process when coming back online
  useEffect(() => {
    const handler = () => { if (queue.length > 0) processQueue(); };
    window.addEventListener('online', handler);
    return () => window.removeEventListener('online', handler);
  }, [queue]);

  return { queue, enqueue, processQueue, pendingCount: queue.length };
}
```

### Sync Status Indicator

```html
<!-- Small indicator showing sync status -->
<div class="inline-flex items-center gap-1.5 text-xs">
  <!-- Synced -->
  <span class="flex items-center gap-1 text-gray-400">
    <svg class="w-3.5 h-3.5 text-green-500" fill="currentColor" viewBox="0 0 20 20">
      <path d="M10 18a8 8 0 100-16 8 8 0 000 16zm3.707-9.293a1 1 0 00-1.414-1.414L9 10.586 7.707 9.293a1 1 0 00-1.414 1.414l2 2a1 1 0 001.414 0l4-4z"/>
    </svg>
    Saved
  </span>

  <!-- Syncing -->
  <span class="flex items-center gap-1 text-gray-400">
    <svg class="w-3.5 h-3.5 animate-spin text-blue-500" fill="none" viewBox="0 0 24 24">
      <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"/>
      <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8v4a4 4 0 00-4 4H4z"/>
    </svg>
    Syncing...
  </span>

  <!-- Pending (offline) -->
  <span class="flex items-center gap-1 text-amber-500">
    <svg class="w-3.5 h-3.5" fill="currentColor" viewBox="0 0 20 20">
      <path d="M10 18a8 8 0 100-16 8 8 0 000 16zm1-12a1 1 0 10-2 0v4a1 1 0 00.293.707l2.828 2.828a1 1 0 101.415-1.414L11 9.586V6z"/>
    </svg>
    3 changes pending
  </span>
</div>
```

---

## Sources

- [LogRocket: Multi-Step Form with React Hook Form and Zod](https://blog.logrocket.com/building-reusable-multi-step-form-react-hook-form-zod/)
- [Smashing Magazine: Inline Validation UX](https://www.smashingmagazine.com/2022/09/inline-validation-web-forms-ux/)
- [Baymard Institute: Inline Form Validation](https://baymard.com/blog/inline-form-validation)
- [MDN: aria-invalid attribute](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Reference/Attributes/aria-invalid)
- [Smashing Magazine: Accessible Form Validation](https://www.smashingmagazine.com/2023/02/guide-accessible-form-validation/)
- [web.dev: Sign in with a passkey through form autofill](https://web.dev/articles/passkey-form-autofill)
- [React.dev: useOptimistic](https://react.dev/reference/react/useOptimistic)
- [TanStack Query: Optimistic Updates](https://tanstack.com/query/latest/docs/framework/react/guides/optimistic-updates)
- [TanStack Virtual: Infinite Scroll](https://tanstack.com/virtual/latest/docs/framework/react/examples/infinite-scroll)
- [LogRocket: React Loading Skeleton](https://blog.logrocket.com/handling-react-loading-states-react-loading-skeleton/)
- [LogRocket: React Toast Libraries 2025](https://blog.logrocket.com/react-toast-libraries-compared-2025/)
- [Carbon Design System: Notifications](https://carbondesignsystem.com/patterns/notification-pattern/)
- [Fluent 2: Toast Usage](https://fluent2.microsoft.design/components/web/react/core/toast/usage)
- [MDN: Intl.NumberFormat](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/NumberFormat)
- [Zuko: Smart Defaults for Form UX](https://www.zuko.io/blog/how-to-use-defaults-to-optimize-your-form-ux)
- [Reform: Accessible Form Validation](https://www.reform.app/blog/accessible-form-validation-best-practices)
- [CoreUI: React Input Mask](https://coreui.io/react/docs/forms/input-mask/)
- [LogRocket: Progressive Image Loading](https://blog.logrocket.com/progressive-image-loading-react-tutorial/)
- [OnboardJS: React Onboarding Libraries 2026](https://onboardjs.com/blog/5-best-react-onboarding-libraries-in-2025-compared)
- [Eleken: Empty State UX](https://www.eleken.co/blog-posts/empty-state-ux)
- [MDN: Service Workers Offline Support](https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps/Guides/Offline_and_background_operation)
- [Shadcn Store: Error Pages](https://shadcnstore.com/blocks/application/error-pages)
- [React Router: Error Boundaries](https://reactrouter.com/how-to/error-boundary)
