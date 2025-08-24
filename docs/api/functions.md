# Standalone Functions

The `@wix/interact` package exports two key standalone functions for managing interactions at the element level: `add()` and `remove()`. These functions work with `wix-interact-element` custom elements to apply and remove interactions.

## Import

```typescript
import { add, remove } from '@wix/interact';
```

## Functions Overview

| Function | Purpose | Parameters | Returns |
|----------|---------|------------|---------|
| `add()` | Add interactions to an element | `element`, `path` | `boolean` |
| `remove()` | Remove interactions from an element | `path` | `void` |

---

## `add(element, path)`

Adds all configured interactions and effects to a `wix-interact-element` based on its path configuration.

### Signature

```typescript
function add(element: IWixInteractElement, path: string): boolean
```

### Parameters

**`element: IWixInteractElement`**
- The custom `wix-interact-element` that wraps the target content
- Must contain a child element that will be the actual target of animations
- Should have `data-wix-path` attribute matching the `path` parameter

**`path: string`**
- The unique identifier for the element in the interaction configuration
- Must match a path defined in an `Interact` instance's configuration
- Used to look up relevant interactions and effects

### Returns

**`boolean`**
- `true` if interactions were successfully added (element has triggers or effects)
- `false` if no interactions were found for this path

### Examples

#### Basic Usage
```typescript
import { Interact, add } from '@wix/interact';

// Create interaction configuration
const config = {
  interactions: [{
    trigger: 'viewEnter',
    source: '#hero',
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

// Get the element and add interactions
const element = document.querySelector('wix-interact-element[data-wix-path="#hero"]');
const hasInteractions = add(element as IWixInteractElement, '#hero');

console.log('Interactions added:', hasInteractions);
```

#### Programmatic Element Creation
```typescript
// Create element programmatically
const wixElement = document.createElement('wix-interact-element');
wixElement.setAttribute('data-wix-path', '#dynamic');

const content = document.createElement('div');
content.textContent = 'Animated content';
wixElement.appendChild(content);

document.body.appendChild(wixElement);

// Add interactions
const success = add(wixElement as IWixInteractElement, '#dynamic');
if (success) {
  console.log('Dynamic interactions added successfully');
}
```

### Behavior Details

#### What `add()` Does:
1. **Caches the Element**: Stores the element in `Interact.elementCache` for future reference
2. **Finds Configuration**: Looks up the interaction configuration for the given path
3. **Registers Triggers**: Sets up event listeners for all configured triggers (hover, click, etc.)
4. **Applies Effects**: Registers effects that target this element from other sources
5. **Handles Conditions**: Evaluates media queries and conditions to determine which interactions to activate
6. **Prevents Duplicates**: Tracks added interactions to avoid duplicate registrations

#### Element Requirements:
```html
<!-- ✅ Correct structure -->
<wix-interact-element data-wix-path="#hero">
  <div class="hero-content">Content to animate</div>
</wix-interact-element>

<!-- ❌ Missing child element -->
<wix-interact-element data-wix-path="#hero">
  <!-- No child element - will log warning -->
</wix-interact-element>

<!-- ❌ Missing data-wix-path -->
<wix-interact-element>
  <div>Content</div>
</wix-interact-element>
```

#### Error Handling:
```typescript
// The function handles various error cases gracefully
const element = document.querySelector('wix-interact-element');

// Missing path
add(element as IWixInteractElement, ''); 
// Logs: "WixInteractElement: No path provided"

// Element without child
const emptyElement = document.createElement('wix-interact-element');
add(emptyElement as IWixInteractElement, '#test');
// Logs: "WixInteractElement: No child element found"

// No matching configuration  
add(element as IWixInteractElement, '#nonexistent');
// Returns false, no errors
```

### Advanced Usage

#### Manual Element Management
```typescript
// Custom element creation with full control
class CustomWixElement extends HTMLElement implements IWixInteractElement {
  _internals: ElementInternals | null = null;
  connected: boolean = false;
  sheet: CSSStyleSheet | null = null;
  
  connectedCallback() {
    // Custom connection logic
    this.connect();
  }
  
  connect(path?: string) {
    const elementPath = path || this.dataset.wixPath;
    if (elementPath) {
      this.connected = add(this, elementPath);
    }
  }
  
  // Implement other required methods...
  disconnectedCallback() { /* ... */ }
  renderStyle(cssText: string) { /* ... */ }
  toggleEffect(effectId: string, method: StateParams['method']) { /* ... */ }
}

customElements.define('custom-wix-element', CustomWixElement);
```

#### Batch Processing
```typescript
// Add interactions to multiple elements efficiently
function addInteractionsToElements(selector: string) {
  const elements = document.querySelectorAll(selector);
  const results = new Map<string, boolean>();
  
  elements.forEach(element => {
    const path = element.getAttribute('data-wix-path');
    if (path) {
      const success = add(element as IWixInteractElement, path);
      results.set(path, success);
    }
  });
  
  return results;
}

// Usage
const results = addInteractionsToElements('wix-interact-element');
console.log('Interaction results:', Object.fromEntries(results));
```

---

## `remove(path)`

Removes all interactions and effects from an element and cleans up associated resources.

### Signature

```typescript
function remove(path: string): void
```

### Parameters

**`path: string`**
- The unique identifier for the element to remove interactions from
- Should match the path used when interactions were added
- Used to look up the cached element and its interactions

### Returns

**`void`** - This function does not return a value

### Examples

#### Basic Removal
```typescript
import { remove } from '@wix/interact';

// Remove all interactions from an element
remove('#hero');

// The element is no longer interactive and is removed from cache
console.log('Interactions removed for #hero');
```

#### Dynamic Content Management
```typescript
// Remove interactions when content changes
function updateContent(path: string, newContent: string) {
  // Remove old interactions
  remove(path);
  
  // Update content
  const element = document.querySelector(`[data-wix-path="${path}"]`);
  if (element?.firstElementChild) {
    element.firstElementChild.textContent = newContent;
  }
  
  // Re-add interactions if needed
  if (element) {
    add(element as IWixInteractElement, path);
  }
}
```

#### Cleanup Before Page Navigation
```typescript
// Clean up all interactions before navigation
function cleanupInteractions() {
  const elements = document.querySelectorAll('wix-interact-element[data-wix-path]');
  
  elements.forEach(element => {
    const path = element.getAttribute('data-wix-path');
    if (path) {
      remove(path);
    }
  });
  
  console.log('All interactions cleaned up');
}

// Call before page unload
window.addEventListener('beforeunload', cleanupInteractions);
```

### Behavior Details

#### What `remove()` Does:
1. **Finds Cached Element**: Looks up the element in `Interact.elementCache`
2. **Removes Event Listeners**: Calls `remove()` on all registered trigger handlers
3. **Clears Element State**: Resets any active interaction states on the element
4. **Cleans Instance State**: Calls `clearInteractionStateForPath()` on the managing instance
5. **Removes from Cache**: Deletes the element from `Interact.elementCache`

#### Safe to Call Multiple Times:
```typescript
// Safe to call remove() multiple times
remove('#element');
remove('#element'); // No error, simply does nothing
remove('#nonexistent'); // No error, path not found
```

#### Automatic Cleanup:
```typescript
// The custom element automatically calls remove() when disconnected
const element = document.querySelector('wix-interact-element[data-wix-path="#auto"]');

// Removing from DOM automatically triggers cleanup
element?.remove(); // Calls remove('#auto') internally
```

---

## Performance Considerations

### Efficient Usage Patterns

#### ✅ Good: Batch Operations
```typescript
// Good: Process multiple elements efficiently
const elements = document.querySelectorAll('wix-interact-element');
elements.forEach(el => {
  const path = el.dataset.wixPath;
  if (path) add(el as IWixInteractElement, path);
});
```

#### ❌ Avoid: Redundant Calls
```typescript
// Avoid: Calling add() multiple times for the same element
add(element, '#hero');
add(element, '#hero'); // Redundant - interactions already added
```

## Error Handling

Both functions include comprehensive error handling:

```typescript
// add() error scenarios
try {
  // Element without child
  const emptyElement = document.createElement('wix-interact-element');
  add(emptyElement as IWixInteractElement, '#test');
  // Logs warning, returns false
  
  // Invalid path
  add(element, ''); 
  // Logs warning, returns false
  
  // No configuration found
  add(element, '#unconfigured');
  // Returns false silently
} catch (error) {
  // Functions don't throw, they log warnings
}

// remove() error scenarios
remove(''); // Safe, does nothing
remove('#nonexistent'); // Safe, does nothing
```

## TypeScript Support

Full TypeScript support with proper type checking:

```typescript
import { add, remove, IWixInteractElement } from '@wix/interact';

const element = document.querySelector('wix-interact-element') as IWixInteractElement;

if (element) {
  const success: boolean = add(element, '#hero');
  
  if (success) {
    console.log('Interactions added successfully');
  }
}

// Type-safe path parameter
const path: string = '#hero';
remove(path); // void return type
```

## See Also

- [Interact Class](interact-class.md) - Main interaction manager
- [Custom Element](wix-interact-element.md) - `wix-interact-element` API
- [Type Definitions](types.md) - `IWixInteractElement` and other types
- [Getting Started](../guides/getting-started.md) - Basic usage examples