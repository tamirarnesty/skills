# skills

`tstack`: my skills for Claude Code, Cursor, and other Agent Skills harnesses. It holds improved forks of upstream skills (so one copy loads, with no conflicting upstream copy) plus my own.

## Install

Claude Code:

```
claude plugin marketplace add tamirarnesty/skills
claude plugin install tstack@tstack --scope user
```

Cursor: Customize > Plugins > From GitHub Repository > `tamirarnesty/skills`. For local development, symlink `plugins/tstack` into `~/.cursor/plugins/local/tstack` and reload the window.

Other harnesses: symlink `plugins/tstack/skills/*` into `~/.agents/skills/`.

## Skills

Invoke as `/tstack:<name>` in Claude Code and `/<name>` in Cursor. Model-invoked skills also fire on their own when the task matches.

### Changed from upstream

| Skill | Replaces | What changed |
| --- | --- | --- |
| `grilling` | `mattpocock-skills:grilling` | One frontier question per turn through the question tool, context first, recommendation first. Upstream asks every open question in one round. `grill` and `grill-docs` use it. |
| `code-review` | `mattpocock-skills:code-review` | Stacked PRs review against the parent PR's head and read sibling PRs before calling code unused. Standards sources add `docs/STANDARDS.md`, `AGENTS.md`, `CLAUDE.md` and the org manifest. Scope the PR marks as intended is reviewed for quality, not flagged. |
| `pr` | `mattpocock-skills:pr` | My PR template: Summary, Key changes, Evidence, Testing, Merge Danger, written for a reviewer who hasn't opened the diff. Visuals come from `show-me`. |
| `gh-stack` | personal `~/.agents/skills/gh-stack` (0.0.8) | Upstream 0.2.0, plus a tree-shaped stacks recovery path in `references/troubleshooting.md`. |

### My own

| Skill | Replaces | What it does |
| --- | --- | --- |
| `voice` | personal `~/.agents/skills/tamir-voice` | My writing voice for anything other people read: Slack, PR and review text, Jira comments, emails. |

### Vendored from other sources

| Skill | Replaces |
| --- | --- |
| `show-me` | personal `~/.agents/skills/show-me` (from [humanlayer/skills](https://github.com/humanlayer/skills)) |

### Renamed from upstream

| Skill | Replaces |
| --- | --- |
| `grill` | `mattpocock-skills:grill-me` |
| `grill-docs` | `mattpocock-skills:grill-with-docs` |
| `setup` | `mattpocock-skills:setup-matt-pocock-skills` |

### Vendored unchanged

From [mattpocock/skills](https://github.com/mattpocock/skills) 1.3.2, covering every upstream skill you have used, so the upstream plugin can be disabled: `codebase-design`, `domain-modeling`, `handoff`, `implement`, `improve-codebase-architecture`, `prototype`, `retro`, `tdd`, `teach`, `to-spec`, `to-tickets`, `wizard`, `writing-for-agents`. Licenses and upstream commits are in `plugins/tstack/vendor/`.

## Cutover

Run after this repo's skills are on `main`:

1. Claude Code: `claude plugin marketplace add tamirarnesty/skills`, `claude plugin install tstack@tstack --scope user`, then `claude plugin disable mattpocock-skills@mattpocock`.
2. Remove the personal `gh-stack`, `show-me` and `tamir-voice` copies so only the `tstack` ones load: `rm -r ~/.claude/skills/gh-stack ~/.agents/skills/gh-stack ~/.claude/skills/tamir-voice ~/.agents/skills/tamir-voice ~/.claude/skills/show-me ~/.agents/skills/show-me` (Cursor and other harnesses read `~/.agents/skills`).
3. Cursor: install `tamirarnesty/skills` from Customize > Plugins > From GitHub Repository, then turn off the `mattpocock-skills` plugin there.
