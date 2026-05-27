# agent-guide-advanced.md — Multi-Agent Workspaces

Advanced guide. Assumes you've read `agent-guide.md` and are comfortable running a single agent. This guide layers on the multi-agent capability.

## Why multi-agent

A single workspace can host **more than one agent**, each with its own identity, brain, and outputs. Useful when you wear multiple hats and want each agent's reasoning kept separate:

- A `sales-agent/` that thinks in deal momentum and relationship dynamics.
- A `compliance-agent/` that thinks in risk and precedent.
- An `ops-agent/` that thinks in process bottlenecks and recurring exceptions.

Different `identity.md` per agent → different professional lens → different extractions from the same raw input.

The simple alternative: stay single-agent. One brain that does everything is fine for many use cases. Only split into multiple agents when the cognitive load of mixing hats outweighs the cost of maintaining them separately.

## Agent folder naming convention

Every named agent's folder uses the `-agent` suffix:

| You provide the name… | The folder is… | Paired output folder |
|---|---|---|
| `sales` | `sales-agent/` | `sales-agent-output/` |
| `compliance` | `compliance-agent/` | `compliance-agent-output/` |
| (default — no name) | `agent/` | `agent-output/` |

The `-agent` suffix is enforced by `create-agent`; you can't make folders that violate the convention through the normal flow. The default agent at `agent/` is the only exception (it's the implicit no-name case).

## Command syntax for non-default agents

The `/agent` command takes the targeted agent's folder as the first argument, **with a trailing slash** to distinguish it from an operation name:

```
/agent <name>-agent/                       # converse with the named agent
/agent <name>-agent/ <op>                  # run <op> on the named agent
/agent <name>-agent/ <op> <args>           # with args
```

### Why the trailing slash?

It's the **parser cue** that tells the dispatcher "this is a folder path, not an op name." Without it, `/agent sales` would look identical to running an op called `sales` on the default agent. The trailing slash also works with Claude Code's folder-path autocomplete — tab-completion gets the slash for free.

### Examples

| Invocation | Agent | Operation |
|---|---|---|
| `/agent` | `agent/` | `converse` (default) |
| `/agent ingest user/call.md` | `agent/` | `ingest` |
| `/agent sales-agent/` | `sales-agent/` | `converse` |
| `/agent sales-agent/ ingest user/call.md` | `sales-agent/` | `ingest` |
| `/agent compliance-agent/ create-reference` | `compliance-agent/` | `create-reference` |

## Creating a new named agent

```
/agent create-agent
```

The dispatcher prompts for a name. Provide the bare name (e.g., `sales`) — the `-agent` suffix is appended automatically. Identity elicitation is the same as for the default agent.

Two rejection rules:

- **`agent` is reserved** for the default. If `agent/` already exists and you name a new agent `agent`, the op rejects and asks for a different name.
- **Names already ending in `-agent`** are rejected (avoids `sales-agent-agent/`).

After bootstrapping, the new agent is immediately usable: `/agent <name>-agent/`.

## Per-agent operation customization (same pattern, but per agent)

The basic guide covers customizing operations for a single agent (`agent-guide.md` → *Customize how an existing built-in behaves*). The same pattern applies per agent in a multi-agent workspace, with one wrinkle worth noting.

Built-in operations are workspace-shared (`.claude/agent-operations/`), so every agent has the same baseline behaviour. Per-agent operations live in each agent's `<name>-agent/operations/` and apply only to that agent.

Practical implication: if you want `sales-agent` to ingest call transcripts differently from how `compliance-agent` does it, you can't customize the built-in `ingest` for sales only (built-ins are shared workspace-wide, not per-agent). Same two options as the basic guide, scoped per agent:

- **Tune the agent's identity.** Each agent's `identity.md` has its own professional lens, so `sales-agent` and `compliance-agent` already get different built-in behaviour without any op customization.
- **Create a per-agent op.** `/agent sales-agent/ create-op` writes only to `sales-agent/operations/` — `compliance-agent` is unaffected.

## Multi-agent v1 limitations

- **No cross-agent reference sharing.** Each agent owns its own `<agent>/references/`. If two agents need the same rule set (say, both `sales-agent` and `compliance-agent` need the same KYC ruleset), copy it into both manually. There is no `workspace/shared-references/` yet.
- **No cross-agent visibility.** `sales-agent` does not see anything inside `compliance-agent/`, and vice versa. Each agent's brain is its own scope. If you want one agent to act on another's outputs, route through the user (read from `compliance-agent-output/`, hand to `/agent sales-agent/ ingest`).
- **`user/` is shared across agents.** The same `user/` folder serves every agent in the workspace — it's the common drop zone.

## When to start a second agent

A rough heuristic:

- **One hat, one brain** is usually right. Don't split until you feel the pain of mixed reasoning.
- **Split when the *intent* differs.** Different intents extract different facts from the same input. If sales and compliance would distill a call transcript differently — they're different hats.
- **Don't split for tidiness alone.** Two folders is more upkeep than one. The grounding-rule discipline, pending resolution, and op customization all multiply.

The single agent at `agent/` is usually enough. Layer in named agents when you've earned them.
