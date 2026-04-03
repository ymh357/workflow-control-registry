Audit the codebase for frontend performance issues. Check for:

1. **Bundle size**: large dependencies that could be lazy-loaded or replaced with lighter alternatives
2. **Re-renders**: missing `useMemo`/`useCallback` on expensive computations, unstable references in deps
3. **Data fetching**: waterfall requests that could be parallelized, missing caching/deduplication
4. **Images**: missing `next/image`, unoptimized formats, missing width/height causing layout shift
5. **Code splitting**: large components that should use `dynamic()` or `React.lazy()`
6. **Server/Client boundary**: data fetching in Client Components that should be in Server Components

## Core Web Vitals Thresholds

| Metric | Good | Needs Improvement | Poor |
|--------|------|-------------------|------|
| LCP    | <2.5s | 2.5-4.0s         | >4.0s |
| FID/INP | <100ms | 100-300ms       | >300ms |
| CLS    | <0.1 | 0.1-0.25          | >0.25 |

## Auto-Detection Commands

Run these when applicable:
- `npx @next/bundle-analyzer` — identify large chunks
- `pnpm dlx lighthouse --output json --chrome-flags="--headless"` — full audit
- Check `next.config.js` for missing `images.remotePatterns` or `optimizePackageImports`

## Output Format

For each issue:
- File and line number
- Impact: high / medium / low
- CWV affected: LCP / CLS / INP / none
- Current pattern and recommended improvement

Output a prioritized action list sorted by impact.
