# 08. Advanced Theming - Multiple Themes and Customization

## 🎯 Learning Objectives
After completing this module, you will be able to:
- Create and manage multiple theme configurations
- Implement dynamic theme switching
- Build complex theme hierarchies and inheritance
- Customize component themes at granular levels
- Create theme-aware components and services
- Optimize theme performance and loading
- Implement advanced theming patterns and architectures

## 📚 Table of Contents
1. [Multi-Theme Architecture](#multi-theme-architecture)
2. [Dynamic Theme Switching](#dynamic-theme-switching)
3. [Theme Inheritance and Hierarchies](#theme-inheritance-and-hierarchies)
4. [Component-Level Theme Customization](#component-level-theme-customization)
5. [Theme-Aware Components](#theme-aware-components)
6. [Theme Performance Optimization](#theme-performance-optimization)
7. [Advanced Theming Patterns](#advanced-theming-patterns)
8. [Theme Testing and Validation](#theme-testing-and-validation)
9. [Practical Exercises](#practical-exercises)

---

## Multi-Theme Architecture

### Setting Up Multiple Themes

```scss
@use '@angular/material' as mat;

// Define multiple color palettes
$light-theme: mat.define-theme((
  color: (
    theme-type: light,
    primary: mat.$blue-palette,
    secondary: mat.$green-palette,
    tertiary: mat.$orange-palette,
  ),
  typography: (
    font-family: 'Inter, sans-serif',
  ),
  density: 0,
));

$dark-theme: mat.define-theme((
  color: (
    theme-type: dark,
    primary: mat.$blue-palette,
    secondary: mat.$green-palette,
    tertiary: mat.$orange-palette,
  ),
  typography: (
    font-family: 'Inter, sans-serif',
  ),
  density: 0,
));

$high-contrast-theme: mat.define-theme((
  color: (
    theme-type: light,
    primary: mat.$grey-palette,
    secondary: mat.$grey-palette,
    tertiary: mat.$grey-palette,
  ),
  typography: (
    font-family: 'Inter, sans-serif',
    font-weight: 600,
  ),
  density: 0,
));
```

### Theme Application Strategy

```scss
// Base theme (light)
:root {
  @include mat.theme($light-theme);
}

// Dark theme
[data-theme="dark"] {
  @include mat.theme($dark-theme);
}

// High contrast theme
[data-theme="high-contrast"] {
  @include mat.theme($high-contrast-theme);
}

// System preference based themes
@media (prefers-color-scheme: dark) {
  :root:not([data-theme]) {
    @include mat.theme($dark-theme);
  }
}

@media (prefers-contrast: high) {
  :root:not([data-theme]) {
    @include mat.theme($high-contrast-theme);
  }
}
```

---

## Dynamic Theme Switching

### Theme Service Implementation

```typescript
import { Injectable, Inject } from '@angular/core';
import { DOCUMENT } from '@angular/common';
import { BehaviorSubject, Observable } from 'rxjs';

export type ThemeType = 'light' | 'dark' | 'high-contrast' | 'auto';

export interface ThemeConfig {
  name: string;
  displayName: string;
  type: ThemeType;
  cssClass?: string;
  customProperties?: Record<string, string>;
}

@Injectable({
  providedIn: 'root'
})
export class ThemeService {
  private readonly THEME_KEY = 'app-theme';
  private readonly THEMES: ThemeConfig[] = [
    { name: 'light', displayName: 'Light', type: 'light' },
    { name: 'dark', displayName: 'Dark', type: 'dark', cssClass: 'dark-theme' },
    { name: 'high-contrast', displayName: 'High Contrast', type: 'high-contrast', cssClass: 'high-contrast-theme' },
    { name: 'auto', displayName: 'System', type: 'auto' }
  ];

  private currentThemeSubject = new BehaviorSubject<ThemeConfig>(this.THEMES[0]);
  public currentTheme$ = this.currentThemeSubject.asObservable();

  constructor(@Inject(DOCUMENT) private document: Document) {
    this.initializeTheme();
    this.watchSystemPreferences();
  }

  private initializeTheme(): void {
    const savedTheme = localStorage.getItem(this.THEME_KEY);
    if (savedTheme) {
      const theme = this.THEMES.find(t => t.name === savedTheme);
      if (theme) {
        this.setTheme(theme.name);
        return;
      }
    }
    
    // Default to system preference
    this.setTheme('auto');
  }

  private watchSystemPreferences(): void {
    // Watch for system dark mode changes
    const darkModeQuery = window.matchMedia('(prefers-color-scheme: dark)');
    darkModeQuery.addEventListener('change', () => {
      if (this.currentThemeSubject.value.type === 'auto') {
        this.applySystemTheme();
      }
    });

    // Watch for high contrast changes
    const highContrastQuery = window.matchMedia('(prefers-contrast: high)');
    highContrastQuery.addEventListener('change', () => {
      if (this.currentThemeSubject.value.type === 'auto') {
        this.applySystemTheme();
      }
    });
  }

  setTheme(themeName: string): void {
    const theme = this.THEMES.find(t => t.name === themeName);
    if (!theme) {
      console.warn(`Theme "${themeName}" not found`);
      return;
    }

    // Remove existing theme classes
    this.document.body.classList.remove(...this.THEMES.map(t => t.cssClass).filter(Boolean) as string[]);
    this.document.documentElement.removeAttribute('data-theme');

    if (theme.type === 'auto') {
      this.applySystemTheme();
    } else {
      this.applyTheme(theme);
    }

    // Save preference
    localStorage.setItem(this.THEME_KEY, theme.name);
    this.currentThemeSubject.next(theme);
  }

  private applySystemTheme(): void {
    const prefersDark = window.matchMedia('(prefers-color-scheme: dark)').matches;
    const prefersHighContrast = window.matchMedia('(prefers-contrast: high)').matches;

    let systemTheme: ThemeConfig;
    if (prefersHighContrast) {
      systemTheme = this.THEMES.find(t => t.type === 'high-contrast')!;
    } else if (prefersDark) {
      systemTheme = this.THEMES.find(t => t.type === 'dark')!;
    } else {
      systemTheme = this.THEMES.find(t => t.type === 'light')!;
    }

    this.applyTheme(systemTheme);
  }

  private applyTheme(theme: ThemeConfig): void {
    // Apply CSS class
    if (theme.cssClass) {
      this.document.body.classList.add(theme.cssClass);
    }

    // Apply data attribute
    if (theme.type !== 'light') {
      this.document.documentElement.setAttribute('data-theme', theme.type);
    }

    // Apply custom properties
    if (theme.customProperties) {
      Object.entries(theme.customProperties).forEach(([property, value]) => {
        this.document.documentElement.style.setProperty(property, value);
      });
    }
  }

  getAvailableThemes(): ThemeConfig[] {
    return [...this.THEMES];
  }

  getCurrentTheme(): ThemeConfig {
    return this.currentThemeSubject.value;
  }
}
```

### Theme Switcher Component

```typescript
import { Component, OnInit, OnDestroy } from '@angular/core';
import { ThemeService, ThemeConfig } from './theme.service';
import { Subject, takeUntil } from 'rxjs';

@Component({
  selector: 'app-theme-switcher',
  template: `
    <mat-form-field appearance="outline">
      <mat-label>Theme</mat-label>
      <mat-select [value]="currentTheme?.name" (selectionChange)="onThemeChange($event)">
        <mat-option *ngFor="let theme of availableThemes" [value]="theme.name">
          <mat-icon>{{getThemeIcon(theme.type)}}</mat-icon>
          {{theme.displayName}}
        </mat-option>
      </mat-select>
    </mat-form-field>
  `,
  styles: [`
    .theme-switcher {
      display: flex;
      align-items: center;
      gap: 8px;
    }
    
    mat-icon {
      margin-right: 8px;
    }
  `]
})
export class ThemeSwitcherComponent implements OnInit, OnDestroy {
  availableThemes: ThemeConfig[] = [];
  currentTheme: ThemeConfig | null = null;
  private destroy$ = new Subject<void>();

  constructor(private themeService: ThemeService) {}

  ngOnInit(): void {
    this.availableThemes = this.themeService.getAvailableThemes();
    
    this.themeService.currentTheme$
      .pipe(takeUntil(this.destroy$))
      .subscribe(theme => {
        this.currentTheme = theme;
      });
  }

  ngOnDestroy(): void {
    this.destroy$.next();
    this.destroy$.complete();
  }

  onThemeChange(event: any): void {
    this.themeService.setTheme(event.value);
  }

  getThemeIcon(themeType: string): string {
    const icons = {
      'light': 'light_mode',
      'dark': 'dark_mode',
      'high-contrast': 'contrast',
      'auto': 'auto_mode'
    };
    return icons[themeType] || 'palette';
  }
}
```

---

## Theme Inheritance and Hierarchies

### Hierarchical Theme Structure

```scss
// Base theme
$base-theme: mat.define-theme((
  color: (
    theme-type: light,
    primary: mat.$blue-palette,
  ),
  typography: (
    font-family: 'Inter, sans-serif',
  ),
  density: 0,
));

// Brand theme extends base
$brand-theme: mat.define-theme((
  color: (
    theme-type: light,
    primary: mat.$indigo-palette,
    secondary: mat.$pink-palette,
  ),
  typography: (
    font-family: 'Poppins, sans-serif',
    font-weight: 500,
  ),
  density: -1,
));

// Admin theme extends brand
$admin-theme: mat.define-theme((
  color: (
    theme-type: light,
    primary: mat.$grey-palette,
    secondary: mat.$blue-grey-palette,
  ),
  typography: (
    font-family: 'JetBrains Mono, monospace',
  ),
  density: -2,
));
```

### Theme Inheritance Implementation

```scss
// Theme inheritance mixin
@mixin extend-theme($base-theme, $overrides) {
  // Apply base theme
  @include mat.theme($base-theme);
  
  // Apply overrides
  @if map-has-key($overrides, 'color') {
    @include mat.color-variants-overrides(map-get($overrides, 'color'));
  }
  
  @if map-has-key($overrides, 'typography') {
    @include mat.typography-hierarchy-overrides(map-get($overrides, 'typography'));
  }
  
  @if map-has-key($overrides, 'density') {
    @include mat.density-overrides(map-get($overrides, 'density'));
  }
}

// Usage
.specialized-section {
  @include extend-theme($base-theme, (
    color: (
      primary: #ff5722,
      secondary: #4caf50,
    ),
    density: -1,
  ));
}
```

---

## Component-Level Theme Customization

### Individual Component Theming

```scss
// Button-specific theme customization
.custom-button-theme {
  @include mat.button-theme((
    color: (
      primary: #e91e63,
      secondary: #9c27b0,
    ),
    typography: (
      button: mat.define-typography-level(
        $font-family: 'Roboto Condensed',
        $font-weight: 600,
        $font-size: 14px,
        $line-height: 1,
        $letter-spacing: 0.5px,
      ),
    ),
    density: -1,
  ));
}

// Card-specific theme customization
.custom-card-theme {
  @include mat.card-theme((
    color: (
      background: #f5f5f5,
      foreground: #333333,
    ),
    typography: (
      title: mat.define-typography-level(
        $font-family: 'Playfair Display',
        $font-weight: 700,
        $font-size: 24px,
      ),
    ),
  ));
}
```

### Component Theme Variants

```scss
// Create theme variants for specific components
.primary-card {
  @include mat.card-color((
    background: var(--mat-sys-primary-container),
    foreground: var(--mat-sys-on-primary-container),
  ));
}

.secondary-card {
  @include mat.card-color((
    background: var(--mat-sys-secondary-container),
    foreground: var(--mat-sys-on-secondary-container),
  ));
}

.success-button {
  @include mat.button-color((
    primary: #4caf50,
    'on-primary': white,
  ));
}

.warning-button {
  @include mat.button-color((
    primary: #ff9800,
    'on-primary': white,
  ));
}
```

---

## Theme-Aware Components

### Theme-Reactive Component

```typescript
import { Component, OnInit, OnDestroy, ChangeDetectorRef } from '@angular/core';
import { ThemeService } from './theme.service';
import { Subject, takeUntil } from 'rxjs';

@Component({
  selector: 'app-theme-aware-card',
  template: `
    <mat-card [class]="cardClass">
      <mat-card-header>
        <mat-card-title>{{title}}</mat-card-title>
        <mat-card-subtitle>Theme: {{currentThemeName}}</mat-card-subtitle>
      </mat-card-header>
      <mat-card-content>
        <p>This card adapts its appearance based on the current theme.</p>
        <div class="theme-indicators">
          <mat-chip [style.background-color]="primaryColor">Primary</mat-chip>
          <mat-chip [style.background-color]="secondaryColor">Secondary</mat-chip>
        </div>
      </mat-card-content>
    </mat-card>
  `,
  styles: [`
    .theme-indicators {
      display: flex;
      gap: 8px;
      margin-top: 16px;
    }
    
    mat-chip {
      color: white;
    }
  `]
})
export class ThemeAwareCardComponent implements OnInit, OnDestroy {
  title = 'Theme-Aware Component';
  currentThemeName = '';
  cardClass = '';
  primaryColor = '';
  secondaryColor = '';
  
  private destroy$ = new Subject<void>();

  constructor(
    private themeService: ThemeService,
    private cdr: ChangeDetectorRef
  ) {}

  ngOnInit(): void {
    this.themeService.currentTheme$
      .pipe(takeUntil(this.destroy$))
      .subscribe(theme => {
        this.currentThemeName = theme.displayName;
        this.updateThemeProperties(theme);
        this.cdr.detectChanges();
      });
  }

  ngOnDestroy(): void {
    this.destroy$.next();
    this.destroy$.complete();
  }

  private updateThemeProperties(theme: any): void {
    // Get computed styles to read CSS custom properties
    const styles = getComputedStyle(document.documentElement);
    
    this.primaryColor = styles.getPropertyValue('--mat-sys-primary').trim();
    this.secondaryColor = styles.getPropertyValue('--mat-sys-secondary').trim();
    
    // Set appropriate card class based on theme
    this.cardClass = `theme-${theme.type}-card`;
  }
}
```

### Theme Context Service

```typescript
import { Injectable } from '@angular/core';
import { BehaviorSubject, Observable } from 'rxjs';

export interface ThemeContext {
  isDark: boolean;
  isHighContrast: boolean;
  primaryColor: string;
  secondaryColor: string;
  backgroundColor: string;
  textColor: string;
}

@Injectable({
  providedIn: 'root'
})
export class ThemeContextService {
  private contextSubject = new BehaviorSubject<ThemeContext>(this.getInitialContext());
  public context$ = this.contextSubject.asObservable();

  constructor() {
    // Watch for theme changes
    this.watchThemeChanges();
  }

  private getInitialContext(): ThemeContext {
    return this.calculateContext();
  }

  private calculateContext(): ThemeContext {
    const styles = getComputedStyle(document.documentElement);
    const dataTheme = document.documentElement.getAttribute('data-theme');
    
    return {
      isDark: dataTheme === 'dark' || window.matchMedia('(prefers-color-scheme: dark)').matches,
      isHighContrast: dataTheme === 'high-contrast' || window.matchMedia('(prefers-contrast: high)').matches,
      primaryColor: styles.getPropertyValue('--mat-sys-primary').trim(),
      secondaryColor: styles.getPropertyValue('--mat-sys-secondary').trim(),
      backgroundColor: styles.getPropertyValue('--mat-sys-background').trim(),
      textColor: styles.getPropertyValue('--mat-sys-on-background').trim(),
    };
  }

  private watchThemeChanges(): void {
    // Watch for data-theme attribute changes
    const observer = new MutationObserver(() => {
      this.contextSubject.next(this.calculateContext());
    });

    observer.observe(document.documentElement, {
      attributes: true,
      attributeFilter: ['data-theme', 'class']
    });

    // Watch for system preference changes
    window.matchMedia('(prefers-color-scheme: dark)').addEventListener('change', () => {
      this.contextSubject.next(this.calculateContext());
    });

    window.matchMedia('(prefers-contrast: high)').addEventListener('change', () => {
      this.contextSubject.next(this.calculateContext());
    });
  }

  getCurrentContext(): ThemeContext {
    return this.contextSubject.value;
  }
}
```

---

## Theme Performance Optimization

### Lazy Theme Loading

```typescript
// Theme loader service
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class ThemeLoaderService {
  private loadedThemes = new Set<string>();

  async loadTheme(themeName: string): Promise<void> {
    if (this.loadedThemes.has(themeName)) {
      return;
    }

    try {
      // Dynamically import theme styles
      const themeModule = await import(`./themes/${themeName}.theme.scss`);
      
      // Create and inject stylesheet
      const styleSheet = document.createElement('style');
      styleSheet.innerHTML = themeModule.default;
      styleSheet.id = `theme-${themeName}`;
      
      document.head.appendChild(styleSheet);
      this.loadedThemes.add(themeName);
    } catch (error) {
      console.error(`Failed to load theme: ${themeName}`, error);
    }
  }

  unloadTheme(themeName: string): void {
    const styleSheet = document.getElementById(`theme-${themeName}`);
    if (styleSheet) {
      styleSheet.remove();
      this.loadedThemes.delete(themeName);
    }
  }

  preloadThemes(themeNames: string[]): Promise<void[]> {
    return Promise.all(themeNames.map(name => this.loadTheme(name)));
  }
}
```

### CSS Custom Properties Optimization

```scss
// Optimize CSS custom properties for performance
:root {
  // Group related properties
  --color-primary: #1976d2;
  --color-primary-variant: #1565c0;
  --color-primary-on: #ffffff;
  
  --color-secondary: #03dac6;
  --color-secondary-variant: #018786;
  --color-secondary-on: #000000;
  
  // Use CSS inheritance efficiently
  --surface-color: var(--color-background);
  --surface-on-color: var(--color-on-background);
  
  // Minimize redundant calculations
  --button-primary-bg: var(--color-primary);
  --button-primary-text: var(--color-primary-on);
  --button-secondary-bg: var(--color-secondary);
  --button-secondary-text: var(--color-secondary-on);
}

// Use contain property for theme boundaries
.theme-container {
  contain: style;
}
```

---

## Advanced Theming Patterns

### Theme Composition Pattern

```scss
// Composable theme mixins
@mixin color-scheme($primary, $secondary, $background) {
  --color-primary: #{$primary};
  --color-secondary: #{$secondary};
  --color-background: #{$background};
  --color-on-background: #{if(lightness($background) > 50%, #000000, #ffffff)};
}

@mixin typography-scheme($font-family, $scale-factor: 1) {
  --font-family-base: #{$font-family};
  --font-size-base: #{16px * $scale-factor};
  --font-size-large: #{20px * $scale-factor};
  --font-size-small: #{14px * $scale-factor};
}

@mixin density-scheme($level) {
  --density-level: #{$level};
  --spacing-unit: #{8px + ($level * 2px)};
  --component-height: #{48px + ($level * 4px)};
}

// Compose themes
.business-theme {
  @include color-scheme(#1976d2, #dc004e, #ffffff);
  @include typography-scheme('Roboto', 1.1);
  @include density-scheme(0);
}

.gaming-theme {
  @include color-scheme(#ff6b00, #00e676, #121212);
  @include typography-scheme('Orbitron', 1.2);
  @include density-scheme(-1);
}
```

### Theme State Management

```typescript
// Theme state management with NgRx
import { createAction, createReducer, createSelector, props } from '@ngrx/store';

// Actions
export const setTheme = createAction(
  '[Theme] Set Theme',
  props<{ theme: string }>()
);

export const toggleTheme = createAction('[Theme] Toggle Theme');

export const loadThemes = createAction('[Theme] Load Themes');

export const themesLoaded = createAction(
  '[Theme] Themes Loaded',
  props<{ themes: ThemeConfig[] }>()
);

// State
export interface ThemeState {
  currentTheme: string;
  availableThemes: ThemeConfig[];
  isLoading: boolean;
  preferences: {
    autoSwitch: boolean;
    preferredTheme: string;
  };
}

const initialState: ThemeState = {
  currentTheme: 'light',
  availableThemes: [],
  isLoading: false,
  preferences: {
    autoSwitch: true,
    preferredTheme: 'auto'
  }
};

// Reducer
export const themeReducer = createReducer(
  initialState,
  on(setTheme, (state, { theme }) => ({
    ...state,
    currentTheme: theme
  })),
  on(toggleTheme, (state) => ({
    ...state,
    currentTheme: state.currentTheme === 'light' ? 'dark' : 'light'
  })),
  on(themesLoaded, (state, { themes }) => ({
    ...state,
    availableThemes: themes,
    isLoading: false
  }))
);

// Selectors
export const selectCurrentTheme = createSelector(
  (state: { theme: ThemeState }) => state.theme,
  (theme) => theme.currentTheme
);

export const selectAvailableThemes = createSelector(
  (state: { theme: ThemeState }) => state.theme,
  (theme) => theme.availableThemes
);
```

---

## Theme Testing and Validation

### Theme Accessibility Testing

```typescript
// Theme accessibility validator
import { Injectable } from '@angular/core';

export interface AccessibilityIssue {
  type: 'contrast' | 'font-size' | 'touch-target';
  severity: 'error' | 'warning' | 'info';
  message: string;
  element?: HTMLElement;
}

@Injectable({
  providedIn: 'root'
})
export class ThemeAccessibilityValidator {
  validateTheme(themeName: string): AccessibilityIssue[] {
    const issues: AccessibilityIssue[] = [];
    
    // Check color contrast
    issues.push(...this.checkColorContrast());
    
    // Check font sizes
    issues.push(...this.checkFontSizes());
    
    // Check touch targets
    issues.push(...this.checkTouchTargets());
    
    return issues;
  }

  private checkColorContrast(): AccessibilityIssue[] {
    const issues: AccessibilityIssue[] = [];
    const elements = document.querySelectorAll('*');
    
    elements.forEach(element => {
      const styles = getComputedStyle(element);
      const backgroundColor = styles.backgroundColor;
      const textColor = styles.color;
      
      if (backgroundColor && textColor) {
        const contrast = this.calculateContrast(backgroundColor, textColor);
        
        if (contrast < 4.5) {
          issues.push({
            type: 'contrast',
            severity: 'error',
            message: `Insufficient color contrast: ${contrast.toFixed(2)}`,
            element: element as HTMLElement
          });
        }
      }
    });
    
    return issues;
  }

  private checkFontSizes(): AccessibilityIssue[] {
    const issues: AccessibilityIssue[] = [];
    const textElements = document.querySelectorAll('p, span, div, button, a');
    
    textElements.forEach(element => {
      const styles = getComputedStyle(element);
      const fontSize = parseInt(styles.fontSize);
      
      if (fontSize < 16) {
        issues.push({
          type: 'font-size',
          severity: 'warning',
          message: `Font size too small: ${fontSize}px`,
          element: element as HTMLElement
        });
      }
    });
    
    return issues;
  }

  private checkTouchTargets(): AccessibilityIssue[] {
    const issues: AccessibilityIssue[] = [];
    const interactiveElements = document.querySelectorAll('button, a, input, select, textarea');
    
    interactiveElements.forEach(element => {
      const rect = element.getBoundingClientRect();
      const minSize = 44; // iOS minimum
      
      if (rect.width < minSize || rect.height < minSize) {
        issues.push({
          type: 'touch-target',
          severity: 'warning',
          message: `Touch target too small: ${rect.width}x${rect.height}`,
          element: element as HTMLElement
        });
      }
    });
    
    return issues;
  }

  private calculateContrast(color1: string, color2: string): number {
    // Simplified contrast calculation
    // In a real implementation, you'd use a proper color contrast library
    const rgb1 = this.parseRgb(color1);
    const rgb2 = this.parseRgb(color2);
    
    const l1 = this.relativeLuminance(rgb1);
    const l2 = this.relativeLuminance(rgb2);
    
    const lighter = Math.max(l1, l2);
    const darker = Math.min(l1, l2);
    
    return (lighter + 0.05) / (darker + 0.05);
  }

  private parseRgb(color: string): [number, number, number] {
    // Simplified RGB parsing
    const match = color.match(/rgb\((\d+),\s*(\d+),\s*(\d+)\)/);
    if (match) {
      return [parseInt(match[1]), parseInt(match[2]), parseInt(match[3])];
    }
    return [0, 0, 0];
  }

  private relativeLuminance([r, g, b]: [number, number, number]): number {
    const [rs, gs, bs] = [r, g, b].map(c => {
      c /= 255;
      return c <= 0.03928 ? c / 12.92 : Math.pow((c + 0.055) / 1.055, 2.4);
    });
    return 0.2126 * rs + 0.7152 * gs + 0.0722 * bs;
  }
}
```

---

## Practical Exercises

### Exercise 1: Multi-Brand Theme System
Create a theme system supporting multiple brands:

1. Build a theme architecture that supports brand inheritance
2. Implement dynamic theme switching
3. Create brand-specific component variants
4. Add theme validation and testing
5. Optimize performance with lazy loading

### Exercise 2: Advanced Theme Customization
Develop a theme customization interface:

1. Build a theme editor with live preview
2. Allow users to customize colors, typography, and density
3. Implement theme export/import functionality
4. Add accessibility validation
5. Create theme sharing capabilities

### Exercise 3: Theme-Aware Application
Build a complete application with advanced theming:

1. Implement context-aware theming
2. Create theme-reactive components
3. Add theme persistence and synchronization
4. Implement theme analytics and usage tracking
5. Build theme A/B testing functionality

### Exercise 4: Theme Performance Optimization
Optimize theme performance:

1. Implement lazy theme loading
2. Optimize CSS custom properties usage
3. Build theme preloading strategies
4. Create theme caching mechanisms
5. Monitor and optimize theme switching performance

---

## 🔗 Additional Resources

- [Angular Material Theming Guide](https://material.angular.dev/guide/theming)
- [CSS Custom Properties Best Practices](https://web.dev/css-props/)
- [WCAG Color Contrast Guidelines](https://www.w3.org/WAI/WCAG21/Understanding/contrast-minimum.html)
- [Material Design Theme Builder](https://m3.material.io/theme-builder)

---

## ✅ Module Completion Checklist

- [ ] Understand multi-theme architecture patterns
- [ ] Can implement dynamic theme switching
- [ ] Build theme inheritance systems
- [ ] Create component-level theme customization
- [ ] Develop theme-aware components
- [ ] Optimize theme performance
- [ ] Implement theme testing and validation
- [ ] Complete all practical exercises

**Next Module**: [09. Component Customization - Fine-tuning Individual Components](../09-component-customization/)
