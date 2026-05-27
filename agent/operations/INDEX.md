# agent/operations/ — User-Defined Operations

This index lists **user-defined operations** for this agent only.

Built-in operations (`converse`, `ingest`, `create-agent`, `manage-agent`, `create-entity`, `manage-entity`, `create-reference`, `manage-reference`, `create-op`, `manage-op`) are shared workspace-wide at `.claude/agent-operations/` — see `.claude/agent-operations/INDEX.md` for that catalogue. The `/agent` dispatcher resolves built-ins first; user-defined ops here extend the surface.

To add a new user-defined op: `/agent create-op`.

| Path | Description |
|---|---|
| `logs/` | Per-operation invocation history (one folder per op — populated as ops run). |

*No user-defined operations yet.*
