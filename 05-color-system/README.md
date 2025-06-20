# 05. Color System - Mastering Material Design 3 Colors

## 🎯 Learning Objectives
After completing this module, you will be able to:
- Understand the Material Design 3 color system architecture
- Create custom color palettes from brand colors
- Implement dynamic color schemes
- Work with tonal palettes and color roles
- Create accessible color combinations
- Implement color variants for different states
- Handle light and dark theme color transitions

## 📚 Table of Contents
1. [Material Design 3 Color Architecture](#material-design-3-color-architecture)
2. [Understanding Color Roles](#understanding-color-roles)
3. [Tonal Palettes](#tonal-palettes)
4. [Creating Custom Color Schemes](#creating-custom-color-schemes)
5. [Dynamic Color Implementation](#dynamic-color-implementation)
6. [Color Accessibility](#color-accessibility)
7. [State-Based Color Variations](#state-based-color-variations)
8. [Advanced Color Techniques](#advanced-color-techniques)
9. [Practical Exercises](#practical-exercises)

---

## Material Design 3 Color Architecture

Material Design 3 introduces a sophisticated color system built around **color roles** rather than just primary/secondary colors. This system ensures better accessibility and more flexible theming.

### Color System Hierarchy

```
Brand Colors → Tonal Palettes → Color Roles → Component Usage
```

### Key Color Roles

#### **Primary Colors**
- `primary`: Main brand color for key components
- `on-primary`: Text/icons on primary backgrounds
- `primary-container`: Subdued primary backgrounds
- `on-primary-container`: Text/icons on primary containers

#### **Secondary Colors**
- `secondary`: Complementary accent color
- `on-secondary`: Text/icons on secondary backgrounds
- `secondary-container`: Subdued secondary backgrounds
- `on-secondary-container`: Text/icons on secondary containers

#### **Tertiary Colors**
- `tertiary`: Additional accent for contrast and balance
- `on-tertiary`: Text/icons on tertiary backgrounds
- `tertiary-container`: Subdued tertiary backgrounds
- `on-tertiary-container`: Text/icons on tertiary containers

#### **Surface Colors**
- `surface`: Default background for components
- `on-surface`: Primary text and icons
- `surface-variant`: Alternative background color
- `on-surface-variant`: Lower-emphasis text and icons

#### **Utility Colors**
- `background`: App background color
- `on-background`: Text/icons on app background
- `error`: Error state color
- `on-error`: Text/icons on error backgrounds
- `outline`: Border and divider color
- `outline-variant`: Lower-emphasis borders

---

## Understanding Color Roles

### Color Role Implementation in SCSS

```scss
@use '@angular/material' as mat;

// Define your base colors
$my-primary: #6750a4;
$my-secondary: #625b71;
$my-tertiary: #7d5260;

// Create color palette
$my-color-config: (
  primary: mat.define-palette($my-primary),
  secondary: mat.define-palette($my-secondary),
  tertiary: mat.define-palette($my-tertiary),
  theme-type: light,
);

// Apply theme
:root {
  @include mat.theme((
    color: $my-color-config,
    typography: Roboto,
    density: 0,
  ));
}
```

### Using Color Roles in Components

```scss
.my-card {
  background-color: var(--mat-sys-surface);
  color: var(--mat-sys-on-surface);
  border: 1px solid var(--mat-sys-outline);
  
  .header {
    background-color: var(--mat-sys-primary-container);
    color: var(--mat-sys-on-primary-container);
  }
  
  .accent-section {
    background-color: var(--mat-sys-secondary-container);
    color: var(--mat-sys-on-secondary-container);
  }
}
```

---

## Tonal Palettes

Material Design 3 generates 13 tones (0-100) for each color, providing a full range from very dark to very light.

### Understanding Tones

```scss
// Tonal palette example
$blue-tones: (
  0: #000000,    // Darkest
  10: #001f2a,
  20: #003544,
  30: #004d61,
  40: #006780,
  50: #0083a0,   // Base color
  60: #00a0c4,
  70: #00bce4,
  80: #5dd5f5,
  90: #b8eaff,
  95: #dbf4ff,
  99: #f6fbff,
  100: #ffffff   // Lightest
);
```

### Creating Custom Tonal Palettes

```scss
@use '@angular/material' as mat;

// Method 1: From a single color
$custom-palette: mat.define-palette(#1976d2);

// Method 2: Define specific tones
$custom-palette: (
  50: #e3f2fd,
  100: #bbdefb,
  200: #90caf9,
  300: #64b5f6,
  400: #42a5f5,
  500: #2196f3,  // Base
  600: #1e88e5,
  700: #1976d2,
  800: #1565c0,
  900: #0d47a1,
  contrast: (
    50: rgba(black, 0.87),
    100: rgba(black, 0.87),
    200: rgba(black, 0.87),
    300: rgba(black, 0.87),
    400: rgba(black, 0.87),
    500: white,
    600: white,
    700: white,
    800: white,
    900: white,
  )
);
```

---

## Creating Custom Color Schemes

### From Brand Colors

```scss
@use '@angular/material' as mat;

// Your brand colors
$brand-primary: #6366f1;     // Indigo
$brand-secondary: #10b981;   // Emerald
$brand-tertiary: #f59e0b;    // Amber

// Create comprehensive color scheme
$custom-theme: mat.define-theme((
  color: (
    theme-type: light,
    primary: mat.$indigo-palette,
    secondary: mat.$green-palette,
    tertiary: mat.$amber-palette,
  ),
));

// Apply theme
html {
  @include mat.theme($custom-theme);
}
```

### Using Material Theme Builder Colors

```scss
// Export from https://m3.material.io/theme-builder
$theme-colors: (
  primary: #6750a4,
  on-primary: #ffffff,
  primary-container: #e9ddff,
  on-primary-container: #22005d,
  
  secondary: #625b71,
  on-secondary: #ffffff,
  secondary-container: #e8def8,
  on-secondary-container: #1e192b,
  
  tertiary: #7e5260,
  on-tertiary: #ffffff,
  tertiary-container: #ffd9e3,
  on-tertiary-container: #31101d,
  
  error: #ba1a1a,
  on-error: #ffffff,
  error-container: #ffdad6,
  on-error-container: #410002,
  
  outline: #7a757f,
  background: #fffbff,
  on-background: #1c1b1e,
  surface: #fffbff,
  on-surface: #1c1b1e,
);

// Convert to Angular Material format
:root {
  @each $token, $value in $theme-colors {
    --mat-sys-#{$token}: #{$value};
  }
}
```

---

## Dynamic Color Implementation

### System Color Adaptation

```scss
// Respect user's system preferences
:root {
  color-scheme: light dark;
  
  @include mat.theme((
    color: (
      theme-type: light,
      primary: mat.$blue-palette,
    ),
  ));
}

// Dark mode overrides
@media (prefers-color-scheme: dark) {
  :root {
    @include mat.theme((
      color: (
        theme-type: dark,
        primary: mat.$blue-palette,
      ),
    ));
  }
}
```

### JavaScript-Based Theme Switching

```typescript
// theme.service.ts
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class ThemeService {
  private currentTheme: 'light' | 'dark' = 'light';

  toggleTheme(): void {
    this.currentTheme = this.currentTheme === 'light' ? 'dark' : 'light';
    document.documentElement.setAttribute('data-theme', this.currentTheme);
  }

  setTheme(theme: 'light' | 'dark'): void {
    this.currentTheme = theme;
    document.documentElement.setAttribute('data-theme', theme);
  }

  getCurrentTheme(): 'light' | 'dark' {
    return this.currentTheme;
  }
}
```

```scss
// Dynamic theme switching
:root {
  @include mat.theme((
    color: (
      theme-type: light,
      primary: mat.$blue-palette,
    ),
  ));
}

[data-theme="dark"] {
  @include mat.theme((
    color: (
      theme-type: dark,
      primary: mat.$blue-palette,
    ),
  ));
}
```

---

## Color Accessibility

### Contrast Requirements

```scss
// Ensure WCAG compliance
.accessible-component {
  // AA level: 4.5:1 for normal text, 3:1 for large text
  // AAA level: 7:1 for normal text, 4.5:1 for large text
  
  background: var(--mat-sys-surface);
  color: var(--mat-sys-on-surface);
  
  .emphasis {
    color: var(--mat-sys-primary);
    // Ensure this meets contrast requirements
  }
  
  .warning {
    background: var(--mat-sys-error-container);
    color: var(--mat-sys-on-error-container);
  }
}
```

### High Contrast Mode Support

```scss
@media (prefers-contrast: high) {
  :root {
    --mat-sys-outline: #000000;
    --mat-sys-outline-variant: #484848;
  }
  
  .high-contrast-borders {
    border-width: 2px;
    border-color: var(--mat-sys-outline);
  }
}
```

### Color Blindness Considerations

```scss
// Don't rely on color alone for information
.status-indicator {
  &.success {
    background: var(--mat-sys-primary-container);
    color: var(--mat-sys-on-primary-container);
    
    &::before {
      content: "✓"; // Visual indicator beyond color
    }
  }
  
  &.error {
    background: var(--mat-sys-error-container);
    color: var(--mat-sys-on-error-container);
    
    &::before {
      content: "⚠"; // Visual indicator beyond color
    }
  }
}
```

---

## State-Based Color Variations

### Interactive States

```scss
.interactive-element {
  background: var(--mat-sys-surface);
  color: var(--mat-sys-on-surface);
  transition: all 200ms ease;
  
  // Hover state
  &:hover {
    background: color-mix(
      in srgb,
      var(--mat-sys-surface) 92%,
      var(--mat-sys-on-surface) 8%
    );
  }
  
  // Active/pressed state
  &:active {
    background: color-mix(
      in srgb,
      var(--mat-sys-surface) 88%,
      var(--mat-sys-on-surface) 12%
    );
  }
  
  // Focus state
  &:focus-visible {
    outline: 2px solid var(--mat-sys-primary);
    outline-offset: 2px;
  }
  
  // Disabled state
  &:disabled {
    background: color-mix(
      in srgb,
      var(--mat-sys-surface) 88%,
      transparent 12%
    );
    color: color-mix(
      in srgb,
      var(--mat-sys-on-surface) 38%,
      transparent 62%
    );
  }
}
```

### State Layers

```scss
// Material Design 3 state layer system
.state-layer-component {
  position: relative;
  overflow: hidden;
  
  &::before {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: var(--mat-sys-on-surface);
    opacity: 0;
    transition: opacity 200ms ease;
    pointer-events: none;
  }
  
  &:hover::before {
    opacity: 0.08; // 8% hover state layer
  }
  
  &:focus-visible::before {
    opacity: 0.12; // 12% focus state layer
  }
  
  &:active::before {
    opacity: 0.16; // 16% pressed state layer
  }
}
```

---

## Advanced Color Techniques

### Color Mixing and Blending

```scss
// Modern CSS color mixing
.modern-color-mixing {
  // Mix two colors
  background: color-mix(
    in srgb,
    var(--mat-sys-primary) 80%,
    var(--mat-sys-secondary) 20%
  );
  
  // Create tinted versions
  .tinted-background {
    background: color-mix(
      in srgb,
      var(--mat-sys-surface) 95%,
      var(--mat-sys-primary) 5%
    );
  }
}
```

### Custom Color Functions

```scss
// SCSS functions for color manipulation
@function create-state-color($base-color, $overlay-color, $opacity) {
  @return color-mix(in srgb, $base-color (100% - $opacity), $overlay-color $opacity);
}

.custom-button {
  $base: var(--mat-sys-primary);
  $overlay: var(--mat-sys-on-primary);
  
  background: $base;
  
  &:hover {
    background: create-state-color($base, $overlay, 8%);
  }
  
  &:active {
    background: create-state-color($base, $overlay, 16%);
  }
}
```

### Gradient Support

```scss
.gradient-component {
  background: linear-gradient(
    135deg,
    var(--mat-sys-primary-container) 0%,
    var(--mat-sys-secondary-container) 100%
  );
  
  // Ensure text is readable on gradient
  color: var(--mat-sys-on-primary-container);
  
  // Add fallback for older browsers
  background-color: var(--mat-sys-primary-container);
}
```

---

## Practical Exercises

### Exercise 1: Custom Brand Color Scheme
Create a complete color scheme based on your brand colors:

1. Choose 3 brand colors (primary, secondary, tertiary)
2. Generate tonal palettes for each
3. Create light and dark theme variants
4. Test accessibility compliance
5. Implement theme switching

### Exercise 2: Component Color Integration
Build a card component that demonstrates:

1. Proper use of surface colors
2. Container color variants
3. Interactive state colors
4. Error state handling
5. High contrast mode support

### Exercise 3: Dynamic Color System
Implement a color system that:

1. Adapts to system preferences
2. Supports user-selected themes
3. Maintains accessibility standards
4. Provides smooth transitions
5. Includes color customization UI

### Exercise 4: Advanced Color Effects
Create components with:

1. Color mixing effects
2. Gradient backgrounds
3. State layer animations
4. Custom color functions
5. Brand color integration

---

## 🔗 Additional Resources

- [Material Design 3 Color System](https://m3.material.io/styles/color/overview)
- [Material Theme Builder](https://m3.material.io/theme-builder)
- [WCAG Color Contrast Guidelines](https://www.w3.org/WAI/WCAG21/Understanding/contrast-minimum.html)
- [Color Universal Design](https://jfly.uni-koeln.de/color/)
- [Angular Material Color Documentation](https://material.angular.dev/guide/theming)

---

## ✅ Module Completion Checklist

- [ ] Understand Material Design 3 color architecture
- [ ] Can create custom color palettes
- [ ] Implement dynamic color schemes
- [ ] Apply color roles appropriately
- [ ] Create accessible color combinations
- [ ] Handle interactive state colors
- [ ] Build theme switching functionality
- [ ] Complete all practical exercises

**Next Module**: [06. Typography - Working with Material Design 3 Text Styles](../06-typography/)
