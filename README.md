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

**First principle: no knowledge loss.** When a savestate closes a thread, the rule is absolute - preserve everything. Discoverability beats elegance. If you're unsure whether a piece of context is worth keeping, keep it. The cost of a slightly longer domain file is nothing compared to the cost of reconstructing lost reasoning weeks later.

**Verify before claiming.** The skill includes a hard rule: any "added X to file Y" line in a savestate summary must be verified against the actual file before the savestate is considered complete. This prevents phantom writes - a class of silent failure where the prose gets written but the file edit never lands. Long sessions produce these, and they stay undetected for days. The skill re-reads or greps every target file before declaring done.

---

## Daily bookends: /SOD and /EOD

Savestate handles mid-session capture. The bigger habit is bracketing each day with a deliberate open and close.

- **`/SOD`** - Start of Day. Parallel fan-out across calendar, open todos, threads in `current.md`, and yesterday's `/EOD` handoff. Synthesizes the result into a focus-anchored brief: today's 5-7 priorities, anything slipping, what's load-bearing this week. Replaces the "what was I doing again" tax.

- **`/EOD`** - End of Day. Closes the day. Captures what shipped, what slipped, what's pending tomorrow, plus any open promises to others that drifted. Writes a dated handoff file that `/SOD` reads first thing the next morning. Also merges the daily branch back to `main` with `--no-ff` so each day shows up as a discrete commit.

`/savestate` protects against mid-session loss. `/SOD` and `/EOD` make days compound. Public skill files coming - the pattern is the part to copy first.

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

### Closing a thread: two modes

When a thread closes, the savestate skill treats it one of two ways:

- **Route-and-remove (default).** The thread's full narrative gets a home in a domain file. `current.md` is cleaned. `context-index.md` is updated to point at the new entry. Memory files are touched if there's a durable rule or fact worth keeping. Four surfaces, one atomic pass, all consistent when done.
- **Remove-only (narrow exception).** Only used when all three hold: the artifact already lives in its permanent home (code, skill file, roadmap), git history fully captures the change with a descriptive commit, and the narrative is reconstructable from the artifact plus git alone. When in doubt, route. Remove-only exists for trivial cleanup threads, not substantive work.

The multi-surface rule matters because a closed thread can touch up to four files - `current.md`, `context-index.md`, a domain file, and a memory file. Inconsistency across surfaces means drift between you and Claude. The skill checks all four before declaring the save complete.

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

## Backup and durability: git as the sync layer

A memory system that doesn't survive machine loss, mid-session crashes, or switching laptops isn't earning its keep. The cheap durability layer is git.

The pattern:

1. The whole project tree lives in a git repo. Make it private if your memory is sensitive, public if it's not - either works.
2. Daily machine-tagged branches: `desktop/2026-04-30`, `laptop/2026-04-30`. Created at start of day, merged to `main` with `--no-ff` at end of day. Each day shows up as a discrete merge commit on `main`.
3. A nightly automated commit/push catches anything not yet captured. On Windows that's a Task Scheduler job running a small `backup.ps1`. On Linux/Mac it's cron.
4. An `audit-log.md` at repo root records non-trivial actions (auto vs. approved-by-user). This is the observability layer - when Claude's behavior drifts, the log tells you why.

What this gets you:

- **Survives anything.** Wipe a drive, switch laptops, restore from backup - pull the repo and Claude reads `CLAUDE.md` + `current.md` + `MEMORY.md` and is back where you left it.
- **Multi-machine and multi-instance.** A primary local instance and a secondary cloud instance (a remote server running scheduled jobs) can both pull from the same repo and write to the same memory files. Conflict resolution is `git pull --rebase` at session start.
- **Auditability.** Every change to memory or `current.md` shows up in `git log`. When something feels off, blame and diff tell you exactly when and why.

Cheap, durable, free. The memory layer becomes as portable as the code itself.

---

## Audit log: what Claude has done on your behalf

Memory captures intent. The audit log captures action.

When Claude does something non-trivial - edits a memory file, sends a message you approved, runs a deploy, modifies a config - append a one-line entry to `audit-log.md` at your repo root. This becomes the running record of what Claude touched and when.

**What to log:**
- Edits to memory files, CLAUDE.md, current.md, or configs
- External actions (emails sent, commits pushed, deploys, API calls on your behalf)
- Approvals you granted (so you can spot patterns of repeated approval)
- Decisions Claude made autonomously (so you can review them later)

**What NOT to log:**
- Every file read, search, or grep
- Routine reads of memory or `current.md` (those happen every session)
- Internal tool use that doesn't change state

**Format:**

```
[2026-04-30 14:32] edited memory/feedback_writing_style.md (auto)
[2026-04-30 14:35] sent reply to client@example.com (approved by user)
[2026-04-30 14:40] pushed commit to main (approved by user)
```

The `auto` vs `approved by user` tag matters. When behavior drifts, scan for auto-actions that should have asked. When you've approved the same action type many times, that's a signal you can probably auto-grant it.

**Wiring it in:**

Add this line to your CLAUDE.md under Capture Discipline:

> Append a one-line entry to `audit-log.md` for every non-trivial action you take on my behalf. Format: `[timestamp] [action] [auto / approved by user]`. Do not log trivial reads or internal tool use.

That's it. Claude reads the instruction every session and starts logging. Review the log periodically; archive when it gets long.

**Why this matters:** When something feels off about Claude's behavior, the audit log tells you what changed. When you onboard a new tool or hook, the log tells you whether it actually fired. When trust is being calibrated between you and the AI, the log is the data.

---

## When MEMORY.md gets long: tier the memory layer

`MEMORY.md` is auto-loaded every session. That's its power - Claude reads it without being asked. It's also the catch: every line in it costs tokens on every session, forever.

A flat `MEMORY.md` is fine for the first 50-100 entries. Past that, every session pays the cost of memory that may not apply to what you're working on. The fix is to tier the memory layer.

**Three tiers:**

- **Tier 1: `MEMORY.md`** - always loaded. Behavioral rules that apply everywhere, identity (who you are, how you work), cross-project preferences. Keep this lean. The rule: if a memory doesn't apply on at least 80% of sessions, it doesn't belong in Tier 1.

- **Tier 2: `MEMORY-projects.md`** - project-gated. Loaded by individual project `CLAUDE.md` files when working in that project. Project-specific memories, ongoing work context, deadlines. A memory about Project A is dead weight when working on Project B - Tier 2 keeps it scoped.

- **Tier 3: `MEMORY-reference.md`** - on-demand. Loaded only when a specific topic comes up. Tool-specific quirks, infrastructure pointers, rare-but-useful references. The pattern: "here's how I work with tool X" is only relevant when working with tool X.

**Wiring it in:**

In your top-level `MEMORY.md`, add a header that makes the tiering explicit so Claude knows what's where:

```markdown
## Tiered Memory System
- **This file (MEMORY.md):** Tier 1 - always loaded. Behavioral rules, identity, cross-project preferences.
- **MEMORY-projects.md:** Tier 2 - loaded by project CLAUDE.md files when relevant.
- **MEMORY-reference.md:** Tier 3 - loaded on-demand when a specific tool or topic comes up.
```

Then in each project's `CLAUDE.md`, add a line referencing Tier 2 so it loads automatically when you open Claude Code in that project:

```
@MEMORY-projects.md
```

Tier 3 stays unloaded until you ask for it: *"Load MEMORY-reference.md and find the deploy steps."*

**The trade-off:** more files to maintain and a slightly higher cognitive load on where things live. Worth it once you have 100+ memories and your session start is paying real token cost for entries that don't apply.

**When to tier:** if `MEMORY.md` hits ~150 lines, start moving project-specific entries to Tier 2. If you load the same reference memory across most topics, leave it in Tier 1. If you load it twice ever, Tier 3.

---

## Recent changes

**April 2026** - Audit log pattern: drop `audit-log.md` at repo root; Claude appends a one-line entry per non-trivial action, tagged `auto` or `approved by user`. Observability for what the AI has done on your behalf, and the data layer for trust calibration.

**April 2026** - Memory tiering: when `MEMORY.md` grows past ~150 lines, split into three tiers (always-loaded, project-gated, on-demand). Keeps session start cost bounded as memory compounds.

**April 2026** - Backup and durability documented: git as the sync layer, daily machine-tagged branches, nightly auto-commit, `audit-log.md` for observability. Same memory loads on any machine that pulls the repo.

**April 2026** - Daily bookends: `/SOD` and `/EOD` skill patterns added. Bracket every day with a start-of-day briefing and end-of-day handoff. `/savestate` protects mid-session; `/SOD` and `/EOD` make days compound.

**April 2026** - Savestate hardening: added the "no knowledge loss" first principle, explicit two-mode thread closure (route-and-remove by default, remove-only as a narrow exception), a multi-surface sync requirement across `current.md` / `context-index.md` / domain files / memory files, and a CRITICAL verify-edits-before-claiming rule that prevents phantom writes - summaries that narrate file edits that never actually landed. The skill now re-reads or greps every target file before declaring a save complete.

**April 2026** - Memory system: persistent memory files with typed entries (user, feedback, project, reference). MEMORY.md as the always-loaded index. Claude learns your preferences, corrections, and project context across sessions, so you stop re-explaining yourself.

**April 2026** - Session startup discipline: tell Claude to ask "Which project are we working on today?" at the start of every session. One line in CLAUDE.md, biggest single habit change.

**April 2026** - PreCompact hook: automated savestate fires when the context window starts compressing. No more losing work mid-session.

**April 2026** - Context alerts: Claude proactively flags when context usage hits ~65-70%. You get a heads-up before things start getting lost.

**April 2026** - Context-index system: solves the current.md growth problem. When the file hits token limits, context becomes invisible. The fix: active threads in current.md, closed threads in domain files, context-index.md as the index Claude loads every session.

**April 2026** - Savestate skill updated: now routes closed threads to domain files and updates the index, keeping current.md lean automatically.

---

For a trust and privacy framework that governs what your AI can access and when, see [progressive-trust](https://github.com/malbers/progressive-trust).

Built by [Michael Albers](https://github.com/malbers).
