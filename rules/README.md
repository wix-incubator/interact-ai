# AI Agent Rules for @wix/interact

This directory contains comprehensive rules and guidelines for AI agents to generate code using the `@wix/interact` library. These rules ensure consistent, type-safe, and effective interaction generation.

## Overview

The `@wix/interact` library provides a declarative way to create interactive animations and effects in web applications. It supports 7 trigger types, 3 effect types, and various configuration patterns for creating rich user experiences.

## Core Architecture

### Main Components
- **Interact Class**: Entry point with static `create()` method
- **InteractConfig**: Main configuration object containing effects, conditions, and interactions
- **Custom Element**: `<wix-interact-element>` wrapper with required `data-wix-path` attribute
- **Trigger Handlers**: Modular system for different interaction types

### Configuration Structure
```typescript
interface InteractConfig {
  effects?: Effect[];           // Reusable effects
  conditions?: Condition[];     // Media queries and conditions
  interactions: Interaction[];  // Main interaction definitions
}
```

## Trigger Types

The library supports 7 trigger types, each with specific use cases and patterns:

1. **hover** - Mouse hover interactions
2. **click** - Mouse click interactions  
3. **viewEnter** - Element enters viewport
4. **viewProgress** - Scroll-based progress tracking
5. **pointerMove** - Mouse movement tracking
6. **animationEnd** - Animation completion events
7. **pageVisible** - Page visibility changes

## Effect Types

Three effect types are available for different animation needs:

1. **TimeEffect** - Duration-based animations (most common)
2. **TransitionEffect** - CSS property transitions
3. **ScrubEffect** - Scroll/pointer-based animations

## Rule Categories

### 1. Trigger-Specific Rules
Each trigger type has dedicated rule files with patterns for common use cases:

- **[hover-rules.md](hover-rules.md)** - Hover effects, state management, enter/leave animations
- **[click-rules.md](click-rules.md)** - Click interactions, toggles, state patterns
- **[viewenter-rules.md](viewenter-rules.md)** - Entrance animations, staggered effects, intersection patterns
- **[viewprogress-rules.md](viewprogress-rules.md)** - Scroll animations, parallax effects, progress tracking

### 2. Configuration Patterns
Common patterns for different interaction scenarios:

- **Basic Effects**: Simple animations with default values
- **Named Effects**: Using pre-built effects from `@wix/motion`
- **Custom Keyframes**: Custom animation definitions
- **State Management**: Toggle, alternate, and state patterns
- **Responsive**: Media query conditions and breakpoints

### 3. Integration Rules
Guidelines for proper DOM integration:

- **Custom Element Wrapper**: Required `<wix-interact-element>` usage
- **Path Management**: Proper `data-wix-path` attribute handling
- **Selector Patterns**: CSS selector best practices
- **React Integration**: JSX patterns and component usage

## AI Agent Guidelines

### When Generating Interactions

1. **Always use the custom element wrapper**:
   ```html
   <wix-interact-element data-wix-path="[UNIQUE_PATH]">
     <!-- Your content -->
   </wix-interact-element>
   ```

2. **Match selectors between config and DOM**:
   - Configuration `source` and `target` must match actual DOM elements
   - Use consistent selector patterns (IDs for unique elements, classes for groups)

3. **Choose appropriate trigger types**:
   - Use `hover` for mouse-over effects
   - Use `click` for user-initiated actions
   - Use `viewEnter` for entrance animations
   - Use `viewProgress` for scroll-based effects

4. **Select effect types based on needs**:
   - **TimeEffect**: Most common, duration-based animations
   - **TransitionEffect**: CSS property transitions
   - **ScrubEffect**: Scroll or pointer-based animations

5. **Apply common patterns**:
   - Use `alternate` type for toggle behaviors
   - Use `state` type for play/pause functionality
   - Use `repeat` type for looping animations
   - Use `once` type for one-time effects

### Default Values and Best Practices

#### Duration Guidelines
- **Micro-interactions** (hover, click): 200-300ms
- **Entrance animations**: 400-800ms
- **Complex transitions**: 500-1000ms
- **Scroll animations**: Variable based on scroll distance

#### Easing Functions
- **Default**: `'ease-out'` for most interactions
- **Hover effects**: `'ease-out'` for smooth feel
- **Entrance animations**: `'ease-out'` or `'cubic-bezier(0.4, 0, 0.2, 1)'`
- **Exit animations**: `'ease-in'` for natural feel

#### Named Effects (from @wix/motion)
- **FadeIn/FadeOut**: Opacity changes
- **SlideIn/SlideOut**: Transform-based movements
- **Scale**: Size changes
- **BlurIn/BlurOut**: Blur effects
- **GlitchIn/GlitchOut**: Glitch effects
- **TiltIn/TiltOut**: Rotation effects

### Type Safety Requirements

1. **Validate configuration structure**:
   - Ensure all required properties are present
   - Use correct TypeScript types for all values
   - Validate effect properties match effect type

2. **Check selector validity**:
   - Ensure selectors match actual DOM elements
   - Use proper CSS selector syntax
   - Avoid overly complex selectors

3. **Validate effect parameters**:
   - TimeEffect requires `duration` and optional `easing`
   - TransitionEffect requires CSS property definitions
   - ScrubEffect requires scroll or pointer parameters

### Error Prevention

1. **Missing custom element wrapper**: Always wrap target elements
2. **Selector mismatches**: Ensure config selectors match DOM elements
3. **Invalid effect types**: Use correct effect type for trigger
4. **Missing required properties**: Include all required configuration properties
5. **Invalid parameter values**: Use appropriate values for trigger parameters

### Performance Considerations

1. **Use transform and opacity**: Prefer GPU-accelerated properties
2. **Limit simultaneous animations**: Avoid too many concurrent effects
3. **Use appropriate durations**: Balance smoothness with responsiveness
4. **Consider mobile performance**: Test on lower-end devices
5. **Optimize scroll effects**: Use throttling for viewProgress triggers

## Integration Examples

### React Component
```jsx
import { Interact } from '@wix/interact';

function MyComponent() {
  useEffect(() => {
    Interact.create({
      interactions: [
        {
          source: '#my-button',
          trigger: 'hover',
          effects: [
            {
              target: '#my-button',
              keyframeEffect: {
                transform: ['scale(1)', 'scale(1.05)']
              },
              duration: 300,
              easing: 'ease-out'
            }
          ]
        }
      ]
    });
  }, []);

  return (
    <wix-interact-element data-wix-path="my-button">
      <button id="my-button">Hover me</button>
    </wix-interact-element>
  );
}
```

### Vanilla JavaScript
```javascript
import { Interact } from '@wix/interact';

Interact.create({
  interactions: [
    {
      source: '.card',
      trigger: 'viewEnter',
      params: {
        type: 'once',
        threshold: 0.1
      },
      effects: [
        {
          target: '.card',
          namedEffect: 'SlideIn',
          duration: 600,
          easing: 'ease-out'
        }
      ]
    }
  ]
});
```

## Testing and Validation

1. **TypeScript compilation**: Ensure generated code compiles without errors
2. **Runtime validation**: Test interactions in browser environment
3. **Performance testing**: Verify smooth animations on target devices
4. **Cross-browser testing**: Ensure compatibility across browsers
5. **Accessibility testing**: Verify animations don't interfere with accessibility

## Resources

- **Documentation**: `/docs/` directory contains comprehensive guides
- **Examples**: `/docs/examples/` provides common use case patterns
- **API Reference**: `/docs/api/` contains detailed API documentation
- **Type Definitions**: `/docs/api/types.md` for TypeScript interfaces

## Rule File Structure

Each rule file follows this structure:
1. **Purpose and Use Cases**: When to apply the rule
2. **Pattern Template**: Code structure with placeholders
3. **Variables**: Required and optional parameters
4. **Examples**: Concrete implementations
5. **Best Practices**: Guidelines for effective usage

Follow these rules to generate consistent, maintainable, and effective interactive animations using the `@wix/interact` library. 