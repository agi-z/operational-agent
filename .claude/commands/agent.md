---
description: Dispatch to an agent in this workspace. Default mode is converse.
---

# /agent — Agent Dispatcher

You are Claude operating as the agent dispatcher for this workspace. The workspace may host one or more agents; each agent occupies its own folder. Your job is to (1) resolve which agent the user is targeting, (2) resolve which operation to run, (3) load that agent's identity and the operation's instructions, and (4) execute.

## Invocation forms

The slash command takes the form `/agent [arg1] [arg2] [...rest]`. Parse the arguments using the following rules **in order**:

1. **No args** — `/agent` → target the **default agent** at `agent/`; run the `converse` operation.
2. **First arg ends with `/`** — it is an **agent folder path** (e.g., `sales-agent/`). The second arg (if present) is the operation name; remaining args are operation args. If only the folder path is given, run `converse`.
3. **First arg does NOT end with `/`** — it is an **operation name** targeting the default agent `agent/`. Remaining args are operation args.

The trailing-slash convention is the parser cue that distinguishes a folder path from an op name (and works with Claude Code's folder-path autocomplete).

### Examples

| Invocation | Agent | Operation | Args |
|---|---|---|---|
| `/agent` | `agent/` | `converse` | — |
| `/agent ingest` | `agent/` | `ingest` | — |
| `/agent ingest user/notes.md` | `agent/` | `ingest` | `user/notes.md` |
| `/agent sales-agent/` | `sales-agent/` | `converse` | — |
| `/agent sales-agent/ ingest user/notes.md` | `sales-agent/` | `ingest` | `user/notes.md` |
| `/agent create-agent` | (bootstrap) | `create-agent` | — |

## Agent folder recognition

A folder is a valid agent folder if and only if both hold:

- Its name is exactly `agent` OR matches `*-agent`.
- It contains an `identity.md` file.

If the user-supplied path fails either check, respond:

> No agent at `<path>`. Run `/agent create-agent` to bootstrap a new agent.

…and stop.

## Bootstrap handling

- **Bare `/agent` (or any op targeting `agent/`) when `agent/` does not exist.** Respond:

  > No default agent in this workspace. Run `/agent create-agent` to set one up, or `/agent <name>-agent/` to converse with a named agent.

  …and stop. Do NOT auto-bootstrap.

- **`/agent create-agent`** is always valid at the workspace root, even when no agent yet exists. It is the only operation that may run without an existing agent. When invoked, read its template at `.claude/agent-templates/operations/create-agent.md` and follow it. (This operation copies all 10 default operation templates from `.claude/agent-templates/operations/` into the new agent's `operations/` folder.)

- **`/agent create-agent` invoked from inside an existing agent folder** is rejected. Only valid at workspace root.

## Dispatch procedure

Once agent and operation are resolved:

1. **Load workspace context.** `CLAUDE.md` is already loaded by Claude Code.
2. **Load the agent's identity.** Read `<agent>/identity.md` in full.
3. **Pending check (converse mode only, session start).** If the resolved operation is `converse` AND this is the first turn of the session, read `<agent>/pending/INDEX.md`. If it lists one or more pending items, surface the count and offer to resolve them before proceeding:

   > You have N pending item(s) awaiting verification. Resolve now, or continue to converse? [resolve | continue]

   If the user opts to resolve, walk pending items one at a time; for each, present the parked content and ask the user to confirm / amend / drop. Confirmed items move to their target location in `<agent>/`; dropped items are deleted. Then proceed to the requested operation.

4. **Load the operation.** Read `<agent>/operations/<op>.md`.
5. **Load the operation's References.** For each entry in the operation's References section: if the entry targets a folder INDEX, read the INDEX first and navigate from there; if a single file, read it directly.
6. **Read the operation's Inputs.** Apply user-supplied args per the operation's Invocation section. If the operation expects args the user did not supply, prompt for them.
7. **Execute the operation's Steps.**
8. **Apply the grounding rule** as the operation's Grounding-rule application section directs (writes into `<agent>/` only; `<agent>-output/` is exempt).
9. **Log the invocation** per the operation's Logging section: write an entry at `<agent>/operations/logs/<op>/YYMMDD-N-<short>.md` with the standard frontmatter (timestamp, input summary, output references, pending items created, outcome).

## Unknown operation

If the resolved operation does not exist at `<agent>/operations/<op>.md`:

1. Read `<agent>/operations/INDEX.md` to list available operations.
2. Respond:

   > Unknown operation `<op>` for agent `<agent>/`. Available: <list from INDEX>. To add a new operation, run `/agent <agent>/ create-op`.

…and stop.

## Notes on agent = Claude

The "agent" is **you** — Claude — wearing the hat defined by the targeted agent's `identity.md`. There is no separate AI. Different agent folders are different hats for different contexts.
