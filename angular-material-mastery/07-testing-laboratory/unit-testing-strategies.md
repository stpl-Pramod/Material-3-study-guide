# Unit Testing Strategies for Angular Material Applications

## 🎯 **Overview**

This guide provides comprehensive unit testing strategies specifically tailored for Angular Material 3 applications. We'll cover component testing, theming validation, accessibility testing, and advanced testing patterns.

## 📋 **Table of Contents**

1. [Testing Environment Setup](#testing-environment-setup)
2. [Component Testing Patterns](#component-testing-patterns)
3. [Theme Testing Strategies](#theme-testing-strategies)
4. [Accessibility Testing](#accessibility-testing)
5. [Advanced Testing Patterns](#advanced-testing-patterns)
6. [Performance Testing](#performance-testing)
7. [Visual Testing](#visual-testing)
8. [Mock Strategies](#mock-strategies)

## 🛠️ **Testing Environment Setup**

### **Jest Configuration for Angular Material**

```typescript
// jest.config.js
module.exports = {
  preset: 'jest-preset-angular',
  setupFilesAfterEnv: ['<rootDir>/src/setup-jest.ts'],
  globalSetup: 'jest-preset-angular/global-setup',
  moduleNameMapping: {
    '@angular/material/(.*)': '<rootDir>/node_modules/@angular/material/$1',
    '@app/(.*)': '<rootDir>/src/app/$1',
    '@shared/(.*)': '<rootDir>/src/app/shared/$1',
    '@testing/(.*)': '<rootDir>/src/testing/$1'
  },
  collectCoverageFrom: [
    'src/app/**/*.ts',
    '!src/app/**/*.interface.ts',
    '!src/app/**/*.module.ts',
    '!src/app/**/*.spec.ts'
  ],
  coverageThreshold: {
    global: {
      branches: 80,
      functions: 80,
      lines: 80,
      statements: 80
    }
  },
  testEnvironment: 'jsdom',
  transformIgnorePatterns: [
    'node_modules/(?!(.*\\.mjs$|@angular|@ngrx))'
  ]
};
```

### **Testing Utilities Setup**

```typescript
// src/testing/material-testing.utils.ts
import { ComponentFixture } from '@angular/core/testing';
import { HarnessLoader } from '@angular/cdk/testing';
import { TestbedHarnessEnvironment } from '@angular/cdk/testing/testbed';
import { MatButtonHarness } from '@angular/material/button/testing';
import { MatInputHarness } from '@angular/material/input/testing';
import { MatSelectHarness } from '@angular/material/select/testing';
import { MatDialogHarness } from '@angular/material/dialog/testing';

export class MaterialTestingUtils {
  static async getHarness<T>(
    fixture: ComponentFixture<any>,
    harnessType: any,
    selector?: string
  ): Promise<T> {
    const loader = TestbedHarnessEnvironment.loader(fixture);
    return await loader.getHarness(harnessType.with(selector ? { selector } : {}));
  }

  static async getAllHarnesses<T>(
    fixture: ComponentFixture<any>,
    harnessType: any,
    selector?: string
  ): Promise<T[]> {
    const loader = TestbedHarnessEnvironment.loader(fixture);
    return await loader.getAllHarnesses(harnessType.with(selector ? { selector } : {}));
  }

  static async clickButton(
    fixture: ComponentFixture<any>,
    buttonText?: string,
    buttonSelector?: string
  ): Promise<void> {
    const button = await this.getHarness<MatButtonHarness>(
      fixture,
      MatButtonHarness,
      buttonSelector || `[data-test="${buttonText}"]`
    );
    await button.click();
  }

  static async fillInput(
    fixture: ComponentFixture<any>,
    inputSelector: string,
    value: string
  ): Promise<void> {
    const input = await this.getHarness<MatInputHarness>(
      fixture,
      MatInputHarness,
      inputSelector
    );
    await input.setValue(value);
  }

  static async selectOption(
    fixture: ComponentFixture<any>,
    selectSelector: string,
    optionText: string
  ): Promise<void> {
    const select = await this.getHarness<MatSelectHarness>(
      fixture,
      MatSelectHarness,
      selectSelector
    );
    await select.open();
    await select.clickOptions({ text: optionText });
  }
}
```

## 🧪 **Component Testing Patterns**

### **Testing Material Components with Harnesses**

```typescript
// user-profile.component.spec.ts
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { NoopAnimationsModule } from '@angular/platform-browser/animations';
import { ReactiveFormsModule } from '@angular/forms';
import { MatFormFieldModule } from '@angular/material/form-field';
import { MatInputModule } from '@angular/material/input';
import { MatButtonModule } from '@angular/material/button';
import { MatSelectModule } from '@angular/material/select';
import { HarnessLoader } from '@angular/cdk/testing';
import { TestbedHarnessEnvironment } from '@angular/cdk/testing/testbed';
import { MatInputHarness } from '@angular/material/input/testing';
import { MatSelectHarness } from '@angular/material/select/testing';
import { MatButtonHarness } from '@angular/material/button/testing';

import { UserProfileComponent } from './user-profile.component';
import { MaterialTestingUtils } from '@testing/material-testing.utils';

describe('UserProfileComponent', () => {
  let component: UserProfileComponent;
  let fixture: ComponentFixture<UserProfileComponent>;
  let loader: HarnessLoader;

  const mockUser = {
    id: '1',
    name: 'John Doe',
    email: 'john@example.com',
    role: 'admin'
  };

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [UserProfileComponent],
      imports: [
        NoopAnimationsModule,
        ReactiveFormsModule,
        MatFormFieldModule,
        MatInputModule,
        MatButtonModule,
        MatSelectModule
      ]
    }).compileComponents();

    fixture = TestBed.createComponent(UserProfileComponent);
    component = fixture.componentInstance;
    loader = TestbedHarnessEnvironment.loader(fixture);
    fixture.detectChanges();
  });

  describe('Form Validation', () => {
    it('should display validation errors for empty required fields', async () => {
      // Trigger validation
      await MaterialTestingUtils.clickButton(fixture, 'save-button');
      fixture.detectChanges();

      const nameInput = await loader.getHarness(
        MatInputHarness.with({ selector: '[data-test="name-input"]' })
      );
      const emailInput = await loader.getHarness(
        MatInputHarness.with({ selector: '[data-test="email-input"]' })
      );

      expect(await nameInput.hasErrors()).toBe(true);
      expect(await emailInput.hasErrors()).toBe(true);
    });

    it('should validate email format', async () => {
      await MaterialTestingUtils.fillInput(
        fixture,
        '[data-test="email-input"]',
        'invalid-email'
      );
      await MaterialTestingUtils.clickButton(fixture, 'save-button');
      fixture.detectChanges();

      const emailInput = await loader.getHarness(
        MatInputHarness.with({ selector: '[data-test="email-input"]' })
      );
      expect(await emailInput.hasErrors()).toBe(true);
    });
  });

  describe('User Interaction', () => {
    it('should update form when user selects role', async () => {
      await MaterialTestingUtils.selectOption(
        fixture,
        '[data-test="role-select"]',
        'Administrator'
      );
      fixture.detectChanges();

      expect(component.userForm.get('role')?.value).toBe('admin');
    });

    it('should enable save button when form is valid', async () => {
      // Fill valid form data
      await MaterialTestingUtils.fillInput(
        fixture,
        '[data-test="name-input"]',
        'John Doe'
      );
      await MaterialTestingUtils.fillInput(
        fixture,
        '[data-test="email-input"]',
        'john@example.com'
      );
      await MaterialTestingUtils.selectOption(
        fixture,
        '[data-test="role-select"]',
        'User'
      );
      fixture.detectChanges();

      const saveButton = await loader.getHarness(
        MatButtonHarness.with({ selector: '[data-test="save-button"]' })
      );
      expect(await saveButton.isDisabled()).toBe(false);
    });
  });

  describe('Data Binding', () => {
    it('should populate form with user data', () => {
      component.user = mockUser;
      component.ngOnInit();
      fixture.detectChanges();

      expect(component.userForm.get('name')?.value).toBe(mockUser.name);
      expect(component.userForm.get('email')?.value).toBe(mockUser.email);
      expect(component.userForm.get('role')?.value).toBe(mockUser.role);
    });
  });
});
```

### **Testing Custom Material Components**

```typescript
// custom-stepper.component.spec.ts
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { MatStepperModule } from '@angular/material/stepper';
import { MatStepperHarness } from '@angular/material/stepper/testing';
import { NoopAnimationsModule } from '@angular/platform-browser/animations';
import { HarnessLoader } from '@angular/cdk/testing';
import { TestbedHarnessEnvironment } from '@angular/cdk/testing/testbed';

import { CustomStepperComponent } from './custom-stepper.component';

describe('CustomStepperComponent', () => {
  let component: CustomStepperComponent;
  let fixture: ComponentFixture<CustomStepperComponent>;
  let loader: HarnessLoader;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [CustomStepperComponent],
      imports: [MatStepperModule, NoopAnimationsModule]
    }).compileComponents();

    fixture = TestBed.createComponent(CustomStepperComponent);
    component = fixture.componentInstance;
    loader = TestbedHarnessEnvironment.loader(fixture);
    fixture.detectChanges();
  });

  it('should navigate through steps correctly', async () => {
    const stepper = await loader.getHarness(MatStepperHarness);
    const steps = await stepper.getSteps();

    expect(steps.length).toBe(3);
    expect(await stepper.getSelectedStepIndex()).toBe(0);

    // Navigate to next step
    await steps[0].select();
    await stepper.next();
    expect(await stepper.getSelectedStepIndex()).toBe(1);

    // Navigate to previous step
    await stepper.previous();
    expect(await stepper.getSelectedStepIndex()).toBe(0);
  });

  it('should handle step completion states', async () => {
    const stepper = await loader.getHarness(MatStepperHarness);
    const steps = await stepper.getSteps();

    // Complete first step
    component.completeStep(0);
    fixture.detectChanges();

    expect(await steps[0].isCompleted()).toBe(true);
    expect(await steps[1].isOptional()).toBe(false);
  });
});
```

## 🎨 **Theme Testing Strategies**

### **Testing Theme Application**

```typescript
// theme-service.spec.ts
import { TestBed } from '@angular/core/testing';
import { DOCUMENT } from '@angular/common';
import { ThemeService } from './theme.service';

describe('ThemeService', () => {
  let service: ThemeService;
  let document: Document;

  beforeEach(() => {
    TestBed.configureTestingModule({
      providers: [ThemeService]
    });
    service = TestBed.inject(ThemeService);
    document = TestBed.inject(DOCUMENT);
  });

  describe('Theme Switching', () => {
    it('should apply dark theme class to body', () => {
      service.setTheme('dark');
      
      expect(document.body.classList.contains('dark-theme')).toBe(true);
      expect(document.body.classList.contains('light-theme')).toBe(false);
    });

    it('should apply light theme class to body', () => {
      service.setTheme('light');
      
      expect(document.body.classList.contains('light-theme')).toBe(true);
      expect(document.body.classList.contains('dark-theme')).toBe(false);
    });

    it('should persist theme preference', () => {
      service.setTheme('dark');
      
      expect(localStorage.getItem('theme')).toBe('dark');
    });
  });

  describe('CSS Custom Properties', () => {
    it('should update CSS custom properties for primary color', () => {
      const primaryColor = '#1976d2';
      service.setPrimaryColor(primaryColor);
      
      const rootStyles = getComputedStyle(document.documentElement);
      expect(rootStyles.getPropertyValue('--mdc-theme-primary')).toBe(primaryColor);
    });

    it('should generate color palette from primary color', () => {
      const primaryColor = '#1976d2';
      service.setPrimaryColor(primaryColor);
      
      const rootStyles = getComputedStyle(document.documentElement);
      expect(rootStyles.getPropertyValue('--mdc-theme-primary-50')).toBeTruthy();
      expect(rootStyles.getPropertyValue('--mdc-theme-primary-100')).toBeTruthy();
      expect(rootStyles.getPropertyValue('--mdc-theme-primary-500')).toBe(primaryColor);
    });
  });

  describe('High Contrast Mode', () => {
    it('should enable high contrast mode', () => {
      service.setHighContrastMode(true);
      
      expect(document.body.classList.contains('cdk-high-contrast-active')).toBe(true);
    });

    it('should adjust color values for high contrast', () => {
      service.setHighContrastMode(true);
      
      const rootStyles = getComputedStyle(document.documentElement);
      const surfaceColor = rootStyles.getPropertyValue('--mdc-theme-surface');
      const onSurfaceColor = rootStyles.getPropertyValue('--mdc-theme-on-surface');
      
      // Should have high contrast ratio
      const contrastRatio = calculateContrastRatio(surfaceColor, onSurfaceColor);
      expect(contrastRatio).toBeGreaterThan(7); // WCAG AAA level
    });
  });
});

function calculateContrastRatio(color1: string, color2: string): number {
  // Implementation of WCAG contrast ratio calculation
  // This is a simplified version - use a proper color contrast library
  const luminance1 = getLuminance(color1);
  const luminance2 = getLuminance(color2);
  
  const lighter = Math.max(luminance1, luminance2);
  const darker = Math.min(luminance1, luminance2);
  
  return (lighter + 0.05) / (darker + 0.05);
}

function getLuminance(color: string): number {
  // Simplified luminance calculation
  // In real implementation, use a proper color library
  return 0.5; // Placeholder
}
```

### **Testing Dynamic Theme Generation**

```typescript
// dynamic-theme.spec.ts
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { MatButtonModule } from '@angular/material/button';
import { MatCardModule } from '@angular/material/card';
import { DOCUMENT } from '@angular/common';

import { DynamicThemeComponent } from './dynamic-theme.component';
import { ThemeService } from './theme.service';

describe('DynamicThemeComponent', () => {
  let component: DynamicThemeComponent;
  let fixture: ComponentFixture<DynamicThemeComponent>;
  let themeService: jasmine.SpyObj<ThemeService>;
  let document: Document;

  beforeEach(async () => {
    const themeServiceSpy = jasmine.createSpyObj('ThemeService', [
      'generateThemeFromImage',
      'applyGeneratedTheme',
      'getColorPalette'
    ]);

    await TestBed.configureTestingModule({
      declarations: [DynamicThemeComponent],
      imports: [MatButtonModule, MatCardModule],
      providers: [
        { provide: ThemeService, useValue: themeServiceSpy }
      ]
    }).compileComponents();

    fixture = TestBed.createComponent(DynamicThemeComponent);
    component = fixture.componentInstance;
    themeService = TestBed.inject(ThemeService) as jasmine.SpyObj<ThemeService>;
    document = TestBed.inject(DOCUMENT);
    
    fixture.detectChanges();
  });

  it('should generate theme from uploaded image', async () => {
    const mockFile = new File([''], 'test-image.jpg', { type: 'image/jpeg' });
    const mockColors = {
      primary: '#1976d2',
      secondary: '#dc004e',
      tertiary: '#8bc34a'
    };

    themeService.generateThemeFromImage.and.returnValue(Promise.resolve(mockColors));

    await component.onImageUpload(mockFile);

    expect(themeService.generateThemeFromImage).toHaveBeenCalledWith(mockFile);
    expect(themeService.applyGeneratedTheme).toHaveBeenCalledWith(mockColors);
  });

  it('should preview theme before applying', () => {
    const mockColors = {
      primary: '#1976d2',
      secondary: '#dc004e',
      tertiary: '#8bc34a'
    };

    component.previewTheme(mockColors);

    // Check if preview styles are applied
    const previewElement = fixture.nativeElement.querySelector('.theme-preview');
    expect(previewElement.style.getPropertyValue('--preview-primary')).toBe(mockColors.primary);
  });

  it('should validate color accessibility', () => {
    const mockColors = {
      primary: '#ffffff', // White on white would fail
      secondary: '#ffffff',
      tertiary: '#ffffff'
    };

    const isValid = component.validateThemeAccessibility(mockColors);
    expect(isValid).toBe(false);
  });
});
```

## ♿ **Accessibility Testing**

### **Testing ARIA Attributes and Roles**

```typescript
// accessibility.spec.ts
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { A11yModule } from '@angular/cdk/a11y';
import { MatButtonModule } from '@angular/material/button';
import { MatFormFieldModule } from '@angular/material/form-field';
import { MatInputModule } from '@angular/material/input';

import { AccessibleFormComponent } from './accessible-form.component';

describe('AccessibleFormComponent - Accessibility', () => {
  let component: AccessibleFormComponent;
  let fixture: ComponentFixture<AccessibleFormComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [AccessibleFormComponent],
      imports: [A11yModule, MatButtonModule, MatFormFieldModule, MatInputModule]
    }).compileComponents();

    fixture = TestBed.createComponent(AccessibleFormComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  describe('ARIA Attributes', () => {
    it('should have proper ARIA labels', () => {
      const nameInput = fixture.nativeElement.querySelector('[data-test="name-input"]');
      const emailInput = fixture.nativeElement.querySelector('[data-test="email-input"]');

      expect(nameInput.getAttribute('aria-label')).toBe('Full Name');
      expect(emailInput.getAttribute('aria-label')).toBe('Email Address');
    });

    it('should associate form controls with error messages', () => {
      // Trigger validation errors
      component.userForm.get('name')?.setValue('');
      component.userForm.get('name')?.markAsTouched();
      fixture.detectChanges();

      const nameInput = fixture.nativeElement.querySelector('[data-test="name-input"]');
      const errorMessage = fixture.nativeElement.querySelector('.mat-error');

      expect(nameInput.getAttribute('aria-describedby')).toContain(errorMessage.id);
      expect(nameInput.getAttribute('aria-invalid')).toBe('true');
    });

    it('should have proper ARIA live regions for dynamic content', () => {
      const statusRegion = fixture.nativeElement.querySelector('[data-test="status-region"]');
      
      expect(statusRegion.getAttribute('aria-live')).toBe('polite');
      expect(statusRegion.getAttribute('aria-atomic')).toBe('true');
    });
  });

  describe('Keyboard Navigation', () => {
    it('should support keyboard navigation through form fields', () => {
      const nameInput = fixture.nativeElement.querySelector('[data-test="name-input"]');
      const emailInput = fixture.nativeElement.querySelector('[data-test="email-input"]');
      const submitButton = fixture.nativeElement.querySelector('[data-test="submit-button"]');

      // Simulate Tab navigation
      nameInput.focus();
      expect(document.activeElement).toBe(nameInput);

      // Tab to next field
      const tabEvent = new KeyboardEvent('keydown', { key: 'Tab' });
      nameInput.dispatchEvent(tabEvent);
      fixture.detectChanges();

      setTimeout(() => {
        expect(document.activeElement).toBe(emailInput);
      }, 0);
    });

    it('should handle Enter key submission', () => {
      spyOn(component, 'onSubmit');
      
      const form = fixture.nativeElement.querySelector('form');
      const enterEvent = new KeyboardEvent('keydown', { key: 'Enter' });
      form.dispatchEvent(enterEvent);

      expect(component.onSubmit).toHaveBeenCalled();
    });
  });

  describe('Focus Management', () => {
    it('should trap focus in modal dialogs', () => {
      component.openModal();
      fixture.detectChanges();

      const modal = fixture.nativeElement.querySelector('.modal');
      const focusableElements = modal.querySelectorAll(
        'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])'
      );

      const firstElement = focusableElements[0] as HTMLElement;
      const lastElement = focusableElements[focusableElements.length - 1] as HTMLElement;

      // Test forward focus trap
      lastElement.focus();
      const tabEvent = new KeyboardEvent('keydown', { key: 'Tab' });
      lastElement.dispatchEvent(tabEvent);

      setTimeout(() => {
        expect(document.activeElement).toBe(firstElement);
      }, 0);
    });

    it('should restore focus after modal closes', () => {
      const triggerButton = fixture.nativeElement.querySelector('[data-test="open-modal"]');
      triggerButton.focus();

      component.openModal();
      fixture.detectChanges();

      component.closeModal();
      fixture.detectChanges();

      setTimeout(() => {
        expect(document.activeElement).toBe(triggerButton);
      }, 0);
    });
  });

  describe('Screen Reader Support', () => {
    it('should announce form validation errors', () => {
      const errorContainer = fixture.nativeElement.querySelector('[aria-live="assertive"]');
      
      component.userForm.get('email')?.setValue('invalid-email');
      component.userForm.get('email')?.markAsTouched();
      fixture.detectChanges();

      expect(errorContainer.textContent).toContain('Please enter a valid email address');
    });

    it('should provide context for complex widgets', () => {
      const dataTable = fixture.nativeElement.querySelector('[role="grid"]');
      
      expect(dataTable.getAttribute('aria-label')).toBe('User data table with 3 columns and 5 rows');
      expect(dataTable.getAttribute('aria-describedby')).toContain('table-instructions');
    });
  });
});
```

## 🚀 **Advanced Testing Patterns**

### **Testing Reactive Patterns**

```typescript
// reactive-component.spec.ts
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { of, Subject, throwError } from 'rxjs';
import { delay } from 'rxjs/operators';

import { ReactiveDataComponent } from './reactive-data.component';
import { DataService } from './data.service';

describe('ReactiveDataComponent', () => {
  let component: ReactiveDataComponent;
  let fixture: ComponentFixture<ReactiveDataComponent>;
  let dataService: jasmine.SpyObj<DataService>;
  let refreshSubject: Subject<void>;

  beforeEach(async () => {
    const dataServiceSpy = jasmine.createSpyObj('DataService', ['getData', 'updateData']);
    refreshSubject = new Subject<void>();

    await TestBed.configureTestingModule({
      declarations: [ReactiveDataComponent],
      providers: [
        { provide: DataService, useValue: dataServiceSpy }
      ]
    }).compileComponents();

    fixture = TestBed.createComponent(ReactiveDataComponent);
    component = fixture.componentInstance;
    dataService = TestBed.inject(DataService) as jasmine.SpyObj<DataService>;
  });

  it('should handle loading states correctly', () => {
    const mockData = [{ id: 1, name: 'Test' }];
    dataService.getData.and.returnValue(of(mockData).pipe(delay(100)));

    component.ngOnInit();
    
    // Should show loading initially
    expect(component.loading).toBe(true);
    expect(component.data).toEqual([]);

    // After data loads
    setTimeout(() => {
      expect(component.loading).toBe(false);
      expect(component.data).toEqual(mockData);
    }, 150);
  });

  it('should handle error states', () => {
    const errorResponse = new Error('API Error');
    dataService.getData.and.returnValue(throwError(errorResponse));

    component.ngOnInit();

    expect(component.loading).toBe(false);
    expect(component.error).toBe('API Error');
    expect(component.data).toEqual([]);
  });

  it('should refresh data when refresh subject emits', () => {
    const initialData = [{ id: 1, name: 'Initial' }];
    const refreshedData = [{ id: 1, name: 'Refreshed' }];
    
    dataService.getData.and.returnValues(of(initialData), of(refreshedData));
    component.refresh$ = refreshSubject.asObservable();

    component.ngOnInit();
    expect(component.data).toEqual(initialData);

    // Trigger refresh
    refreshSubject.next();
    expect(component.data).toEqual(refreshedData);
    expect(dataService.getData).toHaveBeenCalledTimes(2);
  });
});
```

### **Testing Performance Metrics**

```typescript
// performance-testing.spec.ts
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { MatTableModule } from '@angular/material/table';
import { MatPaginatorModule } from '@angular/material/paginator';
import { MatSortModule } from '@angular/material/sort';

import { LargeDataTableComponent } from './large-data-table.component';

describe('LargeDataTableComponent - Performance', () => {
  let component: LargeDataTableComponent;
  let fixture: ComponentFixture<LargeDataTableComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      declarations: [LargeDataTableComponent],
      imports: [MatTableModule, MatPaginatorModule, MatSortModule]
    }).compileComponents();

    fixture = TestBed.createComponent(LargeDataTableComponent);
    component = fixture.componentInstance;
  });

  it('should render large dataset within performance budget', () => {
    const startTime = performance.now();
    
    // Generate large dataset
    const largeDataset = Array.from({ length: 10000 }, (_, i) => ({
      id: i,
      name: `Item ${i}`,
      value: Math.random() * 1000
    }));

    component.dataSource.data = largeDataset;
    fixture.detectChanges();

    const endTime = performance.now();
    const renderTime = endTime - startTime;

    // Should render within 100ms budget
    expect(renderTime).toBeLessThan(100);
  });

  it('should handle virtual scrolling efficiently', () => {
    const startTime = performance.now();
    
    component.enableVirtualScrolling = true;
    component.itemHeight = 48;
    
    const largeDataset = Array.from({ length: 100000 }, (_, i) => ({
      id: i,
      name: `Item ${i}`,
      value: Math.random() * 1000
    }));

    component.dataSource.data = largeDataset;
    fixture.detectChanges();

    const endTime = performance.now();
    const renderTime = endTime - startTime;

    // Virtual scrolling should maintain performance
    expect(renderTime).toBeLessThan(50);
    
    // Should only render visible items
    const renderedRows = fixture.nativeElement.querySelectorAll('mat-row');
    expect(renderedRows.length).toBeLessThan(100); // Only visible rows
  });

  it('should debounce search input efficiently', (done) => {
    spyOn(component, 'performSearch');
    
    const searchInput = fixture.nativeElement.querySelector('[data-test="search-input"]');
    
    // Simulate rapid typing
    searchInput.value = 'a';
    searchInput.dispatchEvent(new Event('input'));
    
    searchInput.value = 'ab';
    searchInput.dispatchEvent(new Event('input'));
    
    searchInput.value = 'abc';
    searchInput.dispatchEvent(new Event('input'));

    // Should debounce and only call search once
    setTimeout(() => {
      expect(component.performSearch).toHaveBeenCalledTimes(1);
      expect(component.performSearch).toHaveBeenCalledWith('abc');
      done();
    }, 350); // Wait for debounce time + buffer
  });
});
```

## 📊 **Testing Checklist**

### **Component Testing Checklist**
- [ ] All component inputs and outputs tested
- [ ] Form validation scenarios covered
- [ ] Error handling tested
- [ ] Loading states verified
- [ ] User interactions simulated
- [ ] Material harnesses used appropriately

### **Theme Testing Checklist**
- [ ] Theme switching functionality
- [ ] CSS custom properties updates
- [ ] Color contrast compliance
- [ ] High contrast mode support
- [ ] Theme persistence tested

### **Accessibility Testing Checklist**
- [ ] ARIA attributes present and correct
- [ ] Keyboard navigation working
- [ ] Screen reader announcements
- [ ] Focus management tested
- [ ] Color contrast ratios verified

### **Performance Testing Checklist**
- [ ] Render time within budget
- [ ] Memory usage optimized
- [ ] Large dataset handling
- [ ] Virtual scrolling performance
- [ ] Debouncing/throttling effective

---

## 🎯 **Next Steps**

1. **Implement Test Suites** - Set up comprehensive test suites for your projects
2. **Add Visual Testing** - Integrate visual regression testing
3. **Performance Monitoring** - Set up continuous performance monitoring
4. **Accessibility Audits** - Regular accessibility testing in CI/CD

This unit testing strategy provides the foundation for building robust, accessible, and performant Angular Material applications. Each pattern can be adapted to your specific use cases and requirements.
