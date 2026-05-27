# Workspace Root — INDEX

| Path | Description |
|---|---|
| `CLAUDE.md` | Auto-loaded workspace instructions (pattern, privacy zones, grounding rule). |
| `agent-guide.md` | Human-facing usage guide. Single-agent workflows; start here. |
| `agent-guide-advanced.md` | Advanced guide — multi-agent workspaces. Read after the basic guide. |
| `.claude/commands/agent.md` | The single dispatcher command. |
| `.claude/agent-operations/` | Built-in operations shared by every agent in this workspace. Dispatched by `/agent` first; per-agent ops at `<agent>/operations/` extend the surface. |
| `user/` | User-private zone, shared across agents. Agents do not read here unless explicitly pointed via `/agent ingest <path>`. |

*No agents yet. Run `/agent create-agent` to bootstrap one.*
