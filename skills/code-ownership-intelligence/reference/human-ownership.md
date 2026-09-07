# Human ownership

Ownership means: **the human understands the important behavior well enough to explain it, question it, debug it, maintain it, and safely change it.**

Not "the tests pass." Not "the AI can explain it." The understanding has to actually be in the human's head.

## The ownership check

Close important work with this — not a checkbox that pretends understanding is proven, but a specific, honest list of the gaps worth closing:

```
### You should understand these before moving on

1. What this code does
2. Why it exists
3. How the main flow works
4. What rules must stay true
5. What can go wrong
6. What changed
7. What the human should remember
```

Keep it specific to the current work. Drop the items that don't apply — a small change might only need 2-3 of these.

## Ownership questions

For important changes, ask questions instead of only handing over answers. The purpose is not to test the human — it's to find gaps in their understanding before those gaps turn into a production incident.

- Can you explain the main flow?
- Why is this condition needed?
- What happens if this assumption is false?
- What other parts depend on this?
- What old behavior must remain?
- What changed, and why?
- What could break?
- Where would you start debugging this later?

Pick one or two that matter most for this specific case — don't run through the whole list every time.

## The human learning loop

For a genuinely important concept, don't just dump the answer. Walk it through a short loop:

```
Research
 ↓
Explain simply
 ↓
Ask a focused question
 ↓
Let the human think
 ↓
Check the answer
 ↓
Correct missing understanding
 ↓
Remember
```

Example:

> Payment status is controlled by `PaymentService`, not by the frontend.
>
> **"If the UI changed the status directly instead of going through PaymentService, what problem could that create?"**

Wait for the answer. If they name the real risk (status and reality drifting apart, no audit trail, backend re-deriving a different status later), confirm it and add anything they missed. If they miss it, explain the gap plainly — don't just move on.

Use this only when it adds real value: a rule that's easy to violate, an assumption that's easy to get backwards, a piece of shared code with a non-obvious blast radius. **Do not turn every small task into a quiz.** Most requests should just get a clear, direct answer.

## Memory: five things, not fifty

The point isn't to make the human remember every file or function — it's to make them remember the handful of ideas that actually matter. For any important area, the shape to think in is:

```
What it does
Why it matters
Main flow
Rules that must stay true
What can go wrong
What is easy to forget
What to check before changing it
```

But the final "Remember This" the human actually reads should be short:

```
## Remember This

- Payment status is controlled by the backend.
- The frontend only displays the status.
- All payment updates go through PaymentService.
- Do not update payment status directly from the UI.
- Failed payments can still have a transaction record.
```

Prefer **5 important things I should remember** over **50 things I need to remember**. If the list is growing past that, it means too much got included — cut it down to what's actually load-bearing.
