# create-op

## Purpose

Add a new user-defined operation to this agent. Operations are the open extensible surface — adding capability = adding an op file, not authoring a new slash command.

## Invocation

- `/agent create-op [op-name]` — interactive.
- `/agent <name>-agent/ create-op [op-name]`.

## Inputs

- Op name and per-section content gathered through dialog.

## References

- `<agent>/operations/INDEX.md` — to check for duplicates.
- `<agent>/identity.md`.
- `.claude/agent-templates/operations/create-op.md` — as the schema example.

## Outputs

- `<agent>/operations/<op-name>.md` — a new operation file following the locked schema.
- Updated `<agent>/operations/INDEX.md`.

## Steps

1. **Elicit op name.** Reject if a file already exists at that name.
2. **Walk the schema with the user.** One section at a time, in this order:
   - Purpose (one line)
   - Invocation (args)
   - Inputs (what it reads)
   - **References** — for each, ask: single file or folder INDEX? (Folder INDEX is preferred for hierarchical rule sets.)
   - Outputs (what it writes, and where; remind: `<agent>/` is grounded; `<agent>-output/` is generative-exempt)
   - Steps (the procedure)
   - Grounding-rule application (only if it writes into `<agent>/`)
   - Logging (always)
3. **Suggest defaults** at each step based on similar existing ops.
4. **Show the draft op file** for user confirmation before write.
5. **Write the file** and update `operations/INDEX.md` with a one-line description.
6. **Log.**

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
