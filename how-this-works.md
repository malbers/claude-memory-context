# Cross-Session Context — How This Works

## The Problem

Every AI session starts fresh. Claude has no memory of what you built yesterday,
what you decided last week, or what you told it not to do again. Every session
is Session 1 — unless you build the structure it reads from.

Most people hit this and blame the tool. The real answer is more interesting:
memory architecture is your job. Once you build it, sessions compound.
Without it, you're always re-explaining yourself.

---

## The Fix: Two Files (Per Project)

**CLAUDE.md** — what Claude knows before you type your first message.
Put this in your project folder. Claude Code loads it automatically every session.
It's your project brief: what you're building, key decisions already made,
how you want Claude to behave.

**current.md** — the live work layer. Active threads, in-progress decisions,
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

This way you don't have to remember to say it — Claude asks first.

---

## The Cross-Project File (Optional but Powerful)

If you're working across multiple projects, some threads don't belong to any
single one — they span both. A shared file handles this.

Create `shared-current.md` at your root level (above all project folders).
Use it for: people you're working with across contexts, cross-project decisions,
threads that touch multiple things at once.

Reference it in each project's CLAUDE.md:

> Also load `../shared-current.md` for cross-project threads.

---

## Savestate — Protecting Against Mid-Session Loss

The session-to-session problem is obvious once you hit it. There's a second
problem most people don't see until it's too late: mid-session context loss.

Claude has a context window — a limit on how much it can hold at once. Go deep
enough in a single session and it starts compacting: older parts of the
conversation get summarized and discarded to make room for new ones. Instructions
you gave earlier? Gone. Decisions you made an hour ago? Summarized into mush.

The fix is **savestate** — a deliberate capture step you run before the window
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

Claude Code supports custom skills — small instruction files that fire on a
trigger phrase. The `savestate` skill in this folder (`savestate-skill/SKILL.md`)
is a drop-in version you can install. It handles the full savestate behavior:
scans the conversation, writes to current.md, and reports back.

To install it:
1. Create `~/.claude/skills/savestate/` on your machine
2. Copy `savestate-skill/SKILL.md` into it as `SKILL.md`
3. Restart Claude Code — the skill is now live
4. Say `/savestate` anytime to trigger it

Claude Code also supports a **PreCompact hook** — a script that fires
automatically when context starts compressing. When wired up, it runs savestate
for you without being asked. That's the fully automated version. Start with
the manual trigger first; add the hook once the habit is solid.

---

## The Discipline: Ask Claude to Write It Down

The files only work if you keep them current. You don't have to do it manually.
Just ask Claude: *"Write that up to current.md."*

Three moments to do it:
1. **After going deep on something** — "We just figured out X, write that to current.md."
2. **When the session gets long** — Claude will flag it: "We've covered a lot, want me to save this?" Say yes.
3. **At the end of a session** — "Summarize what we built today and add it to current.md."

That habit — prompting deliberate capture — is the whole system. Sessions where
you do it compound. Sessions where you don't, you pay for later.

---

## Setup (Claude Code)

1. Create a project folder (e.g. `project-a/`)
2. Copy `CLAUDE.md` into it and fill in the placeholders
3. Copy `current.md` into it
4. Open Claude Code from that folder — it reads CLAUDE.md automatically
5. Say "Hi" — Claude will ask which project you're on, then load current.md

That's it. The first time you go deep on something, ask Claude to write it to
`current.md`. After a few sessions you'll feel the difference.

---

## Building Context Files as You Go

The most important habit after savestate: whenever you go deep on something
specific — a person, a decision, a research thread, a design — ask Claude to
write it to its own `.md` file.

Not everything belongs in `current.md`. Current.md is for active threads.
Specific context deserves its own home so it's easy to find and load later.

**Examples of when to do this:**

- You just had a call with someone new: *"Write up what we know about [name]
  to a people file — files/people/[name].md"*
- You just made a key architectural decision: *"Write that to a decision log —
  files/decisions/[topic].md"*
- You just synthesized a research topic: *"Save this to files/research/[topic].md"*
- You just designed something (like this framework): *"Write this up to
  files/implementations/[name]/"*

**A clean directory structure is load-bearing.** The more specific and organized
your files are, the more precisely Claude can load just what's relevant — instead
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

## What Comes Next (Optional)

Once you have the basics running, you can go deeper:

- **People files** — one `.md` per person you work with regularly. Relationship context,
  what you've discussed, what they're building. Claude reads them when relevant.
- **Decision log** — a file just for architectural choices. When you make a call
  you don't want to re-debate, write it there with the reasoning.
- **Automated capture** — instead of asking Claude to save things, set up a hook
  that fires automatically when context gets long. (Advanced — come back to this.)

But don't over-engineer it upfront. Two files and the capture habit will change
how you work. Start there.

---

This framework is free to use and adapt. Built by [Michael Albers](https://github.com/malbers).
