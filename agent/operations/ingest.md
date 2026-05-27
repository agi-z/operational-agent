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
2. **Classify.** Through the professional lens, decide: is this about a tracked entity? a new entity? a durable reference? noise?
3. **Match entity.** Consult `<agent>/entities/INDEX.md`. If a match exists, target `<agent>/entities/<entity>/log/`.
4. **No matching entity.** Surface to user: "this material doesn't match any tracked entity. Options: (1) skip; (2) create new entity now and route the distilled content; (3) park in pending until you decide." Default to (2); require user confirmation of new entity name before creating.
5. **Distil.** Extract the grounded facts. Note dates, named parties, decisions, commitments. Separate quote-able content from inference.
6. **Apply grounding rule** (see below).
7. **Write the output** with frontmatter (see template below).
8. **Update relevant INDEX.md** entries (entity profile last-touched, references INDEX line, etc.).
9. **Log the invocation.**

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
