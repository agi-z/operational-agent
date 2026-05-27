# agent-guide.md — Using This Workspace

A short guide for humans. (The agent reads `CLAUDE.md` and per-agent `identity.md` automatically.)

## What this is

A workspace that hosts one or more **agents**. An "agent" here is **Claude wearing a hat** — Claude Code running with this workspace's `CLAUDE.md` plus a specific agent's `identity.md` loaded. Different agent folders = different hats for different contexts. There is no separate AI.

Each agent has its own **brain folder** (e.g., `agent/`, `sales-agent/`) holding what it knows, and a paired **output folder** (`agent-output/`, `sales-agent-output/`) for things it produces for you.

## The three zones

| Zone | Who edits | What's in it |
|---|---|---|
| `<agent>/` | the agent | identity, entities, references, operations, pending items, operation logs — the brain |
| `<agent>-output/` | the agent (and then you) | generated drafts, briefs, emails — pickup zone |
| `user/` | you only | your private notes, dumps, scratch — agents do not look in here |

## The one command

```
/agent [folder] [operation] [args...]
```

- `/agent` — open a converse session with the default agent at `agent/`.
- `/agent <op>` — run `<op>` on the default agent.
- `/agent <name>-agent/` — converse with a named agent. **Note the trailing slash** — it's how the dispatcher knows you mean a folder, not an op name.
- `/agent <name>-agent/ <op>` — run `<op>` on a named agent.

## Common workflows

### Create a new agent

```
/agent create-agent
```

The dispatcher prompts for a name and walks you through identity (Quick path: purpose + lens + responsibilities; Deep path: full identity + seed entities/references). It then sets up the agent's folder skeleton and the paired output folder. The 10 built-in operations are NOT copied into the new agent — they live once at `.claude/agent-operations/` and are dispatched from there for every agent in the workspace.

### Bring information into the agent's brain

```
/agent ingest user/some-notes.md
/agent ingest          # then paste, attach, or supply a path
/agent sales-agent/ ingest user/call-recording-transcript.md
```

The agent reads the source, distils it through its professional lens, and writes the distilled output into the right place in the brain (entity log entry, reference file, or a `pending/` item if it needs your judgment).

### Ask the agent something / draft something

```
/agent                 # converse with default agent
/agent sales-agent/    # converse with named agent
```

Free-form conversation. When the agent produces something save-worthy (a draft, brief, email), it offers to write it to `<agent>-output/YYMMDD-N-<short-name>/`.

### Resolve pending items

When you start a converse session, the agent checks `<agent>/pending/` and offers to walk through any unverified judgment items before continuing.

### Add a new capability (operation)

```
/agent create-op
```

Walks you through the operation schema. The new op then dispatches via `/agent <op-name>`.

## What operations are

Operations are how the agent does things. Each operation is a markdown file with its own instructions (purpose, invocation, inputs, references it consults, outputs it writes, steps, grounding-rule handling, logging).

Operations live in two places:

- **Built-in** at `.claude/agent-operations/<op>.md` — the 10 seeded ops (`converse`, `ingest`, `create-agent`, `manage-agent`, `create-entity`, `manage-entity`, `create-reference`, `manage-reference`, `create-op`, `manage-op`). Shared across every agent in this workspace. Not duplicated per agent. **Don't edit by hand** — they're managed at the workspace level (template updates come through `git pull` on the workspace repo).
- **Per-agent** at `<agent>/operations/<op>.md` — operations you author for a specific agent via `/agent create-op`. Extend the built-in surface for that agent only.

The dispatcher checks built-ins first. If you want to extend an agent's behaviour, `/agent create-op` writes a new per-agent op file under `<agent>/operations/`. To add a brand-new capability across the whole workspace, that's a template-level change (workspace repo, not per-agent).

## The grounding rule (short version)

Anything that ends up in `<agent>/` must trace back to either: (a) direct input, (b) external source, or (c) your explicit confirmation. The agent will park ambiguous judgment items in `<agent>/pending/` and surface them next time you converse.

Outputs in `<agent>-output/` are exempt — those are generative on purpose; you verify them when you use them.

## v1 limitations to know

- **No cross-agent reference sharing.** Each agent owns its own `<agent>/references/`. If two agents in the same workspace need the same rule set, copy it manually. (v2: workspace-level `shared-references/`.)
- **Built-in op updates need workspace-repo pull.** Built-ins live at `.claude/agent-operations/` and update through the workspace repo (e.g., `git pull` if the workspace is a submodule). They are not per-agent and do not need syncing — every agent always sees the current built-in.
- **Operation logs accumulate without rotation.** If `<agent>/operations/logs/<op>/` gets large, move old folders into an `_archive/` sub-folder manually.
- **Op references to deleted files degrade gracefully.** The op runs anyway; the missing reference is noted in the op log; the agent proceeds with what's available.
