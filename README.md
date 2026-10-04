# Structured Development Toolkit

Repository: [github.com/jcarlos-coder/xms-gobernanza-ai](https://github.com/jcarlos-coder/xms-gobernanza-ai)

What this repo is: a working set of tools and rules for AI-assisted
development that replaces improvisation with a system — and the 14 rules in
[`development-rules.md`](./development-rules.md) that tie them together.

## Vibe coding vs. structured development

**Vibe coding** means accepting what an AI assistant produces largely on
trust — intuition over verification, moving fast without a record of why a
decision was made, and no mechanism to catch a silent failure before it
compounds. It's a real, industry-wide pattern, not a flaw specific to any one
team: when AI can generate plausible-looking code in seconds, the natural
friction that used to force a second look disappears unless something is
built to replace it.

**Structured development**, as used here, doesn't mean slower or more
bureaucratic. It means four things are always true, automatically, without
anyone having to remember to ask for them:

- Every non-trivial decision is checked before it's acted on (does this need
  to exist, has it been done before, is this really the simplest option).
- Every shortcut taken on purpose is recorded, not just remembered.
- Every failure is diagnosed before it's patched again.
- Nothing silently degrades — a missing tool, a stale assumption, or a weak
  point gets flagged out loud, not quietly worked around.

That's what the tools below do, and what the rules file encodes as an
always-on contract, not a checklist someone has to remember to run.

## The tools

### Claude Code
AI coding agent that runs in the terminal, understands the repo it's working
in, and executes multi-step tasks (edits, git, tests) through natural
language.
**What it contributes:** the runtime this whole toolkit plugs into — the
always-on rules file below loads automatically into every session.
**Get it:** [github.com/anthropics/claude-code](https://github.com/anthropics/claude-code) · install: `curl -fsSL https://claude.ai/install.sh | bash`

### OpenCode
Open-source AI coding agent, also terminal-based, provider-agnostic (not
locked to one model vendor).
**What it contributes:** a second, independently-maintained runtime that can
load the exact same rules file — useful if the team doesn't want to depend
on a single vendor's tool.
**Get it:** [github.com/sst/opencode](https://github.com/sst/opencode) ·
[opencode.ai](https://opencode.ai)

### gentle-ai
Configuration layer for AI coding agents (works with Claude Code, Cursor,
OpenCode, Codex, and others). Bundles persistent memory, Organic/Spec-Driven
Development workflows, curated skills, MCP server setup, and an optional
bounded code-review process — open source, no vendor lock-in.
**What it contributes:** the orchestration underneath the SDD workflow and
the review/governance process mentioned below — it's not a separate thing to
adopt on top, it's the layer that wires the rest together.
**Get it:** [github.com/Gentleman-Programming/gentle-ai](https://github.com/Gentleman-Programming/gentle-ai)

### Engram
Persistent memory for AI agents — a single local binary (SQLite-backed, no
Docker/Node/Python required) that any MCP-compatible agent can read and write
to. The agent records decisions as it works and searches that history before
asking a human to repeat context.
**What it contributes:** cross-session traceability — the "why was this
built this way" survives past the conversation that decided it, which is
exactly the trazabilidad gap a team moving off vibe coding needs closed.
**Get it:** [github.com/Gentleman-Programming/engram](https://github.com/Gentleman-Programming/engram)

### CodeGraph
A pre-indexed, auto-syncing map of the codebase (symbols, call sites,
dependencies) that an agent queries instead of guessing from a plain text
search.
**What it contributes:** answers to "who calls this" or "what breaks if I
change this" without reading the whole repo first — fewer wrong guesses,
fewer wasted tokens re-deriving context that's already indexed.
**Get it:** [github.com/colbymchenry/codegraph](https://github.com/colbymchenry/codegraph)
(confirmed from `gentle-ai`'s own source — several unrelated projects share
the name, this is the one gentle-ai installs) · package: `@colbymchenry/codegraph@latest`

### Context7
Fetches current, version-accurate documentation and code examples for a
library or framework directly into the agent's context, instead of relying
on whatever got memorized during training (which can already be stale by
the time a model ships).
**What it contributes:** the actual mechanism behind rule 5 ("check current
sources before trusting your memory") — not just the policy that says to
verify, but the tool that does it, mid-task, without leaving the editor.
**Get it:** [github.com/upstash/context7](https://github.com/upstash/context7)
— MIT license, free (no API key needed for basic rate limits).

### SDD (Spec-Driven Development) / Organic-Driven Development
Not a separate download — a workflow gentle-ai already provides: explore →
propose → spec → design → tasks → apply → verify → archive, so a non-trivial
change has a written intent and a plan before code gets written, and a record
of what shipped afterward.
**What it contributes:** the opposite of "ask the AI to build X and see what
comes out" — a change exists on paper, reviewable, before it exists in code.

### Persona (optional, internal)
A small per-user file that tunes an agent's conversational tone and learns
from how it's corrected over time. Not a public tool — something built
in-house; mentioned here because it's part of this setup, not something to
install.

## Our own rules — `development-rules.md`

Everything above is infrastructure; this file is the behavior layer on top
of it. 14 rules, always loaded into every session automatically (not
something anyone has to remember to invoke), covering: thinking before
building, closing out over-engineering, when debt is allowed and how it gets
recorded, keeping docs honest, verifying sources instead of trusting
training-data memory, confronting research before coding instead of trusting
it blindly, diagnosing root cause instead of patching blind twice, and a hard
boundary on what the AI can touch without a human's explicit, per-change
approval.

Every one of these 14 rules was tested before being accepted — read cold, in
isolation, by two different models (a smaller and a larger one), against
concrete test cases, specifically to catch rules that sound right but get
misread in practice. See [`MANIFESTO.md`](./MANIFESTO.md) for the full
design rationale, and [`INSTALL.md`](./INSTALL.md) to wire this into a
project.
