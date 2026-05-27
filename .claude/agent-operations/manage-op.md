# manage-op

## Purpose

Edit, rename, or delete a **user-defined** operation in this agent. Built-in operations at `.claude/agent-operations/<op>.md` are NOT editable through this op — they are workspace-level and update via the workspace template repo.

## Invocation

- `/agent manage-op <op-name>` — default agent.
- `/agent <name>-agent/ manage-op <op-name>`.

## Inputs

- The target op file at `<agent>/operations/<op-name>.md` (per-agent only).
- User-stated change.

## References

- `<agent>/operations/<op-name>.md` — the target.
- `<agent>/operations/INDEX.md` — to update after rename/delete.
- `.claude/agent-operations/INDEX.md` — to reject conflicts with built-in names on rename.

## Outputs

- Updated `<op-name>.md` (or renamed / removed). Updated `operations/INDEX.md`.

## Steps

1. **Resolve the op file** at `<agent>/operations/<op-name>.md`. If the name matches a built-in, respond: "`<op-name>` is a built-in operation; manage-op only edits per-agent ops. Built-in updates come through the workspace repo." Stop.
2. If the file does not exist in `<agent>/operations/`, respond: "No user-defined op `<op-name>` for this agent." Stop.
3. Ask: edit section | rename | delete.
4. **Edit section.** Walk through; show old → new before write.
5. **Rename.** New filename; reject if shadowed by a built-in or if a sibling file already uses the new name; update INDEX; warn that operation-log path `logs/<old-name>/` will be left in place (rename or migrate manually if desired).
6. **Delete.** Confirm twice. Remove file. Update INDEX. Note in log.
7. Update INDEX.md.
8. Log.

## Grounding-rule application

- Op-file content is user-authored — direct ground-truth.

## Logging

`<agent>/operations/logs/manage-op/YYMMDD-N-<op-name>-<action>.md`:

```yaml
---
op: manage-op
timestamp: YYYY-MM-DDTHH:MM
input-summary: <action> on op <op-name>
outputs:
  - <paths>
outcome: ok
---
```
