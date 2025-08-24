# ViewEnter Trigger Rules for @wix/interact

These rules help generate viewport-based interactions using the `@wix/interact` library. ViewEnter triggers use Intersection Observer to detect when elements enter the viewport and are ideal for entrance animations, lazy loading effects, and scroll-triggered content reveals.

## Rule 1: ViewEnter with Once Type for Entrance Animations

**Use Case**: One-time entrance animations that play when elements first become visible (e.g., hero sections, content blocks, images, cards)

**When to Apply**: 
- For entrance animations that should only happen once
- When you want elements to stay in their final animated state
- For progressive content reveal as user scrolls
- When implementing lazy-loading visual effects

**Pattern**:
```typescript
{
    source: '[SOURCE_SELECTOR]',
    trigger: 'viewEnter',
    params: {
        type: 'once',
        threshold: [VISIBILITY_THRESHOLD],
        inset: '[VIEWPORT_INSETS]'
    },
    effects: [
        {
            target: '[TARGET_SELECTOR]',
            [EFFECT_TYPE]: [EFFECT_DEFINITION],
            duration: [DURATION_MS],
            easing: '[EASING_FUNCTION]',
            delay: [DELAY_MS],
            effectId: '[UNIQUE_EFFECT_ID]'
        }
    ]
}
```

**Variables**:
- `[SOURCE_SELECTOR]`: CSS selector for element that triggers when visible (often same as target)
- `[TARGET_SELECTOR]`: CSS selector for element to animate (can be same as source or different)
- `[VISIBILITY_THRESHOLD]`: Number between 0-1 indicating how much of element must be visible (e.g., 0.3 = 30%)
- `[VIEWPORT_INSETS]`: String insets around viewport (e.g., '50px', '10%', '-100px')
- `[EFFECT_TYPE]`: Either `namedEffect` or `keyframeEffect`
- `[EFFECT_DEFINITION]`: Named effect string (e.g., 'FadeIn', 'SlideIn') or keyframe object
- `[DURATION_MS]`: Animation duration in milliseconds (typically 500-1200ms for entrances)
- `[EASING_FUNCTION]`: Timing function (recommended: 'ease-out', 'cubic-bezier(0.16, 1, 0.3, 1)')
- `[DELAY_MS]`: Optional delay before animation starts
- `[UNIQUE_EFFECT_ID]`: Optional unique identifier for animation chaining

**Example - Hero Section Entrance**:
```typescript
{
    source: '#hero-section',
    trigger: 'viewEnter',
    params: {
        type: 'once',
        threshold: 0.3,
        inset: '-100px'
    },
    effects: [
        {
            target: '#hero-section',
            keyframeEffect: {
                opacity: ['0', '1'],
                transform: ['translateY(60px) scale(0.95)', 'translateY(0) scale(1)']
            },
            duration: 1000,
            easing: 'cubic-bezier(0.16, 1, 0.3, 1)',
            effectId: 'hero-entrance'
        }
    ]
}
```

**Example - Content Block Fade In**:
```typescript
{
    source: '.content-block',
    trigger: 'viewEnter',
    params: {
        type: 'once',
        threshold: 0.5
    },
    effects: [
        {
            target: '.content-block',
            namedEffect: 'FadeIn',
            duration: 800,
            easing: 'ease-out'
        }
    ]
}
```

---

## Rule 2: ViewEnter with Repeat Type and Separate Source/Target

**Use Case**: Animations that retrigger every time elements enter the viewport, often with separate trigger and target elements (e.g., scroll-triggered counters, image reveals, interactive sections)

**When to Apply**:
- When animations should replay on each scroll encounter
- For scroll-triggered interactive elements
- When using separate observer and animation targets
- For elements that might leave and re-enter viewport

**Pattern**:
```typescript
{
    source: '[OBSERVER_SELECTOR]',
    trigger: 'viewEnter',
    params: {
        type: 'repeat',
        threshold: [VISIBILITY_THRESHOLD],
        inset: '[VIEWPORT_INSETS]'
    },
    effects: [
        {
            target: '[ANIMATION_TARGET_SELECTOR]',
            [EFFECT_TYPE]: [EFFECT_DEFINITION],
            duration: [DURATION_MS],
            easing: '[EASING_FUNCTION]',
            delay: [DELAY_MS],
            effectId: '[UNIQUE_EFFECT_ID]'
        }
    ]
}
```

**Variables**:
- `[OBSERVER_SELECTOR]`: CSS selector for element that acts as scroll trigger
- `[ANIMATION_TARGET_SELECTOR]`: CSS selector for element that gets animated (different from observer)
- Other variables same as Rule 1

**Example - Image Reveal on Scroll**:
```typescript
{
    source: '#image-trigger-zone',
    trigger: 'viewEnter',
    params: {
        type: 'repeat',
        threshold: 0.1,
        inset: '-50px'
    },
    effects: [
        {
            target: '#background-image',
            keyframeEffect: {
                filter: ['blur(20px) brightness(0.7)', 'blur(0) brightness(1)'],
                transform: ['scale(1.1)', 'scale(1)']
            },
            duration: 600,
            easing: 'ease-out'
        }
    ]
}
```

**Example - Counter Animation Repeat**:
```typescript
{
    source: '#stats-section',
    trigger: 'viewEnter',
    params: {
        type: 'repeat',
        threshold: 0.6
    },
    effects: [
        {
            target: '#counter-display',
            customEffect: (element, progress) => {
                const targetValue = 1000;
                const currentValue = Math.floor(targetValue * progress);
                element.textContent = currentValue.toLocaleString();
            },
            duration: 2000,
            easing: 'ease-out',
            effectId: 'counter-animation'
        }
    ]
}
```

---

## Rule 3: ViewEnter with Alternate Type and Separate Source/Target

**Use Case**: Animations that play forward when entering viewport and reverse when leaving, using separate observer and target elements (e.g., parallax effects, reveal/hide content, scroll-responsive UI elements)

**When to Apply**:
- For animations that should reverse when element exits viewport
- When creating scroll-responsive reveals
- For elements that animate in and out smoothly
- When observer element is different from animated element

**Pattern**:
```typescript
{
    source: '[OBSERVER_SELECTOR]',
    trigger: 'viewEnter',
    params: {
        type: 'alternate',
        threshold: [VISIBILITY_THRESHOLD],
        inset: '[VIEWPORT_INSETS]'
    },
    effects: [
        {
            target: '[ANIMATION_TARGET_SELECTOR]',
            [EFFECT_TYPE]: [EFFECT_DEFINITION],
            duration: [DURATION_MS],
            easing: '[EASING_FUNCTION]',
            effectId: '[UNIQUE_EFFECT_ID]'
        }
    ]
}
```

**Variables**:
Same as Rule 2

**Example - Content Reveal with Hide**:
```typescript
{
    source: '#content-trigger',
    trigger: 'viewEnter',
    params: {
        type: 'alternate',
        threshold: 0.3,
        inset: '-20px'
    },
    effects: [
        {
            target: '#sidebar-content',
            keyframeEffect: {
                opacity: ['0', '1'],
                transform: ['translateX(-50px)', 'translateX(0)']
            },
            duration: 400,
            easing: 'ease-in-out'
        }
    ]
}
```

**Example - Navigation Bar Reveal**:
```typescript
{
    source: '#page-content',
    trigger: 'viewEnter',
    params: {
        type: 'alternate',
        threshold: 0.1
    },
    effects: [
        {
            target: '#floating-nav',
            keyframeEffect: {
                opacity: ['0', '1'],
                transform: ['translateY(-100%)', 'translateY(0)']
            },
            duration: 300,
            easing: 'ease-out',
            effectId: 'nav-reveal'
        }
    ]
}
```

---

## Rule 4: ViewEnter with State Type for Loop Animations

**Use Case**: Looping animations that start when element enters viewport and can be paused/resumed (e.g., ambient animations, loading states, decorative effects)

**When to Apply**:
- For continuous animations that should start on viewport enter
- When you need pause/resume control over scroll-triggered loops
- For ambient or decorative animations
- When creating scroll-activated background effects

**Pattern**:
```typescript
{
    source: '[SOURCE_SELECTOR]',
    trigger: 'viewEnter',
    params: {
        type: 'state',
        threshold: [VISIBILITY_THRESHOLD],
        inset: '[VIEWPORT_INSETS]'
    },
    effects: [
        {
            target: '[TARGET_SELECTOR]',
            [EFFECT_TYPE]: [EFFECT_DEFINITION],
            duration: [DURATION_MS],
            easing: '[EASING_FUNCTION]',
            iterations: [ITERATION_COUNT],
            alternate: [ALTERNATE_BOOLEAN],
            effectId: '[UNIQUE_EFFECT_ID]'
        }
    ]
}
```

**Variables**:
- `[ITERATION_COUNT]`: Number of iterations or Infinity for continuous looping
- `[ALTERNATE_BOOLEAN]`: true/false - whether to reverse on alternate iterations
- Other variables same as Rule 1

**Example - Floating Animation Loop**:
```typescript
{
    source: '#floating-elements',
    trigger: 'viewEnter',
    params: {
        type: 'state',
        threshold: 0.4
    },
    effects: [
        {
            target: '.floating-icon',
            keyframeEffect: {
                transform: ['translateY(0)', 'translateY(-20px)', 'translateY(0)']
            },
            duration: 3000,
            easing: 'ease-in-out',
            iterations: Infinity,
            alternate: false,
            effectId: 'floating-loop'
        }
    ]
}
```

**Example - Breathing Light Effect**:
```typescript
{
    source: '#ambient-section',
    trigger: 'viewEnter',
    params: {
        type: 'state',
        threshold: 0.2
    },
    effects: [
        {
            target: '#light-orb',
            namedEffect: 'Pulse',
            duration: 2000,
            easing: 'ease-in-out',
            iterations: Infinity,
            alternate: true,
            effectId: 'breathing-light'
        }
    ]
}
```

---

## Rule 5: Threshold and Viewport Intersection Parameters

**Use Case**: Fine-tuning when animations trigger based on element visibility and viewport positioning (e.g., early triggers, late triggers, precise timing)

**When to Apply**:
- When default triggering timing isn't optimal
- For elements that need early or late animation triggers
- When working with very tall or very short elements
- For precise scroll timing control

**Pattern**:
```typescript
{
    source: '[SOURCE_SELECTOR]',
    trigger: 'viewEnter',
    params: {
        type: '[BEHAVIOR_TYPE]',
        threshold: [PRECISE_THRESHOLD],
        inset: '[VIEWPORT_ADJUSTMENT]'
    },
    effects: [
        {
            target: '[TARGET_SELECTOR]',
            [EFFECT_TYPE]: [EFFECT_DEFINITION],
            duration: [DURATION_MS],
            easing: '[EASING_FUNCTION]'
        }
    ]
}
```

**Variables**:
- `[PRECISE_THRESHOLD]`: Decimal between 0-1 for exact visibility percentage
- `[VIEWPORT_ADJUSTMENT]`: Pixel or percentage adjustment to viewport detection area
- `[BEHAVIOR_TYPE]`: 'once', 'repeat', 'alternate', or 'state'
- Other variables same as Rule 1

**Example - Early Trigger for Tall Elements**:
```typescript
{
    source: '#tall-hero-section',
    trigger: 'viewEnter',
    params: {
        type: 'once',
        threshold: 0.1,    // Trigger when only 10% visible
        inset: '-200px'     // Extend detection area 200px beyond viewport
    },
    effects: [
        {
            target: '#tall-hero-section',
            namedEffect: 'SlideIn',
            duration: 1200,
            easing: 'cubic-bezier(0.16, 1, 0.3, 1)'
        }
    ]
}
```

**Example - Late Trigger for Precise Timing**:
```typescript
{
    source: '#precision-content',
    trigger: 'viewEnter',
    params: {
        type: 'once',
        threshold: 0.8,    // Wait until 80% visible
        inset: '50px'     // Shrink detection area by 50px
    },
    effects: [
        {
            target: '#precision-content',
            keyframeEffect: {
                opacity: ['0', '1'],
                filter: ['blur(5px)', 'blur(0)']
            },
            duration: 600,
            easing: 'ease-out'
        }
    ]
}
```

**Example - Mobile vs Desktop Thresholds**:
```typescript
{
    source: '#responsive-element',
    trigger: 'viewEnter',
    params: {
        type: 'once',
        threshold: 0.3,    // Good for mobile
        inset: '-100px'     // Extra space for desktop
    },
    conditions: ['desktop-only'],
    effects: [
        {
            target: '#responsive-element',
            namedEffect: 'FadeIn',
            duration: 800
        }
    ]
}
```

---

## Rule 6: Staggered Entrance Animations

**Use Case**: Sequential entrance animations where multiple elements animate with delays (e.g., card grids, list items, team member cards, feature sections)

**When to Apply**:
- When multiple elements should animate in sequence
- For creating wave or cascade effects
- When animating lists, grids, or collections
- For progressive content revelation

**Pattern**:
```typescript
[
    {
        source: '[ELEMENT_1_SELECTOR]',
        trigger: 'viewEnter',
        params: {
            type: 'once',
            threshold: [SHARED_THRESHOLD],
            inset: '[SHARED_INSET]'
        },
        effects: [
            {
                [EFFECT_TYPE]: [SHARED_EFFECT_DEFINITION],
                duration: [SHARED_DURATION],
                easing: '[SHARED_EASING]',
                delay: [DELAY_1]
            }
        ]
    },
    {
        source: '[ELEMENT_2_SELECTOR]',
        trigger: 'viewEnter',
        params: {
            type: 'once',
            threshold: [SHARED_THRESHOLD],
            inset: '[SHARED_INSET]'
        },
        effects: [
            {
                [EFFECT_TYPE]: [SHARED_EFFECT_DEFINITION],
                duration: [SHARED_DURATION],
                easing: '[SHARED_EASING]',
                delay: [DELAY_2]
            }
        ]
    }
    // ... additional elements with increasing delays
]
```

**Variables**:
- `[ELEMENT_N_SELECTOR]`: CSS selector for each individual element in sequence
- `[DELAY_N]`: Progressive delay values (e.g., 0, 100, 200, 300ms)
- `[SHARED_*]`: Common values used across all elements in the sequence
- Other variables same as Rule 1

**Example - Card Grid Stagger**:
```typescript
[
    {
        source: '#card-1',
        trigger: 'viewEnter',
        params: {
            type: 'once',
            threshold: 0.3
        },
        effects: [
            {
                namedEffect: 'SlideIn',
                duration: 600,
                easing: 'ease-out',
                delay: 0
            }
        ]
    },
    {
        source: '#card-2',
        trigger: 'viewEnter',
        params: {
            type: 'once',
            threshold: 0.3
        },
        effects: [
            {
                namedEffect: 'SlideIn',
                duration: 600,
                easing: 'ease-out',
                delay: 150
            }
        ]
    },
    {
        source: '#card-3',
        trigger: 'viewEnter',
        params: {
            type: 'once',
            threshold: 0.3
        },
        effects: [
            {
                namedEffect: 'SlideIn',
                duration: 600,
                easing: 'ease-out',
                delay: 300
            }
        ]
    }
]
```

**Example - Feature List Cascade**:
```typescript
[
    {
        source: '.feature-item:nth-child(1)',
        trigger: 'viewEnter',
        params: {
            type: 'once',
            threshold: 0.4
        },
        effects: [
            {
                keyframeEffect: {
                    opacity: ['0', '1'],
                    transform: ['translateX(-30px)', 'translateX(0)']
                },
                duration: 500,
                easing: 'cubic-bezier(0.16, 1, 0.3, 1)',
                delay: 0
            }
        ]
    },
    {
        source: '.feature-item:nth-child(2)',
        trigger: 'viewEnter',
        params: {
            type: 'once',
            threshold: 0.4
        },
        effects: [
            {
                keyframeEffect: {
                    opacity: ['0', '1'],
                    transform: ['translateX(-30px)', 'translateX(0)']
                },
                duration: 500,
                easing: 'cubic-bezier(0.16, 1, 0.3, 1)',
                delay: 100
            }
        ]
    },
    {
        source: '.feature-item:nth-child(3)',
        trigger: 'viewEnter',
        params: {
            type: 'once',
            threshold: 0.4
        },
        effects: [
            {
                keyframeEffect: {
                    opacity: ['0', '1'],
                    transform: ['translateX(-30px)', 'translateX(0)']
                },
                duration: 500,
                easing: 'cubic-bezier(0.16, 1, 0.3, 1)',
                delay: 200
            }
        ]
    }
]
```

---

## Advanced Patterns and Combinations

### ViewEnter with Animation Chaining
Using effectId to trigger subsequent animations:

```typescript
// Primary entrance
{
    source: '#section-container',
    trigger: 'viewEnter',
    params: {
        type: 'once',
        threshold: 0.3
    },
    effects: [
        {
            target: '#section-title',
            namedEffect: 'FadeIn',
            duration: 600,
            effectId: 'title-entrance'
        }
    ]
},
// Chained content animation
{
    source: '#section-title',
    trigger: 'animationEnd',
    params: {
        effectId: 'title-entrance'
    },
    effects: [
        {
            target: '#section-content',
            namedEffect: 'SlideIn',
            duration: 500,
            delay: 100
        }
    ]
}
```

### Multi-Effect ViewEnter
Animating multiple targets from single viewport trigger:

```typescript
{
    source: '#hero-trigger',
    trigger: 'viewEnter',
    params: {
        type: 'once',
        threshold: 0.2
    },
    effects: [
        {
            target: '#hero-background',
            keyframeEffect: {
                filter: ['blur(20px)', 'blur(0)'],
                transform: ['scale(1.1)', 'scale(1)']
            },
            duration: 1200,
            easing: 'ease-out'
        },
        {
            target: '#hero-title',
            namedEffect: 'SlideIn',
            duration: 800,
            delay: 300
        },
        {
            target: '#hero-subtitle',
            keyframeEffect: {
                opacity: ['0', '1'],
                transform: ['translateY(30px)', 'translateY(0)']
            },
            duration: 600,
            delay: 600
        },
        {
            target: '#hero-cta',
            transition: {
                duration: 400,
                delay: 900,
                styleProperties: [
                    { name: 'opacity', value: '1' },
                    { name: 'transform', value: 'translateY(0)' }
                ]
            }
        }
    ]
}
```

### Conditional ViewEnter Animations
Combining with conditions for responsive behavior:

```typescript
{
    source: '#responsive-section',
    trigger: 'viewEnter',
    params: {
        type: 'once',
        threshold: 0.5
    },
    conditions: ['desktop-only', 'prefers-motion'],
    effects: [
        {
            target: '#responsive-section',
            namedEffect: 'ComplexEntrance',
            duration: 1000
        }
    ]
},
// Simplified version for mobile/reduced motion
{
    source: '#responsive-section',
    trigger: 'viewEnter',
    params: {
        type: 'once',
        threshold: 0.7
    },
    conditions: ['mobile-only'],
    effects: [
        {
            target: '#responsive-section',
            namedEffect: 'FadeIn',
            duration: 400
        }
    ]
}
```

---

## Best Practices for ViewEnter Interactions

### Behavior Guildelines
1. **Use `alternate` and `repeat` types only with a separate `source` and `target`** to avoid re-triggering when animation starts or not triggering at all if animated target is out of viewport or clipped

### Performance Guidelines
1. **Use `once` type for entrance animations** to avoid repeated triggers
2. **Be careful with separate source/target patterns** - ensure source doesn't get clipped
3. **Use appropriate thresholds** - avoid triggering too early or too late

### User Experience Guidelines
1. **Use realistic thresholds** (0.1-0.5) for natural timing
2. **Use tiny thresholds for huge elements** 0.01-0.05 for elements much larger than viewport
3. **Provide adequate inset margins** for mobile viewports
4. **Keep entrance animations moderate** (500-1200ms)
5. **Use staggered delays thoughtfully** (50-200ms intervals)
6. **Ensure content is readable** during animations

### Accessibility Considerations
1. **Respect `prefers-reduced-motion`** for all entrance animations
2. **Don't rely solely on animations** to convey important information
3. **Ensure sufficient contrast** during fade-in effects
4. **Provide alternative content loading** for users who disable animations

### Threshold and Timing Guidelines

**Recommended Thresholds by Content Type**:
- **Hero sections**: 0.1-0.3 (early trigger)
- **Content blocks**: 0.3-0.5 (balanced trigger)
- **Small elements**: 0.5-0.8 (late trigger)
- **Tall sections**: 0.1-0.2 (early trigger)
- **HUge sections**: 0.01-0.05 (ensure trigger)

**Recommended Insets by Device**:
- **Desktop**: '-50px' to '-200px'
- **Mobile**: '-20px' to '-100px'
- **Positive insets**: '50px' for precise timing

### Common Use Cases by Pattern

**Once Pattern**:
- Hero section entrances
- Content block reveals
- Image lazy loading
- Feature introductions
- Call-to-action reveals

**Repeat Pattern**:
- Interactive counters
- Scroll-triggered galleries
- Progressive content loading
- Repeated call-to-actions
- Dynamic content sections

**Alternate Pattern**:
- Scroll-responsive UI elements
- Reversible content reveals
- Navigation state changes
- Context-sensitive helpers
- Progressive disclosure

**State Pattern**:
- Ambient animations
- Background effects
- Decorative elements
- Loading states
- Atmospheric content

**Staggered Animations**:
- Card grids and lists
- Team member sections
- Feature comparisons
- Product catalogs
- Timeline elements

### Troubleshooting Common Issues

**ViewEnter not triggering**:
- Check if source element is clipped by parent overflow
- Verify element exists when `Interact.create()` is called
- Ensure threshold and inset values are appropriate
- Check for conflicting CSS that might hide elements

**ViewEnter triggering multiple times**:
- Use `once` type for entrance animations
- Avoid animating the source element if it's also the target
- Consider using separate source and target elements

**Animation performance issues**:
- Limit concurrent viewEnter observers
- Use hardware-accelerated properties
- Avoid animating layout properties
- Consider using `will-change` for complex animations

---

These rules provide comprehensive coverage for ViewEnter trigger interactions in `@wix/interact`, supporting all four behavior types and various intersection observer configurations as outlined in the development plan.
