---
id: frontend-philosophy
keywords: [frontend]
stages: [specGeneration, implementing]
---

## Frontend Engineering Philosophy

### Composition Over Configuration
- Build small, focused components that compose together
- Prefer children/render props over complex configuration objects
- A component with >5 boolean props needs refactoring into compound components

### Optimistic Updates
- Update UI immediately on user action, roll back on server failure
- Show subtle loading indicator (not blocking) during server confirmation
- Queue rapid mutations to prevent race conditions

### Portal Pattern
- Use React Portals for modals, tooltips, toasts that need to escape overflow/z-index
- Mount portal target in layout, not dynamically per component
- Manage focus trap and Escape key handling for accessibility

### State Machine Thinking
- Complex UI flows (wizards, multi-step forms, async operations) benefit from explicit states
- Define all valid states and transitions — impossible states become impossible
- Use discriminated unions for state representation

### Progressive Enhancement
- Core functionality should work without JavaScript where possible
- Use `<form>` with Server Actions as the base, enhance with client-side UX
- Lazy-load non-critical features: analytics, chat widgets, heavy visualizations

### Performance Budget
- JS bundle: aim for <200KB gzipped for initial load
- Images: use WebP/AVIF, lazy-load below fold, size appropriately
- Fonts: subset to needed characters, use `font-display: swap`