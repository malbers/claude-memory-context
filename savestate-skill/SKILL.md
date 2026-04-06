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
| **CLOSED threads** (complete) | Domain file + update `context-index.md` + remove from `current.md` |
| Persistent facts (people, relationships) | People files or decision logs |

## Routing Closed Threads

When a thread is CLOSED, ask the user: **"Where should I route this — is there an existing file, or should I create a new one?"**

Default routing by content type:
- Person/relationship thread → `files/people/[name].md`
- Research/synthesis → `files/research/[topic].md`
- Architectural decisions → `files/decisions/[topic].md`
- Implementation work → `files/implementations/[name]/`
- No clear fit → create a new clearly-named file

After writing to the domain file:
1. Update `context-index.md` — add or update the entry for that file
2. Remove the thread from `current.md`

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
5. **CLOSED threads → ask user for routing**, then write to domain file + update context-index.md + remove from current.md
6. **Report back** — tell the user exactly what was saved and where

## Report Format

```
Saved to:
- current.md — [open thread name, 1 line summary]
- files/[domain-file].md — [closed thread name, routed here]
- context-index.md — updated
```

## What NOT to Save

- Conversation chit-chat with no substance
- Things already captured from a prior save
- Duplicate info already in existing files (update, don't duplicate)
