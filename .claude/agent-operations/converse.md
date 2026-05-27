# converse

## Purpose

Hold an open-ended conversation with the user as this agent. Default mode of `/agent`.

## Invocation

- `/agent` — converse with default agent.
- `/agent <name>-agent/` — converse with a named agent.
- Args: none required. Any free-text the user types after invocation is the opening turn.

## Inputs

- The agent's `identity.md` (already loaded by the dispatcher).
- The user's turns.
- On-demand: any file or folder the conversation refers to (`<agent>/entities/...`, `<agent>/references/...`, etc.). Read lazily — do not pre-load the brain.

## References

- `<agent>/identity.md` — voice, professional lens, sibling-file lookup table.
- `<agent>/entities/INDEX.md` — read on demand when the conversation references an entity.
- `<agent>/references/INDEX.md` — read on demand when the conversation references a reference.
- `<agent>/pending/INDEX.md` — checked at session start (see Steps).

## Outputs

- **Conversation turns** (in-session) — not persisted.
- **Optionally:** if the conversation produces a generative artifact (draft, brief, summary, email) the user wants saved, write it to `<agent>-output/YYMMDD-N-<short-name>/`. Generative-exempt from the grounding rule.
- **Optionally:** if the conversation surfaces a confirmed fact about an entity, prompt the user whether to append it as an entity log entry (`<agent>/entities/<entity>/log/YYMMDD-N-<short>.md`) — that write IS subject to the grounding rule.

## Steps

1. **Pending check at session start.** If this is the first turn AND `<agent>/pending/INDEX.md` lists items, surface the count and offer to resolve before continuing.
2. **Listen.** Read the user's turn through the lens declared in `identity.md` § Professional lens.
3. **Read lazily.** When the conversation references an entity or reference, consult that file (via the lookup table or the relevant INDEX).
4. **Respond.** Stay in the professional lens. Distinguish what you know (grounded in the brain) from what you're inferring or asking for.
5. **Multi-perspective refine (gated trigger).** Apply this before finalizing if the output is BOTH (a) destined for `<agent>-output/` (i.e., a generative artifact the user will use externally) AND (b) substantive — an email to a named recipient, a brief, a status doc, a plan, a recommendation, a draft sent to a third party. **Skip** for short factual answers, in-conversation summaries, and any internal-only content. When triggered: do a self-review pass naming 2-3 perspectives (e.g., "as the recipient", "as a sceptic", "as the user's manager"). Adjust. Surface the perspectives used to the user.
6. **Offer to persist.** If the turn produces a saveable artifact, ask the user whether to save it to `<agent>-output/` (with a suggested short name) or to log a fact into the brain.
7. **Apply grounding rule** for any brain-bound write (see below).

## Grounding-rule application

- Conversation turns themselves are not persisted; the grounding rule does not gate dialogue.
- **For any write the user requests into `<agent>/`** (e.g., a confirmed fact into an entity log):
  - Direct ground-truth (the user just stated it; an input doc carried it) → write through.
  - Direct/straightforward inference (e.g., date arithmetic) → write through with provenance noted.
  - Judgment-requiring inference → either confirm with the user inline, OR park in `<agent>/pending/YYMMDD-N-<short>.md` and link from the log entry.
- **Writes into `<agent>-output/` are exempt** — generative artifacts go through as drafted.

## Logging

Per the dispatcher's always-log rule, write `<agent>/operations/logs/converse/YYMMDD-N-<short>.md` at the end of the converse invocation. *"End of invocation"* = the user has stopped engaging (signals: explicit "thanks/done"; switching to a different `/agent <op>`; or session naturally winding down with no pending follow-up). For long conversations that produce multiple persisted artifacts, write the log entry **after each persist** rather than waiting to the end, so a single log entry covers a coherent chunk of work.

```yaml
---
op: converse
timestamp: YYYY-MM-DDTHH:MM
input-summary: <one-line: what the conversation was about>
outputs:
  - <path to any agent-output/ folder produced>
  - <path to any entity-log entry written>
pending-created:
  - <path to any pending/ file created>
outcome: ok | partial | aborted
---
```

Body: a 2-5 line narrative.
