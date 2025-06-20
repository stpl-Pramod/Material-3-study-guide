# 06. Typography - Material Design 3 Text Styles

## 🎯 Learning Objectives
After completing this module, you will be able to:
- Understand the Material Design 3 typography system
- Configure and customize typography tokens
- Implement responsive typography scales
- Use typography roles effectively in components
- Create custom typography configurations
- Ensure typography accessibility and readability
- Optimize typography performance

## 📚 Table of Contents
1. [Material Design 3 Typography System](#material-design-3-typography-system)
2. [Typography Tokens and Roles](#typography-tokens-and-roles)
3. [Font Configuration](#font-configuration)
4. [Typography Scale Implementation](#typography-scale-implementation)
5. [Responsive Typography](#responsive-typography)
6. [Custom Typography Themes](#custom-typography-themes)
7. [Typography Accessibility](#typography-accessibility)
8. [Performance Optimization](#performance-optimization)
9. [Practical Exercises](#practical-exercises)

---

## Material Design 3 Typography System

Material Design 3 uses a structured typography scale with five categories and three sizes each, creating a harmonious and functional text hierarchy.

### Typography Categories

1. **Display**: The largest text for short, important text or numerals
2. **Headline**: High-emphasis text for short text like article titles
3. **Title**: Medium-emphasis text for longer text like section titles
4. **Label**: Small text used for things like captions or labels
5. **Body**: Readable text used for long-form content

### Size Variations
- **Large**: Maximum impact and emphasis
- **Medium**: Standard usage for most content
- **Small**: Subtle and space-efficient

---

## Typography Tokens and Roles

### Typography Token Structure

```scss
// Typography tokens follow this pattern:
--mat-sys-{category}-{size}-font
--mat-sys-{category}-{size}-line-height
--mat-sys-{category}-{size}-size
--mat-sys-{category}-{size}-tracking
--mat-sys-{category}-{size}-weight
```

### Complete Typography Token Reference

```scss
/* Display Typography */
--mat-sys-display-large-font: 'Roboto', sans-serif;
--mat-sys-display-large-line-height: 64px;
--mat-sys-display-large-size: 57px;
--mat-sys-display-large-tracking: -0.25px;
--mat-sys-display-large-weight: 400;

--mat-sys-display-medium-font: 'Roboto', sans-serif;
--mat-sys-display-medium-line-height: 52px;
--mat-sys-display-medium-size: 45px;
--mat-sys-display-medium-tracking: 0px;
--mat-sys-display-medium-weight: 400;

--mat-sys-display-small-font: 'Roboto', sans-serif;
--mat-sys-display-small-line-height: 44px;
--mat-sys-display-small-size: 36px;
--mat-sys-display-small-tracking: 0px;
--mat-sys-display-small-weight: 400;

/* Headline Typography */
--mat-sys-headline-large-font: 'Roboto', sans-serif;
--mat-sys-headline-large-line-height: 40px;
--mat-sys-headline-large-size: 32px;
--mat-sys-headline-large-tracking: 0px;
--mat-sys-headline-large-weight: 400;

--mat-sys-headline-medium-font: 'Roboto', sans-serif;
--mat-sys-headline-medium-line-height: 36px;
--mat-sys-headline-medium-size: 28px;
--mat-sys-headline-medium-tracking: 0px;
--mat-sys-headline-medium-weight: 400;

--mat-sys-headline-small-font: 'Roboto', sans-serif;
--mat-sys-headline-small-line-height: 32px;
--mat-sys-headline-small-size: 24px;
--mat-sys-headline-small-tracking: 0px;
--mat-sys-headline-small-weight: 400;

/* Title Typography */
--mat-sys-title-large-font: 'Roboto', sans-serif;
--mat-sys-title-large-line-height: 28px;
--mat-sys-title-large-size: 22px;
--mat-sys-title-large-tracking: 0px;
--mat-sys-title-large-weight: 400;

--mat-sys-title-medium-font: 'Roboto', sans-serif;
--mat-sys-title-medium-line-height: 24px;
--mat-sys-title-medium-size: 16px;
--mat-sys-title-medium-tracking: 0.15px;
--mat-sys-title-medium-weight: 500;

--mat-sys-title-small-font: 'Roboto', sans-serif;
--mat-sys-title-small-line-height: 20px;
--mat-sys-title-small-size: 14px;
--mat-sys-title-small-tracking: 0.1px;
--mat-sys-title-small-weight: 500;

/* Label Typography */
--mat-sys-label-large-font: 'Roboto', sans-serif;
--mat-sys-label-large-line-height: 20px;
--mat-sys-label-large-size: 14px;
--mat-sys-label-large-tracking: 0.1px;
--mat-sys-label-large-weight: 500;

--mat-sys-label-medium-font: 'Roboto', sans-serif;
--mat-sys-label-medium-line-height: 16px;
--mat-sys-label-medium-size: 12px;
--mat-sys-label-medium-tracking: 0.5px;
--mat-sys-label-medium-weight: 500;

--mat-sys-label-small-font: 'Roboto', sans-serif;
--mat-sys-label-small-line-height: 16px;
--mat-sys-label-small-size: 11px;
--mat-sys-label-small-tracking: 0.5px;
--mat-sys-label-small-weight: 500;

/* Body Typography */
--mat-sys-body-large-font: 'Roboto', sans-serif;
--mat-sys-body-large-line-height: 24px;
--mat-sys-body-large-size: 16px;
--mat-sys-body-large-tracking: 0.15px;
--mat-sys-body-large-weight: 400;

--mat-sys-body-medium-font: 'Roboto', sans-serif;
--mat-sys-body-medium-line-height: 20px;
--mat-sys-body-medium-size: 14px;
--mat-sys-body-medium-tracking: 0.25px;
--mat-sys-body-medium-weight: 400;

--mat-sys-body-small-font: 'Roboto', sans-serif;
--mat-sys-body-small-line-height: 16px;
--mat-sys-body-small-size: 12px;
--mat-sys-body-small-tracking: 0.4px;
--mat-sys-body-small-weight: 400;
```

---

## Font Configuration

### Basic Typography Configuration

```scss
@use '@angular/material' as mat;

// Configure typography
$custom-typography: mat.define-typography-config(
  $font-family: 'Inter, -apple-system, BlinkMacSystemFont, sans-serif',
  $display-4: mat.define-typography-level(112px, 112px, 300, $letter-spacing: -0.05em),
  $display-3: mat.define-typography-level(56px, 56px, 400, $letter-spacing: -0.02em),
  $display-2: mat.define-typography-level(45px, 48px, 400, $letter-spacing: -0.005em),
  $display-1: mat.define-typography-level(34px, 40px, 400),
  $headline: mat.define-typography-level(24px, 32px, 400),
  $title: mat.define-typography-level(20px, 32px, 500),
  $subheading-2: mat.define-typography-level(16px, 28px, 400),
  $subheading-1: mat.define-typography-level(15px, 24px, 400),
  $body-2: mat.define-typography-level(14px, 24px, 500),
  $body-1: mat.define-typography-level(14px, 20px, 400),
  $button: mat.define-typography-level(14px, 14px, 500),
  $caption: mat.define-typography-level(12px, 20px, 400),
  $overline: mat.define-typography-level(10px, 16px, 400, $letter-spacing: 1.5px),
);

// Apply typography
:root {
  @include mat.theme((
    color: mat.$blue-palette,
    typography: $custom-typography,
    density: 0,
  ));
}
```

### Material Design 3 Typography Configuration

```scss
@use '@angular/material' as mat;

// MD3 Typography system
$md3-typography: mat.define-typography-config(
  // Display styles
  $display-large: mat.define-typography-level(57px, 64px, 400, $letter-spacing: -0.25px),
  $display-medium: mat.define-typography-level(45px, 52px, 400),
  $display-small: mat.define-typography-level(36px, 44px, 400),
  
  // Headline styles
  $headline-large: mat.define-typography-level(32px, 40px, 400),
  $headline-medium: mat.define-typography-level(28px, 36px, 400),
  $headline-small: mat.define-typography-level(24px, 32px, 400),
  
  // Title styles
  $title-large: mat.define-typography-level(22px, 28px, 400),
  $title-medium: mat.define-typography-level(16px, 24px, 500, $letter-spacing: 0.15px),
  $title-small: mat.define-typography-level(14px, 20px, 500, $letter-spacing: 0.1px),
  
  // Label styles
  $label-large: mat.define-typography-level(14px, 20px, 500, $letter-spacing: 0.1px),
  $label-medium: mat.define-typography-level(12px, 16px, 500, $letter-spacing: 0.5px),
  $label-small: mat.define-typography-level(11px, 16px, 500, $letter-spacing: 0.5px),
  
  // Body styles
  $body-large: mat.define-typography-level(16px, 24px, 400, $letter-spacing: 0.15px),
  $body-medium: mat.define-typography-level(14px, 20px, 400, $letter-spacing: 0.25px),
  $body-small: mat.define-typography-level(12px, 16px, 400, $letter-spacing: 0.4px),
);
```

---

## Typography Scale Implementation

### Using Typography Tokens in Components

```scss
.article-layout {
  .hero-title {
    font: var(--mat-sys-display-large);
    color: var(--mat-sys-on-background);
    margin-bottom: 1rem;
  }
  
  .section-heading {
    font: var(--mat-sys-headline-medium);
    color: var(--mat-sys-on-background);
    margin: 2rem 0 1rem 0;
  }
  
  .subsection-title {
    font: var(--mat-sys-title-large);
    color: var(--mat-sys-on-surface-variant);
    margin: 1.5rem 0 0.5rem 0;
  }
  
  .body-text {
    font: var(--mat-sys-body-large);
    color: var(--mat-sys-on-background);
    line-height: 1.6;
    margin-bottom: 1rem;
  }
  
  .caption-text {
    font: var(--mat-sys-label-medium);
    color: var(--mat-sys-on-surface-variant);
  }
}
```

### Creating Typography Utility Classes

```scss
// Typography utility classes
.text-display-large {
  font: var(--mat-sys-display-large);
}

.text-display-medium {
  font: var(--mat-sys-display-medium);
}

.text-display-small {
  font: var(--mat-sys-display-small);
}

.text-headline-large {
  font: var(--mat-sys-headline-large);
}

.text-headline-medium {
  font: var(--mat-sys-headline-medium);
}

.text-headline-small {
  font: var(--mat-sys-headline-small);
}

.text-title-large {
  font: var(--mat-sys-title-large);
}

.text-title-medium {
  font: var(--mat-sys-title-medium);
}

.text-title-small {
  font: var(--mat-sys-title-small);
}

.text-body-large {
  font: var(--mat-sys-body-large);
}

.text-body-medium {
  font: var(--mat-sys-body-medium);
}

.text-body-small {
  font: var(--mat-sys-body-small);
}

.text-label-large {
  font: var(--mat-sys-label-large);
}

.text-label-medium {
  font: var(--mat-sys-label-medium);
}

.text-label-small {
  font: var(--mat-sys-label-small);
}
```

---

## Responsive Typography

### Fluid Typography Implementation

```scss
// Responsive typography using clamp()
:root {
  // Display styles scale responsively
  --mat-sys-display-large-size: clamp(2.5rem, 5vw, 3.5rem);
  --mat-sys-display-medium-size: clamp(2rem, 4vw, 2.8rem);
  --mat-sys-display-small-size: clamp(1.8rem, 3.5vw, 2.25rem);
  
  // Headline styles scale responsively
  --mat-sys-headline-large-size: clamp(1.5rem, 3vw, 2rem);
  --mat-sys-headline-medium-size: clamp(1.3rem, 2.5vw, 1.75rem);
  --mat-sys-headline-small-size: clamp(1.2rem, 2vw, 1.5rem);
  
  // Body text scales for readability
  --mat-sys-body-large-size: clamp(0.9rem, 1.5vw, 1rem);
  --mat-sys-body-medium-size: clamp(0.8rem, 1.2vw, 0.875rem);
}
```

### Breakpoint-Based Typography

```scss
// Typography adjustments for different screen sizes
.responsive-typography {
  // Mobile typography
  @media (max-width: 599px) {
    h1 { font: var(--mat-sys-headline-large); }
    h2 { font: var(--mat-sys-headline-medium); }
    h3 { font: var(--mat-sys-headline-small); }
    p { font: var(--mat-sys-body-medium); }
  }
  
  // Tablet typography
  @media (min-width: 600px) and (max-width: 1199px) {
    h1 { font: var(--mat-sys-display-small); }
    h2 { font: var(--mat-sys-headline-large); }
    h3 { font: var(--mat-sys-headline-medium); }
    p { font: var(--mat-sys-body-large); }
  }
  
  // Desktop typography
  @media (min-width: 1200px) {
    h1 { font: var(--mat-sys-display-medium); }
    h2 { font: var(--mat-sys-display-small); }
    h3 { font: var(--mat-sys-headline-large); }
    p { font: var(--mat-sys-body-large); }
  }
}
```

---

## Custom Typography Themes

### Brand Typography Implementation

```scss
@use '@angular/material' as mat;

// Custom brand typography
$brand-typography: mat.define-typography-config(
  $font-family: '"Poppins", "Inter", -apple-system, sans-serif',
  
  // Headings use display font
  $display-4: mat.define-typography-level(
    $font-size: 7rem,
    $line-height: 7rem,
    $font-weight: 300,
    $font-family: '"Playfair Display", serif',
    $letter-spacing: -0.02em
  ),
  
  // Buttons use brand font with specific weight
  $button: mat.define-typography-level(
    $font-size: 0.875rem,
    $line-height: 1.25rem,
    $font-weight: 600,
    $letter-spacing: 0.025em,
    $text-transform: uppercase
  ),
  
  // Body text optimized for readability
  $body-1: mat.define-typography-level(
    $font-size: 1rem,
    $line-height: 1.7,
    $font-weight: 400,
    $letter-spacing: 0.01em
  ),
);

// Apply brand typography
:root {
  @include mat.theme((
    typography: $brand-typography,
  ));
}
```

### Multi-Language Typography Support

```scss
// Language-specific typography
:root {
  // Default (English/Latin)
  --font-family-display: 'Inter', sans-serif;
  --font-family-body: 'Inter', sans-serif;
}

:root[lang="ja"], 
:root[lang="ko"], 
:root[lang="zh"] {
  // CJK languages
  --font-family-display: 'Noto Sans CJK', sans-serif;
  --font-family-body: 'Noto Sans CJK', sans-serif;
  
  // Adjust line height for CJK characters
  --mat-sys-body-large-line-height: 1.8;
  --mat-sys-body-medium-line-height: 1.7;
}

:root[lang="ar"], 
:root[lang="he"] {
  // RTL languages
  --font-family-display: 'Noto Sans Arabic', sans-serif;
  --font-family-body: 'Noto Sans Arabic', sans-serif;
  
  direction: rtl;
}
```

---

## Typography Accessibility

### Accessibility Best Practices

```scss
// Accessible typography implementation
.accessible-typography {
  // Minimum font sizes for readability
  font-size: max(16px, 1rem); // Never smaller than 16px
  
  // Optimal line height for readability
  line-height: clamp(1.4, 1.5, 1.8);
  
  // Sufficient color contrast
  color: var(--mat-sys-on-background);
  background: var(--mat-sys-background);
  
  // Respect user preferences
  @media (prefers-reduced-motion: reduce) {
    // Disable text animations
    * {
      animation-duration: 0.01ms !important;
      transition-duration: 0.01ms !important;
    }
  }
}
```

### User Preference Adaptations

```scss
// Font size preferences
@media (prefers-reduced-data: reduce) {
  // Use system fonts to reduce data usage
  :root {
    --font-family-display: system-ui, sans-serif;
    --font-family-body: system-ui, sans-serif;
  }
}

// High contrast preferences
@media (prefers-contrast: high) {
  :root {
    // Increase font weights for better contrast
    --mat-sys-body-large-weight: 500;
    --mat-sys-body-medium-weight: 500;
    --mat-sys-label-large-weight: 600;
  }
}

// User font size scaling
@media (min-resolution: 2dppx) {
  // Adjust for high-density displays
  :root {
    --mat-sys-body-large-size: 17px; // Slightly larger for retina
  }
}
```

---

## Performance Optimization

### Font Loading Optimization

```html
<!-- Font loading strategies -->
<head>
  <!-- Preload critical fonts -->
  <link rel="preload" href="/fonts/inter-var.woff2" as="font" type="font/woff2" crossorigin>
  
  <!-- Font display swap for better performance -->
  <style>
    @font-face {
      font-family: 'Inter';
      src: url('/fonts/inter-var.woff2') format('woff2-variations');
      font-weight: 100 900;
      font-style: normal;
      font-display: swap; /* Show fallback font immediately */
    }
  </style>
</head>
```

### CSS Font Loading

```scss
// Progressive font enhancement
.progressive-fonts {
  font-family: system-ui, sans-serif; // Fallback
  
  // Enhanced typography when custom font loads
  .fonts-loaded & {
    font-family: 'Inter', system-ui, sans-serif;
  }
}

// Font loading states
.font-loading {
  visibility: hidden;
}

.fonts-loaded .font-loading {
  visibility: visible;
}
```

### Variable Font Implementation

```scss
// Variable font usage for better performance
@supports (font-variation-settings: normal) {
  :root {
    --font-family-display: 'Inter Variable', sans-serif;
    --font-family-body: 'Inter Variable', sans-serif;
  }
  
  .variable-weight {
    font-variation-settings: 'wght' var(--font-weight, 400);
    transition: font-variation-settings 200ms ease;
  }
  
  .variable-weight:hover {
    --font-weight: 500;
  }
}
```

---

## Practical Exercises

### Exercise 1: Typography System Implementation
Create a complete typography system for a blog:

1. Set up Material Design 3 typography tokens
2. Create typography utility classes
3. Implement responsive typography scaling
4. Add accessibility enhancements
5. Test across different devices and preferences

### Exercise 2: Custom Brand Typography
Develop a custom typography theme:

1. Choose appropriate brand fonts
2. Configure typography scales
3. Create font loading strategies
4. Implement multi-language support
5. Optimize for performance

### Exercise 3: Component Typography Integration
Build a news article component with:

1. Proper typography hierarchy
2. Responsive text scaling
3. Accessibility compliance
4. Reading experience optimization
5. Print-friendly styles

### Exercise 4: Advanced Typography Features
Implement advanced typography features:

1. Variable font integration
2. Advanced font loading strategies
3. Typography animations
4. Custom font metrics
5. Performance monitoring

---

## 🔗 Additional Resources

- [Material Design 3 Typography](https://m3.material.io/styles/typography/overview)
- [Google Fonts](https://fonts.google.com/)
- [Font Loading Best Practices](https://web.dev/font-best-practices/)
- [Typography Accessibility](https://webaim.org/techniques/fonts/)
- [Variable Fonts Guide](https://web.dev/variable-fonts/)

---

## ✅ Module Completion Checklist

- [ ] Understand Material Design 3 typography system
- [ ] Can configure custom typography themes
- [ ] Implement responsive typography
- [ ] Use typography tokens effectively
- [ ] Create accessible typography
- [ ] Optimize typography performance
- [ ] Handle multi-language typography
- [ ] Complete all practical exercises

**Next Module**: [07. Density & Spacing - Controlling Component Density](../07-density-spacing/)
