---
name: pr-brief
description: Score a PR and walk the reviewer through it in one pass.
disable-model-invocation: true
argument-hint: "[PR number, URL, or branch — defaults to the current branch's PR]"
---

Run the `pr-score` skill, then the `pr-walkthrough` skill, on the same PR: the one named in the arguments (`$ARGUMENTS` in Claude Code), or the current branch's PR. Each one's instructions are in [`../pr-score/SKILL.md`](../pr-score/SKILL.md) and [`../pr-walkthrough/SKILL.md`](../pr-walkthrough/SKILL.md); the walkthrough reuses the evidence the score already gathered.
