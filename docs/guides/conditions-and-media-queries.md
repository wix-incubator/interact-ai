# Conditions and Media Queries

Conditions allow you to create responsive interactions that adapt to different screen sizes, user preferences, and environmental factors. This guide explains how to build adaptive interactions using media queries and container queries.

## Overview of Conditions

Conditions in `@wix/interact` act as filters that determine when interactions should be active. They enable you to:

- **Create responsive animations** that work across devices
- **Respect user preferences** like reduced motion
- **Adapt to container sizes** with container queries
- **Build progressive enhancement** for different capabilities

## Types of Conditions

### Media Conditions
Use CSS media queries to target device characteristics:

```typescript
{
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
        }
    }
}
```

### Container Conditions  
Use container queries to respond to element size:

```typescript
{
    conditions: {
        'large-card': {
            type: 'container',
            predicate: '(min-width: 400px)'
        },
        'tall-container': {
            type: 'container', 
            predicate: '(min-height: 300px)'
        }
    }
}
```

## Cascading of effects

Interact allows you to apply multiple effects on the same target and have thme cascade, just like they do in CSS.
This allows you to have different variations of effects for different `media`/`contianer` `conditions` and have only one of them apply, base on the current environment of the user.

**Important**: In order to use this cascading behavior, `conditions` need to be set on the Effect-level, and not on the Interaction-level.

The cascading logic works as follows:

1. Effects that are grouped in the same `effects` array under the same interaction
2. Effects that share the same `target`
3. Last effect in the `effects` array wins - just like in CSS

### Example

Here is a mobile-first example for a slide effect of a card:

```typescript
const responsiveConfig: InteractConfig = {
    conditions: {
        'desktop': {
            type: 'media',
            predicate: '(min-width: 1024px)'
        },
        'tablet': {
            type: 'media',
            predicate: '(min-width: 768px) and (max-width: 1023px)'
        },
        'mobile': {
            type: 'media',
            predicate: '(max-width: 767px)'
        }
    },
    effects: {
        'light-slide': {
            keyframeEffect: {
                transform: ['translateY(-100px)', 'translateY(0)']
            },
            duration: 400,
            easing: 'ease-out'
        },
        'medium-slide': {
            keyframeEffect: {
                transform: ['translateY(-50%)', 'translateY(0)']
            },
            duration: 400,
            easing: 'ease-in-out'
        },
        'hard-slide': {
            keyframeEffect: {
                transform: ['translateY(-100%)', 'translateY(0)']
            },
            duration: 600,
            easing: 'backOut'
        }
    },
    interactions: [
        {
            source: '.product-card',
            trigger: 'viewEnter',
            effects: [
                {
                    conditions: ['desktop'],
                    effectId: 'light-slide'
                },
                {
                    conditions: ['tablet'],
                    effectId: 'medium-slide'
                },
                {
                    conditions: ['mobile'],
                    effectId: 'hard-slide'
                }
            ]
        },
    ]
};
```

## Responsive Interaction Patterns

### Desktop vs Mobile Interactions

```typescript
const responsiveConfig: InteractConfig = {
    conditions: {
        'desktop': {
            type: 'media',
            predicate: '(min-width: 1024px)'
        },
        'tablet': {
            type: 'media',
            predicate: '(min-width: 768px) and (max-width: 1023px)'
        },
        'mobile': {
            type: 'media',
            predicate: '(max-width: 767px)'
        }
    },
    
    interactions: [
        // Desktop: Hover effects
        {
            source: '.product-card',
            trigger: 'hover',
            conditions: ['desktop'],
            effects: [
                {
                    target: '.product-card',
                    keyframeEffect: {
                        transform: ['translateY(0)', 'translateY(-8px)'],
                        boxShadow: ['0 4px 6px rgba(0,0,0,0.1)', '0 20px 25px rgba(0,0,0,0.15)']
                    },
                    duration: 200,
                    easing: 'ease-out'
                }
            ]
        },
        
        // Mobile: Touch feedback
        {
            source: '.product-card',
            trigger: 'click',
            conditions: ['mobile'],
            effects: [
                {
                    target: '.product-card',
                    keyframeEffect: {
                        transform: ['scale(1)', 'scale(0.98)', 'scale(1)']
                    },
                    duration: 150,
                    easing: 'ease-in-out'
                }
            ]
        }
    ]
};
```

### Accessibility-Aware Animations

```typescript
const accessibleConfig: InteractConfig = {
    conditions: {
        'motion-ok': {
            type: 'media',
            predicate: '(prefers-reduced-motion: no-preference)'
        },
        'motion-reduced': {
            type: 'media',
            predicate: '(prefers-reduced-motion: reduce)'
        },
        'high-contrast': {
            type: 'media',
            predicate: '(prefers-contrast: high)'
        }
    },
    
    interactions: [
        // Full animation for users who prefer motion
        {
            source: '.hero-title',
            trigger: 'viewEnter',
            conditions: ['motion-ok'],
            params: { type: 'once', threshold: 0.3 },
            effects: [
                {
                    target: '.hero-title',
                    keyframeEffect: {
                        opacity: ['0', '1'],
                        transform: ['translateY(50px) rotate(-2deg)', 'translateY(0) rotate(0deg)']
                    },
                    duration: 800,
                    easing: 'cubic-bezier(0.34, 1.56, 0.64, 1)'
                }
            ]
        },
        
        // Subtle animation for reduced motion users
        {
            source: '.hero-title',
            trigger: 'viewEnter',
            conditions: ['motion-reduced'],
            params: { type: 'once', threshold: 0.3 },
            effects: [
                {
                    target: '.hero-title',
                    keyframeEffect: {
                        opacity: ['0', '1']
                    },
                    duration: 300,
                    easing: 'ease-out'
                }
            ]
        }
    ]
};
```

## Device-Specific Patterns

### Touch vs Mouse Devices

```typescript
const deviceConfig: InteractConfig = {
    conditions: {
        'touch-device': {
            type: 'media',
            predicate: '(hover: none) and (pointer: coarse)'
        },
        'mouse-device': {
            type: 'media',
            predicate: '(hover: hover) and (pointer: fine)'
        },
        'stylus-device': {
            type: 'media',
            predicate: '(hover: hover) and (pointer: coarse)'
        }
    },
    
    interactions: [
        // Mouse: Hover interactions
        {
            source: '.interactive-button',
            trigger: 'hover',
            conditions: ['mouse-device'],
            effects: [
                {
                    target: '.interactive-button',
                    namedEffect: 'Scale',
                    duration: 200
                }
            ]
        },
        
        // Touch: Press feedback
        {
            source: '.interactive-button',
            trigger: 'click',
            conditions: ['touch-device'],
            effects: [
                {
                    target: '.interactive-button',
                    keyframeEffect: {
                        transform: ['scale(1)', 'scale(0.95)'],
                        opacity: ['1', '0.8']
                    },
                    duration: 100
                }
            ]
        }
    ]
};
```

### Screen Size Breakpoints

```typescript
const breakpointConfig: InteractConfig = {
    conditions: {
        'xs': {
            type: 'media',
            predicate: '(max-width: 475px)'
        },
        'sm': {
            type: 'media',
            predicate: '(min-width: 476px) and (max-width: 640px)'
        },
        'md': {
            type: 'media',
            predicate: '(min-width: 641px) and (max-width: 768px)'
        },
        'lg': {
            type: 'media',
            predicate: '(min-width: 769px) and (max-width: 1024px)'
        },
        'xl': {
            type: 'media',
            predicate: '(min-width: 1025px)'
        }
    },
    
    interactions: [
        // Extra small: Minimal animations
        {
            source: '.card',
            trigger: 'viewEnter',
            conditions: ['xs'],
            effects: [
                {
                    target: '.card',
                    namedEffect: 'FadeIn',
                    duration: 400
                }
            ]
        },
        
        // Large screens: Full animations
        {
            source: '.card',
            trigger: 'viewEnter',
            conditions: ['lg', 'xl'],
            effects: [
                {
                    target: '.card',
                    keyframeEffect: {
                        opacity: ['0', '1'],
                        transform: ['translateY(60px) scale(0.9)', 'translateY(0) scale(1)']
                    },
                    duration: 800,
                    easing: 'cubic-bezier(0.16, 1, 0.3, 1)'
                }
            ]
        }
    ]
};
```

## Container Query Patterns

### Responsive Cards

```typescript
const containerConfig: InteractConfig = {
    conditions: {
        'card-large': {
            type: 'container',
            predicate: '(min-width: 350px)'
        },
        'card-small': {
            type: 'container',
            predicate: '(max-width: 349px)'
        },
        'card-tall': {
            type: 'container',
            predicate: '(min-height: 400px)'
        }
    },
    
    interactions: [
        // Large cards: Complex hover effect
        {
            source: '.adaptive-card',
            trigger: 'hover',
            conditions: ['card-large'],
            effects: [
                {
                    target: '.card-image',
                    keyframeEffect: {
                        transform: ['scale(1)', 'scale(1.1)'],
                        filter: ['brightness(1)', 'brightness(1.1)']
                    },
                    duration: 300
                },
                {
                    target: '.card-overlay',
                    keyframeEffect: {
                        opacity: ['0', '1'],
                        transform: ['translateY(100%)', 'translateY(0)']
                    },
                    duration: 250,
                    delay: 50
                }
            ]
        },
        
        // Small cards: Simple effect
        {
            source: '.adaptive-card',
            trigger: 'hover',
            conditions: ['card-small'],
            effects: [
                {
                    target: '.adaptive-card',
                    keyframeEffect: {
                        boxShadow: ['0 2px 4px rgba(0,0,0,0.1)', '0 8px 16px rgba(0,0,0,0.15)']
                    },
                    duration: 200
                }
            ]
        }
    ]
};
```

### Layout-Aware Animations

```typescript
const layoutConfig: InteractConfig = {
    conditions: {
        'sidebar-layout': {
            type: 'container',
            predicate: '(min-width: 1200px)'
        },
        'stack-layout': {
            type: 'container',
            predicate: '(max-width: 1199px)'
        }
    },
    
    interactions: [
        // Sidebar layout: Slide from side
        {
            source: '.content-panel',
            trigger: 'viewEnter',
            conditions: ['sidebar-layout'],
            effects: [
                {
                    target: '.content-panel',
                    keyframeEffect: {
                        transform: ['translateX(-50px)', 'translateX(0)'],
                        opacity: ['0', '1']
                    },
                    duration: 600
                }
            ]
        },
        
        // Stack layout: Slide from bottom
        {
            source: '.content-panel',
            trigger: 'viewEnter',
            conditions: ['stack-layout'],
            effects: [
                {
                    target: '.content-panel',
                    keyframeEffect: {
                        transform: ['translateY(30px)', 'translateY(0)'],
                        opacity: ['0', '1']
                    },
                    duration: 600
                }
            ]
        }
    ]
};
```

## Advanced Condition Patterns

### Combining Multiple Conditions

```typescript
const complexConfig: InteractConfig = {
    conditions: {
        'desktop': {
            type: 'media',
            predicate: '(min-width: 1024px)'
        },
        'motion-ok': {
            type: 'media',
            predicate: '(prefers-reduced-motion: no-preference)'
        },
        'high-res': {
            type: 'media',
            predicate: '(min-resolution: 144dpi)'
        },
        'wide-container': {
            type: 'container',
            predicate: '(min-width: 600px)'
        }
    },
    
    interactions: [
        // Complex animation: desktop + motion ok + wide container
        {
            source: '.hero-animation',
            trigger: 'viewEnter',
            conditions: ['desktop', 'motion-ok', 'wide-container'],
            effects: [
                {
                    target: '.hero-background',
                    keyframeEffect: {
                        transform: ['scale(1.1)', 'scale(1)'],
                        filter: ['blur(2px)', 'blur(0)']
                    },
                    duration: 1200,
                    easing: 'ease-out'
                },
                {
                    target: '.hero-content',
                    keyframeEffect: {
                        opacity: ['0', '1'],
                        transform: ['translateY(80px)', 'translateY(0)']
                    },
                    duration: 800,
                    delay: 400
                }
            ]
        }
    ]
};
```

### Orientation-Based Interactions

```typescript
const orientationConfig: InteractConfig = {
    conditions: {
        'portrait': {
            type: 'media',
            predicate: '(orientation: portrait)'
        },
        'landscape': {
            type: 'media',
            predicate: '(orientation: landscape)'
        },
        'mobile-portrait': {
            type: 'media',
            predicate: '(max-width: 767px) and (orientation: portrait)'
        }
    },
    
    interactions: [
        // Portrait: Vertical animations
        {
            source: '.gallery-item',
            trigger: 'viewEnter',
            conditions: ['portrait'],
            effects: [
                {
                    target: '.gallery-item',
                    keyframeEffect: {
                        transform: ['translateY(50px)', 'translateY(0)']
                    },
                    duration: 500
                }
            ]
        },
        
        // Landscape: Horizontal animations
        {
            source: '.gallery-item',
            trigger: 'viewEnter',
            conditions: ['landscape'],
            effects: [
                {
                    target: '.gallery-item',
                    keyframeEffect: {
                        transform: ['translateX(-50px)', 'translateX(0)']
                    },
                    duration: 500
                }
            ]
        }
    ]
};
```

## Environment-Based Conditions

### Dark Mode Support

```typescript
const themeConfig: InteractConfig = {
    conditions: {
        'dark-mode': {
            type: 'media',
            predicate: '(prefers-color-scheme: dark)'
        },
        'light-mode': {
            type: 'media',
            predicate: '(prefers-color-scheme: light)'
        }
    },
    
    interactions: [
        // Dark mode: Subtle glow effects
        {
            source: '.interactive-element',
            trigger: 'hover',
            conditions: ['dark-mode'],
            effects: [
                {
                    target: '.interactive-element',
                    keyframeEffect: {
                        boxShadow: ['none', '0 0 20px rgba(255,255,255,0.1)'],
                        borderColor: ['transparent', 'rgba(255,255,255,0.2)']
                    },
                    duration: 300
                }
            ]
        },
        
        // Light mode: Shadow effects
        {
            source: '.interactive-element',
            trigger: 'hover',
            conditions: ['light-mode'],
            effects: [
                {
                    target: '.interactive-element',
                    keyframeEffect: {
                        boxShadow: ['0 2px 4px rgba(0,0,0,0.1)', '0 8px 16px rgba(0,0,0,0.15)'],
                        transform: ['translateY(0)', 'translateY(-2px)']
                    },
                    duration: 300
                }
            ]
        }
    ]
};
```

### Performance-Based Conditions

```typescript
const performanceConfig: InteractConfig = {
    conditions: {
        'high-performance': {
            type: 'media',
            predicate: '(prefers-reduced-motion: no-preference) and (min-resolution: 96dpi)'
        },
        'low-performance': {
            type: 'media',
            predicate: '(prefers-reduced-motion: reduce) or (max-resolution: 95dpi)'
        }
    },
    
    interactions: [
        // High performance: Rich animations
        {
            source: '.feature-card',
            trigger: 'viewEnter',
            conditions: ['high-performance'],
            effects: [
                {
                    target: '.feature-icon',
                    keyframeEffect: {
                        transform: [
                            'scale(0.5) rotate(-180deg)',
                            'scale(1) rotate(0deg)'
                        ],
                        filter: ['blur(10px)', 'blur(0)']
                    },
                    duration: 800,
                    easing: 'cubic-bezier(0.34, 1.56, 0.64, 1)'
                }
            ]
        },
        
        // Low performance: Simple fade
        {
            source: '.feature-card',
            trigger: 'viewEnter',
            conditions: ['low-performance'],
            effects: [
                {
                    target: '.feature-card',
                    namedEffect: 'FadeIn',
                    duration: 400
                }
            ]
        }
    ]
};
```

## Real-World Examples

### E-commerce Product Grid

```typescript
const productGridConfig: InteractConfig = {
    conditions: {
        'desktop': {
            type: 'media',
            predicate: '(min-width: 1024px)'
        },
        'tablet': {
            type: 'media',
            predicate: '(min-width: 768px) and (max-width: 1023px)'
        },
        'mobile': {
            type: 'media',
            predicate: '(max-width: 767px)'
        },
        'large-product-card': {
            type: 'container',
            predicate: '(min-width: 300px)'
        },
        'motion-ok': {
            type: 'media',
            predicate: '(prefers-reduced-motion: no-preference)'
        }
    },
    
    interactions: [
        // Desktop: Full hover experience
        {
            source: '.product-card',
            trigger: 'hover',
            conditions: ['desktop', 'large-product-card', 'motion-ok'],
            effects: [
                {
                    target: '.product-image',
                    keyframeEffect: {
                        transform: ['scale(1)', 'scale(1.05)']
                    },
                    duration: 300
                },
                {
                    target: '.product-overlay',
                    keyframeEffect: {
                        opacity: ['0', '1'],
                        transform: ['translateY(100%)', 'translateY(0)']
                    },
                    duration: 250,
                    delay: 50
                },
                {
                    target: '.quick-view-button',
                    keyframeEffect: {
                        opacity: ['0', '1'],
                        transform: ['scale(0.8)', 'scale(1)']
                    },
                    duration: 200,
                    delay: 150
                }
            ]
        },
        
        // Mobile: Touch feedback only
        {
            source: '.product-card',
            trigger: 'click',
            conditions: ['mobile'],
            effects: [
                {
                    target: '.product-card',
                    keyframeEffect: {
                        transform: ['scale(1)', 'scale(0.98)', 'scale(1)']
                    },
                    duration: 150
                }
            ]
        },
        
        // Tablet: Intermediate experience
        {
            source: '.product-card',
            trigger: 'click',
            conditions: ['tablet'],
            effects: [
                {
                    target: '.product-overlay',
                    keyframeEffect: {
                        opacity: ['0', '1']
                    },
                    duration: 200
                }
            ]
        }
    ]
};
```

### Responsive Navigation

```typescript
const navigationConfig: InteractConfig = {
    conditions: {
        'mobile': {
            type: 'media',
            predicate: '(max-width: 767px)'
        },
        'desktop': {
            type: 'media',
            predicate: '(min-width: 768px)'
        },
        'narrow-header': {
            type: 'container',
            predicate: '(max-width: 800px)'
        }
    },
    
    interactions: [
        // Mobile: Slide menu
        {
            source: '#mobile-menu-toggle',
            trigger: 'click',
            conditions: ['mobile'],
            params: { type: 'alternate' },
            effects: [
                {
                    target: '#mobile-menu',
                    keyframeEffect: {
                        transform: ['translateX(-100%)', 'translateX(0)']
                    },
                    duration: 300,
                    easing: 'ease-out'
                },
                {
                    target: '#menu-overlay',
                    keyframeEffect: {
                        opacity: ['0', '0.5']
                    },
                    duration: 300
                }
            ]
        },
        
        // Desktop: Dropdown menus
        {
            source: '.nav-item',
            trigger: 'hover',
            conditions: ['desktop'],
            effects: [
                {
                    target: '.dropdown-menu',
                    keyframeEffect: {
                        opacity: ['0', '1'],
                        transform: ['translateY(-10px)', 'translateY(0)']
                    },
                    duration: 200
                }
            ]
        }
    ]
};
```

## Best Practices

### Condition Naming
Use descriptive, semantic names:

```typescript
// Good
'desktop-large': { type: 'media', predicate: '(min-width: 1200px)' }
'touch-primary': { type: 'media', predicate: '(pointer: coarse)' }
'motion-safe': { type: 'media', predicate: '(prefers-reduced-motion: no-preference)' }

// Avoid
'condition1': { type: 'media', predicate: '(min-width: 1200px)' }
'big': { type: 'media', predicate: '(min-width: 1200px)' }
```

### Progressive Enhancement
Start with basic functionality and enhance:

```typescript
const progressiveConfig: InteractConfig = {
    interactions: [
        // Base interaction - works everywhere
        {
            source: '.button',
            trigger: 'click',
            effects: [
                {
                    target: '.button',
                    namedEffect: 'Pulse',
                    duration: 200
                }
            ]
        },
        
        // Enhanced interaction - only on capable devices
        {
            source: '.button',
            trigger: 'hover',
            conditions: ['desktop', 'motion-ok'],
            effects: [
                {
                    target: '.button',
                    keyframeEffect: {
                        transform: ['translateY(0)', 'translateY(-2px)'],
                        boxShadow: ['0 2px 4px rgba(0,0,0,0.1)', '0 8px 16px rgba(0,0,0,0.15)']
                    },
                    duration: 250
                }
            ]
        }
    ]
};
```

### Accessibility First
Always provide accessible alternatives:

```typescript
// Always provide a motion-safe version
{
    source: '.animated-element',
    trigger: 'viewEnter',
    conditions: ['motion-ok'],
    effects: [/* complex animation */]
},
{
    source: '.animated-element',
    trigger: 'viewEnter',
    conditions: ['motion-reduced'],
    effects: [/* simple fade or no animation */]
}
```

## Debugging Conditions

TBD

## Next Steps

You've completed the concept guides! Here's what to explore next:

- **[API Reference](../api/README.md)** - Detailed documentation of all classes and methods
- **[Examples](../examples/README.md)** - Practical examples and patterns
- **[Integration Guide](../integration/README.md)** - Framework-specific integration guides