# End-to-End Testing Patterns for Angular Material Applications

## 🎯 **Overview**

This guide provides comprehensive end-to-end (E2E) testing strategies for Angular Material 3 applications using modern testing frameworks including Cypress, Playwright, and WebdriverIO. We'll cover user journey testing, visual testing, performance testing, and advanced E2E patterns.

## 📋 **Table of Contents**

1. [E2E Testing Framework Setup](#e2e-testing-framework-setup)
2. [Page Object Models](#page-object-models)
3. [User Journey Testing](#user-journey-testing)
4. [Visual Regression Testing](#visual-regression-testing)
5. [Performance E2E Testing](#performance-e2e-testing)
6. [Cross-Browser Testing](#cross-browser-testing)
7. [Mobile Testing](#mobile-testing)
8. [CI/CD Integration](#ci-cd-integration)

## 🛠️ **E2E Testing Framework Setup**

### **Cypress Configuration for Angular Material**

```typescript
// cypress.config.ts
import { defineConfig } from 'cypress';

export default defineConfig({
  e2e: {
    baseUrl: 'http://localhost:4200',
    supportFile: 'cypress/support/e2e.ts',
    specPattern: 'cypress/e2e/**/*.cy.{js,jsx,ts,tsx}',
    viewportWidth: 1280,
    viewportHeight: 720,
    video: true,
    screenshotOnRunFailure: true,
    experimentalStudio: true,
    retries: {
      runMode: 2,
      openMode: 0
    },
    env: {
      coverage: true,
      codeCoverage: {
        url: 'http://localhost:4200/__coverage__'
      }
    }
  },
  component: {
    devServer: {
      framework: 'angular',
      bundler: 'webpack',
    },
    specPattern: '**/*.cy.ts'
  }
});
```

### **Custom Commands for Material Components**

```typescript
// cypress/support/commands.ts
declare global {
  namespace Cypress {
    interface Chainable {
      getByTestId(testId: string): Chainable<JQuery<HTMLElement>>;
      clickMatButton(selector: string): Chainable<void>;
      fillMatInput(selector: string, value: string): Chainable<void>;
      selectMatOption(selectSelector: string, optionText: string): Chainable<void>;
      waitForMatDialog(): Chainable<void>;
      dismissMatSnackbar(): Chainable<void>;
      validateAccessibility(): Chainable<void>;
    }
  }
}

// Custom commands implementation
Cypress.Commands.add('getByTestId', (testId: string) => {
  return cy.get(`[data-test="${testId}"]`);
});

Cypress.Commands.add('clickMatButton', (selector: string) => {
  cy.get(selector)
    .should('be.visible')
    .and('not.be.disabled')
    .click();
});

Cypress.Commands.add('fillMatInput', (selector: string, value: string) => {
  cy.get(selector)
    .should('be.visible')
    .clear()
    .type(value)
    .should('have.value', value);
});

Cypress.Commands.add('selectMatOption', (selectSelector: string, optionText: string) => {
  cy.get(selectSelector).click();
  cy.get('mat-option')
    .contains(optionText)
    .click();
  cy.get('.cdk-overlay-backdrop').click({ force: true });
});

Cypress.Commands.add('waitForMatDialog', () => {
  cy.get('mat-dialog-container')
    .should('be.visible')
    .and('have.class', 'mat-dialog-container');
});

Cypress.Commands.add('dismissMatSnackbar', () => {
  cy.get('simple-snack-bar')
    .should('be.visible');
  
  // Wait for auto-dismiss or click dismiss button
  cy.get('simple-snack-bar button[mat-button]', { timeout: 1000 })
    .click()
    .or(() => {
      cy.wait(4000); // Default snackbar duration
    });
});

Cypress.Commands.add('validateAccessibility', () => {
  cy.injectAxe();
  cy.checkA11y();
});
```

### **Playwright Configuration**

```typescript
// playwright.config.ts
import { defineConfig, devices } from '@playwright/test';

export default defineConfig({
  testDir: './e2e',
  fullyParallel: true,
  forbidOnly: !!process.env.CI,
  retries: process.env.CI ? 2 : 0,
  workers: process.env.CI ? 1 : undefined,
  reporter: [
    ['html'],
    ['junit', { outputFile: 'test-results/results.xml' }],
    ['json', { outputFile: 'test-results/results.json' }]
  ],
  use: {
    baseURL: 'http://localhost:4200',
    trace: 'on-first-retry',
    screenshot: 'only-on-failure',
    video: 'retain-on-failure'
  },
  projects: [
    {
      name: 'chromium',
      use: { ...devices['Desktop Chrome'] },
    },
    {
      name: 'firefox',
      use: { ...devices['Desktop Firefox'] },
    },
    {
      name: 'webkit',
      use: { ...devices['Desktop Safari'] },
    },
    {
      name: 'Mobile Chrome',
      use: { ...devices['Pixel 5'] },
    },
    {
      name: 'Mobile Safari',
      use: { ...devices['iPhone 12'] },
    },
  ],
  webServer: {
    command: 'npm run start',
    url: 'http://localhost:4200',
    reuseExistingServer: !process.env.CI,
  },
});
```

## 🏗️ **Page Object Models**

### **Material Dashboard Page Object**

```typescript
// e2e/page-objects/dashboard.page.ts
import { Page, Locator } from '@playwright/test';

export class DashboardPage {
  readonly page: Page;
  readonly header: Locator;
  readonly navigationDrawer: Locator;
  readonly menuToggle: Locator;
  readonly userMenu: Locator;
  readonly themeToggle: Locator;
  readonly searchInput: Locator;
  readonly dataTable: Locator;
  readonly addButton: Locator;
  readonly filterChips: Locator;
  readonly pagination: Locator;

  constructor(page: Page) {
    this.page = page;
    this.header = page.locator('mat-toolbar[data-test="app-header"]');
    this.navigationDrawer = page.locator('mat-sidenav[data-test="nav-drawer"]');
    this.menuToggle = page.locator('button[data-test="menu-toggle"]');
    this.userMenu = page.locator('button[data-test="user-menu"]');
    this.themeToggle = page.locator('button[data-test="theme-toggle"]');
    this.searchInput = page.locator('input[data-test="search-input"]');
    this.dataTable = page.locator('mat-table[data-test="data-table"]');
    this.addButton = page.locator('button[data-test="add-button"]');
    this.filterChips = page.locator('mat-chip-listbox[data-test="filter-chips"]');
    this.pagination = page.locator('mat-paginator[data-test="table-paginator"]');
  }

  async goto() {
    await this.page.goto('/dashboard');
    await this.waitForLoad();
  }

  async waitForLoad() {
    await this.header.waitFor();
    await this.dataTable.waitFor();
    await this.page.waitForLoadState('networkidle');
  }

  async toggleNavigation() {
    await this.menuToggle.click();
    await this.page.waitForTimeout(300); // Animation time
  }

  async searchFor(query: string) {
    await this.searchInput.fill(query);
    await this.searchInput.press('Enter');
    await this.page.waitForResponse(/.*\/api\/search.*/);
  }

  async selectTableRow(rowIndex: number) {
    const row = this.dataTable.locator('mat-row').nth(rowIndex);
    await row.click();
    return row;
  }

  async sortTableBy(columnName: string) {
    const header = this.dataTable.locator('mat-header-cell').filter({ hasText: columnName });
    await header.click();
    await this.page.waitForResponse(/.*\/api\/data.*/);
  }

  async applyFilter(filterName: string) {
    const chip = this.filterChips.locator('mat-chip').filter({ hasText: filterName });
    await chip.click();
    await this.page.waitForResponse(/.*\/api\/data.*/);
  }

  async goToNextPage() {
    const nextButton = this.pagination.locator('button[aria-label="Next page"]');
    await nextButton.click();
    await this.page.waitForResponse(/.*\/api\/data.*/);
  }

  async changePageSize(size: number) {
    const pageSizeSelect = this.pagination.locator('mat-select[data-test="page-size"]');
    await pageSizeSelect.click();
    await this.page.locator('mat-option').filter({ hasText: size.toString() }).click();
    await this.page.waitForResponse(/.*\/api\/data.*/);
  }

  async switchTheme() {
    await this.themeToggle.click();
    await this.page.waitForFunction(() => {
      return document.body.classList.contains('dark-theme') || 
             document.body.classList.contains('light-theme');
    });
  }

  async openUserMenu() {
    await this.userMenu.click();
    await this.page.locator('mat-menu').waitFor();
  }

  async addNewItem() {
    await this.addButton.click();
    await this.page.locator('mat-dialog-container').waitFor();
  }

  async getTableData() {
    const rows = await this.dataTable.locator('mat-row').all();
    const data = [];
    
    for (const row of rows) {
      const cells = await row.locator('mat-cell').allTextContents();
      data.push(cells);
    }
    
    return data;
  }

  async validateTableStructure() {
    const headers = await this.dataTable.locator('mat-header-cell').allTextContents();
    return headers;
  }
}
```

### **Form Page Object**

```typescript
// e2e/page-objects/user-form.page.ts
import { Page, Locator } from '@playwright/test';

export class UserFormPage {
  readonly page: Page;
  readonly form: Locator;
  readonly nameInput: Locator;
  readonly emailInput: Locator;
  readonly roleSelect: Locator;
  readonly birthdatePicker: Locator;
  readonly skillsAutocomplete: Locator;
  readonly bioTextarea: Locator;
  readonly avatarUpload: Locator;
  readonly submitButton: Locator;
  readonly resetButton: Locator;
  readonly errorMessages: Locator;

  constructor(page: Page) {
    this.page = page;
    this.form = page.locator('form[data-test="user-form"]');
    this.nameInput = page.locator('input[data-test="name-input"]');
    this.emailInput = page.locator('input[data-test="email-input"]');
    this.roleSelect = page.locator('mat-select[data-test="role-select"]');
    this.birthdatePicker = page.locator('input[data-test="birthdate-picker"]');
    this.skillsAutocomplete = page.locator('input[data-test="skills-autocomplete"]');
    this.bioTextarea = page.locator('textarea[data-test="bio-textarea"]');
    this.avatarUpload = page.locator('input[data-test="avatar-upload"]');
    this.submitButton = page.locator('button[data-test="submit-button"]');
    this.resetButton = page.locator('button[data-test="reset-button"]');
    this.errorMessages = page.locator('.mat-error');
  }

  async goto() {
    await this.page.goto('/users/new');
    await this.form.waitFor();
  }

  async fillBasicInfo(userData: {
    name: string;
    email: string;
    role: string;
  }) {
    await this.nameInput.fill(userData.name);
    await this.emailInput.fill(userData.email);
    
    await this.roleSelect.click();
    await this.page.locator('mat-option').filter({ hasText: userData.role }).click();
  }

  async selectBirthdate(date: string) {
    await this.birthdatePicker.fill(date);
    await this.birthdatePicker.press('Enter');
  }

  async addSkills(skills: string[]) {
    for (const skill of skills) {
      await this.skillsAutocomplete.fill(skill);
      await this.page.locator('mat-option').filter({ hasText: skill }).click();
    }
  }

  async uploadAvatar(filePath: string) {
    await this.avatarUpload.setInputFiles(filePath);
    await this.page.waitForSelector('.avatar-preview');
  }

  async fillBio(bio: string) {
    await this.bioTextarea.fill(bio);
  }

  async submitForm() {
    await this.submitButton.click();
  }

  async resetForm() {
    await this.resetButton.click();
  }

  async getValidationErrors() {
    return await this.errorMessages.allTextContents();
  }

  async validateFormSubmission() {
    // Wait for success message or redirect
    return Promise.race([
      this.page.waitForSelector('.success-message'),
      this.page.waitForURL('/users'),
      this.page.waitForSelector('mat-snack-bar-container')
    ]);
  }

  async validateFormState() {
    const isSubmitDisabled = await this.submitButton.isDisabled();
    const hasErrors = await this.errorMessages.count() > 0;
    
    return {
      canSubmit: !isSubmitDisabled,
      hasValidationErrors: hasErrors
    };
  }
}
```

## 🚀 **User Journey Testing**

### **Complete User Registration Flow**

```typescript
// e2e/journeys/user-registration.spec.ts
import { test, expect } from '@playwright/test';
import { DashboardPage } from '../page-objects/dashboard.page';
import { UserFormPage } from '../page-objects/user-form.page';

test.describe('User Registration Journey', () => {
  let dashboardPage: DashboardPage;
  let userFormPage: UserFormPage;

  test.beforeEach(async ({ page }) => {
    dashboardPage = new DashboardPage(page);
    userFormPage = new UserFormPage(page);
  });

  test('complete user registration flow', async ({ page }) => {
    // Step 1: Navigate to dashboard
    await dashboardPage.goto();
    await expect(dashboardPage.header).toBeVisible();

    // Step 2: Open user creation form
    await dashboardPage.addNewItem();
    await expect(page.locator('mat-dialog-container')).toBeVisible();

    // Step 3: Fill user form with valid data
    const userData = {
      name: 'John Doe',
      email: 'john.doe@example.com',
      role: 'Administrator'
    };

    await userFormPage.fillBasicInfo(userData);
    await userFormPage.selectBirthdate('1990-01-15');
    await userFormPage.addSkills(['JavaScript', 'Angular', 'TypeScript']);
    await userFormPage.uploadAvatar('./fixtures/avatar.jpg');
    await userFormPage.fillBio('Experienced developer with Angular expertise');

    // Step 4: Validate form state before submission
    const formState = await userFormPage.validateFormState();
    expect(formState.canSubmit).toBe(true);
    expect(formState.hasValidationErrors).toBe(false);

    // Step 5: Submit form
    await userFormPage.submitForm();
    await userFormPage.validateFormSubmission();

    // Step 6: Verify user appears in table
    await dashboardPage.waitForLoad();
    const tableData = await dashboardPage.getTableData();
    const newUserRow = tableData.find(row => row.includes(userData.name));
    expect(newUserRow).toBeDefined();

    // Step 7: Verify user details
    expect(newUserRow).toContain(userData.name);
    expect(newUserRow).toContain(userData.email);
    expect(newUserRow).toContain(userData.role);
  });

  test('user registration with validation errors', async ({ page }) => {
    await dashboardPage.goto();
    await dashboardPage.addNewItem();

    // Submit form with empty required fields
    await userFormPage.submitForm();

    // Verify validation errors appear
    const errors = await userFormPage.getValidationErrors();
    expect(errors).toContain('Name is required');
    expect(errors).toContain('Email is required');

    // Fill invalid email
    await userFormPage.fillBasicInfo({
      name: 'Test User',
      email: 'invalid-email',
      role: 'User'
    });

    await userFormPage.submitForm();
    const emailErrors = await userFormPage.getValidationErrors();
    expect(emailErrors).toContain('Please enter a valid email address');

    // Form should not be submittable
    const formState = await userFormPage.validateFormState();
    expect(formState.canSubmit).toBe(false);
  });

  test('user registration cancellation flow', async ({ page }) => {
    await dashboardPage.goto();
    const initialTableData = await dashboardPage.getTableData();

    await dashboardPage.addNewItem();
    
    // Partially fill form
    await userFormPage.fillBasicInfo({
      name: 'Cancelled User',
      email: 'cancelled@example.com',
      role: 'User'
    });

    // Cancel/close dialog
    await page.locator('button[data-test="cancel-button"]').click();
    
    // Verify no changes to table
    await dashboardPage.waitForLoad();
    const finalTableData = await dashboardPage.getTableData();
    expect(finalTableData).toEqual(initialTableData);
  });
});
```

### **E-commerce Shopping Flow**

```typescript
// e2e/journeys/shopping-flow.spec.ts
import { test, expect } from '@playwright/test';

test.describe('E-commerce Shopping Journey', () => {
  test('complete purchase flow', async ({ page }) => {
    // Step 1: Browse products
    await page.goto('/products');
    await expect(page.locator('mat-card.product-card')).toHaveCount(12);

    // Step 2: Filter products
    await page.locator('mat-select[data-test="category-filter"]').click();
    await page.locator('mat-option').filter({ hasText: 'Electronics' }).click();
    await page.waitForResponse(/.*\/api\/products.*/);

    // Step 3: Search for specific product
    await page.locator('input[data-test="search-input"]').fill('laptop');
    await page.locator('input[data-test="search-input"]').press('Enter');
    await page.waitForResponse(/.*\/api\/products\/search.*/);

    // Step 4: Select product
    const productCard = page.locator('mat-card.product-card').first();
    await productCard.click();
    await page.waitForURL(/.*\/products\/\d+/);

    // Step 5: Add to cart
    await page.locator('button[data-test="add-to-cart"]').click();
    await expect(page.locator('mat-snack-bar-container')).toContainText('Added to cart');

    // Step 6: View cart
    await page.locator('button[data-test="cart-button"]').click();
    await page.waitForURL('/cart');

    // Step 7: Update quantity
    const quantityInput = page.locator('input[data-test="quantity-input"]');
    await quantityInput.fill('2');
    await page.locator('button[data-test="update-cart"]').click();

    // Step 8: Proceed to checkout
    await page.locator('button[data-test="checkout-button"]').click();
    await page.waitForURL('/checkout');

    // Step 9: Fill shipping information
    await page.locator('input[data-test="shipping-name"]').fill('John Doe');
    await page.locator('input[data-test="shipping-address"]').fill('123 Main St');
    await page.locator('input[data-test="shipping-city"]').fill('New York');
    await page.locator('mat-select[data-test="shipping-state"]').click();
    await page.locator('mat-option').filter({ hasText: 'New York' }).click();
    await page.locator('input[data-test="shipping-zip"]').fill('10001');

    // Step 10: Fill payment information
    await page.locator('input[data-test="card-number"]').fill('4111111111111111');
    await page.locator('input[data-test="card-expiry"]').fill('12/25');
    await page.locator('input[data-test="card-cvv"]').fill('123');

    // Step 11: Review order
    await page.locator('button[data-test="review-order"]').click();
    await expect(page.locator('[data-test="order-summary"]')).toBeVisible();

    // Step 12: Place order
    await page.locator('button[data-test="place-order"]').click();
    await page.waitForURL('/order-confirmation');

    // Step 13: Verify order confirmation
    await expect(page.locator('h1')).toContainText('Order Confirmed');
    await expect(page.locator('[data-test="order-number"]')).toBeVisible();
  });

  test('shopping cart persistence across sessions', async ({ page, context }) => {
    // Add item to cart
    await page.goto('/products');
    await page.locator('mat-card.product-card').first().click();
    await page.locator('button[data-test="add-to-cart"]').click();

    // Close and reopen browser
    await page.close();
    const newPage = await context.newPage();

    // Verify cart persists
    await newPage.goto('/cart');
    await expect(newPage.locator('.cart-item')).toHaveCount(1);
  });
});
```

## 📸 **Visual Regression Testing**

### **Theme Visual Testing**

```typescript
// e2e/visual/theme-visual.spec.ts
import { test, expect } from '@playwright/test';

test.describe('Visual Regression - Themes', () => {
  test.beforeEach(async ({ page }) => {
    await page.goto('/dashboard');
    await page.locator('mat-toolbar').waitFor();
  });

  test('light theme visual consistency', async ({ page }) => {
    // Ensure light theme is active
    await page.evaluate(() => {
      document.body.className = 'light-theme';
    });
    
    await page.waitForTimeout(500); // Allow theme transition

    // Full page screenshot
    await expect(page).toHaveScreenshot('dashboard-light-theme.png', {
      fullPage: true,
      threshold: 0.2
    });

    // Component-specific screenshots
    await expect(page.locator('mat-toolbar')).toHaveScreenshot('toolbar-light.png');
    await expect(page.locator('mat-sidenav')).toHaveScreenshot('sidenav-light.png');
    await expect(page.locator('mat-table')).toHaveScreenshot('table-light.png');
  });

  test('dark theme visual consistency', async ({ page }) => {
    // Switch to dark theme
    await page.locator('button[data-test="theme-toggle"]').click();
    await page.waitForTimeout(500);

    await expect(page).toHaveScreenshot('dashboard-dark-theme.png', {
      fullPage: true,
      threshold: 0.2
    });

    await expect(page.locator('mat-toolbar')).toHaveScreenshot('toolbar-dark.png');
    await expect(page.locator('mat-sidenav')).toHaveScreenshot('sidenav-dark.png');
    await expect(page.locator('mat-table')).toHaveScreenshot('table-dark.png');
  });

  test('high contrast theme visual consistency', async ({ page }) => {
    // Enable high contrast mode
    await page.evaluate(() => {
      document.body.classList.add('cdk-high-contrast-active');
    });
    await page.waitForTimeout(500);

    await expect(page).toHaveScreenshot('dashboard-high-contrast.png', {
      fullPage: true,
      threshold: 0.3 // Higher threshold for high contrast variations
    });
  });

  test('custom theme visual consistency', async ({ page }) => {
    // Apply custom theme
    await page.evaluate(() => {
      const style = document.createElement('style');
      style.textContent = `
        :root {
          --mdc-theme-primary: #9c27b0;
          --mdc-theme-secondary: #ff5722;
          --mdc-theme-surface: #f3e5f5;
        }
      `;
      document.head.appendChild(style);
    });
    await page.waitForTimeout(500);

    await expect(page).toHaveScreenshot('dashboard-custom-theme.png', {
      fullPage: true,
      threshold: 0.2
    });
  });

  test('responsive design visual consistency', async ({ page }) => {
    // Test different viewport sizes
    const viewports = [
      { width: 1920, height: 1080, name: 'desktop-large' },
      { width: 1366, height: 768, name: 'desktop-medium' },
      { width: 768, height: 1024, name: 'tablet' },
      { width: 375, height: 667, name: 'mobile' }
    ];

    for (const viewport of viewports) {
      await page.setViewportSize(viewport);
      await page.waitForTimeout(500);
      
      await expect(page).toHaveScreenshot(`dashboard-${viewport.name}.png`, {
        fullPage: true,
        threshold: 0.2
      });
    }
  });
});
```

### **Component State Visual Testing**

```typescript
// e2e/visual/component-states.spec.ts
import { test, expect } from '@playwright/test';

test.describe('Visual Regression - Component States', () => {
  test('button states visual consistency', async ({ page }) => {
    await page.goto('/style-guide/buttons');

    // Test all button variants and states
    const buttonStates = [
      'primary-enabled',
      'primary-hover',
      'primary-focused',
      'primary-disabled',
      'secondary-enabled',
      'secondary-hover',
      'accent-enabled',
      'warn-enabled'
    ];

    for (const state of buttonStates) {
      const button = page.locator(`[data-test="button-${state}"]`);
      
      if (state.includes('hover')) {
        await button.hover();
      } else if (state.includes('focused')) {
        await button.focus();
      }
      
      await expect(button).toHaveScreenshot(`button-${state}.png`);
    }
  });

  test('form field states visual consistency', async ({ page }) => {
    await page.goto('/style-guide/form-fields');

    const fieldStates = [
      'empty',
      'filled',
      'focused',
      'error',
      'disabled',
      'readonly'
    ];

    for (const state of fieldStates) {
      const field = page.locator(`[data-test="field-${state}"]`);
      await expect(field).toHaveScreenshot(`form-field-${state}.png`);
    }
  });

  test('card elevation visual consistency', async ({ page }) => {
    await page.goto('/style-guide/cards');

    for (let elevation = 0; elevation <= 24; elevation += 4) {
      const card = page.locator(`[data-test="card-elevation-${elevation}"]`);
      await expect(card).toHaveScreenshot(`card-elevation-${elevation}.png`);
    }
  });
});
```

## ⚡ **Performance E2E Testing**

### **Page Load Performance**

```typescript
// e2e/performance/page-load.spec.ts
import { test, expect } from '@playwright/test';

test.describe('Performance - Page Load', () => {
  test('dashboard loads within performance budget', async ({ page }) => {
    // Start performance monitoring
    const startTime = Date.now();
    
    await page.goto('/dashboard');
    
    // Wait for critical content
    await page.locator('mat-toolbar').waitFor();
    await page.locator('mat-table').waitFor();
    
    const loadTime = Date.now() - startTime;
    
    // Assert load time is within budget (2 seconds)
    expect(loadTime).toBeLessThan(2000);
    
    // Check Web Vitals
    const webVitals = await page.evaluate(() => {
      return new Promise((resolve) => {
        new PerformanceObserver((list) => {
          const entries = list.getEntries();
          const vitals = {};
          
          entries.forEach((entry) => {
            if (entry.name === 'first-contentful-paint') {
              vitals.fcp = entry.value;
            }
            if (entry.name === 'largest-contentful-paint') {
              vitals.lcp = entry.value;
            }
            if (entry.name === 'first-input-delay') {
              vitals.fid = entry.value;
            }
            if (entry.name === 'cumulative-layout-shift') {
              vitals.cls = entry.value;
            }
          });
          
          resolve(vitals);
        }).observe({ entryTypes: ['paint', 'largest-contentful-paint', 'first-input', 'layout-shift'] });
      });
    });
    
    // Assert Web Vitals thresholds
    expect(webVitals.fcp).toBeLessThan(1800); // Good FCP < 1.8s
    expect(webVitals.lcp).toBeLessThan(2500); // Good LCP < 2.5s
    expect(webVitals.cls).toBeLessThan(0.1);  // Good CLS < 0.1
  });

  test('large data table performance', async ({ page }) => {
    // Navigate to page with large dataset
    await page.goto('/dashboard?dataset=large');
    
    const startTime = performance.now();
    
    // Wait for table to fully render
    await page.locator('mat-table').waitFor();
    await page.waitForFunction(() => {
      const rows = document.querySelectorAll('mat-row');
      return rows.length > 0;
    });
    
    const renderTime = performance.now() - startTime;
    
    // Table should render within 1 second even with large dataset
    expect(renderTime).toBeLessThan(1000);
    
    // Test scroll performance
    const scrollStartTime = performance.now();
    
    await page.evaluate(() => {
      const table = document.querySelector('mat-table');
      table.scrollTop = table.scrollHeight;
    });
    
    const scrollTime = performance.now() - scrollStartTime;
    expect(scrollTime).toBeLessThan(100); // Smooth scrolling
  });

  test('theme switching performance', async ({ page }) => {
    await page.goto('/dashboard');
    await page.locator('mat-toolbar').waitFor();
    
    const switchStartTime = performance.now();
    
    // Switch theme
    await page.locator('button[data-test="theme-toggle"]').click();
    
    // Wait for theme transition to complete
    await page.waitForFunction(() => {
      return document.body.classList.contains('dark-theme') || 
             document.body.classList.contains('light-theme');
    });
    
    const switchTime = performance.now() - switchStartTime;
    
    // Theme switch should be near-instantaneous
    expect(switchTime).toBeLessThan(300);
  });
});
```

### **Memory Usage Testing**

```typescript
// e2e/performance/memory-usage.spec.ts
import { test, expect } from '@playwright/test';

test.describe('Performance - Memory Usage', () => {
  test('memory usage stays within bounds during navigation', async ({ page }) => {
    // Get initial memory usage
    const initialMemory = await page.evaluate(() => {
      return (performance as any).memory?.usedJSHeapSize || 0;
    });
    
    // Navigate through multiple pages
    const pages = ['/dashboard', '/users', '/products', '/settings', '/reports'];
    
    for (const pagePath of pages) {
      await page.goto(pagePath);
      await page.waitForLoadState('networkidle');
      
      // Force garbage collection if available
      await page.evaluate(() => {
        if ((window as any).gc) {
          (window as any).gc();
        }
      });
      
      const currentMemory = await page.evaluate(() => {
        return (performance as any).memory?.usedJSHeapSize || 0;
      });
      
      // Memory shouldn't grow excessively (within 50MB increase)
      const memoryIncrease = currentMemory - initialMemory;
      expect(memoryIncrease).toBeLessThan(50 * 1024 * 1024); // 50MB
    }
  });

  test('no memory leaks in dynamic components', async ({ page }) => {
    await page.goto('/dashboard');
    
    // Get baseline memory
    await page.evaluate(() => {
      if ((window as any).gc) (window as any).gc();
    });
    
    const baselineMemory = await page.evaluate(() => {
      return (performance as any).memory?.usedJSHeapSize || 0;
    });
    
    // Create and destroy components multiple times
    for (let i = 0; i < 10; i++) {
      // Open dialog
      await page.locator('button[data-test="add-button"]').click();
      await page.locator('mat-dialog-container').waitFor();
      
      // Close dialog
      await page.locator('button[data-test="cancel-button"]').click();
      await page.waitForSelector('mat-dialog-container', { state: 'detached' });
    }
    
    // Force garbage collection and check final memory
    await page.evaluate(() => {
      if ((window as any).gc) (window as any).gc();
    });
    
    const finalMemory = await page.evaluate(() => {
      return (performance as any).memory?.usedJSHeapSize || 0;
    });
    
    const memoryIncrease = finalMemory - baselineMemory;
    
    // Should not have significant memory increase after component cleanup
    expect(memoryIncrease).toBeLessThan(5 * 1024 * 1024); // 5MB threshold
  });
});
```

## 🌐 **Cross-Browser Testing**

### **Browser Compatibility Matrix**

```typescript
// e2e/cross-browser/compatibility.spec.ts
import { test, expect, devices } from '@playwright/test';

const browsers = [
  { name: 'chromium', device: devices['Desktop Chrome'] },
  { name: 'firefox', device: devices['Desktop Firefox'] },
  { name: 'webkit', device: devices['Desktop Safari'] },
  { name: 'edge', device: devices['Desktop Edge'] }
];

browsers.forEach(({ name, device }) => {
  test.describe(`Cross-Browser: ${name}`, () => {
    test.use(device);

    test('core functionality works across browsers', async ({ page }) => {
      await page.goto('/dashboard');
      
      // Test basic Material components
      await expect(page.locator('mat-toolbar')).toBeVisible();
      await expect(page.locator('mat-sidenav')).toBeVisible();
      await expect(page.locator('mat-table')).toBeVisible();
      
      // Test interactions
      await page.locator('button[data-test="menu-toggle"]').click();
      await expect(page.locator('mat-sidenav')).toHaveClass(/mat-drawer-opened/);
      
      // Test form interactions
      await page.locator('input[data-test="search-input"]').fill('test');
      await expect(page.locator('input[data-test="search-input"]')).toHaveValue('test');
      
      // Test theme switching
      await page.locator('button[data-test="theme-toggle"]').click();
      await expect(page.locator('body')).toHaveClass(/dark-theme|light-theme/);
    });

    test('CSS Grid and Flexbox layouts work correctly', async ({ page }) => {
      await page.goto('/layout-test');
      
      const gridContainer = page.locator('.grid-container');
      const flexContainer = page.locator('.flex-container');
      
      // Verify grid layout
      const gridComputedStyle = await gridContainer.evaluate((el) => {
        return window.getComputedStyle(el).display;
      });
      expect(gridComputedStyle).toBe('grid');
      
      // Verify flex layout
      const flexComputedStyle = await flexContainer.evaluate((el) => {
        return window.getComputedStyle(el).display;
      });
      expect(flexComputedStyle).toBe('flex');
    });

    test('Material animations work smoothly', async ({ page }) => {
      await page.goto('/animations-test');
      
      // Test slide animation
      const slideElement = page.locator('[data-test="slide-element"]');
      await page.locator('button[data-test="trigger-slide"]').click();
      
      // Verify animation completed
      await expect(slideElement).toHaveClass(/slide-in-complete/);
      
      // Test fade animation
      const fadeElement = page.locator('[data-test="fade-element"]');
      await page.locator('button[data-test="trigger-fade"]').click();
      
      await expect(fadeElement).toHaveClass(/fade-in-complete/);
    });
  });
});
```

## 📱 **Mobile Testing**

### **Mobile-Specific User Flows**

```typescript
// e2e/mobile/mobile-flows.spec.ts
import { test, expect, devices } from '@playwright/test';

test.describe('Mobile User Flows', () => {
  test.use(devices['iPhone 12']);

  test('mobile navigation works correctly', async ({ page }) => {
    await page.goto('/dashboard');
    
    // Verify mobile layout
    const toolbar = page.locator('mat-toolbar');
    await expect(toolbar).toBeVisible();
    
    // Menu should be hidden on mobile
    const sidenav = page.locator('mat-sidenav');
    await expect(sidenav).not.toBeVisible();
    
    // Open mobile menu
    await page.locator('button[data-test="menu-toggle"]').click();
    await expect(sidenav).toBeVisible();
    
    // Navigate using mobile menu
    await page.locator('a[data-test="nav-users"]').click();
    await expect(page).toHaveURL('/users');
    
    // Menu should close after navigation
    await expect(sidenav).not.toBeVisible();
  });

  test('touch interactions work correctly', async ({ page }) => {
    await page.goto('/products');
    
    // Test touch scroll
    await page.touchscreen.tap(200, 300);
    await page.evaluate(() => {
      window.scrollTo(0, 500);
    });
    
    // Test swipe gesture on cards
    const productCard = page.locator('mat-card').first();
    const box = await productCard.boundingBox();
    
    // Swipe left on card
    await page.touchscreen.tap(box.x + box.width / 2, box.y + box.height / 2);
    await page.touchscreen.tap(box.x + 50, box.y + box.height / 2);
    
    // Verify swipe action triggered
    await expect(page.locator('.swipe-actions')).toBeVisible();
  });

  test('mobile form interactions', async ({ page }) => {
    await page.goto('/users/new');
    
    // Test mobile keyboard interactions
    const nameInput = page.locator('input[data-test="name-input"]');
    await nameInput.tap();
    
    // Verify keyboard appeared (viewport height changes)
    const initialHeight = await page.evaluate(() => window.innerHeight);
    await page.waitForTimeout(500); // Wait for keyboard
    const keyboardHeight = await page.evaluate(() => window.innerHeight);
    
    expect(keyboardHeight).toBeLessThan(initialHeight);
    
    // Fill form on mobile
    await nameInput.fill('Mobile User');
    
    // Test date picker on mobile
    const datePicker = page.locator('input[data-test="birthdate-picker"]');
    await datePicker.tap();
    
    // Mobile date picker should appear
    await expect(page.locator('mat-datepicker-content')).toBeVisible();
    
    // Select date
    await page.locator('.mat-calendar-body-cell').first().tap();
    
    // Test select on mobile
    const roleSelect = page.locator('mat-select[data-test="role-select"]');
    await roleSelect.tap();
    
    // Mobile select overlay
    await expect(page.locator('mat-select-panel')).toBeVisible();
    await page.locator('mat-option').first().tap();
  });

  test('mobile table interactions', async ({ page }) => {
    await page.goto('/dashboard');
    
    // Verify mobile table layout
    const table = page.locator('mat-table');
    await expect(table).toBeVisible();
    
    // Test horizontal scroll on mobile
    await table.evaluate((el) => {
      el.scrollLeft = 100;
    });
    
    // Test mobile table actions
    const row = page.locator('mat-row').first();
    await row.tap();
    
    // Mobile action sheet should appear
    await expect(page.locator('.mobile-action-sheet')).toBeVisible();
    
    // Test action selection
    await page.locator('button[data-test="edit-action"]').tap();
    await expect(page).toHaveURL(/\/edit/);
  });
});
```

## 🔄 **CI/CD Integration**

### **GitHub Actions E2E Pipeline**

```yaml
# .github/workflows/e2e-tests.yml
name: E2E Tests

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  e2e-tests:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        browser: [chromium, firefox, webkit]
        shard: [1, 2, 3, 4]
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Build application
        run: npm run build:prod
      
      - name: Install Playwright
        run: npx playwright install --with-deps ${{ matrix.browser }}
      
      - name: Start application
        run: |
          npm run start:prod &
          npx wait-on http://localhost:4200
      
      - name: Run E2E tests
        run: npx playwright test --shard=${{ matrix.shard }}/4 --project=${{ matrix.browser }}
        env:
          CI: true
      
      - name: Upload test results
        uses: actions/upload-artifact@v3
        if: always()
        with:
          name: playwright-results-${{ matrix.browser }}-${{ matrix.shard }}
          path: |
            test-results/
            playwright-report/
          retention-days: 7
      
      - name: Upload screenshots
        uses: actions/upload-artifact@v3
        if: failure()
        with:
          name: screenshots-${{ matrix.browser }}-${{ matrix.shard }}
          path: test-results/
          retention-days: 7

  visual-regression:
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
      
      - name: Build application
        run: npm run build:prod
      
      - name: Install Playwright
        run: npx playwright install --with-deps chromium
      
      - name: Start application
        run: |
          npm run start:prod &
          npx wait-on http://localhost:4200
      
      - name: Run visual regression tests
        run: npx playwright test visual/ --project=chromium
      
      - name: Upload visual diff results
        uses: actions/upload-artifact@v3
        if: failure()
        with:
          name: visual-diff-results
          path: test-results/
          retention-days: 30

  mobile-testing:
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
      
      - name: Build application
        run: npm run build:prod
      
      - name: Install Playwright
        run: npx playwright install --with-deps
      
      - name: Start application
        run: |
          npm run start:prod &
          npx wait-on http://localhost:4200
      
      - name: Run mobile tests
        run: npx playwright test mobile/ --project="Mobile Chrome" --project="Mobile Safari"
      
      - name: Upload mobile test results
        uses: actions/upload-artifact@v3
        if: always()
        with:
          name: mobile-test-results
          path: |
            test-results/
            playwright-report/
          retention-days: 7
```

## 📊 **E2E Testing Checklist**

### **Setup Checklist**
- [ ] Test framework configured (Cypress/Playwright)
- [ ] Page Object Models implemented
- [ ] Custom commands/utilities created
- [ ] CI/CD pipeline configured
- [ ] Test data management setup

### **Test Coverage Checklist**
- [ ] Critical user journeys tested
- [ ] Form validation scenarios covered
- [ ] Error handling tested
- [ ] Cross-browser compatibility verified
- [ ] Mobile responsiveness tested
- [ ] Performance metrics validated
- [ ] Visual regression tests implemented
- [ ] Accessibility testing included

### **Quality Assurance Checklist**
- [ ] Tests are deterministic and reliable
- [ ] Test data is properly managed
- [ ] Screenshots captured on failures
- [ ] Test reports generated
- [ ] Performance budgets enforced
- [ ] Memory leak detection active

---

## 🎯 **Next Steps**

1. **Implement E2E Test Suites** - Set up comprehensive test suites for your projects
2. **Add Visual Testing** - Integrate visual regression testing pipeline
3. **Performance Monitoring** - Set up continuous performance monitoring
4. **Mobile Testing** - Expand mobile testing coverage
5. **Cross-Browser CI** - Set up automated cross-browser testing

This E2E testing strategy provides comprehensive coverage for Angular Material applications, ensuring reliability, performance, and user experience consistency across all supported platforms and browsers.
