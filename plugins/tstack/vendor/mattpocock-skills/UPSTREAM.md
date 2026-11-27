# mattpocock-skills

- Source: https://github.com/mattpocock/skills
- Commit: a496f5513d4a4cf889bc48e189e579f4b0bdf6aa (plugin version 1.3.2)
- Vendored: the skills that plugin's `.claude-plugin/plugin.json` enables, minus the 8 never invoked in any Claude Code or Cursor session (ask-matt, diagnosing-bugs, implement-spec, research, to-questionnaire, triage, wait-what, wayfinder), flattened from `skills/<category>/<name>/` to `skills/<name>/`. All links in them are folder-relative, so flattening changes no paths.
- To re-sync: diff each skill against upstream at a newer commit, keeping this repo's amendments (each records `metadata.changes`).
