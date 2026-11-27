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

See the table below, updated as skills land.
