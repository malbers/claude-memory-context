---
name: savestate
description: Use when the user says 'save state', 'savestate', or '/savestate' — or when PreCompact fires and the thread is deep. Saves the current thread to the relevant project files and memory before context is lost.
---

# Save State

## Overview

Captures the current conversation thread and writes it to the right places before compaction or session end. Prevents context loss across long working sessions.

## When to Use

- User explicitly says `/savestate`, `save state`, or `save this`
- PreCompact hook fires and the thread is substantive (3+ meaningful turns)
- User is about to step away mid-thread
- A long working session is wrapping up

## First Principle: No Knowledge Loss

**The rule is absolute: when closing a thread, preserve everything.** Discoverability beats elegance. If you're unsure whether a piece of context is worth keeping, keep it — the cost of a slightly longer domain file is nothing compared to the cost of reconstructing lost reasoning weeks later.

**Default to routing, not deletion.** When a thread closes, its narrative history belongs in a domain file at full fidelity, even when the underlying artifact (code, skill file, roadmap) already lives elsewhere. The domain file becomes the scannable chronological ledger; the artifact files are authoritative for the *change*, but not for the *why and how*.

**Remove-only closure is a narrow exception, not an optimization.** Only use it when you can honestly answer yes to all three conditions below. When in doubt, route.

## What to Capture

Scan the full conversation and extract:

1. **What was worked on** — topic, task, or project area
2. **Decisions made** — anything that changes how we'll work going forward
3. **Changes made** — files updated, copy written, configs changed
4. **New context learned** — facts about people, projects, relationships, positioning
5. **Open items / next steps** — what's pending, what was parked, what comes next
6. **Thread status** — is this thread OPEN (next steps pending) or CLOSED (complete, no further action)?

## Where to Write

| Content Type | Destination |
|---|---|
| **OPEN threads** (next steps pending) | `current.md` — add dated entry |
| **CLOSED threads** (complete) | Domain file (see routing logic below) + update `context-index.md` + remove from `current.md` |
| Persistent facts (people, relationships) | People files or decision logs |

## Closing a Thread

When a thread is CLOSED, its closure must propagate to **every surface** where the thread lives, in one atomic pass. Drift between surfaces = drift between the user and Claude. Keep them in sync.

### Closure modes

**Route-and-remove (default).** Thread content gets a full-fidelity home in a domain file. Used for every closed thread unless remove-only applies.

**Remove-only (narrow exception).** Only acceptable when ALL THREE conditions hold:
1. The artifact already lives in its permanent home (the actual skill file, code file, roadmap file, etc.)
2. Git history fully captures the change with a descriptive commit message
3. The narrative — what happened, why, what was learned — is fully reconstructable from the artifact + git alone, without the `current.md` entry

Even then, prefer routing. Discoverability beats elegance. Remove-only exists for trivial cleanup threads, not substantive work.

### Route-and-remove procedure

1. Ask the user: **"Where should I route this — is there an existing file, or should I create a new one?"**
2. Write the thread at full fidelity into the chosen domain file with a `## [Thread name] — [date] — CLOSED` header (see "Format for Domain File Entry" below)
3. Update `context-index.md` — add or update the entry pointing at the domain file under the right section
4. Remove the thread from `current.md`
5. If the thread produced a durable rule, reference, or fact, confirm the relevant memory file is created or updated
6. Verify every edit landed (see CRITICAL section below)

### Remove-only procedure

1. Confirm all three criteria above are honestly met
2. Remove the thread from `current.md`
3. If listed in `context-index.md` Active Threads, remove it there too
4. Confirm git history has the descriptive commit (the commit IS the permanent record — if it's not there, you cannot use remove-only)
5. Verify every edit landed (see CRITICAL section below)

### Default routing by content type

- Person/relationship thread → `files/people/[name].md`
- Research/synthesis → `files/research/[topic].md`
- Architectural decisions → `files/decisions/[topic].md`
- Implementation work → `files/implementations/[name]/`
- Cross-project threads → update `shared-context-index.md`
- No clear fit → create a new clearly-named file

### Multi-surface sync requirement

A closed thread has touched up to four surfaces:
- `current.md` — must be cleaned (entry removed)
- `context-index.md` — Active Threads updated; domain file added if routing
- Domain file (if routing) — full-fidelity entry added
- Memory files (if applicable) — any durable rule/reference confirmed in place

All four must be consistent when you're done. Inconsistency across surfaces is the failure mode this section exists to prevent.

**Critical rule: never compress.** Write closed threads at full fidelity. Compression defeats the purpose of the context-index system — you're solving context loss, not creating a summarized version of it.

## Format for current.md Entry (OPEN threads)

```
**[Thread name] — [date]**
[What was done]
Key decisions: [bullet]
Open: [what's still pending]
Next: [immediate next step]
```

## Format for Domain File Entry (CLOSED threads)

Write full fidelity — do NOT compress or summarize. Preserve all context, decisions, and nuance. Use this header:

```
## [Thread name] — [date] — CLOSED
[Full thread content at full fidelity]
Key decisions: [bullet]
```

## Steps

1. **Identify active projects** from the conversation
2. **Scan for extractable content** using the categories above
3. **Determine thread status** — OPEN or CLOSED
4. **OPEN threads → write to current.md**
5. **CLOSED threads → ask user for routing**, then write to domain file + update `context-index.md` + remove from `current.md`
6. **Update memory files** for any persistent new facts
7. **Verify every file-edit claim** — see CRITICAL section below. Any "added X to Y" or "updated Y" line in your summary must be confirmed against the actual file before moving on.
8. **Report back** — tell the user exactly what was saved and where

## CRITICAL: Verify edits before claiming them

**Failure mode this prevents:** phantom writes — a savestate summary that narrates a file edit ("Added X to Y") when the edit never actually landed. The prose gets written, the file doesn't, and the drift goes unnoticed for days.

**Rule:** Any savestate line of the form "added X to file Y", "updated Y with X", "wrote X into Y", or similar MUST be verified before the savestate is considered complete.

**Method:** Re-read the target file, or grep it for a distinctive anchor string pulled from the new content. Confirm the new content is actually present on disk right now.

**If absent:** either do the edit now, or rewrite the summary honestly to match reality. Never both claim and skip.

**Example:** If the summary says `Added "API audit" item to project-roadmap.md`, grep that file for `API audit` before finalizing. No match → fix the file or fix the summary.

**Never trust prior turns.** Even if you remember doing the edit earlier in this session, verify the file state now. Tool calls can fail silently, get reverted, or land at the wrong path. Memory is not evidence; the file is.

This section exists because phantom-write drift is a real, recurring failure mode in long sessions. Treat it as a hard gate on completing a savestate.

## Report Format

```
Saved to:
- current.md — [open thread name, 1 line summary]
- files/[domain-file].md — [closed thread name, routed here]
- context-index.md — updated
- memory/[file].md — [what was updated]
[Any open items flagged]
```

## What NOT to Save

- Conversation chit-chat with no substance
- Things already captured from a prior save
- Duplicate info already in existing files (update, don't duplicate)
