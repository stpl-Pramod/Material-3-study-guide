# 09. Component Customization - Fine-tuning Individual Components

## 🎯 Learning Objectives
After completing this module, you will be able to:
- Customize individual Angular Material components
- Override component-specific styles and themes
- Create component variants and extensions
- Implement custom component behaviors
- Build reusable component theme mixins
- Understand component theming architecture
- Create custom components that integrate with Material Design 3

## 📚 Table of Contents
1. [Component Theming Architecture](#component-theming-architecture)
2. [Button Customization](#button-customization)
3. [Form Field Customization](#form-field-customization)
4. [Card and Layout Customization](#card-and-layout-customization)
5. [Navigation Component Customization](#navigation-component-customization)
6. [Data Display Component Customization](#data-display-component-customization)
7. [Creating Custom Component Variants](#creating-custom-component-variants)
8. [Component Theme Integration](#component-theme-integration)
9. [Practical Exercises](#practical-exercises)

---

## Component Theming Architecture

### Understanding Component Theme Structure

Angular Material components use a structured theming system with three main parts:

```scss
// Component theme structure
@use '@angular/material' as mat;

// 1. Color theme
@include mat.button-color($theme);

// 2. Typography theme  
@include mat.button-typography($theme);

// 3. Density theme
@include mat.button-density($theme);

// Or all at once
@include mat.button-theme($theme);
```

### Component Theme Mixins

```scss
// Individual component theming
.custom-button {
  @include mat.button-theme((
    color: (
      primary: #e91e63,
      accent: #ff4081,
    ),
    typography: (
      button: mat.define-typography-level(14px, 14px, 500),
    ),
    density: -1,
  ));
}
```

---

## Button Customization

### Basic Button Customization

```scss
// Custom button variants
.elevated-button {
  @include mat.button-theme((
    color: (
      primary: #1976d2,
      accent: #dc004e,
    ),
  ));
  
  .mat-mdc-button {
    box-shadow: 0 4px 8px rgba(0,0,0,0.12);
    border-radius: 24px;
    
    &:hover {
      box-shadow: 0 6px 12px rgba(0,0,0,0.16);
      transform: translateY(-2px);
    }
  }
}

// Gradient button
.gradient-button {
  .mat-mdc-button {
    background: linear-gradient(45deg, #ff6b6b, #4ecdc4);
    color: white;
    border: none;
    
    &:hover {
      background: linear-gradient(45deg, #ff5252, #26a69a);
    }
  }
}

// Outlined button with custom styling
.custom-outlined-button {
  .mat-mdc-outlined-button {
    border: 2px solid var(--mat-sys-primary);
    border-radius: 8px;
    
    &:hover {
      background: var(--mat-sys-primary-container);
      border-color: var(--mat-sys-primary-variant);
    }
  }
}
```

### Button Size Variants

```scss
// Size variants
.button-sizes {
  .small-button {
    @include mat.button-density(-2);
    
    .mat-mdc-button {
      min-width: 64px;
      height: 32px;
      font-size: 12px;
      padding: 0 12px;
    }
  }
  
  .large-button {
    @include mat.button-density(1);
    
    .mat-mdc-button {
      min-width: 120px;
      height: 56px;
      font-size: 16px;
      padding: 0 24px;
    }
  }
  
  .full-width-button {
    .mat-mdc-button {
      width: 100%;
      justify-content: center;
    }
  }
}
```

### Icon Button Customization

```scss
.custom-icon-buttons {
  .floating-action-button {
    .mat-mdc-fab {
      background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
      color: white;
      
      &:hover {
        transform: scale(1.1);
      }
    }
  }
  
  .icon-button-variants {
    .primary-icon-button {
      .mat-mdc-icon-button {
        background: var(--mat-sys-primary-container);
        color: var(--mat-sys-on-primary-container);
      }
    }
    
    .secondary-icon-button {
      .mat-mdc-icon-button {
        background: var(--mat-sys-secondary-container);
        color: var(--mat-sys-on-secondary-container);
      }
    }
  }
}
```

---

## Form Field Customization

### Input Field Styling

```scss
.custom-form-fields {
  // Rounded input fields
  .rounded-input {
    .mat-mdc-form-field {
      .mat-mdc-text-field-wrapper {
        border-radius: 16px;
        
        .mat-mdc-form-field-outline {
          border-radius: 16px;
        }
      }
    }
  }
  
  // Filled input with custom colors
  .custom-filled-input {
    .mat-mdc-form-field-appearance-fill {
      .mat-mdc-text-field-wrapper {
        background-color: var(--mat-sys-surface-variant);
        
        &:hover {
          background-color: var(--mat-sys-surface);
        }
      }
      
      .mat-mdc-form-field-focus-overlay {
        background-color: var(--mat-sys-primary);
        opacity: 0.12;
      }
    }
  }
  
  // Outlined input with enhanced styling
  .enhanced-outlined-input {
    .mat-mdc-form-field-appearance-outline {
      .mat-mdc-form-field-outline {
        border-width: 2px;
        border-color: var(--mat-sys-outline);
        
        &:hover {
          border-color: var(--mat-sys-primary);
        }
      }
      
      &.mat-focused {
        .mat-mdc-form-field-outline {
          border-color: var(--mat-sys-primary);
          box-shadow: 0 0 0 3px rgba(var(--mat-sys-primary-rgb), 0.1);
        }
      }
    }
  }
}
```

### Form Field Validation Styling

```scss
.form-validation-styling {
  // Success state
  .mat-mdc-form-field.success-state {
    .mat-mdc-form-field-outline {
      border-color: #4caf50;
    }
    
    .mat-mdc-form-field-label {
      color: #4caf50;
    }
  }
  
  // Warning state
  .mat-mdc-form-field.warning-state {
    .mat-mdc-form-field-outline {
      border-color: #ff9800;
    }
    
    .mat-mdc-form-field-subscript-wrapper {
      color: #ff9800;
    }
  }
  
  // Error state enhancements
  .mat-mdc-form-field.mat-form-field-invalid {
    .mat-mdc-form-field-outline {
      border-color: var(--mat-sys-error);
      box-shadow: 0 0 0 2px rgba(var(--mat-sys-error-rgb), 0.2);
    }
    
    .mat-mdc-form-field-label {
      color: var(--mat-sys-error);
    }
  }
}
```

### Select and Dropdown Customization

```scss
.custom-select-fields {
  .enhanced-select {
    .mat-mdc-select {
      .mat-mdc-select-trigger {
        padding: 12px 16px;
        border-radius: 8px;
        border: 1px solid var(--mat-sys-outline);
        
        &:hover {
          border-color: var(--mat-sys-primary);
        }
      }
      
      .mat-mdc-select-arrow {
        color: var(--mat-sys-primary);
      }
    }
    
    .mat-mdc-select-panel {
      border-radius: 12px;
      box-shadow: 0 8px 32px rgba(0,0,0,0.12);
      
      .mat-mdc-option {
        padding: 12px 16px;
        
        &:hover {
          background: var(--mat-sys-primary-container);
        }
        
        &.mat-mdc-option-active {
          background: var(--mat-sys-secondary-container);
          color: var(--mat-sys-on-secondary-container);
        }
      }
    }
  }
}
```

---

## Card and Layout Customization

### Card Variants

```scss
.custom-cards {
  // Elevated card
  .elevated-card {
    .mat-mdc-card {
      box-shadow: 0 4px 20px rgba(0,0,0,0.08);
      border-radius: 16px;
      overflow: hidden;
      
      &:hover {
        box-shadow: 0 8px 32px rgba(0,0,0,0.12);
        transform: translateY(-4px);
      }
    }
  }
  
  // Outlined card
  .outlined-card {
    .mat-mdc-card {
      border: 2px solid var(--mat-sys-outline-variant);
      border-radius: 12px;
      box-shadow: none;
      
      &:hover {
        border-color: var(--mat-sys-primary);
      }
    }
  }
  
  // Gradient card
  .gradient-card {
    .mat-mdc-card {
      background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
      color: white;
      
      .mat-mdc-card-title,
      .mat-mdc-card-subtitle {
        color: white;
      }
    }
  }
  
  // Image card
  .image-card {
    .mat-mdc-card {
      position: relative;
      overflow: hidden;
      
      .card-image {
        width: 100%;
        height: 200px;
        object-fit: cover;
      }
      
      .card-overlay {
        position: absolute;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        background: linear-gradient(transparent, rgba(0,0,0,0.7));
        display: flex;
        align-items: flex-end;
        padding: 16px;
        
        .mat-mdc-card-title {
          color: white;
          margin: 0;
        }
      }
    }
  }
}
```

### Layout Component Customization

```scss
.custom-layouts {
  // Toolbar customization
  .custom-toolbar {
    .mat-toolbar {
      background: linear-gradient(90deg, #667eea 0%, #764ba2 100%);
      color: white;
      box-shadow: 0 2px 8px rgba(0,0,0,0.1);
      
      .mat-toolbar-row {
        padding: 0 24px;
      }
      
      .toolbar-title {
        font-weight: 600;
        letter-spacing: 0.5px;
      }
    }
  }
  
  // Sidenav customization
  .custom-sidenav {
    .mat-sidenav {
      background: var(--mat-sys-surface-variant);
      border-right: 1px solid var(--mat-sys-outline-variant);
      
      .nav-header {
        padding: 24px 16px;
        background: var(--mat-sys-primary-container);
        color: var(--mat-sys-on-primary-container);
      }
      
      .mat-nav-list {
        .mat-list-item {
          border-radius: 8px;
          margin: 4px 8px;
          
          &:hover {
            background: var(--mat-sys-primary-container);
          }
          
          &.active {
            background: var(--mat-sys-secondary-container);
            color: var(--mat-sys-on-secondary-container);
          }
        }
      }
    }
  }
}
```

---

## Navigation Component Customization

### Tab Customization

```scss
.custom-tabs {
  .mat-mdc-tab-group {
    // Tab header styling
    .mat-mdc-tab-header {
      border-bottom: 1px solid var(--mat-sys-outline-variant);
      
      .mat-mdc-tab-label-container {
        .mat-mdc-tab-label {
          padding: 12px 24px;
          font-weight: 500;
          text-transform: uppercase;
          letter-spacing: 0.5px;
          
          &:hover {
            background: var(--mat-sys-primary-container);
          }
          
          &.mat-mdc-tab-label-active {
            color: var(--mat-sys-primary);
            background: var(--mat-sys-primary-container);
          }
        }
      }
      
      .mat-mdc-tab-header-pagination {
        .mat-mdc-tab-header-pagination-chevron {
          color: var(--mat-sys-primary);
        }
      }
    }
    
    // Tab content styling
    .mat-mdc-tab-body-wrapper {
      .mat-mdc-tab-body {
        padding: 24px;
      }
    }
  }
  
  // Vertical tabs
  .vertical-tabs {
    .mat-mdc-tab-group {
      flex-direction: row;
      
      .mat-mdc-tab-header {
        flex-direction: column;
        width: 200px;
        border-right: 1px solid var(--mat-sys-outline-variant);
        border-bottom: none;
      }
      
      .mat-mdc-tab-body-wrapper {
        flex: 1;
      }
    }
  }
}
```

### Menu Customization

```scss
.custom-menus {
  .mat-mdc-menu-panel {
    border-radius: 12px;
    box-shadow: 0 8px 32px rgba(0,0,0,0.12);
    border: 1px solid var(--mat-sys-outline-variant);
    
    .mat-mdc-menu-item {
      padding: 12px 16px;
      
      &:hover {
        background: var(--mat-sys-primary-container);
        color: var(--mat-sys-on-primary-container);
      }
      
      .mat-icon {
        margin-right: 12px;
        color: var(--mat-sys-on-surface-variant);
      }
      
      &.danger-item {
        color: var(--mat-sys-error);
        
        &:hover {
          background: var(--mat-sys-error-container);
          color: var(--mat-sys-on-error-container);
        }
      }
    }
    
    .mat-mdc-menu-item-submenu-trigger {
      &::after {
        color: var(--mat-sys-on-surface-variant);
      }
    }
  }
}
```

---

## Data Display Component Customization

### Table Customization

```scss
.custom-tables {
  .mat-mdc-table {
    border-radius: 12px;
    overflow: hidden;
    box-shadow: 0 4px 16px rgba(0,0,0,0.04);
    
    // Header styling
    .mat-mdc-header-row {
      background: var(--mat-sys-surface-variant);
      
      .mat-mdc-header-cell {
        font-weight: 600;
        color: var(--mat-sys-on-surface-variant);
        padding: 16px;
        border-bottom: 2px solid var(--mat-sys-outline-variant);
      }
    }
    
    // Row styling
    .mat-mdc-row {
      transition: background-color 200ms ease;
      
      &:hover {
        background: var(--mat-sys-primary-container);
      }
      
      &:nth-child(even) {
        background: var(--mat-sys-surface-variant);
      }
      
      .mat-mdc-cell {
        padding: 16px;
        border-bottom: 1px solid var(--mat-sys-outline-variant);
      }
    }
    
    // Action cells
    .action-cell {
      .mat-mdc-icon-button {
        margin: 0 4px;
        
        &.edit-button {
          color: var(--mat-sys-primary);
        }
        
        &.delete-button {
          color: var(--mat-sys-error);
        }
      }
    }
  }
  
  // Paginator styling
  .mat-mdc-paginator {
    background: var(--mat-sys-surface-variant);
    border-top: 1px solid var(--mat-sys-outline-variant);
    
    .mat-mdc-select {
      margin: 0 8px;
    }
    
    .mat-mdc-icon-button {
      color: var(--mat-sys-primary);
    }
  }
}
```

### List Customization

```scss
.custom-lists {
  .mat-mdc-list {
    .mat-mdc-list-item {
      padding: 16px;
      border-radius: 8px;
      margin: 4px 0;
      
      &:hover {
        background: var(--mat-sys-primary-container);
      }
      
      .mat-mdc-list-item-content {
        .mat-mdc-list-item-title {
          font-weight: 500;
          color: var(--mat-sys-on-surface);
        }
        
        .mat-mdc-list-item-line {
          color: var(--mat-sys-on-surface-variant);
        }
      }
      
      .mat-mdc-list-item-meta {
        .mat-icon {
          color: var(--mat-sys-on-surface-variant);
        }
      }
    }
  }
  
  // Selection list
  .mat-mdc-selection-list {
    .mat-mdc-list-option {
      &.mat-mdc-list-option-selected {
        background: var(--mat-sys-secondary-container);
        color: var(--mat-sys-on-secondary-container);
      }
    }
  }
}
```

---

## Creating Custom Component Variants

### Custom Component Theme Mixin

```scss
// Create reusable component theme mixins
@mixin custom-component-theme($theme) {
  $color: mat.get-color-config($theme);
  $typography: mat.get-typography-config($theme);
  $density: mat.get-density-config($theme);
  
  .custom-component {
    background: mat.get-color-from-palette($color, 'primary');
    color: mat.get-color-from-palette($color, 'primary', 'default-contrast');
    font-family: mat.font-family($typography);
    
    @if $density < 0 {
      padding: 8px;
    } @else {
      padding: 16px;
    }
  }
}

// Apply the mixin
@include custom-component-theme($theme);
```

### Component Variant Factory

```scss
// Factory for creating component variants
@mixin create-component-variant($name, $config) {
  .#{$name}-variant {
    @if map-has-key($config, 'background') {
      background: map-get($config, 'background');
    }
    
    @if map-has-key($config, 'color') {
      color: map-get($config, 'color');
    }
    
    @if map-has-key($config, 'border-radius') {
      border-radius: map-get($config, 'border-radius');
    }
    
    @if map-has-key($config, 'padding') {
      padding: map-get($config, 'padding');
    }
    
    @if map-has-key($config, 'shadow') {
      box-shadow: map-get($config, 'shadow');
    }
  }
}

// Create variants
@include create-component-variant('success', (
  background: #4caf50,
  color: white,
  border-radius: 8px,
  padding: 12px 16px,
  shadow: 0 2px 8px rgba(76, 175, 80, 0.3)
));

@include create-component-variant('warning', (
  background: #ff9800,
  color: white,
  border-radius: 8px,
  padding: 12px 16px,
  shadow: 0 2px 8px rgba(255, 152, 0, 0.3)
));
```

---

## Component Theme Integration

### Theme-Aware Custom Component

```typescript
import { Component, Input, ChangeDetectionStrategy } from '@angular/core';

@Component({
  selector: 'app-custom-card',
  template: `
    <div class="custom-card" [class]="variantClass">
      <div class="card-header" *ngIf="title">
        <h3>{{title}}</h3>
        <mat-icon *ngIf="icon">{{icon}}</mat-icon>
      </div>
      <div class="card-content">
        <ng-content></ng-content>
      </div>
      <div class="card-actions" *ngIf="actions">
        <ng-content select="[slot=actions]"></ng-content>
      </div>
    </div>
  `,
  styles: [`
    .custom-card {
      background: var(--mat-sys-surface);
      color: var(--mat-sys-on-surface);
      border-radius: 12px;
      box-shadow: 0 4px 16px rgba(0,0,0,0.08);
      overflow: hidden;
      transition: all 300ms ease;
      
      &:hover {
        box-shadow: 0 8px 24px rgba(0,0,0,0.12);
        transform: translateY(-2px);
      }
      
      .card-header {
        background: var(--mat-sys-primary-container);
        color: var(--mat-sys-on-primary-container);
        padding: 16px;
        display: flex;
        justify-content: space-between;
        align-items: center;
        
        h3 {
          margin: 0;
          font-weight: 500;
        }
      }
      
      .card-content {
        padding: 16px;
      }
      
      .card-actions {
        padding: 8px 16px 16px;
        display: flex;
        gap: 8px;
        justify-content: flex-end;
      }
      
      // Variants
      &.success-variant {
        .card-header {
          background: #4caf50;
          color: white;
        }
      }
      
      &.warning-variant {
        .card-header {
          background: #ff9800;
          color: white;
        }
      }
      
      &.error-variant {
        .card-header {
          background: var(--mat-sys-error);
          color: var(--mat-sys-on-error);
        }
      }
    }
  `],
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class CustomCardComponent {
  @Input() title?: string;
  @Input() icon?: string;
  @Input() variant: 'default' | 'success' | 'warning' | 'error' = 'default';
  @Input() actions = false;

  get variantClass(): string {
    return this.variant !== 'default' ? `${this.variant}-variant` : '';
  }
}
```

---

## Practical Exercises

### Exercise 1: Button Component Library
Create a comprehensive button component library:

1. Build multiple button variants (elevated, outlined, text, icon)
2. Implement size variations (small, medium, large)
3. Create state-based styling (loading, disabled, success, error)
4. Add animation and interaction effects
5. Ensure accessibility compliance

### Exercise 2: Advanced Form Components
Develop enhanced form components:

1. Create custom form field variants with validation states
2. Build multi-step form components
3. Implement form field animations and transitions
4. Add custom validation styling
5. Create form component composition patterns

### Exercise 3: Data Display Components
Build custom data display components:

1. Create advanced table components with sorting and filtering
2. Develop custom list components with virtual scrolling
3. Build card grid layouts with different layouts
4. Implement responsive data display patterns
5. Add data visualization integrations

### Exercise 4: Navigation Component System
Create a complete navigation system:

1. Build responsive navigation components
2. Create breadcrumb and stepper components
3. Implement multi-level menu systems
4. Add navigation state management
5. Build navigation accessibility features

---

## 🔗 Additional Resources

- [Angular Material Component API](https://material.angular.dev/components/categories)
- [Material Design 3 Component Guidelines](https://m3.material.io/components)
- [CSS Custom Properties for Components](https://web.dev/css-props/)
- [Angular Component Architecture](https://angular.io/guide/architecture-components)

---

## ✅ Module Completion Checklist

- [ ] Understand component theming architecture
- [ ] Can customize button components effectively
- [ ] Master form field customization techniques
- [ ] Create custom layout component variants
- [ ] Build navigation component customizations
- [ ] Develop data display component themes
- [ ] Create reusable component theme mixins
- [ ] Complete all practical exercises

**Next Module**: [10. Icons & Assets - Working with Material Icons and Custom SVGs](../10-icons-assets/)
