---
id: web3-frontend
keywords: [web3, wallet, blockchain]
stages: [specGeneration, implementing, qualityAssurance]
---

## Web3 Frontend Patterns

### Wallet Connection
- Use viem for chain interaction, wagmi for React hooks — never ethers.js
- Handle: connect, disconnect, account switch, chain switch
- Persist connection state with wagmi's built-in storage

### BigInt / Wei Precision
- BigInt arithmetic only: never convert Wei to Number (loses precision beyond 2^53)
- Use `formatUnits(value, decimals)` for display, `parseUnits(input, decimals)` for user input
- Render with locale-aware formatting: `Intl.NumberFormat` after `formatUnits`

### Transaction Lifecycle UI
- States: idle → pending (spinner + "Confirming...") → confirmed (toast) → failed (error toast)
- Show confirmation count for L1 transactions when relevant
- Disable submit button while tx is pending to prevent double-submit
- Use wagmi's `useWaitForTransactionReceipt` for confirmation tracking

### Chain Switching
- Validate `chainId` before any contract interaction
- If wrong network: show banner with "Switch to {chainName}" button
- Use `useSwitchChain()` from wagmi — handle user rejection gracefully

### Contract Interaction Safety
- Validate all parameters before sending transaction (address format, amounts > 0, allowance checks)
- Verify return values from read calls — don't assume shape
- Prevent double-submit: disable button on click, re-enable on tx settle
- Use `useSimulateContract` before `useWriteContract` to catch reverts early

### Error Scenarios
- **User rejected**: show neutral message, re-enable form
- **Insufficient funds**: show balance vs required, suggest amount adjustment
- **RPC failure**: retry with exponential backoff, show "Network issue" after 3 retries
- **Network timeout**: distinguish from rejection, show "Transaction may still be pending"
- **Contract revert**: parse revert reason if available, show human-readable message