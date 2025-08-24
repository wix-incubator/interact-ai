# Understanding Triggers

Triggers are the heart of `@wix/interact` - they define when an interaction should start. This guide covers all 7 trigger types and how to use them effectively.

## Overview of Trigger Types

| Trigger | Description | Use Cases |
|---------|-------------|-----------|
| `hover` | Mouse enter/leave events | Button highlights, image overlays |
| `click` | Mouse click events | Toggles, state changes, menus |
| `viewEnter` | Element enters viewport | Entrance animations, lazy loading |
| `pageVisible` | Page becomes visible | Loop animations, Auto-play videos |
| `animationEnd` | Previous animation completes | Animation sequences, chaining |
| `viewProgress` | Scroll progress through element | Parallax, progress bars |
| `pointerMove` | Mouse movement over element | Interactive cards, 3D effects |

## 1. Hover Trigger

The `hover` trigger responds to mouse enter and leave events.

### Basic Usage
```typescript
{
    source: '#my-button',
    trigger: 'hover',
    effects: [
        {
            keyframeEffect: [
                { scale: 2 }
            ],
            duration: 200
        }
    ]
}
```

### Advanced Options
```typescript
{
    source: '#my-card',
    trigger: 'hover',
    params: {
        type: 'alternate'
    },
    effects: [
        {
            keyframeEffect: {
                transform: ['scale(1)', 'scale(1.05)'],
                boxShadow: ['none', '0 10px 20px rgba(0,0,0,0.1)']
            },
            duration: 300,
            easing: 'ease-out'
        }
    ]
}
```

### Hover Behavior Types
- **`alternate`** (default): Plays forward on enter, reverses on leave
- **`repeat`**: Restarts animation each time
- **`once`**: Only plays once, then stops
- **`state`**: Pauses/resumes on hover

### Real-World Example: Image Overlay
```typescript
{
    source: '#image-card',
    trigger: 'hover',
    params: { type: 'alternate' },
    effects: [
        {
            target: '#image-overlay',
            keyframeEffect: {
                opacity: ['0', '1'],
                transform: ['translateY(20px)', 'translateY(0)']
            },
            duration: 250,
            easing: 'ease-out'
        }
    ]
}
```

## 2. Click Trigger

The `click` trigger responds to mouse click events and supports multiple behavior modes.

### Basic Usage
```typescript
{
    source: '#toggle-button',
    trigger: 'click',
    effects: [
        {
            target: '#sidebar',
            keyframeEffect: {
                transform: ['translateX(-100%)', 'translateX(0)']
            },
            duration: 300
        }
    ]
}
```

### Click Behavior Types
```typescript
{
    source: '#accordion-header',
    trigger: 'click',
    params: {
        type: 'alternate' 
    },
    effects: [
        {
            target: '#accordion-content',
            keyframeEffect: [
                { clipPath: 'inset(0 0 100% 0)', translate: '0 -100%' },
                { clipPath: 'inset(0 0 0 0)', translate: '0 0' }
            ],
            duration: 400
        }
    ]
}
```

- **`alternate`** (default): First click plays forward, subsequent click reverses
- **`repeat`**: Each click restarts the animation
- **`once`**: Only responds to the first click
- **`state`**: First click plays, subsequent clicks pause/resume

### Real-World Example: Menu Toggle
```typescript
{
    source: '#menu-button',
    trigger: 'click',
    params: { type: 'alternate' },
    effects: [
        {
            target: '#menu-icon',
            keyframeEffect: {
                transform: ['rotate(0deg)', 'rotate(45deg)']
            },
            duration: 200,
            effectId: 'menu-icon-rotate'
        },
        {
            target: '#mobile-menu',
            keyframeEffect: {
                opacity: ['0', '1'],
                transform: ['translateX(100%)', 'translateX(0)']
            },
            duration: 300,
            delay: 100
        }
    ]
}
```

## 3. ViewEnter Trigger

The `viewEnter` trigger uses Intersection Observer to detect when elements enter the viewport.

### Basic Usage
```typescript
{
    source: '#section-title',
    trigger: 'viewEnter',
    effects: [
        {
            target: '#section-title',
            namedEffect: 'FadeIn',
            duration: 800
        }
    ]
}
```

### Advanced Configuration
```typescript
{
    source: '#hero-image',
    trigger: 'viewEnter',
    params: {
        type: 'once',        // 'once' | 'repeat' | 'alternate'
        threshold: 0.5,      // 0-1, how much of element must be visible
        inset: '-50px'        // Margin around viewport
    },
    effects: [
        {
            target: '#hero-image',
            keyframeEffect: {
                opacity: ['0', '1'],
                transform: ['translateY(50px)', 'translateY(0)']
            },
            duration: 1000,
            easing: 'ease-out'
        }
    ]
}
```

### ViewEnter Behavior Types
- **`once`** (recommended): Triggers only when element first enters viewport
- **`repeat`**: Triggers every time element enters viewport
- **`alternate`**: Plays forward on enter, reverses on exit

### Real-World Example: Staggered Card Animation
```typescript
// Configure multiple cards with delays
const cardAnimations = [
    {
        source: '#card-1',
        trigger: 'viewEnter',
        params: { type: 'once', threshold: 0.3 },
        effects: [{
            target: '#card-1',
            namedEffect: 'SlideUp',
            duration: 600,
            delay: 0
        }]
    },
    {
        source: '#card-2',
        trigger: 'viewEnter',
        params: { type: 'once', threshold: 0.3 },
        effects: [{
            target: '#card-2',
            namedEffect: 'SlideUp',
            duration: 600,
            delay: 100
        }]
    }
];
```

## 4. PageVisible Trigger

TBD

## 5. ViewProgress Trigger

The `viewProgress` trigger creates scroll-driven animations as elements move through the viewport.

### Basic Usage
```typescript
{
    source: '#parallax-element',
    trigger: 'viewProgress',
    effects: [
        {
            target: '#parallax-element',
            keyframeEffect: {
                transform: ['translateY(0)', 'translateY(-100px)']
            }
            // Note: viewProgress uses ranges, not duration
        }
    ]
}
```

### Advanced Parallax Effect
```typescript
{
    source: '#hero-section',
    trigger: 'viewProgress',
    effects: [
        {
            target: '#background-image',
            keyframeEffect: {
                filter: ['grayscale(0)', 'grayscale(100%)']
            },
            rangeStart: { name: 'exit', offset: { value: 0 } },
            rangeEnd: { name: 'exit', offset: { value: 100 } }
        },
        {
            target: '#foreground-text',
            keyframeEffect: {
                opacity: ['1', '0'],
                transform: ['scale(1)', 'scale(0.8)']
            },
            rangeStart: { name: 'exit', offset: { value: 0 } },
            rangeEnd: { name: 'exit', offset: { value: 100 } }
        }
    ]
}
```

### Real-World Example: Reading Progress Bar
```typescript
{
    source: '#article-content',
    trigger: 'viewProgress',
    effects: [
        {
            target: '#progress-bar',
            keyframeEffect: {
                transform: ['scaleX(0)', 'scaleX(1)']
            },
            rangeStart: { name: 'cover', offset: { value: 0 } },
            rangeEnd: { name: 'cover', offset: { value: 100 } }
        }
    ]
}
```

## 6. PointerMove Trigger

The `pointerMove` trigger creates mouse-following effects and 3D interactions.

### Basic Usage
```typescript
{
    source: '#interactive-card',
    trigger: 'pointerMove',
    effects: [
        {
            target: '#interactive-card',
            keyframeEffect: {
                transform: [
                    'perspective(800px) rotateX(40deg) rotateY(-40deg)',
                    'perspective(800px) rotateX(-40deg) rotateY(40deg)'
                ]
            }
        }
    ]
}
```

### Advanced Configuration
```typescript
{
    source: '#card-section',
    trigger: 'pointerMove',
    params: {
        hitArea: 'self'  // 'self' | 'root'
    },
    effects: [
        {
            target: '#tilt-card',
            keyframeEffect: {
                transform: [
                    'perspective(800px) rotateX(40deg) rotateY(-40deg)',
                    'perspective(800px) rotateX(-40deg) rotateY(40deg)'
                ]
            },
            centeredToTarget: true // will place 50% point at center of animation target
        }
    ]
}
```

### Real-World Example: 3D Product Card
```typescript
{
    source: '#product-card',
    trigger: 'pointerMove',
    params: { hitArea: 'self' },
    effects: [
        {
            target: '#tilt-card',
            keyframeEffect: {
                transform: [
                    'perspective(800px) rotateX(40deg) rotateY(-40deg)',
                    'perspective(800px) rotateX(-40deg) rotateY(40deg)'
                ]
            },
            centeredToTarget: true // will place 50% point at center of animation target
        }
    ]
}
```

## 7. AnimationEnd Trigger

The `animationEnd` trigger allows you to chain animations by waiting for a previous animation to complete.

### Basic Usage
```typescript
// First animation
{
    source: '#sequence-start',
    trigger: 'click',
    effects: [
        {
            target: '#first-element',
            namedEffect: 'FadeIn',
            duration: 500,
            effectId: 'fade-in-first'  // Required for chaining
        }
    ]
},
// Chained animation
{
    source: '#first-element',
    trigger: 'animationEnd',
    params: {
        effectId: 'fade-in-first'  // Must match the effectId above
    },
    effects: [
        {
            target: '#second-element',
            namedEffect: 'SlideIn',
            duration: 500,
            effectId: 'slide-in-second'
        }
    ]
}
```

### Real-World Example: Loading Sequence
```typescript
const loadingSequence = [
    {
        source: '#app-logo',
        trigger: 'viewEnter',
        effects: [{
            target: '#app-logo',
            namedEffect: 'FadeIn',
            duration: 800,
            effectId: 'logo-appear'
        }]
    },
    {
        source: '#app-logo',
        trigger: 'animationEnd',
        params: { effectId: 'logo-appear' },
        effects: [{
            target: '#loading-text',
            namedEffect: 'SlideUp',
            duration: 600,
            effectId: 'text-appear'
        }]
    },
    {
        source: '#loading-text',
        trigger: 'animationEnd',
        params: { effectId: 'text-appear' },
        effects: [{
            target: '#main-content',
            namedEffect: 'FadeIn',
            duration: 1000
        }]
    }
];
```

## Combining Triggers

You can combine multiple triggers on the same element for complex interactions:

```typescript
{
    interactions: [
        // Entrance animation
        {
            source: '#interactive-card',
            trigger: 'viewEnter',
            params: { type: 'once' },
            effects: [{
                target: '#interactive-card',
                namedEffect: 'FadeIn',
                duration: 800
            }]
        },
        // Hover effect
        {
            source: '#interactive-card',
            trigger: 'hover',
            effects: [{
                target: '#interactive-card',
                keyframeEffect: {
                    transform: ['scale(1)', 'scale(1.02)'],
                    boxShadow: ['none', '0 20px 40px rgba(0,0,0,0.1)']
                },
                duration: 200
            }]
        },
        // Click action
        {
            source: '#interactive-card',
            trigger: 'click',
            effects: [{
                target: '#card-details',
                namedEffect: 'SlideDown',
                duration: 400
            }]
        }
    ]
}
```

## Best Practices

### Performance
1. **Prefer animating hardware-accelerated properties**, i.e. transforms, opacity, and filters
2. **Avoid animating layout properties**, these are the most costly properties to animate
3. **Avoid animating custom properties**, since changing them via animation causes a style recalculation

### User Experience
1. **Keep hover animations short** (200-300ms) for responsiveness
2. **Use meaningful delays** in animation sequences
3. **Provide visual feedback** for click interactions
4. **Set appropriate thresholds** for `viewEnter` to avoid premature triggers

### Accessibility
1. **Respect `prefers-reduced-motion`** media query
2. **Ensure click targets are accessible** via keyboard
3. **Don't rely solely on motion** for important information

## Troubleshooting

### Common Issues

**Trigger not firing:**
- Check element exists when `Interact.create()` is called
- Verify `data-wix-path` matches your CSS selector
- Ensure element is actually visible for viewport triggers

**Animation not smooth:**
- Use `transform` and `opacity` properties
- Avoid animating layout properties like `width`/`height`
- Check for conflicting CSS transitions

**ViewEnter not triggering**
- Since entrance animations use `IntersectionObserver`, if the `source` is clipped out, e.g: by a parent's `overflow`, or `clip-path`, or if it's pushed outside of the viewport it may never trigger.
  To avoid this either **use a separate `target` form the `source`, or avoid transforms/clip-path/mask at 0% progress**

**ViewEnter triggering multiple times**
- From same reasons as above, **Use `once` for `viewEnter` triggers**, if the `source` is also the `target`, since it may re-trigger animations multiple times when animating.

**ViewEnter triggering too early/late:**
- Adjust `threshold` and `inset` parameters
- Test on different screen sizes
- Consider content loading delays

## Next Steps

Now that you understand all trigger types, explore:
- **[Effects and Animations](./effects-and-animations.md)** - Learn about animation options
- **[Configuration Structure](./configuration-structure.md)** - Organize complex interactions
- **[State Management](./state-management.md)** - Advanced state handling techniques