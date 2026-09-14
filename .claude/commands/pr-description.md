---
description: Write a PR description from the current git diff
---

Run `git diff --stat` and `git diff` against the base branch, read the actual
changes, then write a pull request description.

Structure:

**Summary**: one or two sentences a reviewer reads before the diff.

**What changed**: bullets grouped by concern, not by file. Reference files in
parentheses.

**Why**: the problem this solves. If it is a bug fix, describe the failure.

**How to test**: exact commands and the expected result. Include setup if any.

**Migration and rollout notes**: database migrations, config keys, feature flags,
breaking changes, anything that has to happen in a particular order. Write "None"
if there are none.

**Risk**: what a reviewer should look hardest at.

Base the whole thing on the diff. Do not invent context you cannot see, and if
the diff suggests something was left unfinished, say so.
