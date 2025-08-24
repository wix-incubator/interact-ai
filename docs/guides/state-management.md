# State Management

`@wix/interact` provides sophisticated state management through CSS custom states and data attributes. This guide explains when and how to use each approach for different interaction patterns.

## Overview of State Management

`@wix/interact` manages element state in two ways:

1. **CSS Custom States** - Modern approach using `ElementInternals.states`
2. **Data Attributes** - Fallback approach for legacy browsers

Both methods allow you to style elements based on their interaction state and create complex, stateful animations.

## CSS Custom States (Modern Approach)

### What are CSS Custom States?

CSS custom states are a modern web standard that allows custom elements to expose custom state information to CSS, similar to how built-in elements have states like `:hover`, `:focus`, or `:checked`.

### How wix-interact-element Uses States

```html
<wix-interact-element data-wix-path="#my-button">
    <button id="my-button">Click me</button>
</wix-interact-element>
```

When interactions trigger, the custom element enters specific states:

```css
/* Style based on interaction state */
wix-interact-element:state(clicked) #my-button {
    background-color: #10b981;
    transform: scale(0.95);
}

wix-interact-element:state(hover-active) #my-button {
    box-shadow: 0 10px 20px rgba(0,0,0,0.1);
}
```

### State Naming Patterns

States are automatically generated based on your effect IDs:

```typescript
{
    source: '#toggle-button',
    trigger: 'click',
    effects: [
        {
            target: '#sidebar',
            namedEffect: 'SlideIn',
            effectId: 'sidebar-toggle'  // Creates state: sidebar-toggle
        }
    ]
}
```

```css
/* Style when sidebar-toggle effect is active */
wix-interact-element:state(sidebar-toggle) #sidebar {
    transform: translateX(0);
}
```

### Real-World Example: Interactive Card

```typescript
// Configuration
const cardConfig = {
    interactions: [
        {
            source: '#product-card',
            trigger: 'hover',
            effects: [
                {
                    target: '#product-card',
                    effectId: 'card-hover',
                    keyframeEffect: {
                        transform: ['translateY(0)', 'translateY(-8px)']
                    },
                    duration: 200
                }
            ]
        },
        {
            source: '#product-card',
            trigger: 'click',
            effects: [
                {
                    target: '#product-details',
                    effectId: 'details-expand',
                    namedEffect: 'SlideDown',
                    duration: 300
                }
            ]
        }
    ]
};
```

```css
/* Corresponding CSS states */
wix-interact-element:state(card-hover) #product-card {
    box-shadow: 0 20px 40px rgba(0,0,0,0.15);
}

wix-interact-element:state(details-expand) #product-details {
    display: block;
    animation: slideDown 300ms ease-out;
}

/* Combine states for complex styling */
wix-interact-element:state(card-hover):state(details-expand) #product-card {
    border-color: #3b82f6;
}
```

## Data Attributes (Legacy Fallback)

### When Data Attributes are Used

For browsers that don't support CSS custom states, `@wix/interact` automatically falls back to data attributes:

```html
<!-- Modern browsers -->
<wix-interact-element data-wix-path="#my-element">
    <!-- Internal state: element._internals.states.has('my-effect') -->
</wix-interact-element>

<!-- Legacy browsers -->
<wix-interact-element 
    data-wix-path="#my-element"
    data-wix-interact-effect="my-effect active-state"
>
    <!-- States stored in data attribute -->
</wix-interact-element>
```

### CSS for Data Attributes

```css
/* Target data attribute states */
wix-interact-element[data-wix-interact-effect~="card-hover"] #product-card {
    transform: translateY(-8px);
    box-shadow: 0 20px 40px rgba(0,0,0,0.15);
}

wix-interact-element[data-wix-interact-effect~="details-expand"] #product-details {
    display: block;
}
```

### Cross-Browser CSS

Write CSS that works in both modern and legacy browsers:

```css
/* Modern browsers - CSS custom states */
wix-interact-element:state(hover-active) .my-element,
/* Legacy browsers - data attributes */
wix-interact-element[data-wix-interact-effect~="hover-active"] .my-element {
    transform: scale(1.05);
    transition: transform 0.2s ease;
}
```

## State-Based Animation Patterns

### Toggle States

Create toggle behaviors using alternating states:

```typescript
{
    source: '#menu-button',
    trigger: 'click',
    params: { type: 'alternate' },
    effects: [
        {
            target: '#mobile-menu',
            effectId: 'menu-open',
            keyframeEffect: {
                transform: ['translateX(-100%)', 'translateX(0)'],
                opacity: ['0', '1']
            },
            duration: 300
        }
    ]
}
```

```css
/* Menu closed state (default) */
#mobile-menu {
    transform: translateX(-100%);
    opacity: 0;
    transition: all 0.3s ease;
}

/* Menu open state */
wix-interact-element:state(menu-open) #mobile-menu {
    transform: translateX(0);
    opacity: 1;
}
```

### Multi-State Elements

Manage multiple states on the same element:

```typescript
const buttonStates = {
    interactions: [
        // Hover state
        {
            source: '#multi-state-btn',
            trigger: 'hover',
            effects: [
                {
                    target: '#multi-state-btn',
                    effectId: 'btn-hover',
                    duration: 200
                }
            ]
        },
        // Click state
        {
            source: '#multi-state-btn',
            trigger: 'click',
            effects: [
                {
                    target: '#multi-state-btn',
                    effectId: 'btn-clicked',
                    duration: 150
                }
            ]
        },
        // Loading state (triggered by custom event)
        {
            source: '#multi-state-btn',
            trigger: 'animationEnd',
            params: { effectId: 'btn-clicked' },
            effects: [
                {
                    target: '#multi-state-btn',
                    effectId: 'btn-loading',
                    duration: 1000
                }
            ]
        }
    ]
};
```

```css
/* Base button styles */
#multi-state-btn {
    background: #3b82f6;
    color: white;
    transition: all 0.2s ease;
}

/* Hover state */
wix-interact-element:state(btn-hover) #multi-state-btn {
    background: #2563eb;
    transform: translateY(-2px);
}

/* Click state */
wix-interact-element:state(btn-clicked) #multi-state-btn {
    transform: scale(0.95);
}

/* Loading state */
wix-interact-element:state(btn-loading) #multi-state-btn {
    background: #6b7280;
    cursor: wait;
}

wix-interact-element:state(btn-loading) #multi-state-btn::after {
    content: '';
    width: 16px;
    height: 16px;
    border: 2px solid transparent;
    border-top: 2px solid currentColor;
    border-radius: 50%;
    animation: spin 1s linear infinite;
}

@keyframes spin {
    to { transform: rotate(360deg); }
}
```

### State Persistence

Some states should persist across interactions:

```typescript
// Create persistent state
{
    source: '#theme-toggle',
    trigger: 'click',
    params: { type: 'alternate' },
    effects: [
        {
            target: 'body',
            effectId: 'dark-theme',
            transition: {
                duration: 400,
                styleProperties: [
                    { name: '--bg-color', value: '#1a1a1a' },
                    { name: '--text-color', value: '#ffffff' }
                ]
            }
        }
    ]
}
```

```css
/* Light theme (default) */
body {
    --bg-color: #ffffff;
    --text-color: #000000;
    background-color: var(--bg-color);
    color: var(--text-color);
    transition: background-color 0.4s, color 0.4s;
}

/* Dark theme persists until toggled again */
wix-interact-element:state(dark-theme) body {
    --bg-color: #1a1a1a;
    --text-color: #ffffff;
}
```

## Advanced State Management

### Programmatic State Control

Access and control states directly:

```javascript
const interactElement = document.querySelector('wix-interact-element');

// Check if state is active
function hasState(stateName) {
    if (interactElement._internals) {
        return interactElement._internals.states.has(stateName);
    } else {
        const effects = interactElement.dataset.wixInteractEffect || '';
        return effects.split(' ').includes(stateName);
    }
}

// Manually toggle state
function toggleState(stateName) {
    interactElement.toggleEffect(stateName, 'toggle');
}

// Clear all states
function clearAllStates() {
    interactElement.toggleEffect('', 'clear');
}
```

### State Change Events

Listen for state changes:

```javascript
// Custom event when state changes
interactElement.addEventListener('statechange', (event) => {
    console.log('State changed:', event.detail);
});

// MutationObserver for data attribute changes
const observer = new MutationObserver((mutations) => {
    mutations.forEach((mutation) => {
        if (mutation.attributeName === 'data-wix-interact-effect') {
            const newStates = mutation.target.dataset.wixInteractEffect?.split(' ') || [];
            console.log('States updated:', newStates);
        }
    });
});

observer.observe(interactElement, {
    attributes: true,
    attributeFilter: ['data-wix-interact-effect']
});
```

### State-Based Conditional Logic

Use states to control other interactions:

```typescript
const conditionalConfig = {
    conditions: {
        'menu-open': {
            type: 'custom',
            predicate: () => {
                const element = document.querySelector('wix-interact-element');
                return hasState('menu-open');
            }
        }
    },
    interactions: [
        // Close menu when clicking outside
        {
            source: 'body',
            trigger: 'click',
            conditions: ['menu-open'],
            effects: [
                {
                    target: '#mobile-menu',
                    effectId: 'menu-close',
                    namedEffect: 'SlideOut',
                    duration: 300
                }
            ]
        }
    ]
};
```

## State Management Patterns

### State Machine Pattern

Implement state machines for complex interactions:

```typescript
// Define possible states
type LoadingState = 'idle' | 'loading' | 'success' | 'error';

const loadingStates: Record<LoadingState, Effect> = {
    idle: {
        transition: {
            duration: 200,
            styleProperties: [
                { name: 'backgroundColor', value: '#3b82f6' },
                { name: 'cursor', value: 'pointer' }
            ]
        }
    },
    loading: {
        transition: {
            duration: 200,
            styleProperties: [
                { name: 'backgroundColor', value: '#6b7280' },
                { name: 'cursor', value: 'wait' }
            ]
        }
    },
    success: {
        transition: {
            duration: 400,
            styleProperties: [
                { name: 'backgroundColor', value: '#10b981' }
            ]
        }
    },
    error: {
        transition: {
            duration: 400,
            styleProperties: [
                { name: 'backgroundColor', value: '#ef4444' }
            ]
        }
    }
};

// State transitions
function transitionToState(newState: LoadingState) {
    const element = document.querySelector('wix-interact-element');
    
    // Clear previous states
    Object.keys(loadingStates).forEach(state => {
        element.toggleEffect(state, 'remove');
    });
    
    // Apply new state
    element.toggleEffect(newState, 'add');
}
```

### Cascading States

Create hierarchical state relationships:

```css
/* Base state */
wix-interact-element:state(card-active) .card {
    border-color: #3b82f6;
}

/* Nested state - only when parent state is active */
wix-interact-element:state(card-active):state(details-visible) .card-details {
    opacity: 1;
    transform: translateY(0);
}

/* Complex state combinations */
wix-interact-element:state(card-active):state(hover-active):not(:state(details-visible)) .card-preview {
    display: block;
}
```

### State History

Track state changes over time:

```javascript
class StateHistory {
    constructor(element) {
        this.element = element;
        this.history = [];
        this.maxHistory = 10;
        
        this.observeStateChanges();
    }
    
    observeStateChanges() {
        const observer = new MutationObserver(() => {
            const currentStates = this.getCurrentStates();
            this.addToHistory(currentStates);
        });
        
        observer.observe(this.element, {
            attributes: true,
            attributeFilter: ['data-wix-interact-effect']
        });
    }
    
    getCurrentStates() {
        if (this.element._internals) {
            return Array.from(this.element._internals.states);
        } else {
            return this.element.dataset.wixInteractEffect?.split(' ') || [];
        }
    }
    
    addToHistory(states) {
        this.history.push({
            states: [...states],
            timestamp: Date.now()
        });
        
        if (this.history.length > this.maxHistory) {
            this.history.shift();
        }
    }
    
    getHistory() {
        return this.history;
    }
    
    revertToState(index) {
        const targetState = this.history[index];
        if (targetState) {
            // Clear current states
            this.element.toggleEffect('', 'clear');
            
            // Apply target states
            targetState.states.forEach(state => {
                this.element.toggleEffect(state, 'add');
            });
        }
    }
}

// Usage
const stateHistory = new StateHistory(document.querySelector('wix-interact-element'));
```

## Performance Considerations

### State Update Frequency

Be mindful of how often states change:

```css
/* Efficient - hardware accelerated properties */
wix-interact-element:state(moving) .element {
    transform: translateX(var(--x));
    will-change: transform;
}

/* Avoid - causes layout recalculation */
wix-interact-element:state(moving) .element {
    left: var(--x);
    width: calc(100% - var(--x));
}
```

### State Cleanup

Ensure states are properly cleaned up:

```javascript
// Clean up when component unmounts
function cleanup() {
    const element = document.querySelector('wix-interact-element');
    if (element) {
        element.toggleEffect('', 'clear');
    }
}

// React useEffect cleanup
useEffect(() => {
    return cleanup;
}, []);
```

## Browser Compatibility

### Feature Detection

Check for custom state support:

```javascript
function supportsCustomStates() {
    try {
        const element = document.createElement('test-element');
        if (element.attachInternals) {
            const internals = element.attachInternals();
            return 'states' in internals;
        }
    } catch (e) {
        return false;
    }
    return false;
}

// Use appropriate CSS based on support
if (supportsCustomStates()) {
    // Use :state() selectors
} else {
    // Use [data-wix-interact-effect] selectors
}
```

### Polyfills

For older browsers, consider using polyfills:

```javascript
// Simple custom states polyfill
if (!('states' in ElementInternals.prototype)) {
    ElementInternals.prototype.states = {
        add(state) {
            const effects = this.element.dataset.wixInteractEffect?.split(' ') || [];
            if (!effects.includes(state)) {
                effects.push(state);
                this.element.dataset.wixInteractEffect = effects.join(' ');
            }
        },
        delete(state) {
            const effects = this.element.dataset.wixInteractEffect?.split(' ') || [];
            const index = effects.indexOf(state);
            if (index > -1) {
                effects.splice(index, 1);
                this.element.dataset.wixInteractEffect = effects.join(' ');
            }
        },
        has(state) {
            const effects = this.element.dataset.wixInteractEffect?.split(' ') || [];
            return effects.includes(state);
        },
        clear() {
            this.element.dataset.wixInteractEffect = '';
        }
    };
}
```

## Best Practices

### State Naming
- Use descriptive, action-based names: `menu-open`, `form-submitted`, `card-expanded`
- Avoid generic names: `active`, `on`, `state1`
- Be consistent with naming conventions across your application

### CSS Organization
```css
/* Group states logically */
/* === Base States === */
wix-interact-element:state(hover-active) .button { }
wix-interact-element:state(focus-visible) .button { }

/* === Action States === */
wix-interact-element:state(clicked) .button { }
wix-interact-element:state(submitting) .form { }

/* === Status States === */
wix-interact-element:state(success) .notification { }
wix-interact-element:state(error) .notification { }
```

### Performance
- Use CSS transforms and opacity for smooth animations
- Minimize state changes during high-frequency events
- Use `will-change` for elements that frequently change state

## Next Steps

Now that you understand state management:
- **[Conditions and Media Queries](./conditions-and-media-queries.md)** - Create responsive interactions
- **[Understanding Triggers](./understanding-triggers.md)** - Learn about trigger behaviors
- **[Effects and Animations](./effects-and-animations.md)** - Master animation techniques