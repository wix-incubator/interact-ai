# @wix/interact Documentation

Welcome to the complete documentation for the `@wix/interact` package - a powerful, declarative interaction library for creating engaging web animations and effects.

## Table of Contents

### 📚 **API Reference**
Complete reference documentation for all classes, methods, and types.

- [**Core API**](api/README.md) - Main classes and functions
  - [Interact Class](api/interact-class.md) - Main interaction manager
  - [Standalone Functions](api/functions.md) - `add()`, `remove()` utility functions
  - [Custom Element](api/wix-interact-element.md) - `<wix-interact-element>` API
- [**Type Definitions**](api/types.md) - Complete TypeScript interfaces
  - [Configuration Types](api/types.md#configuration) - `InteractConfig`, `Interaction`, `Effect`
  - [Trigger Types](api/types.md#triggers) - All trigger parameters and types
  - [Effect Types](api/types.md#effects) - Time, scrub, and transition effects
- [**Utilities**](api/utilities.md) - Helper functions and utilities

### 🎓 **Guides & Tutorials**
Learn the concepts and patterns for building effective interactions.

- [**Getting Started**](guides/getting-started.md) - Your first interaction in 5 minutes
- [**Core Concepts**](guides/README.md) - Understanding the interaction system
  - [Understanding Triggers](guides/triggers.md) - When interactions happen
  - [Working with Effects](guides/effects.md) - What happens during interactions
  - [Configuration Structure](guides/configuration.md) - Organizing complex interactions
  - [Custom Elements](guides/custom-elements.md) - How `wix-interact-element` works
  - [State Management](guides/state-management.md) - CSS states vs data attributes
  - [Conditions & Media Queries](guides/conditions.md) - Responsive interactions
- [**Performance**](guides/performance.md) - Optimization tips and best practices

### 💡 **Examples & Patterns**
Practical examples and common interaction patterns.

- [**Basic Examples**](examples/README.md) - Simple, copy-paste examples
  - [Entrance Animations](examples/entrance-animations.md) - Viewport-triggered effects
  - [Click Interactions](examples/click-interactions.md) - User-triggered actions
  - [Hover Effects](examples/hover-effects.md) - Mouse interaction patterns
  - [Scroll Animations](examples/scroll-animations.md) - Progress-based effects
- [**Advanced Patterns**](examples/advanced-patterns.md) - Complex interaction sequences
- [**Real-world Examples**](examples/real-world.md) - Production-ready implementations

### 🔧 **Integration Guides**
Framework-specific integration and migration guides.

- [**Framework Integration**](integration/README.md) - Using with different frameworks
  - [React Integration](integration/react.md) - Components and hooks
  - [Vanilla JavaScript](integration/vanilla-js.md) - Direct DOM usage
  - [Other Frameworks](integration/other-frameworks.md) - Vue, Angular, etc.
- [**Migration Guides**](integration/migration.md) - Coming from other libraries
- [**Testing**](integration/testing.md) - Testing interaction behaviors
- [**Debugging**](integration/debugging.md) - Development tools and techniques

### 🏗️ **Advanced Topics**
Deep-dive technical documentation for power users.

- [**Architecture**](advanced/architecture.md) - System design and decisions
- [**Custom Triggers**](advanced/custom-triggers.md) - Building your own triggers
- [**Browser Compatibility**](advanced/browser-support.md) - Polyfills and fallbacks
- [**Performance Deep Dive**](advanced/performance-optimization.md) - Advanced optimization
- [**Contributing**](advanced/contributing.md) - Development and contribution guide

## Quick Navigation

### I want to...

**🚀 Get started quickly**
→ [Getting Started Guide](guides/getting-started.md)

**📖 Understand the concepts**
→ [Core Concepts](guides/README.md)

**💻 See code examples**
→ [Examples & Patterns](examples/README.md)

**🔍 Look up API details**
→ [API Reference](api/README.md)

**🔧 Integrate with my framework**
→ [Integration Guides](integration/README.md)

**🐛 Debug an issue**
→ [Debugging Guide](integration/debugging.md)

**⚡ Optimize performance**
→ [Performance Guide](guides/performance.md)

**🛠️ Extend functionality**
→ [Advanced Topics](advanced/README.md)

## Version Information

This documentation is for `@wix/interact` v1.0.0. For earlier versions or migration information, see the [Migration Guide](integration/migration.md).

## Feedback

Found an issue with the documentation? Please [open an issue](https://github.com/wix-incubator/wow-libs/issues) or contribute improvements via pull request.