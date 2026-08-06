# How to use this template

Don't replace your existing CLAUDE.md with this file. Instead, copy the sections below that you need and append them to your project's CLAUDE.md. If you don't have a CLAUDE.md yet, rename this file to CLAUDE.md and fill in the placeholders.

---

# [Project Name] - Claude Context

## What This Is
[One paragraph: what you're building, why, and where it stands right now.
Be specific - this is the first thing Claude reads every session.]

Example:
> I'm building a customer onboarding tool for B2B SaaS teams.
> Goal: reduce time-to-value from 30 days to 7.
> Currently: early prototype, figuring out the core workflow.

---

## Project Context
- **Goal:** [What you're building or trying to accomplish]
- **Current phase:** [Idea / prototype / building / scaling]
- **Key decisions already made:** [Anything Claude shouldn't re-debate]
- **Constraints:** [Things Claude should always keep in mind]

---

## Session Startup
At the start of every session:
1. Read this file (Claude Code does this automatically)
2. Ask: "Which project are we working on today?"
3. Load `current.md` for that project
4. Ask which thread to pick up
5. Proactively flag when context usage approaches 65-70%

If working across multiple projects, also load `../shared-current.md`.

---

## Capture Discipline
Claude has no memory between sessions. These files are that memory.

**When we go deep on something:** Write it to `current.md` before we move on.

**When we go deep on something specific** (a person, a decision, a research topic):
write it to its own `.md` file, e.g. `files/people/[name].md`, `files/decisions/[topic].md`.

**When the conversation gets long:** Flag it - "We've been at this a while -
want me to save state before we go further?"

**When I correct your approach or confirm a non-obvious choice worked:** Save it as a feedback memory so you don't repeat mistakes or drift from validated approaches.

**Savestate:** If I say `/savestate` or "save state", scan the conversation,
extract what matters, write a dated entry to `current.md`, confirm what was saved.

**Audit log:** Append a one-line entry to `audit-log.md` at the repo root for every non-trivial action you take on my behalf. Format: `[YYYY-MM-DD HH:MM] <action> (auto / approved by user)`. Skip routine reads and internal tool use. See `audit-log.md` for full format and examples.

---

## State Files: Edit, Never Write

**`MEMORY.md`, `current.md`, `audit-log.md`, `CLAUDE.md` and anything under `memory/` are amended with the Edit tool. Never write them whole with Write.**

A full-file write replaces the file. Everything not in the new version is gone, with no error and no warning. If you have not committed recently, it is unrecoverable.

This is the rule most worth moving into code rather than trusting to habit, because it fails at the moment of action instead of announcing itself. `README.md` describes the state-file guard hook that enforces it.

---

## Memory
Persistent memory lives in `memory/`. `memory/MEMORY.md` is the always-loaded index - load it every session.
Memory files capture things worth remembering across sessions: user preferences, feedback on approach, project context, external references.
When you learn something that should persist, write it to a memory file and add a one-line pointer in the right tier.

**Tiered memory** (use the higher tiers once `MEMORY.md` hits ~150 lines):
- **`memory/MEMORY.md`** (Tier 1) - always loaded. Behavioral rules, identity, cross-project preferences. The rule: must apply on at least 80% of sessions.
- **`memory/MEMORY-projects.md`** (Tier 2) - project-gated. Reference with `@memory/MEMORY-projects.md` in each project's `CLAUDE.md` so it loads only when working in that project.
- **`memory/MEMORY-reference.md`** (Tier 3) - on-demand. Loaded only when a specific tool or topic comes up.

Start with Tier 1 only. Tiering is an optimization for when memory grows.

**`@`-imports need forward slashes, even on Windows.** `@D:\projects\repo\memory\MEMORY-projects.md` is accepted, looks correct, and silently never loads. `@D:/projects/repo/memory/MEMORY-projects.md` works. The drive letter and colon are fine; it is the separator.

There is no error when this breaks. The file simply is not there, and every session runs without it while appearing normal.

**Verify by running `/context` and counting the memory files it lists.** Compare that number against how many you expect. Do not verify by reading the import line and judging it plausible, because a broken path looks exactly like a working one.

---

## Key Files
- `current.md` - active threads, decisions, in-progress work (load every session)
- `context-index.md` - index of all context files; load at session start to know what exists
- `memory/MEMORY.md` - memory index Tier 1; load every session
- `memory/MEMORY-projects.md` - memory index Tier 2; reference from project CLAUDE.md
- `memory/MEMORY-reference.md` - memory index Tier 3; load on demand
- `audit-log.md` - running record of non-trivial actions Claude takes on your behalf
- `../shared-current.md` - cross-project threads (load if working across projects)
