# Spec brief

Review the diff against the spec. Report:

- **Missing**: requirements the spec asks for that are absent or partial.
- **Wrong**: requirements that look implemented, where the implementation looks wrong.
- **Untrue**: claims in the PR description (what changed, why, what was tested) that the diff or the repo contradicts.
- **Unasked**: behaviour the spec didn't ask for. When the PR description or the user marks a change as intended, review its quality instead.

Quote the spec line for each finding and give `file:line` for the code. Under 400 words. The report is done when every requirement in the spec has been checked against the diff.
