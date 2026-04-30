# Memory Index

This file is auto-loaded every session. Each entry is one line linking to a memory file.
Keep entries under 150 characters. Keep total file under 200 lines.

---

## Tiered Memory System

This file (`MEMORY.md`) is **Tier 1** - always loaded. Behavioral rules that apply everywhere, identity, cross-project preferences. The rule: if a memory doesn't apply on at least 80% of sessions, it doesn't belong in Tier 1.

Two companion tiers exist for when memory grows past what makes sense to load every session. Don't worry about them on day one:

- **`MEMORY-projects.md`** (Tier 2) - project-gated. Loaded by project `CLAUDE.md` files when working in that project.
- **`MEMORY-reference.md`** (Tier 3) - on-demand. Loaded only when a specific tool or topic comes up.

When `MEMORY.md` grows past ~150 lines, move project-specific entries to Tier 2 and rare-but-useful references to Tier 3.

---

## User
- (no entries yet - Claude adds these as it learns about you)

## Feedback
- [example-feedback.md](memory/example-feedback.md) - Example feedback memory, delete and replace with your own

## Project
- (no entries yet - ongoing work context, goals, deadlines)

## Reference
- (no entries yet - pointers to external systems and resources)
