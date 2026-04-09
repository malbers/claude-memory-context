# Cross-Session Context — How This Works

## The Problem

Every AI session starts fresh. Claude has no memory of what you built yesterday,
what you decided last week, or what you told it not to do again. Every session
is Session 1 - unless you build the structure it reads from.

Most people hit this and blame the tool. The real answer is more interesting:
memory architecture is your job. Once you build it, sessions compound.
Without it, you're always re-explaining yourself.

---

## The Fix: Two Files (Per Project)

**CLAUDE.md** - what Claude knows before you type your first message.
Put this in your project folder. Claude Code loads it automatically every session.
It's your project brief: what you're building, key decisions already made,
how you want Claude to behave.

**current.md** - the live work layer. Active threads, in-progress decisions,
things you figured out that you want the next session to know.
This is what makes sessions build on each other instead of starting over.

---

## Directory Structure

Give each project its own folder. Put both files inside it.

```
my-projects/
  project-a/
    CLAUDE.md        ← project brief, always loaded
    current.md       ← live work threads
  project-b/
    CLAUDE.md
    current.md
  shared-current.md  ← cross-project threads (see below)
```

Claude Code reads the CLAUDE.md in whatever directory you open it from.
One project per folder, one CLAUDE.md per project. Keep them separate.

---

## Telling Claude Which Project You're Working On

This is easy to miss, but important: Claude needs to know which project you're
picking up. The fix is to have Claude ask you at startup.

Add this to your CLAUDE.md under Session Startup:

> At the start of every session, after any greeting, ask:
> "Which project are we working on today?"
> Then load the relevant current.md for that project.

This way you don't have to remember to say it - Claude asks first.

---

## Session Startup Automation

The simplest version is just the line in CLAUDE.md shown above. Claude reads it,
asks you which project, and loads the right files. That covers 90% of what you need.

If you want more, Claude Code supports hooks that fire at session start. You can
wire up a startup hook that enforces the "ask which project" behavior, runs a
checklist, or loads specific files automatically. But start with the CLAUDE.md
line. Add the hook once you feel the need.

One useful startup behavior: Claude can proactively alert you when context usage
hits 65-70%. This tells you when to savestate before compaction eats your work.
Add a line like this to your CLAUDE.md:

> Proactively flag context usage when it hits ~65-70%. Don't wait to be asked.

You'll know when it's time to save and wrap, instead of finding out after context
is already lost.

---

## The Cross-Project File (Optional but Powerful)

If you're working across multiple projects, some threads don't belong to any
single one - they span both. A shared file handles this.

Create `shared-current.md` at your root level (above all project folders).
Use it for: people you're working with across contexts, cross-project decisions,
threads that touch multiple things at once.

Reference it in each project's CLAUDE.md:

> Also load `../shared-current.md` for cross-project threads.

---

## Savestate — Protecting Against Mid-Session Loss

The session-to-session problem is obvious once you hit it. There's a second
problem most people don't see until it's too late: mid-session context loss.

Claude has a context window - a limit on how much it can hold at once. Go deep
enough in a single session and it starts compacting: older parts of the
conversation get summarized and discarded to make room for new ones. Instructions
you gave earlier? Gone. Decisions you made an hour ago? Summarized into mush.

The fix is **savestate** - a deliberate capture step you run before the window
fills. Think of it as hitting Save in a document editor. The session keeps going,
but the important stuff is now written to disk.

**How to trigger it manually:**

Just say it: *"save state"* or *"/savestate"*

Claude will scan the conversation, extract what matters (decisions made, work done,
open items, next steps), and write a dated entry to `current.md`. Then confirm
what was saved.

**How to get Claude to remind you:**

Add this line to your CLAUDE.md under Capture Discipline:

> If we've been in a long session and I haven't saved recently, remind me:
> "We've been at this a while — want me to save state before we go further?"

Claude will catch it before you lose it.

**The automated version (advanced):**

Claude Code supports custom skills - small instruction files that fire on a
trigger phrase. The `savestate` skill in this folder (`savestate-skill/SKILL.md`)
is a drop-in version you can install. It handles the full savestate behavior:
scans the conversation, writes to current.md, and reports back.

To install it:
1. Create `~/.claude/skills/savestate/` on your machine
2. Copy `savestate-skill/SKILL.md` into it as `SKILL.md`
3. Restart Claude Code - the skill is now live
4. Say `/savestate` anytime to trigger it

Claude Code also supports a **PreCompact hook** - a script that fires
automatically when context starts compressing. When wired up, it runs savestate
for you without being asked. That's the fully automated version. Start with
the manual trigger first; add the hook once the habit is solid.

---

## The Discipline: Ask Claude to Write It Down

The files only work if you keep them current. You don't have to do it manually.
Just ask Claude: *"Write that up to current.md."*

Three moments to do it:
1. **After going deep on something** - "We just figured out X, write that to current.md."
2. **When the session gets long** - Claude will flag it: "We've covered a lot, want me to save this?" Say yes.
3. **At the end of a session** - "Summarize what we built today and add it to current.md."

That habit - prompting deliberate capture - is the whole system. Sessions where
you do it compound. Sessions where you don't, you pay for later.

---

## Setup (Claude Code)

1. Create a project folder (e.g. `project-a/`)
2. Copy `CLAUDE.md` into it and fill in the placeholders
3. Copy `current.md` into it
4. Open Claude Code from that folder - it reads CLAUDE.md automatically
5. Say "Hi" - Claude will ask which project you're on, then load current.md

That's it. The first time you go deep on something, ask Claude to write it to
`current.md`. After a few sessions you'll feel the difference.

---

## Building Context Files as You Go

The most important habit after savestate: whenever you go deep on something
specific, a person, a decision, a research thread, a design, ask Claude to
write it to its own `.md` file.

Not everything belongs in `current.md`. Current.md is for active threads.
Specific context deserves its own home so it's easy to find and load later.

**Examples of when to do this:**

- You just had a call with someone new: *"Write up what we know about [name]
  to a people file - files/people/[name].md"*
- You just made a key architectural decision: *"Write that to a decision log -
  files/decisions/[topic].md"*
- You just synthesized a research topic: *"Save this to files/research/[topic].md"*
- You just designed something (like this framework): *"Write this up to
  files/implementations/[name]/"*

**People files deserve special attention.** One file per person you work with
regularly. Relationship context, what you've discussed, what they care about,
next steps. This is high-leverage because Claude can load the right person's
file before a meeting or follow-up and pick up exactly where you left off.
You don't have to remember what you talked about three weeks ago. The file does.

**A clean directory structure is load-bearing.** The more specific and organized
your files are, the more precisely Claude can load just what's relevant, instead
of reading everything and guessing. Think of it as a filing cabinet: the better
labeled, the faster you find what you need.

A simple structure to start with:

```
my-project/
  CLAUDE.md
  current.md
  files/
    people/          ← one file per person
    decisions/       ← key architectural choices
    research/        ← synthesized source material
    implementations/ ← things you built or designed
```

You don't need all of these on day one. Add them as the need arises.
The rule: if you'd want to load it in a future session, give it its own file.

---

## The Memory Layer

The memory layer is what turns Claude from a tool that follows instructions into
one that learns how you work. It lives in a `memory/` directory and has two parts:

- **`MEMORY.md`** - an index file that Claude auto-loads every session. One line
  per memory, under 150 characters, linking to the actual file. Keep it under
  200 lines.
- **Individual memory files** - each has frontmatter (name, description, type)
  and content.

There are four types of memory:

- **user** - your role, expertise, preferences. Helps Claude calibrate. A senior
  engineer gets different answers than a first-time coder.
- **feedback** - corrections AND confirmations. "Stop doing X" and "Yes, that
  approach was right." This is the most important type. It prevents Claude from
  repeating mistakes and from drifting away from approaches you've validated.
  Save from both failure and success.
- **project** - ongoing work context, deadlines, who's doing what. Always convert
  relative dates to absolute ("Thursday" becomes "2026-04-10").
- **reference** - pointers to external systems. "Bugs are tracked in Linear
  project X." "The oncall dashboard is at Y."

**What NOT to save:** code patterns (read the code), git history (use git log),
debugging solutions (the fix is in the code), anything already in CLAUDE.md.

**Example: a feedback memory file**

```markdown
---
name: No em dashes in drafts
description: Never use em dashes in any drafted copy - dead giveaway for AI-written content
type: feedback
---

Replace em dashes with regular dashes, commas, or short sentence breaks.
Applies to all human-facing copy: emails, posts, messages, articles.

**Why:** Em dashes are the most common AI writing tell. User noticed immediately.
**How to apply:** Check all drafted output before presenting. Use parentheticals or commas for the same rhythm.
```

**Example: the matching MEMORY.md entry**

```
- [feedback_no_em_dashes.md](memory/feedback_no_em_dashes.md) - Never use em dashes in drafted copy, AI writing tell
```

One line. Under 150 characters. Links to the file. That's the pattern for every
memory you add.

---

## When current.md Gets Too Big: The Context-Index Pattern

Sessions compound - that's what this system is built for. But compounding sessions create a second problem: `current.md` grows without bound. When it hits token limits (roughly 10,000 tokens), Claude can't read the whole file. Older threads become invisible. Sessions start losing context again - the exact problem you built this to solve.

The fix is a three-part pattern that keeps `current.md` lean while preserving everything at full fidelity.

**The fix: current.md stays active-only**

`current.md` holds open threads only - things with next steps pending. When a thread closes, it leaves. This keeps the file small and always fully readable.

Closed threads move to domain files (`files/people/`, `files/decisions/`, `files/research/`, etc.). Full fidelity, not compressed. The detail lives there; `current.md` just tracks what's open.

**context-index.md: the index Claude loads every session**

Add a single index file to your project: `context-index.md`. It lists every domain file with a one-liner - what it contains and when you'd want to load it. Think of it as a table of contents for everything Claude might need to know.

Wire it into your `CLAUDE.md` so it auto-loads alongside `current.md`:

```
## Key Files
- `current.md` — active threads (load every session)
- `context-index.md` — index of all context files (load every session)
```

At session start, Claude reads the index, knows what exists, and loads specific files only when they're relevant.

**The workflow**

1. Thread opens → goes in `current.md` at full fidelity
2. Thread closes → full context written to appropriate domain file → `context-index.md` updated → thread removed from `current.md`
3. Next session: Claude loads `current.md` + `context-index.md`, knows where everything lives

**Directory structure with context-index**

```
my-project/
  CLAUDE.md
  current.md
  context-index.md
  files/
    people/          ← one file per person
    decisions/       ← key architectural choices
    research/        ← synthesized source material
    implementations/ ← things you built or designed
```

**The critical rule: never compress**

When writing a closed thread to a domain file, write it at full fidelity. Do not summarize. Do not compress. Compression defeats the purpose of the entire system - you're solving context loss, not creating a summarized version of it. The domain file should contain everything you'd want a future session to know.

**For multi-project setups**

If you're working across multiple projects, keep a `shared-context-index.md` at your root level alongside `shared-current.md`. Same pattern - lists cross-project domain files with one-liners. Reference it from each project's `CLAUDE.md`.

---

## What Comes Next (Optional)

Once you have the basics running, you can go deeper:

- **Feedback memories** - start saving corrections and confirmations. This is the
  highest-leverage memory type. Every correction you save is a mistake Claude
  never makes again. Every confirmation locks in an approach that worked.
- **Decision log** - a file just for architectural choices. When you make a call
  you don't want to re-debate, write it there with the reasoning.
- **Trust framework** - govern what your AI can access and when. Start with
  read-only, expand as trust is earned. See
  [progressive-trust](https://github.com/malbers/progressive-trust).
- **Automated capture** - instead of asking Claude to save things, set up a hook
  that fires automatically when context gets long. (Advanced - come back to this.)

But don't over-engineer it upfront. Two files and the capture habit will change
how you work. Start there.

---

This framework is free to use and adapt. Built by [Michael Albers](https://github.com/malbers).
