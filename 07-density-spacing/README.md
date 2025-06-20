# 07. Density & Spacing - Controlling Component Density

## 🎯 Learning Objectives
After completing this module, you will be able to:
- Understand Material Design 3 density principles
- Configure density levels for different use cases
- Implement responsive density patterns
- Use spacing tokens effectively
- Create custom density configurations
- Optimize layouts for different screen sizes and contexts
- Balance density with accessibility requirements

## 📚 Table of Contents
1. [Understanding Density in Material Design 3](#understanding-density-in-material-design-3)
2. [Density Levels and Configuration](#density-levels-and-configuration)
3. [Spacing System Overview](#spacing-system-overview)
4. [Implementing Density Themes](#implementing-density-themes)
5. [Responsive Density Patterns](#responsive-density-patterns)
6. [Component-Specific Density](#component-specific-density)
7. [Accessibility Considerations](#accessibility-considerations)
8. [Advanced Density Techniques](#advanced-density-techniques)
9. [Practical Exercises](#practical-exercises)

---

## Understanding Density in Material Design 3

Density in Material Design refers to how tightly packed UI elements are within a given space. It affects padding, margins, component sizes, and overall layout efficiency.

### Density Principles

1. **Functional Density**: More content visible at once
2. **Comfortable Density**: Balance between content and breathing room
3. **Spacious Density**: Emphasis on ease of use and visual hierarchy

### When to Use Different Densities

- **High Density (-4 to -5)**: Data-heavy applications, desktop dashboards
- **Medium Density (-1 to -3)**: Most mobile and web applications
- **Low Density (0 to 1)**: Accessibility-focused interfaces, large displays

---

## Density Levels and Configuration

### Density Scale

```scss
// Material Design 3 density scale
$density-scale: (
  0: 0,      // Default/Comfortable
  -1: -1,    // Slightly dense
  -2: -2,    // Dense
  -3: -3,    // Very dense
  -4: -4,    // Maximum density
  -5: -5,    // Ultra dense (use sparingly)
);
```

### Basic Density Configuration

```scss
@use '@angular/material' as mat;

// Default density theme
:root {
  @include mat.theme((
    color: mat.$blue-palette,
    typography: Roboto,
    density: 0, // Comfortable density
  ));
}

// Dense theme variant
.dense-theme {
  @include mat.theme((
    color: mat.$blue-palette,
    typography: Roboto,
    density: -2, // Dense layout
  ));
}
```

### Component-Level Density

```scss
@use '@angular/material' as mat;

// Apply density to specific components
.form-section {
  @include mat.form-field-density(-1);
  @include mat.button-density(-1);
  @include mat.select-density(-1);
}

.data-table {
  @include mat.table-density(-2);
  @include mat.paginator-density(-2);
}
```

---

## Spacing System Overview

### Material Design 3 Spacing Scale

```scss
// Base spacing unit (typically 8px)
$base-spacing: 8px;

// Spacing scale
$spacing-scale: (
  0: 0,
  1: #{$base-spacing * 0.5},    // 4px
  2: #{$base-spacing * 1},      // 8px
  3: #{$base-spacing * 1.5},    // 12px
  4: #{$base-spacing * 2},      // 16px
  5: #{$base-spacing * 2.5},    // 20px
  6: #{$base-spacing * 3},      // 24px
  7: #{$base-spacing * 3.5},    // 28px
  8: #{$base-spacing * 4},      // 32px
  9: #{$base-spacing * 4.5},    // 36px
  10: #{$base-spacing * 5},     // 40px
  12: #{$base-spacing * 6},     // 48px
  16: #{$base-spacing * 8},     // 64px
  20: #{$base-spacing * 10},    // 80px
  24: #{$base-spacing * 12},    // 96px
);
```

### Spacing Tokens

```scss
// Spacing tokens
:root {
  --mat-sys-space-0: 0;
  --mat-sys-space-1: 4px;
  --mat-sys-space-2: 8px;
  --mat-sys-space-3: 12px;
  --mat-sys-space-4: 16px;
  --mat-sys-space-5: 20px;
  --mat-sys-space-6: 24px;
  --mat-sys-space-8: 32px;
  --mat-sys-space-10: 40px;
  --mat-sys-space-12: 48px;
  --mat-sys-space-16: 64px;
  --mat-sys-space-20: 80px;
  --mat-sys-space-24: 96px;
}
```

---

## Implementing Density Themes

### Multi-Density Theme System

```scss
@use '@angular/material' as mat;

// Create density theme mixins
@mixin comfortable-density-theme() {
  @include mat.theme((
    color: mat.$blue-palette,
    typography: Roboto,
    density: 0,
  ));
}

@mixin compact-density-theme() {
  @include mat.theme((
    color: mat.$blue-palette,
    typography: Roboto,
    density: -2,
  ));
}

@mixin dense-density-theme() {
  @include mat.theme((
    color: mat.$blue-palette,
    typography: Roboto,
    density: -4,
  ));
}

// Apply density themes
:root {
  @include comfortable-density-theme();
}

.compact-mode {
  @include compact-density-theme();
}

.dense-mode {
  @include dense-density-theme();
}
```

### Dynamic Density Switching

```typescript
// Density service
import { Injectable } from '@angular/core';

export type DensityLevel = 'comfortable' | 'compact' | 'dense';

@Injectable({
  providedIn: 'root'
})
export class DensityService {
  private currentDensity: DensityLevel = 'comfortable';

  setDensity(density: DensityLevel): void {
    this.currentDensity = density;
    document.body.className = document.body.className
      .replace(/\b(comfortable|compact|dense)-mode\b/g, '')
      .trim();
    
    if (density !== 'comfortable') {
      document.body.classList.add(`${density}-mode`);
    }
  }

  getDensity(): DensityLevel {
    return this.currentDensity;
  }
}
```

```typescript
// Density toggle component
import { Component } from '@angular/core';
import { DensityService, DensityLevel } from './density.service';

@Component({
  selector: 'app-density-toggle',
  template: `
    <mat-button-toggle-group 
      [value]="currentDensity" 
      (change)="onDensityChange($event)">
      <mat-button-toggle value="comfortable">Comfortable</mat-button-toggle>
      <mat-button-toggle value="compact">Compact</mat-button-toggle>
      <mat-button-toggle value="dense">Dense</mat-button-toggle>
    </mat-button-toggle-group>
  `
})
export class DensityToggleComponent {
  currentDensity: DensityLevel;

  constructor(private densityService: DensityService) {
    this.currentDensity = this.densityService.getDensity();
  }

  onDensityChange(event: any): void {
    this.densityService.setDensity(event.value);
    this.currentDensity = event.value;
  }
}
```

---

## Responsive Density Patterns

### Breakpoint-Based Density

```scss
// Responsive density implementation
.responsive-density {
  // Mobile: Comfortable density for touch targets
  @include mat.theme((
    density: 0,
  ));
  
  // Tablet: Slightly more compact
  @media (min-width: 768px) {
    @include mat.theme((
      density: -1,
    ));
  }
  
  // Desktop: Compact for efficiency
  @media (min-width: 1200px) {
    @include mat.theme((
      density: -2,
    ));
  }
  
  // Large desktop: Dense for data-heavy interfaces
  @media (min-width: 1600px) {
    @include mat.theme((
      density: -3,
    ));
  }
}
```

### Container Query Density

```scss
// Container-based density (when supported)
.adaptive-container {
  container-type: inline-size;
  
  // Default density
  @include mat.theme((density: 0));
  
  // Compact when container is medium
  @container (min-width: 500px) {
    @include mat.theme((density: -1));
  }
  
  // Dense when container is large
  @container (min-width: 800px) {
    @include mat.theme((density: -2));
  }
}
```

---

## Component-Specific Density

### Form Component Density

```scss
.form-density-examples {
  // Default form density
  .standard-form {
    @include mat.form-field-density(0);
    @include mat.button-density(0);
    
    .mat-form-field {
      margin-bottom: var(--mat-sys-space-4);
    }
  }
  
  // Compact form density
  .compact-form {
    @include mat.form-field-density(-1);
    @include mat.button-density(-1);
    
    .mat-form-field {
      margin-bottom: var(--mat-sys-space-2);
    }
  }
  
  // Dense form density
  .dense-form {
    @include mat.form-field-density(-2);
    @include mat.button-density(-2);
    
    .mat-form-field {
      margin-bottom: var(--mat-sys-space-1);
    }
  }
}
```

### Data Table Density

```scss
.table-density-examples {
  // Comfortable table
  .comfortable-table {
    @include mat.table-density(0);
    
    .mat-header-cell,
    .mat-cell {
      padding: var(--mat-sys-space-4) var(--mat-sys-space-6);
    }
  }
  
  // Compact table
  .compact-table {
    @include mat.table-density(-1);
    
    .mat-header-cell,
    .mat-cell {
      padding: var(--mat-sys-space-3) var(--mat-sys-space-4);
    }
  }
  
  // Dense table
  .dense-table {
    @include mat.table-density(-2);
    
    .mat-header-cell,
    .mat-cell {
      padding: var(--mat-sys-space-2) var(--mat-sys-space-3);
    }
  }
}
```

### Navigation Density

```scss
.navigation-density {
  // Toolbar density
  .toolbar-comfortable {
    @include mat.toolbar-density(0);
    
    .mat-toolbar {
      padding: 0 var(--mat-sys-space-6);
    }
  }
  
  .toolbar-compact {
    @include mat.toolbar-density(-1);
    
    .mat-toolbar {
      padding: 0 var(--mat-sys-space-4);
    }
  }
  
  // Side navigation density
  .sidenav-comfortable {
    .mat-list-item {
      height: 48px;
      padding: 0 var(--mat-sys-space-4);
    }
  }
  
  .sidenav-compact {
    .mat-list-item {
      height: 40px;
      padding: 0 var(--mat-sys-space-3);
    }
  }
  
  .sidenav-dense {
    .mat-list-item {
      height: 32px;
      padding: 0 var(--mat-sys-space-2);
    }
  }
}
```

---

## Accessibility Considerations

### Touch Target Requirements

```scss
// Ensure minimum touch target sizes
.accessible-density {
  // Minimum 44px touch targets (iOS)
  // Minimum 48px touch targets (Android)
  
  .touch-target {
    min-height: 48px;
    min-width: 48px;
    
    // On small screens, ensure adequate spacing
    @media (max-width: 480px) {
      min-height: 44px;
      min-width: 44px;
      margin: var(--mat-sys-space-1);
    }
  }
  
  // Dense mode touch target adjustments
  .dense-mode & {
    .touch-target {
      // Maintain minimum size even in dense mode
      min-height: 40px;
      min-width: 40px;
      
      // Add more spacing to compensate
      margin: var(--mat-sys-space-2);
    }
  }
}
```

### Focus and Keyboard Navigation

```scss
.accessible-focus-density {
  // Ensure focus indicators are visible in dense layouts
  .dense-mode & {
    .mat-button:focus,
    .mat-form-field:focus-within {
      outline: 2px solid var(--mat-sys-primary);
      outline-offset: 2px;
    }
    
    // Increase spacing around focused elements
    .mat-button:focus {
      margin: var(--mat-sys-space-2);
    }
  }
}
```

### User Preference Adaptations

```scss
// Respect user preferences for spacing
@media (prefers-reduced-motion: reduce) {
  // Provide more space for users who prefer reduced motion
  :root {
    --density-adjustment: 1;
  }
}

// Large text preferences
@media (prefers-font-size: large) {
  // Reduce density when user prefers larger text
  :root {
    @include mat.theme((density: max(var(--current-density, 0), -1)));
  }
}
```

---

## Advanced Density Techniques

### Custom Density Calculations

```scss
// Custom density calculation function
@function calculate-density-value($base-value, $density-level) {
  $density-factor: 1 + ($density-level * 0.125); // 12.5% reduction per level
  @return $base-value * $density-factor;
}

// Apply custom density
.custom-density-component {
  $base-padding: 16px;
  $base-margin: 8px;
  
  // Default density
  padding: $base-padding;
  margin: $base-margin;
  
  // Density level -2
  .dense-mode & {
    padding: calculate-density-value($base-padding, -2); // 12px
    margin: calculate-density-value($base-margin, -2);   // 6px
  }
}
```

### Density Transitions

```scss
.smooth-density-transitions {
  transition: 
    padding 300ms cubic-bezier(0.4, 0, 0.2, 1),
    margin 300ms cubic-bezier(0.4, 0, 0.2, 1),
    height 300ms cubic-bezier(0.4, 0, 0.2, 1);
    
  // Smooth density changes
  &.changing-density {
    transition-duration: 200ms;
  }
}
```

### Context-Aware Density

```scss
// Density based on content context
.content-aware-density {
  // Dense for data-heavy content
  &[data-content-type="data"] {
    @include mat.theme((density: -2));
  }
  
  // Comfortable for reading content
  &[data-content-type="reading"] {
    @include mat.theme((density: 0));
  }
  
  // Compact for navigation
  &[data-content-type="navigation"] {
    @include mat.theme((density: -1));
  }
}
```

---

## Practical Exercises

### Exercise 1: Responsive Density System
Create a responsive density system that:

1. Adapts density based on screen size
2. Provides user controls for density preferences
3. Maintains accessibility standards
4. Stores user preferences
5. Smoothly transitions between density levels

### Exercise 2: Data Dashboard Density
Build a data dashboard with:

1. Multiple density levels for different sections
2. Context-aware density (tables vs. cards vs. charts)
3. User customization options
4. Responsive behavior
5. Performance optimization

### Exercise 3: Form Density Optimization
Develop a complex form with:

1. Adaptive density based on form complexity
2. Section-specific density levels
3. Mobile-optimized density
4. Accessibility compliance
5. User experience testing

### Exercise 4: Navigation Density Patterns
Create a navigation system with:

1. Collapsible dense navigation
2. Breadcrumb density adaptation
3. Menu density customization
4. Touch-friendly adjustments
5. Keyboard navigation support

---

## 🔗 Additional Resources

- [Material Design Density Guidelines](https://m3.material.io/foundations/adaptive-design/density)
- [Accessibility Touch Target Guidelines](https://www.w3.org/WAI/WCAG21/Understanding/target-size.html)
- [Angular Material Density Documentation](https://material.angular.dev/guide/density)
- [Responsive Design Patterns](https://web.dev/responsive-web-design-basics/)

---

## ✅ Module Completion Checklist

- [ ] Understand Material Design 3 density principles
- [ ] Can configure density levels effectively
- [ ] Implement responsive density patterns
- [ ] Use spacing tokens appropriately
- [ ] Create accessible dense layouts
- [ ] Build context-aware density systems
- [ ] Optimize density for different devices
- [ ] Complete all practical exercises

**Next Module**: [08. Advanced Theming - Multiple Themes and Customization](../08-advanced-theming/)
