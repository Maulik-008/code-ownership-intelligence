# Research method

Don't read a file top to bottom hoping understanding shows up. Ask questions, then go find the answer. This is the repeatable method.

## The eight questions

Work through these for anything that matters. Skip ones that clearly don't apply — this is a checklist to think with, not a form to fill out.

### Find
Where does this behavior actually start? The entry point — a route, a button handler, a queue consumer, a scheduled job.

### Follow
Once it starts, where does the data (or control) go? Trace it forward: function → function → service → database → response. Don't stop at the first hop.

### Connect
What else depends on this? Who calls this function, imports this module, reads this table, listens for this event? A change here can break something that looks unrelated.

### Explain
Why does this logic exist? Not just what it does — why it does it this way. Look for the business reason, not just the mechanism.

### Challenge
What happens if the normal condition is false? What if the list is empty, the user is missing a field, the network call fails, the amount is zero or negative? This is where real bugs and real risks live.

### Compare
How did this work *before*? Relevant any time there's a change to review — a diff, a commit, a PR. Old behavior is the baseline everything else is measured against.

### Verify
Can you actually confirm what you think is true? Check the code, the tests, the git history, the docs — don't rely on a guess that sounds reasonable.

### Remember
Strip away everything else — what's the one thing a developer actually needs to keep in their head about this? If you can't answer this, you haven't finished the research.

## Where to look (research sources)

Use whichever of these actually matter for the question at hand. Don't inspect everything blindly — follow the questions above and let them tell you where to look.

- Source code (the real behavior, always wins over assumptions)
- Tests (what the author believed was worth protecting)
- Types / interfaces (the shape of the contract)
- API definitions (what the outside world can rely on)
- Database schema (what's actually stored, and what's required vs optional)
- Configuration and environment variable usage (what changes behavior per environment)
- Documentation (what someone intended to communicate — may be stale, treat with caution)
- Git history, commit messages, and diffs (why something was done, when it changed, who touched it last)
- Related implementations elsewhere in the codebase (is this pattern used consistently or is this one different?)
- Callers and dependents (who relies on this)
- Error handling (what the author expected could go wrong)
- Logs, if available (what actually happens at runtime)

## Why decisions exist

For any piece of code that matters, try to answer this chain:

```
What?
 ↓
Why?
 ↓
Who depends on it?
 ↓
What happens if it changes?
 ↓
What should I remember?
```

If you can back the "why" up with evidence — a comment, a test name, a commit message, a doc — say exactly where it came from. This makes the explanation checkable instead of just plausible.

If you cannot confirm the reason, say so plainly:

> "The code suggests this may exist for X, but the reason cannot be confirmed."

**Never invent a reason and present it as fact.** A wrong confident answer is worse than an honest "unconfirmed" — it teaches the human something false about code they're about to own.
