# Simplicity brief

Find code a reviewer would ask to be simpler, and for each finding name the existing thing to use instead.

## 1. Search for precedent first

Before judging anything:

1. Read the dependency manifests.
2. For each new helper, pattern, or concept the diff introduces, run `gh search code --owner <org> <term>`.
3. Search the user's context system for a recorded decision or pattern on the same problem.
4. Check the framework's own features.

## 2. Look through four lenses

- **Simplest solution**: a smaller or more direct shape does the same job, or the diff introduces a new pattern where the repo or org already has one. Name the existing pattern, with a link.
- **Hand-rolled code**: custom logic that a dependency already in the manifest, an org library, a framework feature, or a battle-tested package provides. Name it.
- **Low-value tests**: a test earns its place when breaking the behaviour turns it red. Flag tests that stay green either way: tautological (asserting what the test set up), contract or snapshot tests of internals, tests of mocks, tests importing private internals, and tests that a removed thing stays removed.
- **Comments**: apply the deletion test to every added or changed comment and docstring, and give each a keep or delete verdict: it stays only when deleting it loses something a reader needs. A comment that explains what code does points at a naming or structure fix. Ticket keys in code belong only in a `TODO(KEY)` for a real follow-up.

## 3. Report

Each finding: the lens, `file:line`, the issue, and the replacement. Under 400 words.

End with a **Searched** line listing each org and context-system search you ran and what it turned up. The report is done when that line is present and every changed file has been through all four lenses.
