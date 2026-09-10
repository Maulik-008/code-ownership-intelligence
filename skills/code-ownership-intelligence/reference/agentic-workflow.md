# Agentic workflow

Four specialist subagents, each owning one job from [ADVANCED-FEATURES.md](../../../ADVANCED-FEATURES.md) and [DELIVERY-OUTPUT-DESIGN.md](../../../DELIVERY-OUTPUT-DESIGN.md), chained by the main skill acting as orchestrator. This file explains *why* the chain is shaped this way — the agents' own files ([../../../agents/](../../../agents/)) are the actual instructions each one runs on.

## Why split into agents at all

Each stage of this skill's work needs a genuinely different mindset, and mixing them in one pass tends to produce worse output on all fronts at once:

- **Research** wants to be thorough, cautious, and comfortable saying "Unknown." Rewarding it for being concise or friendly makes it skip verification.
- **Diagramming** wants to be selective and structural — its failure mode is drawing a diagram because one is available, not because it's needed.
- **Delivery** wants to compress ruthlessly and think entirely about the reader's working memory — its failure mode is including something because it was researched, not because it matters.
- **Memory** wants to be mechanical and boring — a scheduler, not an explainer. Its failure mode is trying to teach instead of just tracking.

Doing all four in a single pass tends to average these instincts out: research gets rushed to leave room for a nice write-up, or delivery keeps everything because "the research was thorough, why waste it." Separating them means each stage optimizes for its own job only, and the orchestrator — not any one stage — is responsible for the seams.

## The chain

```
Human request
      ↓
Orchestrator (SKILL.md) — scope + mode, exactly as today
      ↓
research-agent    — investigates, returns labeled findings only
      ↓
diagram-agent     — decides if a diagram earns its place, returns Mermaid or "skip"
      ↓
delivery-agent     — compresses findings (+ diagram) into the actual final answer
      ↓
memory-agent       — files the one durable fact for spaced review, surfaces anything due
      ↓
Human reads the final answer
```

Each arrow is a real handoff: what research-agent returns is not what the human sees, and delivery-agent must never receive raw source code or a live investigation task — only the compressed findings block. This is the same principle as the skill's own "explain top-down, not bottom-up" rule, just applied to the pipeline instead of to one response: each stage sees only what it needs, nothing upstream leaks through undigested.

## When to actually run the chain

**Don't, by default.** Most requests — a small question, one function, a normal change — stay inline, handled directly by the orchestrator using the existing research method and output rules, with no subagent calls at all. Spinning up four agents for "what does this function do" is exactly the over-engineering this skill's own philosophy warns against ("small + useful + memorable beats large + complete + unread").

**Do, when:**
- The request is already in the skill's own "Large or risky change" tier (money, auth, shared code, a rewrite).
- A before/after review touches several files or an unclear amount of surface area.
- The human explicitly asks for a fuller review, a diagram, or a report.

**Never split mid-response.** Once the chain starts, don't fall back to inline halfway through — that reintroduces exactly the mixed-instinct problem the split exists to avoid. If research-agent comes back with almost nothing (a small, contained finding), the orchestrator can still route it through delivery-agent alone for consistent shaping — it's fine for a stage to do very little work, just not to be skipped based on a mid-chain change of mind.

## Handling each agent's output

- **research-agent** returns a findings block (SCOPE, FLOW, RULES, EASY TO MISS, RISKS, STILL UNKNOWN, ONE THING TO REMEMBER, confidence labels). Treat "Unknown" as a first-class answer, not a gap to paper over.
- **diagram-agent** returns either one Mermaid block or an explicit "no diagram warranted" line. Respect the "no" — don't insert a diagram anyway because one seems expected.
- **delivery-agent** returns the actual Markdown the human reads. The orchestrator's job at this point is delivery, not further editing — don't append anything after it (no "hope that helps," no restating the ownership check a second time).
- **memory-agent** returns two things depending on the moment: a confirmation that an item was filed (silent, doesn't need to be shown to the human beyond maybe one line), and/or 2-3 due-review questions if any are due — these can be shown either before new research starts (a natural "quick check before we dive in" moment) or appended after delivery-agent's answer, never both in the same turn.

## Failure modes to watch for

- **Research-agent drifting into prose.** Its output should read like structured notes, not a finished explanation. If it starts writing "In conclusion..." it has stepped into delivery-agent's job.
- **Diagram-agent drawing out of habit.** If every response from this chain includes a diagram, the gate in its own instructions isn't being applied — check that "no diagram" is actually a real, frequently-taken path.
- **Delivery-agent keeping too much.** If final answers are consistently near the upper length ceiling, delivery-agent is compressing insufficiently — most findings should collapse to the "quick answer" tier.
- **Memory-agent over-surfacing.** If every session opens with a review quiz, that's fatigue, not retention — its own cap (max 3 due items, only truly overdue ones) exists specifically to prevent this.

## Composing with the rest of the skill

This chain doesn't replace anything in [research-method.md](research-method.md), [understanding-tools.md](understanding-tools.md), [output-formats.md](output-formats.md), or [human-ownership.md](human-ownership.md) — it's those same methods, distributed across four focused executions instead of one. The ownership check, the ADHD-friendly output rules, and the ten-second skim test all still apply; they just now live primarily inside delivery-agent's instructions rather than being re-derived inline every time.
