# Project 2: Portfolio Website - Advanced Theming & Animations

## 🎯 Project Overview

Create a stunning, professional portfolio website that showcases advanced Material Design 3 theming capabilities, smooth animations, and content presentation patterns. This project builds upon the Personal Dashboard foundation to explore multi-theme variants, advanced animations, and professional content presentation.

## 📚 Learning Objectives

After completing this project, you will be able to:
- ✅ Implement multiple theme variants (light, dark, high-contrast)
- ✅ Create smooth page transitions and micro-interactions
- ✅ Build content-focused layouts with Material Design
- ✅ Optimize for SEO and social media sharing
- ✅ Implement advanced animation patterns with Angular Animations
- ✅ Create responsive image galleries and media components

## 🏗️ Project Architecture

### Application Structure
```
portfolio-website/
├── src/app/
│   ├── core/
│   │   ├── services/
│   │   │   ├── theme.service.ts           # Multi-theme management
│   │   │   ├── portfolio.service.ts       # Portfolio data
│   │   │   ├── seo.service.ts            # SEO optimization
│   │   │   └── animation.service.ts       # Animation coordination
│   │   ├── models/
│   │   │   ├── portfolio.types.ts        # Data models
│   │   │   └── theme.types.ts            # Theme definitions
│   │   └── guards/
│   │       └── animation.guard.ts        # Route animation guard
│   ├── shared/
│   │   ├── components/
│   │   │   ├── theme-switcher/           # Multi-theme selector
│   │   │   ├── image-gallery/            # Responsive gallery
│   │   │   ├── skill-meter/              # Animated skill indicators
│   │   │   ├── project-card/             # Enhanced project cards
│   │   │   └── contact-form/             # Material form components
│   │   ├── animations/
│   │   │   ├── page-transitions.ts       # Route animations
│   │   │   ├── scroll-animations.ts      # Scroll-triggered effects
│   │   │   └── micro-interactions.ts     # Button/card animations
│   │   └── pipes/
│   │       ├── safe-url.pipe.ts          # URL sanitization
│   │       └── truncate.pipe.ts          # Text truncation
│   ├── features/
│   │   ├── home/                         # Landing page
│   │   │   ├── components/
│   │   │   │   ├── hero-section/         # Animated hero
│   │   │   │   ├── about-preview/        # About snippet
│   │   │   │   └── featured-projects/    # Highlighted work
│   │   │   └── home.component.ts
│   │   ├── about/                        # About page
│   │   │   ├── components/
│   │   │   │   ├── bio-section/          # Personal info
│   │   │   │   ├── skills-section/       # Animated skills
│   │   │   │   ├── experience-timeline/  # Career timeline
│   │   │   │   └── education-section/    # Educational background
│   │   │   └── about.component.ts
│   │   ├── projects/                     # Portfolio showcase
│   │   │   ├── components/
│   │   │   │   ├── project-grid/         # Filterable grid
│   │   │   │   ├── project-detail/       # Individual project
│   │   │   │   ├── project-filter/       # Category filtering
│   │   │   │   └── technology-chips/     # Tech stack display
│   │   │   └── projects.component.ts
│   │   ├── blog/                         # Blog/articles
│   │   │   ├── components/
│   │   │   │   ├── article-list/         # Blog listing
│   │   │   │   ├── article-detail/       # Full article
│   │   │   │   └── article-preview/      # Article cards
│   │   │   └── blog.component.ts
│   │   └── contact/                      # Contact information
│   │       ├── components/
│   │       │   ├── contact-info/         # Contact details
│   │       │   ├── contact-form/         # Contact form
│   │       │   └── social-links/         # Social media
│   │       └── contact.component.ts
│   ├── layout/
│   │   ├── header/                       # Navigation header
│   │   ├── footer/                       # Site footer
│   │   └── main-layout/                  # Layout wrapper
│   └── styles/
│       ├── themes/
│       │   ├── _light-theme.scss         # Light theme
│       │   ├── _dark-theme.scss          # Dark theme
│       │   ├── _high-contrast.scss       # Accessibility theme
│       │   └── _theme-mixins.scss        # Shared theme utilities
│       ├── animations/
│       │   ├── _transitions.scss         # CSS transitions
│       │   └── _keyframes.scss           # CSS animations
│       └── components/
│           ├── _hero.scss                # Hero section styles
│           ├── _project-grid.scss        # Project showcase
│           └── _skills.scss              # Skills visualization
```

### Design System & Themes

#### Multi-Theme Architecture
```scss
// Light Theme (Professional)
$light-theme: (
  primary: #1976d2,      // Professional blue
  secondary: #424242,     // Sophisticated gray
  tertiary: #00acc1,     // Accent cyan
  surface: #ffffff,
  background: #fafafa,
  success: #4caf50,
  warning: #ff9800,
  error: #f44336
);

// Dark Theme (Creative)
$dark-theme: (
  primary: #bb86fc,      // Creative purple
  secondary: #03dac6,    // Vibrant teal
  tertiary: #cf6679,     // Accent pink
  surface: #121212,
  background: #000000,
  success: #66bb6a,
  warning: #ffb74d,
  error: #f48fb1
);

// High Contrast Theme (Accessibility)
$high-contrast-theme: (
  primary: #0000ff,      // Pure blue
  secondary: #000000,    // Pure black
  tertiary: #ff0000,     // Pure red
  surface: #ffffff,
  background: #ffffff,
  success: #008000,
  warning: #ffff00,
  error: #ff0000
);
```

#### Typography System
```scss
// Professional Typography Scale
$portfolio-typography: (
  // Hero text
  display-large: (4rem, 4.5rem, 300),      // 64px
  display-medium: (3.5rem, 4rem, 300),     // 56px
  display-small: (3rem, 3.5rem, 400),      // 48px
  
  // Section headings
  headline-large: (2.5rem, 3rem, 400),     // 40px
  headline-medium: (2rem, 2.5rem, 400),    // 32px
  headline-small: (1.75rem, 2.25rem, 400), // 28px
  
  // Content text
  title-large: (1.5rem, 2rem, 500),        // 24px
  title-medium: (1.25rem, 1.75rem, 500),   // 20px
  title-small: (1.125rem, 1.5rem, 500),    // 18px
  
  // Body text
  body-large: (1rem, 1.5rem, 400),         // 16px
  body-medium: (0.875rem, 1.25rem, 400),   // 14px
  body-small: (0.75rem, 1rem, 400),        // 12px
  
  // UI elements
  label-large: (0.875rem, 1.25rem, 500),   // 14px
  label-medium: (0.75rem, 1rem, 500),      // 12px
  label-small: (0.6875rem, 0.875rem, 500), // 11px
);
```

## 🎨 Visual Design Specifications

### Layout & Spacing
- **Container Max Width**: 1200px
- **Section Padding**: 64px (desktop) / 32px (tablet) / 16px (mobile)
- **Component Spacing**: 24px between major components
- **Grid Gap**: 16px between grid items
- **Card Padding**: 24px

### Color Psychology
- **Light Theme**: Professional, trustworthy, clean
- **Dark Theme**: Creative, modern, sophisticated
- **High Contrast**: Accessible, clear, inclusive

### Animation Specifications
- **Page Transitions**: 300ms ease-out
- **Micro-interactions**: 150ms ease-in-out
- **Scroll Animations**: 500ms ease-out with 100ms stagger
- **Hover Effects**: 200ms ease-in-out

## 🚀 Key Features Implementation

### Feature 1: Multi-Theme System
```typescript
// Advanced theme service with multiple variants
@Injectable({
  providedIn: 'root'
})
export class AdvancedThemeService {
  private _currentTheme = signal<ThemeVariant>('light');
  private _availableThemes = signal<ThemeOption[]>([
    {
      id: 'light',
      name: 'Professional Light',
      description: 'Clean and professional appearance',
      icon: 'light_mode',
      preview: '/assets/theme-previews/light.png'
    },
    {
      id: 'dark',
      name: 'Creative Dark',
      description: 'Modern dark interface for creative work',
      icon: 'dark_mode',
      preview: '/assets/theme-previews/dark.png'
    },
    {
      id: 'high-contrast',
      name: 'High Contrast',
      description: 'Enhanced accessibility with high contrast',
      icon: 'contrast',
      preview: '/assets/theme-previews/high-contrast.png'
    }
  ]);

  readonly currentTheme = this._currentTheme.asReadonly();
  readonly availableThemes = this._availableThemes.asReadonly();

  // Theme switching with animation coordination
  async switchTheme(themeId: ThemeVariant): Promise<void> {
    // Animate out current content
    await this.animateThemeTransition();
    
    // Apply new theme
    this._currentTheme.set(themeId);
    this.applyThemeToDOM(themeId);
    
    // Save preference
    localStorage.setItem('portfolio-theme', themeId);
    
    // Animate in new content
    await this.animateThemeTransition(false);
  }

  private async animateThemeTransition(out: boolean = true): Promise<void> {
    const elements = document.querySelectorAll('.theme-animated');
    const animation = out ? 'fade-out' : 'fade-in';
    
    elements.forEach((el, index) => {
      setTimeout(() => {
        el.classList.add(animation);
      }, index * 50);
    });
    
    return new Promise(resolve => setTimeout(resolve, 300));
  }
}
```

### Feature 2: Advanced Animations
```typescript
// Page transition animations
export const portfolioAnimations = {
  pageTransition: trigger('pageTransition', [
    transition('* <=> *', [
      query(':enter, :leave', style({ position: 'absolute', width: '100%' }), 
            { optional: true }),
      query(':enter', style({ transform: 'translateX(100%)', opacity: 0 }), 
            { optional: true }),
      query(':leave', animateChild(), { optional: true }),
      group([
        query(':leave', [
          animate('300ms ease-out', 
                 style({ transform: 'translateX(-100%)', opacity: 0 }))
        ], { optional: true }),
        query(':enter', [
          animate('300ms ease-out', 
                 style({ transform: 'translateX(0%)', opacity: 1 }))
        ], { optional: true }),
      ]),
    ]),
  ]),

  scrollReveal: trigger('scrollReveal', [
    state('hidden', style({
      opacity: 0,
      transform: 'translateY(50px)'
    })),
    state('visible', style({
      opacity: 1,
      transform: 'translateY(0)'
    })),
    transition('hidden => visible', [
      animate('500ms ease-out')
    ])
  ]),

  skillMeter: trigger('skillMeter', [
    state('initial', style({
      width: '0%'
    })),
    state('filled', style({
      width: '{{ percentage }}%'
    }), { params: { percentage: 0 } }),
    transition('initial => filled', [
      animate('1000ms ease-out')
    ])
  ])
};
```

### Feature 3: Responsive Image Gallery
```typescript
// Advanced image gallery component
@Component({
  selector: 'app-image-gallery',
  standalone: true,
  imports: [CommonModule, MatDialogModule, MatButtonModule, MatIconModule],
  template: `
    <div class="gallery-grid">
      @for (image of images(); track image.id) {
        <div class="gallery-item" 
             [class.featured]="image.featured"
             (click)="openLightbox(image)"
             [@scrollReveal]="isVisible ? 'visible' : 'hidden'">
          <img 
            [src]="image.thumbnail || image.url" 
            [alt]="image.alt"
            [loading]="'lazy'"
            class="gallery-image">
          <div class="gallery-overlay">
            <div class="gallery-info">
              <h4>{{ image.title }}</h4>
              <p>{{ image.description }}</p>
            </div>
            <div class="gallery-actions">
              <button mat-icon-button>
                <mat-icon>zoom_in</mat-icon>
              </button>
            </div>
          </div>
        </div>
      }
    </div>
  `,
  styles: [`
    .gallery-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
      gap: 16px;
      margin: 24px 0;
    }

    .gallery-item {
      position: relative;
      border-radius: 12px;
      overflow: hidden;
      cursor: pointer;
      transition: transform 200ms ease-out, box-shadow 200ms ease-out;
      
      &:hover {
        transform: translateY(-4px);
        box-shadow: var(--mat-sys-elevation-level3);
        
        .gallery-overlay {
          opacity: 1;
        }
      }
      
      &.featured {
        grid-column: span 2;
        grid-row: span 2;
      }
    }

    .gallery-image {
      width: 100%;
      height: 100%;
      object-fit: cover;
      transition: transform 200ms ease-out;
    }

    .gallery-overlay {
      position: absolute;
      top: 0;
      left: 0;
      right: 0;
      bottom: 0;
      background: linear-gradient(
        to bottom,
        transparent 0%,
        rgba(0, 0, 0, 0.7) 100%
      );
      opacity: 0;
      transition: opacity 200ms ease-out;
      display: flex;
      flex-direction: column;
      justify-content: flex-end;
      padding: 24px;
      color: white;
    }

    .gallery-info {
      h4 {
        margin: 0 0 8px 0;
        font-size: 1.125rem;
        font-weight: 500;
      }
      
      p {
        margin: 0;
        font-size: 0.875rem;
        opacity: 0.9;
      }
    }

    .gallery-actions {
      position: absolute;
      top: 16px;
      right: 16px;
    }

    @media (max-width: 768px) {
      .gallery-grid {
        grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
      }
      
      .gallery-item.featured {
        grid-column: span 1;
        grid-row: span 1;
      }
    }
  `],
  animations: [portfolioAnimations.scrollReveal]
})
export class ImageGalleryComponent implements OnInit {
  images = input.required<GalleryImage[]>();
  isVisible = signal(false);

  @ViewChild('galleryContainer') galleryContainer!: ElementRef;

  ngOnInit() {
    this.setupIntersectionObserver();
  }

  private setupIntersectionObserver() {
    const observer = new IntersectionObserver(
      (entries) => {
        entries.forEach(entry => {
          if (entry.isIntersecting) {
            this.isVisible.set(true);
          }
        });
      },
      { threshold: 0.1 }
    );

    if (this.galleryContainer) {
      observer.observe(this.galleryContainer.nativeElement);
    }
  }

  openLightbox(image: GalleryImage) {
    // Implement lightbox functionality
  }
}
```

## 📱 Responsive Design Strategy

### Breakpoint System
```scss
$breakpoints: (
  mobile: 599px,
  tablet: 959px,
  laptop: 1279px,
  desktop: 1919px
);

// Hero section responsive behavior
.hero-section {
  padding: 120px 0 80px;
  
  @media (max-width: #{map-get($breakpoints, tablet)}) {
    padding: 80px 0 60px;
  }
  
  @media (max-width: #{map-get($breakpoints, mobile)}) {
    padding: 60px 0 40px;
  }
}

// Project grid responsive columns
.project-grid {
  display: grid;
  gap: 24px;
  
  grid-template-columns: repeat(3, 1fr);
  
  @media (max-width: #{map-get($breakpoints, laptop)}) {
    grid-template-columns: repeat(2, 1fr);
  }
  
  @media (max-width: #{map-get($breakpoints, tablet)}) {
    grid-template-columns: 1fr;
    gap: 16px;
  }
}
```

### Mobile-First Approach
- **Touch-friendly**: 44px minimum touch targets
- **Swipe gestures**: Horizontal scrolling for project galleries
- **Progressive enhancement**: Advanced features for larger screens
- **Performance**: Optimized images and lazy loading

## 🎯 Implementation Phases

### Phase 1: Theme System Setup (2 hours)
1. **Multi-theme service implementation**
2. **Theme switcher component**
3. **CSS custom properties setup**
4. **Theme persistence**

### Phase 2: Layout & Navigation (2 hours)
1. **Responsive header with theme switcher**
2. **Footer with social links**
3. **Page routing setup**
4. **Loading states**

### Phase 3: Content Pages (3 hours)
1. **Hero section with animations**
2. **About page with timeline**
3. **Projects showcase with filtering**
4. **Contact page with form**

### Phase 4: Advanced Features (1.5 hours)
1. **Scroll-triggered animations**
2. **Image gallery with lightbox**
3. **SEO optimization**
4. **Performance optimization**

### Phase 5: Testing & Polish (1.5 hours)
1. **Cross-browser testing**
2. **Accessibility validation**
3. **Performance auditing**
4. **Final refinements**

## 🧪 Testing Strategy

### Visual Testing
```typescript
// Storybook stories for theme variants
export default {
  title: 'Pages/Portfolio',
  component: PortfolioComponent,
  parameters: {
    themes: {
      default: 'light',
      list: [
        { name: 'light', class: 'light-theme', color: '#ffffff' },
        { name: 'dark', class: 'dark-theme', color: '#121212' },
        { name: 'high-contrast', class: 'high-contrast-theme', color: '#ffffff' }
      ]
    }
  }
} as Meta;

export const LightTheme: StoryObj = {
  parameters: {
    theme: 'light'
  }
};

export const DarkTheme: StoryObj = {
  parameters: {
    theme: 'dark'
  }
};

export const HighContrast: StoryObj = {
  parameters: {
    theme: 'high-contrast'
  }
};
```

### Animation Testing
```typescript
// Animation performance testing
describe('Portfolio Animations', () => {
  it('should complete page transitions within 300ms', async () => {
    const startTime = performance.now();
    
    await router.navigate(['/about']);
    
    await waitForAnimationComplete();
    
    const endTime = performance.now();
    expect(endTime - startTime).toBeLessThan(350);
  });

  it('should not drop frames during scroll animations', async () => {
    const frameDrops = await measureFrameDrops(() => {
      scrollToElement('.skills-section');
    });
    
    expect(frameDrops).toBeLessThan(5);
  });
});
```

## 🎯 Success Criteria

### Technical Requirements
- [ ] **Multi-theme system** fully functional
- [ ] **Smooth animations** at 60fps
- [ ] **Responsive design** works on all devices
- [ ] **SEO optimized** with meta tags and structured data
- [ ] **Accessibility** meets WCAG 2.1 AA standards
- [ ] **Performance** Lighthouse score > 90

### Visual Requirements
- [ ] **Professional design** suitable for career portfolios
- [ ] **Consistent theming** across all components
- [ ] **Smooth transitions** between pages and states
- [ ] **Readable typography** with proper hierarchy
- [ ] **Optimized images** with lazy loading

### User Experience
- [ ] **Intuitive navigation** with clear structure
- [ ] **Fast loading** under 3 seconds
- [ ] **Mobile-friendly** touch interactions
- [ ] **Accessible** to users with disabilities
- [ ] **Engaging** micro-interactions and animations

## 📈 Performance Targets

- **First Contentful Paint**: < 1.2s
- **Largest Contentful Paint**: < 2.0s
- **Time to Interactive**: < 2.5s
- **Cumulative Layout Shift**: < 0.1
- **Bundle Size**: < 400KB initial load

## 📚 Learning Resources

### Animation & Design
- [Material Design Motion Guidelines](https://m3.material.io/styles/motion/overview)
- [Web Animation API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Animations_API)
- [Angular Animations Guide](https://angular.dev/guide/animations)

### Theme Development
- [Material Design 3 Color System](https://m3.material.io/styles/color/system/overview)
- [CSS Custom Properties](https://developer.mozilla.org/en-US/docs/Web/CSS/--*)
- [Accessibility Guidelines](https://www.w3.org/WAI/WCAG21/quickref/)

## ✅ Next Steps

1. Complete the [implementation guide](./project-guide.md)
2. Follow the [completion checklist](./completion-checklist.md)
3. Review [animation examples](./animation-examples.md)
4. Move to [Project 3: Blog Platform](../project-blog-platform/)

---

**This project transforms your understanding of Material Design theming and introduces professional animation patterns. Focus on creating smooth, engaging user experiences!**
