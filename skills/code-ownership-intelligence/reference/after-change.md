# After change

Key question: **"Do I actually understand and own what AI just changed?"** Not "did the tests pass."

## Flow

```
AI changes code
      ↓
Find what changed
      ↓
Understand the new code
      ↓
Compare old vs new behavior
      ↓
Trace affected flows
      ↓
Check business rules
      ↓
Check new assumptions
      ↓
Check normal + failure + edge scenarios
      ↓
Find side effects
      ↓
Find accidental behavior changes
      ↓
Understand why important decisions were made
      ↓
Human understanding check
      ↓
Remember important things
      ↓
Can the human own this change?
```

Find the changed files from whatever scope was chosen (current diff, a specific commit, a branch — see the scope menu in [SKILL.md](../SKILL.md)). Then use [research-method.md](research-method.md) for the digging, especially **Compare** (how did this work before) and **Challenge** (what if the assumption behind the change is wrong). For anything central to the change, apply [understanding-tools.md](understanding-tools.md) — especially **what would I break** for shared code, and **normal/failure/edge/unexpected** for flows that matter.

## Questions to answer

- What changed?
- Why was it changed?
- How does the new code work?
- What old behavior stays the same?
- What old behavior changed?
- Did the change affect anything unrelated?
- What new assumptions did it introduce?
- What edge cases should the human understand?
- What should the developer remember?

## Before vs after protection (the core of this mode)

For every meaningfully changed piece of logic, run this check:

```
Old behavior
     ↓
New behavior
     ↓
Intended difference?
     ↓
YES → explain why, and move on
NO / UNKNOWN → investigate before moving on
```

Specifically look for accidental changes to:

- Conditions (an `if` that got looser, tighter, or flipped)
- Business rules
- Validation
- Permissions / access checks
- Data flow (what gets read, written, or passed where)
- API behavior (request/response shape, status codes, error format)
- Error handling (what used to be caught that isn't anymore, or vice versa)
- State changes (what gets persisted, and when)
- Side effects (emails sent, events published, logs written, jobs queued)
- Existing workflows that weren't the target of the change
- Shared code (a fix or tweak inside a shared helper/module touches everyone who calls it)
- Related features (features that sit next to the one being changed, not obviously connected on the surface)

The one question that matters most:

> **"The new feature works, but did something old quietly stop working or change meaning?"**

This is the failure mode AI-written changes create most often: the requested thing works, and something else silently breaks or silently changes meaning. Actively hunt for it — don't just confirm the new code does what it says.

## Sizing the response

A small, well-scoped change (one function, one clear intent) usually just needs a short answer plus the ownership check — no file needed.

A change that touches several files, alters a flow, or changes behavior in a way that isn't obviously intentional deserves a real comparison. Use the BEFORE / CHANGE / AFTER / IMPACT template and, if it's genuinely complex, a Markdown or HTML report. See [output-formats.md](output-formats.md).

## Close with ownership, not just a summary

```
### You should understand these before moving on
1. What changed and why
2. Anything old that may now behave differently
3. What to double-check before trusting this in production
```

Full ownership-check pattern (including when to ask the human a question instead of just telling them) is in [human-ownership.md](human-ownership.md).

If something is genuinely unclear or risky, say so directly and suggest the human verify it — don't smooth it over because the tests pass.
