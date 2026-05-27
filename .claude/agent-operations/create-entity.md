# create-entity

## Purpose

Add a new entity to this agent's brain. An entity is a thing the agent is about — an account, project, person, product.

## Invocation

- `/agent create-entity [name]` — interactive; name optional (will be prompted if omitted).
- `/agent <name>-agent/ create-entity [name]` — named agent.

## Inputs

- Entity name and seed profile content gathered through dialog.
- (Optional) a path or pasted material to seed the entity profile from.

## References

- `<agent>/entities/INDEX.md` — to check for duplicates and find parent (for sub-entities).
- `<agent>/identity.md` — to apply the professional lens to the seed profile.

## Outputs

- `<agent>/entities/<entity-slug>/INDEX.md`
- `<agent>/entities/<entity-slug>/profile.md`
- `<agent>/entities/<entity-slug>/log/INDEX.md` (empty)
- For sub-entities: nested under parent (e.g., `<agent>/entities/acme/renewal-q3/`).
- Updated `<agent>/entities/INDEX.md`.

## Steps

1. **Elicit name.** Prompt if not supplied. Slugify (`Acme Corp` → `acme`).
2. **Check for duplicates** in `entities/INDEX.md`. If a similar name exists, surface and confirm.
3. **Sub-entity?** Ask whether this nests under an existing entity. If yes, target `<parent>/<slug>/`.
4. **Elicit profile fields.** Standard: stakeholders, current state, key dates, constraints, history snapshot. Adjust prompts per professional lens.
5. **Apply grounding rule to elicited fields.** For each field provided: if direct ground-truth or direct-inference, accept and write into `profile.md`. If judgment-requiring AND the user declines inline confirmation, park at `<agent>/pending/YYMMDD-N-<entity>-<field>.md` (frontmatter per `agent.md` § Pending file shape; `target` = the profile.md path; `question` = the user-decision needed) AND append a row to `<agent>/pending/INDEX.md`. Omit the field from `profile.md` until resolved.
6. **Write `profile.md`** with frontmatter.
7. **Create `log/` folder and empty `log/INDEX.md`.**
8. **Write entity-folder `INDEX.md`** listing `profile.md` and `log/`.
9. **Update parent `entities/INDEX.md`.**
10. **Confirm to user.** Summarize the created entity and offer the next-step hint: `/agent ingest <path>` to populate the entity's log, or `/agent manage-entity <slug>` to edit.
11. **Log.**

## Grounding-rule application

- Profile content user provides directly → direct ground-truth.
- Content distilled from a seed source — apply ingest's classification (direct, inferred-direct, judgment).
- Judgment items → park in `<agent>/pending/` if user declines to confirm inline.

## Logging

`<agent>/operations/logs/create-entity/YYMMDD-N-<slug>.md`:

```yaml
---
op: create-entity
timestamp: YYYY-MM-DDTHH:MM
input-summary: created entity <slug>
outputs:
  - <agent>/entities/<slug>/
outcome: ok
---
```

## profile.md template

```markdown
---
entity: <slug>
created-at: YYYY-MM-DD
last-touched: YYYY-MM-DD
---

# <Entity name>

## Stakeholders

- <Role: Name>

## Current state

<One paragraph snapshot.>

## Key dates

- <YYYY-MM-DD: event>

## Constraints

- <Bullet>

## History snapshot

<Optional: brief background. Recent activity lives in `log/`.>
```
