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

- **`/agent create-agent`** is always valid at the workspace root, even when no agent yet exists. It is the only operation that may run without an existing agent. When invoked, read it from the built-in location `.claude/agent-operations/create-agent.md` and follow it. (This operation creates the agent's folder skeleton; built-in operations remain shared at the workspace level — they are NOT copied into the new agent.) When the resolved op is `create-agent`, skip steps 1–3 of the Dispatch procedure (workspace context is already loaded; no agent identity exists yet; no pending state to check); resolve and execute the op file directly from `.claude/agent-operations/create-agent.md`.

- **`/agent create-agent` invoked from inside an existing agent folder** is rejected. Only valid at workspace root.

## Pending file shape

Each entry at `<agent>/pending/YYMMDD-N-<short>.md` carries this frontmatter:

```yaml
---
source: <provenance — source path, op invocation, or "user dialog">
op: <op that parked the entry>
written-at: <YYYY-MM-DD HH:MM>
target: <intended destination path inside <agent>/ if confirmed>
question: <one-line judgment the user must resolve>
---

<body — the unverified content as it would be written if confirmed>
```

Every op that writes a pending file MUST also append a row to `<agent>/pending/INDEX.md` (`path | <question>`). The session-start pending-check reads this INDEX.

## Dispatch procedure

Once agent and operation are resolved:

0. **Verify the resolved agent folder exists** and passes the agent-folder recognition rule. If not, emit the no-agent-at-path message from § Agent folder recognition and stop. (Exception: `create-agent` — see § Bootstrap handling.)
1. **Load workspace context.** `CLAUDE.md` is already loaded by Claude Code.
2. **Load the agent's identity.** Read `<agent>/identity.md` in full.
3. **Pending check (converse mode only, session start).** If the resolved operation is `converse` AND this is the first `/agent` converse invocation in this Claude Code session for this agent folder (subsequent converse invocations in the same session skip this check), read `<agent>/pending/INDEX.md`. If it lists one or more pending items, surface the count and offer to resolve them before proceeding:

   > You have N pending item(s) awaiting verification. Resolve now, or continue to converse? [resolve | continue]

   If the user opts to resolve, walk pending items one at a time; for each, present the parked content and ask the user to confirm / amend / drop. Confirmed items move to their target location in `<agent>/`; dropped items are deleted. Then proceed to the requested operation.

4. **Resolve the operation.** Operations are resolved in this order:
   1. **Built-in** at `.claude/agent-operations/<op>.md` — shared workspace-level operations. Checked first.
   2. **Per-agent** at `<agent>/operations/<op>.md` — user-defined operations specific to this agent.

   Read the resolved op file in full. (Built-ins are the canonical surface; per-agent ops extend rather than override — see "Unknown operation" below for the discovery flow.)
5. **Load the operation's References.** For each entry in the operation's References section: if the entry targets a folder INDEX, read the INDEX first and navigate from there; if a single file, read it directly.
6. **Read the operation's Inputs.** Apply user-supplied args per the operation's Invocation section. If the operation expects args the user did not supply, prompt for them.
7. **Execute the operation's Steps.**
8. **Apply the grounding rule** as the operation's Grounding-rule application section directs (writes into `<agent>/` only; `<agent>-output/` is exempt).
9. **Log the invocation** per the operation's Logging section: write an entry at `<agent>/operations/logs/<op>/YYMMDD-N-<short>.md` with the standard frontmatter (timestamp, input summary, output references, pending items created, outcome).
   - **Picking `N`:** scan `<agent>/operations/logs/<op>/` for files with today's `YYMMDD` prefix; use `max(existing N) + 1`, or `1` if none. (Same rule applies to every `YYMMDD-N-...` path in the workspace — entity logs, pending items, `<agent>-output/` sub-folders.)
   - **Lazy INDEX.md.** If `<agent>/operations/logs/<op>/INDEX.md` does not exist (this is the first invocation logged for this op on this agent), create it with the standard two-column header before writing the entry. Append the new entry row.

## Unknown operation

If the operation name resolves to neither `.claude/agent-operations/<op>.md` (built-in) nor `<agent>/operations/<op>.md` (per-agent):

1. Read `.claude/agent-operations/INDEX.md` (built-ins).
2. Read `<agent>/operations/INDEX.md` (per-agent, if it exists).
3. **Heuristic:** if `<op>` matches the pattern `*-agent` (looks like a missing-slash agent folder name), suggest the corrected form before listing operations: `Did you mean \`/agent <op>/\`?`. Then proceed with the unknown-operation listing below.
4. Respond:

   > Unknown operation `<op>` for agent `<agent>/`.
   > Built-in operations: <list from .claude/agent-operations/INDEX.md>
   > Per-agent operations: <list from <agent>/operations/INDEX.md, or "(none)">.
   > To add a new operation, run `/agent <agent>/ create-op`.

…and stop.

## Notes on agent = Claude

The "agent" is **you** — Claude — wearing the hat defined by the targeted agent's `identity.md`. There is no separate AI. Different agent folders are different hats for different contexts.
