# CLAUDE.md — Workspace Instructions

This is a **shared brain** workspace. It hosts one or more **agents** that hold durable knowledge and act on it through a small, fixed command surface.

## Workspace pattern

This workspace can host **one or more agents**. Each agent occupies its own folder:

- `agent/` — the **default** agent (no name). `/agent` with no folder argument targets this one.
- `<name>-agent/` — named agents (e.g., `sales-agent/`, `compliance-agent/`).

Each agent has a **paired output folder** at `<agent-folder>-output/` (so `agent/` ↔ `agent-output/`, `sales-agent/` ↔ `sales-agent-output/`).

A workspace may have zero agents (then run `/agent create-agent` to bootstrap), one agent, or many.

## Three-zone privacy model

Every workspace has three kinds of zone:

1. **`<agent>/` — the agent's brain.** Agent-managed; the user does not edit by hand. Holds identity, entities, references, operations, pending items, operation logs. **Strictly grounded** (see Grounding rule).
2. **`<agent>-output/` — the user-facing pickup zone.** Where the agent writes generative artifacts (drafts, briefs, emails). One sub-folder per operation invocation, named `YYMMDD-N-<short>`. The user picks these up, edits, ships. **Exempt from the grounding rule** — these are intentionally generative.
3. **`user/` — user-private, shared across agents.** Agents do not snoop. The only way information moves from `user/` into an agent's brain is through `/agent ingest <path>` initiated by the user.

## Grounding rule

Information enters a `<agent>/` folder only if it is **grounded**: directly stated in input, externally validated, or user-confirmed.

- Direct ground-truth (statement in source; user assertion) → write through.
- Direct/straightforward inference (date arithmetic; explicit implications) → write through with provenance.
- Judgment-requiring inference (reading-between-lines; pattern-spotting) → confirm with user inline, OR park in `<agent>/pending/` for later resolution.

**The grounding rule applies only to writes into `<agent>/`.** Writes into `<agent>-output/` are exempt.

## Command surface

There is one slash command: **`/agent`**. It dispatches to operations.

**Operations come in two flavours:**

1. **Built-in operations** at `.claude/agent-operations/<op>.md` — shared workspace-level. The 10 seeded ops (converse, ingest, create-agent, manage-agent, create-entity, manage-entity, create-reference, manage-reference, create-op, manage-op) live here. Not duplicated per agent; not edited in normal use; updates come from the workspace template.
2. **Per-agent operations** at `<agent>/operations/<op>.md` — user-defined, specific to one agent. Created via `/agent create-op`.

`/agent` resolves built-ins first, then per-agent. Per-agent ops extend the surface; they do not override built-ins.

See `agent-guide.md` for single-agent usage (the common case). See `agent-guide-advanced.md` for multi-agent workspaces.

## Per-agent specifics

Each agent's purpose, professional lens, responsibilities, and capabilities live in `<agent>/identity.md`. The dispatcher loads this in addition to this file when running an operation against that agent.

## Discoverability

Every folder carries an `INDEX.md` listing its contents. The exceptions are `user/` and its sub-folders, which are user-discretionary.
