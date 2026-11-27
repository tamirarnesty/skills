---
name: code-review
description: "Review a PR, branch, or local diff for Standards, Spec, and Simplicity in parallel sub-agents, then report the issues with root-cause fixes and next steps. Use when the user asks to review a PR (URL or number), a branch, local changes, or \"review since X\"."
metadata:
  upstream: "mattpocock/skills@a496f55 skills/engineering/code-review"
  changes: "Adds the Simplicity axis and the context system; reports issues with root-cause fixes, smaller fixes, and next steps; stacked-PR fixed point; briefs disclosed to sibling files."
---

Review the diff between the PR's head and a **fixed point** along three axes, each in its own sub-agent (`Agent` in Claude Code, `Task` in Cursor) so no axis masks another:

- **Standards**: does the code follow the repo's documented standards and the user's recorded preferences?
- **Spec**: does it do what the ticket asked?
- **Simplicity**: is it the least code in the most familiar shape, reusing what already exists?

Then merge what they find into one list of issues to fix.

## 1. Pin the fixed point

The fixed point is what the user named: a SHA, branch, tag, or `HEAD~5`. For a PR URL or number, it is the PR's base, from `gh pr view <pr> --json baseRefName,headRefName`; in a stack that base is the parent PR's head. `git fetch` so both tips are current, and review the PR's head: check it out, or read files with `git show <head>:<path>`.

Record `git diff <fixed-point>...<head>` and `git log <fixed-point>..<head> --oneline`. Done when `git rev-parse <fixed-point>` resolves and the diff is non-empty; with neither a fixed point nor a PR given, ask the user for one.

## 2. Gather the sources

- **Spec**: the ticket linked from the PR, branch, or commit messages (read through the tracker doc; when there is none, tell the user to run `/setup-tstack`), else a spec path the user gave, else a spec under `docs/`, `specs/`, or `.scratch/` matching the branch, else the PR description (and the descriptions of the PRs it stacks on). Every PR has a spec this way; ask the user only when reviewing a branch or local diff with none of these.
- **Standards**: every file in the repo that says how code is written, including each of `CODING_STANDARDS.md`, `CONTRIBUTING.md`, `docs/STANDARDS.md`, `AGENTS.md`, and `CLAUDE.md` that exists, plus the standards an org manifest selects when `AGENTS.md` points to one.
- **Context system**: the user's recorded preferences and past decisions, wherever this session reaches them (a memory vault through its connector or repo, a Notion workspace, or the harness's memory files). Collect each note that bears on what the diff touches, with its citation. A recorded preference ranks with a documented repo standard.
- **Precedent inputs**: the repo's GitHub org (`gh repo view --json owner`) and its dependency manifests (`package.json`, `pyproject.toml`, `mix.exs`, and so on).

Done when each source is either collected or recorded as absent.

## 3. Run the three axes in parallel

Issue all three sub-agent calls in one batch and wait for every report. Each prompt holds the diff and log commands from step 1, its sources from step 2, and the instruction to read its brief, with the brief's absolute path:

- **Standards**: the standards files and context-system notes; brief [`brief-standards.md`](brief-standards.md).
- **Spec**: the spec's path or fetched contents; brief [`brief-spec.md`](brief-spec.md). Skip this axis when there is no spec.
- **Simplicity**: the org, the manifests, and how to reach the context system; brief [`brief-simplicity.md`](brief-simplicity.md).

Done when every launched axis has returned its report.

## 4. Report the issues and what to do

Merge the reports. Findings that share a root cause become one issue; order the issues by what to fix first. Real findings that are small (a stale link, a test that never goes red, a comment that fails the deletion test) go under **Smaller fixes**. A finding leaves the report only when the evidence disproves it.

```markdown
## Issues

1. **<issue in a few words>** · <tag> ([`file:line`](<permalink>))
   <what is wrong and what it causes, one or two sentences>
   **Fix:** <the change at the root cause, naming the existing pattern, library, or utility when there is one>

## Smaller fixes

- [`file:line`](<permalink>) · <tag>: <what to change>

## Next steps

<which fixes to make now and in what order, which issue needs the user's decision first, and what to re-review after>
```

The **tag** names what kind of problem it is, carried over from the axis report: the smell (Duplicated Code, Feature Envy), the Simplicity lens (hand-rolled code, low-value test, comment), or the rule it breaks (`STANDARDS.md`, or the context-system note). An issue merged from several findings lists each tag. Smaller fixes carry a tag the same way when they have one.

Each fix changes the source of the problem: the design, type, or shared code that lets it happen. When several issues share one source, the next steps fix it once.

Done when every finding from every report sits in an issue, in Smaller fixes, or is named as disproved, and the next steps tell the user what to do next.
