---
name: code-review
description: "Review a PR, branch, or work-in-progress diff along three axes in parallel sub-agents: Standards (the repo's documented standards and a code-smell baseline), Spec (what the ticket asked for), and Simplicity (simplest solution, existing org patterns and utilities, low-value tests, comments that fail the deletion test), then reports the issues with root-cause fixes and next steps. Use when the user asks to review a PR (URL or number), a branch, local changes, or \"review since X\"."
metadata:
  upstream: "mattpocock/skills@a496f55 skills/engineering/code-review"
  changes: "Adds the Simplicity axis; reports issues with root-cause fixes and next steps instead of per-axis sections; stacked-PR fixed point; AGENTS.md and org standards in the Standards sources; accepted scope reviewed for quality."
---

Three-axis review of the diff between `HEAD` and a fixed point:

- **Standards**: does the code conform to this repo's documented coding standards?
- **Spec**: does the code faithfully implement the originating issue / spec?
- **Simplicity**: is this the least code and the most familiar shape that solves the problem?

All three axes run as **parallel sub-agents** (`Agent` in Claude Code, `Task` in Cursor) so they don't pollute each other's context, then this skill merges their findings into one list of issues to fix. Start every sub-agent with a fresh context window.

The issue tracker should have been provided to you. If not, tell the user to run `/setup`.

## Process

### 1. Pin the fixed point

Whatever the user said is the fixed point (a commit SHA, branch name, tag, `main`, `HEAD~5`, etc.). When they give a PR URL or number, the fixed point is the PR's base: read it with `gh pr view <pr> --json baseRefName,headRefName`, and review the PR's head (check it out, or read files with `git show <head>:<path>` when the working tree is elsewhere). If they gave neither, ask for it.

For a PR in a stack, the fixed point is the parent PR's head, not `main`: read it from `gh pr view <n> --json baseRefName`, and `git fetch` first so both tips are current. Before calling code unused or out of scope, read the other open PRs in the stack; a later PR often consumes it.

Capture the diff command once: `git diff <fixed-point>...HEAD` (three-dot, so the comparison is against the merge-base). Also note the list of commits via `git log <fixed-point>..HEAD --oneline`.

Before going further, confirm the fixed point resolves (`git rev-parse <fixed-point>`) and the diff is non-empty. A bad ref or empty diff should fail here, not inside two parallel sub-agents.

### 2. Identify the spec source

Look for the originating spec, in this order:

1. Issue references in the commit messages (`#123`, `Closes #45`, GitLab `!67`, etc.), fetched via the workflow in the tracker doc.
2. A path the user passed as an argument.
3. A spec file under `docs/`, `specs/`, or `.scratch/` matching the branch name or feature.
4. If nothing is found, ask the user where the spec is. If they say there isn't one, the **Spec** sub-agent will skip and report "no spec available".

### 3. Identify the standards sources

Search the repo for every file that documents how code should be written. Each of these that exists must be on the list: `CODING_STANDARDS.md`, `CONTRIBUTING.md`, `docs/STANDARDS.md`, `AGENTS.md`, `CLAUDE.md`. When `AGENTS.md` points to an org standards manifest, follow it and add the standards it selects for this change.

On top of whatever the repo documents, the Standards axis always carries the **smell baseline** below: a fixed set of Fowler code smells (_Refactoring_, ch.3) that applies even when a repo documents nothing. Two rules bind it:

- **The repo overrides.** A documented repo standard always wins; where it endorses something the baseline would flag, suppress the smell.
- **Always a judgement call.** Each smell is a labelled heuristic ("possible Feature Envy"), never a hard violation. Like any standard here, skip anything tooling already enforces.

Each smell reads *what it is* → *how to fix*; match it against the diff:

- **Mysterious Name**: a function, variable, or type whose name doesn't reveal what it does or holds. → rename it; if no honest name comes, the design's murky.
- **Duplicated Code**: the same logic shape appears in more than one hunk or file in the change. → extract the shared shape, call it from both.
- **Feature Envy**: a method that reaches into another object's data more than its own. → move the method onto the data it envies.
- **Data Clumps**: the same few fields or params keep travelling together (a type wanting to be born). → bundle them into one type, pass that.
- **Primitive Obsession**: a primitive or string standing in for a domain concept that deserves its own type. → give the concept its own small type.
- **Repeated Switches**: the same `switch`/`if`-cascade on the same type recurs across the change. → replace with polymorphism, or one map both sites share.
- **Shotgun Surgery**: one logical change forces scattered edits across many files in the diff. → gather what changes together into one module.
- **Divergent Change**: one file or module is edited for several unrelated reasons. → split so each module changes for one reason.
- **Speculative Generality**: abstraction, parameters, or hooks added for needs the spec doesn't have. → delete it; inline back until a real need shows.
- **Message Chains**: long `a.b().c().d()` navigation the caller shouldn't depend on. → hide the walk behind one method on the first object.
- **Middle Man**: a class or function that mostly just delegates onward. → cut it, call the real target direct.
- **Refused Bequest**: a subclass or implementer that ignores or overrides most of what it inherits. → drop the inheritance, use composition.

### 4. Spawn the three sub-agents in parallel

Issue all three sub-agent calls together, in the foreground, and aggregate the reports they return.

**Standards sub-agent prompt** should include:

- The full diff command and commit list.
- The list of standards-source files you found in step 3, **plus the smell baseline from step 3** pasted in full (the sub-agent has no other access to it).
- The brief: "Report, per file/hunk where relevant, (a) every place the diff violates a documented standard: cite the standard (file + the rule); and (b) any baseline smell you spot: name it and quote the hunk. Distinguish hard violations from judgement calls: documented-standard breaches can be hard, but baseline smells are always judgement calls, and a documented repo standard overrides the baseline. Skip anything tooling enforces. Under 400 words."

**Spec sub-agent prompt** should include:

- The diff command and commit list.
- The path or fetched contents of the spec.
- The brief: "Report: (a) requirements the spec asked for that are missing or partial; (b) behaviour in the diff that wasn't asked for (scope creep), except changes the PR description or the user marks as intended: review those for quality instead; (c) requirements that look implemented but where the implementation looks wrong. Quote the spec line for each finding. Under 400 words."

**Simplicity sub-agent prompt** should include:

- The diff command and commit list.
- The repo's GitHub org (from `gh repo view --json owner`) and the repo's dependency manifests (`package.json`, `pyproject.toml`, `mix.exs`, etc.).
- The brief: "Find code a reviewer would ask to be simpler, and for each finding name the existing thing to use instead. Start by searching, before judging anything: read the dependency manifests, then run `gh search code --owner <org> <term>` for each new helper, pattern, or concept the diff introduces, and check the framework's own features. Look through four lenses:
  (a) **Simplest solution**: a smaller or more direct shape would do the same job, or the change introduces a new pattern where the org or repo already has one for this problem; name the existing pattern with a link.
  (b) **Hand-rolled code**: custom logic that a dependency already in the manifest, an org library, a framework feature, or a battle-tested package provides; name it.
  (c) **Low-value tests**: tests that pass whether or not the behaviour works: tautological (asserting what the test itself set up), contract or snapshot tests of internals, tests of mocks, tests importing private internals, or tests that a removed thing stays removed. A test earns its place when breaking the behaviour turns it red.
  (d) **Comments**: apply the deletion test to every added or changed comment and docstring: if deleting it loses nothing a reader needs, it goes. A comment that explains what code does points at a naming or structure fix; ticket keys in code belong only in a `TODO(KEY)` for a real follow-up.
  Report each finding with the lens, `file:line`, the issue, and the replacement. End with a **Searched** line listing each org search you ran and what it turned up; the report is complete only with that line. Under 400 words."

If the spec is missing, skip the Spec sub-agent and note this in the final report.

### 5. Report the issues and what to do

Merge the three reports into one list of issues. Combine findings that share a root cause into one issue, and drop any finding the evidence in the reports does not support. Order the issues by what to fix first.

```markdown
## Issues

1. **<issue in a few words>** ([`file:line`](<permalink>))
   <what is wrong and what it causes, one or two sentences>
   **Fix:** <the change that removes the root cause, naming the existing pattern, library, or utility when there is one>

## Next steps

<the direction to take from here: which fixes to make now and in what order, which issue needs a decision from the user first, and what to re-review after the fixes>
```

Each fix targets the source of the problem: the design, type, or shared code that lets it happen, rather than a guard where it shows up. When several issues share one source, the next steps say so and fix it once.

The review is done when every finding from the three reports is either in an issue or dropped for lack of evidence, and the next steps leave the user knowing what to do next.

## Why separate axes

A change can pass one axis and fail another:

- Code that follows every standard but implements the wrong thing → **Standards pass, Spec fail.**
- Code that does exactly what the issue asked but breaks the project's conventions → **Spec pass, Standards fail.**

- Code that is correct and conventional but rebuilds what a library already does → **Standards and Spec pass, Simplicity fail.**

Running them as separate sub-agents stops one axis from masking another; the report then merges what they found into one list of things to fix.
