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

---

## Memory
Persistent memory lives in `memory/`. `memory/MEMORY.md` is the index - load it every session.
Memory files capture things worth remembering across sessions: user preferences, feedback on approach, project context, external references.
When you learn something that should persist, write it to a memory file and add a one-line pointer in MEMORY.md.

---

## Key Files
- `current.md` - active threads, decisions, in-progress work (load every session)
- `context-index.md` - index of all context files; load at session start to know what exists
- `memory/MEMORY.md` - memory index; load every session for persistent preferences and feedback
- `../shared-current.md` - cross-project threads (load if working across projects)
