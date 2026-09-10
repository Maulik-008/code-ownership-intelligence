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
- Not a bloated report machine (the deliverable is always small and scaled to the ask — never padded to look thorough).
- Not a checklist machine (the ownership check finds gaps — it doesn't prove understanding by itself).

The goal is a human who can honestly say **"I can own this"** — helped by something worth re-opening, not a chat message that scrolled away.

This is one straight flow, five steps. Don't skip the gates (1 and 3 always ask; nothing runs before they're answered) — but scale how much of steps 2 and 4 actually happen to the size of the ask.

```
1. What kind of understanding is this?     (mode)
2. What's the scope?                       (research target)
3. How do you want it delivered?           (output type — ask, don't assume)
4. Research and build it
5. Close with ownership
```

---

## Step 1 — What kind of understanding is this?

Four shapes this request usually takes. If the request already makes it obvious ("what did you just change in checkout"), don't ask — just proceed. Otherwise ask briefly:

| Shape | Key question | Full guide |
|---|---|---|
| **A diff / commit / branch** | "What actually changed, and how does it compare to before?" | [reference/after-change.md](reference/after-change.md) |
| **Before a change** (AI about to touch code) | "What am I about to let AI change, and what must NOT change?" | [reference/before-change.md](reference/before-change.md) |
| **After a change** (AI just changed code) | "Do I actually understand what AI just changed — including anything it changed that nobody asked for?" | [reference/after-change.md](reference/after-change.md) |
| **A flow / feature / area** (plain "explain this") | "How does this actually work, end to end?" | Treat as a light before-change / pure-understanding pass — same method, skip the diff-specific parts. |

These aren't hard categories — a request can be mostly one shape with a bit of another (an after-change review that also needs "explain this area" background first). Pick the closest fit and adjust as you go.

## Step 2 — What's the scope?

Don't scan the whole repo by default. If scope isn't already obvious from the request, ask — keep it short:

```
What do you want to understand?

1. Latest changes on the current branch / uncommitted changes
2. Specific commit(s), or recent commits
3. Specific file or folder
4. Specific feature
5. Specific flow (login, payment, checkout, upload, ...)
6. Full repository
7. Something else
```

Skip this menu when the request already answers it. Only fall back to a full-repository scan when the person asks for it or the task truly needs it. Scope can be one file, one flow across many files and folders, or a whole feature — this skill doesn't care how large the surface is, only that the boundary is known before research starts.

## Step 3 — How do you want it delivered? (hard gate — ask first, always)

Before any research or writing starts, ask using the terminal's interactive options tool (`AskUserQuestion` — the one that renders selectable options in the Claude Code terminal), not a plain-text question the human has to type a reply to. **Do not start Step 4 until they've picked.** This is a gate, not a courtesy.

```
question: "What output do you want for this?"
options:
  - "Markdown file (recommended)" — a small .md file: the research, the explanation, memory-science techniques baked into the writing (mnemonics, named patterns, concrete failure scenarios) so it actually sticks. Chat gets a short pointer to it. Include a Mermaid diagram inside it when one process is being traced.
  - "Quick chat answer only" — a few plain sentences, no file, for something this small/throwaway
  - "Deep explainer (HTML)" — background → intuition (toy examples) → code walkthrough → interactive quiz, as one rich HTML artifact; for a large/tangled area worth really sitting with, not a quick lookup
  - "Interactive walkthrough (build me a tool)" — a small instrumented artifact you drive yourself: step through the execution/migration/flow and watch real state change, instead of reading about it. Only offer this when the request actually fits the shape (a stateful process, a migration, a multi-step flow).
```

The human's pick always wins — if they choose "quick chat answer only," honor that instead of forcing a file. Full detail on each format, including the memory-science writing rules, the Deep-explainer skeleton, the quiz-bias guardrails, and the Micro-worlds ground rules: [reference/output-formats.md](reference/output-formats.md).

Once picked, proceed to Step 4 in that format. Every other question this skill asks — ownership checks, explain-it-back, the post-delivery quiz offer, memory-review quizzes — happens only *after* delivery, as normal chat text; they don't need this same gate.

## Step 4 — Research, then build the picked format

**Research.** Don't read files top to bottom hoping understanding shows up — ask questions, then go find the answers. Work through **Find → Follow → Connect → Explain → Challenge → Compare → Verify → Remember**. Never invent a reason: label everything **Confirmed**, **Likely**, or **Unknown**. Full method and evidence sources: [reference/research-method.md](reference/research-method.md).

**Reach for deeper tools only when the code earns it** — central, shared, risky, or easy to misread: a mental map, the rules that must stay true, what's easy to miss, normal/failure/edge/unexpected scenarios, and "what would I break." Full definitions: [reference/understanding-tools.md](reference/understanding-tools.md).

**Explain top-down.** Layer it high-level first: **what is this → how does it work → why this way → what can go wrong.** Don't open with implementation detail before the human has the shape of the thing.

**Build the format chosen in Step 3.** All four formats, their skeletons, length ceilings, and the delivery test ("a tired person skimming for 10 seconds should still walk away knowing the one thing that matters") live in [reference/output-formats.md](reference/output-formats.md) — including:
- the memory-science techniques the Markdown file's *writing* should use (not just its structure),
- the Deep-explainer HTML skeleton and its interactive-quiz bias guardrails,
- the Micro-worlds ground rules (when a driveable tool beats a written explanation, how to keep it small and fast, when to abandon a stuck piece).

**Scale, delegate if it's genuinely large.** A one-function question doesn't need the full [Final Answer Structure](reference/output-formats.md#final-answer-structure) — a real before/after review across several files usually does. For the "large or risky" tier (money, auth, shared code, a rewrite, or a request the human wants a fuller review of), delegate to the bundled specialist agents (research → diagram → delivery → memory) instead of doing it all inline — you still own Steps 1-3 and final delivery; the chain only produces content. Full delegation rules and failure modes: [reference/agentic-workflow.md](reference/agentic-workflow.md).

## Step 5 — Close with ownership

Every response — however small — ends with the human able to say **"I can own this."** Not a checkbox: a short, honest, specific list of what's still a gap.

```
### You should understand these before moving on
1. ...
2. ...
3. ...
```

The sharpest test: *"If you had to change this six months from now, without AI, would you know where to start?"*

Where it adds real value (not every small task), be a thinking partner instead of only handing over answers — ask the human to **explain it back** in their own words, and don't accept "makes sense" as proof. Full pattern, ownership-question bank, and the human learning loop: [reference/human-ownership.md](reference/human-ownership.md).

**Then, only after delivery:** offer — don't build automatically — a quick recall quiz over what was just explained. One line, easy to decline. If a real "one thing to remember" came out of this response, file it for spaced review (and surface anything already due, at the start of a session or on request): [reference/remember-and-review.md](reference/remember-and-review.md).

---

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
