# Interact Class

The `Interact` class is the main entry point for managing interactions in your application. It provides both static methods for global operations and instance methods for managing specific interaction configurations.

## Import

```typescript
import { Interact } from '@wix/interact';
```

## Class Overview

```typescript
class Interact {
  // Static properties
  static instances: Interact[]
  static elementCache: Map<string, IWixInteractElement>
  
  // Instance properties
  dataCache: InteractCache
  addedInteractions: { [interactionId: string]: boolean }
  
  // Static methods
  static create(config: InteractConfig): Interact
  static getInstance(path: string): Interact | undefined
  static getElement(path: string): IWixInteractElement | undefined
  static setElement(path: string, element: IWixInteractElement): void
  
  // Instance methods
  constructor()
  init(config: InteractConfig): void
  has(path: string): boolean
  clearInteractionStateForPath(path: string): void
}
```

## Static Methods

### `Interact.create(config)`

Creates a new `Interact` instance with the provided configuration and initializes it.

**Parameters:**
- `config: InteractConfig` - The interaction configuration object

**Returns:** `Interact` - The initialized interact instance

**Example:**
```typescript
import { Interact } from '@wix/interact';

const config = {
  interactions: [{
    trigger: 'viewEnter',
    source: '#hero',
    effects: [{ effectId: 'fade-in' }]
  }],
  effects: {
    'fade-in': {
      duration: 1000,
      keyframeEffect: {
        opacity: [0, 1]
      }
    }
  }
};

const interact = Interact.create(config);
```

**Details:**
- Automatically calls `init()` on the created instance
- Adds the instance to the global instances array
- Registers the custom `wix-interact-element` if not already registered
- Returns the fully initialized instance ready for use

### `Interact.getInstance(path)`

Retrieves the `Interact` instance that manages interactions for a specific element path.

**Parameters:**
- `path: string` - The element path to search for

**Returns:** `Interact | undefined` - The instance managing this path, or undefined if not found

**Example:**
```typescript
// Get the instance managing interactions for #hero
const instance = Interact.getInstance('#hero');

if (instance) {
  console.log('Found instance managing #hero');
  console.log('Has interactions:', instance.has('#hero'));
}
```

**Use Cases:**
- Finding which instance manages a specific element
- Debugging interaction configuration
- Checking if an element has active interactions
- Programmatically managing interaction state

### `Interact.getElement(path)`

Retrieves a cached `wix-interact-element` by its path.

**Parameters:**
- `path: string` - The element path to retrieve

**Returns:** `IWixInteractElement | undefined` - The cached element, or undefined if not found

**Example:**
```typescript
// Get a cached element
const element = Interact.getElement('#my-element');

if (element) {
  console.log('Element found in cache');
  console.log('Connected:', element.connected);
  
  // You can interact with the element directly
  element.toggleEffect('my-effect', 'add');
}
```

**Details:**
- Elements are automatically cached when `add()` is called
- Cache is cleared when `remove()` is called
- Useful for programmatic element manipulation
- Returns the custom element wrapper, not the child element

### `Interact.setElement(path, element)`

Manually sets an element in the cache. This is typically used internally but can be useful for advanced use cases.

**Parameters:**
- `path: string` - The path to associate with the element
- `element: IWixInteractElement` - The custom element to cache

**Returns:** `void`

**Example:**
```typescript
// Manually cache an element (advanced usage)
const element = document.querySelector('wix-interact-element[data-wix-path="#custom"]');
if (element) {
  Interact.setElement('#custom', element as IWixInteractElement);
}
```

**Note:** This method is primarily for internal use. In most cases, elements are automatically cached when using the standard API.

## Instance Methods

### `constructor()`

Creates a new `Interact` instance with empty caches.

**Example:**
```typescript
// Create an empty instance (typically you'd use Interact.create() instead)
const instance = new Interact();

// Initialize with configuration
instance.init(config);
```

**Details:**
- Initializes empty `dataCache` with effects, conditions, and interactions
- Initializes empty `addedInteractions` tracking object
- Does not register with global instances array (use `Interact.create()` for that)

### `init(config)`

Initializes the instance with a configuration object.

**Parameters:**
- `config: InteractConfig` - The interaction configuration

**Returns:** `void`

**Example:**
```typescript
const instance = new Interact();

const config = {
  interactions: [
    {
      trigger: 'click',
      source: '#button',
      effects: [{ effectId: 'bounce' }]
    }
  ],
  effects: {
    bounce: {
      duration: 500,
      namedEffect: 'bounce'
    }
  }
};

instance.init(config);
```

**Details:**
- Parses and caches the configuration for efficient lookup
- Registers the custom element if not already registered
- Connects any already-cached elements to their interactions
- Can be called multiple times to update configuration

### `has(path)`

Checks if the instance has interactions configured for a specific element path.

**Parameters:**
- `path: string` - The element path to check

**Returns:** `boolean` - True if interactions exist for this path

**Example:**
```typescript
const instance = Interact.create(config);

// Check if an element has interactions
if (instance.has('#hero')) {
  console.log('#hero has interactions configured');
} else {
  console.log('#hero has no interactions');
}

// Useful for conditional logic
if (instance.has('#optional-animation')) {
  // Only add element if it has interactions
  document.body.appendChild(createOptionalElement());
}
```

**Use Cases:**
- Conditional element creation
- Debugging configuration issues
- Dynamic interaction management
- Performance optimization (avoid creating elements without interactions)

### `clearInteractionStateForPath(path)`

Clears all interaction state for a specific element path, removing any active interactions and resetting state.

**Parameters:**
- `path: string` - The element path to clear

**Returns:** `void`

**Example:**
```typescript
// Clear all interaction state for an element
instance.clearInteractionStateForPath('#hero');

// Useful when dynamically changing interactions
instance.clearInteractionStateForPath('#dynamic-element');
// Update configuration...
// Re-add element to activate new interactions
```

**Details:**
- Removes all interaction IDs associated with the path
- Clears the `addedInteractions` tracking for those interactions
- Does not remove the element from cache (use `remove()` for that)
- Useful for resetting element state before applying new interactions

## Instance Properties

### `dataCache: InteractCache`

Contains the parsed and cached interaction configuration.

**Structure:**
```typescript
{
  effects: { [effectId: string]: Effect },
  conditions: { [conditionId: string]: Condition },
  interactions: {
    [path: string]: {
      triggers: Interaction[],
      effects: Record<string, (InteractionTrigger & { effect: Effect | EffectRef })[]>,
      interactionIds: Set<string>
    }
  }
}
```

**Example:**
```typescript
const instance = Interact.create(config);

// Access cached data
console.log('Available effects:', Object.keys(instance.dataCache.effects));
console.log('Available conditions:', Object.keys(instance.dataCache.conditions));
console.log('Element paths with interactions:', Object.keys(instance.dataCache.interactions));

// Check specific element data
const heroData = instance.dataCache.interactions['#hero'];
if (heroData) {
  console.log('Triggers for #hero:', heroData.triggers.length);
  console.log('Effects for #hero:', Object.keys(heroData.effects));
}
```

### `addedInteractions: { [interactionId: string]: boolean }`

Tracks which interactions have been added to prevent duplicates.

**Example:**
```typescript
// Check if specific interactions are active
const instance = Interact.getInstance('#hero');
if (instance) {
  const interactionIds = Object.keys(instance.addedInteractions);
  console.log('Active interactions:', interactionIds);
  
  // Check specific interaction
  const specificId = '#hero::fade-in::1';
  if (instance.addedInteractions[specificId]) {
    console.log('Fade-in interaction is active');
  }
}
```

## Static Properties

### `Interact.instances: Interact[]`

Array of all created `Interact` instances.

**Example:**
```typescript
// Get all instances
console.log('Total instances:', Interact.instances.length);

// Find instances with specific configurations
const instancesWithHover = Interact.instances.filter(instance => {
  return Object.values(instance.dataCache.interactions).some(data =>
    data.triggers.some(trigger => trigger.trigger === 'hover')
  );
});

console.log('Instances with hover interactions:', instancesWithHover.length);
```

### `Interact.elementCache: Map<string, IWixInteractElement>`

Global cache of all `wix-interact-element` instances by path.

**Example:**
```typescript
// Get all cached elements
console.log('Cached elements:', Interact.elementCache.size);

// Iterate through cached elements
Interact.elementCache.forEach((element, path) => {
  console.log(`Element at ${path}:`, {
    connected: element.connected,
    hasChildren: element.children.length > 0
  });
});

// Clear all cached elements (advanced usage)
Interact.elementCache.clear();
```

## Error Handling

The `Interact` class includes built-in error handling and validation:

```typescript
// Configuration validation
const config = {
  interactions: [{
    // Missing source will log an error
    trigger: 'click',
    effects: [{ effectId: 'missing-effect' }]
  }]
};

const instance = Interact.create(config);
// Console: "Interaction 1 is missing a source element."
```

## Best Practices

### 1. Use `Interact.create()` for Standard Usage
```typescript
// ✅ Recommended
const interact = Interact.create(config);

// ❌ Avoid manual construction
const interact = new Interact();
interact.init(config);
```

### 2. Check Instance Existence
```typescript
// ✅ Safe access
const instance = Interact.getInstance('#element');
if (instance && instance.has('#element')) {
  // Work with instance
}

// ❌ Unsafe access
const instance = Interact.getInstance('#element')!;
instance.clearInteractionStateForPath('#element'); // Could throw
```

### 3. Clean Up When Needed
```typescript
// ✅ Clean up when removing elements
if (instance) {
  instance.clearInteractionStateForPath('#removed-element');
}
remove('#removed-element'); // Also clears cache
```

## TypeScript Support

The `Interact` class provides full TypeScript support with proper type inference:

```typescript
import { Interact, InteractConfig, TimeEffect } from '@wix/interact';

const config: InteractConfig = {
  interactions: [/* ... */],
  effects: {
    'fade': {
      duration: 1000, // TypeScript knows this is a TimeEffect
      keyframeEffect: {
        opacity: [0, 1]
      }
    } satisfies TimeEffect
  }
};

const interact: Interact = Interact.create(config);
```

## See Also

- [Standalone Functions](functions.md) - `add()` and `remove()` functions
- [Type Definitions](types.md) - `InteractConfig` and related types
- [Custom Element](wix-interact-element.md) - `wix-interact-element` API
- [Configuration Guide](../guides/configuration.md) - Building interaction configurations