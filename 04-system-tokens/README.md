# 04. System Tokens - Understanding Material Design 3 Design Tokens

## 🎯 Learning Objectives
After completing this module, you will be able to:
- Understand what design tokens are and why they're important
- Navigate the Material Design 3 token system hierarchy
- Use system tokens effectively in your components
- Understand the relationship between tokens and CSS custom properties
- Override and customize tokens appropriately
- Debug token-related issues

## 📚 Table of Contents
1. [What Are Design Tokens?](#what-are-design-tokens)
2. [Material Design 3 Token Hierarchy](#material-design-3-token-hierarchy)
3. [Color System Tokens](#color-system-tokens)
4. [Typography System Tokens](#typography-system-tokens)
5. [Elevation System Tokens](#elevation-system-tokens)
6. [Using Tokens in Components](#using-tokens-in-components)
7. [Token Overrides and Customization](#token-overrides-and-customization)
8. [Debugging and Inspection](#debugging-and-inspection)
9. [Practical Exercises](#practical-exercises)

---

## What Are Design Tokens?

Design tokens are named entities that store visual design attributes. They're the building blocks of a design system, representing design decisions as data that can be consumed by any platform or technology.

### Benefits of Design Tokens

1. **Consistency**: Ensure uniform visual appearance across the application
2. **Maintainability**: Change values in one place to update everywhere
3. **Scalability**: Easy to add new themes or variations
4. **Cross-platform**: Can be consumed by web, mobile, and desktop applications
5. **Developer Experience**: Semantic names make code more readable

### Token Types in Material Design 3

```
Global Tokens (Brand decisions)
    ↓
System Tokens (Role-based assignments)
    ↓
Component Tokens (Component-specific values)
    ↓
Hard-coded Values (Implementation specifics)
```

---

## Material Design 3 Token Hierarchy

### System Tokens (Global Level)
System tokens define the overall design language:
- Color roles (primary, secondary, surface, etc.)
- Typography scales (headline, body, label, etc.)
- Elevation levels (level0 through level5)
- Shape values (corner radius values)

### Component Tokens (Component Level)
Component tokens define specific component styling:
- Button container color
- Card elevation
- Form field border color

### CSS Custom Properties Implementation

In Angular Material, tokens are implemented as CSS custom properties:

```css
/* System tokens */
--mat-sys-primary: #6750a4;
--mat-sys-on-primary: #ffffff;
--mat-sys-surface: #fffbfe;

/* Component tokens */
--mat-button-container-color: var(--mat-sys-primary);
--mat-card-elevated-container-color: var(--mat-sys-surface);
```

---

## Color System Tokens

### Primary Color Tokens

```scss
// Main primary color
--mat-sys-primary: /* Primary color for high-emphasis elements */
--mat-sys-on-primary: /* Text/icons on primary backgrounds */

// Primary container (softer primary usage)
--mat-sys-primary-container: /* Softer primary for containers */
--mat-sys-on-primary-container: /* Text/icons on primary containers */

// Primary variants
--mat-sys-primary-fixed: /* Fixed primary for consistency */
--mat-sys-primary-fixed-dim: /* Dimmed primary fixed */
--mat-sys-on-primary-fixed: /* Text on primary fixed */
--mat-sys-on-primary-fixed-variant: /* Variant text on primary fixed */
```

### Surface Color Tokens

```scss
// Background surfaces
--mat-sys-background: /* Main app background */
--mat-sys-on-background: /* Text on main background */

--mat-sys-surface: /* Component backgrounds */
--mat-sys-on-surface: /* Text on surfaces */

--mat-sys-surface-dim: /* Dimmed surface */
--mat-sys-surface-bright: /* Bright surface */

// Surface containers (different elevation levels)
--mat-sys-surface-container-lowest: /* Lowest elevation container */
--mat-sys-surface-container-low: /* Low elevation container */
--mat-sys-surface-container: /* Standard container */
--mat-sys-surface-container-high: /* High elevation container */
--mat-sys-surface-container-highest: /* Highest elevation container */
```

### Secondary and Tertiary Tokens

```scss
// Secondary colors (supporting color)
--mat-sys-secondary: /* Secondary color */
--mat-sys-on-secondary: /* Text on secondary */
--mat-sys-secondary-container: /* Secondary container */
--mat-sys-on-secondary-container: /* Text on secondary container */

// Tertiary colors (accent color)
--mat-sys-tertiary: /* Tertiary accent color */
--mat-sys-on-tertiary: /* Text on tertiary */
--mat-sys-tertiary-container: /* Tertiary container */
--mat-sys-on-tertiary-container: /* Text on tertiary container */
```

### Utility Color Tokens

```scss
// Error states
--mat-sys-error: /* Error color */
--mat-sys-on-error: /* Text on error backgrounds */
--mat-sys-error-container: /* Error container */
--mat-sys-on-error-container: /* Text on error containers */

// Outline and borders
--mat-sys-outline: /* Main outline color */
--mat-sys-outline-variant: /* Subtle outline variant */

// Special tokens
--mat-sys-shadow: /* Shadow color */
--mat-sys-scrim: /* Overlay scrim color */
--mat-sys-surface-tint: /* Surface tint color */
```

---

## Typography System Tokens

### Typography Scale Tokens

Material Design 3 provides a comprehensive typography scale:

```scss
// Display (largest text)
--mat-sys-display-large: /* Large display text */
--mat-sys-display-medium: /* Medium display text */
--mat-sys-display-small: /* Small display text */

// Headline (section headers)
--mat-sys-headline-large: /* Large headlines */
--mat-sys-headline-medium: /* Medium headlines */
--mat-sys-headline-small: /* Small headlines */

// Title (subsection headers)
--mat-sys-title-large: /* Large titles */
--mat-sys-title-medium: /* Medium titles */
--mat-sys-title-small: /* Small titles */

// Body (main content)
--mat-sys-body-large: /* Large body text */
--mat-sys-body-medium: /* Medium body text */
--mat-sys-body-small: /* Small body text */

// Label (UI elements)
--mat-sys-label-large: /* Large labels */
--mat-sys-label-medium: /* Medium labels */
--mat-sys-label-small: /* Small labels */
```

### Typography Token Components

Each typography token includes multiple properties:

```scss
// Example for body-medium
--mat-sys-body-medium: 400 0.875rem / 1.25rem Roboto, sans-serif;

// Individual components (auto-generated)
--mat-sys-body-medium-font: Roboto, sans-serif;
--mat-sys-body-medium-line-height: 1.25rem;
--mat-sys-body-medium-size: 0.875rem;
--mat-sys-body-medium-tracking: 0.016rem;
--mat-sys-body-medium-weight: 400;
```

### Typography Usage Examples

```scss
.page-title {
  font: var(--mat-sys-headline-large);
  color: var(--mat-sys-on-surface);
}

.section-header {
  font: var(--mat-sys-title-medium);
  color: var(--mat-sys-primary);
}

.body-text {
  font: var(--mat-sys-body-medium);
  color: var(--mat-sys-on-surface);
}

.button-label {
  font: var(--mat-sys-label-medium);
  color: var(--mat-sys-on-primary);
}
```

---

## Elevation System Tokens

Material Design provides six elevation levels:

```scss
// Elevation levels (box-shadow values)
--mat-sys-level0: /* No elevation */
--mat-sys-level1: /* Minimal elevation */
--mat-sys-level2: /* Low elevation */
--mat-sys-level3: /* Medium elevation */
--mat-sys-level4: /* High elevation */
--mat-sys-level5: /* Maximum elevation */
```

### Elevation Usage Examples

```scss
.floating-card {
  box-shadow: var(--mat-sys-level3);
}

.modal-dialog {
  box-shadow: var(--mat-sys-level5);
}

.bottom-sheet {
  box-shadow: var(--mat-sys-level2);
}
```

### Shape System Tokens

```scss
// Corner radius values
--mat-sys-corner-none: 0;
--mat-sys-corner-extra-small: 4px;
--mat-sys-corner-small: 8px;
--mat-sys-corner-medium: 12px;
--mat-sys-corner-large: 16px;
--mat-sys-corner-extra-large: 28px;
--mat-sys-corner-full: 9999px;

// Directional corners
--mat-sys-corner-large-top: 16px 16px 0 0;
--mat-sys-corner-large-end: 0 16px 16px 0;
```

---

## Using Tokens in Components

### Basic Token Usage

```scss
.custom-component {
  // Use surface tokens for backgrounds
  background: var(--mat-sys-surface);
  color: var(--mat-sys-on-surface);
  
  // Use outline tokens for borders
  border: 1px solid var(--mat-sys-outline-variant);
  
  // Use shape tokens for corners
  border-radius: var(--mat-sys-corner-medium);
  
  // Use elevation tokens for shadows
  box-shadow: var(--mat-sys-level1);
  
  // Use typography tokens for text
  font: var(--mat-sys-body-medium);
}
```

### Semantic Color Usage

```scss
.alert-component {
  // Primary alert
  &.primary {
    background: var(--mat-sys-primary-container);
    color: var(--mat-sys-on-primary-container);
    border-left: 4px solid var(--mat-sys-primary);
  }
  
  // Error alert
  &.error {
    background: var(--mat-sys-error-container);
    color: var(--mat-sys-on-error-container);
    border-left: 4px solid var(--mat-sys-error);
  }
  
  // Surface alert
  &.info {
    background: var(--mat-sys-surface-container-high);
    color: var(--mat-sys-on-surface);
    border-left: 4px solid var(--mat-sys-outline);
  }
}
```

### Interactive States

```scss
.interactive-element {
  background: var(--mat-sys-surface);
  color: var(--mat-sys-on-surface);
  border: 1px solid var(--mat-sys-outline);
  transition: all 0.2s ease;
  
  &:hover {
    background: var(--mat-sys-surface-container-high);
    border-color: var(--mat-sys-primary);
  }
  
  &:focus {
    outline: 2px solid var(--mat-sys-primary);
    outline-offset: 2px;
  }
  
  &:active {
    background: var(--mat-sys-surface-container-highest);
  }
}
```

---

## Token Overrides and Customization

### System Token Overrides

Use the `mat.theme-overrides` mixin to customize system tokens:

```scss
@use '@angular/material' as mat;

:root {
  @include mat.theme((
    color: mat.$blue-palette,
    typography: Roboto,
    density: 0,
  ));
  
  // Override specific system tokens
  @include mat.theme-overrides((
    primary: #1976d2,
    primary-container: #e3f2fd,
    surface: #fafafa,
    corner-large: 24px,
  ));
}
```

### Scoped Token Overrides

Apply overrides to specific sections:

```scss
.custom-section {
  @include mat.theme-overrides((
    primary: #ff5722,
    primary-container: #fbe9e7,
  ));
}
```

### Component Token Overrides

Override tokens for specific components:

```scss
// Button overrides
html {
  @include mat.button-overrides((
    container-color: var(--mat-sys-tertiary),
    label-text-color: var(--mat-sys-on-tertiary),
    container-shape: var(--mat-sys-corner-full),
  ));
}

// Card overrides
html {
  @include mat.card-overrides((
    elevated-container-color: var(--mat-sys-surface-container-high),
    elevated-container-elevation: var(--mat-sys-level2),
    container-shape: var(--mat-sys-corner-large),
  ));
}
```

---

## Debugging and Inspection

### Browser DevTools Inspection

1. **Open DevTools** (F12)
2. **Select element** with inspector
3. **Check Computed styles** for CSS custom properties
4. **Look for `--mat-sys-*` properties**

### Common Token Issues

#### Issue 1: Token Not Applied
```scss
// ❌ Wrong - token doesn't exist
color: var(--mat-sys-primary-text);

// ✅ Correct - proper token name
color: var(--mat-sys-on-primary);
```

#### Issue 2: Token Inheritance
```scss
// ❌ Wrong - tokens don't inherit properly
.parent {
  --mat-sys-primary: red;
  
  .child {
    // This won't work as expected
    color: var(--mat-sys-primary);
  }
}

// ✅ Correct - use theme overrides
.parent {
  @include mat.theme-overrides((
    primary: red,
  ));
  
  .child {
    color: var(--mat-sys-primary);
  }
}
```

### Token Reference Tool

Create a reference page to view all tokens:

```html
<div class="token-reference">
  <h2>Color Tokens</h2>
  <div class="token-grid">
    <div class="token-item" style="background: var(--mat-sys-primary)">
      primary
    </div>
    <div class="token-item" style="background: var(--mat-sys-secondary)">
      secondary
    </div>
    <div class="token-item" style="background: var(--mat-sys-tertiary)">
      tertiary
    </div>
    <!-- Add more tokens as needed -->
  </div>
</div>
```

---

## Practical Exercises

### Exercise 1: Token Exploration
Create a comprehensive token reference page that displays all available system tokens:

```typescript
@Component({
  selector: 'app-token-reference',
  template: `
    <div class="token-reference">
      <h1>Material Design 3 Token Reference</h1>
      
      <section class="token-section">
        <h2>Color Tokens</h2>
        <div class="color-grid">
          <div class="color-token" 
               *ngFor="let token of colorTokens"
               [style.background]="'var(--mat-sys-' + token + ')'"
               [style.color]="'var(--mat-sys-on-' + token + ')'">
            {{ token }}
          </div>
        </div>
      </section>
      
      <section class="token-section">
        <h2>Typography Tokens</h2>
        <div class="typography-samples">
          <div *ngFor="let token of typographyTokens"
               [style.font]="'var(--mat-sys-' + token + ')'">
            {{ token }}: Sample text
          </div>
        </div>
      </section>
      
      <section class="token-section">
        <h2>Elevation Tokens</h2>
        <div class="elevation-samples">
          <div *ngFor="let level of elevationLevels"
               class="elevation-sample"
               [style.box-shadow]="'var(--mat-sys-level' + level + ')'">
            Level {{ level }}
          </div>
        </div>
      </section>
    </div>
  `,
  styleUrl: './token-reference.component.scss',
  standalone: true,
  imports: [CommonModule]
})
export class TokenReferenceComponent {
  colorTokens = [
    'primary', 'secondary', 'tertiary', 'error',
    'surface', 'background', 'primary-container',
    'secondary-container', 'tertiary-container', 'error-container'
  ];
  
  typographyTokens = [
    'display-large', 'display-medium', 'display-small',
    'headline-large', 'headline-medium', 'headline-small',
    'title-large', 'title-medium', 'title-small',
    'body-large', 'body-medium', 'body-small',
    'label-large', 'label-medium', 'label-small'
  ];
  
  elevationLevels = [0, 1, 2, 3, 4, 5];
}
```

### Exercise 2: Custom Alert Component
Build a reusable alert component using system tokens:

```typescript
@Component({
  selector: 'app-alert',
  template: `
    <div class="alert" [class]="'alert-' + type">
      <mat-icon class="alert-icon">{{ getIcon() }}</mat-icon>
      <div class="alert-content">
        <h4 class="alert-title" *ngIf="title">{{ title }}</h4>
        <p class="alert-message">{{ message }}</p>
      </div>
      <button mat-icon-button class="alert-close" (click)="onClose()">
        <mat-icon>close</mat-icon>
      </button>
    </div>
  `,
  styleUrl: './alert.component.scss',
  standalone: true,
  imports: [MatIconModule, MatButtonModule, CommonModule]
})
export class AlertComponent {
  @Input() type: 'info' | 'success' | 'warning' | 'error' = 'info';
  @Input() title?: string;
  @Input() message = '';
  @Output() close = new EventEmitter<void>();
  
  getIcon(): string {
    const icons = {
      info: 'info',
      success: 'check_circle',
      warning: 'warning',
      error: 'error'
    };
    return icons[this.type];
  }
  
  onClose(): void {
    this.close.emit();
  }
}
```

```scss
// alert.component.scss
.alert {
  display: flex;
  align-items: flex-start;
  gap: 12px;
  padding: 16px;
  border-radius: var(--mat-sys-corner-medium);
  border-left: 4px solid;
  font: var(--mat-sys-body-medium);
  
  &.alert-info {
    background: var(--mat-sys-surface-container-high);
    color: var(--mat-sys-on-surface);
    border-left-color: var(--mat-sys-primary);
    
    .alert-icon {
      color: var(--mat-sys-primary);
    }
  }
  
  &.alert-success {
    background: var(--mat-sys-secondary-container);
    color: var(--mat-sys-on-secondary-container);
    border-left-color: var(--mat-sys-secondary);
    
    .alert-icon {
      color: var(--mat-sys-secondary);
    }
  }
  
  &.alert-warning {
    background: var(--mat-sys-tertiary-container);
    color: var(--mat-sys-on-tertiary-container);
    border-left-color: var(--mat-sys-tertiary);
    
    .alert-icon {
      color: var(--mat-sys-tertiary);
    }
  }
  
  &.alert-error {
    background: var(--mat-sys-error-container);
    color: var(--mat-sys-on-error-container);
    border-left-color: var(--mat-sys-error);
    
    .alert-icon {
      color: var(--mat-sys-error);
    }
  }
}

.alert-content {
  flex: 1;
}

.alert-title {
  font: var(--mat-sys-title-small);
  margin: 0 0 4px 0;
}

.alert-message {
  margin: 0;
}

.alert-close {
  opacity: 0.7;
  
  &:hover {
    opacity: 1;
  }
}
```

### Exercise 3: Token Override Experimentation
Create a theme playground that allows you to override different tokens and see the results:

```html
<div class="theme-playground">
  <div class="controls">
    <h3>Token Overrides</h3>
    <mat-form-field>
      <mat-label>Primary Color</mat-label>
      <input matInput type="color" [(ngModel)]="primaryColor" (change)="updateTokens()">
    </mat-form-field>
    
    <mat-form-field>
      <mat-label>Corner Radius</mat-label>
      <mat-select [(value)]="cornerRadius" (selectionChange)="updateTokens()">
        <mat-option value="4px">Small (4px)</mat-option>
        <mat-option value="8px">Medium (8px)</mat-option>
        <mat-option value="16px">Large (16px)</mat-option>
        <mat-option value="24px">Extra Large (24px)</mat-option>
      </mat-select>
    </mat-form-field>
  </div>
  
  <div class="preview" [style]="getCustomStyles()">
    <button mat-raised-button color="primary">Primary Button</button>
    <mat-card>
      <mat-card-content>
        Sample card with custom tokens
      </mat-card-content>
    </mat-card>
  </div>
</div>
```

---

## 📝 Key Takeaways

- Design tokens provide a systematic approach to design decisions
- Material Design 3 uses a hierarchical token system: System → Component → Values
- Tokens are implemented as CSS custom properties for easy theming
- System tokens cover color, typography, elevation, and shape concerns
- Token overrides allow fine-grained customization while maintaining consistency
- Proper token usage ensures accessibility and maintainability

---

## 🔗 Additional Resources

- [Material Design System Variables](https://material.angular.dev/guide/system-variables)
- [Design Tokens Community Group](https://www.designtokens.org/)
- [CSS Custom Properties MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/--*)
- [Material Design Color System](https://m3.material.io/styles/color/system/overview)

---

## ✅ Module Completion Checklist

- [ ] Understand what design tokens are and their benefits
- [ ] Know the Material Design 3 token hierarchy
- [ ] Can identify and use color system tokens
- [ ] Can apply typography tokens effectively
- [ ] Understand elevation and shape tokens
- [ ] Can override tokens using theme-overrides mixin
- [ ] Built custom components using system tokens
- [ ] Know how to debug token-related issues
- [ ] Created token reference page and custom alert component
- [ ] Ready to deep dive into the Color System

---

**Next Module:** [05. Color System](../05-color-system/README.md)
