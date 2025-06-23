# Project 1: Personal Dashboard - Material Design 3 Implementation

## 🎯 Project Overview

Create a modern, responsive personal dashboard using Angular 17+ and Material Design 3. This project introduces fundamental Material Design concepts, responsive theming, and modern Angular patterns through a practical, guided implementation.

## 📚 Learning Objectives

After completing this project, you will be able to:
- ✅ Set up Angular Material 3 with custom themes
- ✅ Implement responsive navigation with sidenav and toolbar
- ✅ Create card-based dashboard layouts
- ✅ Use Material Design system tokens effectively
- ✅ Build accessible and mobile-friendly interfaces
- ✅ Implement basic state management with signals

## 🏗️ Project Architecture

### Component Structure
```
personal-dashboard/
├── src/app/
│   ├── core/                          # Core services and guards
│   │   ├── services/
│   │   │   ├── theme.service.ts        # Theme management
│   │   │   └── dashboard.service.ts    # Dashboard data
│   │   └── models/
│   │       └── dashboard.types.ts      # Type definitions
│   ├── shared/                        # Reusable components
│   │   ├── components/
│   │   │   ├── dashboard-card/        # Reusable card component
│   │   │   └── loading-spinner/       # Loading states
│   │   └── pipes/
│   │       └── dashboard.pipes.ts     # Custom pipes
│   ├── features/                      # Feature modules
│   │   ├── dashboard/                 # Main dashboard
│   │   │   ├── components/
│   │   │   │   ├── dashboard-home/    # Dashboard overview
│   │   │   │   ├── stats-card/        # Statistics cards
│   │   │   │   ├── activity-feed/     # Recent activities
│   │   │   │   └── quick-actions/     # Action buttons
│   │   │   └── services/
│   │   │       └── dashboard-data.service.ts
│   │   ├── profile/                   # User profile
│   │   └── settings/                  # User settings
│   ├── layout/                        # Layout components
│   │   ├── header/                    # Top toolbar
│   │   ├── sidebar/                   # Navigation sidebar
│   │   └── main-layout/               # Main layout wrapper
│   └── styles/                        # Theme and styles
│       ├── _variables.scss            # SCSS variables
│       ├── _mixins.scss              # Reusable mixins
│       ├── _themes.scss              # Material themes
│       └── components/               # Component-specific styles
```

### Technology Stack
- **Framework**: Angular 17+ with Signals
- **UI Library**: Angular Material 17+
- **Styling**: SCSS with Material Design tokens
- **State Management**: Angular Signals
- **Icons**: Material Symbols
- **Testing**: Jest + Angular Testing Library
- **Build**: Angular CLI with optimization

## 🎨 Design System

### Color Palette
```scss
// Primary Brand Colors
$primary-palette: (
  50: #e8f2ff,
  100: #c3ddff,
  200: #9bc7ff,
  300: #70b0ff,
  400: #4f9eff,
  500: #2e8bff,  // Primary
  600: #2979ff,
  700: #2363ed,
  800: #1d4fd6,
  900: #1329a5,
  contrast: (
    50: rgba(black, 0.87),
    100: rgba(black, 0.87),
    200: rgba(black, 0.87),
    300: rgba(black, 0.87),
    400: rgba(black, 0.87),
    500: white,
    600: white,
    700: white,
    800: white,
    900: white,
  )
);

// Secondary Accent Colors
$secondary-palette: (
  50: #f3e5f5,
  100: #e1bee7,
  200: #ce93d8,
  300: #ba68c8,
  400: #ab47bc,
  500: #9c27b0,  // Secondary
  600: #8e24aa,
  700: #7b1fa2,
  800: #6a1b9a,
  900: #4a148c,
);
```

### Typography Scale
```scss
// Material Design 3 Typography
$custom-typography: mat.define-typography-config(
  $font-family: 'Inter, "Helvetica Neue", sans-serif',
  $display-large: mat.define-typography-level(57px, 64px, 400),
  $display-medium: mat.define-typography-level(45px, 52px, 400),
  $display-small: mat.define-typography-level(36px, 44px, 400),
  $headline-large: mat.define-typography-level(32px, 40px, 400),
  $headline-medium: mat.define-typography-level(28px, 36px, 400),
  $headline-small: mat.define-typography-level(24px, 32px, 400),
  $title-large: mat.define-typography-level(22px, 28px, 500),
  $title-medium: mat.define-typography-level(16px, 24px, 500),
  $title-small: mat.define-typography-level(14px, 20px, 500),
  $label-large: mat.define-typography-level(14px, 20px, 500),
  $label-medium: mat.define-typography-level(12px, 16px, 500),
  $label-small: mat.define-typography-level(11px, 16px, 500),
  $body-large: mat.define-typography-level(16px, 24px, 400),
  $body-medium: mat.define-typography-level(14px, 20px, 400),
  $body-small: mat.define-typography-level(12px, 16px, 400),
);
```

### Component Specifications

#### Dashboard Cards
- **Elevation**: Level 1 (2dp)
- **Corner Radius**: 12px
- **Padding**: 16px
- **Gap**: 16px
- **Responsive**: 1-4 columns based on screen size

#### Navigation
- **Sidenav Width**: 280px (desktop) / Full width (mobile)
- **Toolbar Height**: 64px
- **Breakpoint**: 768px for mobile/desktop toggle

## 📱 Responsive Design

### Breakpoint System
```scss
$breakpoints: (
  mobile: 599px,
  tablet: 959px,
  desktop: 1279px,
  large: 1919px
);

// Usage
@media (max-width: #{map-get($breakpoints, mobile)}) {
  // Mobile styles
}
```

### Layout Patterns
- **Mobile**: Single column, collapsible navigation
- **Tablet**: Two columns, overlay navigation
- **Desktop**: Three+ columns, permanent navigation
- **Large**: Four+ columns, expanded content

## 🛠️ Implementation Guide

### Phase 1: Project Setup (2 hours)

#### Step 1: Initialize Project
```bash
# Create new Angular project
ng new personal-dashboard --routing --style=scss
cd personal-dashboard

# Add Angular Material
ng add @angular/material
# Choose: Custom theme, Yes to typography, Yes to animations

# Add additional dependencies
npm install @angular/cdk
```

#### Step 2: Configure Theme
```scss
// src/styles/themes/_material-theme.scss
@use '@angular/material' as mat;
@use './palettes' as palettes;

// Define the theme
$light-theme: mat.define-theme((
  color: (
    theme-type: light,
    primary: palettes.$primary-palette,
    tertiary: palettes.$secondary-palette,
  ),
  typography: (
    brand-family: 'Inter',
    plain-family: 'Inter',
  ),
  density: (
    scale: 0,
  ),
));

// Apply the theme
:root {
  @include mat.all-component-themes($light-theme);
}
```

#### Step 3: Project Structure Setup
```bash
# Generate core structure
ng generate service core/services/theme
ng generate service core/services/dashboard
ng generate interface core/models/dashboard

# Generate shared components
ng generate component shared/components/dashboard-card --standalone
ng generate component shared/components/loading-spinner --standalone

# Generate layout components
ng generate component layout/header --standalone
ng generate component layout/sidebar --standalone
ng generate component layout/main-layout --standalone

# Generate feature components
ng generate component features/dashboard/components/dashboard-home --standalone
ng generate component features/dashboard/components/stats-card --standalone
ng generate component features/dashboard/components/activity-feed --standalone
ng generate component features/dashboard/components/quick-actions --standalone
```

### Phase 2: Layout Implementation (3 hours)

#### Step 1: Main Layout Component
```typescript
// src/app/layout/main-layout/main-layout.component.ts
import { Component, signal, computed, inject } from '@angular/core';
import { CommonModule } from '@angular/common';
import { MatSidenavModule } from '@angular/material/sidenav';
import { MatToolbarModule } from '@angular/material/toolbar';
import { MatIconModule } from '@angular/material/icon';
import { MatButtonModule } from '@angular/material/button';
import { BreakpointObserver, Breakpoints } from '@angular/cdk/layout';

import { HeaderComponent } from '../header/header.component';
import { SidebarComponent } from '../sidebar/sidebar.component';

@Component({
  selector: 'app-main-layout',
  standalone: true,
  imports: [
    CommonModule,
    MatSidenavModule,
    MatToolbarModule,
    MatIconModule,
    MatButtonModule,
    HeaderComponent,
    SidebarComponent
  ],
  template: `
    <mat-sidenav-container class="layout-container">
      <!-- Navigation Sidebar -->
      <mat-sidenav 
        #drawer
        class="sidenav"
        [mode]="isHandset() ? 'over' : 'side'"
        [opened]="!isHandset()"
        [fixedInViewport]="isHandset()"
        fixedTopGap="0">
        <app-sidebar (menuItemSelected)="drawer.close()"></app-sidebar>
      </mat-sidenav>

      <!-- Main Content Area -->
      <mat-sidenav-content class="main-content">
        <!-- Header Toolbar -->
        <app-header 
          [isHandset]="isHandset()"
          (menuToggle)="drawer.toggle()">
        </app-header>

        <!-- Page Content -->
        <main class="content-area">
          <ng-content></ng-content>
        </main>
      </mat-sidenav-content>
    </mat-sidenav-container>
  `,
  styles: [`
    .layout-container {
      height: 100vh;
    }

    .sidenav {
      width: 280px;
      background: var(--mat-sys-surface-container-low);
      border-right: 1px solid var(--mat-sys-outline-variant);
    }

    .main-content {
      display: flex;
      flex-direction: column;
    }

    .content-area {
      flex: 1;
      padding: 24px;
      background: var(--mat-sys-surface);
      overflow-y: auto;
    }

    @media (max-width: 768px) {
      .content-area {
        padding: 16px;
      }
    }
  `]
})
export class MainLayoutComponent {
  private breakpointObserver = inject(BreakpointObserver);
  
  // Reactive breakpoint detection
  private handsetQuery = this.breakpointObserver.observe([
    Breakpoints.Handset,
    Breakpoints.Small,
    '(max-width: 768px)'
  ]);

  isHandset = signal(false);

  constructor() {
    // Subscribe to breakpoint changes
    this.handsetQuery.subscribe(result => {
      this.isHandset.set(result.matches);
    });
  }
}
```

#### Step 2: Header Component
```typescript
// src/app/layout/header/header.component.ts
import { Component, input, output } from '@angular/core';
import { CommonModule } from '@angular/common';
import { MatToolbarModule } from '@angular/material/toolbar';
import { MatIconModule } from '@angular/material/icon';
import { MatButtonModule } from '@angular/material/button';
import { MatMenuModule } from '@angular/material/menu';

@Component({
  selector: 'app-header',
  standalone: true,
  imports: [
    CommonModule,
    MatToolbarModule,
    MatIconModule,
    MatButtonModule,
    MatMenuModule
  ],
  template: `
    <mat-toolbar class="header-toolbar">
      <!-- Menu Toggle (Mobile) -->
      @if (isHandset()) {
        <button mat-icon-button (click)="menuToggle.emit()">
          <mat-icon>menu</mat-icon>
        </button>
      }

      <!-- Page Title -->
      <span class="title">Personal Dashboard</span>

      <!-- Spacer -->
      <span class="spacer"></span>

      <!-- User Actions -->
      <div class="user-actions">
        <!-- Notifications -->
        <button mat-icon-button [matMenuTriggerFor]="notificationMenu">
          <mat-icon>notifications</mat-icon>
        </button>

        <!-- User Profile -->
        <button mat-icon-button [matMenuTriggerFor]="profileMenu">
          <mat-icon>account_circle</mat-icon>
        </button>
      </div>

      <!-- Notification Menu -->
      <mat-menu #notificationMenu="matMenu">
        <button mat-menu-item>
          <mat-icon>info</mat-icon>
          <span>No new notifications</span>
        </button>
      </mat-menu>

      <!-- Profile Menu -->
      <mat-menu #profileMenu="matMenu">
        <button mat-menu-item>
          <mat-icon>person</mat-icon>
          <span>Profile</span>
        </button>
        <button mat-menu-item>
          <mat-icon>settings</mat-icon>
          <span>Settings</span>
        </button>
        <mat-divider></mat-divider>
        <button mat-menu-item>
          <mat-icon>logout</mat-icon>
          <span>Logout</span>
        </button>
      </mat-menu>
    </mat-toolbar>
  `,
  styles: [`
    .header-toolbar {
      background: var(--mat-sys-surface-container);
      color: var(--mat-sys-on-surface);
      border-bottom: 1px solid var(--mat-sys-outline-variant);
    }

    .title {
      font-size: 1.25rem;
      font-weight: 500;
      margin-left: 16px;
    }

    .spacer {
      flex: 1 1 auto;
    }

    .user-actions {
      display: flex;
      align-items: center;
      gap: 8px;
    }
  `]
})
export class HeaderComponent {
  isHandset = input.required<boolean>();
  menuToggle = output<void>();
}
```

#### Step 3: Sidebar Navigation
```typescript
// src/app/layout/sidebar/sidebar.component.ts
import { Component, output } from '@angular/core';
import { CommonModule } from '@angular/common';
import { RouterModule } from '@angular/router';
import { MatListModule } from '@angular/material/list';
import { MatIconModule } from '@angular/material/icon';
import { MatDividerModule } from '@angular/material/divider';

interface NavigationItem {
  label: string;
  icon: string;
  route: string;
  badge?: string;
}

@Component({
  selector: 'app-sidebar',
  standalone: true,
  imports: [
    CommonModule,
    RouterModule,
    MatListModule,
    MatIconModule,
    MatDividerModule
  ],
  template: `
    <div class="sidebar-content">
      <!-- User Profile Section -->
      <div class="user-profile">
        <div class="avatar">
          <mat-icon class="user-avatar">account_circle</mat-icon>
        </div>
        <div class="user-info">
          <h3 class="user-name">John Doe</h3>
          <p class="user-email">john.doe@example.com</p>
        </div>
      </div>

      <mat-divider></mat-divider>

      <!-- Navigation Menu -->
      <mat-nav-list class="navigation-list">
        @for (item of navigationItems; track item.route) {
          <mat-list-item 
            [routerLink]="item.route"
            routerLinkActive="active-link"
            (click)="menuItemSelected.emit()">
            <mat-icon matListItemIcon>{{ item.icon }}</mat-icon>
            <span matListItemTitle>{{ item.label }}</span>
            @if (item.badge) {
              <span class="nav-badge" matListItemMeta>{{ item.badge }}</span>
            }
          </mat-list-item>
        }
      </mat-nav-list>

      <mat-divider></mat-divider>

      <!-- Secondary Actions -->
      <mat-nav-list class="secondary-nav">
        <mat-list-item routerLink="/settings">
          <mat-icon matListItemIcon>settings</mat-icon>
          <span matListItemTitle>Settings</span>
        </mat-list-item>
        <mat-list-item routerLink="/help">
          <mat-icon matListItemIcon>help</mat-icon>
          <span matListItemTitle>Help & Support</span>
        </mat-list-item>
      </mat-nav-list>
    </div>
  `,
  styles: [`
    .sidebar-content {
      height: 100%;
      display: flex;
      flex-direction: column;
    }

    .user-profile {
      padding: 24px 16px;
      display: flex;
      align-items: center;
      gap: 16px;
      background: var(--mat-sys-primary-container);
      color: var(--mat-sys-on-primary-container);
    }

    .user-avatar {
      font-size: 48px;
      width: 48px;
      height: 48px;
      color: var(--mat-sys-primary);
    }

    .user-info {
      flex: 1;
    }

    .user-name {
      margin: 0 0 4px 0;
      font-size: 16px;
      font-weight: 500;
    }

    .user-email {
      margin: 0;
      font-size: 14px;
      opacity: 0.7;
    }

    .navigation-list {
      flex: 1;
      padding: 8px 0;
    }

    .secondary-nav {
      padding: 8px 0 16px 0;
    }

    .active-link {
      background: var(--mat-sys-secondary-container) !important;
      color: var(--mat-sys-on-secondary-container) !important;
    }

    .nav-badge {
      background: var(--mat-sys-error);
      color: var(--mat-sys-on-error);
      border-radius: 12px;
      padding: 2px 8px;
      font-size: 12px;
      font-weight: 500;
    }
  `]
})
export class SidebarComponent {
  menuItemSelected = output<void>();

  navigationItems: NavigationItem[] = [
    { label: 'Dashboard', icon: 'dashboard', route: '/dashboard' },
    { label: 'Analytics', icon: 'analytics', route: '/analytics' },
    { label: 'Projects', icon: 'folder', route: '/projects', badge: '3' },
    { label: 'Tasks', icon: 'task_alt', route: '/tasks', badge: '12' },
    { label: 'Calendar', icon: 'calendar_today', route: '/calendar' },
    { label: 'Messages', icon: 'message', route: '/messages', badge: '5' },
    { label: 'Files', icon: 'folder_open', route: '/files' },
  ];
}
```

### Phase 3: Dashboard Components (4 hours)

#### Step 1: Dashboard Home Component
```typescript
// src/app/features/dashboard/components/dashboard-home/dashboard-home.component.ts
import { Component, signal, computed, inject, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { MatGridListModule } from '@angular/material/grid-list';
import { BreakpointObserver, Breakpoints } from '@angular/cdk/layout';

import { StatsCardComponent } from '../stats-card/stats-card.component';
import { ActivityFeedComponent } from '../activity-feed/activity-feed.component';
import { QuickActionsComponent } from '../quick-actions/quick-actions.component';
import { DashboardService } from '../../services/dashboard-data.service';

@Component({
  selector: 'app-dashboard-home',
  standalone: true,
  imports: [
    CommonModule,
    MatGridListModule,
    StatsCardComponent,
    ActivityFeedComponent,
    QuickActionsComponent
  ],
  template: `
    <div class="dashboard-container">
      <header class="dashboard-header">
        <h1>Welcome back, John!</h1>
        <p class="subtitle">Here's what's happening with your projects today.</p>
      </header>

      <!-- Stats Grid -->
      <section class="stats-section">
        <mat-grid-list [cols]="statsCols()" rowHeight="200px" gutterSize="16px">
          @for (stat of dashboardStats(); track stat.id) {
            <mat-grid-tile>
              <app-stats-card [data]="stat"></app-stats-card>
            </mat-grid-tile>
          }
        </mat-grid-list>
      </section>

      <!-- Content Grid -->
      <section class="content-section">
        <mat-grid-list [cols]="contentCols()" rowHeight="400px" gutterSize="16px">
          <!-- Quick Actions -->
          <mat-grid-tile>
            <app-quick-actions></app-quick-actions>
          </mat-grid-tile>

          <!-- Activity Feed -->
          <mat-grid-tile [colspan]="activitySpan()">
            <app-activity-feed [activities]="recentActivities()"></app-activity-feed>
          </mat-grid-tile>
        </mat-grid-list>
      </section>
    </div>
  `,
  styles: [`
    .dashboard-container {
      max-width: 1400px;
      margin: 0 auto;
    }

    .dashboard-header {
      margin-bottom: 32px;
    }

    .dashboard-header h1 {
      margin: 0 0 8px 0;
      font-size: 2rem;
      font-weight: 400;
      color: var(--mat-sys-on-surface);
    }

    .subtitle {
      margin: 0;
      color: var(--mat-sys-on-surface-variant);
      font-size: 1.1rem;
    }

    .stats-section {
      margin-bottom: 32px;
    }

    .content-section {
      margin-bottom: 32px;
    }

    mat-grid-tile {
      border-radius: 12px;
    }
  `]
})
export class DashboardHomeComponent implements OnInit {
  private breakpointObserver = inject(BreakpointObserver);
  private dashboardService = inject(DashboardService);

  // Reactive grid configuration
  statsCols = signal(4);
  contentCols = signal(3);
  activitySpan = signal(2);

  // Dashboard data
  dashboardStats = signal<any[]>([]);
  recentActivities = signal<any[]>([]);

  ngOnInit() {
    // Configure responsive grid
    this.setupResponsiveGrid();
    
    // Load dashboard data
    this.loadDashboardData();
  }

  private setupResponsiveGrid() {
    // Mobile
    this.breakpointObserver.observe([Breakpoints.XSmall, '(max-width: 599px)'])
      .subscribe(result => {
        if (result.matches) {
          this.statsCols.set(1);
          this.contentCols.set(1);
          this.activitySpan.set(1);
        }
      });

    // Tablet
    this.breakpointObserver.observe([
      Breakpoints.Small, 
      '(min-width: 600px) and (max-width: 959px)'
    ]).subscribe(result => {
      if (result.matches) {
        this.statsCols.set(2);
        this.contentCols.set(2);
        this.activitySpan.set(2);
      }
    });

    // Desktop
    this.breakpointObserver.observe([Breakpoints.Medium, Breakpoints.Large])
      .subscribe(result => {
        if (result.matches) {
          this.statsCols.set(4);
          this.contentCols.set(3);
          this.activitySpan.set(2);
        }
      });
  }

  private loadDashboardData() {
    // Load stats
    this.dashboardStats.set(this.dashboardService.getStats());
    
    // Load activities
    this.recentActivities.set(this.dashboardService.getRecentActivities());
  }
}
```

## 🎯 Success Criteria

### Functional Requirements
- [ ] Responsive navigation works on all screen sizes
- [ ] Dashboard displays statistics and activity feed
- [ ] Theme switching is functional
- [ ] All Material components are properly styled
- [ ] Accessibility features are implemented

### Technical Requirements
- [ ] Uses Angular 17+ features (signals, control flow)
- [ ] Implements Material Design 3 theming
- [ ] Follows component architecture best practices
- [ ] Code is properly tested and documented
- [ ] Performance budgets are met

### Visual Requirements
- [ ] Consistent with Material Design 3 guidelines
- [ ] Proper use of elevation and spacing
- [ ] Readable typography and color contrast
- [ ] Smooth animations and transitions
- [ ] Professional visual hierarchy

## 📈 Performance Targets

- **Bundle Size**: < 500KB initial load
- **First Contentful Paint**: < 1.5s
- **Largest Contentful Paint**: < 2.5s
- **Time to Interactive**: < 3s
- **Cumulative Layout Shift**: < 0.1

## 🧪 Testing Strategy

### Unit Testing
```typescript
// Example test for dashboard component
describe('DashboardHomeComponent', () => {
  let component: DashboardHomeComponent;
  let fixture: ComponentFixture<DashboardHomeComponent>;

  beforeEach(() => {
    TestBed.configureTestingModule({
      imports: [DashboardHomeComponent],
      providers: [
        { provide: DashboardService, useValue: mockDashboardService }
      ]
    });
    
    fixture = TestBed.createComponent(DashboardHomeComponent);
    component = fixture.componentInstance;
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });

  it('should display stats cards', () => {
    component.dashboardStats.set(mockStats);
    fixture.detectChanges();
    
    const statsCards = fixture.debugElement.queryAll(By.css('app-stats-card'));
    expect(statsCards.length).toBe(4);
  });
});
```

## 📚 Additional Resources

- [Material Design 3 Guidelines](https://m3.material.io/)
- [Angular Material Components](https://material.angular.dev/components)
- [Angular Signals Guide](https://angular.dev/guide/signals)
- [Responsive Design Patterns](https://web.dev/responsive-web-design-basics/)

## ✅ Next Steps

1. Complete the [implementation guide](./project-guide.md)
2. Follow the [completion checklist](./completion-checklist.md)
3. Review [testing guidelines](./testing-guide.md)
4. Move to [Project 2: Portfolio Website](../project-portfolio-website/)

---

**This project provides a solid foundation for Material Design 3 development. Take your time to understand each concept thoroughly!**
