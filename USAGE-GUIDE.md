# Usage Guide — When and How to Use This Skill

A practical, situation-by-situation guide. For each real moment you'll actually hit while working with an AI coding agent, this tells you: what to say, what runs behind the scenes, and what you'll get back.

If you want the *design reasoning* behind any of this, see [ADVANCED-FEATURES.md](ADVANCED-FEATURES.md), [DELIVERY-OUTPUT-DESIGN.md](DELIVERY-OUTPUT-DESIGN.md), or [skills/code-ownership-intelligence/reference/agentic-workflow.md](skills/code-ownership-intelligence/reference/agentic-workflow.md). This file is just: *what do I say, what do I get.*

---

## The one rule that decides everything else

> **Before AI touches code you don't fully understand → use it BEFORE.**
> **After AI touches code, before you trust it → use it AFTER.**
> **Anytime you're lost in a codebase → just ask it to explain.**
> **Anytime you want to check what you'll actually remember → ask it to review you.**

Four situations, four ways to invoke it. Everything below is one of these four.

---

## Do you pick the skill, or an agent? (short answer: the skill, almost always)

You never choose `research-agent`, `diagram-agent`, `delivery-agent`, or `memory-agent` by name. They're not built to be talked to directly — `research-agent`'s own instructions say explicitly that it never produces a human-facing answer, only raw findings for the next agent in line. **You always talk to the skill. The skill decides, on its own, whether to run inline or delegate to the agent chain**, based on how big/risky the ask is (see [reference/agentic-workflow.md](skills/code-ownership-intelligence/reference/agentic-workflow.md)).

So the real choice you make isn't "skill vs. agent" — it's **how much signal you put in your prompt about size and stakes**, because that's what the skill uses to decide for you.

### The template for a good prompt

```
[trigger phrase]  +  [what/where]  +  [why it matters, if it's high-stakes]
```

| Part | Examples | What it does |
|---|---|---|
| Trigger phrase | "explain this before you touch it", "what did you just change", "help me understand", "quiz me on" | Picks the mode: BEFORE / AFTER / understanding / memory |
| What/where | a file, a function, a feature name, a flow name, "the current diff", a commit hash | Sets scope so it doesn't guess or over-scan |
| Why it matters (optional but powerful) | "this touches payments", "this is a full rewrite", "this is shared by three services" | Signals stakes → nudges it toward the fuller agent-chain review instead of a quick inline answer |

**Weak prompt:** "explain this" → you'll get scope questions back, or a shallow guess at what you meant.

**Good prompt:** "explain the checkout flow before we touch it — this handles refunds too" → mode, scope, and stakes are all there in one line; you'll likely get a real review with a diagram if one earns its place, not just a paragraph.

### If you specifically want the full agent chain to run

You still don't name an agent — you just say so in plain language, and the skill runs the chain (research → diagram → delivery → memory-filing) for you:

```
Give this a full review
This is a big/risky change, go deep
I want the complete before/after with diagrams
```

### The only agent you might reasonably address directly

`memory-agent`'s job — checking what's due for review — is the one piece that's meant to be triggered on its own, any time, independent of a code change:

```
What should I review today?
Quiz me on the payment flow
```

This still goes through the skill (not a raw agent call), but it's a standalone entry point rather than something that only fires mid-review.

---

## Situation 1 — "I'm about to let AI change something I don't fully understand"

**When:** You're about to ask an AI agent to touch a file, feature, or flow you didn't write, or don't remember well — especially anything involving money, auth, shared code, or a rewrite.

**What to say (any of these work):**
```
Before you touch this, explain it first
Explain the checkout flow before we change anything
What am I about to let you change in PaymentService?
```

**What happens internally:**
- Mode: **BEFORE CHANGE**
- If scope isn't obvious, you'll get a short numbered menu (file? feature? flow? branch?) — answer with a number or just restate what you meant.
- Before any research starts, you'll get a quick pick with checkboxes — Markdown file, quick chat answer, deep HTML explainer, or a tool you drive yourself. Check one or several; answer that first, nothing gets built until you do.
- Small/normal change → handled inline, no subagents, fast.
- Large/risky change (money, auth, a rewrite) → the full agent chain runs: research-agent digs in, diagram-agent adds a picture only if the shape is genuinely relational, delivery-agent compresses it into the final answer.

**What you get back:**
- Whatever format you picked — usually a short Markdown file, chat gets just a pointer to it. Something like:
  > "This touches `PaymentService.charge()`. It's called from checkout and the retry job — both would be affected. Today a failed charge is final; there's no partial-refund path. Full breakdown in `CODEBASE-UNDERSTANDING.md`."
- For a real "Large" change: a diagram (if warranted), the rules that must stay true, and a closing list — **"You should understand these before I start"** — 2-4 specific gaps, not a generic checklist.
- It may ask you a question back ("this function is used by three flows — want to check those first?"). Answer it — that's the point, not a formality.

**You're done when:** you can say, out loud, what must *not* change before the AI starts.

---

## Situation 2 — "AI just changed something and I want to know what actually happened"

**When:** Right after an AI agent finishes editing — a diff exists, a commit landed, a PR is open.

**What to say:**
```
What did you just change?
Did this break anything else?
Can I own this change?
Walk me through what changed in the retry logic
```

**What happens internally:**
- Mode: **AFTER CHANGE**
- First move, always: **requested vs. actually changed vs. extra changes** — it checks whether the AI touched more than you asked for, before anything else.
- Then it hunts specifically for *silent* behavior change: conditions, permissions, error handling, side effects, retry behavior — not just "does the new thing work."
- Same size rule as before: small diff → quick chat answer; a diff that touches several files or changes behavior non-obviously → a real BEFORE/CHANGE/AFTER/IMPACT comparison, possibly with the delta shown as one diagram (changed nodes highlighted, not two side-by-side pictures).

**What you get back:**
- A plain before → after → impact story:
  > "Before: a failed charge was final. After: it retries up to 3 times, then flags for a human. Impact: the retry job now also touches this path — check its assumptions."
- Anything extra the AI changed beyond your request, flagged explicitly, even if it looks harmless.
- **Never** "the tests pass, so it's fine" as a closing line — if it says that, push back; tests are evidence, not proof, and the skill is written to know the difference.
- Ends with a specific ownership check: what changed, what might now behave differently, what to double-check before trusting this in production.

**You're done when:** you can answer "did something old quietly stop working, even though the new thing works?"

---

## Situation 3 — "I just don't understand this code / this codebase / this flow"

**When:** No change is happening. You inherited something, you're onboarding, or you're just lost.

**What to say:**
```
Explain this file
Help me understand this codebase
Walk me through the login flow
What does this function actually do?
```

**What happens internally:**
- Treated as a light BEFORE CHANGE / pure-understanding pass — same research method, no diff-specific steps (no "what changed," no "did AI go beyond scope").
- Explained top-down: what it is → how it works → why it's built this way → what can go wrong. Never starts with implementation detail before you have the shape of the thing.

**What you get back:** first, a quick checkbox question — how do you want this delivered? (Markdown file, quick chat answer, deep HTML explainer, tool you drive yourself — check as many as you actually want) — then, once you pick, it builds every format you checked from the same research pass:
- **Markdown file (the recommended default):** chat gets a 1-3 sentence pointer to it, plus an optional offer afterward to build a quick recall quiz from it. For one function or a small question, it's a short file — the one-line answer plus "Remember This," no padding. For a real feature or flow, a short mental map, the rules that must stay true, and what's easy to misread on a first pass. For a large, tangled area, a fuller `CODEBASE-UNDERSTANDING.md`/`FLOW-UNDERSTANDING.md`, still chunked into short sections, with a diagram where one flow is genuinely being traced.
- **Quick chat answer:** a few plain sentences, no file — for something small enough it's not worth saving. Checked alongside another format, it's just the fast version delivered first, while the fuller thing is still being built.
- **Deep explainer:** one rich HTML artifact — background, a worked toy example, a code walkthrough, and a 5-question quiz built in.
- **Interactive walkthrough:** a small tool you drive yourself — step through a migration or execution and watch real state change, instead of reading about it. Only offered when the request is actually a stateful, multi-step process.

Want both a file to keep and a quick answer right now? Check both boxes — that's exactly what multi-select is for.

**You're done when:** you could explain the flow back in your own words. If asked to try, actually try — a "makes sense" doesn't count as understanding here.

---

## Situation 4 — "What should I actually remember, and check that I still do"

**When:** Any time after a real "one thing to remember" has come out of a prior session — a new day starting, before touching a familiar-but-rusty area, or just wanting a gut-check.

**What to say:**
```
What should I review today?
Quiz me on the payment flow
Do I still remember why that retry limit exists?
```

**What happens internally:**
- `memory-agent` checks `.code-ownership/memory.md` in your project for anything due.
- You'll get **at most 2-3** items, mixed across unrelated areas if more than one is due — never a long quiz, never grouped by topic (mixing helps you actually tell similar patterns apart).
- It asks a **question**, not a restatement: "Why can payment status only move forward?" — not "Remember: payment status only moves forward."

**What you get back:**
- A short back-and-forth: it asks, you answer (even a rough guess is fine — that's more useful than being told), it confirms or fills the gap.
- Recalled it well → that item won't come back for a while (or ever again, if you've nailed it a few times running).
- Missed it or got it wrong → it resets to short-interval review, and you get the actual explanation again, this time because you asked for it rather than having it re-dumped on you.
- If nothing is due: it just tells you that plainly and moves on — it won't invent something to quiz you on.

**You're done when:** you can answer without the skill's help — that's when an item stops needing review at all.

---

## Quick decision table

| You're thinking... | Say something like | Mode |
|---|---|---|
| "AI is about to touch code I don't know well" | "explain this before you touch it" | BEFORE |
| "AI just finished, did it do only what I asked?" | "what did you just change" | AFTER |
| "I inherited/forgot this, just explain it" | "explain this flow/file/feature" | Understanding (light BEFORE) |
| "Am I actually retaining what I've learned?" | "what should I review today" / "quiz me on X" | Memory / review |

---

## What controls how big the answer is (you don't have to ask for this)

You never need to request a specific format — the skill sizes itself:

| Situation | Default output |
|---|---|
| One function, small change, simple question | 3-5 sentences in chat. No file. No diagram unless one relationship genuinely needs it. |
| A feature, a module, a normal change | A short structured answer: explanation + flow + rules + risks + one thing to remember. Still usually just chat. |
| Money, auth, shared code, a rewrite, or you explicitly ask for "a full review" | The full agent chain runs (research → diagram → delivery → memory-filing). You may get a Markdown report, a diagram, or — rarely — a self-contained HTML file, only if it genuinely beats reading text. |

If you ever get more than you wanted, just say "shorter" or "just the important part" — the skill is designed to compress, not to insist on completeness.

## What controls whether a diagram shows up

You don't need to ask for one by name — say what's confusing and let it decide:

- "I don't get how this flows" → likely a sequence/flow diagram.
- "I don't get why status can't go backward" → likely a state diagram.
- "What would break if I change this" → likely a small dependency/impact diagram.
- A plain factual question → usually no diagram at all — and that's correct, not a missed opportunity. One clear diagram beats several competing ones; if a picture wouldn't help, it won't force one in.

## If you want the deeper/agent-level review specifically

Normally you don't choose this — the skill escalates on its own for large/risky changes. But you can ask for it directly:

```
Give this a full review
This is a big change, go deep
I want the complete before/after with diagrams
```

This explicitly triggers the four-agent chain (research → diagram → delivery → memory) even if the change would otherwise have been handled inline.

## Where things get saved (if anything does)

- Most answers: **nowhere** — they're just chat, by design, so you're not left hunting for files you didn't need.
- A real report: `CODEBASE-UNDERSTANDING.md`, `CHANGE-UNDERSTANDING.md`, `FEATURE-UNDERSTANDING.md`, or `FLOW-UNDERSTANDING.md`, named for what it covers.
- Your remembered facts: `.code-ownership/memory.md` in the project — plain Markdown, safe to open and read yourself anytime, not just through the skill.

## First-time setup (if you haven't installed it yet)

Pick one, from [README.md](README.md):
- **Fastest, one project:** copy `skills/code-ownership-intelligence/` into that project's `.claude/skills/`.
- **Every project on your machine:** copy the same folder into `~/.claude/skills/`.
- **Share with a team / install anywhere:** use it as a plugin — `/plugin marketplace add` + `/plugin install`, see README for the full steps.

No configuration after that — it auto-triggers on the phrases above, or you can invoke it directly with `/code-ownership-intelligence`.

---

## The one thing to remember about this whole skill

You are still the owner of any code an AI writes. This skill's only job is making sure that's actually true — not that the code works, not that tests pass, but that **you** could explain it, question it, and fix it yourself six months from now without any AI in the room.
