# claude-memory-context

A lightweight file structure that gives Claude Code persistent memory across sessions.

Claude resets every session. No memory of what you built yesterday, what you decided last week, what you asked it not to do again. Every session is Session 1 - unless you build the structure it reads from.

This repo is that structure. Two files per project, one habit, and sessions start compounding instead of resetting.

---

## What's in here

| File | What it does |
|------|-------------|
| `CLAUDE-memory-template.md` | Template to append to your project's `CLAUDE.md` - not a replacement |
| `current.md` | Live work layer - active threads, decisions made, what's next |
| `context-index.md` | Index of all context files - tells Claude where to find everything |
| `memory/MEMORY.md` | Always-loaded memory index - one line per entry, links to individual files |
| `memory/` | Typed memory files (user, feedback, project, reference) |
| `how-this-works.md` | Full guide - the problem, the fix, the discipline |
| `savestate-skill/SKILL.md` | Drop-in skill for Claude Code - triggers on `/savestate` |

---

## Zero-effort setup

Don't want to wire this up manually? Just give Claude this repo and let it do the work:

> *"Read this repo and set up persistent memory for my project: https://github.com/malbers/claude-memory-context"*

Claude will read the templates, create the file structure in your project, and configure everything. You just answer a few questions about your project and you're running.

---

## Quick start (manual)

If you prefer to set it up yourself:

1. Open `CLAUDE-memory-template.md`, copy the sections you need, and append them to your project's existing `CLAUDE.md`. Don't replace your CLAUDE.md - add to it. If you don't have a CLAUDE.md yet, rename the template to CLAUDE.md and fill in the placeholders.
2. Copy `current.md` into the same folder
3. Copy the `memory/` directory into the same folder
4. Open Claude Code from that directory - it loads `CLAUDE.md` automatically
5. When you go deep on something, say: *"Write that to current.md."*

That's it. After a few sessions you'll feel the difference.

---

## How to start every session

Tell Claude what you're walking into before you ask anything else:

> *"I'm working on project X, with person Y, on topic Z."*

One sentence. Claude loads the right files, pulls the right context, and you skip the re-explanation tax. The more specific you are upfront, the faster the session gets useful. This is the single highest-leverage habit in the whole system.

Even better: add one line to your CLAUDE.md telling Claude to ask at session start:

```
At the start of every session, ask: "Which project are we working on today?"
```

This flips the burden. You don't have to remember to set context. Claude asks.

---

## The one habit that matters

The files only work if you keep them current. You don't have to do it manually - just tell Claude:

- *"Write that decision to current.md."*
- *"Save state before we go further."*
- *"/savestate"* (if you've installed the skill)

Sessions where you do this compound. Sessions where you don't, you pay for later.

---

## Savestate

Claude has a context window. Go deep enough in a single session and it starts compressing older context - decisions you made an hour ago get summarized into mush.

The fix: say `/savestate` anytime. Claude scans the conversation, extracts what matters, and writes a dated entry to `current.md`. Install the skill in `savestate-skill/` for the full trigger behavior.

---

## Keeping files lean: topic files

When a session goes deep on something specific, don't let it bloat `current.md`. Pull it out:

> *"This thread is good - write it to a new file: cooltopic.md"*

Next session, just ask Claude to load it:

> *"Load cooltopic.md - we're picking up that thread."*

`current.md` keeps a pointer:

```
-> See cooltopic.md for full context
```

That's the whole working model: two permanent files for structure, unlimited topic files for depth, `current.md` as the index that ties them together. Nothing bloats. Everything's findable.

---

## The memory system

Topic files hold project threads. But there's a second category of knowledge that matters across sessions: who you are, how you like to work, mistakes Claude should not repeat, and where things live outside the codebase.

That's what the memory system handles.

### Structure

```
my-project/
  CLAUDE.md
  current.md
  memory/
    MEMORY.md              <- always loaded, index of all memory files
    user_profile.md
    feedback_no_emojis.md
    project_api_migration.md
    reference_deploy_steps.md
```

`memory/MEMORY.md` is the index. Claude Code auto-loads it every session because it lives in the project tree. Keep it under 200 lines. Each entry is one line, under 150 characters, linking to the actual file:

```
- [user_profile.md](memory/user_profile.md) - Role, background, communication preferences
- [feedback_no_emojis.md](memory/feedback_no_emojis.md) - No emojis in files or output unless asked
- [project_api_migration.md](memory/project_api_migration.md) - v2 API migration, deadline June 15
- [reference_deploy_steps.md](memory/reference_deploy_steps.md) - Deploy checklist and staging URL
```

### Memory types

There are four types. The filename prefix tells Claude what kind of knowledge it is.

**user** - Who you are, your role, preferences, expertise. Helps Claude tailor tone and depth.

**feedback** - Corrections AND confirmations. "Don't use em dashes" and "Yes, keep doing the structured summaries." This is the most important type. It prevents Claude from repeating mistakes and reinforces what works.

**project** - Ongoing work context, goals, deadlines. When you write these, convert relative dates ("next Tuesday") to absolute dates ("2026-04-15").

**reference** - Pointers to external systems: where bugs are tracked, what dashboard to check, how deploys work. Things Claude can't find in the codebase.

### Example memory file

```markdown
---
name: feedback_no_emojis
description: Don't add emojis to files or output
type: feedback
---

Don't add emojis to files, commit messages, or chat output unless explicitly asked. This applies to all projects.

**Why:** Emoji-heavy output feels unserious and clutters diffs.
**How to apply:** Check all file writes and chat output. Only add emojis when the user explicitly requests them.
```

### When to write memory files

Claude writes these when it learns something worth remembering across sessions. The key test: is this derivable from the code, git history, or existing docs? If yes, don't write a memory file. If no, write one.

Good memory candidates:
- You corrected Claude's approach and want it to stick
- You explained a preference that applies to all future sessions
- There's project context that lives in your head, not in the code
- An external system or process has steps Claude needs to follow

Bad candidates:
- Architecture decisions already in the codebase
- Things documented in existing READMEs or wikis
- One-time instructions that won't recur

---

## The next unlock: sub-agents

Once the memory structure is running, the next thing to wire in is sub-agents.

The pattern: your main Claude context is for thinking and decisions. Sub-agents handle execution - file edits, searches, long writing tasks, research pulls. This keeps your main context clean and focused, and lets Claude parallelize independent work.

Add this to your `CLAUDE.md` under Capture Discipline:

```
Delegate all file edits, searches, and long writing or synthesis tasks to sub-agents.
Main context is for thinking and decisions only. Never do in the main thread what
a sub-agent can do cleanly in parallel.
```

Once it's in your instructions, Claude will route execution work out automatically. Your main thread stays sharp. Sessions that used to bog down in file writes and long reads stop feeling slow.

This pairs naturally with the memory structure: sub-agents write to `current.md` and project files while the main context moves forward. Nothing lost, nothing blocking.

---

## When current.md gets too big

Sessions compound. That's the point. But it creates a second problem: `current.md` grows without bound. When it hits ~10,000 tokens, Claude can't read the whole thing. Older threads become invisible. You're losing context again - the exact problem you built this to solve.

The fix is the **context-index pattern**. Three parts:

1. `current.md` stays active-only - open threads only, always fully readable
2. Closed threads move to domain files (`files/people/`, `files/decisions/`, etc.) at full fidelity
3. `context-index.md` is a one-page index listing every domain file with a one-liner - Claude loads it at session start and knows where everything lives

The key rule: **never compress**. When you route a closed thread to a domain file, write it at full fidelity. Compression defeats the purpose - you're solving context loss, not creating a summarized version of it.

See `how-this-works.md` for the full pattern and wiring instructions.

---

## Directory structure

```
my-projects/
  project-a/
    CLAUDE.md          <- always loaded
    current.md         <- active threads only
    context-index.md   <- index of all domain files
    memory/
      MEMORY.md        <- always-loaded memory index
    files/
      people/          <- one file per person (high-leverage pattern)
      decisions/       <- key architectural choices
      research/        <- synthesized source material
  project-b/
    CLAUDE.md
    current.md
    context-index.md
    memory/
      MEMORY.md
  shared-current.md         <- cross-project threads (optional)
  shared-context-index.md   <- cross-project index (optional)
```

One project per folder. Claude Code reads the `CLAUDE.md` in whatever directory you open it from.

The `files/people/` directory deserves a callout: one file per person you work with regularly. Name, role, last interaction, what you discussed, what they care about. Claude loads the right person file when you say "I'm meeting with Sarah tomorrow" and the whole history is right there. It's one of the highest-leverage patterns in the system, especially if you work across multiple relationships.

---

## Recent changes

**April 2026** - Memory system: persistent memory files with typed entries (user, feedback, project, reference). MEMORY.md as the always-loaded index. Claude learns your preferences, corrections, and project context across sessions, so you stop re-explaining yourself.

**April 2026** - Session startup discipline: tell Claude to ask "Which project are we working on today?" at the start of every session. One line in CLAUDE.md, biggest single habit change.

**April 2026** - PreCompact hook: automated savestate fires when the context window starts compressing. No more losing work mid-session.

**April 2026** - Context alerts: Claude proactively flags when context usage hits ~65-70%. You get a heads-up before things start getting lost.

**April 2026** - Context-index system: solves the current.md growth problem. When the file hits token limits, context becomes invisible. The fix: active threads in current.md, closed threads in domain files, context-index.md as the index Claude loads every session.

**April 2026** - Savestate skill updated: now routes closed threads to domain files and updates the index, keeping current.md lean automatically.

---

For a trust and privacy framework that governs what your AI can access and when, see [progressive-trust](https://github.com/malbers/progressive-trust).

Built by [Michael Albers](https://github.com/malbers).
