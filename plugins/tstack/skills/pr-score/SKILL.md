---
name: pr-score
description: Score a PR's merge confidence (1–5) with severity-ranked findings, suggested fixes, and ticket gaps, backed by checks run at head.
disable-model-invocation: false
argument-hint: "[PR number, URL, or branch — defaults to the current branch's PR]"
---

Score the PR in `$ARGUMENTS` (or the current branch's PR) for a reviewer who knows this repo. You are a **read-only** reviewer: running checks is fine, but the repo, the PR, and every external system stay exactly as you found them. Your only output is the scorecard in this conversation.

## 1. Gather evidence

Follow [EVIDENCE.md](EVIDENCE.md). From the tickets, find the **ticket gaps**, quoting the ticket line for each:

- asked for but missing or partial;
- looks implemented, but the implementation looks wrong;
- in the diff but not asked for, or ruled out of scope (scope creep).

Requirements the PR delivers go unmentioned anywhere in the output.

The PR page already shows CI status, approvals, and labels, so those appear nowhere in the output.

## 2. Write the scorecard

Use this exact shape. Write each `<details>` block as `<details><summary>Title</summary>`, a blank line, the markdown, then `</details>`.

```
## Confidence Score: X/5

<one sentence: the verdict and its main reason>

## Findings

1. **P1** — **<issue in ≤6 words>** (`file:line`)
   <details><summary>Detail and suggested fix</summary>

   <what is wrong and its consequence, 1–3 sentences; mark it as inference when it is not directly seen in code>

   **Suggested fix:** <concrete change>
   </details>

Ticket gaps: <each gap not already a finding or acknowledged by the author, quoting the ticket line, one clause each; or "none">

Acknowledged by the author: <risks the PR itself accepts, one line>

<details><summary>Gaps</summary> … </details>
```

### Confidence score

5 = ready to merge; 4 = merge after minor fixes or confirmations; 3 = fix issues first; 2 = significant rework needed; 1 = rethink the approach. Ticket gaps weigh on the score alongside the code's correctness. Weight by stakes: security, auth, payments, and data integrity count more than internal scripts. Lower the score for material gaps; a check you could have run but did not means step 1 is unfinished, so go back and run it.

### Findings

A finding is a concrete risk tied to a line, with a nameable consequence and a suggested fix. Style nits and generic advice don't qualify. Severities:

- **P0 — Must fix before merge:** security issue, data loss, crash, or broken contract
- **P1 — Should fix:** bug, incorrect behaviour, missed edge case, or a ticket requirement missing or implemented wrongly
- **P2 — Worth a look:** behaviour other code may rely on, risky assumption, missing test, hard-to-roll-back change, docs/event catalogue that no longer match the code, a ticket requirement only partly met, or scope creep beyond the ticket

Order findings highest severity first. A ticket gap serious enough to be a finding appears only as the finding; the Ticket gaps line names the rest. A risk the PR already acknowledges (a follow-up ticket, a stated open question, a documented trade-off) goes on the "Acknowledged by the author" line instead, unless you disagree with the author's call, in which case it is a finding that says why.

With no findings, replace the list with **What we checked:** 2–3 bullets, each a claim followed by its evidence, naming the checks you ran and their result ("`poetry check --lock` passes at head: exit 0, 'All set!'").

### Gaps

The last block lists every gap. Omit it when there are none.
