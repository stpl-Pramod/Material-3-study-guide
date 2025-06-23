# Micro-Frontend Framework Implementation Guide

## 🚀 **Phase 1: Core Architecture Setup**

### **Step 1: Initialize Theme Shell Application**

```bash
# Create the shell application
ng new theme-shell --routing --style=scss --package-manager=npm
cd theme-shell

# Install dependencies
npm install @angular/material @angular/cdk
npm install @angular-architects/module-federation
npm install rxjs lodash-es
npm install --save-dev @types/lodash-es
```

### **Step 2: Configure Module Federation**

```typescript
// webpack.config.js
const ModuleFederationPlugin = require("@angular-architects/module-federation/webpack");

module.exports = {
  plugins: [
    new ModuleFederationPlugin({
      name: "themeShell",
      filename: "remoteEntry.js",
      exposes: {
        './ThemeService': './src/app/theme/theme-shell.service.ts',
        './EventBus': './src/app/shared/event-bus.service.ts'
      },
      shared: {
        "@angular/core": { singleton: true, strictVersion: true, requiredVersion: "^17.0.0" },
        "@angular/common": { singleton: true, strictVersion: true, requiredVersion: "^17.0.0" },
        "@angular/material": { singleton: true, strictVersion: true, requiredVersion: "^17.0.0" },
        "rxjs": { singleton: true, strictVersion: true, requiredVersion: "^7.8.0" }
      }
    })
  ]
};
```

### **Step 3: Create Theme Shell Service**

```typescript
// src/app/theme/theme-shell.service.ts
import { Injectable, inject } from '@angular/core';
import { BehaviorSubject, Observable } from 'rxjs';
import { ThemeRegistryService } from './theme-registry.service';
import { ThemeLoaderService } from './theme-loader.service';
import { EventBusService } from '../shared/event-bus.service';

export interface ThemeConfiguration {
  id: string;
  version: string;
  name: string;
  tokens: Record<string, string>;
  cssFiles: string[];
  metadata: {
    description: string;
    category: string;
    author: string;
    tags: string[];
  };
}

export interface ThemeAdapter {
  applyTheme(theme: ThemeConfiguration): Promise<void>;
  getThemeCompatibility(): string[];
  onThemeError(error: Error): void;
}

@Injectable({
  providedIn: 'root'
})
export class ThemeShellService {
  private themeRegistry = inject(ThemeRegistryService);
  private themeLoader = inject(ThemeLoaderService);
  private eventBus = inject(EventBusService);
  
  private currentTheme$ = new BehaviorSubject<ThemeConfiguration | null>(null);
  private mfeThemeAdapters = new Map<string, ThemeAdapter>();
  private themeHistory: string[] = [];
  
  readonly currentTheme = this.currentTheme$.asObservable();
  
  constructor() {
    this.initializeThemeSystem();
    this.setupMFECommunication();
    this.setupErrorHandling();
  }
  
  async switchTheme(themeId: string): Promise<void> {
    const startTime = performance.now();
    
    try {
      // Load theme configuration
      const theme = await this.themeLoader.loadTheme(themeId);
      
      // Apply to all registered MFEs in parallel
      const applyPromises = Array.from(this.mfeThemeAdapters.entries())
        .map(([mfeId, adapter]) => 
          this.applyThemeToMFE(mfeId, adapter, theme)
        );
      
      const results = await Promise.allSettled(applyPromises);
      
      // Handle any failures
      results.forEach((result, index) => {
        if (result.status === 'rejected') {
          const mfeId = Array.from(this.mfeThemeAdapters.keys())[index];
          console.error(`Failed to apply theme to MFE ${mfeId}:`, result.reason);
          this.eventBus.emit('mfe-theme-error', { mfeId, error: result.reason });
        }
      });
      
      // Update current theme
      this.currentTheme$.next(theme);
      this.themeHistory.push(themeId);
      
      // Record performance metrics
      const loadTime = performance.now() - startTime;
      this.eventBus.emit('theme-switched', { 
        themeId, 
        loadTime, 
        successfulMFEs: results.filter(r => r.status === 'fulfilled').length,
        failedMFEs: results.filter(r => r.status === 'rejected').length
      });
      
    } catch (error) {
      console.error('Failed to switch theme:', error);
      this.eventBus.emit('theme-switch-error', { themeId, error });
      throw error;
    }
  }
  
  registerMicroFrontend(mfeId: string, adapter: ThemeAdapter): void {
    console.log(`Registering micro-frontend: ${mfeId}`);
    
    this.mfeThemeAdapters.set(mfeId, adapter);
    
    // Apply current theme to new MFE
    const currentTheme = this.currentTheme$.value;
    if (currentTheme) {
      this.applyThemeToMFE(mfeId, adapter, currentTheme)
        .catch(error => {
          console.error(`Failed to apply current theme to new MFE ${mfeId}:`, error);
        });
    }
    
    this.eventBus.emit('mfe-registered', { mfeId, adapterType: adapter.constructor.name });
  }
  
  unregisterMicroFrontend(mfeId: string): void {
    if (this.mfeThemeAdapters.has(mfeId)) {
      this.mfeThemeAdapters.delete(mfeId);
      this.eventBus.emit('mfe-unregistered', { mfeId });
    }
  }
  
  async preloadTheme(themeId: string): Promise<void> {
    try {
      await this.themeLoader.loadTheme(themeId);
      this.eventBus.emit('theme-preloaded', { themeId });
    } catch (error) {
      console.error(`Failed to preload theme ${themeId}:`, error);
    }
  }
  
  getRegisteredMFEs(): string[] {
    return Array.from(this.mfeThemeAdapters.keys());
  }
  
  getThemeHistory(): string[] {
    return [...this.themeHistory];
  }
  
  private async initializeThemeSystem(): Promise<void> {
    try {
      // Load available themes
      await this.themeRegistry.initialize();
      
      // Load default theme
      const defaultTheme = await this.themeRegistry.getDefaultTheme();
      if (defaultTheme) {
        this.currentTheme$.next(defaultTheme);
      }
      
      // Preload popular themes
      this.preloadPopularThemes();
      
    } catch (error) {
      console.error('Failed to initialize theme system:', error);
      this.eventBus.emit('theme-system-error', { error });
    }
  }
  
  private setupMFECommunication(): void {
    // Listen for MFE registration events
    this.eventBus.on('mfe-ready').subscribe(event => {
      this.handleMFERegistration(event.data.mfeId, event.data.adapter);
    });
    
    // Handle theme sync requests
    this.eventBus.on('theme-sync-request').subscribe(event => {
      this.syncThemeToMFE(event.data.mfeId);
    });
    
    // Handle MFE health checks
    this.eventBus.on('mfe-health-check').subscribe(event => {
      this.performMFEHealthCheck(event.data.mfeId);
    });
  }
  
  private setupErrorHandling(): void {
    // Global error handler for theme-related errors
    this.eventBus.on('theme-error').subscribe(event => {
      this.handleThemeError(event.data);
    });
    
    // MFE error recovery
    this.eventBus.on('mfe-theme-error').subscribe(event => {
      this.attemptMFERecovery(event.data.mfeId, event.data.error);
    });
  }
  
  private async applyThemeToMFE(
    mfeId: string, 
    adapter: ThemeAdapter, 
    theme: ThemeConfiguration
  ): Promise<void> {
    const startTime = performance.now();
    
    try {
      await adapter.applyTheme(theme);
      
      const applyTime = performance.now() - startTime;
      this.eventBus.emit('mfe-theme-applied', { mfeId, themeId: theme.id, applyTime });
      
    } catch (error) {
      adapter.onThemeError(error as Error);
      throw error;
    }
  }
  
  private async preloadPopularThemes(): Promise<void> {
    const popularThemes = await this.themeRegistry.getPopularThemes();
    
    const preloadPromises = popularThemes.map(themeId => 
      this.preloadTheme(themeId).catch(error => 
        console.warn(`Failed to preload theme ${themeId}:`, error)
      )
    );
    
    await Promise.allSettled(preloadPromises);
  }
  
  private handleMFERegistration(mfeId: string, adapter: ThemeAdapter): void {
    this.registerMicroFrontend(mfeId, adapter);
  }
  
  private async syncThemeToMFE(mfeId: string): Promise<void> {
    const adapter = this.mfeThemeAdapters.get(mfeId);
    const currentTheme = this.currentTheme$.value;
    
    if (adapter && currentTheme) {
      await this.applyThemeToMFE(mfeId, adapter, currentTheme);
    }
  }
  
  private async performMFEHealthCheck(mfeId: string): Promise<void> {
    const adapter = this.mfeThemeAdapters.get(mfeId);
    
    if (adapter) {
      // Perform health check logic
      this.eventBus.emit('mfe-health-status', { 
        mfeId, 
        status: 'healthy', 
        timestamp: Date.now() 
      });
    }
  }
  
  private handleThemeError(errorData: any): void {
    console.error('Theme system error:', errorData);
    
    // Implement error recovery strategies
    // e.g., fallback to default theme, retry with exponential backoff, etc.
  }
  
  private async attemptMFERecovery(mfeId: string, error: Error): Promise<void> {
    console.log(`Attempting recovery for MFE ${mfeId} after error:`, error);
    
    // Implement recovery strategies
    // e.g., re-register MFE, apply fallback theme, restart MFE, etc.
    
    const adapter = this.mfeThemeAdapters.get(mfeId);
    if (adapter && this.currentTheme$.value) {
      try {
        // Try to re-apply current theme
        await this.applyThemeToMFE(mfeId, adapter, this.currentTheme$.value);
        this.eventBus.emit('mfe-recovered', { mfeId });
      } catch (recoveryError) {
        console.error(`Failed to recover MFE ${mfeId}:`, recoveryError);
        this.eventBus.emit('mfe-recovery-failed', { mfeId, error: recoveryError });
      }
    }
  }
}
```

### **Step 4: Create Theme Registry Service**

```typescript
// src/app/theme/theme-registry.service.ts
import { Injectable } from '@angular/core';
import { ThemeConfiguration } from './theme-shell.service';

interface ThemeMetadata {
  id: string;
  name: string;
  version: string;
  category: string;
  popularity: number;
  compatibility: string[];
  cdnUrl: string;
}

@Injectable({
  providedIn: 'root'
})
export class ThemeRegistryService {
  private themes = new Map<string, ThemeMetadata>();
  private categories = new Set<string>();
  private defaultThemeId = 'material-3-default';
  
  async initialize(): Promise<void> {
    try {
      // Fetch theme registry from API
      const response = await fetch('/api/themes/registry');
      const registry = await response.json();
      
      // Populate themes map
      registry.themes.forEach((theme: ThemeMetadata) => {
        this.themes.set(theme.id, theme);
        this.categories.add(theme.category);
      });
      
      console.log(`Loaded ${this.themes.size} themes from registry`);
      
    } catch (error) {
      console.error('Failed to initialize theme registry:', error);
      
      // Fallback to hardcoded themes
      this.loadFallbackThemes();
    }
  }
  
  getTheme(themeId: string): ThemeMetadata | undefined {
    return this.themes.get(themeId);
  }
  
  getAllThemes(): ThemeMetadata[] {
    return Array.from(this.themes.values());
  }
  
  getThemesByCategory(category: string): ThemeMetadata[] {
    return Array.from(this.themes.values())
      .filter(theme => theme.category === category);
  }
  
  getPopularThemes(limit: number = 5): string[] {
    return Array.from(this.themes.values())
      .sort((a, b) => b.popularity - a.popularity)
      .slice(0, limit)
      .map(theme => theme.id);
  }
  
  async getDefaultTheme(): Promise<ThemeConfiguration | null> {
    const metadata = this.themes.get(this.defaultThemeId);
    if (!metadata) {
      return null;
    }
    
    // Load the actual theme configuration
    try {
      const response = await fetch(`${metadata.cdnUrl}/theme.json`);
      const themeConfig = await response.json();
      
      return {
        id: metadata.id,
        version: metadata.version,
        name: metadata.name,
        tokens: themeConfig.tokens,
        cssFiles: themeConfig.cssFiles.map((file: string) => 
          `${metadata.cdnUrl}/${file}`
        ),
        metadata: {
          description: themeConfig.description,
          category: metadata.category,
          author: themeConfig.author,
          tags: themeConfig.tags || []
        }
      };
      
    } catch (error) {
      console.error('Failed to load default theme:', error);
      return null;
    }
  }
  
  searchThemes(query: string): ThemeMetadata[] {
    const lowercaseQuery = query.toLowerCase();
    
    return Array.from(this.themes.values())
      .filter(theme => 
        theme.name.toLowerCase().includes(lowercaseQuery) ||
        theme.category.toLowerCase().includes(lowercaseQuery) ||
        theme.id.toLowerCase().includes(lowercaseQuery)
      );
  }
  
  getCategories(): string[] {
    return Array.from(this.categories);
  }
  
  private loadFallbackThemes(): void {
    const fallbackThemes: ThemeMetadata[] = [
      {
        id: 'material-3-default',
        name: 'Material 3 Default',
        version: '1.0.0',
        category: 'material',
        popularity: 100,
        compatibility: ['angular-17', 'angular-16'],
        cdnUrl: '/assets/themes/material-3-default'
      },
      {
        id: 'material-3-dark',
        name: 'Material 3 Dark',
        version: '1.0.0',
        category: 'material',
        popularity: 95,
        compatibility: ['angular-17', 'angular-16'],
        cdnUrl: '/assets/themes/material-3-dark'
      },
      {
        id: 'corporate-blue',
        name: 'Corporate Blue',
        version: '1.0.0',
        category: 'corporate',
        popularity: 80,
        compatibility: ['angular-17', 'angular-16'],
        cdnUrl: '/assets/themes/corporate-blue'
      }
    ];
    
    fallbackThemes.forEach(theme => {
      this.themes.set(theme.id, theme);
      this.categories.add(theme.category);
    });
  }
}
```

## 🚀 **Phase 2: Theme Loading System**

### **Step 5: Create Theme Loader Service**

```typescript
// src/app/theme/theme-loader.service.ts
import { Injectable, inject } from '@angular/core';
import { ThemeConfiguration } from './theme-shell.service';
import { ThemeRegistryService } from './theme-registry.service';
import { EventBusService } from '../shared/event-bus.service';

interface LoadingState {
  isLoading: boolean;
  progress: number;
  error?: Error;
}

@Injectable({
  providedIn: 'root'
})
export class ThemeLoaderService {
  private themeRegistry = inject(ThemeRegistryService);
  private eventBus = inject(EventBusService);
  
  private themeCache = new Map<string, ThemeConfiguration>();
  private loadingPromises = new Map<string, Promise<ThemeConfiguration>>();
  private loadingStates = new Map<string, LoadingState>();
  
  // Cache configuration
  private readonly MAX_CACHE_SIZE = 50;
  private readonly CACHE_TTL = 1000 * 60 * 60; // 1 hour
  private cacheTimestamps = new Map<string, number>();
  
  async loadTheme(themeId: string): Promise<ThemeConfiguration> {
    const startTime = performance.now();
    
    // Check cache first
    if (this.isThemeCacheValid(themeId)) {
      const cachedTheme = this.themeCache.get(themeId)!;
      this.eventBus.emit('theme-cache-hit', { themeId, loadTime: 0 });
      return cachedTheme;
    }
    
    // Check if already loading
    if (this.loadingPromises.has(themeId)) {
      return this.loadingPromises.get(themeId)!;
    }
    
    // Start loading
    this.setLoadingState(themeId, { isLoading: true, progress: 0 });
    
    const loadingPromise = this.fetchThemeFromCDN(themeId);
    this.loadingPromises.set(themeId, loadingPromise);
    
    try {
      const theme = await loadingPromise;
      
      // Cache the theme
      this.cacheTheme(themeId, theme);
      
      const loadTime = performance.now() - startTime;
      this.eventBus.emit('theme-loaded', { themeId, loadTime, fromCache: false });
      
      return theme;
      
    } catch (error) {
      this.setLoadingState(themeId, { isLoading: false, progress: 0, error: error as Error });
      this.eventBus.emit('theme-load-error', { themeId, error });
      throw error;
      
    } finally {
      this.loadingPromises.delete(themeId);
      this.loadingStates.delete(themeId);
    }
  }
  
  getLoadingState(themeId: string): LoadingState | undefined {
    return this.loadingStates.get(themeId);
  }
  
  preloadThemes(themeIds: string[]): Promise<void[]> {
    const preloadPromises = themeIds.map(themeId => 
      this.loadTheme(themeId)
        .then(() => {})
        .catch(error => {
          console.warn(`Failed to preload theme ${themeId}:`, error);
        })
    );
    
    return Promise.all(preloadPromises);
  }
  
  clearCache(): void {
    this.themeCache.clear();
    this.cacheTimestamps.clear();
    this.eventBus.emit('theme-cache-cleared', {});
  }
  
  getCacheStats(): { size: number; maxSize: number; hitRate: number } {
    // Implementation would track hit/miss ratios
    return {
      size: this.themeCache.size,
      maxSize: this.MAX_CACHE_SIZE,
      hitRate: 0.85 // Placeholder
    };
  }
  
  private async fetchThemeFromCDN(themeId: string): Promise<ThemeConfiguration> {
    const metadata = this.themeRegistry.getTheme(themeId);
    if (!metadata) {
      throw new Error(`Theme not found in registry: ${themeId}`);
    }
    
    this.updateLoadingProgress(themeId, 10);
    
    try {
      // Fetch theme configuration
      const response = await fetch(`${metadata.cdnUrl}/theme.json`);
      if (!response.ok) {
        throw new Error(`Failed to fetch theme config: ${response.statusText}`);
      }
      
      this.updateLoadingProgress(themeId, 30);
      
      const themeConfig = await response.json();
      
      this.updateLoadingProgress(themeId, 50);
      
      // Load CSS files
      const cssFiles = themeConfig.cssFiles.map((file: string) => 
        `${metadata.cdnUrl}/${file}`
      );
      
      await this.loadThemeStyles(themeId, cssFiles);
      
      this.updateLoadingProgress(themeId, 100);
      
      return {
        id: themeId,
        version: metadata.version,
        name: metadata.name,
        tokens: themeConfig.tokens,
        cssFiles,
        metadata: {
          description: themeConfig.description || '',
          category: metadata.category,
          author: themeConfig.author || 'Unknown',
          tags: themeConfig.tags || []
        }
      };
      
    } catch (error) {
      console.error(`Failed to load theme ${themeId}:`, error);
      throw error;
    }
  }
  
  private async loadThemeStyles(themeId: string, cssFiles: string[]): Promise<void> {
    const loadPromises = cssFiles.map((file, index) => 
      this.loadStylesheet(file).then(() => {
        // Update progress as each file loads
        const progress = 50 + ((index + 1) / cssFiles.length) * 50;
        this.updateLoadingProgress(themeId, progress);
      })
    );
    
    await Promise.all(loadPromises);
  }
  
  private loadStylesheet(url: string): Promise<void> {
    return new Promise((resolve, reject) => {
      // Check if stylesheet is already loaded
      const existingLink = document.querySelector(`link[href="${url}"]`);
      if (existingLink) {
        resolve();
        return;
      }
      
      const link = document.createElement('link');
      link.rel = 'stylesheet';
      link.href = url;
      link.type = 'text/css';
      
      link.onload = () => resolve();
      link.onerror = () => reject(new Error(`Failed to load stylesheet: ${url}`));
      
      document.head.appendChild(link);
    });
  }
  
  private cacheTheme(themeId: string, theme: ThemeConfiguration): void {
    // Implement LRU cache eviction if needed
    if (this.themeCache.size >= this.MAX_CACHE_SIZE) {
      this.evictOldestTheme();
    }
    
    this.themeCache.set(themeId, theme);
    this.cacheTimestamps.set(themeId, Date.now());
  }
  
  private isThemeCacheValid(themeId: string): boolean {
    if (!this.themeCache.has(themeId)) {
      return false;
    }
    
    const timestamp = this.cacheTimestamps.get(themeId);
    if (!timestamp) {
      return false;
    }
    
    const age = Date.now() - timestamp;
    return age < this.CACHE_TTL;
  }
  
  private evictOldestTheme(): void {
    let oldestThemeId = '';
    let oldestTimestamp = Date.now();
    
    for (const [themeId, timestamp] of this.cacheTimestamps.entries()) {
      if (timestamp < oldestTimestamp) {
        oldestTimestamp = timestamp;
        oldestThemeId = themeId;
      }
    }
    
    if (oldestThemeId) {
      this.themeCache.delete(oldestThemeId);
      this.cacheTimestamps.delete(oldestThemeId);
      this.eventBus.emit('theme-cache-evicted', { themeId: oldestThemeId });
    }
  }
  
  private setLoadingState(themeId: string, state: LoadingState): void {
    this.loadingStates.set(themeId, state);
    this.eventBus.emit('theme-loading-state-changed', { themeId, state });
  }
  
  private updateLoadingProgress(themeId: string, progress: number): void {
    const currentState = this.loadingStates.get(themeId);
    if (currentState) {
      this.setLoadingState(themeId, { ...currentState, progress });
    }
  }
}
```

## 🚀 **Phase 3: Event Bus System**

### **Step 6: Create Event Bus Service**

```typescript
// src/app/shared/event-bus.service.ts
import { Injectable } from '@angular/core';
import { Subject, Observable, filter, map } from 'rxjs';

interface EventData {
  type: string;
  data: any;
  timestamp: number;
  source?: string;
}

@Injectable({
  providedIn: 'root'
})
export class EventBusService {
  private eventSubject = new Subject<EventData>();
  private eventHistory: EventData[] = [];
  private readonly MAX_HISTORY_SIZE = 1000;
  
  emit(eventType: string, data: any, source?: string): void {
    const eventData: EventData = {
      type: eventType,
      data,
      timestamp: Date.now(),
      source
    };
    
    // Add to history
    this.eventHistory.push(eventData);
    if (this.eventHistory.length > this.MAX_HISTORY_SIZE) {
      this.eventHistory.shift();
    }
    
    // Emit event
    this.eventSubject.next(eventData);
    
    // Log for debugging
    console.debug(`[EventBus] ${eventType}:`, data);
  }
  
  on(eventType: string): Observable<EventData> {
    return this.eventSubject.asObservable().pipe(
      filter(event => event.type === eventType)
    );
  }
  
  onData<T>(eventType: string): Observable<T> {
    return this.on(eventType).pipe(
      map(event => event.data as T)
    );
  }
  
  getHistory(eventType?: string): EventData[] {
    if (eventType) {
      return this.eventHistory.filter(event => event.type === eventType);
    }
    return [...this.eventHistory];
  }
  
  clearHistory(): void {
    this.eventHistory = [];
  }
  
  getEventTypes(): string[] {
    const types = new Set(this.eventHistory.map(event => event.type));
    return Array.from(types);
  }
}
```

This implementation guide provides the foundation for building a production-ready micro-frontend framework with unified theming. The next phases would cover:

- **Phase 4**: MFE Integration Implementation
- **Phase 5**: Performance Monitoring & Analytics
- **Phase 6**: Testing Strategy Implementation
- **Phase 7**: Deployment & DevOps Setup

Would you like me to continue with the remaining phases or move on to expanding other enterprise patterns?
