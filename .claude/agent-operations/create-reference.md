# create-reference

## Purpose

Add a durable reference to this agent's brain. References are factual material that isn't tied to a specific entity: regulations, methodologies, templates, glossaries.

## Invocation

- `/agent create-reference [name]` — interactive.
- `/agent <name>-agent/ create-reference [name]`.

## Inputs

- Reference name and content (typed, pasted, or from a path).
- (Optional) sub-folder placement if the reference belongs to a category.

## References

- `<agent>/references/INDEX.md` — to check for duplicates and find category folders.
- `<agent>/identity.md`.

## Outputs

- `<agent>/references/<...>/<slug>.md` (or `<slug>/<slug>.md` plus folder INDEX for hierarchical reference sets).
- Updated `references/INDEX.md` (and any intermediate folder INDEX.md).

## Steps

1. **Elicit name and category.** Ask: standalone reference, or part of a category folder (e.g., `references/templates/`, `references/rules/`)?
2. **Check for duplicates** in `references/INDEX.md` and target folder INDEX.
3. **Elicit content.** Accept typed, pasted, or path-based input.
4. **Apply grounding rule.**
5. **Write the reference file** with frontmatter.
6. **Update INDEX.md** at each affected level.
7. **Log.**

## Grounding-rule application

- External published material (regulation text, third-party doc) — direct ground-truth; cite source in frontmatter.
- User-authored material (own template, own methodology) — direct ground-truth; user is the source.
- Distilled/summarised material — apply ingest's classification rules.

## Logging

`<agent>/operations/logs/create-reference/YYMMDD-N-<slug>.md`:

```yaml
---
op: create-reference
timestamp: YYYY-MM-DDTHH:MM
input-summary: created reference <slug>
outputs:
  - <path>
outcome: ok
---
```

## Reference file template

```markdown
---
reference-slug: <slug>
category: <folder-name or "standalone">
source: <citation or "user-authored">
created-at: YYYY-MM-DD
---

# <Reference title>

<Body. For templates: the template itself, with placeholders.
For rules/regulations: the rule text, possibly annotated.
For glossaries: term definitions.>
```
