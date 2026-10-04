# AGENTS.md

> Project instructions for every coding agent working in this repository.
> Bootstrap copy of this kit's `project-agents-template.md`
> (development rule 8). Fill the two sections marked FILL at init.

## Read first
- `docs/context.md` — purpose, structure, domain vocabulary
- `docs/roadmap.md` — phases and the next step

## Project rules
1. **Docs must not lie.** When behavior changes, update `docs/context.md` and
   `docs/roadmap.md` in the same work unit as the code that changed them.
   Preserve each doc's declared structure; restructure only when explicitly
   asked.
2. **Changelog.** Record user-facing changes in `docs/changelog.md` (Keep a
   Changelog format) as part of each work unit.
3. **Debt contracts.** Every `[DEBT]` marker needs a contract scheduled in
   `docs/roadmap.md` (global rule 7). No contract, no shortcut.
4. **Ignore file is law.** Never commit secrets, `.env`, local databases,
   caches (`__pycache__`, `node_modules`, `target/`, `dist/`), or logs.
5. **Conventional commits.** No AI attribution lines.

## Commands
<!-- FILL at init: test, lint, build, run — exact commands -->

## Guardrails
<!-- FILL at init: files and directories agents must never touch -->
