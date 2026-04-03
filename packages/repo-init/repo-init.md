---
id: repo-init
stages: [analyzing, techPrep, specGeneration, implementing]
always: true
---

## Repository Context Protocol
Before performing any task in a repository, you MUST:
1. Read `README.md` — architectural overview and setup instructions.
2. Read `package.json` — dependencies and available scripts.
3. Check for `CLAUDE.md` — if it exists, read it for project-specific instructions.
4. Scan the `src/` layout to understand the project's folder structure.
5. In later stages (spec/implementation), also read ALL files in `.workflow/` as the source of truth.