---
name: pr-brief
description: Score a PR and walk the reviewer through it in one pass.
disable-model-invocation: true
argument-hint: "[PR number, URL, or branch — defaults to the current branch's PR]"
---

Call the Skill tool twice, for "pr-score" then "pr-walkthrough", passing `$ARGUMENTS` to each.
