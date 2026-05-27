# ingest

## Purpose

Bring information from outside the agent's brain into it. Distil the source through this agent's professional lens and write the distilled output into the appropriate place in `<agent>/`.

## Invocation

- `/agent ingest` — prompt the user for source (paste, attachment, or path).
- `/agent ingest <path>` — ingest a specific file (inside or outside the workspace; commonly `user/...`).
- `/agent <name>-agent/ ingest [path]` — ingest into a named agent.

## Inputs

- The source: file at the given path, OR pasted content, OR an attached file.
- The agent's professional lens (from `identity.md`).
- The current entity ontology (`<agent>/entities/INDEX.md`).
- The current reference ontology (`<agent>/references/INDEX.md`).

## References

- `<agent>/identity.md` — to apply the professional lens.
- `<agent>/entities/INDEX.md` — to find matching entities.
- `<agent>/references/INDEX.md` — to find matching reference categories.

## Outputs

One of the following, depending on what the source is about:

- **Entity log entry** — `<agent>/entities/<entity>/log/YYMMDD-N-<short>.md` for events / interactions / updates about a tracked entity.
- **Reference file** — `<agent>/references/<...>.md` for durable factual material (regulation, methodology, template).
- **Pending file** — `<agent>/pending/YYMMDD-N-<short>.md` if classification or content requires user judgment.
- **New entity** — created via inline confirmation if source doesn't match any tracked entity and the user opts to create one.

Source file is NOT moved or modified; the distilled output carries provenance back to it.

## Steps

1. **Acquire source.** Read the file at `<path>` if given; otherwise prompt for paste / attachment / path.
2. **Classify.** Through the professional lens, decide which branch applies:
   - **(a) About a tracked entity** → go to step 3a.
   - **(b) About a new entity** → go to step 3b.
   - **(c) Durable reference material** (regulation, methodology, glossary, template — not entity-specific) → go to step 3c.
   - **(d) Noise / not relevant under this lens** → go to step 3d.
3a. **Match entity.** Consult `<agent>/entities/INDEX.md` and confirm the match with the user if any ambiguity. Target `<agent>/entities/<entity>/log/`. Proceed to step 4.
3b. **Propose new entity.** Surface to user: "this material doesn't match any tracked entity. Options: (1) skip; (2) create new entity now and route the distilled content; (3) park in pending until you decide." Default (2); require user confirmation of new entity slug before creating. On confirm, scaffold the entity folder (profile + log) inline; do NOT defer to `create-entity` (operations are atomic). Then proceed to step 4 with the new entity as target.
3c. **Route to references.** Confirm with user: "this looks like durable reference material — route to `<agent>/references/<...>` instead of an entity log?" If user agrees, route output to `<agent>/references/`; if they want it kept against a specific entity, fall back to step 3a.
3d. **Surface "no fit".** Tell the user: "this material doesn't fit the agent's brain under its current lens — skip, or override (route anyway)?" If skip: log the invocation with `outcome: skipped-as-noise`, write no content, stop. If override: ask the user to specify entity or reference target, then proceed.
4. **Distil.** Extract the grounded facts. Note dates, named parties, decisions, commitments. Separate quote-able content from inference.
5. **Apply grounding rule** (see below).
6. **Write the output** with frontmatter (see template below). For entity log entries, use `<agent>/entities/<entity>/log/YYMMDD-N-<short>.md`; for references, `<agent>/references/<...>.md` (per `create-reference`'s nesting rules); for pending, `<agent>/pending/YYMMDD-N-<short>.md`.
7. **Update relevant INDEX.md** entries:
   - For entity log writes: append the new entry row to the entity's `log/INDEX.md`; bump the entity profile's `last-touched` frontmatter to today's date.
   - For new reference writes: append a row to the appropriate `references/.../INDEX.md`.
   - For pending writes: append a row to `<agent>/pending/INDEX.md`.
8. **Log the invocation.**

## Grounding-rule application

- Direct ground-truth (statements in the source) → write through; cite the source path in frontmatter.
- Direct/straightforward inference (date arithmetic; explicit follow-up implications) → write through; mark as `inferred-direct` in frontmatter.
- Judgment-requiring inference (reading-between-lines; tone; pattern-spotting) → either confirm with user inline, OR write the verified content to its target AND park the unverified judgment items at `<agent>/pending/YYMMDD-N-<short>.md`. The pending file references the source and the partial entity-log entry.

## Logging

`<agent>/operations/logs/ingest/YYMMDD-N-<short>.md`:

```yaml
---
op: ingest
timestamp: YYYY-MM-DDTHH:MM
source: <path or "pasted" or "attached">
input-summary: <one-line>
outputs:
  - <path written>
pending-created:
  - <path if any>
outcome: ok | partial | aborted
---
```

## Entity log entry template (what this op writes)

```markdown
---
source: <path or description of source>
op: ingest
written-at: YYYY-MM-DDTHH:MM
classification: direct | inferred-direct | (with linked pending for judgment portions)
---

# <Short title for this log entry>

<Distilled content. Quote source verbatim where useful; otherwise summarise.
Mark inferences explicitly.>

## Provenance

- Source: <path>
- Operation: ingest
```
