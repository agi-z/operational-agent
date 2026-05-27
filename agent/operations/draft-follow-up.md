---
op: draft-follow-up
agent: agent
created-at: 2026-05-01
type: user-defined
---

# draft-follow-up

## Purpose

Generate a follow-up email for a tracked account, drawing on the account's log and a methodology reference. Output is a draft for the user to review, edit, and send — never sent by the agent.

## Invocation

~~~
/agent draft-follow-up <entity-slug> [intent]
~~~

- `<entity-slug>` — required. The folder name under `agent/entities/` (e.g., `acme`). If omitted, the op asks which account.
- `[intent]` — optional. One of: `recap` (default), `nudge`, `next-steps`, `intro-response`. Selects the tone variant. If omitted and the most recent log entry suggests a clear intent (an imminent date → `nudge`; a recent decision → `recap`; a recent open question → `next-steps`; a first interaction → `intro-response`), the op proposes one and asks the user to confirm.

## Inputs

- The named entity's profile and most recent log entries (typically the last 1–3).
- Optional inline guidance from the user during the invocation (e.g., "include a line about the audit report").

## References

- `agent/references/templates/follow-up-email/INDEX.md` — methodology, template, and tone guidance. The op navigates from the index.

## Outputs

- `agent-output/YYMMDD-N-<entity>-followup-<intent>/email.md` — the draft email, with frontmatter capturing op, intent, entity, generated-at, source-log-entries, sender, recipient.

Output is generative-exempt from the grounding rule. Facts inside (commitments, dates, contact names) should still trace to the entity log; if they cannot, the op asks the user before drafting.

## Steps

1. Resolve the entity. Verify `agent/entities/<entity-slug>/` exists. If not, report and exit; suggest `create-entity`.
2. Read the entity profile and the most recent 1–3 log entries.
3. Determine intent. If the user supplied one, use it. Otherwise propose one based on the log signal and confirm with the user.
4. Read `agent/references/templates/follow-up-email/INDEX.md` and navigate to `principles.md`, `template.md`, and the relevant intent section of `tone.md`.
5. Identify the commitments, open items, and next actions from the log entries. If any required placeholder (a date; a specific commitment; the recipient's first name) cannot be grounded, ask the user inline before drafting.
6. Draft the email by filling the template, applying the intent-specific tone.
7. Create `agent-output/YYMMDD-N-<entity>-followup-<intent>/` and write `email.md` with the frontmatter described under Outputs.
8. Log the invocation per Logging below.
9. Surface the path to the user.

## Grounding-rule application

The output is written to `agent-output/`, so the grounding rule does not gate the write. The op still applies grounding at the *placeholder* level: a placeholder is filled only from log content or content the user just stated. Ungrounded placeholders trigger an inline question, not a guess.

## Logging

`agent/operations/logs/draft-follow-up/YYMMDD-N-<entity>-<intent>.md`. One file per invocation.

Frontmatter:

- `invoked-at`
- `entity`
- `intent` (final, after any proposal)
- `source-log-entries` (paths consulted)
- `output-path` (the `agent-output/` folder created)
- `outcome` (`drafted` / `aborted` / `awaiting-user-input`)

Body: brief narrative of the invocation — what was asked, what was used, any inline questions raised, what was produced.
