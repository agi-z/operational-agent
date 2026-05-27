---
reference-slug: follow-up-email-template
parent: follow-up-email
created-at: 2026-05-01
---

# Follow-Up Email — Template

Use as a skeleton. Adapt to the intent — see `tone.md` for register variations.

~~~
Subject: {{topic}} — quick follow-up

Hi {{first-name}},

Thanks for {{specific-anchor}}.

Quick recap of what we agreed:

- {{commitment-from-them-1}}
- {{commitment-from-me-1}}
{{#commitment-extra}}- {{commitment-extra}}{{/commitment-extra}}

{{#open-item}}One open item: {{open-item}} — {{who-owns-it}} will come back on this by {{open-item-by}}.{{/open-item}}

Next from my side: {{next-from-me}} by {{next-from-me-by}}.

{{closing-line}}

Best,
{{sender-name}}
~~~

## Placeholder guide

| Placeholder | Source | Notes |
|---|---|---|
| `{{topic}}` | Meeting topic, 1–4 words | From log entry title or stated subject. |
| `{{first-name}}` | Recipient first name | From entity profile stakeholders. |
| `{{specific-anchor}}` | A specific moment from the conversation | "the call earlier" / "walking through procurement timing on Tuesday" — anchored, not generic. |
| `{{commitment-from-them-1}}` | What they said they'd do | Verbatim or tight paraphrase from the log entry's *Decisions / commitments* section. |
| `{{commitment-from-me-1}}` | What I said I'd do | Same source. |
| `{{commitment-extra}}` | Additional commitments | Optional; omit the bullet if none. |
| `{{open-item}}` | Open question from the log | Omit the block entirely if none. |
| `{{who-owns-it}}` | Name of the person taking the item forward | If user, `I`; if recipient, their first name. |
| `{{open-item-by}}` | Grounded date | Never invented. If no date is set, ask the user before drafting. |
| `{{next-from-me}}` | The next thing I will send / do | Must trace to the log or be just stated by the user. |
| `{{next-from-me-by}}` | Date by which I will do it | Grounded. |
| `{{closing-line}}` | Single closing thought | See `tone.md` for intent-specific defaults. |
| `{{sender-name}}` | Sender first name | The user. |

## Usage

Written to `agent-output/YYMMDD-N-<entity>-followup-<intent>/email.md`. Output is generative-exempt from the grounding rule, but facts inside (commitments, dates, contact names) should still trace to the log to stay defensible.
