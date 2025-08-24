# Click Trigger Rules for @wix/interact

These rules help generate click-based interactions using the `@wix/interact` library. Click triggers respond to mouse click events and support multiple behavior patterns for different user experience needs.

## Rule 1: Click with TimeEffect and Alternate Pattern

**Use Case**: Toggle animations that play forward on first click and reverse on subsequent clicks (e.g., menu toggles, accordion expand/collapse, modal open/close)

**When to Apply**: 
- When you need reversible animations
- For toggle states that should animate back to original position
- When creating expand/collapse functionality
- For modal or sidebar open/close animations

**Pattern**:
```typescript
{
    source: '[TRIGGER_SELECTOR]',
    trigger: 'click',
    params: {
        type: 'alternate'
    },
    effects: [
        {
            target: '[TARGET_SELECTOR]',
            [EFFECT_TYPE]: [EFFECT_DEFINITION],
            duration: [DURATION_MS],
            easing: '[EASING_FUNCTION]',
            effectId: '[UNIQUE_EFFECT_ID]'
        }
    ]
}
```

**Variables**:
- `[TRIGGER_SELECTOR]`: CSS selector for clickable element (e.g., '#menu-button', '.toggle-btn', '#accordion-header')
- `[TARGET_SELECTOR]`: CSS selector for animated element (can be same as trigger or different)
- `[EFFECT_TYPE]`: Either `namedEffect` or `keyframeEffect`
- `[EFFECT_DEFINITION]`: Named effect string (e.g., 'SlideIn', 'FadeIn') or keyframe object
- `[DURATION_MS]`: Animation duration in milliseconds (typically 200-500ms for clicks)
- `[EASING_FUNCTION]`: Timing function ('ease-out', 'ease-in-out', or cubic-bezier)
- `[UNIQUE_EFFECT_ID]`: Optional unique identifier for animation chaining

**Example - Menu Toggle**:
```typescript
{
    source: '#hamburger-menu',
    trigger: 'click',
    params: {
        type: 'alternate'
    },
    effects: [
        {
            target: '#mobile-nav',
            keyframeEffect: {
                transform: ['translateX(-100%)', 'translateX(0)'],
                opacity: ['0', '1']
            },
            duration: 300,
            easing: 'ease-out',
            effectId: 'mobile-nav-toggle'
        }
    ]
}
```

**Example - Accordion Expand**:
```typescript
{
    source: '.accordion-header',
    trigger: 'click',
    params: {
        type: 'alternate'
    },
    effects: [
        {
            target: '.accordion-content',
            keyframeEffect: {
                clipPath: ['inset(0 0 100% 0)', 'inset(0 0 0 0)'],
                opacity: ['0', '1']
            },
            duration: 400,
            easing: 'ease-in-out'
        }
    ]
}
```

---

## Rule 2: Click with TimeEffect and State Pattern

**Use Case**: Animations that can be paused and resumed with clicks (e.g., video controls, loading animations, slideshow controls)

**When to Apply**:
- When you need play/pause functionality
- For controlling ongoing animations
- When users should be able to interrupt and resume animations
- For interactive media controls

**Pattern**:
```typescript
{
    source: '[TRIGGER_SELECTOR]',
    trigger: 'click',
    params: {
        type: 'state'
    },
    effects: [
        {
            target: '[TARGET_SELECTOR]',
            [EFFECT_TYPE]: [EFFECT_DEFINITION],
            duration: [DURATION_MS],
            easing: '[EASING_FUNCTION]',
            iterations: [ITERATION_COUNT],
            effectId: '[UNIQUE_EFFECT_ID]'
        }
    ]
}
```

**Variables**:
- `[ITERATION_COUNT]`: Number of iterations or Infinity for looping animations
- Other variables same as Rule 1

**Example - Loading Spinner Control**:
```typescript
{
    source: '#loading-control',
    trigger: 'click',
    params: {
        type: 'state'
    },
    effects: [
        {
            target: '#spinner',
            keyframeEffect: {
                transform: ['rotate(0deg)', 'rotate(360deg)']
            },
            duration: 1000,
            easing: 'linear',
            iterations: Infinity,
            effectId: 'spinner-rotation'
        }
    ]
}
```

**Example - Slideshow Pause**:
```typescript
{
    source: '#slideshow-toggle',
    trigger: 'click',
    params: {
        type: 'state'
    },
    effects: [
        {
            target: '#slideshow-container',
            namedEffect: 'SlideIn',
            duration: 3000,
            iterations: Infinity,
            alternate: true,
            effectId: 'slideshow-animation'
        }
    ]
}
```

---

## Rule 3: Click with TimeEffect and Repeat Pattern

**Use Case**: Animations that restart from the beginning each time clicked (e.g., pulse effects, notification badges, emphasis animations)

**When to Apply**:
- When you want fresh animation on each click
- For attention-grabbing effects
- When animation should always start from initial state
- For feedback animations that confirm user actions

**Pattern**:
```typescript
{
    source: '[TRIGGER_SELECTOR]',
    trigger: 'click',
    params: {
        type: 'repeat'
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
- `[DELAY_MS]`: Optional delay before animation starts (useful for sequencing)
- Other variables same as Rule 1

**Example - Button Pulse Feedback**:
```typescript
{
    source: '#action-button',
    trigger: 'click',
    params: {
        type: 'repeat'
    },
    effects: [
        {
            target: '#action-button',
            keyframeEffect: {
                transform: ['scale(1)', 'scale(1.1)', 'scale(1)'],
                boxShadow: [
                    '0 2px 4px rgba(0,0,0,0.1)', 
                    '0 8px 16px rgba(0,0,0,0.2)', 
                    '0 2px 4px rgba(0,0,0,0.1)'
                ]
            },
            duration: 300,
            easing: 'ease-out'
        }
    ]
}
```

**Example - Success Notification**:
```typescript
{
    source: '#save-button',
    trigger: 'click',
    params: {
        type: 'repeat'
    },
    effects: [
        {
            target: '#success-badge',
            namedEffect: 'BounceIn',
            duration: 600,
            easing: 'cubic-bezier(0.34, 1.56, 0.64, 1)',
            effectId: 'success-feedback'
        }
    ]
}
```

---

## Rule 4: Click with State Toggles and TransitionEffects

**Use Case**: CSS property changes that toggle between states (e.g., theme switching, style variations, color changes)

**When to Apply**:
- When animating CSS properties directly
- For theme toggles and style switches
- When you need precise control over CSS transitions
- For simple property changes without complex keyframes

**Pattern**:
```typescript
{
    source: '[TRIGGER_SELECTOR]',
    trigger: 'click',
    params: {
        type: 'alternate'
    },
    effects: [
        {
            target: '[TARGET_SELECTOR]',
            transition: {
                duration: [DURATION_MS],
                delay: [DELAY_MS],
                easing: '[EASING_FUNCTION]',
                styleProperties: [
                    { name: '[CSS_PROPERTY_1]', value: '[VALUE_1]' },
                    { name: '[CSS_PROPERTY_2]', value: '[VALUE_2]' }
                ]
            },
            effectId: '[UNIQUE_EFFECT_ID]'
        }
    ]
}
```

**Variables**:
- `[CSS_PROPERTY_N]`: CSS property name (e.g., 'backgroundColor', 'color', 'borderRadius')
- `[VALUE_N]`: CSS property value (e.g., '#2563eb', 'white', '12px')
- Other variables same as previous rules

**Example - Theme Toggle**:
```typescript
{
    source: '#theme-switcher',
    trigger: 'click',
    params: {
        type: 'alternate'
    },
    effects: [
        {
            target: 'body',
            transition: {
                duration: 400,
                easing: 'ease-in-out',
                styleProperties: [
                    { name: '--bg-color', value: '#1a1a1a' },
                    { name: '--text-color', value: '#ffffff' },
                    { name: '--accent-color', value: '#3b82f6' },
                    { name: '--border-color', value: '#374151' }
                ]
            },
            effectId: 'theme-switch'
        }
    ]
}
```

**Example - Button Style Toggle**:
```typescript
{
    source: '#style-toggle',
    trigger: 'click',
    params: {
        type: 'alternate'
    },
    effects: [
        {
            target: '#style-toggle',
            transition: {
                duration: 300,
                easing: 'ease-out',
                styleProperties: [
                    { name: 'backgroundColor', value: '#ef4444' },
                    { name: 'color', value: '#ffffff' },
                    { name: 'borderRadius', value: '24px' },
                    { name: 'transform', value: 'scale(1.05)' }
                ]
            }
        }
    ]
}
```

**Example - Card State Toggle**:
```typescript
{
    source: '.interactive-card',
    trigger: 'click',
    params: {
        type: 'alternate'
    },
    effects: [
        {
            target: '.interactive-card',
            transition: {
                duration: 250,
                easing: 'ease-in-out',
                styleProperties: [
                    { name: 'backgroundColor', value: '#f3f4f6' },
                    { name: 'borderColor', value: '#2563eb' },
                    { name: 'boxShadow', value: '0 20px 25px rgba(0,0,0,0.15)' }
                ]
            }
        }
    ]
}
```

---

## Advanced Patterns and Combinations

### Multi-Target Click Effects
When one click should animate multiple elements:

```typescript
{
    source: '#master-control',
    trigger: 'click',
    params: {
        type: 'alternate'
    },
    effects: [
        {
            target: '#element-1',
            namedEffect: 'FadeIn',
            duration: 300,
            delay: 0
        },
        {
            target: '#element-2',
            namedEffect: 'SlideIn',
            duration: 400,
            delay: 100
        },
        {
            target: '#element-3',
            transition: {
                duration: 200,
                delay: 200,
                styleProperties: [
                    { name: 'opacity', value: '1' }
                ]
            }
        }
    ]
}
```

### Click with Animation Chaining
Using effectId for sequential animations:

```typescript
// First click animation
{
    source: '#sequence-trigger',
    trigger: 'click',
    params: {
        type: 'once'
    },
    effects: [
        {
            target: '#first-element',
            namedEffect: 'FadeIn',
            duration: 500,
            effectId: 'first-fade'
        }
    ]
},
// Chained animation
{
    source: '#first-element',
    trigger: 'animationEnd',
    params: {
        effectId: 'first-fade'
    },
    effects: [
        {
            target: '#second-element',
            namedEffect: 'SlideIn',
            duration: 400,
            effectId: 'second-slide'
        }
    ]
}
```

---

## Best Practices for Click Interactions

### Performance Guidelines
1. **Keep click animations short** (100-500ms) for immediate feedback
2. **Use `transform` and `opacity`** for smooth animations
3. **Avoid animating layout properties** like width/height in clicks
4. **Consider using `will-change`** for complex click animations

### User Experience Guidelines
1. **Provide immediate visual feedback** (within 100ms)
2. **Use alternate pattern** for toggle states
3. **Use repeat pattern** for confirmation actions
4. **Use state pattern** for media controls
5. **Ensure click targets are accessible** (minimum 44px touch target)

### Accessibility Considerations
1. **Respect `prefers-reduced-motion`** setting
2. **Provide alternative interaction methods** (keyboard support)
3. **Ensure sufficient color contrast** during transitions
4. **Don't rely solely on animation** to convey state changes

### Common Use Cases by Pattern

**Alternate Pattern**:
- Navigation menus
- Accordion sections
- Modal dialogs
- Sidebar toggles
- Dropdown menus

**State Pattern**:
- Video/audio controls
- Loading animations
- Slideshow controls
- Progress indicators

**Repeat Pattern**:
- Action confirmations
- Notification badges
- Button feedback
- Success animations
- Error indicators

**Transition Effects**:
- Theme switching
- Style variations
- Color changes
- Simple state toggles
- CSS custom property updates

---

These rules provide comprehensive coverage for click trigger interactions in `@wix/interact`, supporting the four main behavior patterns and two primary effect types as outlined in the development plan.
