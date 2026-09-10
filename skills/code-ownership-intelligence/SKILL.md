---
name: code-ownership-intelligence
description: Helps a developer understand, question, remember, and take ownership of code that an AI agent is about to write or just changed — instead of blindly trusting it. Use before an AI starts changing unfamiliar code (learn current behavior, rules, and risks first) and right after an AI finishes a change (see what actually happened, what old behavior may have quietly broken, and what to remember). Also use for plain "explain this codebase / file / flow / feature" requests, building a mental model of an unfamiliar area, and spaced-review check-ins ("what should I review today", "quiz me on <feature>"). Triggers on "before you touch this, explain it", "what did you just change", "help me understand this code", "did this break anything else", "can I own this change", "walk me through this flow", "what will I break if I change this", "what should I review today", "quiz me on this".
argument-hint: "[before|after|explain] [file, folder, feature, flow, or commit]"
---

# Code Ownership Intelligence

AI can write code fast. That does not make the human who asked for it responsible any less — they still have to understand it, question it, debug it, and maintain it later.

**AI is the worker. The human is still the owner.** This skill's only job is to rebuild the understanding a developer used to get for free by writing the code themselves.

> **Understand first. Think second. Change third. Own always.**

## What this is not

- Not a code review tool (it doesn't grade quality or hunt style issues).
- Not a documentation generator (it doesn't describe files for their own sake).
- Not a bug-finder or test-plan generator (finding risk is a side effect, not the goal).
- Not a report machine (most requests should end in a chat reply, not a file).
- Not a checklist machine (the ownership check finds gaps — it doesn't prove understanding by itself).

The goal is a human who can honestly say **"I can own this"** — not a longer document.

## Two modes

**BEFORE CHANGE** — an AI is about to touch code. The key question: *"What am I about to let AI change?"* Ends with a plain statement of what must **not** change.
Full flow and questions: [reference/before-change.md](reference/before-change.md)

**AFTER CHANGE** — an AI just changed code. The key question: *"Do I actually understand and own what AI just changed?"* Always checks whether AI changed more than was actually asked for.
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

## Explain top-down, not bottom-up

Build the explanation in layers, high-level first — don't open with implementation detail before the human has the shape of the thing:

**Level 1 — What is this?** The problem it solves. **Level 2 — How does it work?** The main pieces and flow. **Level 3 — Why this way?** The rules, decisions, and dependencies. **Level 4 — What can go wrong?** Failure paths, edge cases, blast radius.

This is why the Final Answer Structure below goes in this order — resist starting a response at Level 3 or 4 just because that's where the interesting bug is.

## Decide the output — don't default to a report

Most requests do **not** need a file. Ask: *"Will a file actually help more than a chat reply?"*

- Small question, one function, small change → **just answer in chat.**
- One process to trace → a **Mermaid** diagram.
- A large or tangled area, or something worth revisiting → a **Markdown** report.
- Genuinely complex, visual structure would clearly help → one self-contained **HTML** file (rare).

**Small + useful + memorable beats large + complete + unread.**

Whatever the format, the same delivery test applies: **a tired person skimming for 10 seconds should still walk away knowing the one thing that matters.** Lead with a one-sentence plain answer, let headings carry the takeaway on their own, keep every section to five lines or fewer, use at most one diagram, and close with a single "the one thing to remember" — never a summary that re-reads everything above it.

Scale of response, file naming, and templates for every format: [reference/output-formats.md](reference/output-formats.md)

## Agentic workflow — when to delegate vs. handle inline

This skill can run two ways. Default to the first; only step up to the second when the size of the ask earns it.

**Inline (default):** For a small or medium ask, just do the work yourself, following the sections above. No subagents, no extra overhead — most requests belong here.

**Delegated (large or risky change only):** For the "Large or risky change" tier — money, auth, shared code, a rewrite, or anything the human explicitly wants a fuller review of — delegate to the specialist agents bundled with this plugin, in this order:

```
1. research-agent   → investigates, returns labeled findings (never a human-facing answer)
2. diagram-agent    → given the findings, decides if a diagram earns its place + which type
3. delivery-agent   → compresses findings (+ diagram) into the actual ADHD-friendly final answer
4. memory-agent     → files the "one thing to remember" for spaced review; surfaces anything due
```

Each agent has one job and hands its output to the next — don't let any of them try to do another's job (e.g. research-agent should never write the final prose, delivery-agent should never go re-investigate). You, the orchestrator, decide scope and mode (before/after/explain) up front, run the chain, and are responsible for the final message actually reaching the human — the chain produces the content, you deliver it.

Skip steps that don't apply: no diagram needed → skip diagram-agent and hand findings straight to delivery-agent. Full design rationale, failure modes, and why this order: [reference/agentic-workflow.md](reference/agentic-workflow.md)

memory-agent's job (step 4) runs even for inline, non-delegated responses whenever a real "one thing to remember" was produced, and also on its own when the human asks "what should I review today" or similar. Full mechanics: [reference/remember-and-review.md](reference/remember-and-review.md)

## Be a thinking partner, not just a reporter

Ask the human real questions when it helps, instead of only handing over answers — then check what they say and fill the gap. The strongest version isn't a specific question at all, it's asking them to **explain it back**: "in your own words, how does this flow work?"

- "Before you accept this, can you explain why this condition is required?"
- "This service is used by three other flows. Want to check those before changing it?"
- "If the UI changed this status directly instead of the backend, what could go wrong?"

A "yes" or "makes sense" is not proof they understood it — if they can't say it back in their own words, keep going. Use this only when it adds real value. Don't turn a small task into a quiz. Full pattern and the ownership check template: [reference/human-ownership.md](reference/human-ownership.md)

## Always close with: Can I own this?

End with a short, honest, specific list of gaps — not a checkbox that pretends understanding is proven:

```
### You should understand these before moving on
1. ...
2. ...
3. ...
```

The sharpest version of this test: *"If you had to change this six months from now, without AI, would you know where to start?"*

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
## Still Unknown             (if relevant)
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
