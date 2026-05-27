# manage-reference

## Purpose

Edit, rename, move, restructure (single-file ↔ folder-with-INDEX), archive, or delete an existing reference.

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

0. **Target check.** If the reference slug does not resolve to an existing file or folder under `<agent>/references/`, respond: 'No such reference `<slug>`; run `/agent create-reference` to create one.' Stop.
1. Resolve the reference. If ambiguous, list candidates and prompt.
2. Ask: edit content | rename | move (re-categorise) | restructure | archive | delete.
3. Execute the action:
   - **Edit content.** References don't have a fixed section schema; show the user the file and ask which part to change (or accept a wholesale replacement). Show old → new before writing.
   - **Rename / move.** **Rewrite internal cross-refs:** grep `<agent>/` (excluding `<agent>/operations/logs/`) for the old path; for each hit, present old → new and confirm. Common targets: entity logs and other references that link to this one.
   - **Restructure (single-file ↔ folder-with-INDEX).** If the user invokes `/agent manage-reference <slug> restructure`: (a) detect current form (single file at `references/<slug>.md` vs. folder `references/<slug>/INDEX.md + ...`); (b) prompt the user for the new form; (c) for single→folder: create `references/<slug>/` folder, move original content into `references/<slug>/INDEX.md` or split into sub-files per user direction, update parent `references/INDEX.md` row; (d) for folder→single: collapse folder content into a single `<slug>.md` (user confirms which file becomes canonical), delete folder, update parent INDEX. Cross-ref rewrite scope per `manage-entity` § Cross-ref rewrite scope (any op file's References section that targets the old path).
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
