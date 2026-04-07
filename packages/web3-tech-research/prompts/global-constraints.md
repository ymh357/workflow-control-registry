## Global Constraints — Web3 Tech Research

### The Cardinal Rule

**On-chain facts override all other sources.** If a blog post says "Project X uses Protocol Y" but the deployed contract inherits from Protocol Z, the on-chain fact wins.

### Source Hierarchy (strict ordering)

1. **On-chain data** — contract source code on Etherscan/BscScan, read function results, tx receipts
2. **Protocol deployment pages** — official lists of deployed contracts and supported chains
3. **Official documentation** — docs.project.xyz, developer guides
4. **Official blog posts / announcements** — may contain aspirational or outdated claims
5. **Third-party analysis** — use for context only, always verify independently

### Citation Standards

- Every factual claim must have an inline source link: `[description](url)`
- On-chain facts must link to block explorer (Etherscan, BscScan, etc.)
- When a fact is verified on-chain, prefix with: `[On-chain verified]`
- When a fact comes from docs only, prefix with: `[Docs claim — pending verification]`
- Speculation must be marked: `[Inference]` or `[Unverified]`

### Research Quality

- Enumerate ALL major solutions before narrowing down
- Cross-reference multiple sources for each claim
- Record supply amounts, TVL, and transaction counts to gauge actual usage

### Output Standards

- All research intermediate products go to `.workflow/` as markdown files
- Every file must have a creation date and "Sources Consulted" table at the top
- Do NOT fabricate technical details. If uncertain, search the web to verify.

### Language

- Research notes and internal documents: English
- Final deliverables for the team: Chinese (Simplified), code/identifiers in English
- Address the reader as "你" in Chinese content
