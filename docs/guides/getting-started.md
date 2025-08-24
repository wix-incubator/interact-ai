# Getting Started

Welcome to `@wix/interact`! This guide will help you create your first interaction in under 4 minutes.

## What is @wix/interact?

`@wix/interact` is a powerful library that lets you create interactive animations and effects triggered by user actions like clicking, hovering, or scrolling. It integrates seamlessly with `@wix/motion` to provide smooth, performant animations.

## Core Concepts

Before we dive in, let's understand the three main concepts:

1. **Triggers** - User actions that start an interaction (click, hover, scroll, etc.)
2. **Effects** - The visual changes that happen (animations, transitions, style changes)
3. **Elements** - The HTML elements that participate in the interaction

## Your First Interaction

Let's create a simple hover effect that makes an image grow when you hover over it.

### Step 1: Install the Package

```bash
npm install @wix/interact
```

### Step 2: Basic HTML Structure

```html
<!DOCTYPE html>
<html>
<head>
    <title>My First Interaction</title>
</head>
<body>
    <wix-interact-element data-wix-path="#my-image">
        <img id="my-image" src="logo.png" alt="Logo" />
    </wix-interact-element>
</body>
</html>
```

**Key points:**
- Wrap your element in `<wix-interact-element>`
- Use `data-wix-path` to identify the element (must be unique)
- The path should match the CSS selector for your target element

### Step 3: Configure the Interaction

```javascript
import { Interact } from '@wix/interact';

// Create the interaction configuration
const config = {
    interactions: [
        {
            source: '#my-image',        // What element triggers the interaction
            trigger: 'hover',           // What user action starts it
            effects: [
                {
                    keyframeEffect: [
                        { scale: 2 }
                    ],
                    duration: 300,          // Animation duration in milliseconds
                    easing: 'ease-out'      // Animation timing
                }
            ]
        }
    ]
};

// Initialize the interaction
Interact.create(config);
```

### Step 4: React Example

If you're using React, here's the same example:

```tsx
import React from 'react';
import { Interact } from '@wix/interact';

const config = {
    interactions: [
        {
            source: '#my-image',
            trigger: 'hover',
            effects: [
                {
                    keyframeEffect: [
                        { scale: 2 }
                    ],
                    duration: 300,
                    easing: 'ease-out'
                }
            ]
        }
    ]
};

Interact.create(config);

function App() {
    return (
        <div>
            <wix-interact-element data-wix-path="#my-image">
                <img id="my-image" src="logo.png" alt="Logo" />
            </wix-interact-element>
        </div>
    );
}
```

## Understanding the Configuration

Let's break down what each part does:

### The `interactions` Array
This contains all your interactions. Each interaction defines:
- **source**: Which element triggers the interaction
- **trigger**: What user action starts it
- **effects**: What animations happen

### The `effects` Array
Each effect defines:
- **target**: Which element gets animated (if missing it defaults to source)
- **keyframeEffect**: An array of keyframe objects
- **duration**: How long the animation takes
- **easing**: The animation timing curve

## Common Patterns

### Hover with Scale and Rotation
```javascript
{
    source: '#my-element',
    trigger: 'hover',
    effects: [
        {
            target: '#my-element',
            keyframeEffect: [
                { transform: 'scale(1) rotate(0deg)' },
                { transform: 'scale(1.1) rotate(5deg)' }
            ],
            duration: 200,
            easing: 'ease-out'
        }
    ]
}
```

### Click to Fade In/Out
```javascript
{
    source: '#my-button',
    trigger: 'click',
    effects: [
        {
            target: '#my-content',
            keyframeEffect: [
                { opacity: '0' },
                { opacity: '1' }
            ],
            duration: 500,
            easing: 'ease-in-out',
            fill: 'both'
        }
    ]
}
```

### Entrance Animation on viewport entry
```javascript
{
    source: '#my-element',
    trigger: 'viewEnter',
    effects: [
        {
            target: '#my-element',
            namedEffect: 'FadeIn',
            duration: 800,
            easing: 'ease-out'
        }
    ]
}
```

## Next Steps

Congratulations! You've created your first interaction. Here's what to explore next:

1. **[Understanding Triggers](./understanding-triggers.md)** - Learn about all 7 trigger types
2. **[Effects and Animations](./effects-and-animations.md)** - Discover more animation options
3. **[Custom Elements](./custom-elements.md)** - Understand how `<wix-interact-element>` works
