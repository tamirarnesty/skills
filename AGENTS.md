# Agent instructions

This repo is the `tstack` plugin: one set of skills that loads in Claude Code, Cursor, and any harness that reads the Agent Skills standard (agentskills.io).

## Layout

- `plugins/tstack/skills/<name>/SKILL.md`, one level deep. `name` matches the folder.
- Manifests: `plugins/tstack/plugin.json` (Agent Plugins standard, schema fields only), `plugins/tstack/.claude-plugin/plugin.json`, `plugins/tstack/.cursor-plugin/plugin.json`, and a `marketplace.json` per harness at the root. Keep `name`, `version` and `description` identical across all five; bump `version` in all of them together.
- `plugins/tstack/vendor/<upstream>/`: the license and upstream commit for each forked source.

## Writing a skill

- Read the `writing-for-agents` skill first.
- Write one skill for every harness. Name a capability once with each harness's tool: the question tool (`AskUserQuestion` in Claude Code, `AskQuestion` in Cursor), the subagent tool (`Agent` in Claude Code, `Task` in Cursor). If a skill would need different instructions per harness, raise it before writing a second copy.
- Frontmatter both harnesses read: `name`, `description`, `disable-model-invocation`, `paths`, `metadata`. Claude Code-only keys (`argument-hint`, `context`, `allowed-tools`, `model`) are fine; other harnesses ignore them.
- A fork keeps the upstream skill's name and records `metadata.upstream` (source and commit) and `metadata.changes` (one line).

## Verify

- `claude plugin validate plugins/tstack`
- `claude --plugin-dir plugins/tstack` and check the skills are listed as `tstack:<name>`.
