# 11. Responsive Design - Adapting Themes for Different Screen Sizes

## 🎯 Learning Objectives
After completing this module, you will be able to:
- Create responsive Material Design 3 themes
- Implement breakpoint-based theme variations
- Build adaptive components that respond to screen size changes
- Optimize themes for mobile, tablet, and desktop experiences
- Create fluid layouts with responsive theming
- Implement container queries for component-level responsiveness
- Handle orientation changes and viewport adaptations

## 📚 Table of Contents
1. [Responsive Theming Principles](#responsive-theming-principles)
2. [Breakpoint-Based Theme Variations](#breakpoint-based-theme-variations)
3. [Adaptive Typography and Spacing](#adaptive-typography-and-spacing)
4. [Responsive Component Theming](#responsive-component-theming)
5. [Mobile-First Theme Design](#mobile-first-theme-design)
6. [Container Queries and Element Queries](#container-queries-and-element-queries)
7. [Device-Specific Optimizations](#device-specific-optimizations)
8. [Performance Considerations](#performance-considerations)
9. [Practical Exercises](#practical-exercises)

---

## Responsive Theming Principles

### Material Design 3 Responsive Guidelines

Material Design 3 emphasizes adaptive interfaces that work across all screen sizes while maintaining consistency and usability.

#### Key Principles:
1. **Flexible Grid Systems**: Use fluid grids that adapt to screen sizes
2. **Adaptive Components**: Components that resize and reflow naturally
3. **Contextual Density**: Adjust density based on screen real estate
4. **Touch-Friendly Interactions**: Ensure adequate touch targets
5. **Readable Typography**: Scale text appropriately for viewing distance

### Responsive Breakpoint Strategy

```scss
// Material Design breakpoints
$breakpoints: (
  xs: 0,
  sm: 600px,
  md: 960px,
  lg: 1280px,
  xl: 1920px
);

// Breakpoint mixins
@mixin xs-only {
  @media (max-width: 599px) { @content; }
}

@mixin sm-up {
  @media (min-width: 600px) { @content; }
}

@mixin sm-only {
  @media (min-width: 600px) and (max-width: 959px) { @content; }
}

@mixin md-up {
  @media (min-width: 960px) { @content; }
}

@mixin md-only {
  @media (min-width: 960px) and (max-width: 1279px) { @content; }
}

@mixin lg-up {
  @media (min-width: 1280px) { @content; }
}

@mixin lg-only {
  @media (min-width: 1280px) and (max-width: 1919px) { @content; }
}

@mixin xl-up {
  @media (min-width: 1920px) { @content; }
}
```

---

## Breakpoint-Based Theme Variations

### Responsive Theme Configuration

```scss
@use '@angular/material' as mat;

// Base theme configuration
$base-theme: mat.define-theme((
  color: (
    theme-type: light,
    primary: mat.$blue-palette,
    secondary: mat.$green-palette,
  ),
  typography: (
    font-family: 'Inter, sans-serif',
  ),
  density: 0,
));

// Apply base theme
:root {
  @include mat.theme($base-theme);
}

// Mobile theme adjustments
@include xs-only {
  :root {
    @include mat.theme((
      density: 0, // Comfortable density for touch
      typography: (
        font-size: 16px, // Larger base font for mobile
      ),
    ));
  }
}

// Tablet theme adjustments
@include sm-only {
  :root {
    @include mat.theme((
      density: -1, // Slightly more compact
    ));
  }
}

// Desktop theme adjustments
@include md-up {
  :root {
    @include mat.theme((
      density: -2, // More compact for efficiency
      typography: (
        font-size: 14px, // Smaller font for desktop viewing
      ),
    ));
  }
}

// Large desktop theme adjustments
@include xl-up {
  :root {
    @include mat.theme((
      density: -3, // Maximum density for large screens
    ));
  }
}
```

### Responsive Color Adaptations

```scss
// Responsive color system
.responsive-colors {
  // Mobile: Higher contrast for outdoor viewing
  @include xs-only {
    --mat-sys-primary: #1565c0; // Darker blue
    --mat-sys-outline: #424242; // Stronger borders
  }
  
  // Tablet: Balanced colors
  @include sm-up {
    --mat-sys-primary: #1976d2; // Standard blue
    --mat-sys-outline: #757575; // Medium borders
  }
  
  // Desktop: Refined colors
  @include lg-up {
    --mat-sys-primary: #2196f3; // Lighter blue
    --mat-sys-outline: #e0e0e0; // Subtle borders
  }
}
```

---

## Adaptive Typography and Spacing

### Fluid Typography System

```scss
// Fluid typography using clamp()
:root {
  // Display typography scales fluidly
  --mat-sys-display-large-size: clamp(2.5rem, 5vw, 3.5rem);
  --mat-sys-display-medium-size: clamp(2rem, 4vw, 2.8rem);
  --mat-sys-display-small-size: clamp(1.8rem, 3.5vw, 2.25rem);
  
  // Headline typography
  --mat-sys-headline-large-size: clamp(1.5rem, 3vw, 2rem);
  --mat-sys-headline-medium-size: clamp(1.3rem, 2.5vw, 1.75rem);
  --mat-sys-headline-small-size: clamp(1.2rem, 2vw, 1.5rem);
  
  // Body typography
  --mat-sys-body-large-size: clamp(0.9rem, 1.5vw, 1rem);
  --mat-sys-body-medium-size: clamp(0.8rem, 1.2vw, 0.875rem);
  
  // Line height adjustments
  --mat-sys-body-large-line-height: clamp(1.4, 1.5, 1.7);
  --mat-sys-body-medium-line-height: clamp(1.3, 1.4, 1.6);
}

// Breakpoint-specific typography overrides
@include xs-only {
  :root {
    // Larger fonts for mobile readability
    --mat-sys-body-large-size: 1rem;
    --mat-sys-body-medium-size: 0.875rem;
    --mat-sys-body-large-line-height: 1.6;
  }
}

@include lg-up {
  :root {
    // Optimized fonts for desktop
    --mat-sys-body-large-size: 0.875rem;
    --mat-sys-body-medium-size: 0.75rem;
    --mat-sys-body-large-line-height: 1.5;
  }
}
```

### Responsive Spacing System

```scss
// Fluid spacing system
:root {
  // Base spacing scales with viewport
  --spacing-xs: clamp(0.25rem, 0.5vw, 0.5rem);
  --spacing-sm: clamp(0.5rem, 1vw, 0.75rem);
  --spacing-md: clamp(0.75rem, 1.5vw, 1rem);
  --spacing-lg: clamp(1rem, 2vw, 1.5rem);
  --spacing-xl: clamp(1.5rem, 3vw, 2rem);
  --spacing-2xl: clamp(2rem, 4vw, 3rem);
  
  // Component-specific spacing
  --button-padding: clamp(0.5rem, 1vw, 1rem) clamp(1rem, 2vw, 1.5rem);
  --card-padding: clamp(1rem, 2vw, 1.5rem);
  --section-margin: clamp(1.5rem, 3vw, 2.5rem);
}

// Breakpoint-specific spacing
@include xs-only {
  :root {
    --section-margin: 1rem;
    --card-padding: 1rem;
  }
}

@include sm-up {
  :root {
    --section-margin: 1.5rem;
    --card-padding: 1.25rem;
  }
}

@include lg-up {
  :root {
    --section-margin: 2rem;
    --card-padding: 1.5rem;
  }
}
```

---

## Responsive Component Theming

### Adaptive Form Components

```scss
.responsive-forms {
  .mat-mdc-form-field {
    // Mobile: Full width, larger touch targets
    @include xs-only {
      width: 100%;
      
      .mat-mdc-text-field-wrapper {
        min-height: 56px; // Larger touch target
      }
      
      .mat-mdc-form-field-label {
        font-size: 1rem; // Larger labels
      }
    }
    
    // Tablet: Comfortable sizing
    @include sm-only {
      .mat-mdc-text-field-wrapper {
        min-height: 48px;
      }
    }
    
    // Desktop: Compact, efficient
    @include md-up {
      .mat-mdc-text-field-wrapper {
        min-height: 40px;
      }
      
      .mat-mdc-form-field-label {
        font-size: 0.875rem;
      }
    }
  }
  
  // Button responsiveness
  .mat-mdc-button {
    @include xs-only {
      min-height: 48px; // Touch-friendly
      font-size: 1rem;
      padding: 0 1.5rem;
    }
    
    @include sm-up {
      min-height: 40px;
      font-size: 0.875rem;
      padding: 0 1rem;
    }
    
    @include lg-up {
      min-height: 36px;
      font-size: 0.75rem;
      padding: 0 0.75rem;
    }
  }
}
```

### Responsive Navigation

```scss
.responsive-navigation {
  // Mobile: Bottom navigation
  @include xs-only {
    .mat-toolbar {
      position: fixed;
      bottom: 0;
      left: 0;
      right: 0;
      top: auto;
      height: 64px;
    }
    
    .mat-sidenav-container {
      margin-bottom: 64px;
    }
    
    .mat-sidenav {
      width: 100vw;
      max-width: none;
    }
  }
  
  // Tablet: Collapsible side navigation
  @include sm-only {
    .mat-sidenav {
      width: 280px;
    }
    
    .mat-toolbar {
      height: 64px;
    }
  }
  
  // Desktop: Persistent navigation
  @include md-up {
    .mat-sidenav {
      width: 240px;
      
      &.compact {
        width: 64px;
        
        .nav-text {
          display: none;
        }
      }
    }
    
    .mat-toolbar {
      height: 56px;
    }
  }
  
  // Large desktop: Expanded navigation
  @include xl-up {
    .mat-sidenav {
      width: 320px;
    }
  }
}
```

### Responsive Data Tables

```scss
.responsive-tables {
  .mat-mdc-table {
    // Mobile: Card-based layout
    @include xs-only {
      display: block;
      
      .mat-mdc-header-row {
        display: none;
      }
      
      .mat-mdc-row {
        display: block;
        margin-bottom: 1rem;
        padding: 1rem;
        border: 1px solid var(--mat-sys-outline-variant);
        border-radius: 8px;
        
        .mat-mdc-cell {
          display: block;
          padding: 0.5rem 0;
          border: none;
          
          &:before {
            content: attr(data-label) ': ';
            font-weight: 600;
            color: var(--mat-sys-on-surface-variant);
          }
        }
      }
    }
    
    // Tablet: Simplified table
    @include sm-only {
      font-size: 0.875rem;
      
      .mat-mdc-cell,
      .mat-mdc-header-cell {
        padding: 12px 8px;
      }
      
      // Hide less important columns
      .optional-column {
        display: none;
      }
    }
    
    // Desktop: Full table
    @include md-up {
      .mat-mdc-cell,
      .mat-mdc-header-cell {
        padding: 16px;
      }
      
      .optional-column {
        display: table-cell;
      }
    }
  }
}
```

---

## Mobile-First Theme Design

### Mobile-First Approach

```scss
// Start with mobile-first base styles
.mobile-first-theme {
  // Base styles (mobile)
  --spacing-unit: 1rem;
  --border-radius: 8px;
  --elevation-low: 0 2px 4px rgba(0,0,0,0.1);
  
  .mat-mdc-card {
    padding: var(--spacing-unit);
    border-radius: var(--border-radius);
    box-shadow: var(--elevation-low);
    margin-bottom: var(--spacing-unit);
  }
  
  // Progressive enhancement for larger screens
  @include sm-up {
    --spacing-unit: 1.25rem;
    --border-radius: 12px;
    
    .mat-mdc-card {
      max-width: 600px;
      margin: 0 auto var(--spacing-unit);
    }
  }
  
  @include md-up {
    --spacing-unit: 1.5rem;
    --border-radius: 16px;
    --elevation-low: 0 4px 8px rgba(0,0,0,0.12);
    
    .mat-mdc-card {
      max-width: none;
      margin: var(--spacing-unit);
    }
  }
  
  @include lg-up {
    --spacing-unit: 2rem;
    
    .mat-mdc-card {
      display: inline-block;
      width: calc(50% - var(--spacing-unit));
      vertical-align: top;
    }
  }
}
```

### Touch-Optimized Interactions

```scss
.touch-optimized {
  // Ensure minimum touch target sizes
  .touch-target {
    min-height: 44px;
    min-width: 44px;
    
    @include sm-up {
      min-height: 40px;
      min-width: 40px;
    }
  }
  
  // Larger tap areas on mobile
  @include xs-only {
    .mat-mdc-icon-button {
      width: 48px;
      height: 48px;
    }
    
    .mat-mdc-button {
      min-height: 48px;
      padding: 0 24px;
    }
    
    .mat-mdc-tab {
      min-width: 72px;
      height: 48px;
    }
  }
  
  // Hover effects only on non-touch devices
  @media (hover: hover) {
    .mat-mdc-button:hover {
      background: var(--mat-sys-primary-container);
    }
    
    .mat-mdc-card:hover {
      box-shadow: 0 8px 16px rgba(0,0,0,0.15);
    }
  }
}
```

---

## Container Queries and Element Queries

### Container Query Implementation

```scss
// Container queries for component-level responsiveness
.container-responsive {
  container-type: inline-size;
  
  .adaptive-component {
    // Default layout
    display: block;
    
    // When container is small
    @container (max-width: 300px) {
      .component-title {
        font-size: 1rem;
      }
      
      .component-actions {
        flex-direction: column;
        gap: 0.5rem;
      }
    }
    
    // When container is medium
    @container (min-width: 300px) and (max-width: 600px) {
      display: flex;
      align-items: center;
      
      .component-title {
        font-size: 1.25rem;
      }
      
      .component-actions {
        margin-left: auto;
      }
    }
    
    // When container is large
    @container (min-width: 600px) {
      .component-title {
        font-size: 1.5rem;
      }
      
      .component-actions {
        gap: 1rem;
      }
    }
  }
}
```

### Element Query Polyfill

```typescript
// Element query service for older browsers
import { Injectable, ElementRef } from '@angular/core';

export interface ElementQueryConfig {
  breakpoints: { [key: string]: number };
  element: ElementRef<HTMLElement>;
  callback: (breakpoint: string, width: number) => void;
}

@Injectable({
  providedIn: 'root'
})
export class ElementQueryService {
  private observers = new Map<HTMLElement, ResizeObserver>();
  private configs = new Map<HTMLElement, ElementQueryConfig>();

  observe(config: ElementQueryConfig): void {
    const element = config.element.nativeElement;
    
    if (this.observers.has(element)) {
      this.unobserve(element);
    }

    this.configs.set(element, config);

    const observer = new ResizeObserver(entries => {
      for (const entry of entries) {
        this.handleResize(entry.target as HTMLElement, entry.contentRect.width);
      }
    });

    observer.observe(element);
    this.observers.set(element, observer);
    
    // Initial check
    this.handleResize(element, element.offsetWidth);
  }

  unobserve(element: HTMLElement): void {
    const observer = this.observers.get(element);
    if (observer) {
      observer.disconnect();
      this.observers.delete(element);
      this.configs.delete(element);
    }
  }

  private handleResize(element: HTMLElement, width: number): void {
    const config = this.configs.get(element);
    if (!config) return;

    const breakpoints = Object.entries(config.breakpoints)
      .sort(([,a], [,b]) => b - a);

    let currentBreakpoint = 'default';
    
    for (const [name, minWidth] of breakpoints) {
      if (width >= minWidth) {
        currentBreakpoint = name;
        break;
      }
    }

    // Update CSS classes
    Object.keys(config.breakpoints).forEach(bp => {
      element.classList.remove(`eq-${bp}`);
    });
    element.classList.add(`eq-${currentBreakpoint}`);

    config.callback(currentBreakpoint, width);
  }
}
```

---

## Device-Specific Optimizations

### Device Detection and Optimization

```typescript
// Device detection service
import { Injectable } from '@angular/core';

export interface DeviceInfo {
  isMobile: boolean;
  isTablet: boolean;
  isDesktop: boolean;
  hasTouch: boolean;
  screenSize: 'xs' | 'sm' | 'md' | 'lg' | 'xl';
  orientation: 'portrait' | 'landscape';
}

@Injectable({
  providedIn: 'root'
})
export class DeviceService {
  private deviceInfo: DeviceInfo;

  constructor() {
    this.deviceInfo = this.detectDevice();
    this.watchOrientationChange();
  }

  getDeviceInfo(): DeviceInfo {
    return { ...this.deviceInfo };
  }

  private detectDevice(): DeviceInfo {
    const width = window.innerWidth;
    const hasTouch = 'ontouchstart' in window || navigator.maxTouchPoints > 0;
    
    return {
      isMobile: width < 768 && hasTouch,
      isTablet: width >= 768 && width < 1024 && hasTouch,
      isDesktop: width >= 1024 || !hasTouch,
      hasTouch,
      screenSize: this.getScreenSize(width),
      orientation: width > window.innerHeight ? 'landscape' : 'portrait'
    };
  }

  private getScreenSize(width: number): 'xs' | 'sm' | 'md' | 'lg' | 'xl' {
    if (width < 600) return 'xs';
    if (width < 960) return 'sm';
    if (width < 1280) return 'md';
    if (width < 1920) return 'lg';
    return 'xl';
  }

  private watchOrientationChange(): void {
    window.addEventListener('orientationchange', () => {
      setTimeout(() => {
        this.deviceInfo = this.detectDevice();
        document.body.className = this.getBodyClasses();
      }, 100);
    });
    
    // Initial body classes
    document.body.className = this.getBodyClasses();
  }

  private getBodyClasses(): string {
    const classes = [
      `screen-${this.deviceInfo.screenSize}`,
      `orientation-${this.deviceInfo.orientation}`
    ];
    
    if (this.deviceInfo.isMobile) classes.push('device-mobile');
    if (this.deviceInfo.isTablet) classes.push('device-tablet');
    if (this.deviceInfo.isDesktop) classes.push('device-desktop');
    if (this.deviceInfo.hasTouch) classes.push('has-touch');
    
    return classes.join(' ');
  }
}
```

### Responsive Image Handling

```scss
// Responsive image system
.responsive-images {
  .mat-mdc-card {
    .card-image {
      width: 100%;
      height: auto;
      object-fit: cover;
      
      // Mobile: Larger images for impact
      @include xs-only {
        height: 200px;
      }
      
      // Tablet: Balanced proportions
      @include sm-only {
        height: 150px;
      }
      
      // Desktop: Smaller, more efficient
      @include md-up {
        height: 120px;
      }
    }
    
    // Responsive image grid
    .image-grid {
      display: grid;
      gap: 1rem;
      
      @include xs-only {
        grid-template-columns: 1fr;
      }
      
      @include sm-up {
        grid-template-columns: repeat(2, 1fr);
      }
      
      @include md-up {
        grid-template-columns: repeat(3, 1fr);
      }
      
      @include lg-up {
        grid-template-columns: repeat(4, 1fr);
      }
    }
  }
}
```

---

## Performance Considerations

### Responsive Loading Strategies

```typescript
// Lazy loading service for responsive content
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class ResponsiveLoadingService {
  private intersectionObserver?: IntersectionObserver;

  constructor() {
    this.setupIntersectionObserver();
  }

  private setupIntersectionObserver(): void {
    if ('IntersectionObserver' in window) {
      this.intersectionObserver = new IntersectionObserver(
        (entries) => {
          entries.forEach(entry => {
            if (entry.isIntersecting) {
              this.loadResponsiveContent(entry.target as HTMLElement);
            }
          });
        },
        { threshold: 0.1 }
      );
    }
  }

  observeElement(element: HTMLElement): void {
    if (this.intersectionObserver) {
      this.intersectionObserver.observe(element);
    }
  }

  private loadResponsiveContent(element: HTMLElement): void {
    // Load appropriate content based on screen size
    const screenSize = this.getCurrentScreenSize();
    const contentSrc = element.dataset[`src${screenSize}`];
    
    if (contentSrc && element.tagName === 'IMG') {
      (element as HTMLImageElement).src = contentSrc;
    }
    
    // Remove from observation
    if (this.intersectionObserver) {
      this.intersectionObserver.unobserve(element);
    }
  }

  private getCurrentScreenSize(): string {
    const width = window.innerWidth;
    if (width < 600) return 'Xs';
    if (width < 960) return 'Sm';
    if (width < 1280) return 'Md';
    if (width < 1920) return 'Lg';
    return 'Xl';
  }
}
```

### CSS Optimization for Responsive Themes

```scss
// Optimize CSS for responsive performance
@media (max-width: 599px) {
  // Critical mobile styles only
  .mat-mdc-card {
    padding: 1rem;
    margin: 0.5rem;
  }
}

// Use CSS containment for performance
.responsive-container {
  contain: layout style paint;
}

// Optimize animations for mobile
@media (max-width: 599px) {
  * {
    animation-duration: 0.2s !important;
    transition-duration: 0.2s !important;
  }
}

// Reduce motion on mobile to save battery
@media (prefers-reduced-motion: reduce) {
  * {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}
```

---

## Practical Exercises

### Exercise 1: Responsive Theme System
Build a complete responsive theme system:

1. Create breakpoint-based theme variations
2. Implement fluid typography and spacing
3. Build adaptive component layouts
4. Add device-specific optimizations
5. Test across multiple devices

### Exercise 2: Mobile-First Design System
Develop a mobile-first design system:

1. Start with mobile base styles
2. Progressively enhance for larger screens
3. Optimize touch interactions
4. Implement responsive navigation
5. Create mobile-specific components

### Exercise 3: Container Query Implementation
Build container query based layouts:

1. Implement container queries where supported
2. Create fallback solutions for older browsers
3. Build self-contained responsive components
4. Test container-based responsiveness
5. Optimize performance

### Exercise 4: Advanced Responsive Features
Create advanced responsive features:

1. Build responsive data visualizations
2. Implement adaptive image loading
3. Create responsive form layouts
4. Build responsive navigation patterns
5. Add responsive accessibility features

---

## 🔗 Additional Resources

- [Material Design Responsive Layout](https://m3.material.io/foundations/adaptive-design/overview)
- [CSS Container Queries](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Container_Queries)
- [Responsive Web Design Basics](https://web.dev/responsive-web-design-basics/)
- [Mobile-First Design](https://www.lukew.com/ff/entry.asp?933)

---

## ✅ Module Completion Checklist

- [ ] Understand responsive theming principles
- [ ] Can create breakpoint-based theme variations
- [ ] Implement adaptive typography and spacing
- [ ] Build responsive component themes
- [ ] Master mobile-first design approach
- [ ] Use container queries effectively
- [ ] Optimize for different devices
- [ ] Complete all practical exercises

**Next Module**: [12. Best Practices - Production-ready Patterns and Techniques](../12-best-practices/)
