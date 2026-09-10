# Remember and review

The "Remember This" line at the end of a response only helps if it survives past the moment it was said. This is the spaced-review layer that makes it durable — backed by real memory-science findings, not a novel idea:

- **Spaced repetition** (Ebbinghaus forgetting curve; SM-2, the algorithm behind Anki/SuperMemo): review just before something would be forgotten, growing the interval each time it's recalled successfully.
- **Retrieval practice / the testing effect** (Roediger & Karpicke, 2006): actively recalling something produces stronger, more durable learning than being re-told it — even with no feedback given.
- **Interleaving** (Rohrer & Taylor, 2007): mixing review items from unrelated areas, rather than blocking by topic, improves the ability to tell similar-looking patterns apart.

## How it works in this skill

[memory-agent](../../../agents/memory-agent.md) owns this end-to-end. Two moments matter:

**Filing.** After any review that produces a genuine "one thing to remember" (not a restated fact), that single sentence gets written to `.code-ownership/memory.md` in the project, with a review date starting at tomorrow. Don't file more than one or two items per review — this mirrors the skill's own "five things, not fifty" instinct, just applied over time instead of within one response.

**Surfacing.** At the start of a session, or when explicitly asked ("what should I review today," "quiz me on \<feature\>"), memory-agent checks what's due and asks — as a question, not a restatement:

> Not: "Remember: payment status only moves forward."
> Instead: "Quick one — why can payment status only move forward?"

Wait for a real answer before confirming or correcting. A shrug or "yeah I know" doesn't count — if the human can't say it back, the gap hasn't actually closed, the same principle the ownership check in [human-ownership.md](human-ownership.md) already applies to a fresh explanation.

## Rules for this layer

- **Never more than 2-3 due items at once.** If more are due, surface only the most overdue — this must never feel like a chore or it stops happening.
- **Mix items from different areas** when more than one is due, rather than reviewing one feature's items as a block — this is what makes the review actually test discrimination, not just repetition.
- **An item that's been recalled easily several times in a row stops being surfaced.** The point is retention, not an ever-growing quiz backlog.
- **This is additive, never blocking.** A developer who ignores every review prompt should still get their actual answer to whatever they asked — memory review is a light touch at the edges, not a gate.
- **The file is human-readable on purpose.** `.code-ownership/memory.md` should make sense if a developer opens it directly, without needing this skill to interpret it.

Full field-level format and the interval-adjustment rule: [../../../agents/memory-agent.md](../../../agents/memory-agent.md).
