# Gather PR evidence

Every claim about the PR rests on something read or run here. Skip this when the evidence for the same PR head is already in this conversation.

Done when each item below is either complete or recorded as a **gap**. A gap is an inspection you attempted and could not complete, quoted with its error: an inaccessible link, a truncated diff, a command that failed. Work you skipped is unfinished, never a gap.

1. Base and head revisions of the PR.
2. PR description and any author or agent notes.
3. Every linked ticket. Find Jira keys (`[A-Z]+-\d+`) and issue links in the PR title, description, branch name, and commit messages, and read each ticket with the available Jira tool (e.g. `getJiraIssue`; its `cloudId` is the site host, such as `pagerduty.atlassian.net`), including its acceptance criteria, linked parent epic, and comments.
4. The complete diff against the base. Tools truncate large diffs, so confirm the number of files in the diff matches the PR's changed-file count.
5. The surrounding code each change touches: callers, dependencies, data flow, and existing tests. Read from the first source that has the head commit:
   - the current working directory, when `git rev-parse HEAD` is the head SHA (a pr-dashboard launch runs in a worktree at head with dependencies installed);
   - a local clone that contains it (`git cat-file -e <head-sha>`), reading with `git show <head-sha>:<path>` (no checkout or fetch);
   - `gh api "repos/<owner>/<repo>/contents/<path>?ref=<head-sha>" -H 'Accept: application/vnd.github.raw'`.
6. The full head-commit file for every function a finding cites, so `file:line` references rest on the code itself, not on hunk offsets or the PR's own comments.
7. **Verify.** Run every check the PR's Testing section claims, plus the repo's cheap read-only checks for the changed files: lockfile check, typecheck, lint, and the tests next to the change. Run them at head: in the working directory when it is at head, otherwise in a throwaway directory or venv outside the user's checkout. Capture each exit code with `; echo "exit=$?"`, never through a pipe that hides it. A claim is verified only by your own run; the author's report of a run is a claim.

Tool routing (which connector or MCP served a read) is not evidence about the code, so it stays out of the output.
