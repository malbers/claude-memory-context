# Project Memory (Tier 2)

Project-gated memory. Loaded by individual project `CLAUDE.md` files when working in that project. **Not** auto-loaded at global session start.

---

## When to put a memory here

A memory belongs in Tier 2 when:
- It applies to one specific project, not all your work
- It would be dead weight when working on a different project
- Project deadlines, ongoing work context, project-specific feedback

If a memory applies on at least 80% of your sessions, it belongs in Tier 1 (`MEMORY.md`) instead.

---

## How to wire this in

In each project's `CLAUDE.md`, add a line that loads this file:

```
@memory/MEMORY-projects.md
```

Claude Code resolves `@` references and pulls the file in at session start - but only when that project's `CLAUDE.md` is loaded. So the project memory follows the project.

---

## Format

Same as `MEMORY.md` - one line per memory, under 150 characters, linking to a memory file in `memory/`.

```
- [project_a_deadline.md](memory/project_a_deadline.md) - Project A ships 2026-06-15, no scope changes after 2026-05-30
- [project_a_style.md](memory/project_a_style.md) - Project A uses snake_case for everything, no exceptions
- [project_b_legacy.md](memory/project_b_legacy.md) - Project B has a legacy API; never change response shapes without bumping version
```

---

## Project A
- (no entries yet)

## Project B
- (no entries yet)

## Cross-project (but not 80% of sessions)
- (no entries yet - if a memory hits 80% session usage, promote it to Tier 1)
