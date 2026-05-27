# agent-guide.md — Using This Workspace

A short guide for humans. (The agent reads `CLAUDE.md` automatically.)

> **One agent or many?** This guide assumes you're running a single agent in this workspace — the most common case. If you want multiple agents (e.g., a sales agent and a compliance agent side-by-side), read this guide first, then `agent-guide-advanced.md`.

## What this is

A workspace that hosts an **agent**. The "agent" is **Claude wearing a hat** — Claude Code running with this workspace's `CLAUDE.md` plus the agent's `identity.md` loaded. There is no separate AI; the agent is the model with a specific role and brain attached.

The agent has a **brain folder** at `agent/` holding what it knows, and a paired **output folder** at `agent-output/` for things it produces for you.

## The three zones

| Zone | Who edits | What's in it |
|---|---|---|
| `agent/` | the agent | identity, entities, references, operations, pending items, operation logs — the brain |
| `agent-output/` | the agent (and then you) | generated drafts, briefs, emails — pickup zone |
| `user/` | you only | your private notes, dumps, scratch — the agent does not look in here |

## The one command

```
/agent [operation] [args...]
```

- `/agent` — open a converse session with the agent.
- `/agent <op>` — run `<op>` (e.g., `/agent ingest`).
- `/agent <op> <args>` — run with arguments (e.g., `/agent ingest user/notes.md`).

## First time? Bootstrap the agent

**Look in the file tree.** If you see an `agent/` folder, the agent already exists — skip to the workflows below. If you don't, you're starting fresh and your first action is:

```
/agent create-agent
```

The dispatcher walks you through identity. You pick one of two paths:

- **Quick** (recommended for first try) — 3 fields: purpose, professional lens, one-line responsibilities. Defers entities and references to set up later through `ingest`.
- **Deep** — full identity plus seed entries for entities and references you want to start with.

**About "professional lens":** the perspective the agent reasons through. *"Sales / partnership"* and *"compliance officer"* are different lenses — they make the agent emphasize different things in the same raw input. One sentence is enough.

After bootstrap, the agent's folder and paired output folder exist. All built-in operations are immediately available — nothing to install per agent.

## Common workflows

### Bring information into the agent's brain

```
/agent ingest user/some-notes.md
/agent ingest          # then paste, attach, or supply a path
```

The agent reads the source, distils it through its professional lens, and writes the distilled output into the right place in the brain (entity log entry, reference file, or a `pending/` item if it needs your judgment).

### Ask the agent something / draft something

```
/agent
```

Free-form conversation. Example asks:

> *"What's open with Acme?"* → agent reads relevant entity logs, summarises.
>
> *"Draft a follow-up email to Sarah about the Q3 renewal."* → agent reads the relationship history, drafts the email.

When the agent produces something save-worthy (a draft, brief, email), it offers to write it to `agent-output/YYMMDD-N-<short-name>/` — *YYMMDD* is the date, *N* is a per-day sequence number (1, 2, 3…), and `<short-name>` is a kebab-case summary so you can scan output folders at a glance.

### Resolve pending items

When you start a converse session, the agent checks `agent/pending/` and offers to walk through any unverified judgment items before continuing.

### Add a new capability

```
/agent create-op
```

Walks you through the operation schema. The new op then dispatches via `/agent <op-name>`. (Created ops live in `agent/operations/` — your custom additions to this agent.)

### Customize how an existing built-in behaves

*(Skip on first read — you only need this once you've been using the agent for a while and want to tune it.)*

You can't edit a built-in directly (they're shared at the workspace level and managed there). Two ways to customize behaviour for your agent:

- **Tune the agent's identity.** Built-in ops read `agent/identity.md` and let its professional lens shape their behaviour. Often the simplest fix — sharpening the lens changes how `ingest`, `converse`, etc. extract and frame content.
- **Create a per-agent op with a different name.** `/agent create-op` → e.g., `ingest-deep` that does what you want; built-in `ingest` stays untouched and available for the standard flow.

## What operations are

Operations are how the agent does things. Each operation is a markdown file with its own instructions (purpose, invocation, inputs, references it consults, outputs it writes, steps, grounding-rule handling, logging).

Operations live in two places:

- **Built-in** at `.claude/agent-operations/<op>.md` — the 10 ops shipped with the workspace (`converse`, `ingest`, `create-agent`, `manage-agent`, `create-entity`, `manage-entity`, `create-reference`, `manage-reference`, `create-op`, `manage-op`). **Don't edit by hand** — they're managed at the workspace level (updates come through `git pull` on the workspace repo).
- **Per-agent** at `agent/operations/<op>.md` — operations you author for this agent via `/agent create-op`. These extend the built-in surface.

The dispatcher checks built-ins first. To extend behaviour, `/agent create-op` writes a new per-agent op file. To add a brand-new capability across the whole workspace, that's a template-level change to the workspace repo.

## The grounding rule (short version)

Anything that ends up in `agent/` must trace back to either: (a) direct input, (b) external source, or (c) your explicit confirmation. The agent will park ambiguous judgment items in `agent/pending/` and surface them next time you converse.

Outputs in `agent-output/` are exempt — those are generative on purpose; you verify them when you use them.

## v1 limitations to know

- **Built-in operations are shared, not per-agent.** All agents in this workspace see the same built-ins from `.claude/agent-operations/`. There's nothing to install or sync per agent — the agent always sees the current built-in. Updates to the built-ins themselves happen at the workspace level (whoever maintains this workspace).
- **Operation logs accumulate without rotation.** If `agent/operations/logs/<op>/` gets large, move old folders into an `_archive/` sub-folder manually.
- **Op references to deleted files degrade gracefully.** The op runs anyway; the missing reference is noted in the op log; the agent proceeds with what's available.

## Going further

Need to run more than one agent in this workspace? See `agent-guide-advanced.md`.
