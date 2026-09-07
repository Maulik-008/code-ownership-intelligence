# Understanding tools

These are ways to turn research into something a human can actually hold in their head. Use them for code that is central, shared, risky, or easy to misread — not for every function. A one-line helper doesn't need a mental map.

## Mental map

A simple picture of how the pieces connect. Keep it small — a handful of boxes, not the whole system.

```
User
 ↓
Checkout
 ↓
OrderService
 ├── PaymentService
 ├── InventoryService
 └── NotificationService
```

Then explain it in one or two plain sentences. Don't build a diagram because it looks good — build one because it makes the shape of the thing click faster than a paragraph would.

## Rules that must stay true

Every important area has a small number of rules that, if broken, cause real damage. Write them as plain, short statements — no hedging, no jargon.

```
## Rules That Must Stay True

- Only the backend can change payment status.
- An unpaid order cannot become completed.
- Every invoice must belong to an order.
- A deleted user cannot create a new session.
```

This list matters most: it's what the human needs to protect the next time they (or an AI) touch this area.

## Easy to miss

Things a developer could reasonably misunderstand on a first read. This is often the single most useful section in the whole response.

```
## Easy to Miss

- getStatus() also triggers a notification — it's not a pure read.
- The frontend "status" field is display-only; the backend is the real source of truth.
- `email` looks optional in the type, but the export job crashes without it.
- This service publishes an event as a side effect nobody else in this file expects.
- Status can only move forward (pending → paid → shipped), never backward.
- This helper is shared by three unrelated features — a change here touches all three.
- This odd-looking check was added to guard against a specific past production bug.
```

Look for exactly this shape of surprise: a name that undersells what the code does, a value that looks owned by one side but is actually controlled by the other, a field that looks optional but isn't, a hidden side effect, a one-directional state machine, a shared piece of code with invisible blast radius, or a strange condition that's actually a scar from a past incident.

## Normal / failure / edge / unexpected

For an important flow, don't just explain the happy path. Walk through a small number of concrete scenarios — enough to understand the shape of the behavior, not a full test plan.

```
NORMAL
Payment succeeds → order becomes paid.

FAILURE
Payment fails → order stays unpaid.

EDGE
Payment amount is zero → what happens? (check this — don't assume)

UNEXPECTED
Payment succeeds but the response is lost, and the request is retried →
does the order get charged twice, or does the retry recognize it already succeeded?
```

Use this for flows that matter (money, auth, state that's hard to undo). Skip it for low-stakes code — it's a lens, not a mandatory checklist.

## What would I break?

For shared code and business logic especially, answer this directly instead of leaving it implicit:

```
Code
 ↓
Who uses it?
 ↓
What behavior depends on it?
 ↓
What assumptions exist?
 ↓
What could change?
 ↓
What could silently break?
```

The output is a short, plain answer — "if you change X, Y and Z consume it and both assume it never returns null" — not a restatement of the code.
