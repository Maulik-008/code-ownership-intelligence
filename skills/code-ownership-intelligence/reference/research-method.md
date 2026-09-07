# Research method

Don't read a file top to bottom hoping understanding shows up. Ask questions, then go find the answer. This is the repeatable method.

## The eight steps

Work through these for anything that matters. Skip ones that clearly don't apply — this is a checklist to think with, not a form to fill out.

### Find
Where does this behavior actually start? The entry point — a route, a button handler, a queue consumer, a scheduled job, a CLI command.

### Follow
Once it starts, where does the data (or control) go? Trace it forward:

```
Entry point → Function → Service → Business logic → Database / API → Side effect → Response
```

Don't stop at the first function.

### Connect
What else depends on this? Who calls this function, imports this module, reads this table, listens for this event? A change here can break something that looks unrelated.

### Explain
First: what does this do? Then, separately: why does it exist? Look for the business reason, not just the mechanism.

### Challenge
What happens if the normal condition is false? Missing data, empty values, invalid input, permission failure, network or database failure, a duplicate request, an unexpected status, a zero or negative value, a partial success, a retry, a timeout, stale data. This is where real bugs and real risks live. (See [understanding-tools.md](understanding-tools.md) for the fuller normal/failure/edge/unexpected pattern.)

### Compare
How did this work *before*? Relevant any time there's a change to review — a diff, a commit, a PR. Old behavior is the baseline everything else is measured against.

### Verify
Can you actually confirm what you think is true? Check the code, the tests, the git history, the docs — don't rely on a guess that sounds reasonable.

### Remember
Strip away everything else — what's the one thing a developer actually needs to keep in their head about this? If you can't answer this, you haven't finished the research.

## Decision memory

AI-written code can work perfectly while the human has no idea why it's shaped the way it is. For a decision whose reasoning actually matters — not every line, just the ones a future change could get wrong — recover it in this shape:

```
Decision        → what was actually built
Why             → the reason it was built this way
Evidence        → where that reason is confirmed (or "unknown")
Alternatives    → what else could have been done instead
Trade-off       → why this option won over the others
What to remember → the one thing that matters going forward
```

Fill it in by asking, in order: *What was chosen? What else could have been chosen? Why was this one preferred? What problem was it trying to avoid? Who depends on it today? What breaks if it changes?* Stop as soon as you have enough to answer "what to remember" — this is not a form to fill exhaustively.

Look for the evidence in: older implementations of the same thing, git history and commit messages, tests (what the author thought was worth protecting), comments, related or similar features elsewhere in the codebase, old conditions that look oddly specific, previous bug fixes touching this area, and existing patterns the codebase already follows. A strange, specific-looking condition is very often a scar from a past incident rather than an arbitrary choice — worth checking history for before assuming either way.

If the reasoning genuinely can't be found after looking, say so — don't fill the gap with a guess. The goal isn't historical curiosity; it's recovering reasoning the human never got to build themselves because AI wrote the code.

## Where to look (evidence sources)

Use whichever of these actually matter for the question at hand. Don't inspect everything blindly — let the questions above tell you where to look.

- Source code (the real behavior, always wins over assumptions)
- Tests
- Types / interfaces
- API definitions
- Database schema
- Configuration and environment variable usage
- Documentation (may be stale — treat with caution)
- Git history, commit messages, and diffs
- Related implementations elsewhere in the codebase
- Callers and dependents
- Error handling
- Logs, if available

## Say how sure you are

Never present a guess as a fact. Label what you find as one of three things:

- **Confirmed** — the code, tests, or history clearly show it.
- **Likely** — the code strongly suggests it, but nothing directly confirms it.
- **Unknown** — there isn't enough evidence either way.

When you can back a "why" up with evidence, name exactly where it came from (a test name, a commit message, a comment, a doc). When you can't, say so plainly:

> "The code suggests this may exist for X, but I can't confirm it."

A wrong confident answer is worse than an honest "unconfirmed" — it teaches the human something false about code they're about to own.

The goal is not for the human to know everything. It's for the human to know: **what I know, what I don't know, why I don't know it, and where I'd look if I needed the answer.** When a real question stays open after looking, say so plainly instead of quietly dropping it:

```
## Still Unknown

- Why this limit is set to 3 isn't documented anywhere.
- Git history shows when this check was added, but not why.
- Whether this is intentional needs business context we don't have.
```

Unknown is a fine answer. False confidence is not.
