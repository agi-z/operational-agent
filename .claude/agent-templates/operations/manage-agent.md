# manage-agent

## Purpose

Edit this agent's `identity.md` (purpose, responsibilities, capabilities, constraints, professional lens, sibling-file lookup table). Does NOT create entities, references, or ops — use the matching `create-*` / `manage-*` ops for those.

## Invocation

- `/agent manage-agent` — default agent.
- `/agent <name>-agent/ manage-agent` — named agent.
- Args: none. Interactive.

## Inputs

- Current `<agent>/identity.md`.
- User edits proposed in dialog.

## References

- `<agent>/identity.md`.

## Outputs

- Updated `<agent>/identity.md`.

## Steps

1. Read current `identity.md` and present it to the user.
2. Ask which section to edit (or "all").
3. Walk through edits one section at a time. Show old → new before writing.
4. Update the sibling-file lookup table if folder layout has changed since creation.
5. Write the updated file.
6. Log.

## Grounding-rule application

- Identity content is user-authored — direct ground-truth. Write through after user confirms each change.

## Logging

`<agent>/operations/logs/manage-agent/YYMMDD-N-<short>.md`:

```yaml
---
op: manage-agent
timestamp: YYYY-MM-DDTHH:MM
input-summary: edited <sections>
outputs:
  - <agent>/identity.md
outcome: ok
---
```
