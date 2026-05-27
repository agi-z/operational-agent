# manage-reference

## Purpose

Edit, rename, move, archive, or delete an existing reference.

## Invocation

- `/agent manage-reference <slug-or-path>` — default agent.
- `/agent <name>-agent/ manage-reference <slug-or-path>`.

## Inputs

- The target reference file.
- User-stated change.

## References

- `<agent>/references/INDEX.md`
- The target reference file.

## Outputs

- Updated reference (or moved / renamed / removed file). Updated INDEX.md at each affected level.

## Steps

1. Resolve the reference. If ambiguous, list candidates and prompt.
2. Ask: edit content | rename | move (re-categorise) | archive | delete.
3. Execute the action:
   - **Edit content.** References don't have a fixed section schema; show the user the file and ask which part to change (or accept a wholesale replacement). Show old → new before writing.
   - **Rename / move.** **Rewrite internal cross-refs:** grep `<agent>/` (excluding `<agent>/operations/logs/`) for the old path; for each hit, present old → new and confirm. Common targets: entity logs and other references that link to this one.
   - **Archive.** Move into a sibling `_archive/` folder; remove from active INDEX; note in log.
   - **Delete.** Confirm twice. Remove file. Note in log.
4. Update INDEX.md at affected levels.
5. Log.

## Grounding-rule application

- Content edits — user-confirmed → direct ground-truth.
- Structural changes — no grounding test.

## Logging

`<agent>/operations/logs/manage-reference/YYMMDD-N-<slug>-<action>.md`:

```yaml
---
op: manage-reference
timestamp: YYYY-MM-DDTHH:MM
input-summary: <action> on reference <slug>
outputs:
  - <paths>
outcome: ok
---
```
