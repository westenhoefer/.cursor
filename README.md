# Personal Cursor configuration

Remote: `git@github.com:westenhoefer/.cursor.git`

This repository tracks personal skills, commands, and rules. Shared skills live in `~/.agents`; the `agents` junction also points to that repository and is intentionally excluded here.

`skills/implementation-handoff` is Cursor-specific. It uses Cursor reviewer agents for an explicitly authorized PR workflow; shared specifications do not require it.

## Privacy

The personal rules after Cursor's managed `.gitignore` block restrict tracking to configuration resources. Transcripts, project state, terminal/tool output, plans, plugin caches, IDE state, and extensions are not tracked. `mcp.json` is excluded because it may contain credentials or machine-specific values.

Before committing, inspect `git status --short --untracked-files=all` and the staged diff. Recheck ignore behavior if Cursor rewrites its managed block. Do not force-add runtime or credential files. Add sanitized examples if reproducible MCP setup is needed later.
