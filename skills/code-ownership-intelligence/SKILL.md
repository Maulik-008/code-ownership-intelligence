---
name: code-ownership-intelligence
description: Helps a developer understand, question, remember, and take ownership of code that an AI agent is about to write or just changed — instead of blindly trusting it. Use before an AI starts changing unfamiliar code (learn current behavior, rules, and risks first) and right after an AI finishes a change (see what actually happened, what old behavior may have quietly broken, and what to remember). Also use for plain "explain this codebase / file / flow / feature" requests, and for building a mental model of an unfamiliar area. Triggers on "before you touch this, explain it", "what did you just change", "help me understand this code", "did this break anything else", "can I own this change", "walk me through this flow", "what will I break if I change this".
argument-hint: "[before|after|explain] [file, folder, feature, flow, or commit]"
---

# Code Ownership Intelligence

AI can write code fast. That does not make the human who asked for it responsible any less — they still have to understand it, question it, debug it, and maintain it later.

**AI is the worker. The human is still the owner.** This skill's only job is to rebuild the understanding a developer used to get for free by writing the code themselves.

> **Understand first. Think second. Change third. Own always.**

## What this is not

- Not a code review tool (it doesn't grade quality or hunt style issues).
- Not a documentation generator (it doesn't describe files for their own sake).
- Not a bug-finder (finding risk is a side effect, not the goal).
- Not a report machine (most requests should end in a chat reply, not a file).

The goal is a human who can honestly say **"I can own this"** — not a longer document.

## Two modes

**BEFORE CHANGE** — an AI is about to touch code. The key question: *"What am I about to let AI change?"*
Full flow and questions: [reference/before-change.md](reference/before-change.md)

**AFTER CHANGE** — an AI just changed code. The key question: *"Do I actually understand and own what AI just changed?"*
Full flow and questions: [reference/after-change.md](reference/after-change.md)

If it's not obvious which one applies (someone just says "explain this file"), treat it as a light BEFORE CHANGE / understanding request — no change is pending, so skip the diff-specific parts and just build understanding.

## Step 0 — Get the scope before researching

Don't scan the whole repo by default. If the request doesn't already make the scope obvious, ask. Keep it short:

```
What do you want to understand?

1. Latest changes on the current branch / uncommitted changes
2. Specific commit(s), or recent commits
3. Specific file or folder
4. Specific feature
5. Specific flow (login, payment, checkout, upload, ...)
6. Before a change I'm about to make
7. After a change that just happened
8. Full repository
9. Something else
```

Skip the menu when the request already answers it ("explain the payment flow", "what did you just change in checkout"). Only fall back to a full-repository scan when the person asks for it or the task truly needs it.

## How to research

Don't read files top to bottom. Ask questions and follow the answers. Full method, evidence sources, and how to talk about certainty: [reference/research-method.md](reference/research-method.md)

Short version — for anything important, work through:

**Find → Follow → Connect → Explain → Challenge → Compare → Verify → Remember**

**Never invent a reason.** Label everything **Confirmed**, **Likely**, or **Unknown** based on real evidence (code, tests, git history, docs). A wrong confident answer is worse than an honest "can't confirm."

## Understanding tools — use for important code only

Not every function needs this. Reach for these when the code is central, shared, risky, or easy to misread: a **mental map** of how the pieces connect, the **rules that must stay true**, what's **easy to miss**, **normal/failure/edge/unexpected** scenarios, and **what would I break** if this changed tomorrow.

Full definitions and examples: [reference/understanding-tools.md](reference/understanding-tools.md)

## Decide the output — don't default to a report

Most requests do **not** need a file. Ask: *"Will a file actually help more than a chat reply?"*

- Small question, one function, small change → **just answer in chat.**
- One process to trace → a **Mermaid** diagram.
- A large or tangled area, or something worth revisiting → a **Markdown** report.
- Genuinely complex, visual structure would clearly help → one self-contained **HTML** file (rare).
- A long-lived area worth remembering across many future sessions → a standing **memory document** (rare — not for one-off tasks).

**Small + useful + memorable beats large + complete + unread.**

Scale of response, file naming, and templates for every format: [reference/output-formats.md](reference/output-formats.md)

## Be a thinking partner, not just a reporter

Ask the human real questions when it helps, instead of only handing over answers — then check what they say and fill the gap:

- "Before you accept this, can you explain why this condition is required?"
- "This service is used by three other flows. Want to check those before changing it?"
- "If the UI changed this status directly instead of the backend, what could go wrong?"

Use this only when it adds real value. Don't turn a small task into a quiz. Full pattern and the ownership check template: [reference/human-ownership.md](reference/human-ownership.md)

## Always close with: Can I own this?

End with a short, honest, specific list of gaps — not a checkbox that pretends understanding is proven:

```
### You should understand these before moving on
1. ...
2. ...
3. ...
```

## Scale to the size of the ask

A one-function question doesn't need every section below. A real before/after review usually does. Don't pad a small answer to hit every point, and don't skip a point a big change actually needs:

```
## What I Looked At
## Simple Understanding
## Mental Map / Main Flow
## Important Rules
## Why These Decisions Exist
## Normal / Failure / Edge Scenarios
## What Could Go Wrong
## What Changed              (if applicable)
## What Is Easy to Miss
## Remember This
## You Should Understand These Before Moving On
## Can I Own This?
```

Leave out empty sections. Never fill a section just to complete the template.

## The bridge this skill builds

```
Human gives direction
        ↓
AI / worker writes the code
        ↓
This skill investigates and explains
        ↓
Human thinks and questions
        ↓
This skill checks understanding
        ↓
Human remembers the important things
        ↓
Human owns the result
```

Never confuse "the AI understands the code" with "the human understands the code." The AI can do the research, trace the flow, compare versions, and explain the result — but the understanding has to end up in the human's head, not just in a file.
