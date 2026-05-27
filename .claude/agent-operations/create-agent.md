# create-agent

## Purpose

Bootstrap a new agent in this workspace: create the agent folder skeleton (identity, sub-folders, INDEX files) and the paired output folder. Built-in operations are NOT copied — they live at `.claude/agent-operations/` and are shared.

## Invocation

- `/agent create-agent` — interactive. Must be invoked at workspace root (rejected from inside an existing agent folder).
- Args: none. Name and identity gathered through dialogue.

## Inputs

- User-supplied agent name and identity content.

## References

- `.claude/agent-operations/INDEX.md` — to confirm the catalogue of built-in operations the new agent will have access to.

## Outputs

- New folder `<name>-agent/` at workspace root (or `agent/` if creating the default).
- `<name>-agent/identity.md`
- `<name>-agent/INDEX.md`
- `<name>-agent/entities/INDEX.md` (empty scaffold)
- `<name>-agent/references/INDEX.md` (empty scaffold)
- `<name>-agent/operations/INDEX.md` (empty — for future user-defined ops only)
- `<name>-agent/operations/logs/INDEX.md` (empty)
- `<name>-agent/pending/INDEX.md` (empty)
- New folder `<name>-agent-output/` (or `agent-output/`) with `README.md` declaring the YYMMDD-N convention and `INDEX.md`.
- Root `INDEX.md` updated to list the new agent and its output folder.

## Steps

1. **Verify invocation context.** If invoked from inside an existing agent folder, reject: "create-agent must be run at workspace root." Stop.
2. **Elicit name.** Ask: "What is the name of this agent? (e.g., `sales`, `compliance`.)"
   - **Reject the literal name `agent` unconditionally.** The default `agent/` folder is only chosen when the user explicitly elects "default unnamed agent" during bootstrap (see step below), NOT via a typed name. A user typing `agent` is always rejected; ask for a different name.
   - Reject names already ending in `-agent` (avoid `sales-agent-agent/`).
   - Apply suffix: `sales` → folder `sales-agent/`.
3. **First-agent bootstrap.** If this is the first agent in the workspace (no `agent/` and no `*-agent/` folder exists yet), prompt: "Create the default unnamed agent (folder `agent/`), or a named agent (folder `<name>-agent/`)?" If user picks default, scaffold `agent/` (no name required). If user picks named, request the name (which then runs through the suffix and reserved-name rules).
4. **Choose elicitation depth.** Ask: "Quick (3 fields: purpose + professional lens + one-line responsibilities — defer entities/references) or Deep (full identity + seed entities/references)?" Default: Quick for non-developer attendees.
5. **Quick path.** Gather:
   - Purpose (one paragraph)
   - Professional lens (one sentence — the perspective this agent reasons through)
   - Responsibilities (3-5 bullets)
   - Generate Capabilities and Constraints with a sensible default and ask the user to confirm/edit.
6. **Deep path.** Same as Quick plus walk through Capabilities, Constraints, and seed entries for entities/references the user wants pre-loaded.
7. **Write `identity.md`** using the template at the bottom of this file. Populate the sibling-file lookup table.
8. **Create folder scaffold.** Make `entities/`, `references/`, `operations/`, `operations/logs/`, `pending/`; write empty `INDEX.md` in each.
9. **Write `<agent>/operations/INDEX.md`** as an empty scaffold with a header noting "This index lists user-defined operations for this agent only. Built-in operations are shared at `.claude/agent-operations/INDEX.md`."
10. **Create paired output folder** `<name>-agent-output/` (or `agent-output/`) with `README.md` and empty `INDEX.md`.
11. **Write `<agent>/INDEX.md`** — root index for the agent folder, listing identity.md and the sub-folders.
12. **Update root `INDEX.md`** to include the new agent and its output folder.
13. **Confirm to user** with a summary, the next-step hint (`/agent <name>-agent/` to converse), and a note that all 10 built-in operations are available immediately without per-agent setup.
14. **Log the invocation** AFTER the agent folder scaffold is created (this is the first entry written into the new folder's operations/logs/). Create `<agent>/operations/logs/create-agent/INDEX.md` lazily, then append the log entry per dispatcher § Dispatch procedure step 9.

## Grounding-rule application

- Identity fields are user-supplied — direct ground-truth. Write through.
- Generated defaults (e.g., suggested Capabilities) require user confirmation before write — treated as judgment-requiring inference.
- Folder scaffolding is structural, not factual content; no grounding test needed.

## Logging

`<agent>/operations/logs/create-agent/YYMMDD-N-bootstrap.md`:

```yaml
---
op: create-agent
timestamp: YYYY-MM-DDTHH:MM
input-summary: bootstrap agent <name>; <Quick|Deep> path
outputs:
  - <agent>/identity.md
  - <agent>/ folder scaffold
  - <agent>-output/
outcome: ok
---
```

## identity.md template

```markdown
---
agent-name: <name>
created-at: YYYY-MM-DD
---

# <Name> Agent — Identity

## Purpose

<One paragraph: what this agent exists to do.>

## Responsibilities

- <Bullet>
- <Bullet>

## Capabilities

- <Bullet — what the agent can do>
- <Bullet>

## Constraints

- <Bullet — what the agent must not do; boundary>
- <Bullet>

## Professional lens

<One sentence: the perspective this agent reasons through.
This is the "intent shapes extraction" hook.>

## Sibling-file lookup table

| Look for | Path |
|---|---|
| Tracked entities | `<agent>/entities/INDEX.md` |
| Reference material | `<agent>/references/INDEX.md` |
| User-defined operations (this agent only) | `<agent>/operations/INDEX.md` |
| Built-in operations (shared workspace-wide) | `.claude/agent-operations/INDEX.md` |
| Pending verifications | `<agent>/pending/INDEX.md` |
| Generative outputs | `<agent>-output/INDEX.md` |
```
