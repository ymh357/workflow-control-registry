---
id: tdd-workflow
keywords: [tdd, testing]
stages: [specGeneration, implementing]
---

## Specification-Driven Development (SDD)

Follow this workflow in order:

### 1. Understand the Spec
Read ALL files in .workflow/spec/:
- types.ts — data contracts (Zod or plain TS)
- test-cases.md — expected behaviors in TC-NNN format
- tests/*.test.ts — complete, runnable test files
- implementation-plan.md — file-by-file implementation order

### 2. Verify Tests Fail (Red)
Copy test files from .workflow/spec/tests/ to project test directory.
Run `pnpm test` — all tests MUST fail (they test code that doesn't exist yet).
If any test passes, it's testing the wrong thing — investigate.

### 3. Implement to Pass Tests (Green)
Follow implementation-plan.md step by step:
- Implement one file/module at a time
- Run `pnpm test` after each module — watch tests turn green incrementally

**Test modification rules:**
- NEVER change what a test asserts (the expected behavior / acceptance criteria)
- You MAY fix test setup to match real component API: wrong selectors (`getByRole` target, `data-testid`), incorrect mock structure, wrong import paths, missing providers/wrappers
- When fixing test setup, add a `// Adjusted: <reason>` comment on the changed line
- If >3 test setup fixes are needed in one file, flag it in `.workflow/implementation-log.md` under "Deviations from Spec"

### 4. Verify Spec Alignment
After all tests pass:
- Re-read .workflow/spec/types.ts — confirm all types are used correctly
- Re-read test-cases.md — confirm all TCs are covered
- Run `pnpm test` one final time
- Refactor if needed, keeping tests green