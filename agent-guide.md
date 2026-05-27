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

The dispatcher prompts for a name and walks you through identity (Quick path: purpose + lens + responsibilities; Deep path: full identity + seed entities/references). It then sets up the agent's folder, copies all 10 default operations into it, and creates the paired output folder.

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

Operations are how the agent does things. Each operation is a markdown file at `<agent>/operations/<op>.md` carrying its own instructions (purpose, invocation, inputs, references it consults, outputs it writes, steps, grounding-rule handling, logging). Operations are **per-agent** — you can tune `sales-agent`'s `converse` differently from `compliance-agent`'s.

The 10 seeded operations: `converse`, `ingest`, `create-agent`, `manage-agent`, `create-entity`, `manage-entity`, `create-reference`, `manage-reference`, `create-op`, `manage-op`.

## The grounding rule (short version)

Anything that ends up in `<agent>/` must trace back to either: (a) direct input, (b) external source, or (c) your explicit confirmation. The agent will park ambiguous judgment items in `<agent>/pending/` and surface them next time you converse.

Outputs in `<agent>-output/` are exempt — those are generative on purpose; you verify them when you use them.

## v1 limitations to know

- **No cross-agent reference sharing.** Each agent owns its own `<agent>/references/`. If two agents in the same workspace need the same rule set, copy it manually. (v2: workspace-level `shared-references/`.)
- **No template auto-sync.** Editing `.claude/agent-templates/operations/<op>.md` only affects newly created agents. To update an existing agent's op, run `/agent <agent>/ manage-op <op>` and use the `sync-from-template` action (or paste manually).
- **Operation logs accumulate without rotation.** If `<agent>/operations/logs/<op>/` gets large, move old folders into an `_archive/` sub-folder manually.
- **Op references to deleted files degrade gracefully.** The op runs anyway; the missing reference is noted in the op log; the agent proceeds with what's available.
