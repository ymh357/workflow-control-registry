# Global Constraints — Playwright E2E Test Loop Pipeline

These constraints apply to ALL stages of the Playwright E2E Test Loop pipeline.

## Language Constraints

- **Code artifacts** (test files, selector strings, TypeScript, file paths, API URLs, error messages): **English only**
- **Reports and summaries** written to the `report` store key: **Chinese (Simplified)**
- **Pipeline-internal communication** (store keys, agent reasoning): English

## Test File Constraints

### Import Rule (MANDATORY)
All Playwright test files MUST import from the custom fixtures module:
```typescript
import { test, expect } from '../fixtures';
// NOT from '@playwright/test'
```
The fixtures file is at `apps/web/e2e/fixtures.ts` and provides `apiBase` and `waitForServer` custom fixtures.

### File Location Rule
All new test files MUST be written to `apps/web/e2e/tests/` ONLY.
- Do NOT write to `apps/web/e2e/tests-lifecycle/` (lifecycle tests are separate)
- Do NOT write to `apps/web/src/` or any other directory

### Do Not Overwrite Rule
Never overwrite existing test files. The following 33 files already exist and must not be modified by the generation stage:
```
apps/web/e2e/tests/seed.spec.ts
apps/web/e2e/tests/mcp-seed.spec.ts
apps/web/e2e/tests/home-form.spec.ts
apps/web/e2e/tests/home-locale.spec.ts
apps/web/e2e/tests/home-task-display.spec.ts
apps/web/e2e/tests/home-advanced.spec.ts
apps/web/e2e/tests/config-save-flow.spec.ts
apps/web/e2e/tests/config-stage-crud.spec.ts
apps/web/e2e/tests/config-stage-fields.spec.ts
apps/web/e2e/tests/config-pipeline-settings.spec.ts
apps/web/e2e/tests/config-reads-outputs.spec.ts
apps/web/e2e/tests/config-ai-generate.spec.ts
apps/web/e2e/tests/config-fragments-validation.spec.ts
apps/web/e2e/tests/config-sandbox-interactions.spec.ts
apps/web/e2e/tests/config-script-gate.spec.ts
apps/web/e2e/tests/config-workbench-advanced.spec.ts
apps/web/e2e/tests/config-workbench-mcp.spec.ts
apps/web/e2e/tests/config-visualizer-nav.spec.ts
apps/web/e2e/tests/config-infrastructure.spec.ts
apps/web/e2e/tests/config-integration.spec.ts
apps/web/e2e/tests/task-confirm-panel.spec.ts
apps/web/e2e/tests/task-log-filters.spec.ts
apps/web/e2e/tests/task-detail-extra.spec.ts
apps/web/e2e/tests/task-detail-advanced.spec.ts
apps/web/e2e/tests/task-advanced-interactions.spec.ts
apps/web/e2e/tests/task-draft-config.spec.ts
apps/web/e2e/tests/registry-packages.spec.ts
apps/web/e2e/tests/registry-publish.spec.ts
apps/web/e2e/tests/registry-advanced.spec.ts
apps/web/e2e/tests/registry-help-advanced.spec.ts
apps/web/e2e/tests/registry-help-extra.spec.ts
apps/web/e2e/tests/registry-update.spec.ts
apps/web/e2e/tests/help-page.spec.ts
```

## Infrastructure Constraints

### Server Requirements
- Web app MUST be running at `http://localhost:3000`
- API server MUST be running at `http://localhost:3001`
- Health check endpoint: `GET http://localhost:3001/health/ready` → `{ ok: true }`
- Playwright's webServer config will attempt to start these automatically via `reuseExistingServer: true`

### Browser Constraint
- Only `chromium` project is configured in `apps/web/playwright.config.ts`
- Do NOT run tests with `--project=firefox` or `--project=webkit`

### Timeout Constraints
- Default test timeout: 30,000ms (configured in playwright.config.ts)
- Do NOT set per-test timeouts lower than 5,000ms
- For elements that load via SSE or async API: use `{ timeout: 10_000 }` on expect calls

## Framework Versions (from apps/web/package.json)

```
next: ^16.1.6           (Next.js App Router)
react: ^19.1.0
typescript: ^5.8.0
@playwright/test: ^1.52.0
next-intl: ^4.8.3       (en/zh localization)
tailwindcss: ^4.1.0
vitest: ^4.0.18         (unit tests — separate from E2E)
```

## Test Design Constraints

### No External Network Calls
Tests must not call external APIs, CDNs, or third-party services. All network calls must go to `localhost:3000` (web) or `localhost:3001` (API).

### Cleanup After Tests
Any test that creates server-side state (pipelines, tasks) MUST clean it up in a `finally` block:
```typescript
const id = await createThing(apiBase);
try {
  // test assertions
} finally {
  await fetch(`${apiBase}/api/things/${id}`, { method: 'DELETE' }).catch(() => {});
}
```

### Non-Automatable Scenarios — Do Not Attempt
These scenarios CANNOT be reliably automated and must NOT be included in generated tests:
1. SSE streaming messages (requires live Claude API execution)
2. Real task execution and completion (Claude API not available in test env)
3. Slack notification delivery (external service)
4. Real cost accumulation in cost-summary (requires completed agent run with real API)
5. Cancel a mid-execution task (requires precise timing with live execution)

### Locator Preference Order
1. `getByRole` — most resilient
2. `getByText`, `getByLabel`, `getByPlaceholder` — semantic
3. `page.locator('button', { hasText: '...' })` — text-based
4. CSS class selectors — use only when no semantic option exists
5. `data-testid` — if present in the DOM (check via browser_snapshot)

## MCP Constraint

The `playwright-test` MCP server is required for stages 1, 3, and 4 (discover-and-plan, generate-tests, run-and-heal-loop). It must be configured and running in the pipeline environment before execution begins. Without it, these stages cannot proceed.

## Discovery Stage Constraint

The `discover-and-plan` stage MUST NOT use Edit, Write, or Bash tools. It is read-only + browser exploration only.
