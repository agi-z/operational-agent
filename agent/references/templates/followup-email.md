---
reference-slug: followup-email
category: templates
source: user-authored
created-at: 2026-05-01
---

# Follow-up Email Template

Use after a customer call or meeting. Personalise the placeholders; keep total length under 150 words.

## Template

```
Subject: {{topic}} — quick follow-up

Hi {{first-name}},

Thanks for the time {{when}}. Quick recap of what we agreed:

- {{commitment-1}}
- {{commitment-2}}
{{#commitment-3}}- {{commitment-3}}{{/commitment-3}}

{{#open-item}}One open item: {{open-item}} — I'll come back to you on this by {{open-item-by}}.{{/open-item}}

Next from my side: {{next-from-me}} by {{next-from-me-by}}.

Anything I've missed, just shout.

Best,
{{sender-name}}
```

## Placeholder guide

| Placeholder | Source |
|---|---|
| `{{topic}}` | The substantive topic of the meeting (1-4 words). |
| `{{first-name}}` | Recipient first name from entity profile. |
| `{{when}}` | "yesterday" / "this morning" / "on Tuesday" — relative to send date. |
| `{{commitment-1..3}}` | Commitments from the meeting log entry — verbatim or tight paraphrase. |
| `{{open-item}}` | Any open question from the log entry; omit block if none. |
| `{{open-item-by}}` | A grounded date — never invented. |
| `{{next-from-me}}` | The user's next action — must be grounded in the log or just-stated. |
| `{{sender-name}}` | The user. |

## Usage notes

- Generated via `converse` or a user-defined op; written to `agent-output/YYMMDD-N-followup-<account>/`.
- Output is generative-exempt from the grounding rule. Commitments and dates inside the email body should still trace to log entries to stay defensible.
