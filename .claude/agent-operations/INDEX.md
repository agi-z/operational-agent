# Built-in Agent Operations — INDEX

These are the **built-in operations** shipped with the workspace. They live here (workspace-level, shared across all agents) and are dispatched by `/agent` **before** any per-agent operations at `<agent>/operations/<op>.md`.

**Do not edit these files in normal use.** They are the canonical built-in surface; updates come through the workspace template (e.g., `git pull` on the workspace submodule). To add or customize behaviour for a specific agent, create a user-defined op via `/agent <agent>/ create-op` — that goes into `<agent>/operations/` and is dispatched only when no built-in matches.

All invocations also work in the form `/agent <name>-agent/ <op> [args]` to target a named agent.

| Operation | Invocation | Description |
|---|---|---|
| `converse` | `/agent` (default) or `/agent converse` | Conversation; impart info, perform tasks, refine outputs. |
| `ingest` | `/agent ingest [path]` | Distill an external source into the agent brain under the grounding rule. |
| `create-agent` | `/agent create-agent` | Bootstrap a new agent (identity + folder scaffold). |
| `manage-agent` | `/agent manage-agent` | Edit an existing agent's `identity.md`. |
| `create-entity` | `/agent create-entity` | Add a new tracked entity to the agent. |
| `manage-entity` | `/agent manage-entity <entity>` | Edit/rename/remove an entity. |
| `create-reference` | `/agent create-reference` | Add a new reference document. |
| `manage-reference` | `/agent manage-reference <ref>` | Edit/rename/remove a reference. |
| `create-op` | `/agent create-op` | Define a new user-defined operation (writes to `<agent>/operations/`). |
| `manage-op` | `/agent manage-op <op>` | Edit/rename/remove a user-defined operation. |
