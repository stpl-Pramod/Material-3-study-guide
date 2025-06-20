# 02. Setup & Configuration - Angular Material 3

## 🎯 Learning Objectives
After completing this module, you will be able to:
- Set up a new Angular project with Angular Material
- Configure the development environment for Material Design 3
- Understand the project structure and key files
- Install and configure necessary dependencies
- Create your first Material-themed component

## 📚 Table of Contents
1. [Prerequisites](#prerequisites)
2. [Creating a New Angular Project](#creating-a-new-angular-project)
3. [Installing Angular Material](#installing-angular-material)
4. [Project Structure Overview](#project-structure-overview)
5. [Configuration Files](#configuration-files)
6. [Essential Dependencies](#essential-dependencies)
7. [Development Environment Setup](#development-environment-setup)
8. [Practical Exercises](#practical-exercises)

---

## Prerequisites

Before starting, ensure you have the following installed:

### Required Software
- **Node.js** (v18.10+ recommended)
- **npm** (comes with Node.js) or **yarn**
- **Angular CLI** (latest version)

### Verification Commands
```bash
node --version    # Should show v18.10+
npm --version     # Should show 8.0+
ng version        # Should show Angular CLI 17+
```

### Install Angular CLI (if not installed)
```bash
npm install -g @angular/cli@latest
```

---

## Creating a New Angular Project

### Step 1: Generate New Project
```bash
ng new my-material-app --routing --style=scss
cd my-material-app
```

### Step 2: Serve the Application
```bash
ng serve
```
Navigate to `http://localhost:4200` to verify the basic Angular app is running.

---

## Installing Angular Material

### Method 1: Using Angular Schematics (Recommended)
```bash
ng add @angular/material
```

This command will:
- Install Angular Material, CDK, and Animations
- Add theme files to your project
- Configure necessary imports
- Add Material Icons font
- Add Roboto font
- Set up global styles

### Installation Options
When prompted, choose:
1. **Theme**: Custom (for maximum control)
2. **Typography**: Yes
3. **Animations**: Yes

### Method 2: Manual Installation
```bash
npm install @angular/material @angular/cdk @angular/animations
```

Then manually configure imports and styles (not recommended for beginners).

---

## Project Structure Overview

After installation, your project structure should look like this:

```
my-material-app/
├── src/
│   ├── app/
│   │   ├── app.component.html
│   │   ├── app.component.scss
│   │   ├── app.component.ts
│   │   ├── app.config.ts          # App configuration
│   │   └── app.routes.ts          # Routing configuration
│   ├── styles.scss                # Global styles
│   ├── index.html                # Main HTML file
│   └── main.ts                   # Bootstrap file
├── angular.json                   # Angular CLI configuration
├── package.json                  # Dependencies
└── tsconfig.json                 # TypeScript configuration
```

### Key Files for Material Theming

#### `src/styles.scss`
Global styles and theme configuration:
```scss
// Custom Theming for Angular Material
@use '@angular/material' as mat;

html, body { height: 100%; }
body { 
  margin: 0; 
  font-family: Roboto, "Helvetica Neue", sans-serif; 
}

:root {
  @include mat.theme((
    color: mat.$azure-palette,
    typography: Roboto,
    density: 0,
  ));
}
```

#### `src/index.html`
HTML template with font links:
```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>MyMaterialApp</title>
  <base href="/">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="icon" type="image/x-icon" href="favicon.ico">
  <link href="https://fonts.googleapis.com/css2?family=Roboto:wght@300;400;500&display=swap" rel="stylesheet">
  <link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet">
</head>
<body class="mat-typography">
  <app-root></app-root>
</body>
</html>
```

#### `src/app/app.config.ts`
Application configuration with providers:
```typescript
import { ApplicationConfig, provideZoneChangeDetection } from '@angular/core';
import { provideRouter } from '@angular/router';
import { provideAnimationsAsync } from '@angular/platform-browser/animations/async';

import { routes } from './app.routes';

export const appConfig: ApplicationConfig = {
  providers: [
    provideZoneChangeDetection({ eventCoalescing: true }), 
    provideRouter(routes),
    provideAnimationsAsync()
  ]
};
```

---

## Configuration Files

### Angular.json Configuration

#### Adding SCSS Include Paths
For better organization, add SCSS include paths:
```json
{
  "projects": {
    "my-material-app": {
      "architect": {
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
    }
  }
}
```

#### Font Configuration (Optional - for self-hosted fonts)
```json
{
  "projects": {
    "my-material-app": {
      "architect": {
        "build": {
          "options": {
            "styles": [
              "src/styles.scss",
              "node_modules/roboto-fontface/css/roboto/roboto-fontface.css",
              "node_modules/material-icons/iconfont/material-icons.css"
            ]
          }
        }
      }
    }
  }
}
```

### Package.json Dependencies

After installation, verify these dependencies are present:
```json
{
  "dependencies": {
    "@angular/animations": "^17.0.0",
    "@angular/cdk": "^17.0.0",
    "@angular/material": "^17.0.0",
    // ... other dependencies
  }
}
```

---

## Essential Dependencies

### Core Material Dependencies
- `@angular/material`: Main Material components library
- `@angular/cdk`: Component Development Kit (utilities)
- `@angular/animations`: Required for Material animations

### Optional but Recommended
```bash
# Self-hosted fonts (alternative to Google Fonts CDN)
npm install roboto-fontface material-icons material-symbols

# For advanced theming
npm install sass

# For development and testing
npm install --save-dev @angular/cli@latest
```

### Font Package Configuration
If using self-hosted fonts, update `angular.json`:
```json
{
  "styles": [
    "src/styles.scss",
    "node_modules/roboto-fontface/css/roboto/roboto-fontface.css",
    "node_modules/material-icons/iconfont/material-icons.css",
    "node_modules/material-symbols/index.scss"
  ]
}
```

And remove Google Fonts links from `index.html`.

---

## Development Environment Setup

### VS Code Extensions (Recommended)
- **Angular Language Service**: Enhanced Angular support
- **SCSS IntelliSense**: SCSS autocomplete and validation
- **Material Icon Theme**: Better file icons
- **Angular Schematics**: GUI for Angular schematics

### Browser Tools
- **Chrome DevTools**: For debugging and CSS inspection
- **Angular DevTools**: Browser extension for Angular debugging
- **WAVE**: Web accessibility evaluation tool

### Useful Scripts
Add these scripts to `package.json`:
```json
{
  "scripts": {
    "ng": "ng",
    "start": "ng serve",
    "build": "ng build",
    "watch": "ng build --watch --configuration development",
    "test": "ng test",
    "serve:prod": "ng serve --configuration production",
    "build:analyze": "ng build --stats-json && npx webpack-bundle-analyzer dist/my-material-app/stats.json"
  }
}
```

---

## Practical Exercises

### Exercise 1: Setup Verification
1. Create a new Angular project with Material
2. Verify all dependencies are installed correctly
3. Start the development server
4. Confirm the application loads without errors

### Exercise 2: Basic Material Component
1. Create a simple component using Material buttons
2. Add it to your app component
3. Verify Material styling is applied

**app.component.html:**
```html
<div class="container">
  <h1>Welcome to Material Design 3</h1>
  <div class="button-row">
    <button mat-raised-button color="primary">Primary</button>
    <button mat-raised-button color="accent">Accent</button>
    <button mat-raised-button color="warn">Warn</button>
  </div>
</div>
```

**app.component.ts:**
```typescript
import { Component } from '@angular/core';
import { MatButtonModule } from '@angular/material/button';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [MatButtonModule],
  templateUrl: './app.component.html',
  styleUrl: './app.component.scss'
})
export class AppComponent {
  title = 'my-material-app';
}
```

### Exercise 3: Theme Customization
1. Modify the theme in `styles.scss`
2. Change the color palette to a different preset
3. Observe how the components update automatically

### Exercise 4: Development Workflow
1. Set up your preferred IDE with extensions
2. Configure linting and formatting
3. Create a development checklist for future projects

---

## Common Issues and Solutions

### Issue 1: Module Import Errors
**Problem:** Components not rendering or import errors
**Solution:** Ensure you're importing Material modules in your components:
```typescript
import { MatButtonModule } from '@angular/material/button';
```

### Issue 2: Animations Not Working
**Problem:** Material animations not functioning
**Solution:** Verify `provideAnimationsAsync()` is in your app config:
```typescript
import { provideAnimationsAsync } from '@angular/platform-browser/animations/async';
```

### Issue 3: Fonts Not Loading
**Problem:** Roboto font not applied
**Solution:** Check that font links are in `index.html` and `mat-typography` class is on body.

### Issue 4: Theme Not Applied
**Problem:** Custom theme colors not showing
**Solution:** Ensure theme is included in `:root` selector in `styles.scss`.

---

## 📝 Key Takeaways

- Angular Material setup is streamlined with `ng add @angular/material`
- Proper project structure is crucial for maintainable theming
- Configuration files control fonts, styles, and build processes
- Development environment setup improves productivity
- Always verify setup with basic Material components

---

## 🔗 Additional Resources

- [Angular Material Getting Started](https://material.angular.dev/guide/getting-started)
- [Angular CLI Documentation](https://angular.io/cli)
- [Angular Material Schematics](https://material.angular.dev/guide/schematics)
- [SCSS Documentation](https://sass-lang.com/documentation)

---

## ✅ Module Completion Checklist

- [ ] Node.js and Angular CLI installed and verified
- [ ] New Angular project created successfully
- [ ] Angular Material installed via `ng add`
- [ ] Project structure understood
- [ ] Configuration files reviewed and modified
- [ ] Development environment configured
- [ ] Basic Material component created and tested
- [ ] Common issues and solutions reviewed
- [ ] Ready to move to Basic Theming

---

**Next Module:** [03. Basic Theming](../03-basic-theming/README.md)
