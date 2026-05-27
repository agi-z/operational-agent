# Demo Guide — FictionalCo Sales Workspace

This workspace is a worked example of the shared-brain pattern. It ships with one populated agent (a FictionalCo sales agent), a backstory (Acme Corp Q3 renewal in flight), a few past artifacts (entity log, follow-up emails, a parked judgment), and a small set of staged inputs you can ingest to see the agent in action.

This guide walks you through the demo end-to-end in about 20 minutes.

> **Prerequisite:** open this workspace in Claude Code so the `/agent` command and `CLAUDE.md` are loaded. If `/agent` isn't recognised, you're not in the right workspace.

---

## Before you start — tour the shipped state (5 minutes)

Browse these files in this order. You're reading the agent's existing brain before adding to it.

1. **`agent/identity.md`** — what this agent is. Purpose, responsibilities, constraints, professional lens. Note the lookup table at the bottom — that's how the agent finds its own siblings.
2. **`agent/entities/acme/profile.md`** — the one tracked account. Stakeholders, current state, key dates, constraints. Notice how compact this is — it's a working profile, not a CRM dump.
3. **`agent/entities/acme/log/`** — two past interactions distilled into log entries (5/20 discovery call, 5/24 procurement flag). These came from prior ingest runs.
4. **`agent/pending/`** — one item parked because it required judgment the agent couldn't make alone ("two-week extension" — of what?). Sitting here until the user resolves it.
5. **`agent-output/`** — two follow-up emails already drafted by the `draft-follow-up` op in earlier sessions. Open one to see what the op produces.
6. **`agent/operations/draft-follow-up.md`** — the user-defined op that produced those emails. Note the schema (Purpose / Invocation / Inputs / References / Outputs / Steps / Grounding-rule application / Logging) — every operation file looks like this.
7. **`agent/references/templates/follow-up-email/`** — the methodology the op consults. Three files (`principles.md`, `template.md`, `tone.md`) under a single folder + `INDEX.md`. The op's `References` section targets the index; the agent navigates from there.

Tour complete. Now exercise it.

---

## The four ingest scenarios

The `user/` folder ships with four files (see `user/DEMO.md` for the catalogue). Each demonstrates a different decision `/agent ingest` has to make. Run them in order — each takes 1–2 minutes.

### Scenario 1 — Ingest into a known entity

```
/agent ingest user/260527-acme-call-transcript.md
```

A call transcript with Sarah Chen (Acme). The transcript covers procurement timing (introduces a new contact, Mark Hassan), a compliance question (already partly handled in the 5/26 email thread), a board-review ask, and — at the end — a "soft" mention of support-response noise.

**Watch for:**
- New log entry under `agent/entities/acme/log/`.
- The agent should ask before writing facts that require interpretation. The support-response remark is the clearest example — it's general, unspecified, not a decision. Expect it to **park in `agent/pending/`** rather than enter the log directly.
- The 5/26 compliance thread overlap — the agent may note that this content is already partly captured and decide what to do with the duplicate signal.

### Scenario 2 — Ingest a forwarded email thread

```
/agent ingest user/260526-sarah-email-thread.md
```

A three-message email thread between Sarah and James about audit-report compatibility. Surfaces a new compliance contact at Acme (Priya Khanna) and a board-review date (4 June).

**Watch for:**
- A log entry that captures the direct facts: audit-report format works, Acme uses Falcon-IR, Priya is the compliance lead, board review on 4 June.
- A profile update if the agent decides to add Priya to the stakeholders list. The agent should ground this — Priya's role is explicitly stated in the source.
- Minimal pending — most of this is direct content.

### Scenario 3 — Ingest a reference doc

```
/agent ingest user/bundled-tier-pricing-2026.md
```

This file is **not** about a specific client — it's an internal pricing doc.

**Watch for:**
- The agent should route this to `agent/references/`, not to any entity log. This is the **entity-vs-reference** call.
- If the agent tries to write this under Acme, that's a misclassification — the content is generic to the product.
- Expect a question or proposed reference filename before the write lands.

### Scenario 4 — Ingest an unknown entity

```
/agent ingest user/260524-globex-intro-via-marcus.md
```

An internal email introducing Globex Corp — a potential new client we haven't tracked before.

**Watch for:**
- The agent should recognise it has no `agent/entities/globex/`.
- Per the locked ingest behaviour, you should be offered three options: (1) skip, (2) create a new entity now and route the distilled content, (3) park in pending until you decide.
- Choose option 2 to see the **`create-entity` flow chained from ingest** — a new `agent/entities/globex/` is scaffolded; the distilled intro lands as the first log entry; Adam Voss is captured as the contact.

---

## The custom-op scenario

By now you have richer Acme content (from scenarios 1 and 2) and a new Globex entity. Time to use the agent for output.

```
/agent draft-follow-up acme next-steps
```

Or omit the intent and let the op propose one:

```
/agent draft-follow-up acme
```

**Watch for:**
- A new folder under `agent-output/` (named `YYMMDD-N-acme-followup-<intent>/`) with a fresh `email.md` inside.
- The draft should reference content you literally just ingested — the call's commitments, the board-review date, the new contact name. This is the demo's strongest moment: **the agent's output traces visibly back to your input from minutes ago.**
- An invocation log appended at `agent/operations/logs/draft-follow-up/`.

Try it again with a different intent (`recap`, `nudge`, `next-steps`, `intro-response`) on Acme or on the newly-created Globex entity to see the tone shift while structure holds.

---

## After the demo — what to look at

You've now exercised:

| Demo step | Operation | Concept on display |
|---|---|---|
| Scenario 1 | `ingest` | Grounding rule; judgment → `pending/`; the agent's discrimination of signal vs. noise |
| Scenario 2 | `ingest` | Direct-fact distillation; profile updates derived from grounded source content |
| Scenario 3 | `ingest` | Entity-vs-reference routing; the agent classifies the input before writing |
| Scenario 4 | `ingest` → `create-entity` | Multi-op flows; how a missing entity surfaces and gets bootstrapped from the same input |
| Custom-op step | `draft-follow-up` (user-defined) | The open extensible surface — operations are how a generic agent becomes domain-specific |

The four concepts the workshop teaches map onto these directly:

- **Distillation over storage** — every log entry is shorter than the source; the agent kept the substance, dropped the noise.
- **Intent shapes extraction** — the same call transcript would produce a different log entry under a compliance agent's professional lens. Try changing `agent/identity.md`'s *Professional lens* and re-ingesting the same call to see the contrast.
- **Generation grounded in memory** — open the draft `agent-output/<latest>/email.md`. Every commitment, date, and name traces to a log entry. None invented.
- **Iteration without forgetting** — your second pass through the same entity didn't overwrite the first. Log entries accumulate; the agent reads across them.

---

## What to try next (outside the demo)

- **Resolve the pending item.** Open `agent/pending/260513-1-acme-extension-ambiguity.md` and write a resolution. Or invoke `/agent` (bare) to enter converse mode — the agent will surface the pending count at session start and offer to resolve.
- **Add a new entity by hand.** `/agent create-entity` — no input required; the op elicits.
- **Write your own user-defined op.** `/agent create-op` — e.g., a weekly-summary op that reads recent log entries across all entities and writes a digest into `agent-output/`.
- **Inspect what changed in your own clone.** `git status` inside this workspace will show every file the demo run produced. Compare a fresh clone against your post-demo clone to see the agent's accumulation made concrete.
