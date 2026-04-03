## Global Constraints

### Tool Usage
- `prAnalysis` is the only stage permitted to invoke `gh` CLI commands to fetch PR data. All other stages receive PR information exclusively through injected context (`prInfo`).
- When calling `gh`, always pass `--repo owner/repo` explicitly; never rely on inferred git remotes.
- `securityScan` and `performanceScan` are read-only stages: permitted tools are Read, Glob, Grep, and Bash for read-only commands only. Edit, Write, and any state-mutating Bash commands are forbidden.
- `reviewSynthesis` operates in plan mode and has no tool access; it must work solely from injected context fields — no file reads, no shell commands, no `get_store_value`.

### File System
- No stage may write, edit, move, or delete any file inside the repository being reviewed.
- Do not create temporary files on disk. Stages must not write scratch files anywhere.
- When reading files from the PR repository, derive paths from `prInfo.changedFiles` only — do not crawl or enumerate directories outside the listed files.

### Security and Safety
- Findings must describe vulnerabilities in plain language (e.g., "unsanitized input passed to SQL query at `users.ts:42`") — never include working exploit code, payloads, or proof-of-concept attack scripts.
- Do not reproduce secret values, tokens, or credentials found in the diff. Reference their location (file + line) and classify as "leaked secret" — redact the value.
- CWE or OWASP identifiers should accompany findings where applicable but are not required.

### Error Recovery
- If `gh` commands fail, record the error in the output's `assumptions` or `summary` field and continue with whatever data is available; do not abort the pipeline.
- If a diff or file exceeds 500 KB, process the first 500 KB and flag `truncated: true` in the output so downstream stages are aware of incomplete coverage.
- If an expected upstream context field is absent or null, set the corresponding output array to `[]` rather than throwing. Note the absence in the stage's `summary`.

### Output Contract
- Every issue object in `securityFindings.issues` and `performanceFindings.issues` must contain: `severity` (string), `title` (string, ≤ 80 chars), `file` (repo-relative path or `"unknown"`), and `description` (string).
- Severity values are lowercase strings. Security: `"critical"`, `"high"`, `"medium"`, `"low"`, `"informational"`. Performance: `"blocking"`, `"warning"`, `"info"`.
- `passed` is a boolean — `true` means no blocking issues were found; `false` means the PR should not merge without addressing them.
- `reviewDraft.verdict` must be exactly one of: `"approve"`, `"request_changes"`, `"comment"`. No other values are valid.

### Store Key Naming Contract
The pipeline uses these exact store keys — do not use aliases or alternate names:
- `prInfo` — written by `prAnalysis`, read by `securityScan`, `performanceScan`, `reviewSynthesis`, and `postComment` (via `prInfo.prUrl`)
- `securityFindings` — written by `securityScan`, read by `reviewSynthesis`
- `performanceFindings` — written by `performanceScan`, read by `reviewSynthesis`
- `reviewDraft` — written by `reviewSynthesis`, read by `postComment` (via `reviewDraft.body`)
- `commentUrl` — written by `postComment` script, surfaced as pipeline completion URL

### Pipeline Flow Reference
```
prAnalysis → [prInfo]
  → deepReview (parallel)
      ├─ securityScan  → [securityFindings]
      └─ performanceScan → [performanceFindings]
  → reviewSynthesis → [reviewDraft]
  → reviewGate (human confirm, loops back to reviewSynthesis on reject)
  → postComment → [commentUrl]
```

### Feedback Loop Constraint
The `reviewGate` loops back to `reviewSynthesis` only (max 3 loops). The scan stages (`securityScan`, `performanceScan`) are never re-run during a feedback loop. `reviewSynthesis` must be able to revise the draft using only the already-injected `securityFindings` and `performanceFindings` data plus any feedback text provided.
