# Grok — agents and skills

Step-by-step guides for creating an **agent** (Bot) and a **skill** in Grok.

| Concept | What it is |
|---------|------------|
| **Agent (Bot)** | A durable AI teammate with a name, job, conversation, and lasting context |
| **Skill** | Reusable instructions for how to do a specific task — reusable across Bots / sessions |

Official docs: [Grok Bot](https://docs.x.ai/grok-bot/overview) · [Skills & routines](https://docs.x.ai/grok-bot/skills-routines-and-automations) · [Grok Build skills](https://docs.x.ai/build/features/skills-plugins-marketplaces)

---

## Create an agent (Bot)

In Grok Bot, an **agent** is a **Bot**: a named teammate you message like a coworker.

### Prerequisites

- Eligible plan (e.g. SuperGrok Plus/Heavy, or Cursor Pro+/Ultra/Teams)
- [Grok Bot desktop app](https://cursor.com/bot/onboarding) (macOS/Windows) or iOS
- Signed in with your Cursor account

### Steps

1. **Open New chat**
   - Sidebar → **New**, or press `Cmd/Ctrl+N`.

2. **Create the agent**
   - On the **New chat** screen, choose **Create new agent**.
   - Grok opens a Bot named **New Agent**.

3. **Edit the profile**
   - Open **Bot actions → Edit Profile**.
   - Set:
     - **Name** — short, memorable (e.g. `Piper`)
     - **Title / job** — one primary ownership area
     - **Description** — how it should work, plus hard boundaries
     - **Avatar** — optional

4. **Write an operational description**

   Prefer a clear job over “General Helper”:

   ```text
   Own the weekly account-health review. Pull product usage and support
   signals, flag churn or expansion evidence, and produce a linked watch
   list for customer success. Never contact a customer or change an
   account without approval.
   ```

5. **Give a first concrete task**

   Include outcome, sources, constraints, deliverable, and when to stop for review:

   ```text
   Summarize this document in five bullets. List every date, decision,
   and open question in a separate section. Cite the page or section.
   Do not change the source file.
   ```

6. **Grant tool access as needed**
   - When the Bot hits a login wall: open **Agent Computer**, take over, sign in, return control.
   - Or install connectors under **Settings → Plugins**.

7. **Refine lasting rules in the description**
   - Description = durable rules (“Never send external messages without approval.”)
   - Chat messages = one-off tasks (“Draft follow-ups for these twelve accounts.”)

### Tips

- One Bot per distinct goal, tool set, style, or approval boundary (max **50** Bots + group chats).
- **Duplicate** a Bot to reuse a role for a new scope (history is not copied).
- **Hide** instead of delete if you might need it later; deleting removes routines too.
- Later: save a stable workflow as a **skill**, then optionally schedule it as a **routine**.

---

## Create a skill

A **skill** captures when to run, inputs, steps, validation, output format, and what needs approval. Create one after a task works once — then reuse it.

You can create skills in three ways:

### Option A — From conversation (Grok Bot / grok.com)

Best when you just finished a task and want to keep the method.

1. Finish and refine a task with the Bot until the process is reliable.
2. Ask it to save the skill, for example:

   ```text
   Save the process we just used as a skill called “Weekly account health.”
   Include the source systems, risk definitions, output format, and the
   rule that customer contact always requires approval.
   ```

3. Confirm the skill covers:
   1. When to use it  
   2. Required inputs and access  
   3. Sequence of work  
   4. How to validate the result  
   5. What to return  
   6. What requires approval  

4. Invoke later:
   - Type `/` in the composer to pick a skill.
   - Use `@` for Bots, groups, routines, and connectors.

5. If a private skill is missing from `/`:
   - **Settings → Plugins → Yours** → enable it for the current Bot.

On grok.com / mobile chat, you can also describe a skill, upload a file, or ask Grok to save what it learned (built-in **Skill Creator**).

### Option B — Teach by demonstration (Grok Bot)

When **Teach a task** is available:

1. Open a one-to-one Bot conversation and its computer view.
2. Choose **Teach a task**.
3. Describe the result you are about to demonstrate.
4. Perform the workflow once (up to ~10 minutes; no mic audio).
5. Stop recording and review the draft skill.
6. Add decision rules, failure handling, and approval boundaries.
7. Test on a safe example before scheduling.

Avoid exposing secrets during the demo; use secure credential handoff.

### Option C — File-based skill (Grok Build / project)

For coding agents and shared repos, skills are folders with a `SKILL.md`.

**Discovery paths**

| Location | Scope |
|----------|-------|
| `./.grok/skills/<name>/` | This project (walked up to repo root) |
| `~/.grok/skills/<name>/` | All your projects |
| Plugin `skills/` dirs | When the plugin is enabled |
| Extra paths in `~/.grok/config.toml` under `[skills] paths` | Custom |

Claude Code–style skills under `.claude/` / `~/.agents/skills/` are also discovered.

#### Steps

1. **Choose a name** — lowercase letters, digits, hyphens only (e.g. `weekly-account-health`).

2. **Create the folder**

   ```bash
   mkdir -p .grok/skills/weekly-account-health
   ```

3. **Write `SKILL.md`**

   ```markdown
   ---
   name: weekly-account-health
   description: >
     Builds a linked account-health watch list from usage and support
     signals. Use when reviewing churn risk, expansion signals, or when
     the user runs /weekly-account-health.
   ---

   # Weekly account health

   ## When to use
   - Weekly account reviews
   - Churn / expansion triage

   ## Inputs
   - Product usage export or dashboard
   - Support ticket signals

   ## Steps
   1. Pull current usage and support data (do not use stale caches).
   2. Score accounts against the risk definitions below.
   3. Produce a linked watch list.
   4. Stop for approval before any customer contact.

   ## Output
   - Markdown watch list with account links and evidence
   - Section: Decisions needed

   ## Boundaries
   - Never contact a customer without approval
   - Never change account records without approval
   ```

4. **Optional extras** in the same folder: `reference.md`, `examples.md`, `scripts/`.

5. **Use it**
   - Slash command: `/weekly-account-health` (when `user-invocable` is true, the default)
   - Or describe the task so Grok matches the `description`
   - Manage via `/skills` in the Grok Build TUI
   - Scaffold interactively with `/create-skill`

#### Useful frontmatter fields

| Field | Purpose |
|-------|---------|
| `name` | Identifier (directory name if omitted) |
| `description` | What it does + when to trigger (drives auto-invoke) |
| `when-to-use` | Extra trigger phrases |
| `paths` | Globs — skill hidden until a matching file is touched |
| `user-invocable` | Show as slash command (`true` by default) |
| `disable-model-invocation` | Slash-only; no automatic invoke |

Keep `SKILL.md` focused and actionable. Put long reference material in sibling files.

---

## Agent → skill → routine (recommended flow)

1. Create a focused **agent (Bot)** with a clear job and approval boundaries.  
2. Complete the task once; refine until the method is reliable.  
3. Save the method as a **skill**.  
4. Only then ask the Bot to create a **routine** (schedule or event) that runs that skill.

Example routine request:

```text
Every weekday at 8:00 AM, run the Daily customer-risk skill against the
current account list. Post a linked watch list in this conversation.
Do not contact customers. If the source data is unavailable, report the
failure instead of using old data.
```

Always use **Test run** before enabling a routine for real work.

---

## Related (Grok Build coding agents)

For the terminal coding agent, specialized **subagents** live under:

- `.grok/agents/` (project)
- `~/.grok/agents/` (user)

Manage with `/config-agents` (alias `/agents`). Built-in types include `general-purpose`, `explore`, and `plan`. Personas (tone/focus overlays) live under `.grok/personas/` or config `[subagents.personas]`.

See [Subagents](https://docs.x.ai/build/features/subagents).
