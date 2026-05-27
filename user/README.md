# user/ — User-Private Zone

This folder is yours. Notes, dumps, scratch files, attachments, source documents — anything you want kept private from the agents in this workspace.

**Agents do not read this folder.** They will only ever see something in `user/` if you explicitly point them at it, e.g.:

```
/agent ingest user/your-file.md
```

There is no required structure. No INDEX.md is required. Organise as you like.

`user/` is shared across all agents in this workspace — but since nothing in here flows into any agent automatically, "shared" only matters when you point different agents at different files.
