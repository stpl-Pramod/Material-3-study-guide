# Troubleshooting Guide - Angular Material 3

## 🚨 Common Issues and Solutions

### Installation Issues

#### Issue: `ng add @angular/material` fails
**Symptoms:**
- Command fails with dependency errors
- Packages not installed correctly

**Solutions:**
```bash
# Clear npm cache g
npm cache clean --force

# Update Angular CLI
npm install -g @angular/cli@latest

# Install manually if needed
npm install @angular/material @angular/cdk @angular/animations

# Check Node.js version (should be 18.10+)
node --version
```

#### Issue: Animations not working
**Symptoms:**
- Material components appear without animations
- Console warnings about animations

**Solutions:**
```typescript
// Ensure in app.config.ts or main.ts
import { provideAnimationsAsync } from '@angular/platform-browser/animations/async';

export const appConfig: ApplicationConfig = {
  providers: [
    provideAnimationsAsync(),
    // other providers
  ]
};
```

---

### Theming Issues

#### Issue: Theme colors not applied
**Symptoms:**
- Components show default colors instead of custom theme
- CSS custom properties not generated

**Solutions:**
```scss
// ❌ Wrong - missing @use
html {
  @include mat.theme((
    color: mat.$blue-palette,
  ));
}

// ✅ Correct - proper import
@use '@angular/material' as mat;

:root {
  @include mat.theme((
    color: mat.$blue-palette,
    typography: Roboto,
    density: 0,
  ));
}
```

#### Issue: Dark mode not working
**Symptoms:**
- Dark theme doesn't activate
- Colors don't switch

**Solutions:**
```scss
// ❌ Wrong - missing color-scheme
:root {
  @include mat.theme((
    color: mat.$blue-palette,
  ));
}

// ✅ Correct - with color-scheme
:root {
  color-scheme: light dark;
  @include mat.theme((
    color: mat.$blue-palette,
  ));
}

// Also ensure body background
body {
  background: var(--mat-sys-background);
  color: var(--mat-sys-on-background);
}
```

#### Issue: Custom colors not accessible
**Symptoms:**
- Poor contrast ratios
- Accessibility warnings

**Solutions:**
```scss
// Test contrast ratios
// Use Material's built-in color system
// Avoid hardcoded colors

// ❌ Wrong - hardcoded colors
.my-component {
  background: #ff0000;
  color: #ffffff;
}

// ✅ Correct - system tokens
.my-component {
  background: var(--mat-sys-error-container);
  color: var(--mat-sys-on-error-container);
}
```

---

### Component Issues

#### Issue: Material components not styled
**Symptoms:**
- Components appear unstyled
- Missing Material Design appearance

**Solutions:**
```typescript
// ❌ Wrong - missing imports
@Component({
  selector: 'app-example',
  template: '<button mat-raised-button>Click me</button>',
  // Missing MatButtonModule import
})

// ✅ Correct - proper imports
@Component({
  selector: 'app-example',
  standalone: true,
  imports: [MatButtonModule],
  template: '<button mat-raised-button>Click me</button>',
})
```

#### Issue: Icons not displaying
**Symptoms:**
- Material icons show as text
- Missing icon fonts

**Solutions:**
```html
<!-- Ensure in index.html -->
<link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet">

<!-- Or for self-hosted fonts in angular.json -->
"styles": [
  "src/styles.scss",
  "node_modules/material-icons/iconfont/material-icons.css"
]
```

#### Issue: Form fields looking wrong
**Symptoms:**
- Form fields not styled properly
- Missing Material appearance

**Solutions:**
```html
<!-- ❌ Wrong - missing mat-form-field -->
<input matInput placeholder="Name">

<!-- ✅ Correct - wrapped in mat-form-field -->
<mat-form-field>
  <mat-label>Name</mat-label>
  <input matInput>
</mat-form-field>
```

---

### SCSS Compilation Issues

#### Issue: SCSS compilation errors
**Symptoms:**
- Build fails with SCSS errors
- `@use` or `@import` errors

**Solutions:**
```scss
// ❌ Wrong - old @import syntax
@import '~@angular/material/theming';

// ✅ Correct - new @use syntax
@use '@angular/material' as mat;
```

#### Issue: Include paths not working
**Symptoms:**
- Cannot import SCSS files
- Module not found errors

**Solutions:**
```json
// In angular.json
{
  "build": {
    "options": {
      "stylePreprocessorOptions": {
        "includePaths": [
          "src/styles"
        ]
      }
    }
  }
}
```

---

### Performance Issues

#### Issue: Large bundle size
**Symptoms:**
- Slow app loading
- Large main.js file

**Solutions:**
```typescript
// ❌ Wrong - importing entire Material module
import * as Material from '@angular/material';

// ✅ Correct - import only needed modules
import { MatButtonModule } from '@angular/material/button';
import { MatCardModule } from '@angular/material/card';

// Use standalone components
@Component({
  standalone: true,
  imports: [MatButtonModule, MatCardModule],
  // ...
})
```

#### Issue: Font loading issues
**Symptoms:**
- Flash of unstyled text (FOUT)
- Slow font loading

**Solutions:**
```html
<!-- Preload critical fonts -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>

<!-- Or use self-hosted fonts -->
```

---

### Development Environment Issues

#### Issue: VS Code not recognizing SCSS
**Symptoms:**
- No autocomplete for SCSS
- Syntax errors not highlighted

**Solutions:**
- Install "SCSS IntelliSense" extension
- Install "Angular Language Service" extension
- Ensure workspace settings are correct

#### Issue: Hot reload not working
**Symptoms:**
- Changes don't appear without full refresh
- Development server issues

**Solutions:**
```bash
# Clear Angular CLI cache
ng cache clean

# Restart dev server
ng serve --poll=2000

# Check for file watching issues
```

---

### Browser Compatibility Issues

#### Issue: CSS custom properties not supported
**Symptoms:**
- Theming not working in older browsers
- Styles not applied

**Solutions:**
```scss
// Add fallbacks for older browsers
.my-component {
  background: #1976d2; /* fallback */
  background: var(--mat-sys-primary);
}
```

#### Issue: `light-dark()` function not supported
**Symptoms:**
- Colors not switching between light/dark
- Browser compatibility issues

**Solutions:**
```scss
// Use explicit theme-type instead
:root {
  @include mat.theme((
    color: (
      primary: mat.$blue-palette,
      theme-type: light, // or dark
    ),
  ));
}
```

---

### Testing Issues

#### Issue: Tests failing with Material components
**Symptoms:**
- Unit tests fail with Material component errors
- Missing providers or modules

**Solutions:**
```typescript
// In test files
import { NoopAnimationsModule } from '@angular/platform-browser/animations';

TestBed.configureTestingModule({
  imports: [
    NoopAnimationsModule,
    MatButtonModule,
    // other required modules
  ],
});
```

---

## 🔧 Debugging Tools

### Browser DevTools
1. **Elements tab**: Inspect component structure
2. **Computed styles**: Check CSS custom properties
3. **Console**: Look for error messages
4. **Network tab**: Check font loading

### Angular DevTools
- Install Angular DevTools browser extension
- Inspect component tree and properties
- Debug change detection

### Accessibility Tools
- WAVE Web Accessibility Evaluator
- axe DevTools
- Lighthouse accessibility audit

---

## 📞 Getting Help

### Official Resources
- [Angular Material GitHub Issues](https://github.com/angular/components/issues)
- [Angular Material Documentation](https://material.angular.dev/)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/angular-material)

### Community
- [Angular Discord](https://discord.gg/angular)
- [Reddit r/Angular](https://www.reddit.com/r/Angular/)
- [Angular Community Slack](https://angular-community.slack.com/)

---

## 📋 Debugging Checklist

When encountering issues, check:

- [ ] Angular and Angular Material versions are compatible
- [ ] All required modules are imported
- [ ] SCSS syntax is correct (@use instead of @import)
- [ ] Theme is applied to correct selector (:root)
- [ ] Animation providers are configured
- [ ] Font files are loading correctly
- [ ] Browser supports required features
- [ ] No console errors are present
- [ ] Build process completes successfully

---

**Remember**: Most theming issues stem from incorrect imports, missing providers, or syntax errors. Always check the basics first!
