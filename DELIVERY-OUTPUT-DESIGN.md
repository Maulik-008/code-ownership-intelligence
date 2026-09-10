# Final Delivery Output Design — ADHD-Friendly, Visual-First

How the skill's *actual output* (chat reply, Markdown report, or artifact) should look and feel, so a person never faces a wall of text — they get something they can glance at, feel calm about, and immediately understand. This is about **presentation of the final answer**, not the research process (already covered in [ADVANCED-FEATURES.md](ADVANCED-FEATURES.md)).

## The one-line design goal

> **A tired person, skimming for 10 seconds, should still walk away knowing the one thing that matters.**

Everything below is a test against that line.

---

## 1. Why this matters (the research, compressed)

- **Progressive disclosure** (Nielsen, 1995): show only what's needed right now; hide the rest behind a click. Reduces errors and overwhelm in complex systems — directly applicable to code explanations, which are inherently complex. [Source: IxDF](https://ixdf.org/literature/topics/progressive-disclosure)
- **ADHD-friendly content design**: short sections with *one main idea each*, descriptive headings, generous spacing, lists over paragraphs, predictable structure so a reader can "dip in and out" without losing their place. [Source: Neuroinclusive Content Design](https://www.influencers-time.com/designing-adhd-friendly-content-key-neuroinclusive-principles/)
- **Plain language / WCAG cognitive accessibility**: short sentences, no jargon without a plain definition, bullets and numbered lists over prose, bold instead of ALL CAPS/italics for emphasis. This is a *documented accessibility requirement* (WCAG "Understandable" principle), not just a style preference. [Source: WCAG plain language](https://www.wcag.com/blog/less-is-more-writing-in-plain-language/)
- **Cognitive load theory** (Sweller): every extra word, every diff dumped in full, every unnecessary heading is **extraneous load** — it competes for the same limited working memory as the actual understanding you're trying to build. Cut ruthlessly.

The underlying idea in all four: **the reader's attention and working memory are the scarcest resource in the whole interaction.** Every design choice below spends that resource deliberately or wastes it.

---

## 2. Concrete rules for this skill's output

### 2.1 Lead with the one-sentence answer, always
Before any structure, headings, or lists — one plain sentence that answers "what do I actually need to know." Everything after that is optional depth for someone who wants more.

```
This function safely retries a failed payment up to 3 times, then gives up and flags it for a human.
```

Not: opening with "Let me explain the architecture of the payment retry subsystem..."

### 2.2 One idea per section, section titles do the work
A reader scanning only the headings should understand the shape of the answer without reading a single body line. If a heading needs the paragraph below it to make sense, the heading has failed.

- Bad: "## Details" → forces reading.
- Good: "## Payment can only retry 3 times, then it stops" → the heading *is* the takeaway.

### 2.3 Cap the default response length hard
Extend the existing "small + useful + memorable" rule with actual numbers, since the current guidance has no ceiling:

| Response size | Max length |
|---|---|
| Quick answer (default) | 3–5 sentences or bullets, no headings |
| Medium (feature/module) | ~150–250 words total, 3–5 short sections |
| Large/risky (already rare) | Still chunked into sections of ≤5 lines each — never a large unbroken block, no matter how big the total is |

If a "Large" response would exceed this without cutting content, that's a signal to split it — a short chat summary **plus** a link to a longer artifact/report, rather than one long wall.

### 2.4 Every section maxes out at ~5 lines before it needs a break
This mirrors the skill's own existing "Remember This: five things, not fifty" instinct — just applied everywhere, not only to that one section. If a section runs long, break it into sub-bullets or a table, or split it into two sections with their own headings.

### 2.5 Visual-first for anything structural or relational
If the content describes *connections* (flow, hierarchy, before/after, dependencies) — draw it, don't describe it in prose. A 4-node diagram beats a paragraph explaining the same 4 relationships, every time, per the dual-coding research already cited in ADVANCED-FEATURES.md. Concretely:
- Flow/sequence → Mermaid diagram, not a numbered paragraph.
- A rule with a boundary ("can only move forward") → a small before/after state diagram, not a sentence with "note that."
- A comparison (before vs after, old vs new) → a two-column table or side-by-side diagram, not two separate paragraphs the reader has to hold in memory simultaneously.

### 2.6 One visual anchor per response, not five
Don't scatter five small diagrams through a response — pick the single most important relationship and show it once, clearly. More diagrams competing for attention is its own form of overload. If multiple diagrams feel necessary, that's usually a sign the response itself is too big for the default path (see 2.3).

### 2.7 Plain words, defined jargon, no unexplained acronyms
Every technical term either is common knowledge for the audience or gets a 3–5 word plain-language gloss inline the first time it's used: "uses **idempotency** (calling it twice has the same effect as once)." Never assume the reader will look it up — that's an interruption, and interruptions are exactly what ADHD-friendly design tries to avoid.

### 2.8 Emphasis is rare and means something
Bold only the 1–3 words in a section that matter most — not whole sentences, not everything. If everything is bold, nothing is. No italics or ALL CAPS for emphasis (both are harder to scan and are explicitly flagged in WCAG guidance as worse for cognitive accessibility).

### 2.9 Predictable shape, every time
Reuse the same section order and the same visual language across responses (this skill already has a Final Answer Structure in output-formats.md — good). Predictability itself reduces cognitive load: a reader who has seen the shape before doesn't have to re-learn how to read the response, they just look for the part they need.

### 2.10 End with exactly one takeaway, never a summary of everything already said
Don't close by re-stating the whole response. Close with the single most important thing, restated once, and stop:

```
## The one thing to remember
Payment status only moves forward. If you're tempted to reset it, don't — build a new record instead.
```

Not a "Summary" section that repeats every heading above it — that's re-reading disguised as a conclusion, and it adds length without adding information.

---

## 3. A concrete before/after example

**Before (current default shape — technically fine, but a wall):**
```
## What I Looked At
I reviewed the PaymentService, OrderController, and the three tests covering retry logic...
## Simple Understanding
The payment retry mechanism works by catching failures from the payment gateway and...
## Mental Map / Main Flow
[long paragraph describing OrderService -> PaymentService -> Gateway -> Webhook -> ...]
## Important Rules
...
```

**After (same information, redesigned to the rules above):**
```
Retries a failed payment up to 3 times, then flags it for a human. Nothing resets the order status early.

    OrderService → PaymentService → Gateway
                        ↑ retries here, max 3
                        ↓ gives up → flags for human

## The one rule that matters
Payment status only moves forward — never reset it manually.

## Easy to miss
- The 3rd failure sends a Slack alert, it's not silent.

## The one thing to remember
If a payment looks "stuck," check the retry count before assuming it's broken — 3 failures is expected behavior, not a bug.
```

Same underlying research and same rigor — roughly a third of the length, one diagram doing the work of a paragraph, and a reader who skims only the bold lines still gets the point.

---

## 4. Where to wire this in

This isn't a new mode — it's a tightening of rules that already exist in the skill, made concrete enough to actually follow:

1. **`SKILL.md`** — under "Decide the output," add the one-sentence-lead and single-visual-anchor rules directly (currently only says "small + useful + memorable," no concrete ceiling).
2. **`reference/output-formats.md`** — add the length table from §2.3 and the "one idea per section" test from §2.2 to the Final Answer Structure section; add the visual-first rule (§2.5) to the diagram decision point already suggested in ADVANCED-FEATURES.md §1.1.
3. **`reference/human-ownership.md`** — tighten "Remember This: five things, not fifty" into "the one thing that matters," per §2.10 — an even sharper version of a rule already in the file.

No new files needed beyond this one — this is a set of edits to tighten existing reference files, not a new subsystem.

## Sources

- [Progressive Disclosure — Interaction Design Foundation](https://ixdf.org/literature/topics/progressive-disclosure)
- [Progressive Disclosure UX — LogRocket](https://blog.logrocket.com/ux-design/progressive-disclosure-ux-types-use-cases/)
- [Neuroinclusive / ADHD-friendly content design principles](https://www.influencers-time.com/designing-adhd-friendly-content-key-neuroinclusive-principles/)
- [ADHD-friendly design, high legibility](https://www.influencers-time.com/adhd-friendly-design-high-legibility-tips-for-2025/)
- [WCAG — Less is More: Plain Language](https://www.wcag.com/blog/less-is-more-writing-in-plain-language/)
- [Cognitive Load and Web Accessibility](https://www.boia.org/blog/cognitive-load-and-web-accessibility-quick-tips-for-clearer-content)
- Sweller (1988), Cognitive Load Theory — also cited in [ADVANCED-FEATURES.md](ADVANCED-FEATURES.md)
