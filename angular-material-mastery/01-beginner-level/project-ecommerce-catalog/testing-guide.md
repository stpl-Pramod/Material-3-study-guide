# E-commerce Catalog - Testing Guide

## Testing Strategy Overview

This testing guide covers comprehensive testing approaches for the e-commerce catalog project, including unit tests, integration tests, and end-to-end testing scenarios.

## Unit Testing

### Component Testing

#### Product Card Component Tests
```typescript
// src/app/shared/components/product-card/product-card.component.spec.ts
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { By } from '@angular/platform-browser';
import { MatCardModule } from '@angular/material/card';
import { MatButtonModule } from '@angular/material/button';
import { MatIconModule } from '@angular/material/icon';
import { ProductCardComponent } from './product-card.component';
import { Product } from '../../../core/models/product.model';

describe('ProductCardComponent', () => {
  let component: ProductCardComponent;
  let fixture: ComponentFixture<ProductCardComponent>;
  
  const mockProduct: Product = {
    id: '1',
    name: 'Test Product',
    description: 'Test Description',
    price: 99.99,
    originalPrice: 129.99,
    imageUrl: 'test-image.jpg',
    images: ['test-image.jpg'],
    category: 'Electronics',
    categories: ['Electronics'],
    brand: 'TestBrand',
    rating: 4.5,
    reviewCount: 10,
    inStock: true,
    stockQuantity: 5,
    isOnSale: true,
    isNew: false,
    isFeatured: false,
    tags: ['test'],
    specifications: [],
    createdAt: new Date(),
    updatedAt: new Date()
  };

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      imports: [
        ProductCardComponent,
        MatCardModule,
        MatButtonModule,
        MatIconModule
      ]
    }).compileComponents();

    fixture = TestBed.createComponent(ProductCardComponent);
    component = fixture.componentInstance;
    component.product = mockProduct;
    fixture.detectChanges();
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });

  it('should display product information correctly', () => {
    const productName = fixture.debugElement.query(By.css('.product-name'));
    const productPrice = fixture.debugElement.query(By.css('.current-price'));
    
    expect(productName.nativeElement.textContent).toBe('Test Product');
    expect(productPrice.nativeElement.textContent).toContain('99.99');
  });

  it('should show sale badge when product is on sale', () => {
    const saleBadge = fixture.debugElement.query(By.css('mat-chip[color="accent"]'));
    expect(saleBadge.nativeElement.textContent.trim()).toBe('Sale');
  });

  it('should emit addToCart event when add to cart button is clicked', () => {
    spyOn(component.addToCart, 'emit');
    
    const addToCartButton = fixture.debugElement.query(
      By.css('button[aria-label="Add to Cart"]')
    );
    addToCartButton.nativeElement.click();
    
    expect(component.addToCart.emit).toHaveBeenCalledWith(mockProduct);
  });

  it('should toggle wishlist when heart icon is clicked', () => {
    spyOn(component.toggleWishlist, 'emit');
    
    const wishlistButton = fixture.debugElement.query(
      By.css('button[aria-label="Toggle Wishlist"]')
    );
    wishlistButton.nativeElement.click();
    
    expect(component.toggleWishlist.emit).toHaveBeenCalledWith(mockProduct);
  });

  it('should handle missing product image gracefully', () => {
    component.product = { ...mockProduct, imageUrl: '' };
    fixture.detectChanges();
    
    const productImage = fixture.debugElement.query(By.css('.product-image img'));
    expect(productImage.nativeElement.src).toContain('placeholder');
  });
});
```

#### Filter Panel Component Tests
```typescript
// src/app/shared/components/filter-panel/filter-panel.component.spec.ts
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { ReactiveFormsModule } from '@angular/forms';
import { MatExpansionModule } from '@angular/material/expansion';
import { MatSliderModule } from '@angular/material/slider';
import { MatCheckboxModule } from '@angular/material/checkbox';
import { FilterPanelComponent } from './filter-panel.component';
import { BrowserAnimationsModule } from '@angular/platform-browser/animations';

describe('FilterPanelComponent', () => {
  let component: FilterPanelComponent;
  let fixture: ComponentFixture<FilterPanelComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      imports: [
        FilterPanelComponent,
        ReactiveFormsModule,
        MatExpansionModule,
        MatSliderModule,
        MatCheckboxModule,
        BrowserAnimationsModule
      ]
    }).compileComponents();

    fixture = TestBed.createComponent(FilterPanelComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it('should initialize with default filter values', () => {
    expect(component.filterForm.get('minPrice')?.value).toBe(0);
    expect(component.filterForm.get('maxPrice')?.value).toBe(1000);
    expect(component.filterForm.get('categories')?.value).toEqual([]);
  });

  it('should emit filter changes when form values change', (done) => {
    component.filtersChanged.subscribe(filters => {
      expect(filters.minPrice).toBe(50);
      done();
    });

    component.filterForm.patchValue({ minPrice: 50 });
  });

  it('should clear all filters when clearFilters is called', () => {
    component.filterForm.patchValue({
      minPrice: 100,
      maxPrice: 500,
      categories: ['electronics']
    });

    component.clearFilters();

    expect(component.filterForm.get('minPrice')?.value).toBe(0);
    expect(component.filterForm.get('maxPrice')?.value).toBe(1000);
    expect(component.filterForm.get('categories')?.value).toEqual([]);
  });
});
```

### Service Testing

#### Product Service Tests
```typescript
// src/app/core/services/product.service.spec.ts
import { TestBed } from '@angular/core/testing';
import { HttpClientTestingModule, HttpTestingController } from '@angular/common/http/testing';
import { ProductService } from './product.service';
import { Product } from '../models/product.model';

describe('ProductService', () => {
  let service: ProductService;
  let httpMock: HttpTestingController;

  beforeEach(() => {
    TestBed.configureTestingModule({
      imports: [HttpClientTestingModule],
      providers: [ProductService]
    });
    
    service = TestBed.inject(ProductService);
    httpMock = TestBed.inject(HttpTestingController);
  });

  afterEach(() => {
    httpMock.verify();
  });

  it('should fetch products successfully', () => {
    const mockProducts: Product[] = [
      // Mock product data
    ];

    service.getProducts().subscribe(products => {
      expect(products).toEqual(mockProducts);
    });

    const req = httpMock.expectOne('/api/products');
    expect(req.request.method).toBe('GET');
    req.flush(mockProducts);
  });

  it('should filter products by search term', () => {
    const searchTerm = 'laptop';
    const mockProducts: Product[] = [
      // Mock filtered products
    ];

    service.searchProducts(searchTerm).subscribe(products => {
      expect(products.length).toBeGreaterThan(0);
      expect(products.every(p => 
        p.name.toLowerCase().includes(searchTerm) ||
        p.description.toLowerCase().includes(searchTerm)
      )).toBeTruthy();
    });

    const req = httpMock.expectOne(`/api/products/search?q=${searchTerm}`);
    req.flush(mockProducts);
  });
});
```

#### Cart Service Tests
```typescript
// src/app/core/services/cart.service.spec.ts
import { TestBed } from '@angular/core/testing';
import { CartService } from './cart.service';
import { Product } from '../models/product.model';

describe('CartService', () => {
  let service: CartService;
  
  const mockProduct: Product = {
    id: '1',
    name: 'Test Product',
    price: 99.99,
    // ... other required properties
  };

  beforeEach(() => {
    TestBed.configureTestingModule({
      providers: [CartService]
    });
    service = TestBed.inject(CartService);
  });

  it('should add product to cart', () => {
    service.addToCart(mockProduct, 2);
    
    service.cartItems$.subscribe(items => {
      expect(items.length).toBe(1);
      expect(items[0].product.id).toBe('1');
      expect(items[0].quantity).toBe(2);
    });
  });

  it('should update quantity for existing product', () => {
    service.addToCart(mockProduct, 1);
    service.addToCart(mockProduct, 2);
    
    service.cartItems$.subscribe(items => {
      expect(items.length).toBe(1);
      expect(items[0].quantity).toBe(3);
    });
  });

  it('should calculate cart total correctly', () => {
    service.addToCart(mockProduct, 2);
    
    service.cartTotal$.subscribe(total => {
      expect(total).toBe(199.98);
    });
  });

  it('should remove product from cart', () => {
    service.addToCart(mockProduct, 1);
    service.removeFromCart('1');
    
    service.cartItems$.subscribe(items => {
      expect(items.length).toBe(0);
    });
  });
});
```

## Integration Testing

### Component Integration Tests

#### Catalog Page Integration Test
```typescript
// src/app/features/catalog/pages/catalog-page/catalog-page.component.integration.spec.ts
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { By } from '@angular/platform-browser';
import { of } from 'rxjs';
import { CatalogPageComponent } from './catalog-page.component';
import { ProductService } from '../../../../core/services/product.service';
import { FilterService } from '../../../../core/services/filter.service';

describe('CatalogPageComponent Integration', () => {
  let component: CatalogPageComponent;
  let fixture: ComponentFixture<CatalogPageComponent>;
  let mockProductService: jasmine.SpyObj<ProductService>;
  let mockFilterService: jasmine.SpyObj<FilterService>;

  beforeEach(async () => {
    const productServiceSpy = jasmine.createSpyObj('ProductService', ['getProducts', 'searchProducts']);
    const filterServiceSpy = jasmine.createSpyObj('FilterService', ['applyFilters']);

    await TestBed.configureTestingModule({
      imports: [CatalogPageComponent],
      providers: [
        { provide: ProductService, useValue: productServiceSpy },
        { provide: FilterService, useValue: filterServiceSpy }
      ]
    }).compileComponents();

    mockProductService = TestBed.inject(ProductService) as jasmine.SpyObj<ProductService>;
    mockFilterService = TestBed.inject(FilterService) as jasmine.SpyObj<FilterService>;
    
    fixture = TestBed.createComponent(CatalogPageComponent);
    component = fixture.componentInstance;
  });

  it('should load and display products on initialization', () => {
    const mockProducts = [
      // Mock product data
    ];
    
    mockProductService.getProducts.and.returnValue(of(mockProducts));
    
    fixture.detectChanges();
    
    const productCards = fixture.debugElement.queryAll(By.css('app-product-card'));
    expect(productCards.length).toBe(mockProducts.length);
  });

  it('should filter products when filter is applied', () => {
    const mockProducts = [
      // Mock product data
    ];
    const filteredProducts = [
      // Filtered mock data
    ];
    
    mockProductService.getProducts.and.returnValue(of(mockProducts));
    mockFilterService.applyFilters.and.returnValue(of(filteredProducts));
    
    fixture.detectChanges();
    
    // Simulate filter application
    component.onFiltersChanged({ categories: ['electronics'] });
    fixture.detectChanges();
    
    expect(mockFilterService.applyFilters).toHaveBeenCalled();
  });
});
```

## End-to-End Testing

### Cypress E2E Tests

#### Product Catalog E2E Test
```typescript
// cypress/e2e/product-catalog.cy.ts
describe('Product Catalog', () => {
  beforeEach(() => {
    cy.visit('/catalog');
  });

  it('should display products in grid view', () => {
    cy.get('[data-cy=product-grid]').should('be.visible');
    cy.get('[data-cy=product-card]').should('have.length.greaterThan', 0);
  });

  it('should switch between grid and list view', () => {
    cy.get('[data-cy=view-toggle]').click();
    cy.get('[data-cy=product-list]').should('be.visible');
    
    cy.get('[data-cy=view-toggle]').click();
    cy.get('[data-cy=product-grid]').should('be.visible');
  });

  it('should filter products by category', () => {
    cy.get('[data-cy=category-filter]').click();
    cy.get('[data-cy=category-electronics]').click();
    cy.get('[data-cy=apply-filters]').click();
    
    cy.get('[data-cy=product-card]').each(($card) => {
      cy.wrap($card).should('contain', 'Electronics');
    });
  });

  it('should search for products', () => {
    const searchTerm = 'headphones';
    
    cy.get('[data-cy=search-input]').type(searchTerm);
    cy.get('[data-cy=search-button]').click();
    
    cy.get('[data-cy=product-card]').should('have.length.greaterThan', 0);
    cy.get('[data-cy=search-results]').should('contain', searchTerm);
  });
});
```

#### Shopping Cart E2E Test
```typescript
// cypress/e2e/shopping-cart.cy.ts
describe('Shopping Cart', () => {
  beforeEach(() => {
    cy.visit('/catalog');
  });

  it('should add product to cart', () => {
    cy.get('[data-cy=product-card]').first().within(() => {
      cy.get('[data-cy=add-to-cart]').click();
    });
    
    cy.get('[data-cy=cart-badge]').should('contain', '1');
    cy.get('[data-cy=cart-notification]').should('be.visible');
  });

  it('should update cart quantity', () => {
    // Add product to cart
    cy.get('[data-cy=product-card]').first().within(() => {
      cy.get('[data-cy=add-to-cart]').click();
    });
    
    // Open cart
    cy.get('[data-cy=cart-icon]').click();
    
    // Update quantity
    cy.get('[data-cy=quantity-increase]').click();
    cy.get('[data-cy=item-quantity]').should('contain', '2');
    
    // Verify total updated
    cy.get('[data-cy=cart-total]').should('not.be.empty');
  });

  it('should remove product from cart', () => {
    // Add product to cart
    cy.get('[data-cy=product-card]').first().within(() => {
      cy.get('[data-cy=add-to-cart]').click();
    });
    
    // Open cart
    cy.get('[data-cy=cart-icon]').click();
    
    // Remove item
    cy.get('[data-cy=remove-item]').click();
    cy.get('[data-cy=confirm-remove]').click();
    
    // Verify cart is empty
    cy.get('[data-cy=empty-cart]').should('be.visible');
    cy.get('[data-cy=cart-badge]').should('contain', '0');
  });
});
```

## Performance Testing

### Lighthouse Performance Test
```typescript
// cypress/e2e/performance.cy.ts
describe('Performance Tests', () => {
  it('should meet performance benchmarks', () => {
    cy.visit('/catalog');
    
    cy.lighthouse({
      performance: 90,
      accessibility: 95,
      'best-practices': 90,
      seo: 85,
      pwa: 80
    });
  });

  it('should load products within acceptable time', () => {
    const start = Date.now();
    
    cy.visit('/catalog');
    cy.get('[data-cy=product-card]').should('be.visible');
    
    cy.then(() => {
      const loadTime = Date.now() - start;
      expect(loadTime).to.be.lessThan(3000); // 3 seconds
    });
  });
});
```

## Accessibility Testing

### Keyboard Navigation Test
```typescript
// cypress/e2e/accessibility.cy.ts
describe('Accessibility Tests', () => {
  beforeEach(() => {
    cy.visit('/catalog');
    cy.injectAxe();
  });

  it('should have no accessibility violations', () => {
    cy.checkA11y();
  });

  it('should support keyboard navigation', () => {
    // Test tab navigation
    cy.get('body').tab();
    cy.focused().should('have.attr', 'data-cy', 'search-input');
    
    cy.focused().tab();
    cy.focused().should('have.attr', 'data-cy', 'view-toggle');
    
    // Test enter key activation
    cy.focused().type('{enter}');
    cy.get('[data-cy=product-list]').should('be.visible');
  });

  it('should have proper ARIA labels', () => {
    cy.get('[data-cy=search-input]').should('have.attr', 'aria-label');
    cy.get('[data-cy=add-to-cart]').should('have.attr', 'aria-label');
    cy.get('[data-cy=product-image]').should('have.attr', 'alt');
  });
});
```

## Test Data Management

### Mock Data Factory
```typescript
// src/testing/factories/product.factory.ts
export class ProductFactory {
  static create(overrides: Partial<Product> = {}): Product {
    return {
      id: faker.datatype.uuid(),
      name: faker.commerce.productName(),
      description: faker.commerce.productDescription(),
      price: parseFloat(faker.commerce.price()),
      imageUrl: faker.image.imageUrl(400, 400, 'product'),
      images: [faker.image.imageUrl(400, 400, 'product')],
      category: faker.commerce.department(),
      categories: [faker.commerce.department()],
      brand: faker.company.name(),
      rating: faker.datatype.number({ min: 1, max: 5, precision: 0.1 }),
      reviewCount: faker.datatype.number({ min: 0, max: 1000 }),
      inStock: faker.datatype.boolean(),
      stockQuantity: faker.datatype.number({ min: 0, max: 100 }),
      isOnSale: faker.datatype.boolean(),
      isNew: faker.datatype.boolean(),
      isFeatured: faker.datatype.boolean(),
      tags: faker.random.words(3).split(' '),
      specifications: [],
      createdAt: faker.date.past(),
      updatedAt: faker.date.recent(),
      ...overrides
    };
  }

  static createMany(count: number, overrides: Partial<Product> = {}): Product[] {
    return Array.from({ length: count }, () => this.create(overrides));
  }
}
```

## Testing Best Practices

### Testing Checklist
- [ ] Unit tests cover all critical business logic
- [ ] Integration tests verify component interactions
- [ ] E2E tests cover main user journeys
- [ ] Performance tests validate load times
- [ ] Accessibility tests ensure WCAG compliance
- [ ] Tests are maintainable and readable
- [ ] Mock data is realistic and comprehensive
- [ ] Test coverage is above 80%
- [ ] Tests run in CI/CD pipeline
- [ ] Visual regression tests (optional)

### Common Testing Patterns

#### Component Testing Pattern
```typescript
describe('Component', () => {
  let component: Component;
  let fixture: ComponentFixture<Component>;

  beforeEach(() => {
    // Setup
  });

  describe('Initialization', () => {
    // Test component initialization
  });

  describe('User Interactions', () => {
    // Test user interactions
  });

  describe('Data Binding', () => {
    // Test data binding
  });

  describe('Error Handling', () => {
    // Test error scenarios
  });
});
```

#### Service Testing Pattern
```typescript
describe('Service', () => {
  let service: Service;
  let dependency: jasmine.SpyObj<Dependency>;

  beforeEach(() => {
    // Setup with mocked dependencies
  });

  describe('Happy Path', () => {
    // Test successful operations
  });

  describe('Error Scenarios', () => {
    // Test error handling
  });

  describe('Edge Cases', () => {
    // Test boundary conditions
  });
});
```

---

**Testing Success Criteria:**
- All tests pass consistently
- Code coverage > 80%
- Performance benchmarks met
- Accessibility standards complied
- User journeys validated
- Edge cases handled gracefully
