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

## Think like the developer who wrote it

For code whose reasoning matters, work through this chain instead of stopping at "what it does":

```
What?
 ↓
How?
 ↓
Why?
 ↓
What other choices were possible?
 ↓
Why was this choice used instead?
 ↓
Who depends on it?
 ↓
What happens if it changes?
 ↓
What must stay true?
 ↓
What should I remember?
```

## Recover lost design thinking

AI-written code can work perfectly while the human has no idea why it's shaped the way it is. Try to recover that context instead of leaving it lost:

- Older implementations of the same thing
- Git history and commit messages
- Tests (what the author thought was worth protecting)
- Comments
- Related or similar features elsewhere in the codebase
- Old conditions that look oddly specific
- Previous bug fixes touching this area
- Existing patterns the codebase already follows

Try to answer: *"Why was this decision made?"* and, where possible, *"What problem was this decision trying to avoid?"* If the answer genuinely can't be found, say so — don't fill the gap with a guess.

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
