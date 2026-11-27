---
name: pr-walkthrough
description: Walk a reviewer who knows the repo through a PR — what it does and why, a diagram of the change, and expandable detail where the change is consequential.
disable-model-invocation: false
argument-hint: "[PR number, URL, or branch — defaults to the current branch's PR]"
---

Walk a reviewer who knows this repo through the PR in `$ARGUMENTS` (or the current branch's PR). You are **read-only**: the repo, the PR, and every external system stay exactly as you found them. Your only output is the walkthrough in this conversation.

The reviewer knows the codebase, so skip what any regular contributor already knows. A **consequential** change (new control flow, a changed contract, data or schema changes, concurrency, security, anything hard to roll back) gets explained from the ground up, assuming no prior context on it.

## 1. Gather evidence

Follow [EVIDENCE.md](../pr-score/EVIDENCE.md), steps 1–6. Step 7 (verify) belongs to the score; skip it here.

## 2. Write the walkthrough

The default view is short. Depth lives in `<details>` blocks the reader opens on demand. Write each block as `<details><summary>Title</summary>`, a blank line, the markdown, then `</details>`.

Use this exact shape:

```
### Summary

<one sentence on what the PR does and why>
- <1–5 bullets: the behaviour changes a reviewer must know>

### Diagram

<small Mermaid diagram>

<details><summary><consequential part></summary> … </details>

<details><summary>Gaps</summary> … </details>
```

### Diagram

Only when the PR changes a flow, structure, or data model; otherwise write "Skipped:" and the reason. 3–8 nodes, showing the change. Pick the type that fits: sequence for request flows, ER for schema, flowchart for control flow, class for types.

### Detail blocks

One collapsed block per consequential part of the change, titled by what it covers ("Recommendation selection now runs before report generation"). Inside: what changed, why, and how it behaves on failure, with `file:line` references, explained without assuming prior context. Routine changes get no block; the summary bullets cover them. If the PR bundles unrelated concerns, say so in the summary and propose a review split by concern.

### Gaps

The last block lists every gap not already reported in this conversation. Omit it when there are none.
