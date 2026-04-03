---
id: nextjs-app-router
keywords: [nextjs]
stages: [specGeneration, implementationPlan, implementing]
---

## Next.js App Router Architecture

### Rendering Strategy
- **SERVER COMPONENT** when: data fetched at request time, no interaction, no browser APIs, SEO matters.
- **CLIENT COMPONENT** ("use client") when: uses `useState`, `useEffect`, events, browser APIs, or third-party globals.
- Pattern: Server Component fetches data → passes as props to Client Component.

### Data Fetching
- `fetch` in Server Component: SSR initial render, stable data, no client refetch needed.
- `React Query`: data changes while user is on page, optimistic updates, shared cache, user-triggered refetch.
- `Server Action`: form submissions, simple mutations, no external consumers.

### API Layer
- **Route Handler**: webhooks from third-party, custom headers/streaming, consumed by non-Next.js clients.
- **Server Action**: internal mutations, form-based, always from own frontend.
- **Direct fetch in Server Component**: read-only, no transformation needed.

### Cache Strategy
- `cache: 'no-store'` → real-time or user-specific data.
- `next: { revalidate: N }` → semi-static, acceptable to be N seconds old.
- `cache: 'force-cache'` → truly static, build-time stable.

### State Management
- Server → `react-query`; UI → `zustand`; Form → `react-hook-form`; URL → `nuqs`.
- API client: server → native `fetch`; client → `ky`.

### Suspense & Streaming
- Wrap async components with `<Suspense fallback={<Skeleton />}>`.
- Use `loading.tsx` for route-level loading states.
- Stream long-running data with nested Suspense boundaries.