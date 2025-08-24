# API Reference

Complete reference documentation for all public APIs in `@wix/interact`.

## Core API

### Classes

- [**Interact**](interact-class.md) - Main interaction manager class
  - [Static methods](interact-class.md#static-methods): `create()`, `getInstance()`, `getElement()`, `setElement()`
  - [Instance methods](interact-class.md#instance-methods): `init()`, `has()`, `clearInteractionStateForPath()`
  - [Properties](interact-class.md#properties): `dataCache`, `addedInteractions`
  - [Error handling](interact-class.md#error-handling) and [best practices](interact-class.md#best-practices)

### Functions

- [**add(element, path)**](functions.md#add) - Add interactions to an element
  - [Usage examples](functions.md#examples) and [behavior details](functions.md#behavior-details)
  - [Error handling](functions.md#error-handling) and [performance considerations](functions.md#performance-considerations)
- [**remove(path)**](functions.md#remove) - Remove interactions from an element
  - [Cleanup behavior](functions.md#behavior-details) and [advanced usage](functions.md#advanced-usage)

### Custom Elements

- [**wix-interact-element**](wix-interact-element.md) - Custom element for wrapping interactive content
  - [Interface](wix-interact-element.md#element-interface) and [lifecycle methods](wix-interact-element.md#methods)
  - [Framework integration](wix-interact-element.md#framework-integration) (React, Vue, Angular)
  - [State management](wix-interact-element.md#state-management) and [browser support](wix-interact-element.md#browser-support)

## Type Definitions

### Configuration Types

- [**InteractConfig**](types.md#interactconfig) - Main configuration object
- [**Interaction**](types.md#interaction) - Individual interaction definition
- [**Effect**](types.md#effect) - Effect definition and parameters
- [**Condition**](types.md#condition) - Conditional interaction logic

### Trigger Types

- [**TriggerType**](types.md#triggertype) - Union of all supported triggers
- [**ViewEnterParams**](types.md#viewenterparams) - Viewport entry configuration
- [**StateParams**](types.md#stateparams) - State transition parameters  
- [**PointerMoveParams**](types.md#pointermoveparams) - Pointer movement configuration
- [**AnimationEndParams**](types.md#animationendparams) - Animation completion triggers

### Effect Types

- [**TimeEffect**](types.md#timeeffect) - Time-based animations with examples
- [**ScrubEffect**](types.md#scrubeffect) - Progress-based animations with named ranges
- [**TransitionEffect**](types.md#transitioneffect) - CSS transition effects
- [**EffectRef**](types.md#effectref) - Effect reference by ID

### Advanced Types

- [**IWixInteractElement**](types.md#iwixinteractelement) - Custom element interface
- [**InteractCache**](types.md#interactcache) - Internal cache structure
- [**EffectProperty**](types.md#effectproperty) - Animation content types
- [**TypeScript patterns**](types.md#typescript-usage-patterns) - Type guards and generic constraints

## Quick Reference

### Essential Imports
```typescript
import { 
  Interact,           // Main class
  add, remove,        // Standalone functions
  InteractConfig,     // Configuration type
  IWixInteractElement // Custom element interface
} from '@wix/interact';
```

### Basic Pattern
```typescript
// 1. Create configuration
const config: InteractConfig = {
  interactions: [{ /* ... */ }],
  effects: { /* ... */ }
};

// 2. Initialize
const interact = Interact.create(config);

// 3. HTML usage
// <wix-interact-element data-wix-path="#element">
//   <div>Content</div>
// </wix-interact-element>
```

### Type Hierarchy

```
InteractConfig
├── interactions: Interaction[]
│   ├── trigger: TriggerType
│   ├── source: string
│   ├── params?: TriggerParams  
│   ├── conditions?: string[]
│   └── effects: (Effect | EffectRef)[]
├── effects?: Record<string, Effect>
│   ├── TimeEffect (duration-based)
│   ├── ScrubEffect (progress-based)
│   └── TransitionEffect (CSS transitions)
└── conditions?: Record<string, Condition>
    ├── Media queries
    └── Container queries
```

## Integration with @wix/motion

The `@wix/interact` package provides a declarative layer on top of `@wix/motion`. Effect definitions can include:

- `keyframeEffect` - Raw keyframe animations
- `namedEffect` - Pre-built animation effects from @wix/motion
- `customEffect` - Custom animation functions with full control

**Example Integration:**
```typescript
{
  effects: {
    'slide-in': {
      duration: 800,
      keyframeEffect: {
        transform: ['translateY(50px)', 'translateY(0)'],
        opacity: [0, 1]
      }
    },
    'bounce': {
      duration: 600,
      namedEffect: 'bounce' // Pre-built from @wix/motion
    }
  }
}
```

See the [Effects Guide](../guides/effects.md) for detailed information on animation integration and [Examples](../examples/README.md) for practical usage patterns.