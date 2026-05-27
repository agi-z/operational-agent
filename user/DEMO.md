# Demo artifacts shipped with this workspace

This `user/` folder ships with sample source material so `/agent ingest` is immediately exercisable. Each file demonstrates a different ingestion shape and exercises a different decision the agent has to make.

| File | Source shape | What to expect when you ingest |
|---|---|---|
| `260527-acme-call-transcript.md` | Call transcript with Sarah Chen (Acme). Q3 renewal logistics, compliance question, board-review request, and a "soft" mention of support-response noise. | Several entity-log entries (procurement date, action items, new key contact "Mark Hassan"). The support-response remark is judgment-call territory — expect it to **park in `agent/pending/`** rather than enter the log directly. |
| `260526-sarah-email-thread.md` | Three-message email thread between Sarah and James (FictionalCo) about audit-report compatibility. | Entity-log entries: Acme uses Falcon-IR, new compliance contact Priya Khanna, board-review date (4 June). Direct facts; minimal judgment work. |
| `bundled-tier-pricing-2026.md` | Internal pricing reference doc — tiers, discount structure, renewal terms. **Not** about a specific client. | Should route to `agent/references/` as a durable reference (not the Acme entity log). Tests the **entity-vs-reference** call during ingest. |
| `260524-globex-intro-via-marcus.md` | Internal email introducing a potential new client (Globex Corp). | No Globex entity exists yet. The agent should **ask whether to create a new entity** before routing content — demonstrates the no-matching-entity flow. |

## Try them

```
/agent ingest user/260527-acme-call-transcript.md
/agent ingest user/260526-sarah-email-thread.md
/agent ingest user/bundled-tier-pricing-2026.md
/agent ingest user/260524-globex-intro-via-marcus.md
```

After each, look at where the agent routed the content (`agent/entities/acme/log/`, `agent/references/`, `agent/pending/`) to see the distilled output.

## After ingesting, try the user-defined op

This workspace also ships with one user-defined operation — `draft-follow-up` — to demonstrate the per-agent extensibility surface. Once you've ingested the call transcript and/or the email thread above, you can draft a follow-up email:

```
/agent draft-follow-up acme              # uses the default intent (recap)
/agent draft-follow-up acme next-steps   # propose a path forward
/agent draft-follow-up acme nudge        # friendly ping with a date anchor
```

The draft lands in `agent-output/YYMMDD-N-acme-followup-<intent>/email.md`. See `agent/references/templates/follow-up-email/` for the methodology the op consults, and `agent-output/` for past invocations on Acme.
