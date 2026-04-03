---
id: typescript-strict
keywords: [typescript, ts]
stages: [techPrep, specGeneration, implementing]
---

## TypeScript & Zod Rules

- If `.workflow/tech-context.md` notes "Zod unavailable", use plain TypeScript types.
- Otherwise, use Zod schema-first: define schemas with `z.object()`, derive types with `z.infer<typeof Schema>`.
- Never use `any` — use `unknown` with type guards or proper generics.
- Prefer `interface` for object shapes, `type` for unions/intersections/mapped types.
- API nullable fields: use `field: string | null`, not `field?: string`.
- Explicit return types on exported functions; inferred types for local variables.
- Use `as const` for literal tuples and fixed-value objects.