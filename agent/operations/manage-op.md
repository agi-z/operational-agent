# manage-op

## Purpose

Edit, rename, or delete an existing operation in this agent. Also the route for pulling updates from `.claude/agent-templates/operations/<op>.md` into an existing agent (no auto-sync in v1).

## Invocation

- `/agent manage-op <op-name>` — default agent.
- `/agent <name>-agent/ manage-op <op-name>`.

## Inputs

- The target op file.
- User-stated change OR a template-update intent.

## References

- `<agent>/operations/<op-name>.md`
- `.claude/agent-templates/operations/<op-name>.md` (for template-sync flow).
- `<agent>/operations/INDEX.md`.

## Outputs

- Updated `<op-name>.md` (or renamed / removed). Updated `operations/INDEX.md`.

## Steps

1. Resolve the op file.
2. Ask: edit section | rename | delete | sync-from-template.
3. **Edit section.** Walk through; show old → new before write.
4. **Rename.** New filename; update INDEX; warn that operation-log path `logs/<old-name>/` will be left in place (rename or migrate manually if desired).
5. **Delete.** Confirm twice. Remove file. Update INDEX. Note in log.
6. **Sync-from-template.** Read `.claude/agent-templates/operations/<op-name>.md`. Diff against current. Present to user for accept-all, cherry-pick, or abort.
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
