# Advanced Feature Proposals for code-ownership-intelligence

Research + concrete suggestions for three additions: **(1) graphical diagram views, (2) artifact-based interactive output, (3) science-backed remember/review tricks.** Written against what the skill already does today, not as a generic feature list.

## What exists today (baseline)

- Diagrams: plain Mermaid text blocks only ([reference/output-formats.md §4](skills/code-ownership-intelligence/reference/output-formats.md)).
- Interactivity: none — HTML output is a rare, static, single-file exception with `<details>/<summary>` as the only "interactive" element.
- Memory: a single static "Remember This" list of ≤5 bullets, shown once, never re-surfaced ([reference/human-ownership.md](skills/code-ownership-intelligence/reference/human-ownership.md)).

Everything below builds on that instead of replacing it — the skill's core philosophy ("small + useful + memorable beats large + complete + unread," never pad a small answer) should still gate all of this.

---

## 1. Graphical diagram view (beyond plain Mermaid)

### 1.1 Layered diagrams, not one flat graph
Today's Mermaid example is a single flat `graph TD`. Real value comes from **switching diagram type to the question being asked**, the way CodeSee and Sourcegraph's code-intel maps do:
- `graph TD` — control flow / call sequence (already covered).
- `sequenceDiagram` — request lifecycle across services/layers (better than boxes-and-arrows for "what happens when a user clicks X").
- `stateDiagram-v2` — for exactly the "status can only move forward" rule class this skill already flags in [understanding-tools.md](skills/code-ownership-intelligence/reference/understanding-tools.md) — a state machine diagram makes an illegal transition visually obvious in a way a bullet list can't.
- `erDiagram` — data/ownership relationships ("every invoice must belong to an order").
- `classDiagram` — for "what would I break" queries, to show dependents at a glance.

**Concrete addition:** extend `reference/output-formats.md` §4 with a short decision table: *rule-about-state → stateDiagram-v2, data ownership → erDiagram, request lifecycle → sequenceDiagram, "who calls this" → classDiagram or flowchart with dependency edges.* Cheap to add, immediately raises diagram quality.

### 1.2 Before/After as one diagram, not two
Section 5 of output-formats.md currently renders before/after as parallel text blocks. A single Mermaid diagram with changed edges/nodes styled differently (`classDef changed fill:#ffecec`) lets the human see the *delta* structurally instead of diffing two paragraphs mentally. This directly serves the skill's own "did AI change more than was asked" check.

### 1.3 Blast-radius / impact diagram
The "What would I break?" tool in understanding-tools.md is currently pure text (Code → Who uses it → ...). This is the single best candidate for a diagram: a small graph with the changed node in the center and dependents radiating out, color-coded by confidence (Confirmed/Likely/Unknown — reusing the skill's existing evidence-labeling convention). This turns an abstract question into something scannable in 2 seconds.

### 1.4 Keep it plain-text-first, diagram-second
Important constraint to preserve: Mermaid stays the default because it's plain text, diffable, and lives in version control — this is a real advantage over image-based tools like CodeSee/Excalidraw, and the skill should say so explicitly when deciding between Mermaid and a heavier HTML/artifact option (see below). Don't reach for a heavier renderer just because it's available.

---

## 2. Artifact-based interactive output

This is the biggest lever, because Claude Code / Claude.ai Artifacts support real interactivity, not just a static single HTML file. Three concrete additions:

### 2.1 Collapsible layered explanation as an actual artifact
The skill already writes explanations "top-down... Level 1 → Level 4" (SKILL.md §"Explain top-down"). Right now that's just heading order in a flat Markdown file. As an artifact, this becomes literally **progressive disclosure**: Level 1 shown by default, Levels 2–4 behind expandable sections — matching the cognitive-load idea in section 3 below (don't force full detail into working memory at once). This is a natural fit for the existing `<details>` pattern in output-formats.md §3, just upgraded from "static HTML file" to a live, versioned, re-visitable Artifact URL the developer can bookmark and return to.

### 2.2 Interactive impact/dependency map
Pair with 1.3: instead of a static Mermaid PNG-in-your-head, an artifact where clicking a node highlights its callers/dependents (simple JS, no external libs needed — inline SVG per the `artifact-diagramming` skill conventions). This is exactly CodeSee's core pitch ("visual code review maps... show potential impact of a proposed change before merge") but generated on-demand from the skill's own research instead of requiring a separate always-on indexing product.

### 2.3 A living "ownership dashboard" artifact per area/feature
For genuinely large or recurring areas (the skill's own "Large or risky change" tier), publish one Artifact per feature/flow that accumulates over time rather than a new throwaway file each time:
- Sections: Mental Map, Rules That Must Stay True, Easy to Miss, Remember This, and a **review-due indicator** (ties directly into the spaced-repetition system in section 3).
- Republish (update in place) after each after-change review, so it becomes the one artifact a developer opens before touching that area again — replacing scattered `CHANGE-UNDERSTANDING.md` files with one durable, evolving reference per feature.
- This uses the Artifact database capability (shared, persistent key-value/document store) to record review history and due dates — real state, not a static snapshot.

**Important guardrail to write into the skill:** artifacts should stay the *rare, upgraded case* of an already-rare HTML case (output-formats.md is explicit that HTML is "the exception, not the default"). Add a rule: only propose an artifact when (a) the area is large/recurring enough to be worth a persistent URL, or (b) the review-scheduling feature in section 3 needs somewhere to live. Never default to it for a one-function question.

---

## 3. Scientific memory/review techniques (the biggest opportunity)

Full research brief (condensed): the current "Remember This" list applies zero memory science beyond "keep it short." Below are the techniques with real empirical backing, each mapped to something this skill could actually implement without a database or app — using local files, since the skill already writes and reads local Markdown/reference files.

| Technique | Core science | Concrete feature |
|---|---|---|
| **Spaced repetition** | Ebbinghaus forgetting curve; SM-2 algorithm (Anki/SuperMemo) — review just before forgetting, growing intervals for easy items | Write each "Remember This" bullet as a small record (concept, `next_review_date`, `ease`) to a local `.code-ownership/memory.md` or `.jsonl`. At the start of a new session touching the same area, surface only the items due, before new work — a lightweight SM-2, not a full app. |
| **Retrieval practice / testing effect** | Roediger & Karpicke (2006) — active recall beats re-reading, even with no feedback | Instead of re-stating "Remember This" verbatim on a later visit, ask it as a question first ("Why can payment status only move forward?") and let the human answer before revealing/confirming. Never lead with the answer on a re-visit. |
| **Self-explanation / elaborative interrogation** | Chi et al. (1989) — explaining material in your own words produces more accurate, transferable understanding | The skill already asks "explain it back" ([human-ownership.md](skills/code-ownership-intelligence/reference/human-ownership.md)) — formalize it: for risky changes, chain 2–3 "why" levels instead of one, and note (don't grade) where the human's paraphrase missed something, feeding that gap into what gets re-reviewed. |
| **Dual coding** | Paivio — verbal + visual encoding gives two retrieval paths, strongest for structural/relational content | When a diagram exists (section 1), keep a durable link/thumbnail attached to its "Remember This" bullets, so a later spaced-review question can show the diagram again as a recall cue instead of text alone. |
| **Chunking / schemas** | Miller (1956); Chase & Simon (1973); Soloway & Ehrlich on "programming plans" — experts store recognizable patterns, not lines | Name the *pattern* a change represents ("cache-invalidation-on-write", "retry-with-backoff") as the memorable unit, not the diff. Accumulate a small per-project `schemas.md` of named recurring patterns the AI has touched — recognizing "I've seen this shape before" is more useful than re-deriving it. |
| **Generation effect / desirable difficulties** | Slamecka & Graf (1978); Bjork — self-generated or effortful recall sticks better than being handed the answer | Before showing an ownership-check answer, ask the developer to guess first, even roughly — apply this to "where would you start debugging this" rather than only asking it rhetorically. |
| **Interleaving** | Rohrer & Taylor (2007) — mixing item types beats blocking, improves discrimination between similar cases | When multiple review items are due at once, deliberately mix items from unrelated features/flows rather than reviewing one PR's items as a block — helps distinguish similar-looking patterns (two different retry implementations, two different status machines). |
| **Cognitive load theory** | Sweller (1988) — intrinsic vs. extraneous vs. germane load; extraneous load (raw diffs, clutter) wastes limited working memory | Applies to the skill's existing "small + useful + memorable" instinct — validates it scientifically. Keep full diffs behind an optional expand (artifact `<details>`), keep the primary explanation to the germane content (the pattern, the rule), per section 2.1. |

### Minimal implementation shape (no new infra required)
1. Add a `reference/remember-and-review.md` file to the skill, parallel to the existing reference files, defining: the local memory-record format, the SM-2-style interval logic (in plain pseudocode, since this is a prompt-driven skill, not an app), and when to surface due items.
2. Extend the "Remember This" section (output-formats.md §6, human-ownership.md) to optionally write its bullets into that local memory file, each tagged with a review date — purely additive, doesn't change the default small-answer path.
3. Add one new trigger phrase to SKILL.md's description: something like "what should I review today" / "quiz me on \<feature\>" — so the review-surfacing behavior is discoverable the same way before/after modes already are.
4. Keep everything file-based and human-readable (Markdown/JSON in the repo or a dotfolder) — consistent with the skill's existing "plain text, stays in version control" preference for Mermaid, and avoids needing a database, server, or the Artifact runtime just for this piece. Artifacts (section 2.3) are the *optional* upgrade path once someone wants a persistent dashboard, not a requirement for the core spaced-review loop to work.

---

## Suggested priority order

1. **Section 3 (memory science)** — highest leverage, cheapest to implement (pure prompt/reference-file changes, no new tool dependencies), and most directly on-mission for a skill literally named "ownership intelligence."
2. **Section 1.1–1.3 (diagram variety)** — small, additive changes to an existing reference file; low risk of scope creep.
3. **Section 2 (artifacts)** — highest ceiling but should stay explicitly gated to "large/recurring area" cases, matching the skill's existing bias against over-producing files.

## Sources referenced

- [CodeSee visual code mapping](https://tiorai.com/tools/codesee/) — interactive dependency/impact maps, pre-merge blast-radius visualization
- [Mermaid vs Excalidraw vs drawio for code diagrams](https://mcp.directory/blog/drawio-vs-excalidraw-vs-mermaid-vs-penpot-skills-2026) — Mermaid's advantage as plain-text, diffable, code-reviewable diagrams
- [Claude Code Artifacts guide](https://nimbalyst.com/blog/claude-code-artifacts-guide/) — interactive, persistent, updatable artifact capabilities
- Cognitive science: Ebbinghaus (1885); Woźniak SM-2/SuperMemo; Cepeda et al. (2006); Roediger & Karpicke (2006); Chi et al. (1989); Paivio (1971); Mayer (2001); Miller (1956); Chase & Simon (1973); Soloway & Ehrlich (1984); Sweller (1988); Slamecka & Graf (1978); Bjork (2011); Rohrer & Taylor (2007); Letovsky (1986); von Mayrhauser & Vans (1995)
