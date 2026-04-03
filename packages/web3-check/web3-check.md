Review all Web3-related code for correctness and safety. Check for:

1. **BigInt handling**: any use of `Number()` on Wei values, floating-point arithmetic on token amounts
2. **Chain validation**: contract interactions without chain ID verification, missing network switch prompts
3. **Transaction UX**: missing pending/confirmed/failed states, no loading indicator during tx, double-submit possible
4. **Error handling**: unhandled user rejection, missing insufficient funds check, no RPC failure fallback
5. **Wallet lifecycle**: disconnect handling, account change detection, reconnection on page refresh
6. **Contract calls**: missing parameter validation, unchecked return values, no simulation before write

## viem/wagmi Specific Checks

- Prefer `useReadContract` over manual `publicClient.readContract` in React components
- Use `useWaitForTransactionReceipt` for transaction confirmations, not polling
- Chain config must use `viem/chains` canonical definitions, not custom chain objects
- `useAccount` status must handle: connecting, reconnecting, connected, disconnected
- `useWriteContract` must have `onError` handling for UserRejectedRequestError
- Format values with `formatEther`/`formatUnits`, never manual division
- Parse values with `parseEther`/`parseUnits`, never manual multiplication

## Output Format

For each issue:
```json
{
  "file": "src/hooks/useSwap.ts",
  "line": 23,
  "severity": "critical | error | warning",
  "category": "bigint | chain | tx-ux | error | wallet | contract",
  "description": "What is wrong",
  "fix": "Recommended fix"
}
```

Output a summary with pass/fail status per category.
