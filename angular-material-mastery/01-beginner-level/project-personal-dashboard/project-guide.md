# Personal Dashboard - Step-by-Step Implementation Guide

## 🎯 Overview

This guide provides detailed, step-by-step instructions for building a personal dashboard with Angular 17+ and Material Design 3. Follow each phase carefully to ensure a solid foundation for your Material Design journey.

## 📋 Prerequisites

- [x] Node.js 18+ installed
- [x] Angular CLI 17+ installed
- [x] VS Code with Angular extensions
- [x] Basic understanding of Angular and TypeScript
- [x] Completed [Foundation Module](../../00-modern-angular-setup/README.md)

## 🏗️ Phase 1: Project Setup (2 hours)

### Step 1.1: Initialize Angular Project (30 minutes)

#### Create New Project
```bash
# Create the project
ng new personal-dashboard --routing --style=scss --package-manager=npm

# Navigate to project directory
cd personal-dashboard

# Verify Angular version
ng version
```

#### Add Angular Material
```bash
# Add Material with schematics
ng add @angular/material

# When prompted, choose:
# 1. Custom theme (for maximum control)
# 2. Set up global typography styles? Yes
# 3. Include Angular Animations? Yes
```

#### Install Additional Dependencies
```bash
# Install CDK for layout utilities
npm install @angular/cdk

# Install development dependencies
npm install --save-dev @angular-eslint/schematics
npm install --save-dev prettier

# Initialize ESLint
ng add @angular-eslint/schematics
```

### Step 1.2: Configure Project Structure (30 minutes)

#### Create Folder Structure
```bash
# Create core directories
mkdir -p src/app/core/{services,models,guards}
mkdir -p src/app/shared/{components,pipes,directives}
mkdir -p src/app/features/{dashboard,profile,settings}
mkdir -p src/app/layout/{header,sidebar,main-layout}
mkdir -p src/styles/{themes,components,utilities}

# Create initial files
touch src/app/core/models/dashboard.types.ts
touch src/styles/themes/_variables.scss
touch src/styles/themes/_mixins.scss
touch src/styles/themes/_material-theme.scss
```

#### Configure TypeScript Paths
```json
// tsconfig.json - Add path mapping
{
  "compilerOptions": {
    "baseUrl": "./",
    "paths": {
      "@core/*": ["src/app/core/*"],
      "@shared/*": ["src/app/shared/*"],
      "@features/*": ["src/app/features/*"],
      "@layout/*": ["src/app/layout/*"],
      "@styles/*": ["src/styles/*"]
    }
  }
}
```

### Step 1.3: Set Up Material Theme (60 minutes)

#### Create Color Palettes
```scss
// src/styles/themes/_palettes.scss
@use '@angular/material' as mat;

// Define custom primary palette
$primary-palette: (
  50: #e8f2ff,
  100: #c3ddff,
  200: #9bc7ff,
  300: #70b0ff,
  400: #4f9eff,
  500: #2e8bff,
  600: #2979ff,
  700: #2363ed,
  800: #1d4fd6,
  900: #1329a5,
  A100: #ffffff,
  A200: #e6f0ff,
  A400: #b3d9ff,
  A700: #99ccff,
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
    A100: rgba(black, 0.87),
    A200: rgba(black, 0.87),
    A400: rgba(black, 0.87),
    A700: rgba(black, 0.87),
  )
);

// Define custom secondary palette
$secondary-palette: (
  50: #f3e5f5,
  100: #e1bee7,
  200: #ce93d8,
  300: #ba68c8,
  400: #ab47bc,
  500: #9c27b0,
  600: #8e24aa,
  700: #7b1fa2,
  800: #6a1b9a,
  900: #4a148c,
  A100: #ea80fc,
  A200: #e040fb,
  A400: #d500f9,
  A700: #aa00ff,
  contrast: (
    50: rgba(black, 0.87),
    100: rgba(black, 0.87),
    200: rgba(black, 0.87),
    300: white,
    400: white,
    500: white,
    600: white,
    700: white,
    800: white,
    900: white,
    A100: rgba(black, 0.87),
    A200: white,
    A400: white,
    A700: white,
  )
);
```

#### Configure Material Theme
```scss
// src/styles/themes/_material-theme.scss
@use '@angular/material' as mat;
@use 'palettes' as palettes;

// Include the common styles for Angular Material
@include mat.core();

// Define typography
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
  $body-large: mat.define-typography-level(16px, 24px, 400),
  $body-medium: mat.define-typography-level(14px, 20px, 400),
  $body-small: mat.define-typography-level(12px, 16px, 400),
  $label-large: mat.define-typography-level(14px, 20px, 500),
  $label-medium: mat.define-typography-level(12px, 16px, 500),
  $label-small: mat.define-typography-level(11px, 16px, 500),
);

// Define the light theme
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

// Define the dark theme
$dark-theme: mat.define-theme((
  color: (
    theme-type: dark,
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

// Apply the light theme by default
:root {
  @include mat.all-component-themes($light-theme);
}

// Apply dark theme when class is present
.dark-theme {
  @include mat.all-component-colors($dark-theme);
}
```

#### Update Global Styles
```scss
// src/styles.scss
@use 'styles/themes/material-theme';

// Import Google Fonts
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@20..48,100..700,0..1,-50..200');

// Global styles
* {
  box-sizing: border-box;
}

html, body { 
  height: 100%; 
  margin: 0;
  padding: 0;
}

body { 
  font-family: 'Inter', "Helvetica Neue", sans-serif;
  background: var(--mat-sys-surface);
  color: var(--mat-sys-on-surface);
}

// Smooth transitions
* {
  transition: background-color 0.3s ease, color 0.3s ease;
}

// Custom scrollbar
::-webkit-scrollbar {
  width: 8px;
}

::-webkit-scrollbar-track {
  background: var(--mat-sys-surface-variant);
}

::-webkit-scrollbar-thumb {
  background: var(--mat-sys-outline);
  border-radius: 4px;
}

::-webkit-scrollbar-thumb:hover {
  background: var(--mat-sys-outline-variant);
}
```

---

## 🏗️ Phase 2: Core Services (1 hour)

### Step 2.1: Create Type Definitions (15 minutes)

```typescript
// src/app/core/models/dashboard.types.ts
export interface DashboardStats {
  id: string;
  title: string;
  value: number | string;
  icon: string;
  color: 'primary' | 'secondary' | 'tertiary' | 'error' | 'success' | 'warning';
  trend?: {
    value: number;
    direction: 'up' | 'down' | 'neutral';
  };
  description?: string;
}

export interface Activity {
  id: string;
  type: 'task' | 'project' | 'message' | 'file' | 'meeting';
  title: string;
  description: string;
  timestamp: Date;
  user?: {
    name: string;
    avatar?: string;
  };
  metadata?: {
    [key: string]: any;
  };
}

export interface QuickAction {
  id: string;
  title: string;
  description: string;
  icon: string;
  color: string;
  action: string;
  disabled?: boolean;
}

export interface DashboardConfig {
  theme: 'light' | 'dark' | 'auto';
  compactMode: boolean;
  showNotifications: boolean;
  refreshInterval: number;
}
```

### Step 2.2: Create Dashboard Service (30 minutes)

```typescript
// src/app/core/services/dashboard.service.ts
import { Injectable, signal, computed } from '@angular/core';
import { DashboardStats, Activity, QuickAction } from '../models/dashboard.types';

@Injectable({
  providedIn: 'root'
})
export class DashboardService {
  // Reactive state
  private _stats = signal<DashboardStats[]>([]);
  private _activities = signal<Activity[]>([]);
  private _quickActions = signal<QuickAction[]>([]);
  private _isLoading = signal(false);

  // Public readonly signals
  readonly stats = this._stats.asReadonly();
  readonly activities = this._activities.asReadonly();
  readonly quickActions = this._quickActions.asReadonly();
  readonly isLoading = this._isLoading.asReadonly();

  // Computed values
  readonly totalTasks = computed(() => {
    const taskStat = this._stats().find(s => s.id === 'tasks');
    return taskStat ? Number(taskStat.value) : 0;
  });

  readonly recentActivities = computed(() => 
    this._activities().slice(0, 5)
  );

  constructor() {
    this.loadMockData();
  }

  // Public methods
  async refreshData(): Promise<void> {
    this._isLoading.set(true);
    
    try {
      // Simulate API call
      await new Promise(resolve => setTimeout(resolve, 1000));
      this.loadMockData();
    } finally {
      this._isLoading.set(false);
    }
  }

  addActivity(activity: Omit<Activity, 'id' | 'timestamp'>): void {
    const newActivity: Activity = {
      ...activity,
      id: Date.now().toString(),
      timestamp: new Date()
    };
    
    this._activities.update(activities => [newActivity, ...activities]);
  }

  updateStats(stats: DashboardStats[]): void {
    this._stats.set(stats);
  }

  // Private methods
  private loadMockData(): void {
    // Mock stats data
    const mockStats: DashboardStats[] = [
      {
        id: 'tasks',
        title: 'Active Tasks',
        value: 24,
        icon: 'task_alt',
        color: 'primary',
        trend: { value: 12, direction: 'up' },
        description: 'Tasks in progress'
      },
      {
        id: 'projects',
        title: 'Projects',
        value: 8,
        icon: 'folder',
        color: 'secondary',
        trend: { value: 2, direction: 'up' },
        description: 'Active projects'
      },
      {
        id: 'messages',
        title: 'Messages',
        value: 42,
        icon: 'message',
        color: 'tertiary',
        trend: { value: 8, direction: 'down' },
        description: 'Unread messages'
      },
      {
        id: 'completion',
        title: 'Completion Rate',
        value: '87%',
        icon: 'trending_up',
        color: 'success',
        trend: { value: 5, direction: 'up' },
        description: 'This month'
      }
    ];

    // Mock activities data
    const mockActivities: Activity[] = [
      {
        id: '1',
        type: 'task',
        title: 'Task Completed',
        description: 'Finished implementing dashboard layout',
        timestamp: new Date(Date.now() - 1000 * 60 * 5), // 5 minutes ago
        user: { name: 'John Doe' }
      },
      {
        id: '2',
        type: 'project',
        title: 'Project Updated',
        description: 'Personal Dashboard project milestone reached',
        timestamp: new Date(Date.now() - 1000 * 60 * 30), // 30 minutes ago
        user: { name: 'Jane Smith' }
      },
      {
        id: '3',
        type: 'message',
        title: 'New Message',
        description: 'Meeting scheduled for tomorrow at 2 PM',
        timestamp: new Date(Date.now() - 1000 * 60 * 60), // 1 hour ago
        user: { name: 'Mike Johnson' }
      },
      {
        id: '4',
        type: 'file',
        title: 'File Uploaded',
        description: 'Design mockups added to project folder',
        timestamp: new Date(Date.now() - 1000 * 60 * 120), // 2 hours ago
        user: { name: 'Sarah Wilson' }
      },
      {
        id: '5',
        type: 'meeting',
        title: 'Meeting Reminder',
        description: 'Standup meeting in 15 minutes',
        timestamp: new Date(Date.now() - 1000 * 60 * 180), // 3 hours ago,
        user: { name: 'Team Calendar' }
      }
    ];

    // Mock quick actions
    const mockQuickActions: QuickAction[] = [
      {
        id: 'create-task',
        title: 'Create Task',
        description: 'Add a new task to your list',
        icon: 'add_task',
        color: 'primary',
        action: 'CREATE_TASK'
      },
      {
        id: 'new-project',
        title: 'New Project',
        description: 'Start a new project',
        icon: 'create_new_folder',
        color: 'secondary',
        action: 'CREATE_PROJECT'
      },
      {
        id: 'schedule-meeting',
        title: 'Schedule Meeting',
        description: 'Book a meeting with your team',
        icon: 'event',
        color: 'tertiary',
        action: 'SCHEDULE_MEETING'
      },
      {
        id: 'upload-file',
        title: 'Upload File',
        description: 'Share files with your team',
        icon: 'cloud_upload',
        color: 'success',
        action: 'UPLOAD_FILE'
      }
    ];

    this._stats.set(mockStats);
    this._activities.set(mockActivities);
    this._quickActions.set(mockQuickActions);
  }
}
```

### Step 2.3: Create Theme Service (15 minutes)

```typescript
// src/app/core/services/theme.service.ts
import { Injectable, signal, effect } from '@angular/core';

export type ThemeMode = 'light' | 'dark' | 'auto';

@Injectable({
  providedIn: 'root'
})
export class ThemeService {
  private _currentTheme = signal<ThemeMode>('light');
  private _isDarkMode = signal(false);

  readonly currentTheme = this._currentTheme.asReadonly();
  readonly isDarkMode = this._isDarkMode.asReadonly();

  constructor() {
    // Load saved theme from localStorage
    const savedTheme = localStorage.getItem('theme') as ThemeMode;
    if (savedTheme) {
      this._currentTheme.set(savedTheme);
    }

    // Apply theme changes to DOM
    effect(() => {
      this.applyTheme(this._currentTheme());
    });

    // Listen for system theme changes
    this.listenForSystemThemeChanges();
  }

  setTheme(theme: ThemeMode): void {
    this._currentTheme.set(theme);
    localStorage.setItem('theme', theme);
  }

  toggleTheme(): void {
    const current = this._currentTheme();
    const newTheme = current === 'light' ? 'dark' : 'light';
    this.setTheme(newTheme);
  }

  private applyTheme(theme: ThemeMode): void {
    const body = document.body;
    
    // Remove existing theme classes
    body.classList.remove('light-theme', 'dark-theme');
    
    // Determine which theme to apply
    let actualTheme = theme;
    if (theme === 'auto') {
      actualTheme = this.getSystemTheme();
    }
    
    // Apply theme class
    body.classList.add(`${actualTheme}-theme`);
    this._isDarkMode.set(actualTheme === 'dark');
  }

  private getSystemTheme(): 'light' | 'dark' {
    return window.matchMedia('(prefers-color-scheme: dark)').matches ? 'dark' : 'light';
  }

  private listenForSystemThemeChanges(): void {
    window.matchMedia('(prefers-color-scheme: dark)')
      .addEventListener('change', () => {
        if (this._currentTheme() === 'auto') {
          this.applyTheme('auto');
        }
      });
  }
}
```

---

## 🏗️ Phase 3: Layout Components (3 hours)

### Step 3.1: Create Main Layout Component (45 minutes)

```bash
# Generate the main layout component
ng generate component layout/main-layout --standalone
```

```typescript
// src/app/layout/main-layout/main-layout.component.ts
import { Component, signal, inject, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { RouterOutlet } from '@angular/router';
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
    RouterOutlet,
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
        [mode]="sidenavMode()"
        [opened]="sidenavOpened()"
        [fixedInViewport]="isHandset()"
        fixedTopGap="0">
        <app-sidebar (menuItemSelected)="onMenuItemSelected(drawer)"></app-sidebar>
      </mat-sidenav>

      <!-- Main Content Area -->
      <mat-sidenav-content class="main-content">
        <!-- Header Toolbar -->
        <app-header 
          [isHandset]="isHandset()"
          (menuToggle)="drawer.toggle()"
          (themeToggle)="onThemeToggle()">
        </app-header>

        <!-- Page Content -->
        <main class="content-area">
          <router-outlet></router-outlet>
        </main>
      </mat-sidenav-content>
    </mat-sidenav-container>
  `,
  styles: [`
    .layout-container {
      height: 100vh;
      background: var(--mat-sys-surface);
    }

    .sidenav {
      width: 280px;
      background: var(--mat-sys-surface-container-low);
      border-right: 1px solid var(--mat-sys-outline-variant);
    }

    .main-content {
      display: flex;
      flex-direction: column;
      height: 100%;
    }

    .content-area {
      flex: 1;
      padding: 24px;
      background: var(--mat-sys-surface);
      overflow-y: auto;
      overflow-x: hidden;
    }

    @media (max-width: 768px) {
      .content-area {
        padding: 16px;
      }
    }

    // Smooth transitions
    .sidenav, .main-content {
      transition: all 0.3s cubic-bezier(0.25, 0.8, 0.25, 1);
    }
  `]
})
export class MainLayoutComponent implements OnInit {
  private breakpointObserver = inject(BreakpointObserver);

  // Reactive state
  isHandset = signal(false);
  sidenavMode = signal<'over' | 'side'>('side');
  sidenavOpened = signal(true);

  ngOnInit() {
    this.setupResponsiveLayout();
  }

  onMenuItemSelected(drawer: any) {
    if (this.isHandset()) {
      drawer.close();
    }
  }

  onThemeToggle() {
    // Theme toggle logic will be implemented
    console.log('Theme toggle requested');
  }

  private setupResponsiveLayout() {
    // Monitor breakpoint changes
    this.breakpointObserver.observe([
      Breakpoints.Handset,
      Breakpoints.Small,
      '(max-width: 768px)'
    ]).subscribe(result => {
      const isHandset = result.matches;
      
      this.isHandset.set(isHandset);
      
      if (isHandset) {
        this.sidenavMode.set('over');
        this.sidenavOpened.set(false);
      } else {
        this.sidenavMode.set('side');
        this.sidenavOpened.set(true);
      }
    });
  }
}
```

### Step 3.2: Create Header Component (45 minutes)

```bash
# Generate header component
ng generate component layout/header --standalone
```

```typescript
// src/app/layout/header/header.component.ts
import { Component, input, output, inject } from '@angular/core';
import { CommonModule } from '@angular/common';
import { MatToolbarModule } from '@angular/material/toolbar';
import { MatIconModule } from '@angular/material/icon';
import { MatButtonModule } from '@angular/material/button';
import { MatMenuModule } from '@angular/material/menu';
import { MatBadgeModule } from '@angular/material/badge';
import { MatTooltipModule } from '@angular/material/tooltip';

import { ThemeService } from '../../core/services/theme.service';

@Component({
  selector: 'app-header',
  standalone: true,
  imports: [
    CommonModule,
    MatToolbarModule,
    MatIconModule,
    MatButtonModule,
    MatMenuModule,
    MatBadgeModule,
    MatTooltipModule
  ],
  template: `
    <mat-toolbar class="header-toolbar">
      <!-- Menu Toggle Button (Mobile) -->
      @if (isHandset()) {
        <button 
          mat-icon-button 
          (click)="menuToggle.emit()"
          matTooltip="Toggle menu"
          aria-label="Toggle menu">
          <mat-icon>menu</mat-icon>
        </button>
      }

      <!-- App Title -->
      <span class="app-title">Personal Dashboard</span>

      <!-- Spacer -->
      <span class="spacer"></span>

      <!-- Header Actions -->
      <div class="header-actions">
        <!-- Search Button -->
        <button 
          mat-icon-button 
          matTooltip="Search"
          aria-label="Search">
          <mat-icon>search</mat-icon>
        </button>

        <!-- Notifications -->
        <button 
          mat-icon-button 
          [matMenuTriggerFor]="notificationMenu"
          matTooltip="Notifications"
          aria-label="Notifications"
          matBadge="3" 
          matBadgeColor="accent"
          matBadgeSize="small">
          <mat-icon>notifications</mat-icon>
        </button>

        <!-- Theme Toggle -->
        <button 
          mat-icon-button 
          (click)="toggleTheme()"
          [matTooltip]="themeService.isDarkMode() ? 'Switch to light mode' : 'Switch to dark mode'"
          aria-label="Toggle theme">
          <mat-icon>
            {{ themeService.isDarkMode() ? 'light_mode' : 'dark_mode' }}
          </mat-icon>
        </button>

        <!-- User Profile -->
        <button 
          mat-icon-button 
          [matMenuTriggerFor]="profileMenu"
          matTooltip="Profile menu"
          aria-label="User profile">
          <mat-icon>account_circle</mat-icon>
        </button>
      </div>

      <!-- Notification Menu -->
      <mat-menu #notificationMenu="matMenu" class="notification-menu">
        <div class="menu-header">
          <h3>Notifications</h3>
          <button mat-button color="primary">Mark all read</button>
        </div>
        <mat-divider></mat-divider>
        
        <button mat-menu-item class="notification-item">
          <div class="notification-content">
            <div class="notification-title">New task assigned</div>
            <div class="notification-time">5 minutes ago</div>
          </div>
        </button>
        
        <button mat-menu-item class="notification-item">
          <div class="notification-content">
            <div class="notification-title">Project deadline approaching</div>
            <div class="notification-time">1 hour ago</div>
          </div>
        </button>
        
        <button mat-menu-item class="notification-item">
          <div class="notification-content">
            <div class="notification-title">Team meeting in 30 minutes</div>
            <div class="notification-time">2 hours ago</div>
          </div>
        </button>
        
        <mat-divider></mat-divider>
        <button mat-menu-item class="view-all-btn">
          <mat-icon>visibility</mat-icon>
          View all notifications
        </button>
      </mat-menu>

      <!-- Profile Menu -->
      <mat-menu #profileMenu="matMenu" class="profile-menu">
        <div class="profile-header">
          <div class="profile-avatar">
            <mat-icon>account_circle</mat-icon>
          </div>
          <div class="profile-info">
            <div class="profile-name">John Doe</div>
            <div class="profile-email">john.doe@example.com</div>
          </div>
        </div>
        <mat-divider></mat-divider>
        
        <button mat-menu-item>
          <mat-icon>person</mat-icon>
          <span>Profile</span>
        </button>
        
        <button mat-menu-item>
          <mat-icon>settings</mat-icon>
          <span>Settings</span>
        </button>
        
        <button mat-menu-item>
          <mat-icon>help</mat-icon>
          <span>Help & Support</span>
        </button>
        
        <mat-divider></mat-divider>
        
        <button mat-menu-item class="logout-btn">
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
      position: sticky;
      top: 0;
      z-index: 100;
    }

    .app-title {
      font-size: 1.25rem;
      font-weight: 500;
      margin-left: 16px;
      color: var(--mat-sys-on-surface);
    }

    .spacer {
      flex: 1 1 auto;
    }

    .header-actions {
      display: flex;
      align-items: center;
      gap: 4px;
    }

    // Menu styles
    .menu-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 12px 16px;
      
      h3 {
        margin: 0;
        font-size: 1rem;
        font-weight: 500;
      }
    }

    .notification-menu {
      width: 320px;
    }

    .notification-item {
      padding: 12px 16px;
      
      .notification-content {
        display: flex;
        flex-direction: column;
        align-items: flex-start;
        gap: 4px;
      }
      
      .notification-title {
        font-weight: 500;
        color: var(--mat-sys-on-surface);
      }
      
      .notification-time {
        font-size: 0.875rem;
        color: var(--mat-sys-on-surface-variant);
      }
    }

    .view-all-btn {
      color: var(--mat-sys-primary);
      font-weight: 500;
    }

    .profile-menu {
      width: 280px;
    }

    .profile-header {
      display: flex;
      align-items: center;
      gap: 12px;
      padding: 16px;
      background: var(--mat-sys-primary-container);
      color: var(--mat-sys-on-primary-container);
      
      .profile-avatar {
        mat-icon {
          font-size: 48px;
          width: 48px;
          height: 48px;
        }
      }
      
      .profile-info {
        display: flex;
        flex-direction: column;
        gap: 4px;
        
        .profile-name {
          font-weight: 500;
          font-size: 1rem;
        }
        
        .profile-email {
          font-size: 0.875rem;
          opacity: 0.8;
        }
      }
    }

    .logout-btn {
      color: var(--mat-sys-error);
    }

    // Responsive adjustments
    @media (max-width: 768px) {
      .app-title {
        font-size: 1.125rem;
        margin-left: 8px;
      }
      
      .header-actions {
        gap: 2px;
      }
    }
  `]
})
export class HeaderComponent {
  // Inputs
  isHandset = input.required<boolean>();
  
  // Outputs
  menuToggle = output<void>();
  themeToggle = output<void>();
  
  // Services
  themeService = inject(ThemeService);

  toggleTheme() {
    this.themeService.toggleTheme();
    this.themeToggle.emit();
  }
}
```

### Step 3.3: Create Sidebar Component (45 minutes)

```bash
# Generate sidebar component
ng generate component layout/sidebar --standalone
```

```typescript
// src/app/layout/sidebar/sidebar.component.ts
import { Component, output, signal } from '@angular/core';
import { CommonModule } from '@angular/common';
import { RouterModule } from '@angular/router';
import { MatListModule } from '@angular/material/list';
import { MatIconModule } from '@angular/material/icon';
import { MatDividerModule } from '@angular/material/divider';
import { MatButtonModule } from '@angular/material/button';
import { MatTooltipModule } from '@angular/material/tooltip';

interface NavigationItem {
  id: string;
  label: string;
  icon: string;
  route: string;
  badge?: string;
  tooltip?: string;
  children?: NavigationItem[];
}

interface NavigationSection {
  title: string;
  items: NavigationItem[];
}

@Component({
  selector: 'app-sidebar',
  standalone: true,
  imports: [
    CommonModule,
    RouterModule,
    MatListModule,
    MatIconModule,
    MatDividerModule,
    MatButtonModule,
    MatTooltipModule
  ],
  template: `
    <div class="sidebar-content">
      <!-- User Profile Section -->
      <div class="user-profile">
        <div class="user-avatar">
          <mat-icon class="avatar-icon">account_circle</mat-icon>
        </div>
        <div class="user-info">
          <h3 class="user-name">John Doe</h3>
          <p class="user-role">Product Manager</p>
          <p class="user-email">john.doe@example.com</p>
        </div>
      </div>

      <mat-divider></mat-divider>

      <!-- Navigation Sections -->
      @for (section of navigationSections; track section.title) {
        <div class="nav-section">
          <h4 class="section-title">{{ section.title }}</h4>
          <mat-nav-list class="nav-list">
            @for (item of section.items; track item.id) {
              <mat-list-item 
                [routerLink]="item.route"
                routerLinkActive="active-link"
                [matTooltip]="item.tooltip || ''"
                [matTooltipDisabled]="!item.tooltip"
                (click)="onItemClick(item)">
                
                <mat-icon matListItemIcon>{{ item.icon }}</mat-icon>
                <span matListItemTitle>{{ item.label }}</span>
                
                @if (item.badge) {
                  <span class="nav-badge" matListItemMeta>{{ item.badge }}</span>
                }
              </mat-list-item>
            }
          </mat-nav-list>
        </div>
        
        @if (!$last) {
          <mat-divider></mat-divider>
        }
      }

      <!-- Footer Actions -->
      <div class="sidebar-footer">
        <mat-divider></mat-divider>
        <div class="footer-actions">
          <button mat-button class="footer-btn" matTooltip="Feedback">
            <mat-icon>feedback</mat-icon>
            <span>Feedback</span>
          </button>
          <button mat-button class="footer-btn" matTooltip="Keyboard shortcuts">
            <mat-icon>keyboard</mat-icon>
            <span>Shortcuts</span>
          </button>
        </div>
      </div>
    </div>
  `,
  styles: [`
    .sidebar-content {
      height: 100%;
      display: flex;
      flex-direction: column;
      overflow-y: auto;
    }

    // User profile section
    .user-profile {
      padding: 24px 16px;
      background: var(--mat-sys-primary-container);
      color: var(--mat-sys-on-primary-container);
      
      .user-avatar {
        text-align: center;
        margin-bottom: 16px;
        
        .avatar-icon {
          font-size: 64px;
          width: 64px;
          height: 64px;
          color: var(--mat-sys-primary);
        }
      }
      
      .user-info {
        text-align: center;
        
        .user-name {
          margin: 0 0 4px 0;
          font-size: 1.125rem;
          font-weight: 500;
        }
        
        .user-role {
          margin: 0 0 8px 0;
          font-size: 0.875rem;
          font-weight: 500;
          color: var(--mat-sys-primary);
        }
        
        .user-email {
          margin: 0;
          font-size: 0.875rem;
          opacity: 0.8;
        }
      }
    }

    // Navigation sections
    .nav-section {
      padding: 16px 0 8px 0;
      
      .section-title {
        margin: 0 0 8px 0;
        padding: 0 16px;
        font-size: 0.75rem;
        font-weight: 600;
        text-transform: uppercase;
        letter-spacing: 0.5px;
        color: var(--mat-sys-on-surface-variant);
      }
      
      .nav-list {
        padding: 0;
      }
    }

    // Navigation items
    mat-list-item {
      margin: 0 8px 4px 8px;
      border-radius: 12px;
      transition: background-color 0.2s ease;
      
      &:hover {
        background: var(--mat-sys-surface-container-high);
      }
      
      &.active-link {
        background: var(--mat-sys-secondary-container) !important;
        color: var(--mat-sys-on-secondary-container) !important;
        
        mat-icon {
          color: var(--mat-sys-on-secondary-container) !important;
        }
      }
    }

    .nav-badge {
      background: var(--mat-sys-error);
      color: var(--mat-sys-on-error);
      border-radius: 10px;
      padding: 2px 6px;
      font-size: 0.75rem;
      font-weight: 500;
      min-width: 18px;
      text-align: center;
    }

    // Footer
    .sidebar-footer {
      margin-top: auto;
      padding: 16px 0;
      
      .footer-actions {
        padding: 8px;
        display: flex;
        flex-direction: column;
        gap: 4px;
        
        .footer-btn {
          justify-content: flex-start;
          padding: 8px 16px;
          color: var(--mat-sys-on-surface-variant);
          
          mat-icon {
            margin-right: 12px;
            font-size: 20px;
            width: 20px;
            height: 20px;
          }
          
          &:hover {
            background: var(--mat-sys-surface-container-high);
          }
        }
      }
    }

    // Scrollbar customization
    .sidebar-content::-webkit-scrollbar {
      width: 4px;
    }
    
    .sidebar-content::-webkit-scrollbar-track {
      background: transparent;
    }
    
    .sidebar-content::-webkit-scrollbar-thumb {
      background: var(--mat-sys-outline-variant);
      border-radius: 2px;
    }
    
    .sidebar-content::-webkit-scrollbar-thumb:hover {
      background: var(--mat-sys-outline);
    }
  `]
})
export class SidebarComponent {
  menuItemSelected = output<NavigationItem>();

  navigationSections: NavigationSection[] = [
    {
      title: 'Main',
      items: [
        {
          id: 'dashboard',
          label: 'Dashboard',
          icon: 'dashboard',
          route: '/dashboard',
          tooltip: 'Main dashboard overview'
        },
        {
          id: 'analytics',
          label: 'Analytics',
          icon: 'analytics',
          route: '/analytics',
          tooltip: 'View detailed analytics'
        }
      ]
    },
    {
      title: 'Work',
      items: [
        {
          id: 'projects',
          label: 'Projects',
          icon: 'folder',
          route: '/projects',
          badge: '3',
          tooltip: 'Manage your projects'
        },
        {
          id: 'tasks',
          label: 'Tasks',
          icon: 'task_alt',
          route: '/tasks',
          badge: '12',
          tooltip: 'View and manage tasks'
        },
        {
          id: 'calendar',
          label: 'Calendar',
          icon: 'calendar_today',
          route: '/calendar',
          tooltip: 'Schedule and events'
        }
      ]
    },
    {
      title: 'Communication',
      items: [
        {
          id: 'messages',
          label: 'Messages',
          icon: 'message',
          route: '/messages',
          badge: '5',
          tooltip: 'Team messages and chat'
        },
        {
          id: 'notifications',
          label: 'Notifications',
          icon: 'notifications',
          route: '/notifications',
          tooltip: 'All notifications'
        }
      ]
    },
    {
      title: 'Resources',
      items: [
        {
          id: 'files',
          label: 'Files',
          icon: 'folder_open',
          route: '/files',
          tooltip: 'File storage and sharing'
        },
        {
          id: 'team',
          label: 'Team',
          icon: 'group',
          route: '/team',
          tooltip: 'Team members and roles'
        }
      ]
    }
  ];

  onItemClick(item: NavigationItem) {
    this.menuItemSelected.emit(item);
  }
}
```

### Step 3.4: Update App Routes (15 minutes)

```typescript
// src/app/app.routes.ts
import { Routes } from '@angular/router';

export const routes: Routes = [
  {
    path: '',
    redirectTo: 'dashboard',
    pathMatch: 'full'
  },
  {
    path: 'dashboard',
    loadComponent: () => 
      import('./features/dashboard/components/dashboard-home/dashboard-home.component')
        .then(m => m.DashboardHomeComponent)
  },
  {
    path: 'analytics',
    loadComponent: () => 
      import('./features/analytics/analytics.component')
        .then(m => m.AnalyticsComponent)
  },
  {
    path: 'projects',
    loadComponent: () => 
      import('./features/projects/projects.component')
        .then(m => m.ProjectsComponent)
  },
  {
    path: 'tasks',
    loadComponent: () => 
      import('./features/tasks/tasks.component')
        .then(m => m.TasksComponent)
  },
  {
    path: 'calendar',
    loadComponent: () => 
      import('./features/calendar/calendar.component')
        .then(m => m.CalendarComponent)
  },
  {
    path: 'messages',
    loadComponent: () => 
      import('./features/messages/messages.component')
        .then(m => m.MessagesComponent)
  },
  {
    path: 'notifications',
    loadComponent: () => 
      import('./features/notifications/notifications.component')
        .then(m => m.NotificationsComponent)
  },
  {
    path: 'files',
    loadComponent: () => 
      import('./features/files/files.component')
        .then(m => m.FilesComponent)
  },
  {
    path: 'team',
    loadComponent: () => 
      import('./features/team/team.component')
        .then(m => m.TeamComponent)
  },
  {
    path: '**',
    redirectTo: 'dashboard'
  }
];
```

```typescript
// src/app/app.component.ts
import { Component } from '@angular/core';
import { RouterOutlet } from '@angular/router';
import { MainLayoutComponent } from './layout/main-layout/main-layout.component';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [RouterOutlet, MainLayoutComponent],
  template: `
    <app-main-layout>
      <router-outlet></router-outlet>
    </app-main-layout>
  `,
  styles: []
})
export class AppComponent {
  title = 'personal-dashboard';
}
```

---

## ⏱️ Time Checkpoint

At this point, you should have:
- ✅ Complete project setup with Angular Material
- ✅ Custom theme configuration
- ✅ Core services (Dashboard, Theme)
- ✅ Layout components (Main, Header, Sidebar)
- ✅ Routing configuration

**Total time invested**: ~5 hours  
**Remaining time**: ~5 hours for dashboard components and testing

Take a break and test your current implementation:

```bash
# Start the development server
ng serve

# Navigate to http://localhost:4200
# You should see the layout with sidebar and header
```

---

## 🎯 Next Phase

Continue with [Phase 4: Dashboard Components](./project-guide-phase4.md) to complete the dashboard implementation.

## 🆘 Troubleshooting

### Common Issues at This Stage

1. **Material Theme Not Applied**
   ```scss
   // Ensure this is in src/styles.scss
   @use 'styles/themes/material-theme';
   ```

2. **Sidebar Not Responsive**
   ```typescript
   // Check BreakpointObserver is properly injected
   private breakpointObserver = inject(BreakpointObserver);
   ```

3. **Icons Not Displaying**
   ```html
   <!-- Ensure Material Icons are loaded in index.html -->
   <link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet">
   ```

4. **Routing Not Working**
   ```typescript
   // Verify routes are properly configured in app.routes.ts
   // and components are generated/imported correctly
   ```

---

*Continue to [Phase 4: Dashboard Components](./project-guide-phase4.md)*
