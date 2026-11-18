---
name: git
description: >
  Git and GitHub conventions: commit message format, line lengths,
  issue linking, and pull request titles and descriptions. Use when
  writing commit messages, staging changes, or opening PRs.
metadata:
  author: Stu Field
  version: "1.0"
---

# Git / GitHub

## Commit messages

- Subject line: <= 50 chars (hard limit), sentence case
  (starts with a capital letter), no trailing period
- Blank line between Subject and Body
- Subject should summarize concisely overall purpose of the
  changes
  - Good: `Fixed broken links`
  - Bad: `More fixes for broken stuff`, `Updates`
- Body: use bullet points; wrap all lines at <= 60 chars
- Omit the body for trivial changes, e.g.
  `Fixed typo in introduction to user guide`
- Body explains *what* and *why*, not *how*. The code
  shows how. State:
  - how things worked before and what was wrong
  - how things work now
  - why you chose this solution
  - side effects or non-obvious consequences
- Body explains any verification steps that have been
  performed when data changes as final bullet
- `Fixes #<issue-number>` auto-closes issues on merge
- inside a commit message, reference to a function
  accompanied by parens should have back ticks, e.g.
  `myfunction()`, not myfunction().
- Make commits atomic: one logical change per commit.
  If the subject is hard to write, the commit is
  probably too big. Try to split it up if possible.
- Verify line lengths before committing. Both commands
  must print nothing:
  - Subject: `awk 'NR == 1 && length > 50' msg.txt`
  - Body: `awk 'NR > 1 && length > 60' msg.txt`


## Pull requests

- Title: brief description of changes, standalone
  (no issue number in title)
- Description: blank for obvious diffs; overview for complex
  changes; include `Fixes #<issue-number>` if applicable
