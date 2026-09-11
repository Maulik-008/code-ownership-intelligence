# Code Ownership Intelligence

A Claude Code skill/plugin for one problem: **AI can write code fast, but that doesn't make the human who asked for it responsible any less.** They still have to understand it, question it, debug it, and maintain it later — long after the AI session that produced it is gone.

> **AI is the worker. The human is still the owner.**
> Understand first. Think second. Change third. Own always.

This isn't a code review tool, a documentation generator, or a bug-finder. It's a rebuild of the understanding a developer used to get for free by writing the code themselves — and it exists because that understanding doesn't happen automatically just because an AI agent did the typing.

---

## The problem this solves

When you write code yourself, understanding is a side effect — you can't type it without also building a mental model of it. When an AI writes it for you, that side effect disappears. You get working code and *zero* forced understanding, unless something makes you stop and build it deliberately.

Two moments matter most:

- **Before an AI touches unfamiliar code** — you're about to hand over a task in an area you don't fully understand yet. What's actually there today? What must *not* break?
- **Right after an AI changes code** — something now exists that you didn't write. Do you actually understand what changed, including anything the AI touched that nobody asked for?

This skill exists for both moments, plus the everyday case of just wanting to understand a flow, a feature, or a folder you're new to.

---

## The flow — one straight path, five steps

Every request goes through the same five steps, in order. Steps 1 and 3 are **hard gates** — the skill stops and asks before proceeding past them. Steps 2 and 4 scale up or down depending on how big the ask actually is.

```
1. What kind of understanding is this?     (mode)
2. What's the scope?                       (research target)
3. How do you want it delivered?           (output type — ask, don't assume)
4. Research and build it
5. Close with ownership
```

### Step 1 — What kind of understanding is this?

Four shapes a request usually takes. The skill infers this from what you say — you rarely have to answer this explicitly.

| Shape | Example trigger | Key question it asks |
|---|---|---|
| A diff / commit / branch | "what did you just change" | What actually changed, and how does it compare to before? |
| Before a change | "explain this before you touch it" | What am I about to let AI change, and what must NOT change? |
| After a change | "did this break anything else" | Do I actually understand what changed — including anything nobody asked for? |
| A flow / feature / area | "explain the checkout flow" | How does this actually work, end to end? |

### Step 2 — What's the scope?

If it's not already obvious from your request, the skill asks — a short numbered menu (this file? this feature? this flow across several folders? the whole repo?). Scope can be one function or a flow that spans many files and folders; the skill doesn't care how large the surface is, only that the boundary is known before research starts.

### Step 3 — How do you want it delivered? *(hard gate)*

Before any research happens, the skill stops and asks — as real checkboxes in the Claude Code terminal, not a question you'd have to type a reply to. **You can check more than one** — it's not an either/or:

| Option | What you get |
|---|---|
| **Markdown file** *(recommended default)* | A small `.md` file — explanation, rules, flow — written with memory techniques baked in (mnemonics, named patterns, concrete failure scenarios) so it's actually easy to recall later, not just accurate. Chat gets a short pointer to it. |
| **Quick chat answer only** | A few plain sentences, no file — for something genuinely small or throwaway. Checked alongside another option, it just means "give me the fast answer now, and still build the other thing." |
| **Deep explainer (HTML)** | One rich, self-contained artifact: Background → Intuition (toy examples) → Code walkthrough → a 5-question interactive quiz. For a large or tangled area worth really sitting with. |
| **Interactive walkthrough** | A small tool you drive yourself — step through a migration, an execution, or a multi-step flow and watch real state change, instead of reading about it. Offered only when the request actually fits that shape. |

Your picks always win — check one for a single deliverable, or several if you want, say, both a Markdown file to keep and a quick answer right now. Nothing gets built until you've answered.

### Step 4 — Research, then build

The skill investigates using a repeatable method (**Find → Follow → Connect → Explain → Challenge → Compare → Verify → Remember**), labels every claim **Confirmed / Likely / Unknown**, and never presents a guess as fact. It explains top-down — what it is, how it works, why it's built that way, what can go wrong — before it ever gets into implementation detail. For a genuinely large or risky ask (money, auth, shared code, a rewrite), it delegates to four specialist subagents (research → diagram → delivery → memory) instead of doing everything in one pass; for everything else, it just does the work directly.

### Step 5 — Close with ownership

Every response — however small — ends with a short, honest list: **"You should understand these before moving on."** Not a checkbox. The sharpest test behind it: *if you had to change this six months from now, without AI, would you know where to start?* Where it genuinely helps, the skill asks you to explain the flow back in your own words instead of just handing you the answer — "makes sense" doesn't count as proof of understanding.

After delivery, it may offer one more thing: a quick recall quiz built from what it just explained, entirely optional, and if you accept a "one thing to remember" gets filed for spaced review — so weeks later, when you've forgotten, the skill can quiz you on it before it's needed for real.

---

## A worked example

You say: *"before you touch this, explain the refund logic in PaymentService"*

1. **Mode** — before-change, inferred from your phrasing. No need to ask.
2. **Scope** — you already named it (`PaymentService`, refund logic), so no menu.
3. **Output type (gate)** — you get four checkboxes in the terminal. You check just "Markdown file."
4. **Research** — the skill traces how refunds work today, who calls this code, what business rules protect it, what would break if it changed. It writes `CODEBASE-UNDERSTANDING.md` and drops a two-sentence pointer in chat.
5. **Ownership close** — the file ends with *"You should understand these before I start"* — three specific things, not a generic list. Maybe it asks: *"This function is also called by the retry job — want to check that path before we touch it?"*

You now know what the AI is about to change, before it changes it.

---

## Install

This repo works two ways: as a **plain skill** you drop into a project, or as an installable **Claude Code plugin**.

### Option A — One project (fastest)

```bash
mkdir -p .claude/skills
cp -r /path/to/code-ownership-intelligence/skills/code-ownership-intelligence .claude/skills/
```

Commit `.claude/skills/code-ownership-intelligence/` so teammates get it automatically. No config needed.

### Option B — Every project on your machine

```bash
mkdir -p ~/.claude/skills
cp -r /path/to/code-ownership-intelligence/skills/code-ownership-intelligence ~/.claude/skills/
```

### Option C — Install as a plugin (shareable, updatable, includes the specialist subagents)

```bash
# Try locally first, no install step
claude --plugin-dir "/path/to/code-ownership-intelligence"
```

Or install properly, from a local path:

```
/plugin marketplace add "/path/to/code-ownership-intelligence"
/plugin install code-ownership-intelligence@code-ownership-intelligence
```

Or from this repo's git remote, from any machine:

```
/plugin marketplace add Maulik-008/code-ownership-intelligence
/plugin install code-ownership-intelligence@code-ownership-intelligence
```

To auto-enable it for every teammate who opens a project, add to that project's `.claude/settings.json`:

```json
{
  "enabledPlugins": {
    "code-ownership-intelligence@code-ownership-intelligence": true
  }
}
```

**Updating:** Option A/B — re-copy the folder. Option C — bump `version` in `.claude-plugin/plugin.json` and `.claude-plugin/marketplace.json`, push, then `/plugin marketplace update code-ownership-intelligence`.

### After installing

Nothing else to configure. It auto-triggers on phrases like *"explain this before you touch it"* or *"what did you just change"*. You can also invoke it by name — `/code-ownership-intelligence` (Option A/B) or `/code-ownership-intelligence:code-ownership-intelligence` (Option C).

---

## Repo layout

```
code-ownership-intelligence/
├── .claude-plugin/
│   ├── plugin.json                # plugin manifest
│   └── marketplace.json           # makes this repo installable via /plugin marketplace add
├── skills/code-ownership-intelligence/
│   ├── SKILL.md                   # the actual skill — the 5-step flow. Copy just this folder for Option A/B.
│   └── reference/
│       ├── before-change.md       # Step 1: before-change mode, full flow and questions
│       ├── after-change.md        # Step 1: after-change mode, full flow and questions
│       ├── research-method.md     # Step 4: the Find→Follow→Connect... research method
│       ├── understanding-tools.md # Step 4: mental maps, rules that must stay true, "what would I break"
│       ├── output-formats.md      # Step 3/4: all four output formats, skeletons, length rules
│       ├── human-ownership.md     # Step 5: the ownership check, explain-it-back pattern
│       ├── remember-and-review.md # Step 5: spaced-review mechanics (the memory layer)
│       └── agentic-workflow.md    # Step 4: when/how the skill delegates to the subagents below
├── agents/                         # specialist subagents for large/risky reviews — auto-discovered
│   ├── research-agent.md          #   investigates, returns labeled findings only
│   ├── diagram-agent.md           #   decides if a diagram earns its place
│   ├── delivery-agent.md          #   compresses findings into the actual human-facing file
│   └── memory-agent.md            #   files "the one thing to remember," surfaces due reviews
├── README.md                       # this file
├── USAGE-GUIDE.md                  # situation-by-situation: what to say, what you get back
├── ADVANCED-FEATURES.md            # the research and rationale behind diagrams, artifacts, memory science, HUD mode
└── DELIVERY-OUTPUT-DESIGN.md       # the ADHD-friendly / cognitive-load design rules behind every response's shape
```

## Going deeper

- **[USAGE-GUIDE.md](USAGE-GUIDE.md)** — the practical, situation-by-situation guide: exactly what to type and what comes back, for every real moment (before a change, after a change, plain "explain this," and spaced-review check-ins).
- **[ADVANCED-FEATURES.md](ADVANCED-FEATURES.md)** — the design rationale: why Markdown-first, how the memory-science techniques work, what a Deep explainer / Micro-world actually is and why, and an opt-in "HUD mode" direction that hasn't been built yet.
- **[skills/code-ownership-intelligence/SKILL.md](skills/code-ownership-intelligence/SKILL.md)** — the skill's actual instructions, if you want to read exactly what governs its behavior.

## License

Declared as MIT in [.claude-plugin/plugin.json](.claude-plugin/plugin.json) — no `LICENSE` file exists in the repo yet; add one if you plan to distribute this beyond your own team.
