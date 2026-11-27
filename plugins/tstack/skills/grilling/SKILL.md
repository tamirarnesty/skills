---
name: grilling
description: Grill the user relentlessly about a plan, decision, or idea. Use when the user wants to stress-test their thinking, or uses any 'grill' trigger phrases.
metadata:
  upstream: "mattpocock/skills@a496f55 skills/productivity/grilling"
  changes: "One question per turn through the question tool, with context first, instead of whole-frontier rounds."
---

Interview the user relentlessly until you reach a shared understanding. Map this as a **design tree**: every decision branches into the decisions that hang off it.

The **frontier** is every decision whose prerequisites are already settled: the questions you can ask _now_ without guessing at answers you haven't heard yet. Ask **one frontier question per turn**, the one that unblocks the most of the tree, through the question tool (`AskUserQuestion` in Claude Code, `AskQuestion` in Cursor; in plain text where the harness has none). Each question:

1. Opens with the one or two lines of context the user needs to answer it: what you found, or what the choice changes.
2. Offers concrete options, your recommended answer first and marked as recommended.

Then wait for the answer. Each answer reshapes the tree: settled decisions push the frontier outward and unblock the questions that hung on them. Recompute the frontier and ask the next one.

Finding _facts_ is your job, never the user's. When a frontier question needs a fact from the environment (filesystem, tools, etc.), dispatch a sub-agent (`Agent` in Claude Code, `Task` in Cursor) with a fresh context window to find it, and look it up rather than asking the user. Keep going while it runs: only the questions downstream of that fact wait for its report. The _decisions_ are the user's: put each to them and wait.

The session is done when the frontier is empty: every branch of the design tree visited, nothing left silently assumed. Act on it only once the user confirms you have reached a shared understanding.
