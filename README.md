# ai-all

Shared AI guidance for Cursor: rules, skills, and prompts you can reuse across repositories.

This repo stores assets under `cursor/` (no leading dot) so they can be versioned here and copied or linked into each project's `.cursor/` folder, where Cursor loads them.

## What's in this repo

```
cursor/
├── rules/                          # Persistent agent instructions
│   └── senior-fullstack-developer.mdc
└── skills/                         # Task-specific workflows
    └── three-layer-api/
        └── SKILL.md
```

| Asset | Purpose | When it applies |
|-------|---------|-----------------|
| **Rules** (`.mdc`) | Standing conventions — coding style, principles, file-specific patterns | Automatically, based on `alwaysApply` or open file globs |
| **Skills** (`SKILL.md`) | Step-by-step workflows for specialized tasks | When the agent matches the skill description, or when you `@`-mention the skill |

**Rules** are always-on context. **Skills** are deeper playbooks the agent pulls in for specific jobs (adding an API endpoint, running a review checklist, etc.).

---

## Quick start: use in a project

### 1. Copy into your repo

From your target project root:

```bash
# Create Cursor folders if they don't exist
mkdir -p .cursor/rules .cursor/skills

# Copy everything from this repo
cp -r /path/to/ai-all/cursor/rules/* .cursor/rules/
cp -r /path/to/ai-all/cursor/skills/* .cursor/skills/
```

On Windows (PowerShell):

```powershell
New-Item -ItemType Directory -Force -Path .cursor\rules, .cursor\skills
Copy-Item -Recurse -Force path\to\ai-all\cursor\rules\* .cursor\rules\
Copy-Item -Recurse -Force path\to\ai-all\cursor\skills\* .cursor\skills\
```

Open the project in Cursor. Rules with `alwaysApply: true` take effect immediately; file-scoped rules activate when you work on matching files. Skills are discovered from `.cursor/skills/*/SKILL.md`.

### 2. Pick only what you need

You do not have to copy the whole tree. Copy individual files or skill folders:

```bash
cp /path/to/ai-all/cursor/rules/senior-fullstack-developer.mdc .cursor/rules/
cp -r /path/to/ai-all/cursor/skills/three-layer-api .cursor/skills/
```

### 3. Keep in sync (optional)

**Git submodule** — pin a version of this repo inside your project:

```bash
git submodule add https://github.com/<you>/ai-all vendor/ai-all
cp -r vendor/ai-all/cursor/rules/* .cursor/rules/
cp -r vendor/ai-all/cursor/skills/* .cursor/skills/
```

**Symlink** — live link to a local clone (handy when iterating on shared guidance):

```bash
ln -s /path/to/ai-all/cursor/rules/senior-fullstack-developer.mdc .cursor/rules/
```

Re-copy or update the submodule when you pull new changes from `ai-all`.

---

## Rules

Rules live in `.cursor/rules/` as `.mdc` files with YAML frontmatter.

```markdown
---
description: Brief summary shown in the rule picker
globs: **/*.ts          # optional — apply when these files are open
alwaysApply: false      # true = every conversation
---

# Rule title

Your instructions here.
```

| Field | Meaning |
|-------|---------|
| `description` | Short label for the rule |
| `alwaysApply` | `true` — included in every chat; `false` — only when globs match or you enable it |
| `globs` | File pattern (e.g. `**/*.tsx`, `backend/**/*.py`) |

**Example in this repo:** `cursor/rules/senior-fullstack-developer.mdc` sets `alwaysApply: true` for general engineering principles.

Tips:

- One concern per rule; keep each file short and actionable.
- Use `alwaysApply` sparingly — too many global rules compete for context.
- Prefer globs for language- or folder-specific standards.

---

## Skills

Skills live in `.cursor/skills/<skill-name>/SKILL.md`. Each skill is a directory; only `SKILL.md` is required. Optional extras: `reference.md`, `examples.md`, `scripts/`.

```markdown
---
name: my-skill-name
description: What it does and when to use it. Include trigger terms the agent should match.
---

# My Skill

Step-by-step instructions for the agent.
```

| Location | Scope |
|----------|-------|
| `.cursor/skills/` in a repo | Shared with everyone who clones the project |
| `~/.cursor/skills/` on your machine | Personal — available in all your projects |

Do **not** put custom skills in `~/.cursor/skills-cursor/` — that path is reserved for Cursor built-ins.

**Example in this repo:** `cursor/skills/three-layer-api/` documents the Express three-layer API pattern and when to use it (new endpoints, controllers, validators, etc.).

To use a skill explicitly, reference it in chat (e.g. `@three-layer-api`) or describe the task so the agent matches the skill's `description`.

---

## Add your own

**New rule** — create `.cursor/rules/my-rule.mdc` with frontmatter and markdown body. Set `alwaysApply` or `globs` for scope.

**New skill** — create `.cursor/skills/my-skill/SKILL.md` with `name` and a specific third-person `description` (what it does + when to trigger). Keep `SKILL.md` under ~500 lines; move long reference material to sibling files.

When contributing back to `ai-all`, add files under `cursor/rules/` or `cursor/skills/<name>/` in this repository (still without the leading dot), then copy or sync into target projects as above.

---

## Included assets

| Path | Type | Summary |
|------|------|---------|
| `cursor/rules/senior-fullstack-developer.mdc` | Rule | Correctness, clarity, minimal diffs, match existing style |
| `cursor/skills/three-layer-api/SKILL.md` | Skill | Express + TypeScript three-layer API architecture for `api_3layer_v1` |
