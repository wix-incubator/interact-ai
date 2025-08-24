# Configuration Structure

The `InteractConfig` object is the heart of `@wix/interact`. This guide explains how to organize and structure complex interactions efficiently.

## Basic Configuration Structure

```typescript
import type { InteractConfig } from '@wix/interact';

const config: InteractConfig = {
    effects: {
        // Reusable effect definitions
    },
    conditions: {
        // Reusable condition definitions
    },
    interactions: [
        // Array of interaction definitions
    ]
};
```

## Anatomy of an Interaction

Each interaction defines a complete cause-and-effect relationship:

```typescript
{
    source: '#trigger-element',           // What element triggers the interaction
    trigger: 'hover',                     // What user action starts it
    params: {                             // Optional trigger parameters
        type: 'alternate'
    },
    conditions: ['desktop-only'],         // Optional conditions to check
    effects: [                            // Array of effects to apply
        {
            target: '#animated-element',
            namedEffect: 'FadeIn',
            duration: 300
        }
    ]
}
```

## Organizing Simple Interactions

### Inline Effects (Small Projects)
For simple interactions, define effects directly:

```typescript
const simpleConfig: InteractConfig = {
    interactions: [
        {
            source: '#button-1',
            trigger: 'hover',
            effects: [
                {
                    target: '#button-1',
                    namedEffect: 'Scale',
                    duration: 200,
                    easing: 'ease-out'
                }
            ]
        },
        {
            source: '#button-2',
            trigger: 'click',
            effects: [
                {
                    target: '#modal',
                    keyframeEffect: {
                        opacity: ['0', '1'],
                        transform: ['scale(0.9)', 'scale(1)']
                    },
                    duration: 300
                }
            ]
        }
    ]
};
```

## Organizing Complex Interactions

### Reusable Effects
For larger projects, define effects separately and reference them:

```typescript
const complexConfig: InteractConfig = {
    effects: {
        // Reusable effect definitions
        'button-hover': {
            keyframeEffect: {
                transform: ['scale(1)', 'scale(1.05)'],
                boxShadow: ['0 2px 4px rgba(0,0,0,0.1)', '0 8px 16px rgba(0,0,0,0.15)']
            },
            duration: 200,
            easing: 'ease-out'
        },
        'card-entrance': {
            keyframeEffect: {
                opacity: ['0', '1'],
                transform: ['translateY(30px)', 'translateY(0)']
            },
            duration: 600,
            easing: 'cubic-bezier(0.16, 1, 0.3, 1)'
        },
        'modal-open': {
            keyframeEffect: {
                opacity: ['0', '1'],
                transform: ['scale(0.95)', 'scale(1)']
            },
            duration: 300,
            easing: 'ease-out'
        }
    },
    
    interactions: [
        {
            source: '.btn-primary',
            trigger: 'hover',
            effects: [
                {
                    target: '.btn-primary',
                    effectId: 'button-hover'  // Reference to reusable effect
                }
            ]
        },
        {
            source: '.product-card',
            trigger: 'viewEnter',
            params: { type: 'once', threshold: 0.3 },
            effects: [
                {
                    target: '.product-card',
                    effectId: 'card-entrance'
                }
            ]
        }
    ]
};
```

## Conditional Interactions

### Defining Conditions
Create reusable conditions for responsive design:

```typescript
const responsiveConfig: InteractConfig = {
    conditions: {
        'desktop-only': {
            type: 'media',
            predicate: '(min-width: 768px)'
        },
        'mobile-only': {
            type: 'media',
            predicate: '(max-width: 767px)'
        },
        'prefers-motion': {
            type: 'media',
            predicate: '(prefers-reduced-motion: no-preference)'
        },
        'large-container': {
            type: 'container',
            predicate: '(min-width: 600px)'
        }
    },
    
    interactions: [
        // Desktop hover effects
        {
            source: '#hero-image',
            trigger: 'hover',
            conditions: ['desktop-only', 'prefers-motion'],
            effects: [
                {
                    target: '#hero-image',
                    namedEffect: 'Scale',
                    duration: 400
                }
            ]
        },
        
        // Mobile tap effects
        {
            source: '#hero-image',
            trigger: 'click',
            conditions: ['mobile-only'],
            effects: [
                {
                    target: '#hero-image',
                    namedEffect: 'Pulse',
                    duration: 300
                }
            ]
        }
    ]
};
```

### Using Conditions in Effects
Apply conditions at the effect level:

```typescript
{
    source: '#animated-section',
    trigger: 'viewEnter',
    effects: [
        // Basic fade for all devices
        {
            target: '#animated-section',
            namedEffect: 'FadeIn',
            duration: 600
        },
        // Enhanced animation only on desktop
        {
            target: '#animated-section .particles',
            namedEffect: 'SlideUp',
            duration: 800,
            delay: 200,
            conditions: ['desktop-only', 'prefers-motion']
        }
    ]
}
```

## Modular Configuration Patterns

### Feature-Based Organization
Organize by features or components:

```typescript
// buttons.ts
export const buttonInteractions = [
    {
        source: '.btn-primary',
        trigger: 'hover',
        effects: [{ target: '.btn-primary', effectId: 'button-hover' }]
    },
    {
        source: '.btn-secondary',
        trigger: 'hover',
        effects: [{ target: '.btn-secondary', effectId: 'button-hover-secondary' }]
    }
];

// cards.ts
export const cardInteractions = [
    {
        source: '.card',
        trigger: 'viewEnter',
        params: { type: 'once' },
        effects: [{ target: '.card', effectId: 'card-entrance' }]
    }
];

// main.ts
import { buttonInteractions } from './buttons';
import { cardInteractions } from './cards';
import { commonEffects } from './effects';
import { mediaConditions } from './conditions';

const config: InteractConfig = {
    effects: commonEffects,
    conditions: mediaConditions,
    interactions: [
        ...buttonInteractions,
        ...cardInteractions
    ]
};
```

### Component-Specific Configurations
Create configurations for specific components:

```typescript
// NavbarConfig.ts
export const createNavbarConfig = (navbarId: string): InteractConfig => ({
    effects: {
        'nav-slide': {
            keyframeEffect: {
                transform: ['translateY(-100%)', 'translateY(0)']
            },
            duration: 300,
            easing: 'ease-out'
        }
    },
    interactions: [
        {
            source: `${navbarId} .nav-toggle`,
            trigger: 'click',
            effects: [
                {
                    target: `${navbarId} .nav-menu`,
                    effectId: 'nav-slide'
                }
            ]
        }
    ]
});

// Usage
const navConfig = createNavbarConfig('#main-nav');
Interact.create(navConfig);
```

## Advanced Configuration Patterns

TBD

## Performance Considerations

### Lazy Loading Configurations
Load configurations on demand:

```typescript
const loadFeatureConfig = async (featureName: string): Promise<InteractConfig> => {
    const module = await import(`./features/${featureName}/interactions.js`);
    return module.default;
};

// Usage
const heroConfig = await loadFeatureConfig('hero');
Interact.create(heroConfig);
```

## Real-World Example: E-commerce Product Page

```typescript
const productPageConfig: InteractConfig = {
    effects: {
        'product-image-zoom': {
            keyframeEffect: {
                transform: ['scale(1)', 'scale(1.1)']
            },
            duration: 300,
            easing: 'ease-out'
        },
        'add-to-cart-success': {
            keyframeEffect: {
                backgroundColor: ['#ef4444', '#10b981'],
                transform: ['scale(1)', 'scale(1.05)', 'scale(1)']
            },
            duration: 600,
            easing: 'ease-in-out'
        },
        'review-entrance': {
            keyframeEffect: {
                opacity: ['0', '1'],
                transform: ['translateY(20px)', 'translateY(0)']
            },
            duration: 500,
            easing: 'ease-out'
        }
    },
    
    conditions: {
        'desktop': {
            type: 'media',
            predicate: '(min-width: 1024px)'
        },
        'touch-device': {
            type: 'media',
            predicate: '(hover: none)'
        }
    },
    
    interactions: [
        // Product image hover (desktop only)
        {
            source: '.product-image',
            trigger: 'hover',
            conditions: ['desktop'],
            effects: [
                {
                    target: '.product-image img',
                    effectId: 'product-image-zoom'
                }
            ]
        },
        
        // Add to cart success animation
        {
            source: '#add-to-cart-btn',
            trigger: 'click',
            effects: [
                {
                    target: '#add-to-cart-btn',
                    effectId: 'add-to-cart-success'
                }
            ]
        },
        
        // Staggered review animations
        {
            source: '.review-1',
            trigger: 'viewEnter',
            params: { type: 'once', threshold: 0.2 },
            effects: [
                {
                    target: '.review-1',
                    effectId: 'review-entrance',
                    delay: 0
                }
            ]
        },
        {
            source: '.review-2',
            trigger: 'viewEnter',
            params: { type: 'once', threshold: 0.2 },
            effects: [
                {
                    target: '.review-2',
                    effectId: 'review-entrance',
                    delay: 100
                }
            ]
        }
    ]
};
```

## Best Practices

### Configuration Organization
1. **Start simple** - Use inline effects for prototypes
2. **Extract reusable effects** as your project grows
3. **Group by feature** rather than by type

### Performance Tips
1. **Reuse effects** instead of duplicating them
2. **Use conditions** to avoid unnecessary animations
3. **Lazy load** configurations for large applications
4. **Validate in development** to catch errors early

## Next Steps

Now that you understand configuration structure:
- **[Custom Elements](./custom-elements.md)** - Learn about `<wix-interact-element>`
- **[State Management](./state-management.md)** - Advanced state handling
- **[Conditions and Media Queries](./conditions-and-media-queries.md)** - Responsive interactions