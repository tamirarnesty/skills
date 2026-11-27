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
| `grilling` | `mattpocock-skills:grilling` | One frontier question per turn through the question tool, context first, recommendation first. Upstream asks every open question in one round. `grill-me` and `grill-with-docs` use it. |
| `code-review` | `mattpocock-skills:code-review` | Stacked PRs review against the parent PR's head and read sibling PRs before calling code unused. Standards sources add `docs/STANDARDS.md`, `AGENTS.md`, `CLAUDE.md` and the org manifest. Scope the PR marks as intended is reviewed for quality, not flagged. |
| `gh-stack` | personal `~/.agents/skills/gh-stack` (0.0.8) | Upstream 0.2.0, plus a tree-shaped stacks recovery path in `references/troubleshooting.md`. |

### Vendored unchanged

From [mattpocock/skills](https://github.com/mattpocock/skills) 1.3.2, so the upstream plugin can be disabled without losing anything: `ask-matt`, `codebase-design`, `diagnosing-bugs`, `domain-modeling`, `grill-me`, `grill-with-docs`, `handoff`, `implement`, `implement-spec`, `improve-codebase-architecture`, `pr`, `prototype`, `research`, `retro`, `setup-matt-pocock-skills`, `tdd`, `teach`, `to-questionnaire`, `to-spec`, `to-tickets`, `triage`, `wait-what`, `wayfinder`, `wizard`, `writing-for-agents`. Licenses and upstream commits are in `plugins/tstack/vendor/`.

## Cutover

Run after this repo's skills are on `main`:

1. Claude Code: `claude plugin marketplace add tamirarnesty/skills`, `claude plugin install tstack@tstack --scope user`, then `claude plugin disable mattpocock-skills@mattpocock`.
2. Remove the personal `gh-stack` copy so only `tstack:gh-stack` loads: `rm ~/.claude/skills/gh-stack ~/.agents/skills/gh-stack` (Cursor and other harnesses read `~/.agents/skills`).
3. Cursor: install `tamirarnesty/skills` from Customize > Plugins > From GitHub Repository, then turn off the `mattpocock-skills` plugin there.
