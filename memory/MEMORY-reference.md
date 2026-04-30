# Reference Memory (Tier 3)

On-demand memory. Loaded only when a specific tool, topic, or problem comes up. **Not** auto-loaded at session start.

---

## When to put a memory here

A memory belongs in Tier 3 when:
- It applies to a narrow tool or topic
- You'd only want to load it when working with that specific thing
- "Here's how I work with tool X" - only relevant when working with X

Rare-but-useful references that don't apply on most sessions live here. Tool-specific quirks, infrastructure pointers, deploy steps, dashboard URLs.

If you find yourself loading the same Tier 3 memory across most topics, promote it to Tier 1.

---

## How to wire this in

Tier 3 stays unloaded until you ask for it. Trigger pattern:

> *"Load MEMORY-reference.md and find the deploy steps."*

Or, if your project has a hook for it:

> *"Pull the X reference from Tier 3."*

You can also add a line to `CLAUDE.md` or your skill files that conditionally loads this file when a specific keyword comes up - but the manual trigger works fine and keeps the token cost where it should be: only when you need it.

---

## Format

Same as the other memory files - one line per entry, under 150 characters, linking to a memory file in `memory/`.

```
- [reference_deploy_steps.md](memory/reference_deploy_steps.md) - Deploy checklist, staging URL, rollback command
- [reference_dashboard_links.md](memory/reference_dashboard_links.md) - Where the latency, error rate, and conversion dashboards live
- [reference_tool_x_quirks.md](memory/reference_tool_x_quirks.md) - Tool X has a 60s timeout on auth; retry pattern documented
```

---

## Tools
- (no entries yet)

## Infrastructure
- (no entries yet)

## External systems
- (no entries yet)

## Quirks and workarounds
- (no entries yet)
