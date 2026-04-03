Review all changed files for security issues. Check for:

1. **XSS vectors**: `dangerouslySetInnerHTML`, unescaped user input in `href`/`src`, template injection
2. **Code execution**: `eval()`, `new Function()`, dynamic `import()` with user input
3. **Secrets exposure**: API keys, private keys, mnemonics, or tokens in client code or git history
4. **Contract safety** (if Web3): parameter validation, reentrancy patterns, unchecked return values
5. **Input validation**: form inputs sanitized before API calls, URL params validated
6. **Auth/CSRF**: state-changing operations protected, session tokens handled securely

## Output Format

For each issue, output a JSON object:
```json
{
  "file": "src/components/Foo.tsx",
  "line": 42,
  "severity": "critical | error | warning",
  "category": "xss | eval | secrets | contract | input | auth",
  "description": "What is wrong",
  "fix": "Recommended fix"
}
```

## Summary Table

End with a markdown table:

| File | Line | Severity | Category | Description |
|------|------|----------|----------|-------------|

Return `passed: true` only if zero critical/error findings.
