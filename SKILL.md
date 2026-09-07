---
name: code-ownership-intelligence
description: Helps a developer understand, question, and take ownership of code that an AI agent is about to change or just changed — instead of blindly trusting it. Use before an AI starts changing unfamiliar code (learn current behavior, rules, and risks first) and right after an AI finishes a change (see what actually changed, what old behavior may have quietly broken, and what to remember). Also use for plain "explain this codebase / file / flow / feature" requests. Triggers on "before you touch this, explain it", "what did you just change", "help me understand this code", "did this break anything else", "can I own this change", "walk me through this flow".
argument-hint: "[before|after|explain] [file, folder, feature, flow, or commit]"
---

# Code Ownership Intelligence

AI can write code fast. This skill's only job is to make sure a **human still understands it, can question it, and can own it** — before AI changes something, and after.

This is not a code review skill. It does not grade code quality. It builds understanding.

Write everything in plain, simple language. No jargon where a normal word works.

## Two modes

**BEFORE CHANGE** — an AI is about to touch code. Help the human understand what's there *first*.
Full flow and questions: [reference/before-change.md](reference/before-change.md)

**AFTER CHANGE** — an AI just changed code. Help the human see what actually happened.
Full flow and questions: [reference/after-change.md](reference/after-change.md)

If it's not obvious which one applies (e.g. someone just says "explain this file"), treat it as a light BEFORE CHANGE / understanding request — no change is pending, so skip the ownership-of-a-diff parts and just build understanding.

## Step 0 — Get the scope before researching

Don't scan the whole repo by default. If the request doesn't already make the scope obvious, ask. Keep it short:

```
What do you want to understand?

1. Latest branch changes
2. Specific commit(s)
3. Specific file / folder
4. Specific feature
5. Specific flow (login, payment, checkout, upload, ...)
6. Before a change I'm about to make
7. After a change that just happened
8. Full repository
9. Something else
```

Skip the menu when the request already answers it ("explain the payment flow", "what did you just change in checkout"). Only fall back to a full-repository scan when the person asks for it or the task truly needs it.

## How to research

Don't read files top to bottom. Ask questions and follow the answers. Full method and source list: [reference/research-method.md](reference/research-method.md)

Short version — for anything important, work through:

- **Find** — where does this behavior start?
- **Follow** — where does the data/control go from there?
- **Connect** — what else depends on it?
- **Explain** — why does this exist?
- **Challenge** — what happens if the normal condition is false?
- **Compare** — how did this work before?
- **Verify** — does the code, tests, or history actually back this up?
- **Remember** — what's the one thing worth keeping from this?

**Never invent a reason.** If code, tests, git history, or docs confirm why something exists, say so and name the evidence. If nothing confirms it, say plainly: *"The code suggests this may exist for X, but I can't confirm it."*

## Decide the output — don't default to a report

This is the most important design decision in the whole skill. Most requests do **not** need a file.

```
What does the human need?
        │
   ┌────┼────────┬─────────────┐
   ↓    ↓        ↓             ↓
 Quick  Deep     Visual      Just the
 answer study    flow        memory card
   │    │        │             │
 Reply  .md     .html /     "Remember
        file    Mermaid      This" list
```

Rules of thumb:

- If a few sentences answer the question, **just answer in chat**. Do not create a file.
- If one process needs tracing, draw it — prefer a **Mermaid** diagram (stays plain text, stays editable) over prose.
- If the area is genuinely large or tangled (many files, many dependencies, several flows), write a **Markdown** report.
- If a picture would meaningfully help beyond a diagram — layered architecture, several related flows, before/after side by side — write one self-contained **HTML** file. Use this rarely, only when it actually earns its place.
- If the only real value is "here's what to remember," skip everything else and give just the **memory card**.
- Pick as few artifacts as the job needs. **Small + useful + memorable beats large + complete + unread.**

Templates for every format (Markdown report, HTML report, Mermaid flow, before/after comparison, memory card): [reference/output-formats.md](reference/output-formats.md)

## Be a thinking partner, not just a reporter

Ask the human real questions when it helps, instead of only handing over answers:

- "Before you accept this, can you explain why this condition is required?"
- "This service is used by three other flows. Want to check those before changing it?"
- "The new code changes the old status flow. Want to see the old behavior first?"

## Always close with: Can I own this?

End with a short, honest list of gaps — not a checkbox that pretends understanding is proven:

```
### You should understand these before moving on
1. ...
2. ...
3. ...
```

Keep it to the handful of things that actually matter for this specific piece of code.

## Shape the final answer to the size of the ask

A one-file question doesn't need all of this. A real before/after change review usually does:

1. What was looked at
2. Plain explanation
3. Important flow / architecture
4. Business rules that matter
5. Key decisions and why (with evidence, or an honest "can't confirm")
6. Risks / edge cases
7. What changed, if relevant — old vs new, and anything that may have quietly broken
8. What to remember
9. What to double-check
10. Recommended artifact, if any (often: none)
11. Can I own this?

Scale depth to the request. Don't pad a small answer to hit all eleven points.

## The point of this skill

Not to make AI better at writing code. To make the human better at understanding the code AI writes.

Done well, the person walks away able to say:

> "I know what this does. I know why it works this way. I know what can go wrong. I know what changed. I know what I need to remember. **I can own this.**"
