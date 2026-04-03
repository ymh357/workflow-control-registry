5-step scientific debugging protocol. Use when a bug persists after 3+ fix attempts.

## Step 1 — Reproduce
- Write the exact steps or command to reproduce the bug.
- Capture the full error output (stack trace, console, network).
- Identify the trigger: what input/action causes the failure?

## Step 2 — Isolate
- Narrow the scope: which file, function, or line causes the error?
- Use binary search: comment out half the code, does the bug persist?
- Check recent changes: `git diff` and `git log --oneline -10` for suspects.
- Verify assumptions: is the data shape what you expect? Log it.

## Step 3 — Hypothesize
- Form exactly ONE testable hypothesis: "The bug occurs because X".
- Predict the outcome: "If I change Y, the error should change to Z (or disappear)".
- Write it down before making any changes.

## Step 4 — Verify
- Make the MINIMAL change to test your hypothesis.
- Run the reproduction steps from Step 1.
- Compare actual vs predicted outcome:
  - Match → hypothesis confirmed, proceed to fix.
  - Mismatch → hypothesis rejected, return to Step 2 with new information.

## Step 5 — Escape
- Apply the fix.
- Run the full test suite, not just the failing test.
- Document: what was the root cause? Add a comment if the fix is non-obvious.
- Check for similar patterns elsewhere in the codebase (the same bug may exist in other files).

## Anti-Patterns to Avoid
- Do NOT make multiple changes at once — you won't know which one fixed it.
- Do NOT guess-and-check without a hypothesis.
- Do NOT ignore the stack trace — read every line.
- Do NOT assume the bug is where the error message points — it's often upstream.
