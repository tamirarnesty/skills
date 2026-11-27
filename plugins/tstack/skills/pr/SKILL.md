---
name: pr
description: "Use when writing a PR body."
metadata:
  upstream: "mattpocock/skills@a496f55 skills/engineering/pr"
  changes: "Tamir's template: Summary, Key changes, Evidence, Testing, Merge Danger; visuals come from show-me."
---

Write every PR body with this template, all five sections in this order. The reader is a reviewer who has not opened the diff yet: each line gives them the overview, or a fact they would otherwise have to dig out of the code. Use the domain language from `GLOSSARY.md` when the repo has one.

```markdown
## Summary

- Issue: [<ticket-key>](<ticket link>)

<1-2 sentences>

## Key changes

**Intended behaviour changes**

<optional visual>

- **<area>** ([<file>](<permalink>)): <what changed and why>

## Evidence

<proof the change works>

## Testing

> [!NOTE]
> <before>
> <after>

Steps:

1. <step>: <what to inspect>

## Merge Danger

**Door:** two-way | one-way. <how to revert>
**Blast Radius:** <short label>. <bullets only if non-obvious>
```

The PR body is done when every placeholder holds a real value (a fetched ticket link, a permalink at the head commit, a URL that resolves) or its line is gone.

## Summary

- **Issue:** the ticket key, linked. One line per ticket. Leave the line out when there is no ticket.
- **The sentences:** what was wrong or missing, then what is true after this PR. Add the background a reviewer needs to see why it matters, and stop there.

## Key changes

- **Intended behaviour changes:** a brief note of each change other code, logs, or contracts will notice. Leave the heading out when there are none.
- **Visual:** include one when it shows the shape of the change faster than the bullets. Read [`../show-me/SKILL.md`](../show-me/SKILL.md) and pick the smallest view it describes (pseudocode, call tree, component tree, file tree, Mermaid, or a `diff` sketch), written as a Markdown code block. Place it above the bullets it supports.
- **Bullets:** one per area of change, with a bold area name, a permalink to the file at the head commit, and what changed and why in one line.

## Evidence

Required. Proof that the change works as intended.

- For a visual change: before and after screenshots or a recording.
- Otherwise: the observable result, such as the response, log line, or rendered output, before and after. For a bug fix, name the test that failed before and passes now.

## Testing

What a reviewer does to see the change for themselves.

- **The note:** how the behaviour looks or feels before this PR, then after it.
- **Steps:** numbered actions a reviewer performs, each with the exact URL, branch, or environment, and what to inspect. Confirm each step works before writing it.

## Merge Danger

- **Door:** two-way when a revert fully undoes the change, one-way when it leaves something behind (a migration, a sent message, deleted data, a published contract). Add the revert path in one line.
- **Blast Radius:** a short label for the widest reach of a failure, such as one page, every consumer of an API, or all mobile layouts. Add bullets only for reach a reviewer would not guess.
