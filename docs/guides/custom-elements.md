# Custom Elements

The `<wix-interact-element>` custom element is the foundation of `@wix/interact`. This guide explains how it works, when to use it, and how to integrate it effectively into your applications.

## What is wix-interact-element?

`<wix-interact-element>` is a custom HTML element that serves as a bridge between your HTML content and the `@wix/interact` system. It:

- **Wraps your interactive content** to enable trigger detection
- **Manages element state** using CSS custom states
- **Handles lifecycle events** for adding/removing interactions
- **Provides a unique identifier** via `data-wix-path`

## Basic Usage

### HTML Structure
```html
<wix-interact-element data-wix-path="#my-button">
    <button id="my-button">Click me</button>
</wix-interact-element>
```

### React/JSX Usage
```tsx
import React from 'react';

function InteractiveButton() {
    return (
        <wix-interact-element data-wix-path="#my-button">
            <button id="my-button">Click me</button>
        </wix-interact-element>
    );
}
```

### Key Requirements
1. **Unique `data-wix-path`** - Must match your interaction source selector
2. **Child element** - The custom element must contain at least one child
3. **Valid CSS selector** - The path should be a valid CSS selector

## The data-wix-path Attribute

The `data-wix-path` attribute is crucial - it connects your HTML to your interaction configuration.

### Matching Selectors
The path must match your interaction's source selector:

```typescript
// Configuration
{
    source: '#my-button',  // This selector...
    trigger: 'hover',
    effects: [/* ... */]
}
```

```html
<!-- Must match this path -->
<wix-interact-element data-wix-path="#my-button">
    <button id="my-button">Hover me</button>
</wix-interact-element>
```

### Path Patterns
You can use any valid CSS selector:

```html
<!-- ID selector -->
<wix-interact-element data-wix-path="#unique-element">
    <div id="unique-element">Content</div>
</wix-interact-element>

<!-- Class selector -->
<wix-interact-element data-wix-path=".interactive-card">
    <div class="interactive-card">Card content</div>
</wix-interact-element>

<!-- Attribute selector -->
<wix-interact-element data-wix-path="[data-product='123']">
    <div data-product="123">Product card</div>
</wix-interact-element>

<!-- Complex selector -->
<wix-interact-element data-wix-path=".gallery .image:first-child">
    <div class="gallery">
        <img class="image" src="photo.jpg" alt="Photo" />
    </div>
</wix-interact-element>
```

## Element Lifecycle

When `<wix-interact-element>` connects to the DOM:

1. **Custom element registration** - Registers the element class if not already done
2. **Path validation** - Checks for valid `data-wix-path`
3. **Child validation** - Ensures at least one child element exists
4. **Interaction activation** - Calls `add()` to enable interactions when connected to DOM
5. **Automatic cleanup** - Calls `remove()` to clean up interactions when disconnected from DOM

## State Management

### CSS Custom States
`<wix-interact-element>` uses modern CSS custom states for effect management:

```css
/* Target elements in specific states */
wix-interact-element:state(hover-active) .my-element {
    transform: scale(1.1);
    transition: transform 0.2s ease;
}

wix-interact-element:state(clicked) .my-element {
    background-color: #blue;
}
```

### Legacy Browser Support
For browsers without custom state support, falls back to CSS classes:

```css
/* Legacy fallback */
wix-interact-element.--hover-active .my-element {
    transform: scale(1.1);
}
```

### State Toggle Methods
The element provides methods to manage states:

1. `add`: adds a state named by `effectId`
2. `remove`: removes a state named by `effectId`
3. `toggle`: toggles a state named by `effectId`
4. `clear`: removes all states

## Integration Patterns

### React Integration

TBD

### Vue Integration
```vue
<template>
    <wix-interact-element :data-wix-path="`#${elementId}`">
        <div :id="elementId" class="interactive-element">
            <slot />
        </div>
    </wix-interact-element>
</template>

<script>
import { Interact } from '@wix/interact';

export default {
    props: ['elementId'],
    mounted() {
        const config = {
            interactions: [
                {
                    source: `#${this.elementId}`,
                    trigger: 'hover',
                    effects: [
                        {
                            target: `#${this.elementId}`,
                            namedEffect: 'FadeIn',
                            duration: 300
                        }
                    ]
                }
            ]
        };
        
        Interact.create(config);
    }
};
</script>
```

### Vanilla JavaScript
```javascript
// Create element programmatically
function createInteractiveElement(id, content) {
    const wixElement = document.createElement('wix-interact-element');
    wixElement.dataset.wixPath = `#${id}`;
    
    const childElement = document.createElement('div');
    childElement.id = id;
    childElement.innerHTML = content;
    
    wixElement.appendChild(childElement);
    
    return wixElement;
}

// Usage
const interactive = createInteractiveElement('my-element', 'Interactive content');
document.body.appendChild(interactive);
```

## Advanced Usage

### Multiple Children
You can have multiple children, but only the first one is used for interaction targeting:

```html
<wix-interact-element data-wix-path="#main-target">
    <div id="main-target">Primary interactive element</div>
    <div>Supporting content (not interactive)</div>
    <span>Additional content</span>
</wix-interact-element>
```

### Nested wix-interact-elements
Nesting is supported for complex interactions:

```html
<wix-interact-element data-wix-path="#outer-container">
    <div id="outer-container">
        <wix-interact-element data-wix-path="#inner-button">
            <button id="inner-button">Nested interactive button</button>
        </wix-interact-element>
        <p>Other content in outer container</p>
    </div>
</wix-interact-element>
```

### Dynamic Path Updates
You can change the path dynamically:

```javascript
const element = document.querySelector('wix-interact-element');

// Remove old interactions
if (element.dataset.wixPath) {
    remove(element.dataset.wixPath);
}

// Update path
element.dataset.wixPath = '#new-target';

// Reconnect with new path
element.connect('#new-target');
```

## Styling wix-interact-element

### Default Display
The custom element has minimal default styling:

```css
wix-interact-element {
    display: contents; /* Doesn't affect layout */
}
```

### Custom Styling
You can style the element wrapper if needed:

```css
/* Make wrapper a block container */
wix-interact-element.card-wrapper {
    display: block;
    padding: 1rem;
    border-radius: 8px;
}

/* Style based on state */
wix-interact-element:state(hover-active) {
    background-color: rgba(0, 0, 0, 0.05);
}
```

## Troubleshooting

### Common Issues

#### "No path provided" Warning
```html
<!-- Wrong - missing data-wix-path -->
<wix-interact-element>
    <div>Content</div>
</wix-interact-element>

<!-- Correct -->
<wix-interact-element data-wix-path="#my-element">
    <div id="my-element">Content</div>
</wix-interact-element>
```

#### "No child element found" Warning
```html
<!-- Wrong - empty element -->
<wix-interact-element data-wix-path="#my-element"></wix-interact-element>

<!-- Correct -->
<wix-interact-element data-wix-path="#my-element">
    <div id="my-element">Content</div>
</wix-interact-element>
```

#### Path Selector Mismatch
```typescript
// Configuration uses class selector
{
    source: '.my-card',  // Class selector
    trigger: 'hover'
}
```

```html
<!-- Wrong - path doesn't match -->
<wix-interact-element data-wix-path="#my-card">
    <div class="my-card">Card</div>
</wix-interact-element>

<!-- Correct - path matches configuration -->
<wix-interact-element data-wix-path=".my-card">
    <div class="my-card">Card</div>
</wix-interact-element>
```

### Debugging Tips

#### Check Element Registration
```javascript
// Verify custom element is registered
console.log('wix-interact-element registered:', 
    customElements.get('wix-interact-element') !== undefined);
```

#### Inspect Element State
```javascript
const element = document.querySelector('wix-interact-element');
console.log('Connected:', element.connected);
console.log('Path:', element.dataset.wixPath);
console.log('Has child:', element.firstElementChild !== null);
```

#### Monitor State Changes
```javascript
const element = document.querySelector('wix-interact-element');
if (element._internals) {
    console.log('Current states:', Array.from(element._internals.states));
}
```

## Performance Considerations

### Element Creation
- Custom elements are lightweight but avoid creating thousands
- Use CSS selectors to target multiple elements with one interaction
- Consider using event delegation for dynamic content

### Memory Management
- Elements automatically clean up when removed from DOM
- For SPA routing, ensure proper cleanup during navigation
- Use `remove()` explicitly if needed for manual cleanup

## Browser Support

### Modern Browsers
- Adopted style sheets for dynamic CSS

## Best Practices

TBD

## Next Steps

Now that you understand custom elements:
- **[State Management](./state-management.md)** - Learn about CSS states vs data attributes
- **[Conditions and Media Queries](./conditions-and-media-queries.md)** - Create responsive interactions
- **[Understanding Triggers](./understanding-triggers.md)** - Master trigger types and behaviors