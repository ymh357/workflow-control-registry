---
id: security-checklist
keywords: [security]
stages: [qualityAssurance, implementing]
---

## Frontend Security Checklist

### Forbidden Patterns
- **No `dangerouslySetInnerHTML`** — use a sanitization library (DOMPurify) if absolutely necessary
- **No `eval()` or `new Function()`** — never execute dynamic strings as code
- **No inline `<script>` injection** — use Next.js `<Script>` component with strategy

### Secrets Protection
- Private keys, API keys, mnemonics, and secrets must NEVER appear in client-side code
- Use `NEXT_PUBLIC_` prefix only for truly public values (chain IDs, public API endpoints)
- Validate that `.env.local` / `.env` files are in `.gitignore`

### XSS Prevention
- All user input rendered in JSX is auto-escaped — do not bypass with innerHTML
- Sanitize URL parameters before using in `href` or `src` attributes
- Use `encodeURIComponent()` for dynamic URL construction

### Contract Interaction Security
- Validate all user-supplied addresses with `isAddress()` from viem
- Validate amounts are positive and within expected ranges before tx submission
- Never trust client-side allowance checks alone — contracts enforce on-chain

### API Security
- Use CSRF tokens for state-changing API calls
- Validate and sanitize all form inputs before sending to API
- Set appropriate CORS headers — never use `*` in production

### Content Security
- Configure Content Security Policy headers in `next.config.js`
- Use `nonce` for inline scripts when CSP is strict
- Subresource Integrity (SRI) for external CDN scripts