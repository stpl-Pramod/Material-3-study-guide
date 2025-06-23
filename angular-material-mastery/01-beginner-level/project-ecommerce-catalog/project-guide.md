# E-commerce Catalog - Project Implementation Guide

## Phase 1: Project Setup & Foundation (2-3 hours)

### Step 1: Create New Angular Project

```bash
ng new ecommerce-catalog --routing --style=scss --skip-git
cd ecommerce-catalog
```

### Step 2: Install Dependencies

```bash
ng add @angular/material
npm install @angular/cdk @angular/animations
npm install rxjs
```

### Step 3: Configure Angular Material

Update `src/app/app.config.ts`:
```typescript
import { ApplicationConfig, importProvidersFrom } from '@angular/core';
import { provideRouter } from '@angular/router';
import { provideAnimationsAsync } from '@angular/platform-browser/animations/async';
import { BrowserAnimationsModule } from '@angular/platform-browser/animations';

import { routes } from './app.routes';

export const appConfig: ApplicationConfig = {
  providers: [
    provideRouter(routes),
    provideAnimationsAsync(),
    importProvidersFrom(BrowserAnimationsModule)
  ]
};
```

### Step 4: Set Up Data Models

Create `src/app/core/models/product.model.ts`:
```typescript
export interface Product {
  id: string;
  name: string;
  description: string;
  price: number;
  originalPrice?: number;
  imageUrl: string;
  images: string[];
  category: string;
  categories: string[];
  brand: string;
  rating: number;
  reviewCount: number;
  inStock: boolean;
  stockQuantity: number;
  isOnSale: boolean;
  isNew: boolean;
  isFeatured: boolean;
  tags: string[];
  specifications: ProductSpecification[];
  createdAt: Date;
  updatedAt: Date;
}

export interface ProductSpecification {
  key: string;
  value: string;
  category: string;
}

export interface Category {
  id: string;
  name: string;
  slug: string;
  description: string;
  imageUrl: string;
  parentId?: string;
  children?: Category[];
  productCount: number;
}

export interface Brand {
  id: string;
  name: string;
  logo: string;
  description: string;
  productCount: number;
}
```

Create `src/app/core/models/filter.model.ts`:
```typescript
export interface ProductFilter {
  search?: string;
  categories: string[];
  brands: string[];
  minPrice: number;
  maxPrice: number;
  rating?: number;
  inStock?: boolean;
  isOnSale?: boolean;
  isNew?: boolean;
  tags: string[];
}

export interface SortOption {
  key: string;
  label: string;
  direction: 'asc' | 'desc';
}

export interface PaginationConfig {
  page: number;
  pageSize: number;
  total: number;
}

export interface FilterOption {
  id: string;
  label: string;
  count: number;
  selected: boolean;
}
```

Create `src/app/core/models/cart.model.ts`:
```typescript
export interface CartItem {
  id: string;
  product: Product;
  quantity: number;
  selectedOptions?: CartItemOption[];
  addedAt: Date;
}

export interface CartItemOption {
  key: string;
  value: string;
  priceModifier: number;
}

export interface Cart {
  id: string;
  items: CartItem[];
  subtotal: number;
  tax: number;
  shipping: number;
  total: number;
  currency: string;
  createdAt: Date;
  updatedAt: Date;
}
```

### Step 5: Create Mock Data Service

Create `src/app/core/services/product-data.service.ts`:
```typescript
import { Injectable } from '@angular/core';
import { Observable, of, delay } from 'rxjs';
import { Product, Category, Brand } from '../models/product.model';

@Injectable({
  providedIn: 'root'
})
export class ProductDataService {
  private mockProducts: Product[] = [
    {
      id: '1',
      name: 'Premium Wireless Headphones',
      description: 'High-quality wireless headphones with noise cancellation and premium sound quality. Perfect for music lovers and professionals.',
      price: 299.99,
      originalPrice: 399.99,
      imageUrl: 'https://images.unsplash.com/photo-1505740420928-5e560c06d30e?w=400',
      images: [
        'https://images.unsplash.com/photo-1505740420928-5e560c06d30e?w=400',
        'https://images.unsplash.com/photo-1484704849700-f032a568e944?w=400'
      ],
      category: 'Electronics',
      categories: ['Electronics', 'Audio', 'Headphones'],
      brand: 'AudioTech',
      rating: 4.5,
      reviewCount: 128,
      inStock: true,
      stockQuantity: 25,
      isOnSale: true,
      isNew: false,
      isFeatured: true,
      tags: ['wireless', 'noise-cancellation', 'premium'],
      specifications: [
        { key: 'Battery Life', value: '30 hours', category: 'Power' },
        { key: 'Wireless Range', value: '10 meters', category: 'Connectivity' },
        { key: 'Weight', value: '250g', category: 'Physical' }
      ],
      createdAt: new Date('2024-01-01'),
      updatedAt: new Date('2024-01-15')
    },
    {
      id: '2',
      name: 'Smart Fitness Watch',
      description: 'Advanced fitness tracking watch with heart rate monitoring, GPS, and smart notifications. Track your health and stay connected.',
      price: 249.99,
      imageUrl: 'https://images.unsplash.com/photo-1523275335684-37898b6baf30?w=400',
      images: [
        'https://images.unsplash.com/photo-1523275335684-37898b6baf30?w=400',
        'https://images.unsplash.com/photo-1434493789847-2f02dc6ca35d?w=400'
      ],
      category: 'Electronics',
      categories: ['Electronics', 'Wearables', 'Fitness'],
      brand: 'FitTech',
      rating: 4.2,
      reviewCount: 89,
      inStock: true,
      stockQuantity: 15,
      isOnSale: false,
      isNew: true,
      isFeatured: false,
      tags: ['fitness', 'smart', 'health'],
      specifications: [
        { key: 'Display', value: '1.4" AMOLED', category: 'Display' },
        { key: 'Battery Life', value: '7 days', category: 'Power' },
        { key: 'Water Resistance', value: '50m', category: 'Durability' }
      ],
      createdAt: new Date('2024-02-01'),
      updatedAt: new Date('2024-02-01')
    }
    // Add more mock products as needed
  ];

  private mockCategories: Category[] = [
    {
      id: '1',
      name: 'Electronics',
      slug: 'electronics',
      description: 'Latest electronic devices and gadgets',
      imageUrl: 'https://images.unsplash.com/photo-1498049794561-7780e7231661?w=400',
      productCount: 150
    },
    {
      id: '2',
      name: 'Clothing',
      slug: 'clothing',
      description: 'Fashion and apparel for all occasions',
      imageUrl: 'https://images.unsplash.com/photo-1441986300917-64674bd600d8?w=400',
      productCount: 89
    }
  ];

  private mockBrands: Brand[] = [
    {
      id: '1',
      name: 'AudioTech',
      logo: '',
      description: 'Premium audio equipment manufacturer',
      productCount: 25
    },
    {
      id: '2',
      name: 'FitTech',
      logo: '',
      description: 'Innovative fitness technology',
      productCount: 18
    }
  ];

  getProducts(): Observable<Product[]> {
    return of(this.mockProducts).pipe(delay(500));
  }

  getProduct(id: string): Observable<Product | undefined> {
    const product = this.mockProducts.find(p => p.id === id);
    return of(product).pipe(delay(300));
  }

  getCategories(): Observable<Category[]> {
    return of(this.mockCategories).pipe(delay(200));
  }

  getBrands(): Observable<Brand[]> {
    return of(this.mockBrands).pipe(delay(200));
  }

  searchProducts(query: string): Observable<Product[]> {
    const filtered = this.mockProducts.filter(product =>
      product.name.toLowerCase().includes(query.toLowerCase()) ||
      product.description.toLowerCase().includes(query.toLowerCase()) ||
      product.tags.some(tag => tag.toLowerCase().includes(query.toLowerCase()))
    );
    return of(filtered).pipe(delay(300));
  }
}
```

### Step 6: Set Up Basic Theming

Create `src/assets/themes/ecommerce-theme.scss`:
```scss
@use '@angular/material' as mat;

// Define custom color palettes
$primary: mat.define-palette((
  50: #e3f2fd,
  100: #bbdefb,
  200: #90caf9,
  300: #64b5f6,
  400: #42a5f5,
  500: #2196f3,
  600: #1e88e5,
  700: #1976d2,
  800: #1565c0,
  900: #0d47a1,
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
));

$accent: mat.define-palette((
  50: #fff3e0,
  100: #ffe0b2,
  200: #ffcc80,
  300: #ffb74d,
  400: #ffa726,
  500: #ff9800,
  600: #fb8c00,
  700: #f57f17,
  800: #ef6c00,
  900: #e65100,
  contrast: (
    50: rgba(black, 0.87),
    100: rgba(black, 0.87),
    200: rgba(black, 0.87),
    300: rgba(black, 0.87),
    400: rgba(black, 0.87),
    500: rgba(black, 0.87),
    600: rgba(black, 0.87),
    700: white,
    800: white,
    900: white,
  )
));

$warn: mat.define-palette(mat.$red-palette);

// Create theme
$theme: mat.define-light-theme((
  color: (
    primary: $primary,
    accent: $accent,
    warn: $warn,
  ),
  typography: mat.define-typography-config(
    $font-family: 'Inter, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif',
    $headline-1: mat.define-typography-level(2.5rem, 3rem, 300),
    $headline-2: mat.define-typography-level(2rem, 2.5rem, 400),
    $headline-3: mat.define-typography-level(1.75rem, 2.25rem, 500),
    $headline-4: mat.define-typography-level(1.5rem, 2rem, 500),
    $headline-5: mat.define-typography-level(1.25rem, 1.75rem, 600),
    $headline-6: mat.define-typography-level(1.125rem, 1.5rem, 600),
    $body-1: mat.define-typography-level(1rem, 1.5rem, 400),
    $body-2: mat.define-typography-level(0.875rem, 1.25rem, 400),
  ),
  density: 0
));

@include mat.all-component-themes($theme);

// Custom component overrides
.mat-mdc-card {
  border-radius: 12px !important;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1) !important;
  
  &:hover {
    box-shadow: 0 4px 16px rgba(0, 0, 0, 0.15) !important;
  }
}

.mat-mdc-fab {
  border-radius: 16px !important;
}

.mat-mdc-raised-button {
  border-radius: 8px !important;
  text-transform: none !important;
  font-weight: 500 !important;
}
```

## Phase 2: Core Layout & Navigation (3-4 hours)

### Step 1: Create Main Layout Structure

Create `src/app/layout/main-layout/main-layout.component.ts`:
```typescript
import { Component, ViewChild, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { RouterOutlet } from '@angular/router';
import { MatSidenav, MatSidenavModule } from '@angular/material/sidenav';
import { MatToolbarModule } from '@angular/material/toolbar';
import { MatButtonModule } from '@angular/material/button';
import { MatIconModule } from '@angular/material/icon';
import { MatBadgeModule } from '@angular/material/badge';
import { BreakpointObserver, Breakpoints } from '@angular/cdk/layout';
import { Observable } from 'rxjs';
import { map, shareReplay } from 'rxjs/operators';

@Component({
  selector: 'app-main-layout',
  standalone: true,
  imports: [
    CommonModule,
    RouterOutlet,
    MatSidenavModule,
    MatToolbarModule,
    MatButtonModule,
    MatIconModule,
    MatBadgeModule
  ],
  template: `
    <mat-sidenav-container class="sidenav-container">
      <mat-sidenav 
        #drawer 
        class="sidenav" 
        fixedInViewport
        [attr.role]="(isHandset$ | async) ? 'dialog' : 'navigation'"
        [mode]="(isHandset$ | async) ? 'over' : 'side'"
        [opened]="(isHandset$ | async) === false">
        
        <mat-toolbar class="sidenav-header">
          <span>Categories</span>
        </mat-toolbar>
        
        <div class="sidenav-content">
          <!-- Category navigation will go here -->
          <nav class="category-nav">
            <a mat-button class="nav-link" routerLink="/catalog">
              <mat-icon>shopping_basket</mat-icon>
              All Products
            </a>
            <a mat-button class="nav-link" routerLink="/catalog/electronics">
              <mat-icon>devices</mat-icon>
              Electronics
            </a>
            <a mat-button class="nav-link" routerLink="/catalog/clothing">
              <mat-icon>checkroom</mat-icon>
              Clothing
            </a>
            <a mat-button class="nav-link" routerLink="/catalog/home">
              <mat-icon>home</mat-icon>
              Home & Garden
            </a>
          </nav>
        </div>
      </mat-sidenav>
      
      <mat-sidenav-content>
        <mat-toolbar color="primary" class="main-toolbar">
          <button
            type="button"
            aria-label="Toggle sidenav"
            mat-icon-button
            (click)="drawer.toggle()"
            *ngIf="isHandset$ | async">
            <mat-icon aria-label="Side nav toggle icon">menu</mat-icon>
          </button>
          
          <span class="app-title">E-Commerce Store</span>
          
          <div class="toolbar-spacer"></div>
          
          <button mat-icon-button routerLink="/search" aria-label="Search">
            <mat-icon>search</mat-icon>
          </button>
          
          <button mat-icon-button routerLink="/wishlist" aria-label="Wishlist">
            <mat-icon matBadge="3" matBadgeColor="accent">favorite</mat-icon>
          </button>
          
          <button mat-icon-button routerLink="/cart" aria-label="Shopping Cart">
            <mat-icon matBadge="2" matBadgeColor="accent">shopping_cart</mat-icon>
          </button>
          
          <button mat-icon-button [matMenuTriggerFor]="menu" aria-label="User menu">
            <mat-icon>account_circle</mat-icon>
          </button>
        </mat-toolbar>
        
        <main class="main-content">
          <router-outlet></router-outlet>
        </main>
      </mat-sidenav-content>
    </mat-sidenav-container>
  `,
  styles: [`
    .sidenav-container {
      height: 100vh;
    }
    
    .sidenav {
      width: 280px;
      background: var(--mat-sidenav-container-background-color);
    }
    
    .sidenav-header {
      background: mat.get-color-from-palette($primary, 50);
      color: mat.get-color-from-palette($primary, 700);
    }
    
    .sidenav-content {
      padding: 1rem 0;
    }
    
    .category-nav {
      display: flex;
      flex-direction: column;
      gap: 0.5rem;
      padding: 0 1rem;
    }
    
    .nav-link {
      justify-content: flex-start;
      gap: 1rem;
      padding: 0.75rem 1rem;
      border-radius: 8px;
      
      mat-icon {
        margin-right: 0.5rem;
      }
    }
    
    .main-toolbar {
      position: sticky;
      top: 0;
      z-index: 2;
      box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    }
    
    .app-title {
      font-size: 1.25rem;
      font-weight: 600;
    }
    
    .toolbar-spacer {
      flex: 1 1 auto;
    }
    
    .main-content {
      min-height: calc(100vh - 64px);
      background: #fafafa;
    }
    
    @media (max-width: 768px) {
      .sidenav {
        width: 100%;
      }
    }
  `]
})
export class MainLayoutComponent implements OnInit {
  @ViewChild('drawer') drawer!: MatSidenav;
  
  isHandset$: Observable<boolean> = this.breakpointObserver.observe(Breakpoints.Handset)
    .pipe(
      map(result => result.matches),
      shareReplay()
    );

  constructor(private breakpointObserver: BreakpointObserver) {}

  ngOnInit() {
    // Additional initialization logic
  }
}
```

### Step 2: Update App Routes

Update `src/app/app.routes.ts`:
```typescript
import { Routes } from '@angular/router';
import { MainLayoutComponent } from './layout/main-layout/main-layout.component';

export const routes: Routes = [
  {
    path: '',
    component: MainLayoutComponent,
    children: [
      {
        path: '',
        redirectTo: 'catalog',
        pathMatch: 'full'
      },
      {
        path: 'catalog',
        loadChildren: () => import('./features/catalog/catalog.routes').then(m => m.catalogRoutes)
      },
      {
        path: 'cart',
        loadChildren: () => import('./features/cart/cart.routes').then(m => m.cartRoutes)
      }
    ]
  }
];
```

### Step 3: Create Catalog Routes

Create `src/app/features/catalog/catalog.routes.ts`:
```typescript
import { Routes } from '@angular/router';

export const catalogRoutes: Routes = [
  {
    path: '',
    loadComponent: () => import('./pages/catalog-page/catalog-page.component').then(c => c.CatalogPageComponent)
  },
  {
    path: 'product/:id',
    loadComponent: () => import('./pages/product-detail/product-detail.component').then(c => c.ProductDetailComponent)
  },
  {
    path: ':category',
    loadComponent: () => import('./pages/catalog-page/catalog-page.component').then(c => c.CatalogPageComponent)
  }
];
```

**Validation Checkpoint:**
- [ ] Project structure is set up correctly
- [ ] Angular Material is properly configured
- [ ] Data models are defined and type-safe
- [ ] Mock data service provides realistic data
- [ ] Basic theming is applied
- [ ] Main layout renders correctly
- [ ] Navigation works on desktop and mobile
- [ ] Routes are properly configured

Continue to Phase 3 once all validation points are complete.

---

*This guide continues with the remaining phases covering product display, filtering, cart integration, and optimization. Each phase builds upon the previous work and includes detailed validation steps.*
