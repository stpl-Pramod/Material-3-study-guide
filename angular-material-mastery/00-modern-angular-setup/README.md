# 00. Modern Angular Setup - Foundation for Material 3 Mastery

## 🎯 Module Overview

This foundation module establishes the modern Angular development environment and architectural patterns needed for Material Design 3 mastery. You'll learn Angular 17+ features, set up an optimized development environment, and implement professional project architecture patterns.

## 📚 Learning Objectives

After completing this module, you will be able to:
- ✅ Set up Angular 17+ projects with signals and standalone components
- ✅ Configure an optimized development environment
- ✅ Implement modern Angular patterns and best practices
- ✅ Establish professional project architecture
- ✅ Use Angular's latest features effectively

## 🏗️ Module Structure

```
00-modern-angular-setup/
├── README.md                          # This overview
├── angular-17-features/               # Modern Angular capabilities
├── development-environment/           # VS Code, debugging, profiling
├── project-architecture/              # Folder structure, build optimization
├── signals-integration/               # Reactive state management
├── standalone-components/             # Module-free architecture
├── control-flow-syntax/               # New template syntax
└── completion-checklist.md            # Validation and next steps
```

## 🚀 Quick Start Guide

### Prerequisites Check
```bash
# Verify required versions
node --version    # Should be >= 18.0.0
npm --version     # Should be >= 9.0.0
ng version        # Should be >= 17.0.0
```

### Installation Commands
```bash
# Install/update Angular CLI
npm install -g @angular/cli@latest

# Create new project with Material
ng new material-mastery-foundation --routing --style=scss
cd material-mastery-foundation
ng add @angular/material

# Verify setup
ng serve
```

## 📖 Learning Path

### Phase 1: Angular 17+ Features (6 hours)
**Objective**: Master the latest Angular capabilities

1. **[Signals System](./angular-17-features/#signals-system)** (2 hours)
   - Reactive state management
   - Computed signals
   - Effect handling
   - Signal integration patterns

2. **[Control Flow Syntax](./control-flow-syntax/)** (2 hours)
   - `@if` conditional rendering
   - `@for` loops and tracking
   - `@switch` case handling
   - Migration from structural directives

3. **[Standalone Components](./standalone-components/)** (2 hours)
   - Module-free architecture
   - Dependency injection
   - Routing with standalone components
   - Component composition

### Phase 2: Development Environment (4 hours)
**Objective**: Optimize development productivity

1. **[VS Code Configuration](./development-environment/#vscode-setup)** (2 hours)
   - Extensions installation
   - Workspace settings
   - Debugging configuration
   - Code snippets

2. **[Development Tools](./development-environment/#dev-tools)** (2 hours)
   - Performance profiling
   - Network monitoring
   - Bundle analysis
   - Testing setup

### Phase 3: Project Architecture (6 hours)
**Objective**: Establish scalable project structure

1. **[Folder Structure Patterns](./project-architecture/folder-structure-patterns/)** (2 hours)
   - Feature-based organization
   - Domain-driven design
   - Shared resources
   - Scaling strategies

2. **[Build Optimization](./project-architecture/build-optimization/)** (2 hours)
   - Webpack configuration
   - Bundle splitting
   - Performance budgets
   - Deployment optimization

3. **[Code Quality Gates](./project-architecture/code-quality-gates/)** (2 hours)
   - ESLint configuration
   - Prettier formatting
   - Husky git hooks
   - CI/CD integration

## 🛠️ Hands-on Exercises

### Exercise 1: Signals Implementation
**Time**: 1 hour  
**Objective**: Build a reactive counter with signals

```typescript
// Create src/app/components/signal-counter.component.ts
import { Component, signal, computed } from '@angular/core';

@Component({
  selector: 'app-signal-counter',
  standalone: true,
  template: `
    <div class="counter-container">
      <h2>Signal Counter: {{ count() }}</h2>
      <p>Double: {{ doubleCount() }}</p>
      <button (click)="increment()">+</button>
      <button (click)="decrement()">-</button>
      <button (click)="reset()">Reset</button>
    </div>
  `,
  styles: [`
    .counter-container {
      text-align: center;
      padding: 2rem;
    }
    button {
      margin: 0 0.5rem;
      padding: 0.5rem 1rem;
    }
  `]
})
export class SignalCounterComponent {
  count = signal(0);
  doubleCount = computed(() => this.count() * 2);

  increment() {
    this.count.update(value => value + 1);
  }

  decrement() {
    this.count.update(value => value - 1);
  }

  reset() {
    this.count.set(0);
  }
}
```

### Exercise 2: Control Flow Syntax
**Time**: 1 hour  
**Objective**: Implement modern control flow patterns

```typescript
// Create src/app/components/control-flow-demo.component.ts
import { Component, signal } from '@angular/core';

interface User {
  id: number;
  name: string;
  role: 'admin' | 'user' | 'guest';
}

@Component({
  selector: 'app-control-flow-demo',
  standalone: true,
  template: `
    <div class="demo-container">
      <h2>Control Flow Demo</h2>
      
      <!-- New @if syntax -->
      @if (currentUser()) {
        <div class="user-info">
          <h3>Welcome, {{ currentUser()?.name }}!</h3>
          
          <!-- New @switch syntax -->
          @switch (currentUser()?.role) {
            @case ('admin') {
              <p class="admin">Admin Dashboard Access</p>
            }
            @case ('user') {
              <p class="user">User Dashboard Access</p>
            }
            @default {
              <p class="guest">Limited Access</p>
            }
          }
        </div>
      } @else {
        <p>Please log in</p>
      }

      <!-- New @for syntax -->
      <div class="users-list">
        <h3>All Users</h3>
        @for (user of users(); track user.id) {
          <div class="user-card">
            <span>{{ user.name }}</span>
            <span class="role">{{ user.role }}</span>
          </div>
        } @empty {
          <p>No users found</p>
        }
      </div>
    </div>
  `,
  styles: [`
    .demo-container { padding: 2rem; }
    .user-card { 
      display: flex; 
      justify-content: space-between; 
      padding: 0.5rem; 
      border: 1px solid #ccc; 
      margin: 0.5rem 0; 
    }
    .admin { color: red; }
    .user { color: blue; }
    .guest { color: gray; }
  `]
})
export class ControlFlowDemoComponent {
  currentUser = signal<User | null>({
    id: 1,
    name: 'John Doe',
    role: 'admin'
  });

  users = signal<User[]>([
    { id: 1, name: 'John Doe', role: 'admin' },
    { id: 2, name: 'Jane Smith', role: 'user' },
    { id: 3, name: 'Bob Johnson', role: 'guest' }
  ]);
}
```

### Exercise 3: Standalone Component Architecture
**Time**: 1 hour  
**Objective**: Build a standalone component system

```typescript
// Create src/app/components/standalone-demo.component.ts
import { Component, inject } from '@angular/core';
import { CommonModule } from '@angular/common';
import { MatButtonModule } from '@angular/material/button';
import { MatCardModule } from '@angular/material/card';
import { Router } from '@angular/router';

@Component({
  selector: 'app-standalone-demo',
  standalone: true,
  imports: [CommonModule, MatButtonModule, MatCardModule],
  template: `
    <mat-card class="demo-card">
      <mat-card-header>
        <mat-card-title>Standalone Component</mat-card-title>
        <mat-card-subtitle>No NgModule Required</mat-card-subtitle>
      </mat-card-header>
      <mat-card-content>
        <p>This component imports its own dependencies</p>
        <p>Modern Angular architecture with standalone components</p>
      </mat-card-content>
      <mat-card-actions>
        <button mat-raised-button color="primary" (click)="navigate()">
          Navigate
        </button>
      </mat-card-actions>
    </mat-card>
  `,
  styles: [`
    .demo-card {
      max-width: 400px;
      margin: 2rem auto;
    }
  `]
})
export class StandaloneDemoComponent {
  private router = inject(Router);

  navigate() {
    // Navigation logic
    console.log('Navigation triggered');
  }
}
```

## 🎯 Success Metrics

### Technical Competency
- [ ] Can create Angular 17+ projects with modern features
- [ ] Implements signals for reactive state management
- [ ] Uses new control flow syntax effectively
- [ ] Builds standalone component architectures
- [ ] Configures optimized development environment

### Code Quality Standards
- [ ] ESLint and Prettier configured and working
- [ ] Git hooks prevent bad commits
- [ ] Bundle size optimized
- [ ] Performance budgets established
- [ ] Testing setup functional

### Architecture Understanding
- [ ] Folder structure follows best practices
- [ ] Components are properly organized
- [ ] Shared resources are reusable
- [ ] Build process is optimized
- [ ] Code quality gates are automated

## 📊 Performance Benchmarks

### Initial Setup Targets
- **Bundle Size**: < 500KB initial
- **First Contentful Paint**: < 1.5s
- **Largest Contentful Paint**: < 2.5s
- **Cumulative Layout Shift**: < 0.1
- **First Input Delay**: < 100ms

### Development Productivity
- **Build Time**: < 30s for development
- **Rebuild Time**: < 5s for changes
- **Test Execution**: < 10s for unit tests
- **Linting Time**: < 5s for full project

## 🔍 Troubleshooting Guide

### Common Issues

#### Angular CLI Version Mismatch
```bash
# Problem: Old Angular CLI version
npm uninstall -g @angular/cli
npm install -g @angular/cli@latest
ng version
```

#### Signals Not Working
```typescript
// Problem: Missing imports
import { signal, computed, effect } from '@angular/core';

// Problem: Incorrect usage
// Wrong: signal.value = newValue
// Correct: signal.set(newValue) or signal.update(fn)
```

#### Standalone Component Errors
```typescript
// Problem: Missing imports in standalone component
@Component({
  standalone: true,
  imports: [CommonModule, MatButtonModule], // Required imports
  // ...
})
```

### Performance Issues
```bash
# Analyze bundle size
ng build --stats-json
npx webpack-bundle-analyzer dist/stats.json

# Check for memory leaks
ng build --watch
# Monitor memory usage in task manager
```

## 📚 Additional Resources

### Official Documentation
- [Angular 17+ Features](https://angular.dev/guide/components)
- [Signals Guide](https://angular.dev/guide/signals)
- [Standalone Components](https://angular.dev/guide/components/importing)
- [Control Flow Syntax](https://angular.dev/guide/templates/control-flow)

### Development Tools
- [Angular DevTools Extension](https://chrome.google.com/webstore/detail/angular-devtools/ienfalfjdbdpebioblfackkekamfmbnh)
- [VS Code Angular Extensions](https://marketplace.visualstudio.com/items?itemName=Angular.ng-template)
- [Bundle Analyzer](https://www.npmjs.com/package/webpack-bundle-analyzer)

### Community Resources
- [Angular Blog](https://blog.angular.io/)
- [Angular Community Discord](https://discord.gg/angular)
- [Angular Reddit](https://reddit.com/r/angular)

## ✅ Completion Checklist

### Foundation Setup
- [ ] Angular 17+ project created
- [ ] Material Design installed
- [ ] Development environment configured
- [ ] VS Code extensions installed

### Modern Angular Features
- [ ] Signals implementation completed
- [ ] Control flow syntax demonstrated
- [ ] Standalone components created
- [ ] New lifecycle hooks understood

### Project Architecture
- [ ] Folder structure established
- [ ] Build optimization configured
- [ ] Code quality gates active
- [ ] Performance budgets set

### Validation
- [ ] All exercises completed successfully
- [ ] Code quality checks passing
- [ ] Performance targets met
- [ ] Ready for beginner projects

---

## 🎉 Next Steps

Once you've completed this foundation module:

1. **Validate Your Setup**: Run through the [completion checklist](./completion-checklist.md)
2. **Start Building**: Move to [Beginner Level Projects](../01-beginner-level/README.md)
3. **Track Progress**: Update your [progress tracker](../planning/progress-tracker.md)
4. **Join Community**: Share your foundation setup with the learning community

**Congratulations on establishing your modern Angular foundation! 🚀**

---

*This foundation is designed to last throughout your Material 3 mastery journey. Take time to understand each concept thoroughly.*
