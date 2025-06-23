# E-commerce Catalog Project

> **Project 4 of 4** - Beginner Level | **Estimated Time:** 15-20 hours | **Difficulty:** ⭐⭐⭐

## Project Overview

Build a modern e-commerce product catalog using Angular Material 3, focusing on advanced layout techniques, data presentation, and interactive filtering. This project combines multiple Material components to create a production-ready shopping experience.

## Learning Objectives

By completing this project, you will master:

- **Advanced Layout Systems**: CSS Grid, Flexbox, and Material Layout components
- **Data Presentation**: Cards, Lists, Tables, and custom data displays
- **Interactive Filtering**: Search, filters, sorting, and pagination
- **State Management**: Component state, services, and data flow
- **Performance**: Virtual scrolling, lazy loading, and optimization
- **Accessibility**: ARIA labels, keyboard navigation, and screen reader support

## Technical Specifications

### Core Features
- Product catalog with grid/list view toggle
- Advanced search and filtering system
- Category navigation with breadcrumbs
- Product detail modals/pages
- Shopping cart integration
- Responsive design for all devices

### Angular Material Components Used
```typescript
// Layout & Navigation
MatSidenavModule,
MatToolbarModule,
MatButtonModule,
MatIconModule,
MatBadgeModule,

// Data Display
MatCardModule,
MatListModule,
MatTableModule,
MatPaginatorModule,
MatSortModule,

// Form Controls
MatFormFieldModule,
MatInputModule,
MatSelectModule,
MatCheckboxModule,
MatSliderModule,
MatChipsModule,

// Feedback
MatProgressSpinnerModule,
MatSnackBarModule,
MatDialogModule,
MatBottomSheetModule
```

### Technology Stack
- **Angular 17+** with standalone components
- **Angular Material 3** with custom theming
- **Angular CDK** for advanced functionality
- **RxJS** for reactive programming
- **TypeScript** for type safety
- **CSS Grid & Flexbox** for layouts

## Project Structure

```
src/
├── app/
│   ├── core/
│   │   ├── models/
│   │   │   ├── product.model.ts
│   │   │   ├── category.model.ts
│   │   │   └── cart.model.ts
│   │   ├── services/
│   │   │   ├── product.service.ts
│   │   │   ├── cart.service.ts
│   │   │   └── filter.service.ts
│   │   └── guards/
│   │       └── route.guard.ts
│   ├── shared/
│   │   ├── components/
│   │   │   ├── product-card/
│   │   │   ├── filter-panel/
│   │   │   ├── search-bar/
│   │   │   └── pagination/
│   │   ├── pipes/
│   │   │   ├── currency-format.pipe.ts
│   │   │   └── text-highlight.pipe.ts
│   │   └── directives/
│   │       └── lazy-load.directive.ts
│   ├── features/
│   │   ├── catalog/
│   │   │   ├── pages/
│   │   │   │   ├── catalog-page/
│   │   │   │   └── product-detail/
│   │   │   ├── components/
│   │   │   │   ├── product-grid/
│   │   │   │   ├── product-list/
│   │   │   │   └── category-nav/
│   │   │   └── catalog.routes.ts
│   │   └── cart/
│   │       ├── components/
│   │       │   ├── cart-sidebar/
│   │       │   └── cart-summary/
│   │       └── cart.routes.ts
│   └── layout/
│       ├── header/
│       ├── sidebar/
│       └── footer/
├── assets/
│   ├── themes/
│   │   ├── ecommerce-theme.scss
│   │   └── component-overrides.scss
│   ├── images/
│   └── data/
│       └── mock-products.json
└── styles/
    ├── variables.scss
    ├── mixins.scss
    └── responsive.scss
```

## Key Features Deep Dive

### 1. Advanced Product Display
```typescript
// Product Grid with Material Cards
@Component({
  selector: 'app-product-grid',
  template: `
    <div class="product-grid" [class.list-view]="viewMode === 'list'">
      <mat-card 
        *ngFor="let product of products; trackBy: trackByProductId"
        class="product-card"
        (click)="selectProduct(product)">
        
        <div class="product-image">
          <img [src]="product.imageUrl" [alt]="product.name" loading="lazy">
          <mat-chip-set class="product-badges">
            <mat-chip *ngIf="product.isOnSale" color="accent">Sale</mat-chip>
            <mat-chip *ngIf="product.isNew" color="primary">New</mat-chip>
          </mat-chip-set>
        </div>
        
        <mat-card-content>
          <h3 class="product-name">{{ product.name }}</h3>
          <p class="product-description">{{ product.description | slice:0:100 }}...</p>
          
          <div class="product-pricing">
            <span class="current-price">{{ product.price | currency }}</span>
            <span *ngIf="product.originalPrice" 
                  class="original-price">{{ product.originalPrice | currency }}</span>
          </div>
          
          <mat-chip-set class="product-categories">
            <mat-chip *ngFor="let category of product.categories">
              {{ category }}
            </mat-chip>
          </mat-chip-set>
        </mat-card-content>
        
        <mat-card-actions>
          <button mat-button (click)="addToCart(product); $event.stopPropagation()">
            <mat-icon>shopping_cart</mat-icon>
            Add to Cart
          </button>
          <button mat-icon-button (click)="toggleWishlist(product); $event.stopPropagation()">
            <mat-icon [class.favorited]="product.isFavorited">
              {{ product.isFavorited ? 'favorite' : 'favorite_border' }}
            </mat-icon>
          </button>
        </mat-card-actions>
      </mat-card>
    </div>
  `
})
export class ProductGridComponent { }
```

### 2. Advanced Filtering System
```typescript
// Filter Panel with Multiple Controls
@Component({
  selector: 'app-filter-panel',
  template: `
    <mat-expansion-panel-group class="filter-panel">
      
      <!-- Price Range Filter -->
      <mat-expansion-panel expanded>
        <mat-expansion-panel-header>
          <mat-panel-title>Price Range</mat-panel-title>
        </mat-expansion-panel-header>
        
        <div class="price-filter">
          <mat-slider 
            [min]="priceRange.min" 
            [max]="priceRange.max"
            [step]="10"
            [displayWith]="formatPrice">
            <input matSliderStartThumb [(ngModel)]="filters.minPrice">
            <input matSliderEndThumb [(ngModel)]="filters.maxPrice">
          </mat-slider>
          
          <div class="price-inputs">
            <mat-form-field appearance="outline">
              <mat-label>Min Price</mat-label>
              <input matInput type="number" [(ngModel)]="filters.minPrice">
            </mat-form-field>
            <mat-form-field appearance="outline">
              <mat-label>Max Price</mat-label>
              <input matInput type="number" [(ngModel)]="filters.maxPrice">
            </mat-form-field>
          </div>
        </div>
      </mat-expansion-panel>
      
      <!-- Category Filter -->
      <mat-expansion-panel>
        <mat-expansion-panel-header>
          <mat-panel-title>Categories</mat-panel-title>
        </mat-expansion-panel-header>
        
        <div class="category-filter">
          <mat-selection-list [(ngModel)]="filters.categories">
            <mat-list-option 
              *ngFor="let category of availableCategories" 
              [value]="category.id">
              {{ category.name }}
              <span class="category-count">({{ category.count }})</span>
            </mat-list-option>
          </mat-selection-list>
        </div>
      </mat-expansion-panel>
      
      <!-- Brand Filter -->
      <mat-expansion-panel>
        <mat-expansion-panel-header>
          <mat-panel-title>Brands</mat-panel-title>
        </mat-expansion-panel-header>
        
        <div class="brand-filter">
          <mat-form-field appearance="outline" class="brand-search">
            <mat-label>Search brands...</mat-label>
            <input matInput [(ngModel)]="brandSearch">
            <mat-icon matPrefix>search</mat-icon>
          </mat-form-field>
          
          <div class="brand-checkboxes">
            <mat-checkbox 
              *ngFor="let brand of filteredBrands" 
              [(ngModel)]="brand.selected"
              (change)="updateBrandFilter()">
              {{ brand.name }}
            </mat-checkbox>
          </div>
        </div>
      </mat-expansion-panel>
      
    </mat-expansion-panel-group>
    
    <div class="filter-actions">
      <button mat-button (click)="clearFilters()">Clear All</button>
      <button mat-raised-button color="primary" (click)="applyFilters()">
        Apply Filters
      </button>
    </div>
  `
})
export class FilterPanelComponent { }
```

### 3. Responsive Layout System
```scss
// Advanced responsive grid system
.product-grid {
  display: grid;
  gap: 1.5rem;
  padding: 1rem;
  
  // Responsive grid columns
  grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
  
  @media (max-width: 768px) {
    grid-template-columns: repeat(auto-fill, minmax(240px, 1fr));
    gap: 1rem;
    padding: 0.5rem;
  }
  
  @media (max-width: 480px) {
    grid-template-columns: 1fr;
    gap: 0.75rem;
  }
  
  // List view mode
  &.list-view {
    grid-template-columns: 1fr;
    
    .product-card {
      display: flex;
      align-items: center;
      
      .product-image {
        flex: 0 0 120px;
        height: 120px;
      }
      
      mat-card-content {
        flex: 1;
        padding-left: 1rem;
      }
    }
  }
}

// Advanced card styling with Material 3 principles
.product-card {
  @include mat.elevation(1);
  transition: all 0.3s ease;
  cursor: pointer;
  border-radius: 12px;
  overflow: hidden;
  
  &:hover {
    @include mat.elevation(4);
    transform: translateY(-2px);
  }
  
  .product-image {
    position: relative;
    height: 200px;
    overflow: hidden;
    
    img {
      width: 100%;
      height: 100%;
      object-fit: cover;
      transition: transform 0.3s ease;
    }
    
    &:hover img {
      transform: scale(1.05);
    }
    
    .product-badges {
      position: absolute;
      top: 0.5rem;
      right: 0.5rem;
      flex-direction: column;
      gap: 0.25rem;
    }
  }
  
  .product-pricing {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    margin: 0.5rem 0;
    
    .current-price {
      font-size: 1.25rem;
      font-weight: 600;
      color: mat.get-color-from-palette($primary, 500);
    }
    
    .original-price {
      text-decoration: line-through;
      color: mat.get-color-from-palette($foreground, secondary-text);
      font-size: 0.9rem;
    }
  }
}
```

## Implementation Phases

### Phase 1: Project Setup & Data Models (2-3 hours)
1. Create Angular project with Material 3
2. Set up routing and lazy loading
3. Define data models and interfaces
4. Create mock data service
5. Set up basic theming

### Phase 2: Core Layout & Navigation (3-4 hours)
1. Implement main layout with header/sidebar
2. Create responsive navigation
3. Add breadcrumb navigation
4. Set up routing structure
5. Implement view mode toggle

### Phase 3: Product Display System (4-5 hours)
1. Create product card component
2. Implement grid and list views
3. Add product image handling
4. Create product detail modal
5. Implement lazy loading

### Phase 4: Search & Filtering (4-5 hours)
1. Build advanced search bar
2. Create multi-criteria filter panel
3. Implement real-time filtering
4. Add sorting capabilities
5. Create pagination system

### Phase 5: Shopping Cart Integration (2-3 hours)
1. Implement cart service
2. Create cart sidebar component
3. Add cart state management
4. Implement cart persistence
5. Add cart animations

### Phase 6: Polish & Optimization (2-3 hours)
1. Performance optimization
2. Accessibility improvements
3. Advanced animations
4. Error handling
5. Testing and validation

## Success Criteria

### Technical Requirements
- [ ] Responsive design works on all screen sizes
- [ ] All Material components properly themed
- [ ] Fast search and filtering performance
- [ ] Proper error handling and loading states
- [ ] Accessibility compliance (WCAG 2.1)
- [ ] Clean, maintainable code structure

### User Experience Requirements
- [ ] Intuitive navigation and product discovery
- [ ] Smooth animations and transitions
- [ ] Fast and responsive interactions
- [ ] Clear visual hierarchy and information design
- [ ] Consistent design system throughout

### Performance Requirements
- [ ] Initial page load under 3 seconds
- [ ] Search results appear within 500ms
- [ ] Smooth scrolling and animations (60fps)
- [ ] Efficient memory usage
- [ ] Optimized bundle size

## Next Steps

After completing this project:

1. **Review & Refactor**: Clean up code and optimize performance
2. **Testing**: Add unit and integration tests
3. **Documentation**: Create comprehensive project documentation
4. **Portfolio**: Add to your development portfolio
5. **Advanced Features**: Consider adding PWA capabilities, offline support

## Resources & References

- [Angular Material 3 Documentation](https://material.angular.io/)
- [Material Design 3 Guidelines](https://m3.material.io/)
- [Angular CDK Documentation](https://material.angular.io/cdk)
- [RxJS Operators Guide](https://rxjs.dev/guide/operators)
- [Web Accessibility Guidelines](https://www.w3.org/WAI/WCAG21/quickref/)

---

**Ready to build a production-ready e-commerce catalog?** Start with the project guide and work through each phase systematically. This project will give you hands-on experience with advanced Material 3 patterns and real-world application development.
