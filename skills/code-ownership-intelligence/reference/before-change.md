# Before change

Goal: before an AI agent starts changing code, make sure the human understands what's actually there. This is a guard rail, not a delay tactic — it should be as fast as the question allows.

## Flow

```
User request
    ↓
What is being changed?
    ↓
Find related code
    ↓
Understand current behavior
    ↓
Understand business rules
    ↓
Trace important flows
    ↓
Find dependencies and side effects
    ↓
Find risks and edge cases
    ↓
Human understanding check
    ↓
AI starts the change
```

Use [research-method.md](research-method.md) to actually do the "find / understand / trace" steps — don't reinvent a process, use Find → Follow → Connect → Explain → Challenge → Compare → Verify → Remember.

## Questions to answer

- What code will probably be changed?
- Why is that code related to the request?
- How does it work today?
- What existing behavior must keep working?
- What other parts depend on it?
- What business rules are involved?
- What could break?
- What should the developer understand before allowing this change to happen?

Answer only the ones that matter for this specific request. A one-line config change doesn't need all eight; a rewrite of a payment flow does.

## Sizing the response

Most before-change checks are small. Default to a quick chat answer:

- "This touches `PaymentService.charge()`. It's called from checkout and from the retry job — both would be affected. Today it treats a failed charge as final; there's no partial-refund path. Want me to check the retry job's assumptions before we change this?"

Only escalate to a Markdown report or diagram when the area is genuinely large or tangled (many files, several flows, unclear ownership). See [output-formats.md](output-formats.md) for when and how.

## Be a thinking partner here specifically

Before-change is the best moment to ask the human to think, not just read:

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

Keep it to what actually matters for *this* change — not a generic list.
