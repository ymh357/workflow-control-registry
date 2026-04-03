---
id: seo-metadata
keywords: [seo]
stages: [specGeneration]
---

## SEO & Structured Data

### Next.js Metadata API
- Use `generateMetadata()` in layout.tsx / page.tsx for dynamic metadata
- Set `title`, `description`, `openGraph`, `twitter` fields
- Use `metadata.title.template` in root layout: `"%s | SiteName"`

### Open Graph
- Required: `og:title`, `og:description`, `og:image`, `og:url`, `og:type`
- Image dimensions: 1200x630px for optimal display across platforms
- Use `opengraph-image.tsx` convention for dynamic OG images

### JSON-LD Structured Data
- Add JSON-LD via `<script type="application/ld+json">` in page components
- Common schemas: Organization, WebPage, BreadcrumbList, FAQ, Product
- Validate with Google Rich Results Test before shipping

### Technical SEO
- Semantic HTML: use `<main>`, `<article>`, `<section>`, `<nav>`, `<h1>`-`<h6>` hierarchy
- One `<h1>` per page — should match the page title
- Use `<link rel="canonical">` for pages with query params or duplicate content
- `robots.txt` and `sitemap.xml` via Next.js conventions (`app/robots.ts`, `app/sitemap.ts`)

### Performance for SEO
- Core Web Vitals: LCP < 2.5s, FID < 100ms, CLS < 0.1
- Use `next/image` with proper `width`, `height`, and `alt` text
- Preload critical fonts with `next/font`