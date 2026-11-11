# Personal Cursor configuration

Remote: `git@github.com:westenhoefer/.cursor.git`

This repository tracks personal instructions, subagents, skills, commands, and rules. Shared skills remain in `~/.agents/skills/`.

## Personal instructions

`AGENTS.md` ports the personal safety, issue-repair, simplicity, and terminal-command guidance from `~/.pi/agent/AGENTS.md`, with Cursor-specific wording. Verify global loading in your installed Cursor version; if needed, use its contents as User Rules in **Customize → Rules**.

## Subagents

Custom Cursor subagent definitions live directly in `agents/` and are versioned here:

- `ui-ux-developer`: visual/interaction design and frontend implementation.

The UI/UX agent specification is preserved. There is no custom implementation-handoff skill or mandatory Cursor reviewer/Bugbot workflow.

Reload Cursor after adding or renaming agent definitions.

## Privacy

The personal rules after Cursor's managed `.gitignore` block restrict tracking to configuration resources. Transcripts, project state, terminal/tool output, plans, plugin caches, IDE state, and extensions are not tracked. `mcp.json` is excluded because it may contain credentials or machine-specific values.

Before committing, inspect `git status --short --untracked-files=all` and the staged diff. Recheck ignore behavior if Cursor rewrites its managed block. Do not force-add runtime or credential files. Add sanitized examples if reproducible MCP setup is needed later.
