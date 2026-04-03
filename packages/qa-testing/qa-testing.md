---
id: qa-testing
keywords: [qa, testing]
stages: [qualityAssurance, implementing]
---

## Testing Strategy

### Test Pyramid
- **Unit tests** (70%): pure functions, hooks, utilities — fast, isolated
- **Integration tests** (20%): component + API interaction, form submissions
- **E2E tests** (10%): critical user flows only — login, checkout, key conversions

### AAA Pattern
- **Arrange**: set up test data and render component
- **Act**: simulate user interaction (click, type, submit)
- **Assert**: verify expected outcome (DOM change, API call, state update)

### Test Isolation
- Each test must be independent — no shared mutable state between tests
- Use `beforeEach` for common setup, not global variables
- Mock external dependencies (API calls, timers, browser APIs) at the boundary

### Component Testing
- Test behavior, not implementation: query by role/text, not by class/id
- Use `@testing-library/react` — prefer `getByRole`, `getByText` over `getByTestId`
- Test user flows: "user clicks button → sees loading → sees result"
- Test error states: "API fails → user sees error message → can retry"

### Page Object Model (for E2E)
- Encapsulate page interactions in page objects
- One class per page/component: `LoginPage.fillEmail()`, `LoginPage.submit()`
- Reuse across tests for maintainability

### What NOT to Test
- Third-party library internals (React, Next.js, wagmi)
- Implementation details (internal state shape, private methods)
- Styles and CSS classes — use visual regression testing instead