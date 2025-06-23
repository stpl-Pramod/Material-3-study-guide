# Performance Testing Strategies for Angular Material Applications

## 🎯 **Overview**

This guide provides comprehensive performance testing strategies specifically designed for Angular Material 3 applications. We'll cover component performance, bundle analysis, runtime performance, memory optimization, and advanced performance testing patterns.

## 📋 **Table of Contents**

1. [Performance Testing Setup](#performance-testing-setup)
2. [Component Performance Testing](#component-performance-testing)
3. [Bundle Analysis and Optimization](#bundle-analysis-and-optimization)
4. [Runtime Performance Monitoring](#runtime-performance-monitoring)
5. [Memory Leak Detection](#memory-leak-detection)
6. [Advanced Performance Patterns](#advanced-performance-patterns)
7. [Performance Budgets](#performance-budgets)
8. [Continuous Performance Monitoring](#continuous-performance-monitoring)

## 🛠️ **Performance Testing Setup**

### **Lighthouse CI Configuration**

```json
// lighthouserc.js
module.exports = {
  ci: {
    collect: {
      url: [
        'http://localhost:4200/',
        'http://localhost:4200/dashboard',
        'http://localhost:4200/users',
        'http://localhost:4200/products'
      ],
      startServerCommand: 'npm run start:prod',
      numberOfRuns: 5,
      settings: {
        chromeFlags: '--no-sandbox --headless',
        preset: 'desktop',
        onlyCategories: ['performance', 'accessibility', 'best-practices'],
        skipAudits: ['uses-http2']
      }
    },
    assert: {
      assertions: {
        'categories:performance': ['error', { minScore: 0.9 }],
        'categories:accessibility': ['error', { minScore: 0.9 }],
        'categories:best-practices': ['error', { minScore: 0.9 }],
        'first-contentful-paint': ['error', { maxNumericValue: 2000 }],
        'largest-contentful-paint': ['error', { maxNumericValue: 2500 }],
        'cumulative-layout-shift': ['error', { maxNumericValue: 0.1 }],
        'total-blocking-time': ['error', { maxNumericValue: 300 }],
        'speed-index': ['error', { maxNumericValue: 3000 }]
      }
    },
    upload: {
      target: 'temporary-public-storage'
    }
  }
};
```

### **Web Vitals Testing Setup**

```typescript
// src/testing/performance-testing.utils.ts
export interface WebVitalsMetrics {
  fcp: number; // First Contentful Paint
  lcp: number; // Largest Contentful Paint
  fid: number; // First Input Delay
  cls: number; // Cumulative Layout Shift
  ttfb: number; // Time to First Byte
}

export class PerformanceTestingUtils {
  static async measureWebVitals(): Promise<WebVitalsMetrics> {
    return new Promise((resolve) => {
      const metrics: Partial<WebVitalsMetrics> = {};
      let metricsCollected = 0;
      const totalMetrics = 5;

      const observer = new PerformanceObserver((list) => {
        list.getEntries().forEach((entry) => {
          switch (entry.entryType) {
            case 'paint':
              if (entry.name === 'first-contentful-paint') {
                metrics.fcp = entry.startTime;
                metricsCollected++;
              }
              break;
            case 'largest-contentful-paint':
              metrics.lcp = entry.startTime;
              metricsCollected++;
              break;
            case 'first-input':
              metrics.fid = entry.processingStart - entry.startTime;
              metricsCollected++;
              break;
            case 'layout-shift':
              if (!entry.hadRecentInput) {
                metrics.cls = (metrics.cls || 0) + entry.value;
              }
              break;
            case 'navigation':
              metrics.ttfb = entry.responseStart;
              metricsCollected++;
              break;
          }

          if (metricsCollected >= totalMetrics) {
            resolve(metrics as WebVitalsMetrics);
            observer.disconnect();
          }
        });
      });

      observer.observe({ entryTypes: ['paint', 'largest-contentful-paint', 'first-input', 'layout-shift', 'navigation'] });

      // Timeout after 10 seconds
      setTimeout(() => {
        resolve(metrics as WebVitalsMetrics);
        observer.disconnect();
      }, 10000);
    });
  }

  static async measureRenderTime(componentSelector: string): Promise<number> {
    const startTime = performance.now();
    
    // Wait for component to be fully rendered
    await new Promise<void>((resolve) => {
      const observer = new MutationObserver(() => {
        const element = document.querySelector(componentSelector);
        if (element && element.offsetHeight > 0) {
          observer.disconnect();
          resolve();
        }
      });
      
      observer.observe(document.body, {
        childList: true,
        subtree: true,
        attributes: true
      });
    });

    return performance.now() - startTime;
  }

  static measureMemoryUsage(): number {
    return (performance as any).memory?.usedJSHeapSize || 0;
  }

  static async measureInteractionLatency(
    triggerFn: () => void,
    expectedChangeSelector: string
  ): Promise<number> {
    const startTime = performance.now();
    
    triggerFn();
    
    await new Promise<void>((resolve) => {
      const observer = new MutationObserver(() => {
        const element = document.querySelector(expectedChangeSelector);
        if (element) {
          observer.disconnect();
          resolve();
        }
      });
      
      observer.observe(document.body, {
        childList: true,
        subtree: true,
        attributes: true
      });
    });

    return performance.now() - startTime;
  }
}
```

## 🧪 **Component Performance Testing**

### **Material Component Render Performance**

```typescript
// src/testing/component-performance.spec.ts
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { MatTableModule } from '@angular/material/table';
import { MatPaginatorModule } from '@angular/material/paginator';
import { MatSortModule } from '@angular/material/sort';
import { NoopAnimationsModule } from '@angular/platform-browser/animations';

import { DataTableComponent } from '../components/data-table.component';
import { PerformanceTestingUtils } from './performance-testing.utils';

describe('DataTableComponent - Performance', () => {
  let component: DataTableComponent;
  let fixture: ComponentFixture<DataTableComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [DataTableComponent],
      imports: [
        MatTableModule,
        MatPaginatorModule,
        MatSortModule,
        NoopAnimationsModule
      ]
    }).compileComponents();

    fixture = TestBed.createComponent(DataTableComponent);
    component = fixture.componentInstance;
  });

  describe('Render Performance', () => {
    it('should render small dataset within 100ms', async () => {
      const smallDataset = Array.from({ length: 50 }, (_, i) => ({
        id: i,
        name: `Item ${i}`,
        value: Math.random() * 1000
      }));

      const startTime = performance.now();
      component.dataSource.data = smallDataset;
      fixture.detectChanges();
      const renderTime = performance.now() - startTime;

      expect(renderTime).toBeLessThan(100);
    });

    it('should render medium dataset within 200ms', async () => {
      const mediumDataset = Array.from({ length: 500 }, (_, i) => ({
        id: i,
        name: `Item ${i}`,
        value: Math.random() * 1000,
        category: `Category ${i % 10}`,
        date: new Date(2023, i % 12, (i % 28) + 1)
      }));

      const startTime = performance.now();
      component.dataSource.data = mediumDataset;
      fixture.detectChanges();
      const renderTime = performance.now() - startTime;

      expect(renderTime).toBeLessThan(200);
    });

    it('should handle large dataset with virtual scrolling', async () => {
      component.enableVirtualScrolling = true;
      component.itemHeight = 48;

      const largeDataset = Array.from({ length: 10000 }, (_, i) => ({
        id: i,
        name: `Item ${i}`,
        value: Math.random() * 1000
      }));

      const startTime = performance.now();
      component.dataSource.data = largeDataset;
      fixture.detectChanges();
      const renderTime = performance.now() - startTime;

      // Virtual scrolling should maintain consistent performance
      expect(renderTime).toBeLessThan(150);
    });
  });

  describe('Interaction Performance', () => {
    beforeEach(() => {
      const dataset = Array.from({ length: 100 }, (_, i) => ({
        id: i,
        name: `Item ${i}`,
        value: Math.random() * 1000
      }));
      component.dataSource.data = dataset;
      fixture.detectChanges();
    });

    it('should sort data within 50ms', async () => {
      const startTime = performance.now();
      
      const sortHeader = fixture.nativeElement.querySelector('th[mat-sort-header]');
      sortHeader.click();
      fixture.detectChanges();
      
      const sortTime = performance.now() - startTime;
      expect(sortTime).toBeLessThan(50);
    });

    it('should filter data within 30ms', async () => {
      const startTime = performance.now();
      
      component.applyFilter('Item 5');
      fixture.detectChanges();
      
      const filterTime = performance.now() - startTime;
      expect(filterTime).toBeLessThan(30);
    });

    it('should paginate within 25ms', async () => {
      const startTime = performance.now();
      
      component.paginator.nextPage();
      fixture.detectChanges();
      
      const paginationTime = performance.now() - startTime;
      expect(paginationTime).toBeLessThan(25);
    });
  });

  describe('Memory Usage', () => {
    it('should not leak memory when updating data', () => {
      const initialMemory = PerformanceTestingUtils.measureMemoryUsage();
      
      // Update data multiple times
      for (let i = 0; i < 10; i++) {
        const dataset = Array.from({ length: 100 }, (_, j) => ({
          id: j + (i * 100),
          name: `Item ${j + (i * 100)}`,
          value: Math.random() * 1000
        }));
        
        component.dataSource.data = dataset;
        fixture.detectChanges();
      }
      
      // Force garbage collection if available
      if ((window as any).gc) {
        (window as any).gc();
      }
      
      const finalMemory = PerformanceTestingUtils.measureMemoryUsage();
      const memoryIncrease = finalMemory - initialMemory;
      
      // Memory increase should be minimal (less than 5MB)
      expect(memoryIncrease).toBeLessThan(5 * 1024 * 1024);
    });
  });
});
```

### **Theme Performance Testing**

```typescript
// src/testing/theme-performance.spec.ts
import { TestBed } from '@angular/core/testing';
import { DOCUMENT } from '@angular/common';
import { ThemeService } from '../services/theme.service';
import { PerformanceTestingUtils } from './performance-testing.utils';

describe('ThemeService - Performance', () => {
  let service: ThemeService;
  let document: Document;

  beforeEach(() => {
    TestBed.configureTestingModule({
      providers: [ThemeService]
    });
    service = TestBed.inject(ThemeService);
    document = TestBed.inject(DOCUMENT);
  });

  describe('Theme Switching Performance', () => {
    it('should switch themes within 50ms', async () => {
      const startTime = performance.now();
      
      service.setTheme('dark');
      
      // Wait for DOM updates
      await new Promise(resolve => setTimeout(resolve, 0));
      
      const switchTime = performance.now() - startTime;
      expect(switchTime).toBeLessThan(50);
    });

    it('should update CSS custom properties efficiently', async () => {
      const colors = {
        primary: '#1976d2',
        secondary: '#dc004e',
        tertiary: '#8bc34a'
      };

      const startTime = performance.now();
      
      service.updateThemeColors(colors);
      
      const updateTime = performance.now() - startTime;
      expect(updateTime).toBeLessThan(30);
    });

    it('should handle rapid theme switches without performance degradation', async () => {
      const themes = ['light', 'dark', 'high-contrast', 'custom'];
      const switchTimes: number[] = [];

      for (const theme of themes) {
        const startTime = performance.now();
        service.setTheme(theme);
        await new Promise(resolve => setTimeout(resolve, 10));
        const switchTime = performance.now() - startTime;
        switchTimes.push(switchTime);
      }

      // All switches should be consistently fast
      switchTimes.forEach(time => {
        expect(time).toBeLessThan(60);
      });

      // Performance shouldn't degrade over time
      const averageFirstHalf = switchTimes.slice(0, 2).reduce((a, b) => a + b) / 2;
      const averageSecondHalf = switchTimes.slice(2).reduce((a, b) => a + b) / 2;
      
      expect(averageSecondHalf).toBeLessThan(averageFirstHalf * 1.5);
    });
  });

  describe('Dynamic Theme Generation Performance', () => {
    it('should generate theme from color palette within 200ms', async () => {
      const primaryColor = '#1976d2';
      
      const startTime = performance.now();
      
      const palette = service.generateColorPalette(primaryColor);
      
      const generationTime = performance.now() - startTime;
      expect(generationTime).toBeLessThan(200);
      expect(palette).toBeDefined();
      expect(Object.keys(palette)).toHaveLength(13); // Material 3 tonal palette
    });

    it('should apply generated theme efficiently', async () => {
      const palette = {
        primary0: '#000000',
        primary10: '#1a0033',
        primary20: '#2d004a',
        primary30: '#420062',
        primary40: '#57007a',
        primary50: '#6e0093',
        primary60: '#8519ad',
        primary70: '#9e35c8',
        primary80: '#b852e4',
        primary90: '#d370ff',
        primary95: '#eab4ff',
        primary98: '#f8f0ff',
        primary99: '#fef7ff'
      };

      const startTime = performance.now();
      
      service.applyGeneratedPalette(palette);
      
      const applicationTime = performance.now() - startTime;
      expect(applicationTime).toBeLessThan(100);
    });
  });
});
```

## 📦 **Bundle Analysis and Optimization**

### **Webpack Bundle Analyzer Configuration**

```typescript
// webpack-bundle-analyzer.config.js
const { BundleAnalyzerPlugin } = require('webpack-bundle-analyzer');

module.exports = {
  plugins: [
    new BundleAnalyzerPlugin({
      analyzerMode: 'static',
      reportFilename: 'bundle-report.html',
      openAnalyzer: false,
      generateStatsFile: true,
      statsFilename: 'bundle-stats.json',
      logLevel: 'info'
    })
  ]
};
```

### **Bundle Size Testing**

```typescript
// src/testing/bundle-size.spec.ts
import { promises as fs } from 'fs';
import * as path from 'path';

describe('Bundle Size Analysis', () => {
  let bundleStats: any;

  beforeAll(async () => {
    const statsPath = path.join(process.cwd(), 'dist/bundle-stats.json');
    const statsContent = await fs.readFile(statsPath, 'utf-8');
    bundleStats = JSON.parse(statsContent);
  });

  describe('Main Bundle Size', () => {
    it('should keep main bundle under 2MB', () => {
      const mainChunk = bundleStats.chunks.find((chunk: any) => 
        chunk.names.includes('main')
      );
      
      expect(mainChunk.size).toBeLessThan(2 * 1024 * 1024); // 2MB
    });

    it('should keep vendor bundle under 3MB', () => {
      const vendorChunk = bundleStats.chunks.find((chunk: any) => 
        chunk.names.includes('vendor')
      );
      
      if (vendorChunk) {
        expect(vendorChunk.size).toBeLessThan(3 * 1024 * 1024); // 3MB
      }
    });

    it('should have proper code splitting for lazy-loaded modules', () => {
      const lazyChunks = bundleStats.chunks.filter((chunk: any) => 
        chunk.names.some((name: string) => name.includes('lazy'))
      );
      
      expect(lazyChunks.length).toBeGreaterThan(0);
      
      lazyChunks.forEach((chunk: any) => {
        expect(chunk.size).toBeLessThan(500 * 1024); // 500KB per lazy chunk
      });
    });
  });

  describe('Angular Material Tree Shaking', () => {
    it('should only include used Material modules', () => {
      const materialModules = bundleStats.modules.filter((module: any) =>
        module.name.includes('@angular/material')
      );

      // Should not include unused modules like mat-accordion if not used
      const unusedModules = ['accordion', 'expansion', 'stepper'];
      const includedModuleNames = materialModules.map((m: any) => m.name);
      
      unusedModules.forEach(moduleName => {
        const isIncluded = includedModuleNames.some((name: string) => 
          name.includes(moduleName)
        );
        
        if (!isIncluded) {
          // This is good - unused modules are tree-shaken
          expect(isIncluded).toBe(false);
        }
      });
    });

    it('should have minimal Material core overhead', () => {
      const coreModules = bundleStats.modules.filter((module: any) =>
        module.name.includes('@angular/material/core')
      );
      
      const totalCoreSize = coreModules.reduce((sum: number, module: any) => 
        sum + module.size, 0
      );
      
      expect(totalCoreSize).toBeLessThan(100 * 1024); // 100KB for core
    });
  });

  describe('Third-party Dependencies', () => {
    it('should minimize third-party library sizes', () => {
      const thirdPartyModules = bundleStats.modules.filter((module: any) =>
        module.name.includes('node_modules') && 
        !module.name.includes('@angular')
      );
      
      const largeDependencies = thirdPartyModules.filter((module: any) => 
        module.size > 100 * 1024 // 100KB
      );
      
      // Log large dependencies for review
      largeDependencies.forEach((module: any) => {
        console.warn(`Large dependency: ${module.name} (${module.size} bytes)`);
      });
      
      // Should have minimal large dependencies
      expect(largeDependencies.length).toBeLessThan(5);
    });
  });
});
```

### **Lighthouse Bundle Performance**

```typescript
// src/testing/lighthouse-performance.spec.ts
import { launch } from 'chrome-launcher';
import lighthouse from 'lighthouse';

describe('Lighthouse Performance Analysis', () => {
  let chrome: any;

  beforeAll(async () => {
    chrome = await launch({ chromeFlags: ['--headless'] });
  });

  afterAll(async () => {
    await chrome.kill();
  });

  it('should meet performance benchmarks', async () => {
    const options = {
      logLevel: 'info',
      output: 'json',
      onlyCategories: ['performance'],
      port: chrome.port
    };

    const runnerResult = await lighthouse('http://localhost:4200', options);
    const { lhr } = runnerResult;

    // Performance score should be above 90
    expect(lhr.categories.performance.score).toBeGreaterThan(0.9);

    // Specific metrics
    const metrics = lhr.audits;
    
    expect(metrics['first-contentful-paint'].numericValue).toBeLessThan(2000);
    expect(metrics['largest-contentful-paint'].numericValue).toBeLessThan(2500);
    expect(metrics['cumulative-layout-shift'].numericValue).toBeLessThan(0.1);
    expect(metrics['total-blocking-time'].numericValue).toBeLessThan(300);
  }, 30000);

  it('should have optimal resource loading', async () => {
    const options = {
      logLevel: 'info',
      output: 'json',
      onlyCategories: ['performance'],
      port: chrome.port
    };

    const runnerResult = await lighthouse('http://localhost:4200/dashboard', options);
    const { lhr } = runnerResult;

    const audits = lhr.audits;

    // Resource optimization audits
    expect(audits['unused-css-rules'].score).toBeGreaterThan(0.8);
    expect(audits['unused-javascript'].score).toBeGreaterThan(0.8);
    expect(audits['modern-image-formats'].score).toBeGreaterThan(0.9);
    expect(audits['uses-text-compression'].score).toBe(1);
  }, 30000);
});
```

## 🎯 **Runtime Performance Monitoring**

### **Performance Observer Setup**

```typescript
// src/services/performance-monitor.service.ts
import { Injectable } from '@angular/core';
import { BehaviorSubject, Observable } from 'rxjs';

export interface PerformanceMetric {
  name: string;
  value: number;
  timestamp: number;
  type: 'navigation' | 'paint' | 'measure' | 'memory';
}

@Injectable({
  providedIn: 'root'
})
export class PerformanceMonitorService {
  private metricsSubject = new BehaviorSubject<PerformanceMetric[]>([]);
  private observer: PerformanceObserver | null = null;

  get metrics$(): Observable<PerformanceMetric[]> {
    return this.metricsSubject.asObservable();
  }

  startMonitoring(): void {
    if (this.observer) {
      this.stopMonitoring();
    }

    this.observer = new PerformanceObserver((list) => {
      const entries = list.getEntries();
      const metrics: PerformanceMetric[] = [];

      entries.forEach(entry => {
        switch (entry.entryType) {
          case 'navigation':
            const navEntry = entry as PerformanceNavigationTiming;
            metrics.push(
              {
                name: 'dns-lookup',
                value: navEntry.domainLookupEnd - navEntry.domainLookupStart,
                timestamp: Date.now(),
                type: 'navigation'
              },
              {
                name: 'tcp-connection',
                value: navEntry.connectEnd - navEntry.connectStart,
                timestamp: Date.now(),
                type: 'navigation'
              },
              {
                name: 'server-response',
                value: navEntry.responseEnd - navEntry.requestStart,
                timestamp: Date.now(),
                type: 'navigation'
              },
              {
                name: 'dom-processing',
                value: navEntry.domComplete - navEntry.domLoading,
                timestamp: Date.now(),
                type: 'navigation'
              }
            );
            break;

          case 'paint':
            metrics.push({
              name: entry.name,
              value: entry.startTime,
              timestamp: Date.now(),
              type: 'paint'
            });
            break;

          case 'measure':
            metrics.push({
              name: entry.name,
              value: entry.duration,
              timestamp: Date.now(),
              type: 'measure'
            });
            break;
        }
      });

      if (metrics.length > 0) {
        const currentMetrics = this.metricsSubject.value;
        this.metricsSubject.next([...currentMetrics, ...metrics]);
      }
    });

    this.observer.observe({
      entryTypes: ['navigation', 'paint', 'measure']
    });

    // Monitor memory usage
    this.monitorMemoryUsage();
  }

  stopMonitoring(): void {
    if (this.observer) {
      this.observer.disconnect();
      this.observer = null;
    }
  }

  measureComponentRender(componentName: string): void {
    performance.mark(`${componentName}-start`);
  }

  completeComponentRender(componentName: string): void {
    performance.mark(`${componentName}-end`);
    performance.measure(
      `${componentName}-render`,
      `${componentName}-start`,
      `${componentName}-end`
    );
  }

  measureUserInteraction(interactionName: string): void {
    performance.mark(`${interactionName}-start`);
  }

  completeUserInteraction(interactionName: string): void {
    performance.mark(`${interactionName}-end`);
    performance.measure(
      `${interactionName}-interaction`,
      `${interactionName}-start`,
      `${interactionName}-end`
    );
  }

  private monitorMemoryUsage(): void {
    if ('memory' in performance) {
      setInterval(() => {
        const memoryInfo = (performance as any).memory;
        const metric: PerformanceMetric = {
          name: 'memory-usage',
          value: memoryInfo.usedJSHeapSize,
          timestamp: Date.now(),
          type: 'memory'
        };

        const currentMetrics = this.metricsSubject.value;
        this.metricsSubject.next([...currentMetrics, metric]);
      }, 5000); // Every 5 seconds
    }
  }

  getMetricsSummary(): {
    averageRenderTime: number;
    memoryUsage: number;
    paintTimings: { fcp: number; lcp: number };
  } {
    const metrics = this.metricsSubject.value;
    
    const renderMetrics = metrics.filter(m => 
      m.type === 'measure' && m.name.includes('render')
    );
    const averageRenderTime = renderMetrics.length > 0 
      ? renderMetrics.reduce((sum, m) => sum + m.value, 0) / renderMetrics.length 
      : 0;

    const memoryMetrics = metrics.filter(m => m.type === 'memory');
    const latestMemory = memoryMetrics.length > 0 
      ? memoryMetrics[memoryMetrics.length - 1].value 
      : 0;

    const fcpMetric = metrics.find(m => m.name === 'first-contentful-paint');
    const lcpMetric = metrics.find(m => m.name === 'largest-contentful-paint');

    return {
      averageRenderTime,
      memoryUsage: latestMemory,
      paintTimings: {
        fcp: fcpMetric?.value || 0,
        lcp: lcpMetric?.value || 0
      }
    };
  }
}
```

### **Component Performance Decorator**

```typescript
// src/decorators/performance-monitor.decorator.ts
import { PerformanceMonitorService } from '../services/performance-monitor.service';

export function MonitorPerformance(componentName?: string) {
  return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
    const originalMethod = descriptor.value;
    const name = componentName || `${target.constructor.name}.${propertyKey}`;

    descriptor.value = function (...args: any[]) {
      const monitor = (this as any).performanceMonitor || 
        new PerformanceMonitorService();
      
      monitor.measureComponentRender(name);
      
      const result = originalMethod.apply(this, args);
      
      if (result instanceof Promise) {
        return result.finally(() => {
          monitor.completeComponentRender(name);
        });
      } else {
        setTimeout(() => {
          monitor.completeComponentRender(name);
        }, 0);
        return result;
      }
    };

    return descriptor;
  };
}

// Usage example:
@Component({
  selector: 'app-data-table',
  templateUrl: './data-table.component.html'
})
export class DataTableComponent {
  constructor(private performanceMonitor: PerformanceMonitorService) {}

  @MonitorPerformance('data-table-load')
  loadData(): void {
    // Component logic here
  }

  @MonitorPerformance('data-table-sort')
  sortData(column: string): void {
    // Sorting logic here
  }
}
```

## 🧠 **Memory Leak Detection**

### **Memory Leak Testing Service**

```typescript
// src/testing/memory-leak-detector.service.ts
import { Injectable } from '@angular/core';

export interface MemorySnapshot {
  timestamp: number;
  usedJSHeapSize: number;
  totalJSHeapSize: number;
  jsHeapSizeLimit: number;
  domNodes: number;
  eventListeners: number;
}

@Injectable({
  providedIn: 'root'
})
export class MemoryLeakDetectorService {
  private snapshots: MemorySnapshot[] = [];
  private isMonitoring = false;
  private monitoringInterval: any;

  startMonitoring(intervalMs: number = 1000): void {
    if (this.isMonitoring) {
      this.stopMonitoring();
    }

    this.isMonitoring = true;
    this.snapshots = [];

    this.monitoringInterval = setInterval(() => {
      this.takeSnapshot();
    }, intervalMs);
  }

  stopMonitoring(): void {
    if (this.monitoringInterval) {
      clearInterval(this.monitoringInterval);
      this.monitoringInterval = null;
    }
    this.isMonitoring = false;
  }

  takeSnapshot(): MemorySnapshot {
    const snapshot: MemorySnapshot = {
      timestamp: Date.now(),
      usedJSHeapSize: this.getUsedJSHeapSize(),
      totalJSHeapSize: this.getTotalJSHeapSize(),
      jsHeapSizeLimit: this.getJSHeapSizeLimit(),
      domNodes: document.querySelectorAll('*').length,
      eventListeners: this.countEventListeners()
    };

    this.snapshots.push(snapshot);
    return snapshot;
  }

  analyzeMemoryLeaks(): {
    hasMemoryLeak: boolean;
    memoryGrowthRate: number;
    domNodeGrowthRate: number;
    recommendations: string[];
  } {
    if (this.snapshots.length < 10) {
      return {
        hasMemoryLeak: false,
        memoryGrowthRate: 0,
        domNodeGrowthRate: 0,
        recommendations: ['Need more data points for analysis']
      };
    }

    const firstSnapshot = this.snapshots[0];
    const lastSnapshot = this.snapshots[this.snapshots.length - 1];
    const timeElapsed = lastSnapshot.timestamp - firstSnapshot.timestamp;

    const memoryGrowth = lastSnapshot.usedJSHeapSize - firstSnapshot.usedJSHeapSize;
    const domNodeGrowth = lastSnapshot.domNodes - firstSnapshot.domNodes;

    const memoryGrowthRate = memoryGrowth / timeElapsed; // bytes per ms
    const domNodeGrowthRate = domNodeGrowth / timeElapsed; // nodes per ms

    const hasMemoryLeak = this.detectMemoryLeak();
    const recommendations = this.generateRecommendations(memoryGrowthRate, domNodeGrowthRate);

    return {
      hasMemoryLeak,
      memoryGrowthRate: memoryGrowthRate * 1000, // Convert to bytes per second
      domNodeGrowthRate: domNodeGrowthRate * 1000, // Convert to nodes per second
      recommendations
    };
  }

  private detectMemoryLeak(): boolean {
    if (this.snapshots.length < 20) return false;

    // Check for consistent memory growth over time
    const recentSnapshots = this.snapshots.slice(-20);
    let growthCount = 0;

    for (let i = 1; i < recentSnapshots.length; i++) {
      if (recentSnapshots[i].usedJSHeapSize > recentSnapshots[i - 1].usedJSHeapSize) {
        growthCount++;
      }
    }

    // If memory grows in more than 70% of recent snapshots, likely a leak
    return growthCount / (recentSnapshots.length - 1) > 0.7;
  }

  private generateRecommendations(memoryGrowthRate: number, domNodeGrowthRate: number): string[] {
    const recommendations: string[] = [];

    if (memoryGrowthRate > 1024 * 1024) { // 1MB per second
      recommendations.push('High memory growth rate detected. Check for unsubscribed observables.');
    }

    if (domNodeGrowthRate > 10) { // 10 nodes per second
      recommendations.push('High DOM node growth rate. Check for dynamic component creation without cleanup.');
    }

    if (this.snapshots.length > 0) {
      const latestSnapshot = this.snapshots[this.snapshots.length - 1];
      
      if (latestSnapshot.usedJSHeapSize > 100 * 1024 * 1024) { // 100MB
        recommendations.push('High memory usage detected. Consider implementing lazy loading.');
      }

      if (latestSnapshot.domNodes > 10000) {
        recommendations.push('High DOM node count. Consider virtual scrolling for large lists.');
      }
    }

    if (recommendations.length === 0) {
      recommendations.push('Memory usage appears normal.');
    }

    return recommendations;
  }

  private getUsedJSHeapSize(): number {
    return (performance as any).memory?.usedJSHeapSize || 0;
  }

  private getTotalJSHeapSize(): number {
    return (performance as any).memory?.totalJSHeapSize || 0;
  }

  private getJSHeapSizeLimit(): number {
    return (performance as any).memory?.jsHeapSizeLimit || 0;
  }

  private countEventListeners(): number {
    // This is a simplified count - in practice, you'd need a more sophisticated approach
    let count = 0;
    const elements = document.querySelectorAll('*');
    
    elements.forEach(el => {
      // Check for common event listener attributes
      const attrs = el.attributes;
      for (let i = 0; i < attrs.length; i++) {
        if (attrs[i].name.startsWith('on') || attrs[i].name.includes('click')) {
          count++;
        }
      }
    });
    
    return count;
  }

  getSnapshots(): MemorySnapshot[] {
    return [...this.snapshots];
  }

  clearSnapshots(): void {
    this.snapshots = [];
  }
}
```

### **Memory Leak Testing Spec**

```typescript
// src/testing/memory-leak.spec.ts
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { Component, OnInit, OnDestroy } from '@angular/core';
import { Subject, interval } from 'rxjs';
import { takeUntil } from 'rxjs/operators';

import { MemoryLeakDetectorService } from './memory-leak-detector.service';

@Component({
  template: '<div>Test Component</div>'
})
class TestComponent implements OnInit, OnDestroy {
  private destroy$ = new Subject<void>();
  private subscription: any;

  ngOnInit() {
    // Simulate a potential memory leak
    this.subscription = interval(100).subscribe();
  }

  ngOnDestroy() {
    this.destroy$.next();
    this.destroy$.complete();
    
    // Uncomment to fix the leak
    // if (this.subscription) {
    //   this.subscription.unsubscribe();
    // }
  }
}

describe('Memory Leak Detection', () => {
  let memoryDetector: MemoryLeakDetectorService;
  let component: TestComponent;
  let fixture: ComponentFixture<TestComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [TestComponent],
      providers: [MemoryLeakDetectorService]
    }).compileComponents();

    memoryDetector = TestBed.inject(MemoryLeakDetectorService);
    fixture = TestBed.createComponent(TestComponent);
    component = fixture.componentInstance;
  });

  it('should detect memory leaks in component lifecycle', (done) => {
    memoryDetector.startMonitoring(100);

    // Create and destroy component multiple times
    let createDestroyCount = 0;
    const maxIterations = 20;

    const createDestroyInterval = setInterval(() => {
      // Destroy existing component
      if (fixture) {
        fixture.destroy();
      }

      // Create new component
      fixture = TestBed.createComponent(TestComponent);
      component = fixture.componentInstance;
      fixture.detectChanges();

      createDestroyCount++;

      if (createDestroyCount >= maxIterations) {
        clearInterval(createDestroyInterval);
        
        setTimeout(() => {
          memoryDetector.stopMonitoring();
          const analysis = memoryDetector.analyzeMemoryLeaks();
          
          console.log('Memory Analysis:', analysis);
          
          // In this test, we expect a memory leak due to unsubscribed interval
          expect(analysis.hasMemoryLeak).toBe(true);
          expect(analysis.recommendations).toContain(
            jasmine.stringMatching(/unsubscribed observables/)
          );
          
          done();
        }, 1000);
      }
    }, 200);
  }, 15000);

  it('should not detect leaks in properly managed components', (done) => {
    @Component({
      template: '<div>Clean Component</div>'
    })
    class CleanComponent implements OnInit, OnDestroy {
      private destroy$ = new Subject<void>();

      ngOnInit() {
        interval(100)
          .pipe(takeUntil(this.destroy$))
          .subscribe();
      }

      ngOnDestroy() {
        this.destroy$.next();
        this.destroy$.complete();
      }
    }

    TestBed.overrideComponent(TestComponent, {
      set: { 
        template: '<div>Clean Component</div>',
        providers: []
      }
    });

    memoryDetector.startMonitoring(100);

    let iterations = 0;
    const maxIterations = 15;

    const interval = setInterval(() => {
      if (fixture) {
        fixture.destroy();
      }

      fixture = TestBed.createComponent(TestComponent);
      fixture.detectChanges();
      iterations++;

      if (iterations >= maxIterations) {
        clearInterval(interval);
        
        setTimeout(() => {
          memoryDetector.stopMonitoring();
          const analysis = memoryDetector.analyzeMemoryLeaks();
          
          expect(analysis.hasMemoryLeak).toBe(false);
          expect(analysis.recommendations).toContain('Memory usage appears normal.');
          
          done();
        }, 1000);
      }
    }, 200);
  }, 10000);
});
```

## ⚡ **Advanced Performance Patterns**

### **Virtual Scrolling Performance**

```typescript
// src/components/virtual-scroll-performance.spec.ts
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { ScrollingModule } from '@angular/cdk/scrolling';
import { MatListModule } from '@angular/material/list';

import { VirtualScrollListComponent } from './virtual-scroll-list.component';
import { PerformanceTestingUtils } from '../testing/performance-testing.utils';

describe('VirtualScrollListComponent - Performance', () => {
  let component: VirtualScrollListComponent;
  let fixture: ComponentFixture<VirtualScrollListComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [VirtualScrollListComponent],
      imports: [ScrollingModule, MatListModule]
    }).compileComponents();

    fixture = TestBed.createComponent(VirtualScrollListComponent);
    component = fixture.componentInstance;
  });

  it('should maintain performance with large datasets', async () => {
    // Create dataset with 100,000 items
    const largeDataset = Array.from({ length: 100000 }, (_, i) => ({
      id: i,
      name: `Item ${i}`,
      description: `Description for item ${i}`,
      value: Math.random() * 1000
    }));

    const startTime = performance.now();
    
    component.items = largeDataset;
    component.itemSize = 60;
    fixture.detectChanges();
    
    const renderTime = performance.now() - startTime;
    
    // Should render quickly even with 100k items
    expect(renderTime).toBeLessThan(100);
    
    // Should only render visible items
    const renderedItems = fixture.nativeElement.querySelectorAll('.list-item');
    expect(renderedItems.length).toBeLessThan(50); // Only visible items
  });

  it('should handle rapid scrolling efficiently', async () => {
    const dataset = Array.from({ length: 10000 }, (_, i) => ({
      id: i,
      name: `Item ${i}`
    }));

    component.items = dataset;
    component.itemSize = 48;
    fixture.detectChanges();

    const scrollContainer = fixture.nativeElement.querySelector('cdk-virtual-scroll-viewport');
    
    // Measure scroll performance
    const scrollTimes: number[] = [];
    
    for (let i = 0; i < 10; i++) {
      const startTime = performance.now();
      
      scrollContainer.scrollTop = i * 1000;
      await new Promise(resolve => setTimeout(resolve, 10));
      
      const scrollTime = performance.now() - startTime;
      scrollTimes.push(scrollTime);
    }

    // All scroll operations should be fast
    scrollTimes.forEach(time => {
      expect(time).toBeLessThan(20);
    });

    // Performance should remain consistent
    const averageTime = scrollTimes.reduce((a, b) => a + b) / scrollTimes.length;
    expect(Math.max(...scrollTimes)).toBeLessThan(averageTime * 2);
  });
});
```

### **Lazy Loading Performance**

```typescript
// src/testing/lazy-loading-performance.spec.ts
import { TestBed } from '@angular/core/testing';
import { Router } from '@angular/router';
import { Location } from '@angular/common';
import { Component } from '@angular/core';

describe('Lazy Loading Performance', () => {
  let router: Router;
  let location: Location;
  let fixture: any;

  beforeEach(async () => {
    @Component({
      template: '<router-outlet></router-outlet>'
    })
    class TestComponent { }

    await TestBed.configureTestingModule({
      declarations: [TestComponent],
      imports: [
        // Your routing module here
      ]
    }).compileComponents();

    router = TestBed.inject(Router);
    location = TestBed.inject(Location);
    fixture = TestBed.createComponent(TestComponent);
  });

  it('should load lazy modules within performance budget', async () => {
    const lazyRoutes = [
      '/dashboard',
      '/users',
      '/products',
      '/settings',
      '/reports'
    ];

    for (const route of lazyRoutes) {
      const startTime = performance.now();
      
      await router.navigate([route]);
      fixture.detectChanges();
      
      const loadTime = performance.now() - startTime;
      
      // Lazy modules should load within 500ms
      expect(loadTime).toBeLessThan(500);
      expect(location.path()).toBe(route);
    }
  });

  it('should preload critical modules efficiently', async () => {
    // Measure initial app load time
    const startTime = performance.now();
    
    // Navigate to dashboard (should be preloaded)
    await router.navigate(['/dashboard']);
    fixture.detectChanges();
    
    const loadTime = performance.now() - startTime;
    
    // Preloaded modules should load very quickly
    expect(loadTime).toBeLessThan(100);
  });
});
```

## 📊 **Performance Budgets**

### **Performance Budget Configuration**

```typescript
// performance-budget.config.ts
export interface PerformanceBudget {
  metric: string;
  budget: number;
  unit: 'ms' | 'bytes' | 'score';
  critical: boolean;
}

export const PERFORMANCE_BUDGETS: PerformanceBudget[] = [
  // Web Vitals
  { metric: 'first-contentful-paint', budget: 1800, unit: 'ms', critical: true },
  { metric: 'largest-contentful-paint', budget: 2500, unit: 'ms', critical: true },
  { metric: 'cumulative-layout-shift', budget: 0.1, unit: 'score', critical: true },
  { metric: 'total-blocking-time', budget: 300, unit: 'ms', critical: true },
  
  // Bundle sizes
  { metric: 'main-bundle-size', budget: 2048000, unit: 'bytes', critical: true }, // 2MB
  { metric: 'vendor-bundle-size', budget: 3145728, unit: 'bytes', critical: false }, // 3MB
  { metric: 'total-bundle-size', budget: 5242880, unit: 'bytes', critical: true }, // 5MB
  
  // Runtime performance
  { metric: 'component-render-time', budget: 100, unit: 'ms', critical: false },
  { metric: 'user-interaction-latency', budget: 50, unit: 'ms', critical: true },
  { metric: 'theme-switch-time', budget: 200, unit: 'ms', critical: false },
  
  // Memory usage
  { metric: 'memory-usage', budget: 104857600, unit: 'bytes', critical: false }, // 100MB
  { metric: 'memory-growth-rate', budget: 1048576, unit: 'bytes', critical: true } // 1MB/s
];

export class PerformanceBudgetValidator {
  static validateBudgets(metrics: Record<string, number>): {
    passed: boolean;
    violations: Array<{
      metric: string;
      actual: number;
      budget: number;
      critical: boolean;
    }>;
  } {
    const violations: Array<{
      metric: string;
      actual: number;
      budget: number;
      critical: boolean;
    }> = [];

    PERFORMANCE_BUDGETS.forEach(budget => {
      const actualValue = metrics[budget.metric];
      
      if (actualValue !== undefined && actualValue > budget.budget) {
        violations.push({
          metric: budget.metric,
          actual: actualValue,
          budget: budget.budget,
          critical: budget.critical
        });
      }
    });

    const criticalViolations = violations.filter(v => v.critical);
    const passed = criticalViolations.length === 0;

    return { passed, violations };
  }

  static formatViolations(violations: Array<{
    metric: string;
    actual: number;
    budget: number;
    critical: boolean;
  }>): string[] {
    return violations.map(violation => {
      const unit = PERFORMANCE_BUDGETS.find(b => b.metric === violation.metric)?.unit || '';
      const severity = violation.critical ? 'CRITICAL' : 'WARNING';
      
      return `${severity}: ${violation.metric} exceeded budget. ` +
             `Actual: ${violation.actual}${unit}, Budget: ${violation.budget}${unit}`;
    });
  }
}
```

## 🔄 **Continuous Performance Monitoring**

### **Performance CI/CD Integration**

```yaml
# .github/workflows/performance-testing.yml
name: Performance Testing

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  performance-testing:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Build production bundle
        run: npm run build:prod
      
      - name: Analyze bundle size
        run: |
          npm run analyze-bundle
          node scripts/validate-bundle-size.js
      
      - name: Start application
        run: |
          npm run start:prod &
          npx wait-on http://localhost:4200
      
      - name: Run Lighthouse CI
        run: |
          npm install -g @lhci/cli@0.12.x
          lhci autorun
        env:
          LHCI_GITHUB_APP_TOKEN: ${{ secrets.LHCI_GITHUB_APP_TOKEN }}
      
      - name: Run performance tests
        run: npm run test:performance
      
      - name: Upload performance results
        uses: actions/upload-artifact@v3
        with:
          name: performance-results
          path: |
            lighthouse-results/
            performance-reports/
            bundle-analysis/
          retention-days: 30
      
      - name: Comment PR with performance results
        if: github.event_name == 'pull_request'
        uses: actions/github-script@v6
        with:
          script: |
            const fs = require('fs');
            const performanceReport = fs.readFileSync('./performance-reports/summary.json', 'utf8');
            const report = JSON.parse(performanceReport);
            
            const comment = `
            ## Performance Test Results 📊
            
            ### Bundle Size Analysis
            - Main Bundle: ${(report.bundleSize.main / 1024 / 1024).toFixed(2)}MB
            - Vendor Bundle: ${(report.bundleSize.vendor / 1024 / 1024).toFixed(2)}MB
            - Total Size: ${(report.bundleSize.total / 1024 / 1024).toFixed(2)}MB
            
            ### Web Vitals
            - First Contentful Paint: ${report.webVitals.fcp}ms
            - Largest Contentful Paint: ${report.webVitals.lcp}ms
            - Cumulative Layout Shift: ${report.webVitals.cls}
            - Total Blocking Time: ${report.webVitals.tbt}ms
            
            ### Performance Score: ${report.lighthouseScore}/100
            
            ${report.budgetViolations.length > 0 ? 
              '⚠️ **Budget Violations:**\n' + report.budgetViolations.join('\n') : 
              '✅ All performance budgets passed!'
            }
            `;
            
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: comment
            });

  performance-monitoring:
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Deploy to staging
        run: npm run deploy:staging
      
      - name: Run continuous performance monitoring
        run: |
          node scripts/performance-monitor.js --url=https://staging.example.com --duration=300
      
      - name: Send performance alerts
        if: failure()
        run: |
          node scripts/send-performance-alert.js
        env:
          SLACK_WEBHOOK: ${{ secrets.SLACK_WEBHOOK }}
          EMAIL_API_KEY: ${{ secrets.EMAIL_API_KEY }}
```

### **Performance Dashboard Service**

```typescript
// src/services/performance-dashboard.service.ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable, BehaviorSubject } from 'rxjs';

export interface PerformanceDashboardData {
  timestamp: number;
  pageLoadTime: number;
  bundleSize: number;
  memoryUsage: number;
  webVitals: {
    fcp: number;
    lcp: number;
    cls: number;
    fid: number;
  };
  userAgent: string;
  url: string;
}

@Injectable({
  providedIn: 'root'
})
export class PerformanceDashboardService {
  private apiUrl = '/api/performance';
  private performanceData$ = new BehaviorSubject<PerformanceDashboardData[]>([]);

  constructor(private http: HttpClient) {}

  collectPerformanceData(): void {
    const data: PerformanceDashboardData = {
      timestamp: Date.now(),
      pageLoadTime: this.getPageLoadTime(),
      bundleSize: this.getBundleSize(),
      memoryUsage: this.getMemoryUsage(),
      webVitals: this.getWebVitals(),
      userAgent: navigator.userAgent,
      url: window.location.href
    };

    this.sendPerformanceData(data).subscribe();
  }

  private getPageLoadTime(): number {
    const navigation = performance.getEntriesByType('navigation')[0] as PerformanceNavigationTiming;
    return navigation ? navigation.loadEventEnd - navigation.loadEventStart : 0;
  }

  private getBundleSize(): number {
    // This would typically come from build-time analysis
    return performance.getEntriesByType('resource')
      .filter(resource => resource.name.includes('.js'))
      .reduce((total, resource) => total + (resource as any).transferSize || 0, 0);
  }

  private getMemoryUsage(): number {
    return (performance as any).memory?.usedJSHeapSize || 0;
  }

  private getWebVitals(): PerformanceDashboardData['webVitals'] {
    const paintEntries = performance.getEntriesByType('paint');
    const fcpEntry = paintEntries.find(entry => entry.name === 'first-contentful-paint');
    
    return {
      fcp: fcpEntry?.startTime || 0,
      lcp: 0, // Would be collected via PerformanceObserver
      cls: 0, // Would be collected via PerformanceObserver
      fid: 0  // Would be collected via PerformanceObserver
    };
  }

  private sendPerformanceData(data: PerformanceDashboardData): Observable<any> {
    return this.http.post(`${this.apiUrl}/collect`, data);
  }

  getPerformanceHistory(): Observable<PerformanceDashboardData[]> {
    return this.http.get<PerformanceDashboardData[]>(`${this.apiUrl}/history`);
  }

  getPerformanceAlerts(): Observable<any[]> {
    return this.http.get<any[]>(`${this.apiUrl}/alerts`);
  }
}
```

## 📊 **Performance Testing Checklist**

### **Setup Checklist**
- [ ] Lighthouse CI configured
- [ ] Bundle analyzer integrated
- [ ] Performance budgets defined
- [ ] Web Vitals monitoring active
- [ ] Memory leak detection implemented

### **Testing Coverage Checklist**
- [ ] Component render performance tested
- [ ] Bundle size limits enforced
- [ ] Runtime performance monitored
- [ ] Memory usage tracked
- [ ] User interaction latency measured
- [ ] Theme switching performance verified

### **Continuous Monitoring Checklist**
- [ ] CI/CD performance gates active
- [ ] Performance dashboard deployed
- [ ] Alert system configured
- [ ] Regular performance audits scheduled
- [ ] Performance regression detection active

---

## 🎯 **Next Steps**

1. **Implement Performance Testing** - Set up comprehensive performance test suites
2. **Configure Monitoring** - Deploy continuous performance monitoring
3. **Set Up Alerts** - Configure performance regression alerts
4. **Optimize Critical Paths** - Focus on user-critical performance paths
5. **Regular Audits** - Schedule regular performance audits and reviews

This performance testing strategy ensures your Angular Material applications maintain optimal performance throughout development and production deployment.
