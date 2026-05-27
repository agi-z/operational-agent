# manage-entity

## Purpose

Make **structural** changes to an existing entity: rename, move (re-parent), edit profile fields, archive, delete. NOT for routine log additions — those come through `ingest` or `converse`.

## Invocation

- `/agent manage-entity <entity-slug>` — default agent.
- `/agent <name>-agent/ manage-entity <entity-slug>`.
- Args: `<entity-slug>` required; if absent, list entities and prompt.

## Inputs

- `<agent>/entities/<slug>/profile.md` and surrounding files.
- User-stated change.

## References

- `<agent>/entities/INDEX.md`
- Target entity's `INDEX.md`, `profile.md`.

## Outputs

- Updated entity profile, INDEX entries, and (for renames/moves) the renamed folder. For archive/delete, the entity folder is moved or removed.

## Steps

1. Resolve the entity. If slug ambiguous, list candidates and prompt.
2. Ask what kind of change: rename | re-parent | edit profile | archive | delete.
3. **Rename.** New slug; move folder; update parent `entities/INDEX.md`; rewrite any internal cross-refs.
4. **Re-parent.** Move folder to new parent; update both old and new parent INDEX.md.
5. **Edit profile.** Walk through `profile.md` sections; show old → new; write.
6. **Archive.** Move to `<agent>/entities/_archive/<slug>/`; remove from active INDEX; note in log.
7. **Delete.** Confirm twice. Remove folder. Note in log.
8. Update relevant INDEX.md files.
9. Log.

## Grounding-rule application

- Profile edits — user-confirmed → direct ground-truth.
- Structural changes (rename/move/delete) are not factual content; no grounding test.

## Logging

`<agent>/operations/logs/manage-entity/YYMMDD-N-<slug>-<action>.md`:

```yaml
---
op: manage-entity
timestamp: YYYY-MM-DDTHH:MM
input-summary: <action> on entity <slug>
outputs:
  - <paths affected>
outcome: ok
---
```
