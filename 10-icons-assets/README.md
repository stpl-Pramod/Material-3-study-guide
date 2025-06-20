# 10. Icons & Assets - Working with Material Icons and Custom SVGs

## 🎯 Learning Objectives
After completing this module, you will be able to:
- Integrate Material Icons effectively in themed applications
- Create and use custom SVG icons
- Implement icon theming and customization
- Optimize icon performance and loading
- Build scalable icon systems
- Create icon component libraries
- Handle icon accessibility and internationalization

## 📚 Table of Contents
1. [Material Icons Integration](#material-icons-integration)
2. [Custom SVG Icon Implementation](#custom-svg-icon-implementation)
3. [Icon Theming and Customization](#icon-theming-and-customization)
4. [Icon Component Library](#icon-component-library)
5. [Icon Performance Optimization](#icon-performance-optimization)
6. [Icon Accessibility](#icon-accessibility)
7. [Advanced Icon Techniques](#advanced-icon-techniques)
8. [Icon Asset Management](#icon-asset-management)
9. [Practical Exercises](#practical-exercises)

---

## Material Icons Integration

### Basic Material Icons Setup

```typescript
// app.module.ts
import { MatIconModule } from '@angular/material/icon';

@NgModule({
  imports: [
    MatIconModule,
    // ... other imports
  ],
})
export class AppModule {}
```

```html
<!-- index.html -->
<link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet">
<link href="https://fonts.googleapis.com/icon?family=Material+Icons+Outlined" rel="stylesheet">
<link href="https://fonts.googleapis.com/icon?family=Material+Icons+Round" rel="stylesheet">
<link href="https://fonts.googleapis.com/icon?family=Material+Icons+Sharp" rel="stylesheet">
<link href="https://fonts.googleapis.com/icon?family=Material+Icons+Two+Tone" rel="stylesheet">
```

### Using Material Icons in Components

```html
<!-- Basic usage -->
<mat-icon>home</mat-icon>
<mat-icon>favorite</mat-icon>
<mat-icon>settings</mat-icon>

<!-- Different icon styles -->
<mat-icon fontSet="material-icons-outlined">home</mat-icon>
<mat-icon fontSet="material-icons-round">favorite</mat-icon>
<mat-icon fontSet="material-icons-sharp">settings</mat-icon>

<!-- With buttons -->
<button mat-icon-button>
  <mat-icon>menu</mat-icon>
</button>

<button mat-raised-button>
  <mat-icon>save</mat-icon>
  Save
</button>

<!-- With form fields -->
<mat-form-field>
  <mat-label>Search</mat-label>
  <input matInput>
  <mat-icon matPrefix>search</mat-icon>
  <mat-icon matSuffix>clear</mat-icon>
</mat-form-field>
```

### Icon Sizing and Styling

```scss
.icon-sizing {
  // Size classes
  .icon-small {
    .mat-icon {
      font-size: 16px;
      width: 16px;
      height: 16px;
    }
  }
  
  .icon-medium {
    .mat-icon {
      font-size: 24px; // Default
      width: 24px;
      height: 24px;
    }
  }
  
  .icon-large {
    .mat-icon {
      font-size: 32px;
      width: 32px;
      height: 32px;
    }
  }
  
  .icon-xl {
    .mat-icon {
      font-size: 48px;
      width: 48px;
      height: 48px;
    }
  }
}

// Icon color theming
.icon-colors {
  .icon-primary {
    .mat-icon {
      color: var(--mat-sys-primary);
    }
  }
  
  .icon-secondary {
    .mat-icon {
      color: var(--mat-sys-secondary);
    }
  }
  
  .icon-error {
    .mat-icon {
      color: var(--mat-sys-error);
    }
  }
  
  .icon-success {
    .mat-icon {
      color: #4caf50;
    }
  }
  
  .icon-warning {
    .mat-icon {
      color: #ff9800;
    }
  }
}
```

---

## Custom SVG Icon Implementation

### SVG Icon Registration

```typescript
// icon.service.ts
import { Injectable } from '@angular/core';
import { MatIconRegistry } from '@angular/material/icon';
import { DomSanitizer } from '@angular/platform-browser';

@Injectable({
  providedIn: 'root'
})
export class IconService {
  constructor(
    private matIconRegistry: MatIconRegistry,
    private domSanitizer: DomSanitizer
  ) {
    this.registerIcons();
  }

  private registerIcons(): void {
    // Register individual SVG icons
    this.matIconRegistry.addSvgIcon(
      'custom-logo',
      this.domSanitizer.bypassSecurityTrustResourceUrl('assets/icons/logo.svg')
    );

    this.matIconRegistry.addSvgIcon(
      'custom-user',
      this.domSanitizer.bypassSecurityTrustResourceUrl('assets/icons/user.svg')
    );

    // Register icon sets
    this.matIconRegistry.addSvgIconSet(
      this.domSanitizer.bypassSecurityTrustResourceUrl('assets/icons/custom-icons.svg')
    );

    this.matIconRegistry.addSvgIconSetInNamespace(
      'custom',
      this.domSanitizer.bypassSecurityTrustResourceUrl('assets/icons/custom-set.svg')
    );
  }

  // Register icons dynamically
  registerIcon(name: string, path: string, namespace?: string): void {
    if (namespace) {
      this.matIconRegistry.addSvgIconInNamespace(
        namespace,
        name,
        this.domSanitizer.bypassSecurityTrustResourceUrl(path)
      );
    } else {
      this.matIconRegistry.addSvgIcon(
        name,
        this.domSanitizer.bypassSecurityTrustResourceUrl(path)
      );
    }
  }
}
```

### Using Custom SVG Icons

```html
<!-- Custom SVG icons -->
<mat-icon svgIcon="custom-logo"></mat-icon>
<mat-icon svgIcon="custom-user"></mat-icon>

<!-- Icons from namespace -->
<mat-icon svgIcon="custom:icon-name"></mat-icon>

<!-- Inline SVG icons -->
<mat-icon>
  <svg viewBox="0 0 24 24">
    <path d="M12 2l3.09 6.26L22 9.27l-5 4.87 1.18 6.88L12 17.77l-6.18 3.25L7 14.14 2 9.27l6.91-1.01L12 2z"/>
  </svg>
</mat-icon>
```

### SVG Icon Optimization

```typescript
// Optimized SVG icon component
import { Component, Input, ChangeDetectionStrategy } from '@angular/core';

@Component({
  selector: 'app-svg-icon',
  template: `
    <svg
      [attr.width]="size"
      [attr.height]="size"
      [attr.viewBox]="viewBox"
      [attr.fill]="fill"
      [attr.stroke]="stroke"
      [attr.aria-label]="ariaLabel"
      [attr.role]="ariaLabel ? 'img' : 'presentation'"
    >
      <ng-content></ng-content>
    </svg>
  `,
  styles: [`
    svg {
      display: inline-block;
      vertical-align: middle;
    }
  `],
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class SvgIconComponent {
  @Input() size: string | number = 24;
  @Input() viewBox = '0 0 24 24';
  @Input() fill = 'currentColor';
  @Input() stroke = 'none';
  @Input() ariaLabel?: string;
}
```

---

## Icon Theming and Customization

### Themed Icon Components

```scss
// Icon theming system
.themed-icons {
  // Primary themed icons
  .icon-primary {
    color: var(--mat-sys-primary);
    
    &:hover {
      color: var(--mat-sys-primary-variant);
    }
  }
  
  // Secondary themed icons
  .icon-secondary {
    color: var(--mat-sys-secondary);
    
    &:hover {
      color: var(--mat-sys-secondary-variant);
    }
  }
  
  // Surface themed icons
  .icon-on-surface {
    color: var(--mat-sys-on-surface);
    opacity: 0.87;
    
    &.low-emphasis {
      opacity: 0.6;
    }
    
    &.medium-emphasis {
      opacity: 0.74;
    }
  }
  
  // Container themed icons
  .icon-on-primary {
    color: var(--mat-sys-on-primary);
  }
  
  .icon-on-secondary {
    color: var(--mat-sys-on-secondary);
  }
}

// Icon state variations
.icon-states {
  .icon-button {
    .mat-icon {
      transition: all 200ms ease;
      
      &:hover {
        transform: scale(1.1);
      }
      
      &:active {
        transform: scale(0.95);
      }
    }
  }
  
  .icon-disabled {
    .mat-icon {
      color: var(--mat-sys-on-surface);
      opacity: 0.38;
    }
  }
  
  .icon-loading {
    .mat-icon {
      animation: spin 1s linear infinite;
    }
  }
}

@keyframes spin {
  from { transform: rotate(0deg); }
  to { transform: rotate(360deg); }
}
```

### Dynamic Icon Theming

```typescript
// Dynamic icon theming service
import { Injectable } from '@angular/core';
import { BehaviorSubject } from 'rxjs';

export interface IconTheme {
  primary: string;
  secondary: string;
  surface: string;
  error: string;
  success: string;
  warning: string;
}

@Injectable({
  providedIn: 'root'
})
export class IconThemeService {
  private defaultTheme: IconTheme = {
    primary: 'var(--mat-sys-primary)',
    secondary: 'var(--mat-sys-secondary)',
    surface: 'var(--mat-sys-on-surface)',
    error: 'var(--mat-sys-error)',
    success: '#4caf50',
    warning: '#ff9800'
  };

  private currentTheme = new BehaviorSubject<IconTheme>(this.defaultTheme);
  public theme$ = this.currentTheme.asObservable();

  setTheme(theme: Partial<IconTheme>): void {
    const newTheme = { ...this.defaultTheme, ...theme };
    this.currentTheme.next(newTheme);
  }

  getIconColor(type: keyof IconTheme): string {
    return this.currentTheme.value[type];
  }
}
```

---

## Icon Component Library

### Comprehensive Icon Component

```typescript
import { Component, Input, Output, EventEmitter, ChangeDetectionStrategy } from '@angular/core';

export type IconSize = 'xs' | 'sm' | 'md' | 'lg' | 'xl';
export type IconColor = 'primary' | 'secondary' | 'surface' | 'error' | 'success' | 'warning';
export type IconVariant = 'filled' | 'outlined' | 'round' | 'sharp' | 'two-tone';

@Component({
  selector: 'app-icon',
  template: `
    <mat-icon
      [class]="iconClasses"
      [fontSet]="fontSet"
      [svgIcon]="svgIcon"
      [attr.aria-label]="ariaLabel"
      [attr.role]="ariaLabel ? 'img' : 'presentation'"
      (click)="handleClick($event)"
    >
      {{name}}
    </mat-icon>
  `,
  styles: [`
    .mat-icon {
      transition: all 200ms ease;
      cursor: inherit;
      
      &.clickable {
        cursor: pointer;
        
        &:hover {
          transform: scale(1.1);
        }
        
        &:active {
          transform: scale(0.95);
        }
      }
      
      &.spinning {
        animation: spin 1s linear infinite;
      }
      
      // Size variants
      &.size-xs { font-size: 12px; width: 12px; height: 12px; }
      &.size-sm { font-size: 16px; width: 16px; height: 16px; }
      &.size-md { font-size: 24px; width: 24px; height: 24px; }
      &.size-lg { font-size: 32px; width: 32px; height: 32px; }
      &.size-xl { font-size: 48px; width: 48px; height: 48px; }
      
      // Color variants
      &.color-primary { color: var(--mat-sys-primary); }
      &.color-secondary { color: var(--mat-sys-secondary); }
      &.color-surface { color: var(--mat-sys-on-surface); }
      &.color-error { color: var(--mat-sys-error); }
      &.color-success { color: #4caf50; }
      &.color-warning { color: #ff9800; }
    }
    
    @keyframes spin {
      from { transform: rotate(0deg); }
      to { transform: rotate(360deg); }
    }
  `],
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class IconComponent {
  @Input() name: string = '';
  @Input() size: IconSize = 'md';
  @Input() color: IconColor = 'surface';
  @Input() variant: IconVariant = 'filled';
  @Input() svgIcon?: string;
  @Input() spinning = false;
  @Input() clickable = false;
  @Input() ariaLabel?: string;
  
  @Output() iconClick = new EventEmitter<MouseEvent>();

  get fontSet(): string {
    const variantMap = {
      'filled': 'material-icons',
      'outlined': 'material-icons-outlined',
      'round': 'material-icons-round',
      'sharp': 'material-icons-sharp',
      'two-tone': 'material-icons-two-tone'
    };
    return variantMap[this.variant];
  }

  get iconClasses(): string {
    const classes = [
      `size-${this.size}`,
      `color-${this.color}`
    ];
    
    if (this.spinning) classes.push('spinning');
    if (this.clickable) classes.push('clickable');
    
    return classes.join(' ');
  }

  handleClick(event: MouseEvent): void {
    if (this.clickable) {
      this.iconClick.emit(event);
    }
  }
}
```

### Icon Button Component

```typescript
import { Component, Input, Output, EventEmitter, ChangeDetectionStrategy } from '@angular/core';

@Component({
  selector: 'app-icon-button',
  template: `
    <button
      mat-icon-button
      [class]="buttonClasses"
      [disabled]="disabled"
      [attr.aria-label]="ariaLabel"
      (click)="handleClick($event)"
    >
      <app-icon
        [name]="icon"
        [size]="iconSize"
        [color]="iconColor"
        [variant]="iconVariant"
        [spinning]="loading"
        [svgIcon]="svgIcon"
        [ariaLabel]="ariaLabel"
      ></app-icon>
    </button>
  `,
  styles: [`
    .mat-mdc-icon-button {
      transition: all 200ms ease;
      
      &.size-sm {
        width: 32px;
        height: 32px;
      }
      
      &.size-lg {
        width: 56px;
        height: 56px;
      }
      
      &.variant-filled {
        background: var(--mat-sys-primary);
        color: var(--mat-sys-on-primary);
        
        &:hover {
          background: var(--mat-sys-primary-variant);
        }
      }
      
      &.variant-outlined {
        border: 1px solid var(--mat-sys-outline);
        
        &:hover {
          background: var(--mat-sys-primary-container);
        }
      }
      
      &.variant-elevated {
        box-shadow: 0 2px 8px rgba(0,0,0,0.12);
        
        &:hover {
          box-shadow: 0 4px 12px rgba(0,0,0,0.16);
        }
      }
    }
  `],
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class IconButtonComponent {
  @Input() icon: string = '';
  @Input() svgIcon?: string;
  @Input() size: 'sm' | 'md' | 'lg' = 'md';
  @Input() variant: 'standard' | 'filled' | 'outlined' | 'elevated' = 'standard';
  @Input() disabled = false;
  @Input() loading = false;
  @Input() ariaLabel?: string;
  
  @Output() buttonClick = new EventEmitter<MouseEvent>();

  get iconSize(): IconSize {
    const sizeMap = { 'sm': 'sm', 'md': 'md', 'lg': 'lg' } as const;
    return sizeMap[this.size];
  }

  get iconColor(): IconColor {
    return this.variant === 'filled' ? 'surface' : 'primary';
  }

  get iconVariant(): IconVariant {
    return 'filled';
  }

  get buttonClasses(): string {
    return `size-${this.size} variant-${this.variant}`;
  }

  handleClick(event: MouseEvent): void {
    this.buttonClick.emit(event);
  }
}
```

---

## Icon Performance Optimization

### Icon Lazy Loading

```typescript
// Icon lazy loading service
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class IconLoaderService {
  private loadedSets = new Set<string>();
  private loadingPromises = new Map<string, Promise<void>>();

  async loadIconSet(name: string): Promise<void> {
    if (this.loadedSets.has(name)) {
      return;
    }

    if (this.loadingPromises.has(name)) {
      return this.loadingPromises.get(name)!;
    }

    const loadPromise = this.loadIconSetFromAssets(name);
    this.loadingPromises.set(name, loadPromise);

    try {
      await loadPromise;
      this.loadedSets.add(name);
    } catch (error) {
      console.error(`Failed to load icon set: ${name}`, error);
    } finally {
      this.loadingPromises.delete(name);
    }
  }

  private async loadIconSetFromAssets(name: string): Promise<void> {
    const response = await fetch(`assets/icons/${name}.svg`);
    if (!response.ok) {
      throw new Error(`Failed to load icon set: ${name}`);
    }

    const svgContent = await response.text();
    
    // Add to DOM
    const div = document.createElement('div');
    div.innerHTML = svgContent;
    div.style.display = 'none';
    document.body.appendChild(div);
  }

  preloadIconSets(names: string[]): Promise<void[]> {
    return Promise.all(names.map(name => this.loadIconSet(name)));
  }
}
```

### Icon Sprite Generation

```typescript
// Icon sprite builder utility
export class IconSpriteBuilder {
  private icons: Map<string, string> = new Map();
  
  addIcon(name: string, svgContent: string): void {
    this.icons.set(name, svgContent);
  }
  
  generateSprite(): string {
    const symbols = Array.from(this.icons.entries()).map(([name, content]) => {
      // Extract viewBox and paths from SVG
      const viewBoxMatch = content.match(/viewBox="([^"]*)"/);
      const viewBox = viewBoxMatch ? viewBoxMatch[1] : '0 0 24 24';
      
      // Extract inner content (paths, etc.)
      const innerContent = content.replace(/<svg[^>]*>|<\/svg>/g, '');
      
      return `<symbol id="${name}" viewBox="${viewBox}">${innerContent}</symbol>`;
    }).join('\n    ');
    
    return `<svg xmlns="http://www.w3.org/2000/svg" style="display: none;">
  <defs>
    ${symbols}
  </defs>
</svg>`;
  }
  
  generateCSS(): string {
    const rules = Array.from(this.icons.keys()).map(name => 
      `.icon-${name} { mask-image: url(#${name}); }`
    ).join('\n');
    
    return `.icon-sprite {
  display: inline-block;
  width: 1em;
  height: 1em;
  background-color: currentColor;
  mask-repeat: no-repeat;
  mask-position: center;
  mask-size: contain;
}

${rules}`;
  }
}
```

---

## Icon Accessibility

### Screen Reader Support

```typescript
// Accessible icon component
import { Component, Input, ChangeDetectionStrategy } from '@angular/core';

@Component({
  selector: 'app-accessible-icon',
  template: `
    <mat-icon
      [attr.aria-label]="ariaLabel"
      [attr.aria-hidden]="ariaHidden"
      [attr.role]="role"
      [attr.title]="title"
    >
      {{iconName}}
    </mat-icon>
    <span class="sr-only" *ngIf="screenReaderText">{{screenReaderText}}</span>
  `,
  styles: [`
    .sr-only {
      position: absolute;
      width: 1px;
      height: 1px;
      padding: 0;
      margin: -1px;
      overflow: hidden;
      clip: rect(0, 0, 0, 0);
      white-space: nowrap;
      border: 0;
    }
  `],
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class AccessibleIconComponent {
  @Input() iconName: string = '';
  @Input() ariaLabel?: string;
  @Input() screenReaderText?: string;
  @Input() title?: string;
  @Input() decorative = false;

  get ariaHidden(): boolean {
    return this.decorative || (!this.ariaLabel && !this.screenReaderText);
  }

  get role(): string | null {
    if (this.decorative) return null;
    return this.ariaLabel || this.screenReaderText ? 'img' : null;
  }
}
```

### High Contrast Support

```scss
// High contrast icon support
@media (prefers-contrast: high) {
  .mat-icon {
    // Ensure icons are visible in high contrast mode
    filter: contrast(2);
    
    &.color-primary,
    &.color-secondary,
    &.color-surface {
      color: CanvasText;
    }
    
    &.color-error {
      color: #ff0000;
    }
    
    &.color-success {
      color: #00ff00;
    }
    
    &.color-warning {
      color: #ffff00;
    }
  }
  
  // Icon buttons in high contrast
  .mat-mdc-icon-button {
    border: 1px solid CanvasText;
    
    &:hover,
    &:focus {
      background: Highlight;
      color: HighlightText;
    }
  }
}

// Forced colors support
@media (forced-colors: active) {
  .mat-icon {
    forced-color-adjust: auto;
  }
  
  .mat-mdc-icon-button {
    forced-color-adjust: auto;
    border: 1px solid ButtonText;
  }
}
```

---

## Advanced Icon Techniques

### Animated Icons

```scss
// Icon animations
.animated-icons {
  .icon-bounce {
    .mat-icon {
      animation: bounce 0.6s ease-in-out;
    }
  }
  
  .icon-pulse {
    .mat-icon {
      animation: pulse 1.5s ease-in-out infinite;
    }
  }
  
  .icon-shake {
    .mat-icon {
      animation: shake 0.5s ease-in-out;
    }
  }
  
  .icon-flip {
    .mat-icon {
      animation: flip 0.6s ease-in-out;
    }
  }
}

@keyframes bounce {
  0%, 20%, 60%, 100% { transform: translateY(0); }
  40% { transform: translateY(-20px); }
  80% { transform: translateY(-10px); }
}

@keyframes pulse {
  0% { transform: scale(1); opacity: 1; }
  50% { transform: scale(1.1); opacity: 0.7; }
  100% { transform: scale(1); opacity: 1; }
}

@keyframes shake {
  0%, 100% { transform: translateX(0); }
  10%, 30%, 50%, 70%, 90% { transform: translateX(-10px); }
  20%, 40%, 60%, 80% { transform: translateX(10px); }
}

@keyframes flip {
  0% { transform: rotateY(0); }
  50% { transform: rotateY(-90deg); }
  100% { transform: rotateY(0); }
}
```

### Icon Morphing

```typescript
// Icon morphing component
import { Component, Input, ChangeDetectionStrategy } from '@angular/core';

@Component({
  selector: 'app-morphing-icon',
  template: `
    <div class="morphing-icon" [class.morphed]="morphed" (click)="toggle()">
      <mat-icon class="icon-from">{{fromIcon}}</mat-icon>
      <mat-icon class="icon-to">{{toIcon}}</mat-icon>
    </div>
  `,
  styles: [`
    .morphing-icon {
      position: relative;
      display: inline-block;
      cursor: pointer;
      
      .mat-icon {
        transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        position: absolute;
        top: 0;
        left: 0;
      }
      
      .icon-from {
        opacity: 1;
        transform: scale(1) rotate(0deg);
      }
      
      .icon-to {
        opacity: 0;
        transform: scale(0.5) rotate(180deg);
      }
      
      &.morphed {
        .icon-from {
          opacity: 0;
          transform: scale(0.5) rotate(-180deg);
        }
        
        .icon-to {
          opacity: 1;
          transform: scale(1) rotate(0deg);
        }
      }
    }
  `],
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class MorphingIconComponent {
  @Input() fromIcon: string = '';
  @Input() toIcon: string = '';
  @Input() morphed = false;

  toggle(): void {
    this.morphed = !this.morphed;
  }
}
```

---

## Icon Asset Management

### Icon Build Pipeline

```typescript
// Icon build utility
import { readFileSync, writeFileSync, readdirSync } from 'fs';
import { join } from 'path';
import { optimize } from 'svgo';

export class IconBuildPipeline {
  private iconsDir: string;
  private outputDir: string;

  constructor(iconsDir: string, outputDir: string) {
    this.iconsDir = iconsDir;
    this.outputDir = outputDir;
  }

  async buildIcons(): Promise<void> {
    const iconFiles = readdirSync(this.iconsDir).filter(file => file.endsWith('.svg'));
    const optimizedIcons = new Map<string, string>();
    
    for (const file of iconFiles) {
      const content = readFileSync(join(this.iconsDir, file), 'utf8');
      const optimized = await this.optimizeSvg(content);
      const name = file.replace('.svg', '');
      optimizedIcons.set(name, optimized);
    }
    
    // Generate sprite
    await this.generateSprite(optimizedIcons);
    
    // Generate TypeScript definitions
    await this.generateTypeDefinitions(Array.from(optimizedIcons.keys()));
    
    // Generate icon index
    await this.generateIconIndex(optimizedIcons);
  }

  private async optimizeSvg(content: string): Promise<string> {
    const result = optimize(content, {
      plugins: [
        'preset-default',
        'removeDimensions',
        'removeXMLNS'
      ]
    });
    return result.data;
  }

  private async generateSprite(icons: Map<string, string>): Promise<void> {
    const symbols = Array.from(icons.entries()).map(([name, content]) => {
      const inner = content.replace(/<svg[^>]*>|<\/svg>/g, '');
      return `<symbol id="${name}" viewBox="0 0 24 24">${inner}</symbol>`;
    }).join('\n');

    const sprite = `<svg xmlns="http://www.w3.org/2000/svg" style="display: none;">
  <defs>
    ${symbols}
  </defs>
</svg>`;

    writeFileSync(join(this.outputDir, 'icons-sprite.svg'), sprite);
  }

  private async generateTypeDefinitions(iconNames: string[]): Promise<void> {
    const types = `export type IconName = ${iconNames.map(name => `'${name}'`).join(' | ')};

export const ICON_NAMES: IconName[] = [
  ${iconNames.map(name => `'${name}'`).join(',\n  ')}
];`;

    writeFileSync(join(this.outputDir, 'icon-types.ts'), types);
  }

  private async generateIconIndex(icons: Map<string, string>): Promise<void> {
    const index = `export const ICONS = {
  ${Array.from(icons.entries()).map(([name, content]) => 
    `'${name}': \`${content}\``
  ).join(',\n  ')}
};`;

    writeFileSync(join(this.outputDir, 'icon-index.ts'), index);
  }
}
```

---

## Practical Exercises

### Exercise 1: Icon System Implementation
Create a comprehensive icon system:

1. Set up Material Icons with multiple variants
2. Implement custom SVG icon registration
3. Create icon size and color systems
4. Build icon accessibility features
5. Add icon performance optimizations

### Exercise 2: Custom Icon Component Library
Develop a complete icon component library:

1. Build reusable icon components
2. Create icon button variants
3. Implement icon animations and morphing
4. Add icon theming capabilities
5. Build icon documentation system

### Exercise 3: Icon Asset Pipeline
Create an automated icon asset pipeline:

1. Build SVG optimization tools
2. Generate icon sprites automatically
3. Create TypeScript type definitions
4. Implement icon lazy loading
5. Add icon usage analytics

### Exercise 4: Advanced Icon Features
Implement advanced icon features:

1. Create animated icon transitions
2. Build icon morphing components
3. Implement contextual icon switching
4. Add icon accessibility testing
5. Create icon performance monitoring

---

## 🔗 Additional Resources

- [Material Icons Library](https://fonts.google.com/icons)
- [Angular Material Icon API](https://material.angular.dev/components/icon/api)
- [SVG Optimization Tools](https://github.com/svg/svgo)
- [Icon Accessibility Guidelines](https://www.w3.org/WAI/ARIA/apg/patterns/landmarks/)

---

## ✅ Module Completion Checklist

- [ ] Understand Material Icons integration
- [ ] Can implement custom SVG icons
- [ ] Master icon theming and customization
- [ ] Build comprehensive icon component libraries
- [ ] Implement icon performance optimizations
- [ ] Create accessible icon systems
- [ ] Build advanced icon animations and effects
- [ ] Complete all practical exercises

**Next Module**: [11. Responsive Design - Adapting Themes for Different Screen Sizes](../11-responsive-design/)
