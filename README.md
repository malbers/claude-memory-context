# claude-memory-context

A lightweight file structure that gives Claude Code persistent memory across sessions.

Claude resets every session. No memory of what you built yesterday, what you decided last week, what you asked it not to do again. Every session is Session 1 — unless you build the structure it reads from.

This repo is that structure. Two files per project, one habit, and sessions start compounding instead of resetting.

---

## What's in here

| File | What it does |
|------|-------------|
| `CLAUDE.md` | Project brief — Claude reads this automatically at the start of every session |
| `current.md` | Live work layer — active threads, decisions made, what's next |
| `context-index.md` | Index of all context files — tells Claude where to find everything |
| `how-this-works.md` | Full guide — the problem, the fix, the discipline |
| `savestate-skill/SKILL.md` | Drop-in skill for Claude Code — triggers on `/savestate` |

---

## Quick start

1. Copy `CLAUDE.md` into your project folder and fill in the placeholders
2. Copy `current.md` into the same folder
3. Open Claude Code from that directory — it loads `CLAUDE.md` automatically
4. When you go deep on something, say: *"Write that to current.md."*

That's it. After a few sessions you'll feel the difference.

---

## How to start every session

Tell Claude what you're walking into before you ask anything else:

> *"I'm working on project X, with person Y, on topic Z."*

One sentence. Claude loads the right files, pulls the right context, and you skip the re-explanation tax. The more specific you are upfront, the faster the session gets useful. This is the single highest-leverage habit in the whole system.

---

## The one habit that matters

The files only work if you keep them current. You don't have to do it manually — just tell Claude:

- *"Write that decision to current.md."*
- *"Save state before we go further."*
- *"/savestate"* (if you've installed the skill)

Sessions where you do this compound. Sessions where you don't, you pay for later.

---

## Savestate

Claude has a context window. Go deep enough in a single session and it starts compressing older context — decisions you made an hour ago get summarized into mush.

The fix: say `/savestate` anytime. Claude scans the conversation, extracts what matters, and writes a dated entry to `current.md`. Install the skill in `savestate-skill/` for the full trigger behavior.

---

## Keeping files lean: topic files

When a session goes deep on something specific, don't let it bloat `current.md`. Pull it out:

> *"This thread is good — write it to a new file: cooltopic.md"*

Next session, just ask Claude to load it:

> *"Load cooltopic.md — we're picking up that thread."*

`current.md` keeps a pointer:

```
→ See cooltopic.md for full context
```

That's the whole working model: two permanent files for structure, unlimited topic files for depth, `current.md` as the index that ties them together. Nothing bloats. Everything's findable.

---

## The next unlock: sub-agents

Once the memory structure is running, the next thing to wire in is sub-agents.

The pattern: your main Claude context is for thinking and decisions. Sub-agents handle execution — file edits, searches, long writing tasks, research pulls. This keeps your main context clean and focused, and lets Claude parallelize independent work.

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

Sessions compound. That's the point. But it creates a second problem: `current.md` grows without bound. When it hits ~10,000 tokens, Claude can't read the whole thing. Older threads become invisible. You're losing context again — the exact problem you built this to solve.

The fix is the **context-index pattern**. Three parts:

1. `current.md` stays active-only — open threads only, always fully readable
2. Closed threads move to domain files (`files/people/`, `files/decisions/`, etc.) at full fidelity
3. `context-index.md` is a one-page index listing every domain file with a one-liner — Claude loads it at session start and knows where everything lives

The key rule: **never compress**. When you route a closed thread to a domain file, write it at full fidelity. Compression defeats the purpose — you're solving context loss, not creating a summarized version of it.

See `how-this-works.md` for the full pattern and wiring instructions.

---

## Directory structure

```
my-projects/
  project-a/
    CLAUDE.md          ← always loaded
    current.md         ← active threads only
    context-index.md   ← index of all domain files
    files/
      people/          ← one file per person
      decisions/       ← key architectural choices
      research/        ← synthesized source material
  project-b/
    CLAUDE.md
    current.md
    context-index.md
  shared-current.md         ← cross-project threads (optional)
  shared-context-index.md   ← cross-project index (optional)
```

One project per folder. Claude Code reads the `CLAUDE.md` in whatever directory you open it from.

---

## Recent changes

**April 2026** — Context-index system: solves the current.md growth problem. When the file hits token limits, context becomes invisible. The fix: active threads in current.md, closed threads in domain files, context-index.md as the index Claude loads every session.

**April 2026** — Savestate skill updated: now routes closed threads to domain files and updates the index, keeping current.md lean automatically.

---

Built by [Michael Albers](https://github.com/malbers). Related: [progressive-trust](https://github.com/malbers/progressive-trust).
