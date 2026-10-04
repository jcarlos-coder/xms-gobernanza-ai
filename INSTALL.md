# Install

This kit is a single, shared source of truth for how an AI coding assistant
should approach any task — correctness discipline, debt handling, research
verification, documentation hygiene, and what it's never allowed to touch on
its own. It's **always-on**: once wired in, it applies to every task in every
project, automatically — no need to invoke it, no risk of it silently not
firing on a task with no obvious trigger keyword.

## 1. Clone it once, somewhere stable

```
git clone <this-repo-url> ~/dev-governance-kit
```

One copy per machine is enough — every project on that machine points to the
same files. Don't copy it into individual project repos; that's how fourteen
slightly-different versions of the same rule happen.

## 2. Point your AI tool's always-on config at it

Add **one line** to whichever file your tool loads at the start of every
session:

- **Claude Code** (`~/.claude/CLAUDE.md`):
  ```
  @~/dev-governance-kit/development-rules.md
  ```
- **OpenCode** (`~/.config/opencode/opencode.json`, top-level `instructions`
  field):
  ```json
  "instructions": ["~/dev-governance-kit/development-rules.md"]
  ```
- **Any other tool** that supports an always-on project/user instructions
  file: point it at the same path the same way. If your tool only supports
  pasting content inline (no file include), paste the contents of
  `development-rules.md` directly — just re-paste it after every update
  instead of relying on the include.

That's the whole install. No build step, no dependencies.

## 3. What happens automatically after that

- On a repo missing basic docs, the agent bootstraps `AGENTS.md`, `README.md`,
  `.gitignore`, and a `docs/` set from the templates in this kit (rule 8) —
  nothing to run by hand.
- `e2e-evidence.md` and `MANIFESTO.md` are reference material the agent reads
  on demand (when it actually runs E2E tests, or if it needs the full
  rationale behind a rule) — you don't need to read them to start, but they're
  there if a rule's "why" isn't obvious.
- `FEEDBACK-LEDGER.md` fills in on its own over time, as a plain-text record
  of corrections the team gives the agent that are worth remembering across
  every project, not just one.

## 4. Updating

Pull the latest version into `~/dev-governance-kit` and every project picks
it up on its next session — nothing to re-point, nothing per-project to
touch.
