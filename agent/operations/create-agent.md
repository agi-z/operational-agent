# create-agent

## Purpose

Bootstrap a new agent in this workspace: create the agent folder, elicit identity, copy all 10 default operation templates into the agent.

## Invocation

- `/agent create-agent` — interactive. Must be invoked at workspace root (rejected from inside an existing agent folder).
- Args: none. Name and identity gathered through dialogue.

## Inputs

- User-supplied agent name and identity content.
- The 10 default operation templates at `.claude/agent-templates/operations/`.

## References

- `.claude/agent-templates/operations/` — templates copied into the new agent.

## Outputs

- New folder `<name>-agent/` at workspace root (or `agent/` if creating the default).
- `<name>-agent/identity.md`
- `<name>-agent/INDEX.md`
- `<name>-agent/entities/INDEX.md` (empty index scaffold)
- `<name>-agent/references/INDEX.md` (empty index scaffold)
- `<name>-agent/operations/INDEX.md`
- `<name>-agent/operations/<op>.md` × 10 (copies of templates)
- `<name>-agent/operations/logs/INDEX.md` (empty)
- `<name>-agent/pending/INDEX.md` (empty)
- New folder `<name>-agent-output/` (or `agent-output/`) with `README.md` declaring the YYMMDD-N convention and `INDEX.md`.
- Root `INDEX.md` updated to list the new agent and its output folder.

## Steps

1. **Verify invocation context.** If invoked from inside an existing agent folder, reject: "create-agent must be run at workspace root." Stop.
2. **Elicit name.** Ask: "What is the name of this agent? (e.g., `sales`, `compliance`. The bare default agent uses `agent` — pick a different name if `agent/` already exists.)"
   - Reject the literal name `agent` if `agent/` already exists.
   - Reject names already ending in `-agent` (avoid `sales-agent-agent/`).
   - Apply suffix: `sales` → folder `sales-agent/`. (Bare default agent — name `agent` and no folder yet — uses folder `agent/`.)
3. **Choose elicitation depth.** Ask: "Quick (3 fields: purpose + professional lens + one-line responsibilities — defer entities/references) or Deep (full identity + seed entities/references)?" Default: Quick for non-developer attendees.
4. **Quick path.** Gather:
   - Purpose (one paragraph)
   - Professional lens (one sentence — the perspective this agent reasons through)
   - Responsibilities (3-5 bullets)
   - Generate Capabilities and Constraints with a sensible default and ask the user to confirm/edit.
5. **Deep path.** Same as Quick plus walk through Capabilities, Constraints, and seed entries for entities/references the user wants pre-loaded.
6. **Write `identity.md`** using the template in this op (see below). Populate the sibling-file lookup table.
7. **Create folder scaffold.** Make `entities/`, `references/`, `operations/`, `operations/logs/`, `pending/`; write empty `INDEX.md` in each.
8. **Copy operation templates.** Copy every file from `.claude/agent-templates/operations/` to `<agent>/operations/`. Light per-agent tuning (e.g., changing a sample example) is optional — defaults are fine.
9. **Write `operations/INDEX.md`** listing all 10 ops with one-line descriptions.
10. **Create paired output folder** `<name>-agent-output/` (or `agent-output/`) with `README.md` and empty `INDEX.md`.
11. **Write `<agent>/INDEX.md`** — root index for the agent folder, listing identity.md and the sub-folders.
12. **Update root `INDEX.md`** to include the new agent and its output folder.
13. **Confirm to user** with a summary and the next-step hint (`/agent <name>-agent/` to converse).
14. **Log the invocation.**

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
  - <agent>/operations/ (10 ops copied)
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
| Available operations | `<agent>/operations/INDEX.md` |
| Pending verifications | `<agent>/pending/INDEX.md` |
| Generative outputs | `<agent>-output/INDEX.md` |
```
