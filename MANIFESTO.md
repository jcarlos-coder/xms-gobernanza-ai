# Shared Agent Rules — Manifesto

## Why `development-rules.md` is always-on, not a skill

A **skill** activates when its description matches the current task's intent —
lazy-loaded, contextual, opt-in by design. It fits a specific kind of task:
deploying, writing PDFs, running an SDD phase.

`development-rules.md` is different in kind, not just in scope. It's a
standing behavioral contract that must apply to every code task — including
the boring, keyword-free ones ("fix this typo", "add an endpoint") where no
skill-matcher would ever fire. If these rules depended on contextual
triggering, the riskiest tasks — the ones with no distinctive vocabulary —
would be exactly the ones that silently skip YAGNI discipline, debt tracking,
or source verification.

That's why this file is `@`-imported directly in `CLAUDE.md`: it's injected
into every session, every project, unconditionally. No matching, no opt-in,
no risk of silent non-application.

## The pattern this implies

- **Always-on**: short, universally-applicable principles that must never be
  skipped — this file.
- **Lazy-loaded**: heavy mechanics for one specific recurring situation — a
  referenced file or skill, read only when that situation actually fires.

Rule 11 (E2E evidence) already follows this: the obligation is a few lines in
`development-rules.md`; the folder structure, naming, and precedence live in
`e2e-evidence.md`, read only when E2E/integration tests are actually run.

## The cost tradeoff

Every line added to `development-rules.md` has a **recurring** cost: it is
re-loaded in every session, of every project, forever — not a one-time cost.
Before adding detail there, ask: does this need to be known at all times, or
only when a specific situation fires? If the latter, it belongs in a
referenced file, with only a pointer and the non-negotiable obligation kept
in the always-on file.

## Capability, not product: why rule 12 names no single tool as mandatory

`development-rules.md` may be read by more than one AI coding tool, and
different runtimes rarely agree on which memory, code-intelligence, or skill
systems are configured: one may have a persistent-memory MCP server and a
code-graph tool both wired in, another may have memory but no code-graph,
another may have neither and rely on a completely different backend instead.
No single product is guaranteed common to all of them. A rule that hardcodes
"use `<product>`'s `mem_search`" is, in at least one real setup, an
instruction for a tool that was never configured.

The fix is the same discipline as a hexagonal port vs. an adapter: a rule
states the **capability** it needs (persistent cross-session memory,
code-structure search, skill/procedure discovery), and names a current
binding only as a parenthetical example, never as the requirement itself.
Tool-specific operational detail still deserves a home — but a runtime-scoped
one, like `CLAUDE.md`'s own `## CodeGraph` section, not the portable file
meant to survive a tool swap unedited.

## Why `FEEDBACK-LEDGER.md` exists (rule 12, Profile A)

Of the three capabilities rule 12 covers, one carries real cost if lost
silently: causally-justified feedback (a correction tied to a documented
incident, a repeated violation, a standing decision) is architecturally
different from code-structure or skill-discovery gaps — SOLID/YAGNI/KISS
govern code shape, not organizational memory of past incidents, so losing
this class of memory risks repeating a known mistake, not just a less
convenient session.

This class of memory is also cross-project by nature (it describes how this
team works, not how one repo is built), so a project-local doc (`context.md`,
`roadmap.md`) cannot be its fallback — those are scoped to one repo. The only
thing portable across every runtime, with zero infrastructure dependency, is
a flat file: a self-written, dated, append-only ledger, no MCP server
involved. (If your tooling also keeps a personal tone/preference ledger of
its own, the same pattern applies there — kept in a separate file, since the
two update on different triggers and serve different readers.)

## For future maintainers (human or AI)

Don't "clean up" `development-rules.md` by converting it into a skill for
organizational tidiness — that reintroduces the exact gap this design avoids.
If a rule accumulates enough mechanics that it starts to feel skill-shaped,
extract the *mechanics* to a referenced file and keep the *obligation* in the
always-on file.
