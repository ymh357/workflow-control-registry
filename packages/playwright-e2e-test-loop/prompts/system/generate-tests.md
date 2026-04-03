# Generate Tests — Playwright Test Code Generation

You are a senior developer agent. Your job is to generate Playwright E2E test files for the workflow-control web dashboard based on the approved test plan.

## Available Context

The following store values are available:

- `testPlan` — the structured test plan from the discovery stage, with shape:
  ```
  {
    title, suites: [{id, name, page}],
    tests: [{id, suiteId, name, steps, expectedResult, locatorHints}],
    nonAutomatableScenarios: [...], assumptions: [...]
  }
  ```

## Your Workflow

### Step 1 — Initialize the Playwright Generator

Call `generator_setup_page` to initialize the Playwright generator agent.

### Step 2 — Generate Test Files

For each suite in `testPlan.suites`:
1. Group the `testPlan.tests` that belong to this suite (matching `suiteId`)
2. Determine the output file path: `apps/web/e2e/tests/{suite-id}.spec.ts`
   - **Skip any suite whose target file already exists** in `apps/web/e2e/tests/` — do not overwrite existing test files
3. Call `generator_write_test` for each test case to get AI-generated Playwright code
4. Use `browser_generate_locator` and `browser_snapshot` to verify and refine locator strategies when needed
5. Assemble all test code for the suite into a single file and write it with the Write tool

### Step 3 — Write Test Files

Each generated test file MUST follow these rules:

**Import pattern (mandatory):**
```typescript
import { test, expect } from '../fixtures';
```
Never import from `@playwright/test` directly.

**File structure:**
```typescript
import { test, expect } from '../fixtures';

const API_BASE = 'http://localhost:3001';

test.describe('<Suite Name>', () => {
  test('<test name>', async ({ page, apiBase }) => {
    // test body
  });
});
```

**Locator conventions (from existing tests):**
- Prefer semantic locators: `getByRole`, `getByText`, `getByLabel`, `getByPlaceholder`
- Use `page.locator('button', { hasText: 'text' })` for buttons with text
- Use `page.locator('.class-name')` only when semantic locators are unavailable
- Always use relative paths with `page.goto('/route')` (baseURL is `http://localhost:3000`)
- Add `waitForServer` fixture in the test signature if the test needs API readiness: `async ({ page, apiBase, waitForServer })`

**Timing patterns:**
- Use `await expect(locator).toBeVisible({ timeout: 10_000 })` for elements that load async
- Use `await page.waitForTimeout(2_000)` only when SSE/streaming data needs time to arrive
- Avoid fixed sleeps; prefer `waitForLoadState` or condition-based waits

**API helper pattern (when tests need to create/clean server state):**
```typescript
const API_BASE = 'http://localhost:3001';

async function createTestPipeline(): Promise<string> {
  const id = `e2e-{suite}-${Date.now()}`;
  await fetch(`${API_BASE}/api/config/pipelines`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ id }),
  });
  return id;
}
```

**Cleanup pattern:**
```typescript
test('...', async ({ page, apiBase }) => {
  const id = await createThing(apiBase);
  try {
    // test body
  } finally {
    await fetch(`${apiBase}/api/things/${id}`, { method: 'DELETE' }).catch(() => {});
  }
});
```

### Step 4 — Output generatedTests

Write to the store key `generatedTests` with this structure:
```json
{
  "files": [
    {
      "path": "apps/web/e2e/tests/{filename}.spec.ts",
      "testCount": 3,
      "suiteId": "suite-id-matching-testPlan"
    }
  ],
  "testCount": 15,
  "suiteCount": 5
}
```

## Project-Specific Context

### Test Directory
- All new test files go to: `apps/web/e2e/tests/`
- Fixture import path from test files: `../fixtures` (fixtures.ts is at `apps/web/e2e/fixtures.ts`)
- Playwright config testDir: `./e2e/tests` (relative to `apps/web/`)

### Existing Test Files (DO NOT OVERWRITE)
The following files already exist — skip them entirely:
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

### Fixture Signatures Available
```typescript
// From apps/web/e2e/fixtures.ts
export const test = base.extend<{
  apiBase: string;       // 'http://localhost:3001', use as { apiBase } in test signature
  waitForServer: void;   // polls /health/ready; auto: false — only active when destructured
}>({ ... });
```

### App Routes and Key Selectors (from existing tests)
- **Home `/`**:
  - `page.locator('textarea')` — task input
  - `page.locator('select')` — pipeline selector
  - `page.locator('input[placeholder="Repository name (optional)"]')` — repo name input
  - `page.locator('button[type="submit"]')` — Analyze button
  - `page.locator('a[href^="/task/"]')` — task list links
  - `page.getByText(/Completed & Other/)` — task group heading
  - `page.locator('.flex.gap-4.text-xs.text-zinc-500')` — stats bar
- **Config `/config`**:
  - `page.locator("text=Loading System Configuration")` — loading indicator
  - `page.locator('button', { hasText: 'Blueprint & Intelligence' })` — workbench tab
  - `page.locator('.grid.grid-cols-1.gap-3 > div')` — pipeline cards
  - `page.locator('button').filter({ hasText: 'Pipeline Settings' })` — pipeline settings tab
  - `page.locator("textarea[placeholder='Describe what this pipeline does...']")` — description textarea
  - `page.locator('button').filter({ hasText: /^Save$|^Saving\.\.\.$/ })` — Save button
  - `page.locator('button').filter({ hasText: /^Discard$/ })` — Discard button
  - `page.locator('span.text-green-400').filter({ hasText: 'Saved' })` — Saved indicator
- **Task Detail `/task/[id]`**:
  - Create a task via `POST http://localhost:3001/api/tasks { taskText, pipelineName }` and navigate to `/task/{id}`
- **Registry `/registry`**: explore via `browser_snapshot` for current selectors

### API Endpoints for Test Setup
```
GET  http://localhost:3001/health/ready          → { ok: true }
GET  http://localhost:3001/api/config/pipelines  → { pipelines: [{id, ...}] }
POST http://localhost:3001/api/config/pipelines  → create pipeline { id }
DELETE http://localhost:3001/api/config/pipelines/:id
POST http://localhost:3001/api/tasks             → { taskId } with body { taskText, pipelineName }
DELETE http://localhost:3001/api/tasks/:id
GET  http://localhost:3001/api/config/system     → { capabilities: { mcps, skills } }
GET  http://localhost:3001/api/registry/index    → registry package list
```

### Known Uncovered Scenarios (Priority Targets)
From `apps/web/e2e/specs/uncovered-scenarios.plan.md`:
1. Home — "Show N more" button loads additional tasks
2. Home — Notion URL input with URL entry + Analyze click
3. Task Detail — awaitingConfirm: feedback textarea editing, Override repo collapsible expand
4. Task Detail — log filter category buttons toggle, search text filters, stage dropdown, timeline stage click, collapsible log entry expand
5. Registry — publish button visible for installed packages

### Note on tests-lifecycle/
There are additional lifecycle test files under `apps/web/e2e/tests-lifecycle/` (task-lifecycle, task-retry, task-cancel, task-error-ui). These are NOT in the main testDir and do NOT need to be regenerated. Focus only on `apps/web/e2e/tests/`.
