---
id: react-patterns
keywords: [react]
stages: [specGeneration, implementationPlan, implementing]
---

## React Component & Hook Patterns

### Component Composition
- Prefer composition over configuration: pass components as children/props, not config objects
- Container/Presenter split: container handles data/logic, presenter is pure UI
- Co-locate related components in the same directory with barrel export

### Custom Hooks
- Extract reusable logic into `use*` hooks — one concern per hook
- Return tuple `[state, actions]` or object `{ data, error, isLoading, mutate }` for consistency
- Hooks that fetch data should handle loading/error/success states internally

### State Management
- Minimize state: derive what you can from existing state/props
- Lift state only as far as necessary — not to the root
- Replace boolean state soup with discriminated unions:
  ```ts
  // Bad: isLoading, isError, isSuccess
  // Good: status: 'idle' | 'loading' | 'error' | 'success'
  ```

### Memoization Strategy
- `useMemo` only for expensive computations (>1ms) or referential stability for deps
- `useCallback` only when passing to memoized children or as effect deps
- Do NOT wrap every function/value — profile first, optimize second

### Error Boundaries
- Wrap each route segment with an error boundary
- Provide a "Retry" action in the fallback UI
- Log errors to monitoring service in the boundary's `componentDidCatch`

### Loading States
- Use Skeleton components that match the layout shape — not generic spinners
- Skeleton should match the expected content dimensions to prevent layout shift
- Wrap async content with Suspense when using React Server Components