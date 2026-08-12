# ai-all

Shared Cursor guidance — **rules**, **skills**, and **agents** — versioned here under `cursor/` and copied into each project's `.cursor/` folder.

## What's in this repo

```
cursor/
├── rules/                              # Always-on or file-scoped instructions
│   └── senior-fullstack-developer.mdc
├── skills/                             # Task playbooks (on demand)
│   ├── senior-front-end-developer/
│   ├── senior-back-end-developer/
│   └── three-layer-api/
└── agents/                             # Optional — add subagent definitions here
```

| Asset | Purpose | When it applies |
|-------|---------|-----------------|
| **Rules** | Standing conventions and principles | Automatically (`alwaysApply`) or when matching files are open (`globs`) |
| **Skills** | Step-by-step workflows for specialized tasks | Agent matches the `description`, or you `@`-mention the skill |
| **Agents** | Specialized subagents with focused system prompts | Parent agent delegates by `description`, or you invoke by name |

**Rules** = persistent context. **Skills** = deep playbooks for specific jobs. **Agents** = isolated specialists (review, debug, explore) with their own prompt.

---

## Install in a project

```bash
mkdir -p .cursor/rules .cursor/skills .cursor/agents
cp -r /path/to/ai-all/cursor/rules/*    .cursor/rules/
cp -r /path/to/ai-all/cursor/skills/*   .cursor/skills/
cp -r /path/to/ai-all/cursor/agents/*   .cursor/agents/   # if present
```

Windows (PowerShell):

```powershell
New-Item -ItemType Directory -Force -Path .cursor\rules, .cursor\skills, .cursor\agents
Copy-Item -Recurse -Force path\to\ai-all\cursor\rules\*  .cursor\rules\
Copy-Item -Recurse -Force path\to\ai-all\cursor\skills\* .cursor\skills\
Copy-Item -Recurse -Force path\to\ai-all\cursor\agents\* .cursor\agents\
```

Copy only what you need — single files or folders work the same way. Use a git submodule or symlinks to stay in sync when iterating on shared guidance.

---

## Rules

**Use:** Open the project in Cursor. Rules with `alwaysApply: true` apply to every chat; others apply when you open files matching `globs`. Enable or disable rules in **Cursor Settings → Rules**.

**Create:** Add `.cursor/rules/my-rule.mdc`:

```markdown
---
description: Short label for the rule picker
globs: **/*.tsx          # optional — apply when these files are open
alwaysApply: false       # true = every conversation
---

# Rule title

Instructions for the agent.
```

| Field | Meaning |
|-------|---------|
| `description` | Shown in the rule picker |
| `alwaysApply` | `true` — every chat; `false` — globs or manual enable |
| `globs` | File pattern (e.g. `backend/**/*.py`) |

One concern per rule; keep files short. Use `alwaysApply` sparingly.

**Contribute to ai-all:** `cursor/rules/my-rule.mdc` (no leading dot).

---

## Skills

**Use:** Cursor discovers `.cursor/skills/<name>/SKILL.md` automatically. Reference a skill in chat (`@senior-front-end-developer`) or describe the task so the agent matches the skill's `description`.

| Location | Scope |
|----------|-------|
| `.cursor/skills/` in a repo | Shared with the team |
| `~/.cursor/skills/` | Personal, all projects |

Do **not** put custom skills in `~/.cursor/skills-cursor/` — reserved for Cursor built-ins.

**Create:** Add `.cursor/skills/my-skill/SKILL.md`:

```markdown
---
name: my-skill
description: What it does and when to use it. Third person; include trigger terms.
---

# My Skill

Step-by-step instructions for the agent.
```

- `name` — lowercase, hyphens, max 64 chars
- `description` — what + when (agent uses this to decide when to load the skill)
- Optional siblings: `reference.md`, `examples.md`, `scripts/`
- Keep `SKILL.md` under ~500 lines

**Contribute to ai-all:** `cursor/skills/my-skill/SKILL.md`.

---

## Agents

Subagents run in isolated context with a dedicated system prompt. The parent agent can delegate to them automatically based on each agent's `description`.

**Use:** Place agent files in `.cursor/agents/`. Invoke by name in chat or let the parent agent delegate when the task matches the `description`.

| Location | Scope | Priority |
|----------|-------|----------|
| `.cursor/agents/` | Current project | Higher |
| `~/.cursor/agents/` | All projects | Lower |

**Create:** Add `.cursor/agents/my-agent.md`:

```markdown
---
name: my-agent
description: When to delegate — be specific; include "use proactively" to encourage auto-delegation.
---

You are a specialist for [domain]. When invoked:
1. ...
2. ...

Output format: ...
```

- `name` — lowercase letters and hyphens
- `description` — critical for auto-delegation; state expertise and trigger scenarios
- Body — the subagent's system prompt (workflow, checklist, output format)

**Contribute to ai-all:** `cursor/agents/my-agent.md`.

---

## Included assets

| Path | Type | Summary |
|------|------|---------|
| `cursor/rules/senior-fullstack-developer.mdc` | Rule | Correctness, clarity, minimal diffs |
| `cursor/skills/senior-front-end-developer/SKILL.md` | Skill | UI, components, a11y, client performance |
| `cursor/skills/senior-back-end-developer/SKILL.md` | Skill | APIs, services, auth, data layer |
| `cursor/skills/three-layer-api/SKILL.md` | Skill | Express + TypeScript three-layer API |

## How to apply rules, skills and agents in each new project

**User-level** assets work in every project with no copy step. **Project-level** assets apply only when `.cursor/` exists in the open workspace.

| Type | User-level (all projects) | Project-level (per repo) |
|------|---------------------------|--------------------------|
| **Rules** | Cursor Settings → Rules | `.cursor/rules/*.mdc` |
| **Skills** | `~/.cursor/skills/<name>/SKILL.md` | `.cursor/skills/<name>/SKILL.md` |
| **Agents** | `~/.cursor/agents/<name>.md` | `.cursor/agents/<name>.md` |

Do **not** put custom skills in `~/.cursor/skills-cursor/` — reserved for Cursor built-ins.

### Fastest setup for new repos

1. Keep a template `.cursor/` folder (rules, skills, agents) — copy from this repo's `cursor/` tree.
2. Copy or symlink it when you create a project, or start from a boilerplate repo.
3. For team sharing, commit `.cursor/` in the project repo; for personal defaults, use `~/.cursor/skills/` and `~/.cursor/agents/`.


### activate/ deactivate and call the skill

- Add
    - `disable-model-invocation: true` to the yaml part of the SKILL.md file

- to call the skill
    - use the @.cursor/skills/senior-csharp-dotnet-backend/SKILL.md skill to

### comments in a SKILLS.md file

`SKILL.md` is YAML frontmatter + Markdown, so comments work differently in each part.

**In the YAML frontmatter** (between `---` lines):

```yaml
---
name: senior-csharp-dotnet-backend
# This is a YAML comment
description: ...
disable-model-invocation: true
---
```

**In the Markdown body**, use HTML comments (they won’t show when rendered):

```markdown
<!-- This is a comment for humans reading the skill file -->

## Instructions
...
```

Normal Markdown doesn’t have a `#` comment syntax in the body — `#` there means a heading. Use `<!-- ... -->` instead.

**Note:** The agent still sees HTML comments when it reads the skill, so only use them for notes you want available in context, or keep comments out of the skill if you want to save tokens.

## documentation

- cursor cli install
    - [https://cursor.com/docs/cli/installation](https://cursor.com/docs/cli/installation)
- skill md format
    - [https://cursor.com/docs/skills#skillmd-file-format](https://cursor.com/docs/skills#skillmd-file-format)