# Audit Log

Running record of non-trivial actions Claude has taken on your behalf.

---

## How to use this file

Claude appends one line per non-trivial action. Format:

```
[YYYY-MM-DD HH:MM] <what Claude did> (auto / approved by user)
```

**Tag meanings:**
- `auto` - Claude did this on its own initiative (memory writes, routine syncs, housekeeping)
- `approved by user` - Claude executed after explicit approval (irreversible actions, external messages, deploys, file changes)

**What gets logged:**
- Edits to memory files, `CLAUDE.md`, `current.md`, or configs
- External actions (emails, commits, deploys, API calls on your behalf)
- Approvals you granted (so you can spot patterns)
- Decisions Claude made autonomously

**What doesn't get logged:**
- Routine reads of memory or `current.md` (those happen every session)
- Every grep, search, or file read
- Internal tool use that doesn't change state

Review the log periodically. Archive when it gets long. Use `git log audit-log.md` to spot trends.

---

## Examples (delete once you have real entries)

```
[2026-04-30 14:32] edited memory/feedback_writing_style.md (auto)
[2026-04-30 14:35] sent reply to client@example.com (approved by user)
[2026-04-30 14:40] pushed commit to main (approved by user)
[2026-04-30 14:42] created files/decisions/api-versioning.md (approved by user)
[2026-04-30 15:01] updated current.md - closed onboarding-flow thread (auto)
```

---

## Log
