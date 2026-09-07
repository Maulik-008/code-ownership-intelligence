# Before change

Key question: **"What am I about to let AI change?"**

Goal: before an AI agent starts changing code, make sure the human understands what's actually there. This is a guard rail, not a delay tactic — it should be as fast as the question allows.

## Flow

```
Human request
      ↓
What is the human trying to change?
      ↓
Find related code
      ↓
Understand current behavior
      ↓
Understand important business rules
      ↓
Trace the important flow
      ↓
Find dependencies
      ↓
Find assumptions
      ↓
Think about normal + failure + edge scenarios
      ↓
Find risks
      ↓
Explain important decisions
      ↓
What must NOT change?
      ↓
Human understanding check
      ↓
AI starts the change
```

Do the "find / understand / trace" steps with [research-method.md](research-method.md) — Find → Follow → Connect → Explain → Challenge → Compare → Verify → Remember. For the code that turns out to be central to the change, apply the relevant tools from [understanding-tools.md](understanding-tools.md): a mental map if the shape is confusing, the rules that must stay true, what's easy to miss, and normal/failure/edge scenarios if the flow matters (money, auth, hard-to-undo state).

## Questions to answer

- What is going to be changed?
- Where is the current behavior implemented?
- How does it work today?
- Why is this code related to the request?
- What business rules are involved?
- What other code depends on it?
- What assumptions does it make?
- What must continue working?
- What could break?
- What should the human understand before the AI starts?

Answer only the ones that matter for this specific request. A one-line config change doesn't need all ten; a rewrite of a payment flow does.

Don't scan the entire repository unless the change actually requires it — stay scoped to what the request touches plus its real dependencies.

## What must NOT change

Before the AI starts, say out loud what's off-limits — a boundary around the AI, not just a description of the present. Use whatever's relevant from: existing business rules, existing permissions, existing API behavior, existing retry behavior, existing data guarantees, existing workflows, existing side effects. In practice this is usually the same list as "rules that must stay true" (see [understanding-tools.md](understanding-tools.md)), just asked from the angle of "here's what you may not touch" instead of "here's how it works today."

Skip this for trivial changes — a one-line config tweak doesn't need a boundary statement. Use it when the change is real enough that an AI could plausibly "fix" something nearby that wasn't part of the request.

The before-change mental model, in short:

```
What are we changing?  →  What exists today?  →  What must stay the same?  →
What is allowed to change?  →  What could break?  →  Human understands  →  AI changes
```

## Sizing the response

Most before-change checks are small. Default to a quick chat answer:

- "This touches `PaymentService.charge()`. It's called from checkout and from the retry job — both would be affected. Today it treats a failed charge as final; there's no partial-refund path. Want me to check the retry job's assumptions before we change this?"

Only escalate to a Markdown report or diagram when the area is genuinely large or tangled (many files, several flows, unclear ownership). See [output-formats.md](output-formats.md) for when and how, and how depth should scale with the size of the change.

## Be a thinking partner here specifically

Before-change is the best moment to ask the human to think, not just read — see [human-ownership.md](human-ownership.md) for the full pattern:

- "Before accepting this change, can you explain why this condition is required?"
- "This function is used by three workflows. Do you want to review those before we touch it?"
- "This looks like it enforces a business rule, not just a technical check — do you know which rule?"

## Close with a short ownership check

```
### You should understand these before I start
1. ...
2. ...
3. ...
```

Keep it to what actually matters for *this* change — not a generic list. Full template in [human-ownership.md](human-ownership.md).
