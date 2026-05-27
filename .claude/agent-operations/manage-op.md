# manage-op

## Purpose

Edit, rename, archive, or delete a **user-defined** operation in this agent. Built-in operations at `.claude/agent-operations/<op>.md` are NOT editable through this op — they are workspace-level and update via the workspace template repo.

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

0. **Target check.** If the op name does not resolve to an existing file under `<agent>/operations/`, respond: 'No such per-agent op `<op-name>`; run `/agent create-op` to create one.' Stop. (Note: built-in ops are managed via workspace template, not `manage-op` — the built-in-name rejection in step 1 still applies.)
1. **Resolve the op file** at `<agent>/operations/<op-name>.md`. If the name matches a built-in, respond: "`<op-name>` is a built-in operation; manage-op only edits per-agent ops. Built-in updates come through the workspace repo." Stop.
2. If the file does not exist in `<agent>/operations/`, respond: "No user-defined op `<op-name>` for this agent." Stop.
3. Ask: edit section | rename | archive | delete.
4. **Edit section.** Walk through; show old → new before write.
5. **Rename.** New filename; reject if shadowed by a built-in or if a sibling file already uses the new name; update INDEX. On rename, **offer to migrate** `logs/<old-name>/` → `logs/<new-name>/` (default: yes). User can decline to keep the old log folder in place as historical record. Note: because operations are atomic in v1 (no op-to-op invocation), no cross-op references exist to rewrite. Only `<agent>/operations/INDEX.md` needs updating.
6. **Archive.** Move `<agent>/operations/<op-name>.md` → `<agent>/operations/_archive/<op-name>.md`; remove the row from `<agent>/operations/INDEX.md`. Log folder stays at `logs/<op-name>/` as historical record.
7. **Delete.** Confirm twice. Remove file. Update INDEX. Note in log. **Log folder is preserved.** `<agent>/operations/logs/<op-name>/` remains untouched on delete — operation logs are historical record. The op file is removed; the log folder is not.
8. Update INDEX.md.
9. Log.

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
