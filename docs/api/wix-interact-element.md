# wix-interact-element Custom Element

The `wix-interact-element` is a custom HTML element that serves as a wrapper for content that should have interactions applied to it. It automatically manages the connection between DOM elements and the `@wix/interact` system.

## Basic Usage

```html
<wix-interact-element data-wix-path="#unique-id">
  <div class="content">Your interactive content here</div>
</wix-interact-element>
```

```typescript
import { Interact } from '@wix/interact';

// Configure interactions for the element
const config = {
  interactions: [{
    trigger: 'viewEnter',
    source: '#unique-id',
    effects: [{ effectId: 'fade-in' }]
  }],
  effects: {
    'fade-in': {
      duration: 1000,
      keyframeEffect: { opacity: [0, 1] }
    }
  }
};

Interact.create(config);
```

## HTML Usage

### Basic Structure

```html
<!-- Minimal structure -->
<wix-interact-element data-wix-path="#my-element">
  <div>Content to animate</div>
</wix-interact-element>
```

### Required Attributes

#### `data-wix-path`

**Required:** Yes  
**Type:** `string`  
**Description:** Unique identifier matching the interaction configuration.

**Example:**
```html
<wix-interact-element data-wix-path="#hero">
  <div>Hero content</div>
</wix-interact-element>
```

### Child Element Requirements

The custom element's first element child **must** be the event/animation target element:

```html
<!-- ✅ Correct: Single child element -->
<wix-interact-element data-wix-path="#hero">
  <div class="hero-content">
    <h1>Title</h1>
    <p>Description</p>
  </div>
</wix-interact-element>

<!-- ❌ Incorrect: No child element -->
<wix-interact-element data-wix-path="#empty">
</wix-interact-element>

<!-- ❌ Incorrect: Multiple child elements -->
<wix-interact-element data-wix-path="#multiple">
  <div>First child</div>
  <div>Second child</div>
</wix-interact-element>

<!-- ❌ Incorrect: Text node only -->
<wix-interact-element data-wix-path="#text">
  Just text content
</wix-interact-element>
```

## Framework Integration

### React

```tsx
import React from 'react';

// Basic React usage
function InteractiveComponent() {
  return (
    <wix-interact-element data-wix-path="#react-component">
      <div className="content">
        <h2>React Component</h2>
        <p>This will be animated</p>
      </div>
    </wix-interact-element>
  );
}

// TypeScript with proper typing
import { IWixInteractElement } from '@wix/interact';

function TypedComponent() {
  const ref = React.useRef<IWixInteractElement>(null);
  
  const handleClick = () => {
    ref.current?.toggleEffect('clicked', 'toggle');
  };
  
  return (
    <wix-interact-element 
      ref={ref}
      data-wix-path="#typed-component"
    >
      <button onClick={handleClick}>
        Toggle Effect
      </button>
    </wix-interact-element>
  );
}
```

### Vue.js

```vue
<template>
  <wix-interact-element 
    :data-wix-path="elementPath"
    ref="interactElement"
  >
    <div class="vue-content">
      <h2>{{ title }}</h2>
      <button @click="toggleEffect">Toggle</button>
    </div>
  </wix-interact-element>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import type { IWixInteractElement } from '@wix/interact';

const elementPath = ref('#vue-component');
const title = ref('Vue Component');
const interactElement = ref<IWixInteractElement>();

const toggleEffect = () => {
  interactElement.value?.toggleEffect('active', 'toggle');
};
</script>
```

### Angular

```typescript
import { Component, ElementRef, ViewChild, AfterViewInit } from '@angular/core';
import type { IWixInteractElement } from '@wix/interact';

@Component({
  selector: 'app-interactive',
  template: `
    <wix-interact-element 
      #interactElement
      data-wix-path="#angular-component"
    >
      <div class="angular-content">
        <h2>{{ title }}</h2>
        <button (click)="toggleEffect()">Toggle</button>
      </div>
    </wix-interact-element>
  `
})

export class InteractiveComponent implements AfterViewInit {
  @ViewChild('interactElement') 
  interactElement!: ElementRef<IWixInteractElement>;
  
  title = 'Angular Component';
  
  ngAfterViewInit() {
    // Element is ready for interaction
    console.log('Connected:', this.interactElement.nativeElement.connected);
  }
  
  toggleEffect() {
    this.interactElement.nativeElement.toggleEffect('active', 'toggle');
  }
}
```

## State Management

### Modern CSS States

When `ElementInternals` is supported, effects use modern CSS custom states:

```css
/* Modern syntax */
wix-interact-element:state(hover-effect) > :first-child {
  transform: scale(1.05);
}

wix-interact-element:state(active) > :first-child {
  background-color: blue;
}
```

### Legacy Fallback

For older browsers, the element falls back to CSS custom properties:

```css
/* Legacy syntax */
wix-interact-element:--hover-effect > :first-child {
  transform: scale(1.05);
}

/* Or data attribute fallback */
wix-interact-element[data-wix-interact-effect~="hover-effect"] > :first-child {
  transform: scale(1.05);
}
```

### Programmatic State Control

```typescript
const element = document.querySelector('wix-interact-element') as IWixInteractElement;

// Add multiple states
element.toggleEffect('hover', 'add');
element.toggleEffect('focus', 'add');
element.toggleEffect('disabled', 'add');

// Check current states
if (element._internals) {
  console.log('Active states:', Array.from(element._internals.states));
} else {
  const effectAttr = element.dataset.wixInteractEffect;
  console.log('Active effects:', effectAttr?.split(' ') || []);
}

// Clear all states
element.toggleEffect('', 'clear');
```

## Performance Considerations

### Efficient Element Usage

```html
<!-- ✅ Good: Minimal nesting -->
<wix-interact-element data-wix-path="#efficient">
  <div class="content">Content here</div>
</wix-interact-element>

<!-- ❌ Avoid: Unnecessary nesting -->
<div class="wrapper">
  <wix-interact-element data-wix-path="#nested">
    <div class="inner-wrapper">
      <div class="content">Content here</div>
    </div>
  </wix-interact-element>
</div>
```

### Dynamic Creation

```typescript
// Efficient dynamic element creation
function createInteractiveElement(path: string, content: HTMLElement): IWixInteractElement {
  const wrapper = document.createElement('wix-interact-element') as IWixInteractElement;
  wrapper.setAttribute('data-wix-path', path);
  wrapper.appendChild(content);
  
  return wrapper;
}

// Usage
const content = document.createElement('div');
content.textContent = 'Dynamic content';

const interactive = createInteractiveElement('#dynamic', content);
document.body.appendChild(interactive);
// Interactions automatically connect when added to DOM
```

## Browser Support

### Modern Browsers
- ✅ Full feature support including CSS custom states
- ✅ ElementInternals API for state management
- ✅ Adopted stylesheets for dynamic CSS

### Legacy Browsers
- ✅ Automatic fallback to data attributes
- ✅ CSS custom property fallbacks
- ✅ Polyfill support for custom elements

### Detection

```typescript
function checkSupport() {
  const element = document.createElement('wix-interact-element') as IWixInteractElement;
  
  return {
    customElements: !!window.customElements,
    elementInternals: !!element.attachInternals,
    adoptedStyleSheets: !!document.adoptedStyleSheets,
    cssCustomStates: !!element._internals?.states
  };
}

const support = checkSupport();
console.log('Browser support:', support);
```

## Debugging

### Element Inspection

```typescript
// Debug element state
function debugElement(path: string) {
  const element = Interact.getElement(path);
  
  if (!element) {
    console.log(`Element ${path} not found in cache`);
    return;
  }
  
  console.log(`Element ${path}:`, {
    connected: element.connected,
    hasChild: !!element.firstElementChild,
    hasSheet: !!element.sheet,
    hasInternals: !!element._internals,
    states: element._internals ? 
      Array.from(element._internals.states) : 
      element.dataset.wixInteractEffect?.split(' ') || []
  });
}

// Usage
debugElement('#hero');
```

### Common Issues

```typescript
// Check for common problems
function validateElement(element: IWixInteractElement) {
  const issues: string[] = [];
  
  if (!element.dataset.wixPath) {
    issues.push('Missing data-wix-path attribute');
  }
  
  if (!element.firstElementChild) {
    issues.push('Missing child element');
  }
  
  if (element.children.length > 1) {
    issues.push('Multiple child elements found');
  }
  
  if (!element.connected) {
    issues.push('Element not connected to interaction system');
  }
  
  return issues;
}
```

## See Also

- [Interact Class](interact-class.md) - Main interaction manager
- [Functions](functions.md) - `add()` and `remove()` functions  
- [Type Definitions](types.md) - `IWixInteractElement` interface
- [Configuration Guide](../guides/configuration.md) - Setting up interactions
- [Getting Started](../guides/getting-started.md) - Basic usage examples