## Global Constraints

- **Plan-mode agents have no tools.** `report-generator`, `risk-assessment`, and `fix-validator` run with `permission_mode: plan`. They must never attempt file reads, shell commands, or MCP calls. All reasoning must derive solely from injected context fields.
- **code-fix-suggestion has full tool access.** It may read files, grep the repo, and call context7. Restrict writes — do not edit or create files; only read and analyze.
- **context7 usage requires two steps.** Always call `resolve-library-id` first, then `query-docs`. Never pass a raw package name directly to `query-docs`.
- **Do not speculate about unread files.** `code-fix-suggestion` must only include files in `codeChanges` that it has actually read. No inferred or assumed paths.

## Error Recovery

- **Missing input fields**: every agent must still produce all required output fields. Use safe defaults (empty array, false, 0, "unknown") and surface the gap in the relevant text field (`description`, `feedback`, `mitigations`).
- **context7 unavailable**: fall back to reading source files directly for API usage patterns. Do not stall or return an empty fix.
- **Vague annotation**: produce a best-effort output with low confidence and explicit notes about what information is missing — do not refuse to output.
- **Retry context**: on `fix-validator` failure, `validateFix` retries up to 2× back to `analyzeItem`. The retry receives the prior `blockers` array; `code-fix-suggestion` and `risk-assessment` should treat that as corrective feedback, not a fresh run.

## Output Contract

- All output fields defined in each stage's Outputs section are required. Never omit a field, even if its value is null, empty array, or zero.
- String fields must be non-null. Use `""` rather than omitting the key.
- Boolean fields must be strict booleans (`true`/`false`), not truthy strings or integers.
- `riskLevel` must be exactly one of: `"low"`, `"medium"`, `"high"`, `"critical"`.
- `estimatedEffort` must be exactly one of: `"trivial"`, `"small"`, `"medium"`, `"large"`.
- `confidence` and `score` are numeric; `confidence` is float 0.0–1.0, `score` is integer 0–100.
- Do not include commentary, markdown, or explanation outside the required JSON structure.