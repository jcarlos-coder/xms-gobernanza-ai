# E2E / Integration Evidence Guide

> Companion to Development Rule 11 (the law). This guide holds the
> mechanics; the rule holds the invariant. Load this file BEFORE running
> integration, E2E, or exploratory system tests.

## What this covers

Integration, E2E, and exploratory system runs — smokes, A/B model
comparisons, adapter PoCs, full pipeline runs, performance measurements —
and everything they produce: logs, result dumps, media, screenshots,
write-ups.

## Layout

```
tests/<type>/                  # <type> = e2e | integration (conventional names)
  README.md                    # committed — the suite's folder vocabulary
  <name>/                      # one folder per run
    README.md                  # committed (mandatory)
    mock/ , playwright/ , …    # committed support subfolders (suite vocabulary)
    evidence/                  # gitignored run products (mandatory)
```

## Naming

- `<name>` is any descriptive slug for what the run targets — an experiment,
  an artifact, a page or route under test, a path. NO date suffix:
  provenance lives in the README's date field.
- If two names collide, disambiguate with a descriptive qualifier or a
  numeric suffix (`<name>-1`, `<name>-2`, …).

## README contract (committed, mandatory)

What it tests / date / exact command / result / verdict / how to regenerate /
the task-feature-change that motivated it. The input list in the README is
the reproducibility contract: every input is named with its location.

## Gitignore (exact pattern)

```
tests/**/evidence/**
!tests/**/evidence/**/
!tests/**/evidence/**/README.md
```

Depth-agnostic: any `evidence/` folder under `tests/` is ignored wherever
the run sits; READMEs inside are rescued. `**/` matches zero or more
directories, so the top-level `evidence/README.md` is rescued too.

Traps (verified against gitignore(5) semantics, git 2.55):

- Never `evidence/` + `!evidence/README.md`: dead negation — a file cannot
  be re-included if a parent directory is excluded.
- Never re-include directories with single-star (`!folder/*/` after
  `folder/*` un-ignores everything inside).
- Rescue directories BEFORE files (`!…**/` then `!…**/README.md`).

## Resolution order (no conflicts with tools or between agents)

1. A tool's own convention wins for that tool's scope, inputs AND outputs —
   e.g. Cypress `cypress/fixtures/`, Playwright `test-results/` and its
   committed `*-snapshots/`.
2. The repo's documented vocabulary wins inside the repo: each suite
   records its folder vocabulary ONCE in a committed `tests/<type>/README.md`
   (precedent: dapr's committed `tests/README.md`). Agents follow it; they
   never invent sibling names.
3. This guide is only the floor.

## Promotion on second use

A support input starts inside its run (self-contained, reproducible from a
fresh clone). When a SECOND run needs it, promote it to the suite level (or
the tool's own location) in the same work unit, updating every run README
that references it — Rule 9 (docs must not lie, same work unit) keeps the
reproducibility contract true. No speculative centralization.

## Inputs/outputs line

Binaries and regenerated run products → `evidence/`. Textual conclusions and
verdicts → the feature doc, the artifact store, or the README. Never the
reverse: a mock inside `evidence/` is a lost mock (gitignored), and a video
inside a feature doc is noise.

## Scope

This binds WHERE evidence lives, not WHEN tests run. The project's existing
gates (functional checks per task, verification gates, strict TDD when
active) decide when tests run.

## Enforcement recipes (violations must be loud, not silent)

- **Scaffold**: the conventional folders and patterns exist from repo
  bootstrap.
- **Meta-test**: `git check-ignore` assertions — evidence ignored (nested
  dummy at two levels), READMEs rescued, support code not ignored.
- **Hook**: check-only pre-commit (it REJECTS, never mutates — mutating
  hooks invalidate native-review receipts) blocking new binaries >1MB
  outside approved asset folders. Version it via
  `git config core.hooksPath .githooks` so it survives clones
  (`.git/hooks/` is not committed).
- **Delegation**: every agent delegation that runs tests names the evidence
  path upfront; a launch without an evidence path is malformed.

## Conventions research (sources, verified 2026-10-02)

- Naming: Playwright's scaffolder offers `e2e/` as the canonical alternative
  to `tests/` (v1.63); Cypress `cypress/e2e/` (v16, since v10); Kubernetes
  `test/e2e` + `test/integration`; dapr `tests/{e2e,integration,perf}` with a
  committed `tests/README.md`; LocalStack and Ansible use
  `tests/integration/`. pytest officially blesses only a top-level `tests/`
  (docs.pytest.org, Good Integration Practices) — type subfolders are
  ecosystem convention, not forbidden.
- Commit vs ignore: Playwright's generated `.gitignore` ignores
  `/test-results/`, `/playwright-report/`, `/blob-report/`; Cypress docs
  officially keep generated assets (screenshots/videos/downloads) out of
  source control AND commit fixtures ("data you control and check in
  alongside your tests"); pytest's `.pytest_cache` self-ignores with `*`;
  the official github/gitignore Python template ignores `.pytest_cache/`,
  `htmlcov/`, `.coverage`, `.tox/`; the Git FAQ recommends against build
  products in repositories. Deliberate exception: small golden/snapshot
  files (Playwright: "You should commit this directory") — reviewable,
  deliberately-updated expectation inputs.
- The negation mechanism is official practice: github/gitignore templates
  ship `.pixi/*` + `!.pixi/config.toml` and `.env.*` + `!.env.example`.
