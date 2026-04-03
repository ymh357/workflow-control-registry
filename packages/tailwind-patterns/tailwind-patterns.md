---
id: tailwind-patterns
keywords: [tailwind]
stages: [specGeneration, implementing]
---

## Tailwind CSS Patterns

### Mobile-First Responsive
- Write base styles for mobile, add breakpoint prefixes for larger screens
- Breakpoints: `sm:` (640px), `md:` (768px), `lg:` (1024px), `xl:` (1280px)
- Container pattern: `mx-auto max-w-7xl px-4 sm:px-6 lg:px-8`

### Dark Mode
- Use `dark:` variant with class-based strategy (`darkMode: 'class'`)
- Define semantic color tokens in CSS variables for easy theme switching
- Test both modes — don't leave dark mode as afterthought

### Class Composition
- Use `cn()` utility (clsx + tailwind-merge) for conditional classes
- Keep class strings readable: group by concern (layout, spacing, colors, typography)
- Extract repeated patterns with `@apply` in CSS modules only for truly shared base styles

### Component Extraction Rules
- 3+ identical class combinations → extract to a component, not a utility class
- `@apply` is acceptable for base element styles (buttons, inputs) in a design system
- Prefer component props over class overrides for variants

### Common Patterns
- Truncation: `truncate` (single line) or `line-clamp-N` (multi-line)
- Aspect ratio: `aspect-video`, `aspect-square`
- Grid: `grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4`
- Stack: `flex flex-col gap-4` (vertical), `flex items-center gap-2` (horizontal)