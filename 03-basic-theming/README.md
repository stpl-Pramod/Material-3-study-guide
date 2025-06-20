# 03. Basic Theming - Your First Material Design 3 Theme

## 🎯 Learning Objectives
After completing this module, you will be able to:
- Create and apply basic Material Design 3 themes
- Understand the theme configuration structure
- Use prebuilt color palettes effectively
- Apply themes to components
- Switch between different themes
- Understand the relationship between themes and CSS custom properties

## 📚 Table of Contents
1. [Theme Structure Overview](#theme-structure-overview)
2. [Creating Your First Theme](#creating-your-first-theme)
3. [Understanding Theme Configuration](#understanding-theme-configuration)
4. [Prebuilt Color Palettes](#prebuilt-color-palettes)
5. [Applying Themes to Components](#applying-themes-to-components)
6. [Theme Customization Basics](#theme-customization-basics)
7. [Light and Dark Mode Setup](#light-and-dark-mode-setup)
8. [Practical Exercises](#practical-exercises)

---

## Theme Structure Overview

A Material Design 3 theme consists of three main parts:

```scss
@use '@angular/material' as mat;

:root {
  @include mat.theme((
    color: /* Color palette configuration */,
    typography: /* Font and text configuration */,
    density: /* Spacing and sizing configuration */
  ));
}
```

### Theme Components

1. **Color**: Defines the color scheme (primary, secondary, tertiary, etc.)
2. **Typography**: Controls fonts, sizes, and text styles
3. **Density**: Manages spacing and component sizes

---

## Creating Your First Theme

### Step 1: Basic Theme Setup

Create or modify `src/styles.scss`:

```scss
// Import Angular Material
@use '@angular/material' as mat;

// Basic app styles
html, body { 
  height: 100%; 
  margin: 0;
  padding: 0;
}

body { 
  font-family: Roboto, "Helvetica Neue", sans-serif; 
}

// Apply theme
:root {
  @include mat.theme((
    color: mat.$azure-palette,
    typography: Roboto,
    density: 0,
  ));
}
```

### Step 2: Test Your Theme

Create a simple component to test the theme:

**app.component.html:**
```html
<div class="app-container">
  <h1>My Material Design 3 App</h1>
  <p>Testing the basic theme setup</p>
  
  <div class="button-container">
    <button mat-raised-button color="primary">Primary Button</button>
    <button mat-raised-button color="accent">Accent Button</button>
    <button mat-raised-button color="warn">Warn Button</button>
  </div>
  
  <mat-card class="example-card">
    <mat-card-header>
      <mat-card-title>Card Title</mat-card-title>
      <mat-card-subtitle>Card Subtitle</mat-card-subtitle>
    </mat-card-header>
    <mat-card-content>
      <p>This is a sample card to demonstrate theming.</p>
    </mat-card-content>
  </mat-card>
</div>
```

**app.component.scss:**
```scss
.app-container {
  padding: 2rem;
  max-width: 800px;
  margin: 0 auto;
}

.button-container {
  display: flex;
  gap: 1rem;
  margin: 2rem 0;
  flex-wrap: wrap;
}

.example-card {
  max-width: 400px;
  margin: 2rem 0;
}
```

**app.component.ts:**
```typescript
import { Component } from '@angular/core';
import { MatButtonModule } from '@angular/material/button';
import { MatCardModule } from '@angular/material/card';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [MatButtonModule, MatCardModule],
  templateUrl: './app.component.html',
  styleUrl: './app.component.scss'
})
export class AppComponent {
  title = 'my-material-app';
}
```

---

## Understanding Theme Configuration

### Color Configuration Options

#### Option 1: Single Palette
```scss
:root {
  @include mat.theme((
    color: mat.$blue-palette,
    typography: Roboto,
    density: 0,
  ));
}
```

#### Option 2: Color Map
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

### Typography Configuration Options

#### Option 1: Single Font Family
```scss
typography: Roboto
```

#### Option 2: Typography Map
```scss
typography: (
  plain-family: Roboto,
  brand-family: 'Open Sans',
  bold-weight: 700,
  medium-weight: 500,
  regular-weight: 400,
)
```

### Density Configuration
```scss
density: 0    // Default spacing
density: -1   // Slightly compact
density: -2   // More compact
density: -3   // Very compact
density: -4   // Maximum compact
density: -5   // Ultra compact
```

---

## Prebuilt Color Palettes

Angular Material provides 12 prebuilt color palettes:

### Available Palettes
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

### Palette Examples

**Professional Blue Theme:**
```scss
:root {
  @include mat.theme((
    color: (
      primary: mat.$azure-palette,
      secondary: mat.$blue-palette,
      tertiary: mat.$cyan-palette,
      theme-type: light,
    ),
    typography: Roboto,
    density: 0,
  ));
}
```

**Warm Orange Theme:**
```scss
:root {
  @include mat.theme((
    color: (
      primary: mat.$orange-palette,
      secondary: mat.$yellow-palette,
      tertiary: mat.$red-palette,
      theme-type: light,
    ),
    typography: Roboto,
    density: 0,
  ));
}
```

**Nature Green Theme:**
```scss
:root {
  @include mat.theme((
    color: (
      primary: mat.$green-palette,
      secondary: mat.$spring-green-palette,
      tertiary: mat.$chartreuse-palette,
      theme-type: light,
    ),
    typography: Roboto,
    density: 0,
  ));
}
```

---

## Applying Themes to Components

### Using System Tokens

Material Design 3 generates CSS custom properties that you can use in your components:

```scss
.my-component {
  // Background colors
  background: var(--mat-sys-surface);
  
  // Text colors
  color: var(--mat-sys-on-surface);
  
  // Border colors
  border: 1px solid var(--mat-sys-outline);
  
  // Primary colors
  .primary-section {
    background: var(--mat-sys-primary-container);
    color: var(--mat-sys-on-primary-container);
  }
  
  // Typography
  h1 {
    font: var(--mat-sys-headline-large);
    color: var(--mat-sys-primary);
  }
  
  p {
    font: var(--mat-sys-body-medium);
  }
}
```

### Component Color Attributes

Material components support color attributes:

```html
<!-- Primary color -->
<button mat-raised-button color="primary">Primary</button>

<!-- Accent/Secondary color -->
<button mat-raised-button color="accent">Accent</button>

<!-- Warning color -->
<button mat-raised-button color="warn">Warning</button>

<!-- Default color -->
<button mat-raised-button>Default</button>
```

---

## Theme Customization Basics

### Creating Theme Variations

You can create different theme variations for different sections:

```scss
// Main theme
:root {
  @include mat.theme((
    color: mat.$blue-palette,
    typography: Roboto,
    density: 0,
  ));
}

// Header theme
.header-theme {
  @include mat.theme((
    color: mat.$violet-palette,
  ));
}

// Sidebar theme
.sidebar-theme {
  @include mat.theme((
    color: (
      primary: mat.$green-palette,
      theme-type: dark,
    ),
  ));
}
```

### Using Theme Variations

```html
<header class="header-theme">
  <mat-toolbar color="primary">
    <span>My App</span>
  </mat-toolbar>
</header>

<div class="main-content">
  <!-- Uses main theme -->
  <button mat-raised-button color="primary">Main Theme Button</button>
</div>

<aside class="sidebar-theme">
  <!-- Uses sidebar theme -->
  <button mat-raised-button color="primary">Sidebar Theme Button</button>
</aside>
```

---

## Light and Dark Mode Setup

### Basic Light/Dark Theme

```scss
@use '@angular/material' as mat;

html, body { height: 100%; }
body { 
  margin: 0; 
  font-family: Roboto, "Helvetica Neue", sans-serif; 
}

// Enable light/dark mode switching
:root {
  color-scheme: light dark;
  
  @include mat.theme((
    color: mat.$azure-palette,
    typography: Roboto,
    density: 0,
  ));
}

// Apply theme background and text colors
body {
  background: var(--mat-sys-background);
  color: var(--mat-sys-on-background);
}
```

### Manual Dark Mode Override

```scss
// Light theme (default)
:root {
  color-scheme: light;
  
  @include mat.theme((
    color: (
      primary: mat.$blue-palette,
      theme-type: light,
    ),
    typography: Roboto,
    density: 0,
  ));
}

// Dark theme
body.dark-theme {
  color-scheme: dark;
  
  @include mat.theme((
    color: (
      primary: mat.$blue-palette,
      theme-type: dark,
    ),
  ));
}
```

---

## Practical Exercises

### Exercise 1: Theme Exploration
1. Create a new Angular project with Material
2. Try each of the 12 prebuilt palettes
3. Document your observations about each palette
4. Choose your three favorites

**Goal:** Understand the visual differences between palettes

### Exercise 2: Multi-Component Theme Test
Create a comprehensive test page with various Material components:

```html
<div class="theme-test-page">
  <h1>Theme Test Page</h1>
  
  <!-- Buttons -->
  <section class="test-section">
    <h2>Buttons</h2>
    <button mat-button>Basic</button>
    <button mat-raised-button color="primary">Primary</button>
    <button mat-stroked-button color="accent">Accent</button>
    <button mat-flat-button color="warn">Warning</button>
  </section>
  
  <!-- Form Fields -->
  <section class="test-section">
    <h2>Form Fields</h2>
    <mat-form-field>
      <mat-label>Input</mat-label>
      <input matInput>
    </mat-form-field>
    
    <mat-form-field>
      <mat-label>Select</mat-label>
      <mat-select>
        <mat-option value="option1">Option 1</mat-option>
        <mat-option value="option2">Option 2</mat-option>
      </mat-select>
    </mat-form-field>
  </section>
  
  <!-- Cards -->
  <section class="test-section">
    <h2>Cards</h2>
    <mat-card>
      <mat-card-header>
        <mat-card-title>Card Title</mat-card-title>
      </mat-card-header>
      <mat-card-content>
        <p>Card content goes here.</p>
      </mat-card-content>
    </mat-card>
  </section>
</div>
```

Test this page with different color palettes and density settings.

### Exercise 3: Custom Theme Creation
1. Create a theme that matches your favorite brand colors
2. Test it with the comprehensive test page
3. Adjust colors if needed for accessibility
4. Document your theme configuration

### Exercise 4: Light/Dark Mode Toggle
Create a simple toggle to switch between light and dark modes:

**Component:**
```typescript
@Component({
  selector: 'app-theme-toggle',
  template: `
    <button mat-icon-button (click)="toggleTheme()">
      <mat-icon>{{ isDarkMode ? 'light_mode' : 'dark_mode' }}</mat-icon>
    </button>
  `,
  standalone: true,
  imports: [MatButtonModule, MatIconModule]
})
export class ThemeToggleComponent {
  isDarkMode = false;
  
  toggleTheme() {
    this.isDarkMode = !this.isDarkMode;
    document.body.classList.toggle('dark-theme', this.isDarkMode);
  }
}
```

---

## Common Pitfalls and Solutions

### Pitfall 1: Theme Not Applied
**Problem:** Colors don't change when switching themes
**Solution:** Ensure themes are applied to the correct selector (`:root` for global, specific class for scoped)

### Pitfall 2: Component Colors Not Working
**Problem:** `color="primary"` attribute not working
**Solution:** Verify the component supports the color attribute and the theme defines primary colors

### Pitfall 3: Custom Colors Not Accessible
**Problem:** Poor contrast between text and background
**Solution:** Use Material's color system and test with accessibility tools

### Pitfall 4: Inconsistent Styling
**Problem:** Some components look different than others
**Solution:** Use system tokens consistently and avoid hardcoding colors

---

## 📝 Key Takeaways

- Material Design 3 themes consist of color, typography, and density configurations
- Prebuilt palettes provide professional color schemes out of the box
- System tokens (CSS custom properties) ensure consistent theming
- Light/dark mode is built into the system using `color-scheme` and `light-dark()`
- Theme variations can be applied to specific sections of your app
- Always test themes with comprehensive component sets

---

## 🔗 Additional Resources

- [Material Design Color System](https://m3.material.io/styles/color/system/overview)
- [Angular Material Theming Guide](https://material.angular.dev/guide/theming)
- [CSS `color-scheme` Property](https://developer.mozilla.org/en-US/docs/Web/CSS/color-scheme)
- [WebAIM Contrast Checker](https://webaim.org/resources/contrastchecker/)

---

## ✅ Module Completion Checklist

- [ ] Created basic theme with prebuilt palette
- [ ] Understand theme configuration structure
- [ ] Tested multiple color palettes
- [ ] Applied themes to various Material components
- [ ] Used system tokens in custom components
- [ ] Implemented light/dark mode switching
- [ ] Created comprehensive theme test page
- [ ] Understand common pitfalls and solutions
- [ ] Ready to move to System Tokens deep dive

---

**Next Module:** [04. System Tokens](../04-system-tokens/README.md)
