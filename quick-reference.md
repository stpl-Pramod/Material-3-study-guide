# Angular Material 3 - Quick Reference Guide

## 🚀 Quick Start

### Installation
```bash
ng add @angular/material
```

### Basic Theme Setup
```scss
@use '@angular/material' as mat;

:root {
  @include mat.theme((
    color: mat.$azure-palette,
    typography: Roboto,
    density: 0,
  ));
}
```

---

## 🎨 Color Palettes

### Prebuilt Palettes
- `mat.$red-palette`
- `mat.$green-palette` 
- `mat.$blue-palette`
- `mat.$yellow-palette`
- `mat.$cyan-palette`
- `mat.$magenta-palette`
- `mat.$orange-palette`
- `mat.$chartreuse-palette`
- `mat.$spring-green-palette`
- `mat.$azure-palette`
- `mat.$violet-palette`
- `mat.$rose-palette`

### Custom Color Configuration
```scss
:root {
  @include mat.theme((
    color: (
      primary: mat.$blue-palette,
      secondary: mat.$green-palette,
      tertiary: mat.$orange-palette,
      theme-type: light,
    ),
    typography: Roboto,
    density: 0,
  ));
}
```

---

## 🎯 System Tokens

### Color Tokens
```scss
// Primary colors
--mat-sys-primary
--mat-sys-on-primary
--mat-sys-primary-container
--mat-sys-on-primary-container

// Surface colors
--mat-sys-surface
--mat-sys-on-surface
--mat-sys-background
--mat-sys-on-background

// Utility colors
--mat-sys-error
--mat-sys-outline
--mat-sys-outline-variant
```

### Typography Tokens
```scss
// Display (largest)
--mat-sys-display-large
--mat-sys-display-medium
--mat-sys-display-small

// Headlines
--mat-sys-headline-large
--mat-sys-headline-medium
--mat-sys-headline-small

// Titles
--mat-sys-title-large
--mat-sys-title-medium
--mat-sys-title-small

// Body text
--mat-sys-body-large
--mat-sys-body-medium
--mat-sys-body-small

// Labels
--mat-sys-label-large
--mat-sys-label-medium
--mat-sys-label-small
```

### Elevation Tokens
```scss
--mat-sys-level0  // No elevation
--mat-sys-level1  // Minimal
--mat-sys-level2  // Low
--mat-sys-level3  // Medium
--mat-sys-level4  // High
--mat-sys-level5  // Maximum
```

---

## 🔧 Common Patterns

### Component with System Tokens
```scss
.my-component {
  background: var(--mat-sys-surface);
  color: var(--mat-sys-on-surface);
  border: 1px solid var(--mat-sys-outline);
  border-radius: var(--mat-sys-corner-medium);
  box-shadow: var(--mat-sys-level1);
  font: var(--mat-sys-body-medium);
}
```

### Theme Overrides
```scss
:root {
  @include mat.theme((
    color: mat.$blue-palette,
    typography: Roboto,
    density: 0,
  ));
  
  @include mat.theme-overrides((
    primary: #1976d2,
    corner-large: 24px,
  ));
}
```

### Light/Dark Mode
```scss
:root {
  color-scheme: light dark;
  @include mat.theme((
    color: mat.$blue-palette,
    typography: Roboto,
    density: 0,
  ));
}

body {
  background: var(--mat-sys-background);
  color: var(--mat-sys-on-background);
}
```

### Scoped Themes
```scss
.special-section {
  @include mat.theme((
    color: mat.$orange-palette,
  ));
}
```

---

## 📦 Component Imports

### Essential Imports
```typescript
import { MatButtonModule } from '@angular/material/button';
import { MatCardModule } from '@angular/material/card';
import { MatIconModule } from '@angular/material/icon';
import { MatToolbarModule } from '@angular/material/toolbar';
import { MatFormFieldModule } from '@angular/material/form-field';
import { MatInputModule } from '@angular/material/input';
import { MatSelectModule } from '@angular/material/select';
```

### Component Usage
```html
<button mat-raised-button color="primary">Primary</button>
<button mat-stroked-button color="accent">Accent</button>
<button mat-flat-button color="warn">Warning</button>

<mat-card>
  <mat-card-header>
    <mat-card-title>Title</mat-card-title>
  </mat-card-header>
  <mat-card-content>Content</mat-card-content>
</mat-card>
```

---

## 🎛️ Density Configuration

```scss
density: 0   // Default
density: -1  // Compact
density: -2  // More compact
density: -3  // Very compact
density: -4  // Maximum compact
density: -5  // Ultra compact
```

---

## 🔍 Debugging

### Inspect Tokens in DevTools
1. Right-click element → Inspect
2. Go to Computed styles
3. Look for `--mat-sys-*` properties

### Common Issues
- Token not applied: Check spelling and existence
- Theme not inherited: Use proper selectors
- Colors not accessible: Test contrast ratios

---

## 📱 Responsive Considerations

### Breakpoint Variables
```scss
@media (max-width: 599px) {
  // Mobile
  density: -1;
}

@media (min-width: 600px) {
  // Tablet+
  density: 0;
}
```

---

## ♿ Accessibility

### High Contrast
```scss
@media (prefers-contrast: high) {
  @include mat.theme-overrides((
    outline: #000000,
    outline-variant: #666666,
  ));
}
```

### Reduced Motion
```scss
@media (prefers-reduced-motion: reduce) {
  * {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

---

## 🔗 Useful Links

- [Material Design 3 Guidelines](https://m3.material.io/)
- [Angular Material Docs](https://material.angular.dev/)
- [Material Theme Builder](https://m3.material.io/theme-builder)
- [Material Icons](https://fonts.google.com/icons)
- [WAVE Accessibility Checker](https://wave.webaim.org/)

---

## 📋 Checklist for New Projects

- [ ] Install Angular Material with `ng add`
- [ ] Choose appropriate color palette
- [ ] Configure typography and density
- [ ] Set up light/dark mode support
- [ ] Create reusable theme utilities
- [ ] Test accessibility and contrast
- [ ] Document theme decisions
- [ ] Set up development workflow
