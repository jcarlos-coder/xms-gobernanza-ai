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

## 5. Persona (optional)

Persona is the voice layer: a `PERSONA.md` tone control panel that the
agent loads at session start and evolves from evidence of how the user
corrects it — with the user's manual edits always winning over learned
adjustments.

This repo publishes **only the placeholder template**
(`persona/PERSONA.template.md`). The working layer — the skill and your
live voice file — lives in the machine's governance implementation, not in
this repo, and is wired into each runtime from there.

### Install (this setup)

1. Place the working layer in your governance implementation folder
   (in this setup: `~/.config/shared-agent-rules/persona/`) with the skill
   (`SKILL.md`, `references/`) and a copy of `PERSONA.template.md` as the
   bootstrap fallback.

2. Create your live voice file from the template. It's personal and
   gitignored in the implementation repo, so pulls never overwrite it:

   ```
   cp ~/.config/shared-agent-rules/persona/PERSONA.template.md \
      ~/.config/shared-agent-rules/persona/PERSONA.md
   ```

3. Point each tool at the implementation's persona folder with one
   symlink — no copies, same one-copy-per-machine principle as the rules
   file:

   - **OpenCode**:
     ```
     ln -s ~/.config/shared-agent-rules/persona \
        ~/.config/opencode/skills/persona
     ```
   - **Claude Code**:
     ```
     ln -s ~/.config/shared-agent-rules/persona \
        ~/.claude/skills/persona
     ```

4. Next session, the agent reads `PERSONA.md` and applies the voice.

### Updating

- Your live `PERSONA.md` is yours: gitignored, never touched by pulls, and
  the agent only appends dated entries to its **Evolución** section.
- Skill and template updates flow through the implementation repo
  (`git pull` there), not through this one.

Other teams: use `PERSONA.template.md` as the starting point for your own
voice layer; the working skill itself is internal to this setup.
