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
3. Execute the action; for edits, walk through sections.
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
