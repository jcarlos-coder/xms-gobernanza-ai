## Development Rules

> **Scope note (human + AI):** this file is always-on — imported directly into
> every session via CLAUDE.md, not a skill. It governs HOW any code task is
> approached, not a procedure for one task type; skills are contextual and can
> silently fail to fire on keyword-free tasks ("fix this typo"), which is
> exactly where these rules matter most. Every line here has a recurring
> per-session, per-project cost, so keep additions short: if a rule needs
> heavy mechanics, put the obligation here and the mechanics in a referenced
> file (see rule 11 → `e2e-evidence.md`). Name capabilities, not products: a
> current binding (engram, codegraph, a skill registry, a UI framework) is an
> example of what satisfies a capability today, never the contract itself.
> Full rationale: `MANIFESTO.md` in this same directory.

1. **Think before writing code: does it exist, then what/how/where/why, then
   the smallest correct solution.** Before any non-trivial change, resolve the
   design in a four-step pass:
   - **Does this need to exist?** (YAGNI) If it doesn't solve a real, stated
     need, don't build it — and don't spend tokens designing it. Deliberate
     architecture or learning decisions count as real needs.
   - **What / how / where / why.** What is the behavior, how does it fit,
     where does it live (which layer/adapter), why does it exist. If any
     answer is uncertain — project purpose, logic, architecture fit,
     maintainability — do not start editing: research, think, propose, then
     execute.
   - **Smallest correct detour.** Before writing, ask "does it already exist /
     did I already decide / did I already do this?" and reuse in this order:
     persistent memory and skill/procedure discovery (if configured) → code
     already in the codebase (code-structure search, or grep) → stdlib →
     native platform feature → installed dependency → a one-liner → only then
     the minimal module. Cascade per rule 12 when a step's tool isn't
     available.
   - **Never trim correctness.** Validation, error handling, security, and
     accessibility are never cut to save lines.
   Exempt: trivial, already-understood mechanical edits skip the ceremony.

2. **Re-check YAGNI before closing — review the diff for over-engineering.**
   Before declaring a change that wrote code as done, review the diff once for
   anything that does not need to exist: dead code, speculative abstractions,
   unused wrappers, duplicated utilities. Delete it before verification, not
   later. This is the closing mirror of rule 1; it never trims correctness
   (validation, error handling, security, accessibility).

3. **Avoid debt by default; gate twice before deferring.** Don't leave debt
   just to move faster — take the clean solution if it costs the same effort.
   A shortcut only clears to defer if it passes both: **proximity** (not in
   the dependency path of current work — debt in-path gets fixed now, before
   the blast radius grows) and **justification** (a real, specific reason,
   not a weak excuse). Either gate fails → fix it now, before anything else.
   Passing both still requires the formal contract in rule 7 — never left
   implicit.

4. **Split view markup and view logic into separate files.** Wherever a view
   can mix code with presentation, keep the markup (template/JSX/HTML) in one
   file and its state, effects, and data logic in another, so the logic is
   testable without rendering — today that means a custom hook in React, a
   composable in Vue, a script module in Astro. Projects without a UI layer
   are exempt.

5. **Check current sources before trusting your memory of a library, API, or
   dependency.** Training data goes stale; repos and APIs change after any
   cutoff. Before using or recommending one, check its current source —
   official docs, the target repo's README/changelog, or the version
   actually installed — instead of recalling it from memory. Well-known,
   stable primitives are exempt.

6. **Weigh your solution against existing ones before coding and explicitly
   approve or reject it — thorough research doesn't make it correct.**
   Research can be fresh, well-sourced, and still wrong: a reinvented wheel,
   a biased source, or a path that hits a blocker something else already
   solved. Judge it against prior art — is this the simplest option, does
   something else already solve it. Skipping this wastes the project's time,
   tokens, and resources on a path that didn't need to be taken (e.g.,
   building a custom CMS when adopting an existing one's model was simpler).

7. **Justified debt is a contract, not a footnote.** When rule 3 allows a
   justified shortcut, record a formal debt contract before moving on:
   - **Marker**: tag the shortcut in code with a `[DEBT]` marker so it is
     greppable and never silent.
   - **Fields**: what was shortcut, why it's justified (the reason), the
     target spec (how it should finally behave), and the concrete steps to
     resolve it.
   - **Roadmap slot**: append the steps to the matching item in the project
     roadmap (`CONTEXT.md` plan) so the debt has a defined home and a
     resolution path — "later" becomes a scheduled step, not a hope.
   - **Ledger**: mirror the contract into persistent cross-session memory if
     configured, for tracking beyond this repo — the exact binding (which
     memory system, which topic/tag) lives in each runtime's own config, not
     here. Cascade per rule 12 when unavailable; the roadmap slot above is
     already the primary record and doesn't depend on this layer.
   A deferred shortcut with no contract is an implicit shortcut — disallowed
   by rule 3.

8. **Bootstrap the minimal project doc set when starting a new project.**
   When the user begins authorized substantive work in a repository they own
   that is missing one or more of the minimal docs, create the missing ones (announcing
   what was created) before the first source change:
   - Root: `AGENTS.md` (copy the template at
     `project-agents-template.md` and fill the
     stack-specific parts), `README.md` (copy
     `readme-template.md`),
     `.gitignore` (secrets, env files, caches, build artifacts, local data).
   - `docs/`: `context.md` (purpose, structure, domain vocabulary, known
     environment issues),
     `roadmap.md` (copy `roadmap-template.md`;
     the scheduled home for every `[DEBT]` contract), `changelog.md` (copy
     `changelog-template.md`).
   Never auto-create files in a repository you are only visiting, reading, or
   answering questions about. The trigger is the start of real, authorized
   work — not opening a repo.

9. **Docs must not lie: update maintained docs in the same work unit.** When
   a change alters anything a maintained doc describes — purpose, structure,
   phases, commands, guardrails — update that doc in the same work unit as
   the code, never later. Which docs are maintained is named by the
   repository's own AGENTS.md; in projects bootstrapped per rule 8 that
   means `docs/context.md`, `docs/roadmap.md`, `docs/changelog.md`, and
   `README.md`. Preserve each doc's declared structure — its in-file format
   contract — restructuring only when the task explicitly calls for it.
   Trivial edits that alter nothing documented are exempt, and the rule
   never applies in repositories you are only visiting.

10. **Attribute failures to the environment before blaming repo code.** When
    a command fails, verify with the project's canonical runner (its
    venv/toolchain, not the system default) — always, with or without
    external tooling. Check persistent memory or `context.md`'s known-issues
    section first, if either is available; their absence doesn't block the
    verification. Document only canonical-runner-verified commands, and
    record any environmental misattribution — in memory if available, in
    `context.md` otherwise — so it isn't repeated. Cascade per rule 12,
    Profile B.

11. **Write E2E/integration test evidence inside the repo, under
    `tests/e2e/<name>/` or `tests/integration/<name>/`** — never `/tmp`,
    session scratch, or anywhere outside the repo (MANDATORY unless the user
    overrides). This covers E2E, integration, and exploratory system runs and
    everything they produce. Each run folder holds a committed `README.md`
    and a gitignored `evidence/` subfolder for run products (logs, dumps,
    media); everything else, including support code like mocks, is
    committed. Create the folder if missing, and name the evidence path
    upfront when delegating a test run. This binds WHERE evidence lives, not
    WHEN tests run. Read `e2e-evidence.md`, alongside this file, BEFORE
    running such tests — it holds naming, README fields, and enforcement
    (check-ignore meta-test, pre-commit size gate).

12. **Declare and cascade when an external capability fails** — never
    degrade in silence. Two risk profiles, not one:
    - **Profile A (causally-justified feedback, cross-project by nature):** a
      correction or confirmation tied to a documented incident, a repeated
      violation, or a standing decision. Cascade: persistent cross-session
      memory first (if configured); if unavailable, `FEEDBACK-LEDGER.md`,
      alongside this file, is the zero-infra mirror (a dated, append-only
      ledger — `date | rule | why | scope`, one line per entry) — every
      Profile A save is dual-written there, never instead-of. If both are
      unavailable, do not
      assume no precedent exists: ask before touching anything with real
      blast radius.
    - **Profile B (everything else — code intelligence, skill/procedure
      discovery, project-scoped decisions with no reach beyond this project
      and no documented incident, interaction preferences):** the configured
      tool for that capability first; its project-local fallback if one
      exists (grep per rule 1, or `context.md`/`roadmap.md` if the repo was
      bootstrapped per rule 8); if nothing is available, declare the absence
      once per session/task and continue — acceptable degradation, not a
      blocker.
    - **When scope and cause disagree, scope wins:** if it could matter
      beyond this one project, it's Profile A even without a documented
      incident yet — a standing decision doesn't need a prior incident to
      already be worth preserving across projects.
    - Rule 7's `[DEBT]` code marker already has no external dependency and is
      unaffected by either profile.

13. **Diagnose the root cause the moment a fix doesn't hold — never patch the
    same failure blind a second time.** If the same failure (or a close
    variant) reappears after a fix, that's a signal of an undiagnosed weak
    point, not bad luck: find the actual cause before touching the symptom
    again. Record what you find — the real fix if applied now, or a tracked
    debt entry if deferred. Skipping this burns time and tokens in a
    patch-reapply loop that never converges.

14. **Never touch production secrets, CI/CD pipeline config, or push
    directly to a protected branch without a human explicitly approving that
    specific change.** A standing permission to use AI assistance is not
    approval for these categories — everything else routes through the
    team's normal PR/review process like any other change. If a task seems
    to require one of these, stop and ask, naming exactly what you'd change
    and why.
