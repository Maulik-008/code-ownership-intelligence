# Code Ownership Intelligence

A Claude Code skill/plugin that helps a human understand, question, and own code that an AI agent is about to change or just changed. See [skills/code-ownership-intelligence/SKILL.md](skills/code-ownership-intelligence/SKILL.md) for what it actually does.

This repo works two ways at once:
- as a **plain skill** you can drop into any project or your own machine, or
- as an installable **Claude Code plugin**, shareable with a team through git.

Pick whichever fits. You don't need to do both.

## Option A — Use it in one project (fastest)

Copy the skill folder into that project's `.claude/skills/` directory:

```bash
# from inside your project
mkdir -p .claude/skills
cp -r /path/to/code-ownership-intelligence/skills/code-ownership-intelligence .claude/skills/
```

Commit `.claude/skills/code-ownership-intelligence/` to the project's repo so teammates get it automatically — it needs no extra config. Claude Code picks it up the next time you open the project, and auto-invokes it whenever a request matches (e.g. "explain this before you change it", "what did you just change").

## Option B — Use it everywhere on your machine

Copy the same folder into your personal skills directory instead:

```bash
mkdir -p ~/.claude/skills
cp -r /path/to/code-ownership-intelligence/skills/code-ownership-intelligence ~/.claude/skills/
```

Now every project you open with Claude Code has it available, without touching each repo.

## Option C — Install it as a plugin (shareable, updatable)

This is the setup to use if you want to **share it with a team**, install it on **another machine**, or **upload it somewhere others can pull it from** (a git host).

### C1. Try it locally first, no install step

```bash
claude --plugin-dir "/path/to/code-ownership-intelligence"
```

This loads the plugin for just that session — good for testing before you share it.

### C2. Install from a local path

Inside a Claude Code session:

```
/plugin marketplace add "/path/to/code-ownership-intelligence"
/plugin install code-ownership-intelligence@code-ownership-intelligence
```

The first command registers this folder as a marketplace (it already describes itself as one, via `.claude-plugin/marketplace.json`). The second installs the plugin from it. After this, it behaves like any other installed plugin — `/plugin list` shows it, `/plugin disable` / `/plugin uninstall` manage it.

### C3. Push it to a git host, then anyone can install it

This repo already has a remote: `https://github.com/Maulik-008/code-ownership-intelligence.git`. Once the current changes are committed and pushed there, from any Claude Code session (yours, another machine, a teammate's):

```
/plugin marketplace add Maulik-008/code-ownership-intelligence
# or
/plugin marketplace add https://github.com/Maulik-008/code-ownership-intelligence.git

/plugin install code-ownership-intelligence@code-ownership-intelligence
```

That's the whole install step, from any machine — no cloning, no manual copying.

If you ever move this to a different remote (GitLab, a different GitHub org, etc.), the same pattern applies with that host's URL:

```bash
cd "/path/to/code-ownership-intelligence"
git remote set-url origin <new-repo-url>   # or: git remote add origin <new-repo-url>
git push -u origin main
```

That's the "upload it, connect to it from any Claude" setup — once it's pushed, `/plugin marketplace add <repo>` plus `/plugin install ...` is the whole install step, from any machine.

### C4. Turn it on automatically for a team project

To make a project auto-enable this plugin for everyone who opens it (instead of each person running `/plugin install` themselves), add this to that project's `.claude/settings.json`:

```json
{
  "enabledPlugins": {
    "code-ownership-intelligence@code-ownership-intelligence": true
  }
}
```

Teammates still need the marketplace added once (`/plugin marketplace add ...`); after that, opening the project turns the plugin on for them automatically.

## After installing (any option)

Nothing else to configure. The skill auto-triggers based on its description — asking "explain this before you touch it" or "what did you just change" is enough. You can also invoke it by name:

- As a loose skill (Option A/B): `/code-ownership-intelligence`
- As an installed plugin (Option C): `/code-ownership-intelligence:code-ownership-intelligence`

## Updating

- Option A/B: re-copy the folder over the old one.
- Option C: bump `version` in `.claude-plugin/plugin.json` and `.claude-plugin/marketplace.json`, push, then teammates run `/plugin marketplace update code-ownership-intelligence` followed by `/plugin install ...` again (or just wait for their next auto-refresh).

## Repo layout

```
code-ownership-intelligence/
├── .claude-plugin/
│   ├── plugin.json          # plugin manifest
│   └── marketplace.json     # makes this repo installable via /plugin marketplace add
├── skills/
│   └── code-ownership-intelligence/
│       ├── SKILL.md         # the actual skill — copy just this folder for Option A/B
│       └── reference/       # research method, before/after flows, output templates
└── README.md                 # this file
```
