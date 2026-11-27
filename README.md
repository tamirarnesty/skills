# skills

The `tstack` plugin.

## Claude Code

```
claude plugin marketplace add tamirarnesty/skills
claude plugin install tstack@tstack --scope user
```

Skills are invoked as `/tstack:<name>`.

## Cursor

Customize > Plugins > From GitHub Repository > `tamirarnesty/skills`.

For local development, symlink `plugins/tstack` into `~/.cursor/plugins/local/tstack` and reload the window.

## Other Agent Skills harnesses

Symlink each folder in `plugins/tstack/skills/` into `~/.agents/skills/`.
