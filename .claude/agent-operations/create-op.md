# create-op

## Purpose

Add a new user-defined operation to this agent. Operations are the open extensible surface — adding capability = adding an op file, not authoring a new slash command.

## Invocation

- `/agent create-op [op-name]` — interactive.
- `/agent <name>-agent/ create-op [op-name]`.

## Inputs

- Op name and per-section content gathered through dialog.

## References

- `<agent>/operations/INDEX.md` — per-agent ops; check for duplicates.
- `.claude/agent-operations/INDEX.md` — built-ins; check that the proposed name doesn't shadow one (built-ins resolve first; a per-agent same-name op would be unreachable).
- `<agent>/identity.md`.
- `.claude/agent-operations/create-op.md` — this file, used as the schema example.

## Outputs

- `<agent>/operations/<op-name>.md` — a new operation file following the locked schema.
- Updated `<agent>/operations/INDEX.md`.

## Steps

1. **Elicit op name.** Reject if (a) a built-in op already uses that name (the built-in would shadow it; the user's per-agent op would never run) — check by reading `.claude/agent-operations/INDEX.md`; if `<op-name>.md` appears in the catalog, reject — or (b) a per-agent op file already exists at that name in `<agent>/operations/`.
2. **Walk the schema with the user.** One section at a time, in this order:
   - Purpose (one line)
   - Invocation (args)
   - Inputs (what it reads)
   - **References** — for each, ask: single file or folder INDEX? (Folder INDEX is preferred for hierarchical rule sets.)
   - Outputs (what it writes, and where; remind: `<agent>/` is grounded; `<agent>-output/` is generative-exempt)
   - Steps (the procedure)
   - **Grounding-rule application section content.** If the new op writes into `<agent>/`, the user must classify direct / direct-inference / judgment in their op file's Grounding-rule application section. If the new op writes ONLY to `<agent>-output/`, the user must state the exemption explicitly (e.g., "writes to `<agent>-output/`; grounding rule exempt"). Silence on grounding is ambiguous; make explicit either application or exemption.
   - Logging (always)
3. **Suggest defaults** at each step based on similar existing ops.
4. **Show the draft op file** for user confirmation before write.
5. **Write the file** and update `operations/INDEX.md` with a one-line description.
6. **Confirm to user.** Summarize the created op and offer next-step hint: `/agent <op-name>` to invoke it.
7. **Log.**

## Grounding-rule application

- Op-file content is user-authored — direct ground-truth.
- Suggested defaults require user confirmation before they go in.

## Logging

`<agent>/operations/logs/create-op/YYMMDD-N-<op-name>.md`:

```yaml
---
op: create-op
timestamp: YYYY-MM-DDTHH:MM
input-summary: created op <op-name>
outputs:
  - <agent>/operations/<op-name>.md
outcome: ok
---
```
