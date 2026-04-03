Pre-PR completion checklist. Run before creating a pull request.

## Code Quality
- [ ] All files match the spec in `.workflow/spec/`
- [ ] No `console.log` or debug statements left in production code
- [ ] No `// TODO` without a linked issue or ticket
- [ ] No commented-out code blocks
- [ ] All imports are used (no dead imports)
- [ ] No `any` types — use `unknown` with type guards

## Tests
- [ ] `pnpm test` passes with zero failures
- [ ] New components have corresponding test files
- [ ] Test coverage for P0/P1 test cases from spec

## Build
- [ ] `pnpm build` succeeds with zero errors
- [ ] No new TypeScript errors (`pnpm tsc --noEmit`)
- [ ] No new ESLint warnings (`pnpm lint`)

## Git Hygiene
- [ ] Branch is up to date with base branch (`git pull --rebase origin main`)
- [ ] Commit messages are descriptive (not "fix" or "wip")
- [ ] No untracked files that should be committed
- [ ] No sensitive files staged (.env, credentials, keys)

## Documentation
- [ ] `.workflow/implementation-log.md` is up to date
- [ ] Deviations from spec are documented with rationale
- [ ] Complex logic has inline comments explaining "why"

## Final Verification
Run these commands and confirm all pass:
```bash
pnpm tsc --noEmit
pnpm lint
pnpm test
pnpm build
```
