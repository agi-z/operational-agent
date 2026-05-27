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

0. **Target check.** If the entity slug does not resolve to an existing folder under `<agent>/entities/`, respond: 'No such entity `<slug>`; run `/agent create-entity` to create one.' Stop.
1. Resolve the entity. If slug ambiguous, list candidates and prompt.
2. Ask what kind of change: rename | re-parent | edit profile | archive | delete.
3. **Rename.** New slug; move folder; update parent `entities/INDEX.md`. **Cross-ref rewrite scope.** On rename, rewrite cross-references in: `<agent>/<entity>/profile.md` and sub-entity profile.md files (relative paths and parent references); references in `<agent>/references/`; pending items targeting this entity. **Do NOT rewrite:** entity `log/` entries (dated historical record), operation logs under `<agent>/operations/logs/`, `<agent>-output/` sub-folders (frozen snapshots). For each in-scope hit, present old → new and confirm before editing.
4. **Re-parent.** Move folder to new parent; update both old and new parent INDEX.md.
5. **Edit profile.** Walk through `profile.md` sections; show old → new; write.
6. **Archive.** Move to `<agent>/entities/_archive/<slug>/`; remove from active INDEX; note in log.
7. **Delete.** Confirm twice. Remove folder. Note in log.
8. Update relevant INDEX.md files.
9. Log.

## Grounding-rule application

- **Profile-content edits reapply the grounding test.** Structural edits (rename, move/re-parent, archive, delete) are exempt. Factual-content edits to `profile.md` are NOT exempt: classify each edit as direct / direct-inference / judgment. Judgment-requiring content goes through the pending-park flow (frontmatter per `agent.md` § Pending file shape; update `pending/INDEX.md`).

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
