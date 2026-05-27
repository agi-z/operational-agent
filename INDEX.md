# Workspace Root — INDEX

| Path | Description |
|---|---|
| `CLAUDE.md` | Auto-loaded workspace instructions (pattern, privacy zones, grounding rule). |
| `agent-guide.md` | Human-facing usage guide for the `/agent` command and folder zones. |
| `.claude/commands/agent.md` | The single dispatcher command. |
| `.claude/agent-templates/operations/` | Canonical operation templates copied into each new agent by `create-agent`. |
| `user/` | User-private zone, shared across agents. Agents do not read here unless explicitly pointed via `/agent ingest <path>`. |

*No agents yet. Run `/agent create-agent` to bootstrap one.*
