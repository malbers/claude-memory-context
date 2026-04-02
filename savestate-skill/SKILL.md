---
name: savestate
description: Use when the user says 'save state', 'savestate', or '/savestate' — or when PreCompact fires and the thread is deep. Saves the current thread to the relevant project current.md before context is lost.
---

# Save State

## Overview

Captures the current conversation thread and writes it to the right place before
compaction or session end. Prevents context loss across long working sessions.

## When to Use

- User says `/savestate`, `save state`, or `save this`
- PreCompact hook fires and the thread is substantive (3+ meaningful turns)
- User is about to step away mid-thread
- A long working session is wrapping up

## What to Capture

Scan the full conversation and extract:

1. **What was worked on** — topic, task, or project area
2. **Decisions made** — anything that changes how we'll work going forward
3. **Changes made** — files updated, code written, configs changed
4. **New context learned** — facts relevant to the project
5. **Open items / next steps** — what's pending, what was parked, what comes next

## Where to Write

Write to `current.md` in the active project folder.

If working across multiple projects, write to `shared-current.md` for threads
that span more than one project.

## Format for current.md Entry

Add under the relevant thread heading (or create a new one):

```
**[Thread name] — updated [date]**
[2-3 sentence summary of what was done]
Key decisions: [bullet if applicable]
Open: [what's still pending]
Next: [immediate next step]
```

## Steps

1. Identify the active project from the conversation
2. Scan for extractable content using the categories above
3. Write a dated entry to the project's `current.md`
4. Report back — tell the user what was saved and where, in one short summary

## Report Format

```
Saved to:
- [project]/current.md — [thread name, 1 line summary]
[Any open items flagged]
```

## What NOT to Save

- Conversation chit-chat with no substance
- Things already captured in current.md from a prior save
- Duplicate info already written to the file
