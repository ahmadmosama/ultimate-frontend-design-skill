# Mobile Design Patterns Reference

## 1. iOS Human Interface Guidelines Specifics

### Safe Areas (exact pt values, iPhone with Dynamic Island)

| Element            | Height (pt) | Notes                              |
|--------------------|-------------|------------------------------------|
| Status bar         | 54          | Dynamic Island devices; 44 on notch; 20 on legacy |
| Navigation bar     | 44          | Content area only; 96 total with status bar |
| Large title nav    | 96          | Collapsed = 44                     |
| Tab bar            | 49          | Content area; 83 total with home indicator |
| Home indicator     | 34          | Bottom safe area inset             |
| Search bar         | 36          | Inside nav bar adds to height      |
| Toolbar            | 44          | Same as nav bar height             |

**Safe area insets (iPhone 15 Pro):** top: 59pt, bottom: 34pt, left: 0, right: 0

### Dynamic Type Scale

| Style              | Default Size (pt) | Weight    | Leading (pt) |
|--------------------|-------------------|-----------|---------------|
| Large Title        | 34                | Regular   | 41            |
| Title 1            | 28                | Regular   | 34            |
| Title 2            | 22                | Regular   | 28            |
| Title 3            | 20                | Regular   | 25            |
| Headline           | 17                | Semibold  | 22            |
| Body               | 17                | Regular   | 22            |
| Callout            | 16                | Regular   | 21            |
| Subheadline        | 15                | Regular   | 20            |
| Footnote           | 13                | Regular   | 18            |
| Caption 1          | 12                | Regular   | 16            |
| Caption 2          | 11                | Regular   | 13            |

**Implementation:** Always use semantic styles, never hardcoded sizes. Sizes scale from xSmall (accessibility) to AX5 (largest accessibility).

### SF Symbols

- 5,000+ symbols, 9 weights, 3 scales (small/medium/large)
- Rendering modes: monochrome, hierarchical (depth), palette (custom colors), multicolor (fixed)
- Always pair with text using same weight: `UIImage.SymbolConfiguration(textStyle: .body)`
- Minimum tap target: 44x44pt even if symbol is smaller

### Gesture Patterns

| Gesture       | System Use                | Custom Use Rules                     |
|---------------|---------------------------|--------------------------------------|
| Swipe back    | Navigation pop            | Never override on leading edge       |
| Pull to refresh | UIRefreshControl        | Only on scrollable content           |
| Long press    | Context menu              | Always provide alternative access    |
| Pinch         | Zoom                      | Only for zoomable content            |
| Edge swipe    | System gestures           | Avoid custom gestures near edges     |

### Haptic Types (UIFeedbackGenerator)

| Type            | Use Case                          |
|-----------------|-----------------------------------|
| `.light`        | Selection changes                 |
| `.medium`       | Snapping to position              |
| `.heavy`        | Significant actions               |
| `.success`      | Task completed                    |
| `.warning`      | Caution before action             |
| `.error`        | Action failed                     |
| `.selection`    | Scrolling through picker values   |

## 2. Material 3 / Android

### Navigation Patterns

| Pattern       | When to Use                           | Max Items |
|---------------|---------------------------------------|-----------|
| Bottom nav    | 3-5 top-level destinations            | 5         |
| Nav drawer    | 5+ destinations or deep hierarchy     | No limit  |
| Nav rail      | Tablet/foldable, always visible       | 7         |
| Top app bar   | Page title, actions, back nav         | 2-3 icons |

### Shape System (rounded corners)

| Token         | dp Value | Use Case                        |
|---------------|----------|---------------------------------|
| Extra-small   | 4dp      | Buttons, small chips            |
| Small         | 8dp      | Cards, text fields              |
| Medium        | 12dp     | FAB, dialogs                    |
| Large         | 16dp     | Bottom sheets (top corners)     |
| Extra-large   | 28dp     | Large bottom sheets, nav drawer |
| Full          | 50%      | Circular badges, avatars        |

### Material 3 Type Scale

| Role           | Size (sp) | Weight   | Tracking (sp) |
|----------------|-----------|----------|---------------|
| Display Large  | 57        | 400      | -0.25         |
| Display Medium | 45        | 400      | 0             |
| Display Small  | 36        | 400      | 0             |
| Headline Large | 32        | 400      | 0             |
| Headline Medium| 28        | 400      | 0             |
| Headline Small | 24        | 400      | 0             |
| Title Large    | 22        | 400      | 0             |
| Title Medium   | 16        | 500      | 0.15          |
| Title Small    | 14        | 500      | 0.1           |
| Body Large     | 16        | 400      | 0.5           |
| Body Medium    | 14        | 400      | 0.25          |
| Body Small     | 12        | 400      | 0.4           |
| Label Large    | 14        | 500      | 0.1           |
| Label Medium   | 12        | 500      | 0.5           |
| Label Small    | 11        | 500      | 0.5           |

### Dynamic Color (Material You)

Extracts tonal palettes from user's wallpaper. Five key colors: primary, secondary, tertiary, neutral, neutral-variant. Each generates a 13-tone palette. Falls back to baseline theme on older devices.

### Elevation System

| Level | dp  | Use Case                          |
|-------|-----|-----------------------------------|
| 0     | 0   | Background                        |
| 1     | 1   | Cards, navigation bar             |
| 2     | 3   | FAB, elevated buttons             |
| 3     | 6   | Snackbar, bottom sheet            |
| 4     | 8   | Navigation drawer, modal side sheet |
| 5     | 12  | Dialog, modal bottom sheet        |

In M3, elevation is expressed via tonal surface color overlay rather than shadow alone.

## 3. Cross-Platform Strategy

### Share Across Platforms

- **Spacing scale:** 4px base unit (4, 8, 12, 16, 24, 32, 48, 64)
- **Color tokens:** semantic names (`--text-primary`), not platform names
- **Type scale:** map to platform equivalents (Body = iOS Body = M3 Body Large)
- **Icon set:** use a cross-platform icon library, or SVGs
- **Animation durations:** enter 250ms, exit 200ms, micro 150ms

### Platform-Specialize (Never Share)

| Concern       | iOS                        | Android                    |
|---------------|----------------------------|----------------------------|
| Navigation    | Tab bar bottom, push/pop   | Bottom nav, top app bar    |
| Back action   | Swipe from left edge       | System back button/gesture |
| Action sheets | UIAlertController bottom   | Bottom sheet or dialog     |
| Selection     | Checkmark trailing         | Checkbox leading           |
| Switches      | Rounded, green/gray        | Thumb on track, M3 colors  |
| Haptics       | Taptic Engine API          | VibrationEffect API        |

## 4. React Native Patterns

### Platform Detection

```jsx
import { Platform, StyleSheet } from 'react-native';

const styles = StyleSheet.create({
  header: {
    paddingTop: Platform.OS === 'ios' ? 44 : 0,
    ...Platform.select({
      ios: { shadowColor: '#000', shadowOffset: { width: 0, height: 2 }, shadowOpacity: 0.1 },
      android: { elevation: 4 },
    }),
  },
});
```

### Safe Area Handling

```jsx
import { SafeAreaProvider, useSafeAreaInsets } from 'react-native-safe-area-context';

function Screen() {
  const insets = useSafeAreaInsets();
  return (
    <View style={{ paddingTop: insets.top, paddingBottom: insets.bottom }}>
      {/* Content respects notch, home indicator, status bar */}
    </View>
  );
}

// Wrap app root
export default () => (
  <SafeAreaProvider>
    <App />
  </SafeAreaProvider>
);
```

### Responsive Sizing

```jsx
import { Dimensions, useWindowDimensions } from 'react-native';

function ResponsiveGrid() {
  const { width } = useWindowDimensions(); // reactive to rotation
  const columns = width >= 768 ? 3 : width >= 480 ? 2 : 1;
  const itemWidth = (width - 16 * (columns + 1)) / columns;
  // ...
}
```

## 5. Flutter Patterns

### Platform-Adaptive Widgets

```dart
import 'dart:io' show Platform;
import 'package:flutter/material.dart';
import 'package:flutter/cupertino.dart';

class AdaptiveButton extends StatelessWidget {
  final String label;
  final VoidCallback onPressed;
  const AdaptiveButton({required this.label, required this.onPressed});

  @override
  Widget build(BuildContext context) {
    if (Platform.isIOS) {
      return CupertinoButton.filled(onPressed: onPressed, child: Text(label));
    }
    return ElevatedButton(onPressed: onPressed, child: Text(label));
  }
}
```

### MediaQuery for Responsive Layout

```dart
Widget build(BuildContext context) {
  final size = MediaQuery.of(context).size;
  final padding = MediaQuery.of(context).padding; // safe area insets
  final textScale = MediaQuery.of(context).textScaleFactor;

  return Padding(
    padding: EdgeInsets.only(top: padding.top, bottom: padding.bottom),
    child: size.width > 600
        ? TwoColumnLayout()
        : SingleColumnLayout(),
  );
}
```

### MaterialApp vs CupertinoApp

- `MaterialApp`: Material 3 widgets, `ThemeData`, scaffold/app bar pattern
- `CupertinoApp`: iOS widgets, `CupertinoThemeData`, nav bar/tab pattern
- For cross-platform: use `MaterialApp` with platform checks for iOS-specific widgets

## 6. Thumb Zone Design

### Reach Zones (right-handed, standard phone)

```
┌──────────────────────┐
│   HARD TO REACH      │  ← top 20% of screen
│                      │
│   OK / STRETCH       │  ← middle 30%
│                      │
│   EASY / NATURAL     │  ← bottom 50%
│                      │
│  [  TAB  BAR  ]      │  ← prime real estate
└──────────────────────┘
```

### Rules

- **Primary actions** in bottom 50% of screen (FAB, tab bar, bottom CTA)
- **Destructive actions** in hard-to-reach zones (top right)
- **Bottom sheets** over modals for action selection (closer to thumb)
- **Tab bar** (3-5 items) beats hamburger menu for discoverability
- **Floating action button** at bottom-right (M3) or bottom-center

### Navigation Decision Tree

| Items | Mobile Pattern                    |
|-------|-----------------------------------|
| 2-3   | Segmented control or tabs         |
| 3-5   | Bottom tab bar                    |
| 5-8   | Bottom tab bar + "More" tab       |
| 8+    | Drawer / hamburger                |

## 7. Mobile Form Patterns

### Keyboard-Aware Scrolling

```jsx
// React Native
import { KeyboardAvoidingView, Platform } from 'react-native';

<KeyboardAvoidingView
  behavior={Platform.OS === 'ios' ? 'padding' : 'height'}
  keyboardVerticalOffset={Platform.OS === 'ios' ? 88 : 0}
>
  <ScrollView keyboardShouldPersistTaps="handled">
    {/* form fields */}
  </ScrollView>
</KeyboardAvoidingView>
```

### Input Types for Mobile Keyboards

| Data           | HTML `type`/`inputmode` | iOS Keyboard        | Android Input Type     |
|----------------|------------------------|---------------------|------------------------|
| Email          | `type="email"`         | Email layout        | `textEmailAddress`     |
| Phone          | `type="tel"`           | Phone pad           | `phone`                |
| Number         | `inputmode="numeric"`  | Number pad          | `number`               |
| Decimal        | `inputmode="decimal"`  | Number + decimal    | `numberDecimal`        |
| URL            | `type="url"`           | URL layout          | `textUri`              |
| Search         | `type="search"`        | Search + Go button  | `text` with search action |
| Password       | `type="password"`      | Secure text         | `textPassword`         |
| OTP            | `inputmode="numeric" autocomplete="one-time-code"` | OTP autofill | SMS autofill |

### Autofill Attributes

```html
<input type="text" autocomplete="name" />
<input type="email" autocomplete="email" />
<input type="tel" autocomplete="tel" />
<input type="text" autocomplete="street-address" />
<input type="text" autocomplete="postal-code" inputmode="numeric" />
<input type="text" autocomplete="cc-number" inputmode="numeric" />
<input type="password" autocomplete="new-password" />    <!-- sign up -->
<input type="password" autocomplete="current-password" /> <!-- login -->
<input type="text" autocomplete="one-time-code" inputmode="numeric" /> <!-- OTP -->
```

### Biometric Auth Pattern

1. Offer biometric after first successful password login
2. Store auth token in secure enclave (Keychain / Keystore), not the password
3. Fallback: password entry, never lock user out
4. Re-prompt biometric on sensitive actions (payment, settings change)

## 8. Mobile Performance

### Image Optimization

- **Format:** WebP (30% smaller than JPEG), AVIF where supported
- **Sizing:** serve 2x for retina, 3x for xxhdpi. Never send desktop-size images
- **Lazy load:** `loading="lazy"` for web; use `onViewableItemsChanged` in RN FlatList
- **Placeholder:** show blurred thumbnail (blurhash) or solid color while loading
- **CDN:** use `?w=400&q=80` params to request appropriate size from image CDN

### List Virtualization

```jsx
// React Native: FlatList (only renders visible items)
<FlatList
  data={items}
  renderItem={({ item }) => <ItemCard item={item} />}
  keyExtractor={(item) => item.id}
  initialNumToRender={10}
  maxToRenderPerBatch={5}
  windowSize={5}                    // render 5 screens worth
  removeClippedSubviews={true}      // Android: unmount off-screen views
  getItemLayout={(data, index) => ({ // skip measurement if fixed height
    length: 80, offset: 80 * index, index
  })}
/>
```

```kotlin
// Android: RecyclerView with DiffUtil
class ItemAdapter : ListAdapter<Item, ItemViewHolder>(ItemDiffCallback()) {
    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ItemViewHolder {
        val view = LayoutInflater.from(parent.context).inflate(R.layout.item, parent, false)
        return ItemViewHolder(view)
    }
    override fun onBindViewHolder(holder: ItemViewHolder, position: Int) {
        holder.bind(getItem(position))
    }
}
```

### 60fps Scroll Checklist

1. **Avoid layout thrashing:** no `measure()` calls during scroll callbacks
2. **Use `transform` for animations**, not `top`/`left`/`width`/`height`
3. **Flatten view hierarchy:** deep nesting = slow layout passes
4. **Offload work:** heavy computation in `requestAnimationFrame` or background thread
5. **Image decoding:** decode off main thread (`UIImage` in background queue, Glide/Coil on Android)
6. **React Native specific:** use `useNativeDriver: true` for Animated, avoid `setState` in scroll handler
7. **Shadow removal:** complex shadows on scrolling items kill performance; simplify or remove during scroll
