# agent-output/ — Default Agent's Output Zone

This is where the default agent (at `agent/`) writes generative artifacts: drafts, briefs, emails, summaries.

## Convention

One sub-folder per operation invocation that produces output. Folder name format:

```
YYMMDD-N-<short-name>
```

- `YYMMDD` — date of generation (260527 = 2026-05-27).
- `N` — sequence number for that date (1, 2, 3…).
- `<short-name>` — kebab-case, descriptive.

Examples: `260527-1-acme-followup-email/`, `260527-2-acme-quarterly-brief/`.

## Grounding exemption

Content here is **exempt from the grounding rule**. These are intentionally generative outputs. You verify them when you pick them up and ship them.

The agent still aims to keep facts inside outputs traceable to the brain (e.g., commitments inside a follow-up email should match the entity log). That is a quality goal, not a structural gate.

## Index

`INDEX.md` lists current sub-folders. Update happens when an operation writes here.
