# Discover & Plan — Playwright E2E Test Discovery

You are a testing-evidence-collector agent. Your job is to explore the workflow-control web dashboard at `http://localhost:3000` using the playwright-test MCP server and produce a structured test plan covering all testable UI scenarios.

## Available Context

This is the first stage. No prior store values are available.

## Your Workflow

### Step 1 — Initialize the Playwright Planner

Call `planner_setup_page` with seed file `e2e/tests/seed.spec.ts` to initialize the Playwright planner agent. The seed file is located at `apps/web/e2e/tests/seed.spec.ts`.

### Step 2 — Explore App Pages

Use `browser_navigate` and `browser_snapshot` to explore each route:
- `/` — Home page (task creation form, task list, stats bar, language switcher)
- `/config` — Config workbench (pipeline list, stage editor, save/discard flow)
- `/task/[id]` — Task detail (message stream, stage timeline, log filters, confirm panel)
- `/registry` — Registry (package list, install/publish actions)
- `/help` — Help page

For each page, capture a snapshot and identify interactive elements, forms, state transitions, and edge cases.

### Step 3 — Read Existing Test Coverage

Use the Read tool to scan existing test files under `apps/web/e2e/tests/`. Read the following files to understand current coverage:
- `apps/web/e2e/tests/seed.spec.ts`
- `apps/web/e2e/tests/home-form.spec.ts`
- `apps/web/e2e/tests/home-locale.spec.ts`
- `apps/web/e2e/tests/home-task-display.spec.ts`
- `apps/web/e2e/tests/home-advanced.spec.ts`
- `apps/web/e2e/tests/config-save-flow.spec.ts`
- `apps/web/e2e/tests/config-stage-crud.spec.ts`
- `apps/web/e2e/tests/config-stage-fields.spec.ts`
- `apps/web/e2e/tests/config-pipeline-settings.spec.ts`
- `apps/web/e2e/tests/config-reads-outputs.spec.ts`
- `apps/web/e2e/tests/config-ai-generate.spec.ts`
- `apps/web/e2e/tests/config-fragments-validation.spec.ts`
- `apps/web/e2e/tests/config-sandbox-interactions.spec.ts`
- `apps/web/e2e/tests/config-script-gate.spec.ts`
- `apps/web/e2e/tests/config-workbench-advanced.spec.ts`
- `apps/web/e2e/tests/config-workbench-mcp.spec.ts`
- `apps/web/e2e/tests/config-visualizer-nav.spec.ts`
- `apps/web/e2e/tests/config-infrastructure.spec.ts`
- `apps/web/e2e/tests/config-integration.spec.ts`
- `apps/web/e2e/tests/task-confirm-panel.spec.ts`
- `apps/web/e2e/tests/task-log-filters.spec.ts`
- `apps/web/e2e/tests/task-detail-extra.spec.ts`
- `apps/web/e2e/tests/task-detail-advanced.spec.ts`
- `apps/web/e2e/tests/task-advanced-interactions.spec.ts`
- `apps/web/e2e/tests/task-draft-config.spec.ts`
- `apps/web/e2e/tests/registry-packages.spec.ts`
- `apps/web/e2e/tests/registry-publish.spec.ts`
- `apps/web/e2e/tests/registry-advanced.spec.ts`
- `apps/web/e2e/tests/registry-help-advanced.spec.ts`
- `apps/web/e2e/tests/registry-help-extra.spec.ts`
- `apps/web/e2e/tests/registry-update.spec.ts`
- `apps/web/e2e/tests/help-page.spec.ts`
- `apps/web/e2e/tests/mcp-seed.spec.ts`

### Step 4 — Read Known Gaps

Read `apps/web/e2e/specs/uncovered-scenarios.plan.md` for known gaps identified previously. If the file does not exist, note its absence and continue from browser exploration alone.

### Step 5 — Classify Scenarios

For each discovered scenario, classify as:
- **Automatable**: can be tested with Playwright without a live Claude API, SSE streaming, or special server state
- **Non-automatable**: requires SSE streams, real Claude API calls, live task execution, or other prerequisites that cannot be set up in a test

### Step 6 — Output testPlan

Write to the store key `testPlan` with this exact structure:

```json
{
  "title": "string — concise plan title",
  "suites": [
    {
      "id": "string — kebab-case suite id",
      "name": "string — human readable suite name",
      "page": "string — route e.g. /, /config, /registry"
    }
  ],
  "tests": [
    {
      "id": "string — unique test id",
      "suiteId": "string — matches a suite.id above",
      "name": "string — test name (English)",
      "steps": ["string — step description"],
      "expectedResult": "string — what passing looks like",
      "locatorHints": ["string — suggested selectors or getByRole/getByText hints"]
    }
  ],
  "nonAutomatableScenarios": [
    {
      "scenario": "string — scenario name",
      "reason": "string — why it cannot be automated",
      "manualInstructions": "string — how to test manually",
      "prerequisites": ["string — what must be set up first"]
    }
  ],
  "assumptions": [
    "string — any inference you made that the user should review"
  ]
}
```

**Important rules:**
- Do NOT use Edit, Write, or Bash tools in this stage — only read and explore
- If AskUserQuestion is unavailable, record all inferred decisions in `testPlan.assumptions`
- Focus on NEW scenarios not already covered by the existing test files
- Check `apps/web/e2e/specs/uncovered-scenarios.plan.md` as a starting point for known gaps

## Project-Specific Context

### App Structure
- **Framework**: Next.js 16.1.6 (App Router), React 19.1.0, TypeScript 5.8
- **Internationalization**: next-intl 4.8.3 (en/zh locales in `apps/web/src/messages/`)
- **Styling**: Tailwind CSS 4.1
- **Test framework**: @playwright/test 1.52.0 (devDependency)

### Routes (from `apps/web/src/app/`)
- `/` → `src/app/page.tsx` — Home: task creation form (textarea + pipeline select + repo input + Analyze button), task list with groups ("Running", "Completed & Other"), stats bar, EN/中 language switcher in nav
- `/config` → `src/app/config/page.tsx` — Config workbench with pipeline editor (stage CRUD, pipeline settings, AI generate, sandbox panel, MCP config, visualizer)
- `/task/[id]` → `src/app/task/[id]/page.tsx` — Task detail with message stream, stage timeline, log filter bar (category buttons, search input, stage dropdown), confirm panel (awaitingConfirm state)
- `/registry` → `src/app/registry/page.tsx` — Registry page with package list, install/uninstall/publish actions
- `/help` → `src/app/help/page.tsx` — Help/documentation page

### Key Components (from `apps/web/src/components/`)
- `nav.tsx` — Navigation bar with language switcher (EN/中 buttons)
- `confirm-panel.tsx` — Human confirm panel with Confirm/Re-run buttons, feedback textarea, override collapsible
- `question-panel.tsx` — Question panel for interactive agent stages
- `stage-timeline.tsx` — Clickable stage timeline
- `message-stream.tsx` — SSE-driven message stream
- `dynamic-store-viewer.tsx` — Store viewer in task detail
- `cost-summary.tsx` — Cost summary component
- `config-workbench.tsx` — Main config workbench component
- `config/pipeline-builder.tsx` — Pipeline builder with stage CRUD
- `config/stage-detail.tsx` — Stage detail editor
- `config/sandbox-panel.tsx` — Sandbox configuration panel
- `config/pipeline-visualizer.tsx` — Flow graph visualizer
- `config/runtime-panel.tsx` — Runtime/MCP configuration panel

### Playwright Config (`apps/web/playwright.config.ts`)
- `testDir`: `./e2e/tests`
- `outputDir`: `./e2e/test-results`
- `baseURL`: `http://localhost:3000`
- API server runs at `http://localhost:3001` (started via `pnpm dev:server`)
- `retries`: 1 (non-CI), 2 (CI)
- `timeout`: 30,000ms per test
- Project: `chromium` only

### Fixture File (`apps/web/e2e/fixtures.ts`)
```typescript
import { test as base, expect } from "@playwright/test";
const API_BASE = "http://localhost:3001";
export const test = base.extend<CustomFixtures>({
  apiBase: [API_BASE, { option: true }],
  waitForServer: [async ({}, use) => { /* polls /health/ready */ }, { auto: false }],
});
export { expect };
```

### Existing Test Coverage Summary
The following areas are already covered by existing tests in `apps/web/e2e/tests/`:
- Home form: textarea visibility, pipeline selector with optgroups, text mode input, stats bar, task groups
- Home locale: EN/中 language switcher
- Home task display: task list items, task links
- Home advanced: various home interactions
- Config: save/discard flow, stage CRUD, stage fields, pipeline settings, reads/outputs, AI generate, fragments validation, sandbox, script gate, workbench advanced, MCP workbench, visualizer nav, infrastructure, integration
- Task: confirm panel, log filters, task detail extra/advanced, advanced interactions, draft config
- Registry: packages, publish, advanced, help/extra, update
- Help: help page content
- MCP seed: MCP server seeding

### Known Gaps (from `apps/web/e2e/specs/uncovered-scenarios.plan.md`)
The plan file identifies these uncovered areas:
1. Home — "Show N more" button loading additional tasks
2. Home — Notion URL input + Analyze flow
3. Task Detail — awaitingConfirm full interaction (feedback textarea editing, Override repo collapsible expand)
4. Task Detail — log filter category buttons, search, stage dropdown, timeline stage click, collapsible log entry expand
5. Registry — publish button visibility for installed packages

### API Patterns Used in Existing Tests
- `GET http://localhost:3001/health/ready` — health check
- `GET http://localhost:3001/api/config/pipelines` — list pipelines
- `POST http://localhost:3001/api/config/pipelines` — create pipeline
- `DELETE http://localhost:3001/api/config/pipelines/:id` — delete pipeline
- `POST http://localhost:3001/api/tasks` — create task `{ taskText, pipelineName }`
- `DELETE http://localhost:3001/api/tasks/:id` — delete task
- `GET http://localhost:3001/api/config/system` — system capabilities
- `GET http://localhost:3001/api/registry/index` — registry index

### Non-Automatable Scenario Categories (expected)
- SSE-driven message streaming (requires live Claude API execution)
- awaitingConfirm panel click actions (Confirm/Re-run) — requires a real task in that state with server running
- Task cancellation mid-execution — requires running task
- Real cost accumulation visible in cost-summary — requires completed agent run
- Slack notification delivery — external service
